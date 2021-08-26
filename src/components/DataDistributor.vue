<template>
    <div>
        <div class="noti" v-if="state==0">서버에 연결 중...</div>
        <div class="noti" v-if="state==1">데이터를 받아오는 중..</div>
        <div class="noti" v-if="state==-1">Fail to Connect</div>
        <div v-if="state==2" class="body-wrap">
            <div class="rank-container">
                <rank-list :livedata="rankdata" :select-stream-id="selectStreamIdFromList"></rank-list>
            </div>
            <!-- <div class="divider">
                <div class="ad"></div>
            </div> -->
            <div class="up-container">
                <up-list :livedata="updata"></up-list>
            </div>
        </div>
    </div>
</template>
<style>
.noti {
    font-size: 5rem;
    line-height: 100vh;
}
.body-wrap {
    width: 80%;
    height: calc(100vh);
    display: flex;
    margin: auto;
}
.rank-container {
    width: calc(100% - 350px);
    height: calc(100vh);
    min-height: 600px;
    margin-right: 10px;
    display: flex;
}
/* .divider {
    width: 160px;
    height: calc(100vh);
    min-height: 600px;
    display: flex;
}
.divider .ad {
    width: 160px;
    height: 600px;
    margin: auto;
    background-color: black;
} */
.up-container{
    width: 350px;
    height: calc(100vh);
    min-height: 600px;
    display: inline-block;
}
@media screen and (max-width: 1080px)  {
    .rank-container,
    .divider {
        display: none;
    }
    .up-container {
        width: 100%;
    }
}
/* @media screen and (min-width: 960px) {.rank-wrap .item .title {-webkit-line-clamp: 1; height: 2.5rem;}}
@media screen and (min-width: 1140px) {.rank-wrap .item .title {-webkit-line-clamp: 1; height: 2.5rem;}} */

</style>
<script>

import LineChart from "@/components/LineChart.vue";
import RankList from "@/components/RankList.vue";
import UpList from "@/components/UpList.vue";

// var color = function(str) {
//     var hash = 0;
//     for (var i = 0; i < str.length; i++) {
//         hash = str.charCodeAt(i) + ((hash << 5) - hash);
//     }
//     var colour = '#';
//     for (var i = 0; i < 3; i++) {
//         var value = (hash >> (i * 8)) & 0xFF;
//         colour += ('00' + value.toString(16)).substr(-2);
//     }
//     return colour;
// }

export default {
  name: "datadistirbutor",
  components: {
    LineChart,
    RankList,
    UpList,
  },
  data() {
    return {
      websocketUrl: "wss://" + process.env.VUE_APP_BASE_URL + "/ws",
      send_message: null,
      connected: false,
      chartdata: [],
      rankdata: [],
      updata: {},
      curtime: 0,
      timer: null,
      timer_count: 0,
      selectStreamIdFromChart: null,
      selectStreamIdFromList: null,
      state: 0,
    };
  },
  created: function () {
    var getColor = function(str) {
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

    // fetch('http://localhost:8080/init').then( response => {
    //     if(response.ok) {
    //         return response.json();
    //     }
    //     throw new Error('error');
    // }).then( initData=> {
    //     console.log( "initData", initData);

    //     this.chartdata = initData["Prev"].Data;
    //     this.update( initData["Live"].Data, initData["Live"].Time)
    //     this.livedata = initData["Live"].Data;
    
        // connect websocket
        this.socket = new WebSocket(this.websocketUrl);
        this.socket.onopen = () => {
            console.log("connected to " + this.websocketUrl);
            this.connected = true;
            this.state = 1;
        };

        this.socket.onerror = error => {
            console.log("could not connect to " + this.websocketUrl);
            console.log(error);
            this.state = -1;
        };

        this.socket.onmessage = message => {
            this.state = 2;
            let data = JSON.parse(message.data);
            if(data["rank"].Data) {
                this.rankdata = data["rank"].Data;
            }
            if(data["up"].Data) {
                this.updata = data["up"];
            }
        };

        this.socket.onclose = error => {
            console.log("disconnect from " + this.websocketUrl);
            this.connected = false;
            console.log(error);
        };
    //});
  },
  methods: {
    selectFromList( streamid) {
        this.selectStreamIdFromList = streamid;
    }
  },
  mounted() {
    // this.connect();
  }

  // cur time value > through timer / but consider a lazy initializing
  // data
  // 
};
</script>