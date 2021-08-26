

<template>
    <div class="up-item-wrap" :class="[isActive? 'active':'']">
        <div class="img">
            <img :src='item.Thumbnail' />
        </div>
        <div class="title">
            <a :href="`https://play.afreecatv.com/${item.User_id}/${item.Board_id}`" target="_blank">{{item.Nick}} - {{item.Broad_title}}</a>
        </div>
        <div class="ment">{{item.Up_ment}}</div>
        <div class="ago">{{ago_ment}}</div>
    </div>
</template>
<style>

.up-item-wrap {
    display: none;
    width: 100%;
    padding: 1rem;
    position: relative;
    cursor: pointer;
}
.up-item-wrap.active {
    display: block;
    padding: 1rem;
}
.up-item-wrap .img {
    width: 100%;
    display: inline-block;
}
.up-item-wrap .img img {
    width: 100%;
    border-radius: 8px;    
}
.up-item-wrap .title {
    position: absolute;
    top: 15px;
    left: 14px;
    width: 90%;
    padding: 1px;
    text-shadow: -1px 0 black, 0 1px black, 1px 0 black, 0 -1px black;
    font-weight: bold;
    font-size: 1.2rem;
    display: block; 
    display: -webkit-box; 
    -webkit-line-clamp: 1; 
    height: 1.3rem;
    line-height: 1.2rem; 
    -webkit-box-orient: vertical; 
    overflow: hidden; 
    text-overflow: ellipsis;
    text-align: left;
}
.up-item-wrap .title a{ 
    color: #ffffff;
}
.up-item-wrap .title a:hover{ 
    color: #0905ff;
}
.up-item-wrap .ment {
    color: #ff6363;
    padding: 1px;
    font-weight: bold;
    font-size: 1.2rem;
    position: absolute;
    bottom: 13px;
    background-color: white;
    border: solid 0.1rem;
    left: 13px;
    border-radius: 5px;
}
.up-item-wrap .ago {
    color: #e2e2e2;
    padding: 1px;
    font-weight: bold;
    font-size: 1.2rem;
    position: absolute;
    bottom: 13px;
    /* background-color: rgb(255 255 255); */
    /* border: solid 0.1rem; */
    right: 13px;
    border-radius: 5px;
    text-shadow: -1px 0 black, 0 1px black, 1px 0 black, 0 -1px black;
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

function getMent(ago) {
    if (ago < 60) {
        return ago + "초 전";
    }
    return Math.round(ago/60) + "분 전";
}
export default {
  name: "uplist",
  components: {
  },
  props: ['item'],
  data() {
    return {
        active: null,
        upList : [],
        isActive: false,
        ago_ment: "0초전",
    };
  },
  created: function () {
    window.setTimeout(() => {
        this.isActive = true;
    }, this.item.interval);

    const ago = Math.round(((new Date().getTime()/1000) - this.item.Time) - this.item.interval/1000);
    this.ago_ment = getMent(ago);

    window.setInterval(() => {
        const ago = Math.round(((new Date().getTime()/1000) - this.item.Time) - this.item.interval/1000);
        this.ago_ment = getMent(ago);
    }, 2000);
  },
  computed: {
    
  },
  watch: {
  },
  methods: {

  }
};
</script>
