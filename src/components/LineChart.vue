

<template>
  <div>
      <div class="range-wrap"> 
        <span @click="clickRange('')" :class="[yRange==''? 'active': '']" class="total"> 전체보기 </span>
        <span @click="clickRange('5000')" :class="[yRange=='5000'? 'active': '']" class="top1"> ~ 5000명</span>
        <span @click="clickRange('1000')" :class="[yRange=='1000'? 'active': '']" class="top2"> 1000 ~ 5000명</span>
        <span @click="clickRange('500')" :class="[yRange=='500'? 'active': '']" class="top3"> 500 ~ 1000명</span>
        <span @click="clickRange('300')" :class="[yRange=='300'? 'active': '']" class="top4"> 300 ~ 500명</span>
        <span @click="clickRange('100')" :class="[yRange=='100'? 'active': '']" class="top4"> 100 ~ 300명</span>
      </div>
      <!-- <div class="upup-wrap">
        <span class="upup">원에 마우스를 올려 시청자가 떡상한 썸네일을 확인하세요</span>
      </div> -->
      <div id="lineChart-wrap">
        <svg id="visualisation"/>
    </div>
  </div>
</template>

  <style>
  .range-wrap {
      height: 20px;
      font-size: 9px;
      margin-left: 60px;
      margin-bottom: 10px;
  }
    .range-wrap span {
        border: 0.1rem solid;
        margin-left: 1rem;
        text-align: center;
        padding: 3px;
        float: left;
        cursor: pointer;
        border-radius: 5px;
    }
    .range-wrap span.active {
        background-color: #d2dfff;
    }
    #lineChart-wrap {
        width: 100%;
        height: 100%;
    }
     .axis path{
         stroke:black;
         stroke-width:0px ;
     }   

     .axis line{
         stroke: black;
         stroke-width: 0px;
     } 
  
     .axis text{
         fill: black;
         font-size: 10px;
     } 

     .legend text{
         fill:  black;
     }
  </style>
<script>

import * as d3 from "d3";

var color = function(str) {
    var hash = 0;
    for (var i = 0; i < str.length; i++) {
        hash = str.charCodeAt(i) + ((hash << 5) - hash);
    }
    var colour = '#';
    for (var i = 0; i < 3; i++) {
        var value = (hash >> (i * 8)) & 0xFF;
        colour += ('00' + value.toString(16)).substr(-2);
    }
    return colour;
}

