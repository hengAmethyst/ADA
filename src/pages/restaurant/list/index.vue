<template>
  <div class="wrap">
    <div class="head">
      <div class="info">
        <div class="hello">
          <span>Hello，</span>
        </div>
        <div  class="name" @click="bindToUserOrder">
          <span>
            {{userinfo.name}}
          </span>
        </div>
      </div>
      <div class="tool-wrap">
        <div class="tool-box" :class="{'tool-fixed' : !scrollTopFlag}">
          <div class="avatar">
            <div class="image-wrap">
              <img src="http://insurance.awbchina.com/ada/images/restaurant/details/avatar@2x.png" alt="">
            </div>
          </div>
           <div class="what">
            <div class="icon-wrap">
                <i></i>
            </div>
            <div @click="bindToscanQR" class="what-text">
              <span>这是什么菜 ？</span>
            </div>
          </div>
          <screen></screen>   
          <div class="search" @click="bindToSearch">
            <div class="icon-wrap"></div>
          </div>
        </div>
      </div>
    </div>
    <div class="content">
      <ul>
        <li v-for="(rest, restIndex) in restaurantList" :key="rest.id" @click="bindToDetails">
          <div class="left">
            <div class="image-wrap">
              <img :src="rest.header_img" alt="">
            </div>
          </div>
          <div class="restaurant-info">
            <div class="top">
              <div class="state" :class="{ crowding: rest.open_status == 1 }">
              </div>
              <div>
                {{rest.distance}}
              </div>
            </div>
            <div class="center">
              {{rest.name}}
            </div>
            <div class="bottom">
              <span>¥</span>
              <span>{{rest.average_price}}</span>
              <i></i>
              <span>{{rest.street}}</span>
              <i></i>
              <span>{{rest.class_name}}</span>
            </div>
            <div class="crowding-tip" v-if=" rest.statu == 1">
                拥挤需排队
            </div>
          </div>
        </li>
      </ul>
    </div> 
    <invitation :invitationShow="invitationShow" @bindClose = "bindInvClose" @bindBtn = "bindInvBtn"></invitation>   
    <welcome :welcomeShow="welcomeShow" @bindClose = "bindClose" @bindBtn = "bindBtn"></welcome>   
  </div>
</template>

<script>
  import invitation from '@/components/restaurant/invitation';
  import welcome from '@/components/restaurant/welcome';
  import screen from '@/components/restaurant/screen';
  import { fetchRestList } from '@/http/api.js'
  import { mapState, mapMutations } from 'vuex'
  export default {
    components: {
      invitation,
      welcome,
      screen  
    },
    data () {
      return {
        userinfo: {
          name: 'Trista'
        },
        // restaurantList: [
        //   {
        //     id: 1,
        //     image: 'http://cdn.awbchina.com/wximage/default.png',
        //     name: 'A aboluo 阿波罗',
        //     price: '98',
        //     adress: '光明路',
        //     type: '火锅',
        //     state: 1,
        //     range: '100m'
        //   },
        //   {
        //     id: 2,
        //     image: 'http://cdn.awbchina.com/wximage/default.png',
        //     name: '阿波罗阿波罗阿波罗阿波罗',
        //     price: '98',
        //     adress: '光明路',
        //     type: '火锅',
        //     state: 0,
        //     range: '100m'
        //   },
        //   {
        //     id: 3,
        //     image: 'http://cdn.awbchina.com/wximage/default.png',
        //     name: 'A aboluo 阿波罗',
        //     price: '98',
        //     adress: '光明路',
        //     type: '火锅',
        //     state: 1,
        //     range: '100m'
        //   }
        // ],
        restList:[],
        invitationShow:0,
        welcomeShow:0,
        scrollTopFlag:1
      }
    },
    computed: {
      restaurantList:function(){
        let restList = this.restList || [];
        restList.forEach( r => {
          if ( r.distance < 1000 ) {
            r.distance = Math.round(r.distance);
            r.distance = r.distance + 'm'
          }else{
            r.distance = Math.round(r.distance / 1000);
            r.distance = r.distance + 'km'
          }
        })
        return restList
      },
      ...mapState([
        'locationInfo'
      ])
    },
    onPageScroll:function(e){
      if (e.scrollTop >= 120) {
        this.scrollTopFlag = 0
      }else{
        this.scrollTopFlag = 1
      }
    },
    methods: {
      bindClose(){
        this.welcomeShow = 0 ;
      },
      bindBtn(){
        this.welcomeShow = 0 ;
      },
      bindInvClose(){
        this.invitationShow = 0 ;
      },
      bindInvBtn(){
        this.invitationShow = 0 ;
      },
      bindToscanQR(){
        wx.navigateTo({
          url: '/pages/restaurant/scanQR/whatDish/main'
        })
      },
      bindToDetails(){
        wx.navigateTo({
          url: '/pages/restaurant/details/main'
        })
      },
      bindToSearch(){
        wx.navigateTo({
          url: '/pages/restaurant/search/main'
        })
      },
      bindToUserOrder(){
         wx.navigateTo({
          url: '/pages/user/userOrder/main'
        })
      },
      fetchRestList(){
        let _this = this;
        let params = {
            lat: this.locationInfo.latitude,
            lng: this.locationInfo.longitude,
            page: 1
          }
        fetchRestList(params).then(res=>{ 
          let r = res.data
          _this.restList = r.data
        })
      }
    },

    created () {
      this.fetchRestList()
      // 调用应用实例的方法获取全局数据
    },
    onLoad(e){
      if (this.$root.$mp.query.id) {
        this.welcomeShow = 1 ;
      }
      this.scrollTopFlag = 1;
    }
  }
