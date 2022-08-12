<template>


  <div>
    <div class="mY-heater">
      <!--  左侧-->
      <div class="mY-left">
        <div class="mY-left-top" v-if="urLimg">
          <img style="width: 400px;height: 400px;"
               :src="urLimg[hoverIndex].img" alt="">
          <div class="mY-left-top-lg" v-for="(item,index) in urLimg">
            <img @mouseover="houverInde(index)"
                 style="width: 60px;height: 60px;"
                 :src="item.img"
                 alt="">
          </div>

        </div>
      </div>
      <!--  右侧  -->

      <div class="mY-right" style="width: 65%;background: #ccc">
        <div class="pxiangqing" v-if="shoujia">
          <p>小米手机: <span>{{ shoujia.shuoming }}</span>
          </p>
          <p>小米详情:{{ shoujia.mingcheng }}</p>
          <p>小米配置:{{ shoujia.peizhi }}</p>
          <p>售价:{{ shoujia.name }}</p>
        </div>
        <div class="pxiangqing" v-else="shoujia2">
          <div v-for="item in shoujia2">
          <p>小米手机: <span>{{ item.shuoming }}</span>
          </p>
          <p>小米详情:{{ item.mingcheng }}</p>
          <p>小米配置:{{ item.peizhi }}</p>
          <p>售价:{{ item.name }}</p>
        </div>
        </div>
        <div class="my-shuliang">
          <p> 数量: 1</p>
          <button @click="num--">-</button>
          <input type="text" v-model="num">
          <button @click="num++">+</button>
        </div>

        <div>
          <button>共买</button>
        </div>
        <div>
          <button @click="gouwuche">添加购物车</button>

        </div>

      </div>
    </div>
  </div>
</template>

<script>
import _ from "lodash";
import {mapMutations, mapState} from 'vuex'

export default {

  data() {
    return {
      data: '',
      list: '',
      list2: '',
      urLimg: '',
      hoverIndex: '0',
      shoujia: '',
      shoujia2: '',
      num: '1',
    }
  },
  mounted() {
    this.getData()
    this.geData()
    this.gtData()
    this.gETData()
    this.gETata()
  },
  computed: {
    ...mapState(['uname'])
  },
  methods: {
    ...mapMutations(['namebaocun']),
    houverInde: _.throttle(function (index) {
      this.hoverIndex = index
    }, 50),
    gouwuche() {
      const path='location/woshizhu?id'
      const url = 'zsg/gouwuche'
      if (this.uname == null) {
        this.$message({
          showClose: true,
          message: '请先登录',
          type: 'warning'
        });
      } else {
        let body = Object.assign(this.shoujia, {});
        body.uname = this.uname
        body.num = this.num

        this.axios.post(url, body).then(res => {
              if (res.code == 200) {
                this.$message({
                  message: '添加购物车成功',
                  type: 'success'
                })
              } else {
                this.$message({
                  showClose: true,
                  message: '添加购物车失败',
                  type: 'warning'
                });
              }
            }
        )
      }
    },
    //二级分类获取
    getData() {

      let query = {};
      query.nid = this.$route.query.nid
      console.log(query.nid)
      const url = 'zsg/ejcha'
      this.axios.post(url, {
        nid: query.nid
      }).then(res => {
        this.list = res.data
        console.log('nid:', res)

      })

    },
    //三级分类获取
    geData() {
      let query = {};
      query.id = this.$route.query.id

      const url = 'zsg/sjcha'
      this.axios.post(url, {
        id: query.id
      }).then(res => {
        this.list2 = res.data
        console.log('id:', res)
      })

    },
    //左侧
    gtData() {
      const url = 'zsg/imglunbo'
      this.axios.get(url).then(res => {
        this.urLimg = res.data
        console.log('lunbo:', res)
      })
    },
    //二级分类的购物车
    gETData() {
      let query = {};
      query.sid = this.$route.query.nid
      const url = 'zsg/shoujia'
      this.axios.post(url, {
        sid: query.sid
      }).then(res => {
        this.shoujia2 = res.data
        console.log('2gwc:', res)
      })
    },
    //三级分类的购物车
    gETata() {
      let query = {};
      query.id = this.$route.query.id
      const url = 'zsg/shoujia'
      this.axios.post(url, {
        sid: query.id
      }).then(res => {
        this.shoujia = res.data ? res.data[0] : null
        console.log('3gwc:', res)
      })
    },
  },
  watch: {
    //购物车数量的监听设置
    num(to, from) {
      const reg = /^[1-9]\d*$/
      if (!reg.test(to)) this.num = from
      if (this.num > 10) this.num = 10
      if (this.num < 1) this.num = 1
    }
  }
}
</script>

<style lang="scss" scoped>
.mY-heater {
  display: flex;
  justify-content: space-between;
  width: 80%;
  margin: auto;
}

.mY-left-top {

  height: 400px;
  width: 400px;

  .mY-left-top-lg {
    margin-top: 70px;
    display: inline-block;
  }

}

.my-shuliang {
  p {
    margin: 30px 0;
  }

  line-height: 16px;

  button {
    width: 36px;
    text-align: center;
    border: 1px solid #ddd;
    vertical-align: middle;
    padding: 8px 0px;
    font-size: 14px;
    cursor: pointer;
    outline: none;
  }

  input {
    margin: 0 5px;
    width: 36px;
    text-align: center;
    border: 1px solid #ddd;
    vertical-align: middle;
    padding: 8px 0;
    font-size: 14px;
    cursor: pointer;
    outline: none;
  }

  margin-bottom: 30px;

}

.pxiangqing {
  line-height: 40px;
}
</style>
<style lang="scss">

</style>