export default {
  name: "linechart",
  props: ['chartdata', 'curtime', 'selectStreamId'],
  data() {
    const margin = {top: 10, right: 30, bottom: 20, left: 60};
    return {
        svgg: null,
        xAxis: null,
        yAxis: null,
        margin: margin,
        width: 100,
        height: 100,
        popup: null,
        yRange: '',
    };
  },
  created: function () {
    //   console.log( "chartdata", this.chartdata);
  },
  watch: {
    'chartdata': function(newData, oldData) {
        this.chartdata = newData;
        this.updateChart();
    },
    'curtime': function(newData, oldData) {
        this.curtime = newData;
        this.updateChart();
    },
    "selectStreamId": function(newstreamid, oldstreamid) {
        if( newstreamid && newstreamid !== "") {
            d3.selectAll('.line')
                             .style('opacity', 0.1);

            d3.selectAll('.line.' + newstreamid)
                .style('opacity', 0.85);
        }
        else {
            d3.selectAll('.line')
                             .style('opacity', 0.85);
        }
    }
  },
  methods: {
    clickRange(range) {
        this.yRange = range;
        this.updateChart();
    },
    updateYScale() {
        if(!this.yAxis) return;

        if (this.yRange == '') {
            this.yAxis.domain([1000, d3.max(this.chartdata, d => +d.Cur_count * 1.1)]).range([ this.height, 0 ]);
        }
        else if (this.yRange == '5000') {
            this.yAxis.domain([5000, d3.max(this.chartdata, d => +d.Cur_count + 500)]).range([ this.height, 0 ]);
        }
        else if (this.yRange == '1000') {
            this.yAxis.domain([1000, 5100]).range([ this.height, 0 ]);
        }
        else if (this.yRange == '500') {
            this.yAxis.domain([500, 1100]).range([ this.height, 0 ]);
        }
        else if (this.yRange == '300') {
            this.yAxis.domain([300, 530]).range([ this.height, 0 ]);
        }
        else if (this.yRange == '100') {
            this.yAxis.domain([100, 330]).range([ this.height, 0 ]);
        }
    },
    updateChart() {
        // Select the section we want to apply our changes to
        const vis = d3.select("#visualisation").transition();
        const self = this;

        // re-create data
        var sumstat = d3.group(this.chartdata, d => d.User_id + "_" + d.Board_id);

        // color palette
        var res = Array.from(sumstat.keys()); // list of group names
        this.xAxis.domain(d3.extent(this.chartdata, d => {
            if (this.curtime < d.Time) return this.curtime;
            return d.Time
        })).range([ 0, this.width ]);
        //this.yAxis.domain([1000, d3.max(this.chartdata, d => +d.Cur_count + 500)]).range([ this.height, 0 ]);
        this.updateYScale();

        this.svgg.selectAll("path.line")
            .data(sumstat)
            .join("path")
            .attr("class", function(d) {
                return "line " + d[0];
            })
            .attr('fill', 'none')
            .attr('stroke-width', 1.5)
            .attr('stroke', d => color(d[0]))
            .attr("d", d => {
                return d3.line()
                    .x(d => this.xAxis(d.Time))
                    .y(d => {
                        return this.yAxis(+d.Cur_count)
                    })
                    (d[1])
                })
        .style('opacity', 0.65)
        // .on("mouseover", function(d) {
        //     d3.selectAll('.line')
        //                     .style('opacity', 0.1);
        //     d3.selectAll('.circle')
        //                     .style('opacity', 0.25);
        //     d3.select(this)
        //         .style('opacity', 0.85)
        //         .style("stroke-width", "2.5px")
        //         .style("cursor", "pointer");
        //     })
        // .on("mouseout", function(d) {
        //     d3.selectAll(".line")
        //                     .style('opacity', 0.65);
        //     d3.selectAll('.circle')
        //                     .style('opacity', 0.85);
        //     d3.select(this)
        //         .style("stroke-width", "1.5px")
        //         .style("cursor", "none");
        //     });

        this.svgg.selectAll("circle.thumb")
            .data(this.chartdata.filter(function(d){ return d.Thumbnail && d.Thumbnail !=="" }))
            .join("circle")
            .attr("class", function(d) {
                return "thumb " + d.User_id + "_" + d.Board_id;
            })
            .style("fill", (d, i) => {
                return color(d.User_id + "_" + d.Board_id)
            })
            .attr("cx", d => this.xAxis(d.Time))
            .attr("cy", d => this.yAxis(d.Cur_count))
            .attr("r", 3)
            .style('opacity', 0.85)
        .on("mouseover", function(event, d) {
            const classId = d.User_id + "_" + d.Board_id;

            d3.select(".popup")
                .style("opacity", .85)
                .attr("xlink:href", d.Thumbnail)
                .style("font-color", "white")
                .attr("x", 228)
                .attr("y",53);

            d3.selectAll('.line')
                             .style('opacity', 0.1);
            d3.selectAll('.thumb')
                             .style('opacity', 0.25);

            d3.select(this)
                .style('opacity', 0.85)
                .style("cursor", "pointer");

            d3.selectAll('.line.' + classId)
                .style('opacity', 0.85);

            self.$parent.selectFromChart(classId);

        })
        .on("mouseout", function(d) {
            d3.select(".popup")
                .style("opacity", .0)

            d3.selectAll('.line')
                             .style('opacity', 0.85);
            d3.selectAll('.thumb')
                             .style('opacity', 0.85);
            
            self.$parent.selectFromChart('');
        });

        this.svgg.select(".y.axis") 
            .call(d3.axisLeft(this.yAxis).ticks(3));
    },
  },
  mounted() {
    var parentDiv = document.getElementById("lineChart-wrap");
    this.width = parentDiv.clientWidth -  this.margin.left - this.margin.right;
    this.height = parentDiv.clientHeight - this.margin.top - this.margin.bottom - 30;

    const svg = d3.selectAll("svg")
        .attr("width", this.width + this.margin.left + this.margin.right)
        .attr("height", this.height + this.margin.top + this.margin.bottom);
    
    this.svgg = svg.append("g")
        .attr("transform",
            "translate(" + this.margin.left + "," + this.margin.top + ")");
        
    var sumstat = d3.group(this.chartdata, d => d.User_id + "_" + d.Board_id);

    // Add X axis --> it is a date format
    this.xAxis = d3.scaleLinear()
        .domain(d3.extent(this.chartdata, d => {
            if (this.curtime < d.Time) return this.curtime;
            return d.Time
        }))
        .range([ 0, this.width ]);

    // Add Y axis
    this.yAxis = d3.scaleLinear()
        .domain([1000, d3.max(this.chartdata, d => +d.Cur_count + 500)])
        .range([ this.height, 0 ]);

    // color palette
    var res = Array.from(sumstat.keys()); // list of group names

    this.svgg.selectAll("path.line")
        .data(sumstat)
        .join("path")
        .attr("class", function(d) {
            return "line " + d[0];
        })
        .attr('fill', 'none')
        .attr('stroke-width', 1.5)
        .attr('stroke', d => color(d[0]))
        .attr("d", d => {
            return d3.line()
                .x(d => this.xAxis(d.Time))
                .y(d => {
                    return this.yAxis(+d.Cur_count)
                })
                (d[1])
            })
        .style('opacity', 0.65)
        // .on("mouseover", function(d) {
        //     d3.selectAll('.line')
        //                     .style('opacity', 0.1);
        //     d3.selectAll('.circle')
        //                     .style('opacity', 0.25);
        //     d3.select(this)
        //         .style('opacity', 0.85)
        //         .style("stroke-width", "2.5px")
        //         .style("cursor", "pointer");
        //     })
        // .on("mouseout", function(d) {
        //     d3.selectAll(".line")
        //                     .style('opacity', 0.65);
        //     d3.selectAll('.circle')
        //                     .style('opacity', 0.85);
        //     d3.select(this)
        //         .style("stroke-width", "1.5px")
        //         .style("cursor", "none");
        //     });

        // this.svgg.selectAll("circle-group")
        //     .data(this.chartdata.filter(function(d){ return d.Thubmnail !=="" }))
        //     .attr("class", "circle")  
        //     .style("fill", (d, i) => color(d.User_id + "_" + d.Board_id))
        //     .append("circle")
        //     .attr("cx", d => this.xAxis(d.Time))
        //     .attr("cy", d => this.xAxis(d.Cur_count))
        //     .attr("r", 5)
        //     .style('opacity', 0.85)
            // .on("mouseover", function(d) {
            //     d3.select(this)
            //     .transition()
            //     .duration(duration)
            //     .attr("r", circleRadiusHover);
            // })
            // .on("mouseout", function(d) {
            //     d3.select(this) 
            //     .transition()
            //     .duration(duration)
            //     .attr("r", circleRadius);  
            // });


    // this.svgg.append("g")
    //     .attr("class", "x axis")
    //     .attr("transform", "translate(0," + this.height + ")")
    //     .call(d3.axisBottom(this.xAxis).ticks(5));

    this.popup = this.svgg
        .append("svg:image")
        .attr("class", "popup") 
        .attr("width", 400)
        .attr("height", 320)
        .style("opacity", 0);

    this.svgg.append("g")
        .attr("class", "y axis")
        .call(d3.axisLeft(this.yAxis).ticks(3));
    
    return(svg.node()); 
  }
};
</script>