</script>

<style lang="scss" scoped>
  @import "@/sass/common.scss";
  .wrap{
    width: 100%;
    .head{
      width: 100%;
      position:relative;
      background-color: $theme-color;
      color: #fff;
      box-sizing: border-box;
      .info{
        padding-top: 20px;
        height:120px;
        box-sizing:border-box;
        .hello{
          opacity: 0.5;
          color: rgba(253, 253, 253, 1);
          font-size:20px;
          padding-left: 15px;
        }
        .name{
          color: rgba(253, 253, 253, 1);
          font-size: 48px;
          padding-top: 10px;
          text-align: left;
          padding-left: 15px;
          font-family: PingFangSC-Regular;
        }
      }
      .tool-wrap{
        height:103px;
        width:100%;
        .tool-box{
          height:103px;
          width:100%;
           .what{
            font-size:15px;
            padding-top: 20px;
            opacity: 0.5;
            color: rgba(253, 253, 253, 1);
            margin-bottom:30px;
            display:flex;
            padding-left: 15px;
            align-items:center;
            .icon-wrap{
              width:20px;
              height:20px;
              background-image:url($image-url + 'images/restaurant/details/Rectangle10@2x.png');
              background-size:cover;
            }
            .what-text{
              margin-left:8px;
              padding-bottom:2px;
              border-bottom:1px solid rgba(255,255,255,1);
            }
          }
        }
        .tool-fixed{
          background-color: $theme-color;
          z-index:54;
          position:fixed;
          top:0;
          left:0;
          .search{
            right:65px;
            top:20px;
          }
        }
        .avatar{
          position:absolute;
          width:40px;
          height:40px;
          border-radius:50%;
          right:15px;
          top:20px;
          overflow:hidden;
          z-index:55;
          .image-wrap{
            width:100%;
            height:100%;
            img{
              width:100%;
              height:100%;
            }
          }
        }
      }
      .search{
        position: absolute;
        right: 15px;
        bottom: -15px;
        border-radius:50%;
        .icon-wrap{
          background-size:cover;
          border-radius:50%;
          width: 40px;
          height: 40px;
          background-image:url($image-url + 'images/restaurant/details/Group4@2x.png');
          box-shadow: 0px 16px 34px 0px rgba(0, 0, 0, 0.16);
        }
      }
      
    }
    .content{
      width: 100%;
      box-sizing: border-box;
      padding-left: 15px;
      padding-right: 15px;
      padding-top: 40px;
      ul{
        width: 100%;
        li{
          width: 100%;
          display: flex;
          z-index: 9;
          height: 160px;
          box-shadow: 0px 2px 11px 0px rgba(0, 0, 0, 0.11);
          border-radius: 1px;
          position: relative;
          margin-bottom: 80px;
          .left{
            width: 160px;
            .image-wrap{
              width: 150px;
              height: 216px;
              position: absolute;
              top: -10px;
              left: 10px;
              z-index: 10;
              overflow: hidden;
              background-color:#f9f9f9;
              img{
                width: 100%;
              }
            }
          }
          .restaurant-info{
            flex: 1;
            padding:15px 10px 0 10px;
            .top{
              display:flex;
              margin-bottom: 15px;
              color: #999;
              font-size: 12px;
              justify-content: space-between;
              align-items: center;
              .state{
                width: 10px;
                height: 10px;
                background-color: #999;
                border-radius: 50%;
              }
              .crowding{
                background-color: rgba(225, 11, 34, 1);
              }
            }
            .center{
              min-height:22px;
              font-size: 16px;
              font-weight:550;
              margin-bottom: 10px;
            }
            .bottom{
              display: flex;
              align-items: center;
              color: #999;
              font-size: 12px;
              i{
                width: 4px;
                height: 4px;
                display: inline-block;
                background-color: #999;
                margin: 0 8px;
              }
            }
          }
          .crowding-tip{
            position: absolute;
            width:88px;
            height:20px;
            line-height:20px;
            font-size:12px;
            text-align:center;
            letter-spacing: 1px;
            color:#fff;
            background-color:#E10B22;
            right:0;
            bottom:20px;
          }
        }
      }
    }
  }
</style>
