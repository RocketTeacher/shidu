<template>
  <div class="more-rank">

    <div class="back-fixed">
      <div class="back-top">
        <img class="back" @click="$router.back(-1)" src="@/static/img/back-2.png" alt="">
        <span class="tit">更多畅销书排行榜</span>
      </div>
    </div>

    <div class="student-tab reading-tab great">
      <div class="rank">
        <div class="two">
          <img class="m" src="@/static/img/rank-m2.png" alt="">
          <img class="h" :src="rank_2.cover_img" alt="">
          <span>{{rank_2.book_name}}</span>
        </div>
        <div class="one">
          <img class="m" src="@/static/img/rank-m1.png" alt="">
          <img class="h" :src="rank_1.cover_img" alt="">
          <span>{{rank_1.book_name}}</span>
        </div>
        <div class="three">
          <img class="m" src="@/static/img/rank-m3.png" alt="">
          <img class="h" :src="rank_3.cover_img" alt="">
          <span>{{rank_3.book_name}}</span>
        </div>
      </div>
      <van-pull-refresh v-model="pull_loading" @refresh="onRefresh" success-text="刷新成功">
				<van-list v-model="list_loading" :finished="finished" loading-text="加载中..." :offset="off_h" :finished-text="finished_text" @load="onLoad">
          <div class="list list-3" v-for="(v,i) in list" :key="v.book_id" @click="$router.push({ path: `/books/${v.book_id}` })">
            <span class="num">{{i+1}}</span>
            <img class="img" :src="v.cover_img" alt="">
            <div class="cont">
              <label>{{v.book_name}}</label>
              <i>{{v.intro}}</i>
              <span>{{v.author}}</span>
            </div>
          </div>
        </van-list>
			</van-pull-refresh> 
    </div>

  </div>
</template>

<script>
import Book from '@/api/books'
export default {
  name: 'moreRank',
  data(){
    return{
      list_loading: false,
			finished: false,
			page: 0,
			pull_loading: false,
			off_h: 10,
      finished_text: '没有更多了',
      list: [],
      rank_1: {},
      rank_2: {},
      rank_3: {},
    }
  },
  methods:{
    //下拉刷新
    onRefresh() {
      setTimeout(() => {
        this.page = 1;
        this.finished = false;
        this.get_rank_list();
      },800);
    },
    //上拉加载
    onLoad() {
      setTimeout(() => {
        this.page = this.page + 1;
        this.get_rank_list();
      }, 500);
    },
    get_rank_list(){
      let params = {
        member_id: this.$memberId(),
        page: this.page
      }
      Book.getRankList(params).then( res => {
        let data = res.data;
        if (this.page == 1) {
          this.list = data;
          this.rank_1 = data[0];
          this.rank_2 = data[1];
          this.rank_3 = data[2];
        }else {
          this.list.push(...data);
        }
        this.list_loading = false;
        this.pull_loading = false;
        if (!data.length) {
          this.finished = true;
        }
      }).catch( () => {
        this.list_loading = false;
				this.pull_loading = false;
				this.finished = true;
				this.finished_text = '加载失败,请重新下拉刷新';
      })
    },
  }
}
</script>

<style lang="scss" scoped>
  .more-rank{    
    .reading-tab{
      margin-bottom: 15px;
      padding: 0 12px;
      .rank{
        width: 100%;
        height: 188px;
        background: url(../../static/img/rank-bg.png) center / cover no-repeat;
        margin: 0 auto 6px;
        display: flex;
        align-items: flex-end;
        .two, .three, .one{
          width: 90px;
          height: 121px;
          background: url(../../static/img/rank-t-bg2.png) center / cover no-repeat;
          position: relative;
          display: flex;
          flex-direction: column;
          align-items: center;
          justify-content: center;
          .m{
            width: 40px;
            height: 40px;
            position: absolute;
            left: 0;
            top: 0;
            z-index: 10;
          }
          .h{
            width: 48px;
            height: 65px;
            margin-bottom: 6px;
          }
          span{
            width: 70%;
            font-size: 12px;
            color: #333;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
          }
        }
        .three{
          background: url(../../static/img/rank-t-bg3.png) center / cover no-repeat;
        }
        .one{
          width: 110px;
          height: 157px;
          background: url(../../static/img/rank-t-bg1.png) center / cover no-repeat;
          .m{
            width: 46px;
            height: 46px;
          }
          .h{
            width: 64px;
            height: 86px;
            margin-bottom: 6px;
          }
        }
      }
      .list{
        display: flex;
        padding-bottom: 8px;
        margin-bottom: 10px;
        border-bottom: 1px solid #E5E5E5;
        &.list-3{
          align-items: center;
          .num{
            font-size: 16px;
            color: #333;
            padding: 0 5px;
          }
          .img{
            width: 56px;
            height: 76px;
          }
          .cont{
            width: 200px;
          }
        }
        .img{
          width: 60px;
          height: 82px;
          border-radius: 5px;
          margin-right: 8px; 
        }
        .cont{
          width: 230px;
          display: flex;
          flex-direction: column;
          justify-content: space-between;
          label{
            font-size: 16px;
            color: #333;
            margin-bottom: 3px;
          }
          i{
            line-height: 18px;
            margin-bottom: 5px;
            text-overflow: -o-ellipsis-lastline;
            overflow: hidden;
            text-overflow: ellipsis;
            -webkit-line-clamp: 2;
            display: -webkit-box;
            line-clamp: 2;
            -webkit-box-orient: vertical;
          }
          i,span{
            font-size: 12px;
            color: #999;
          }
        } 
      }
      .list:last-child{
        margin-bottom: 0;
      }
    }
    .reading-tab.great{
      .list:last-child{
        border-bottom: none; 
      }
    }
    .student-tab{
      .tab{
        display: flex;
        align-items: center;
        justify-content: space-between;
        margin-bottom: 12px;
        label{
          font-size: 16px;
          color: #333;
          position: relative;
        }
        label::before{
          display: inline-block;
          content: '';
          width:2px;
          height:12px;
          background:linear-gradient(180deg,rgba(151,190,255,1) 0%,rgba(105,158,246,1) 100%);
          border-radius:1px;
          margin-right: 3px;
        }
        a{
          font-size: 10px;
          color: #699EF6;
        }
      }
      .list-3{
        display: flex;
        flex-wrap: wrap;
        .item{
          width: 140px;
          height: 64px;
          background: url(../../static/img/book-bg.png) center / cover no-repeat;
          margin: 0 10px 10px 0;
          display: flex;
          align-items: center;
          padding-left: 5px;
          box-sizing: border-box; 
          &:nth-child(2n){
            margin-right: 0;
          }
          img{
            width: 52px;
            height: 52px;
            margin-right: 10px;
          }
          div{
            width: 70px;
            font-size: 14px;
            label, span{
              display: block;
            }
            label{
              display: block;
              color: #333;
              margin-bottom: 5px;
            }
            span{
              color: #999;
            }
          }
        }
      }
    }
    .mb12{
      margin-bottom: 12px;
    }
  }
</style>