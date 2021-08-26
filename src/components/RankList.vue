

<template>
<div>
    <h2 @click="clickTitle">아프리카tv 실시간 랭킹</h2>
    <div class="rank-wrap">
        <div class="item" 
            v-for="item in sortedListdata" :key="item.User_id + '_' + item.Board_id" 
            @mouseenter="active=item.User_id + '_' + item.Board_id" @mouseleave="active=''" 
            :class="[active==(item.User_id + '_' + item.Board_id) || selectStreamId==(item.User_id + '_' + item.Board_id)? 'active': '']" 
            :id="item.User_id + '_' + item.Board_id">
            <div class="img">
                <img :src='`http://liveimg.afreecatv.com/m/${item.Board_id}?${Math.round(new Date().getTime()/10000)}`' />
            </div>
            <div class="desc">
                <div class="title" :title="item.Broad_title">
                    <a :href="`https://play.afreecatv.com/${item.User_id}/${item.Board_id}`" target="_blank">{{item.Broad_title}}</a>
                </div>
                <div class="nick">{{item.Nick}}</div>
                <div class="count-wrap">
                    <span class="count_icon"></span>
                    <animated-number class="count" :number="item.Cur_count" :prev-number="item.Prev_count"></animated-number>
                </div>
            </div>
        </div>
    </div>
    </div>
</template>
<style>
.rank-wrap {
    width: 100%;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: flex-start;
    overflow-y: scroll;
    height: calc(100vh - 100px);
    border: solid 0.1rem gray;

}
.rank-wrap .item {
    width: 100%;
    padding: 1rem;
    /* border: 1px solid #888; */
    display: grid;
    grid-template-columns: 40% 60%;
	grid-template-rows: 100%;
}
@media screen and (min-width: 940px) {.rank-wrap .item {width: 100%;}}
@media screen and (min-width: 1450px)  {.rank-wrap .item {width: 50%;}}
/* @media screen and (min-width: 1680px) {.rank-wrap .item {width: 33%;}} */

.rank-wrap .item.active {
    background-color: #e9e5f7;;
}
.rank-wrap .item .img {
    width: 100%;
    display: inline-block;
}
.rank-wrap .item .img img {
    width: 100%;
    border-radius: 8px;    
}
.rank-wrap .item .desc {    
    width: 100%;
    padding-right: 1rem;
    padding-left: 1rem;
    display: inline-block;
    position: relative;
}
.rank-wrap .item .title {
    width: 100%;
    display: block; 
    display: -webkit-box; 
    -webkit-line-clamp: 2; 
    height: 5rem;
    line-height: 2.5rem; 
    -webkit-box-orient: vertical; 
    overflow: hidden; 
    text-overflow: ellipsis;
    text-align: left;
}
@media screen and (min-width: 540px) {.rank-wrap .item .title {-webkit-line-clamp: 2; height: 5rem;}}
@media screen and (min-width: 720px)  {.rank-wrap .item  .title {-webkit-line-clamp: 1; height: 2.5rem;}}
@media screen and (min-width: 960px) {.rank-wrap .item .title {-webkit-line-clamp: 1; height: 2.5rem;}}
@media screen and (min-width: 1140px) {.rank-wrap .item .title {-webkit-line-clamp: 1; height: 2.5rem;}}

.rank-wrap .item .title a {
    color: black;
}
.rank-wrap .item .nick {
    position: absolute;
    bottom: 0px;
    left: 1rem;
    color: #4279ff;
}
.rank-wrap .item .count-wrap {
    position: absolute;
    bottom: 0px;
    right: 1rem;
    color: #888;
}

.rank-wrap .item .count-wrap .count_icon {
    content: "";
    display: inline-block;
    flex: 1 0 auto;
    background: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 12 9'%3e%3cpath fill-rule='evenodd' fill='%23ADADAD' d='M11.98 9H3.54c-.23-2.04 1.54-4 4.22-4 1.44 0 2.68.6 3.44 1.52.6.71.89 1.6.78 2.48zM7.76 4.22c-1.1 0-2-.94-2-2.11S6.67 0 7.77 0s2 .94 2 2.1c0 1.18-.9 2.12-2 2.12zm-4.55.93c-.88 0-1.6-.76-1.6-1.7 0-.93.72-1.69 1.6-1.69.88 0 1.6.76 1.6 1.7 0 .93-.72 1.69-1.6 -1.31zm.44.51l-.1.11A4.43 4.43 0 002.52 9H0c.2-1.88.77-3.34 3.52-3.34h.13z'/%3e%3c/svg%3e") 50% 50% no-repeat;
    background-size: 100% 100%;
    width: 1.5rem;
    height: 1rem;
    margin: 0px 2px 0 0;
}
</style>
<script>
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

import AnimatedNumber from "@/components/AnimatedNumber.vue";
export default {
  name: "ranklist",
  components: {
      AnimatedNumber
  },
  props: ['livedata', 'selectStreamId'],
  data() {
    return {
        active: null,
    };
  },
  created: function () {
    //console.log( "livedata", this.livedata);
  },
  computed: {
    sortedListdata: function() {
        function compare(a, b) {
            return b.Cur_count - a.Cur_count;
        }
        const list = this.livedata.sort(compare).filter(function(d) { return d.Cur_count > 0});
        return list;
    }
  },
  watch: {
      "livedata": function(newdata, olddata) {
      },
      "selectStreamId": function(newstreamid, oldstreamid) {
          if( newstreamid && newstreamid !== "") {
            this.selectStreamId = newstreamid;
            setTimeout(function() {
                document.getElementById(newstreamid).scrollIntoView();
            }, 100);
          }
      }
  },
  methods: {
    updateChart() {
    },
    mounted() {
    },
    // enterItem( item) {
    //     const streamid = item.User_id + "_" + item.Board_id;
    //     this.$parent.selectFromList(streamid);
    // },
    // leaveItem(item) {
    //     this.$parent.selectFromList('');
    // }
    clickTitle() {
        if (this.sortedListdata.length > 0) {
            console.log(" click");
            const tmp_item = this.sortedListdata[0];
            const selectStreamId = tmp_item.User_id + '_' + tmp_item.Board_id
            setTimeout(function() {
                document.getElementById(selectStreamId).scrollIntoView();
            }, 100);
        }
    }
  }
};
</script>
