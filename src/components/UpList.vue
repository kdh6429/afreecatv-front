

<template>
<div>
    <h2>시청자 급상승 장면</h2>
    <div class="up-wrap">
        <div class="fit-wrap">
            <ins class="kakao_ad_area" style="display:none;" 
                data-ad-unit    = "DAN-7iZhBxIZM0Li05dB" 
                data-ad-width   = "320" 
                data-ad-height  = "100"></ins> 
        </div>
        <div class="item" 
            v-for="item in upList" :key="item.User_id + '_' + item.Board_id + '_' + item.Time" 
            @mouseenter="enterItem(item); active=item.User_id + '_' + item.Board_id + '_' + item.Time" 
            @mouseleave="leaveItem(item); active=''" 
            @click="clickItem(item)"
            :class="[active==(item.User_id + '_' + item.Board_id + '_' + item.Time)? 'active': '']" 
            :id="item.User_id + '_' + item.Board_id">
            <up-item :item="item"></up-item>
        </div>
    </div>
    </div>
</template>
<style>
.logo {
    width: 50px;
    border-radius: 10px;;
}
.up-wrap {
    display: block;
    overflow-y: scroll;
    overflow-x: hidden;
    height: calc(100vh - 100px);
    min-height: 0px;
    border: solid 0.1rem gray;
}
.up-wrap .fit-wrap{
    background-color: white;
    width: 100%;
    height: 120px;
    margin: auto;
    padding-top: 10px;
    padding-bottom: 10px;
    position: -webkit-sticky;
    position: sticky;
    top: 0;
    z-index: 9;
}
.up-wrap .fit{
    background-color: black;
    width: 320px;
    height: 100px;
    margin: auto;
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
import UpItem from "@/components/UpItem.vue";
export default {
  name: "uplist",
  components: {
      UpItem,
  },
  props: ['livedata'],
  data() {
    return {
        active: null,
        upList : []
    };
  },
  created: function () {
    //console.log( "livedata", this.livedata);
    this.updateUpItems(this.livedata);
  },
  mounted: function() {
      let recaptchaScript = document.createElement('script')
      recaptchaScript.setAttribute('src', '//t1.daumcdn.net/kas/static/ba.min.js')
      document.head.appendChild(recaptchaScript)
  },
  computed: {
    
  },
  watch: {
      "livedata": function(newdata, olddata) {
          this.updateUpItems(newdata);
      },
  },
  methods: {
    updateChart() {
    },
    mounted() {
    },
    enterItem( item) {
        const streamid = item.User_id + "_" + item.Board_id;
    },
    leaveItem(item) {
        this.$parent.selectFromList('');
    },
    clickItem(item) {
        const streamid = item.User_id + "_" + item.Board_id;
        this.$parent.selectFromList(streamid);
    },
    updateUpItems(data) {
          if(!data["Data"]) return;
          const type = data["Type"];
          const list = [...data["Data"].filter(function(d) { return d.Cur_count > 0 && d.Thumbnail != ""})];
          const updateItemCount = list.length;
          const eachInterval = Math.round(8000 / updateItemCount-1);
          let sumInterval = 0;
          if(list.length > 0) {
            const random_interval = Math.floor(Math.random() * 100) + 500;
            list.forEach(function(item) {
                var interval = sumInterval + random_interval + eachInterval;
                if (type =="prevup") interval = 0;
                sumInterval += random_interval + eachInterval;
                item.interval = interval;
            });
            list.reverse();
            this.upList.unshift(...list);
            if (this.upList.length > 30) {
                this.upList.slice(0, 30);
            }
          }
    }
  }
};
</script>
