<template>

  <div class="gouwuche" v-if="gouwuche">
    <div>
      <p>全部商品</p>
      <div>
        <table>
          <tbody>
          <tr class="tr_size">
            <th><input type="checkbox" @click='box_go' v-model="gx"></th>
            <th>商品名称</th>
            <th>数量</th>
            <th>单价（元）</th>
            <th>金额（元）</th>
            <th>操作</th>
          </tr>
          </tbody>
          <tbody>
          <tr v-for='(tmp,index) in gouwuche' class="commodity">
            <!--            勾选状态-->
            <th><input type="checkbox" v-model="tmp.checkbox"
                       @change="checkChange(index)"></th>
            <!--详情-->
            <th>{{ tmp.shuoming }}</th>
            <th class="inputpeizhi">
              <button @click="numjian(index)">
                -
              </button>
              <!--              数量-->
              <input
                  type="text"
                  v-model="tmp.num"
                  class="in-put">
              <button @click="numjia(index)">
                +
              </button>
            </th>
            <!--            单价-->
            <th class="content">
              {{ tmp.name }}
            </th>
            <!--小计-->
            <th class="jiner">
              {{ tmp.name * tmp.num }}
            </th>

            <th class="dellll">
              <el-button
                  type="danger"
                  icon="el-icon-delete"
                  @click="del(index)">
                删除
              </el-button>
            </th>
          </tr>
          </tbody>

        </table>
        <div class="shoppingBar-footer">
          <el-button type="success" plain>
            去结算
          </el-button>
          <!--          allshop 为总计n件商品 allmoney为总计价格-->
          <div class="buy">
            <span>
              {{ allshop() }}
            </span> 件商品总计:
            <span>
              {{ allmoney() }}
            </span>
          </div>
          <el-button
              type="danger"
              icon="el-icon-delete"
              @click="shanchushangpin"
          >
            删除选中商品
          </el-button>
          <el-button
              align="right"
              type="danger"
              icon="el-icon-delete"
              @click="qingkonggouwuche">
            清空购物车
          </el-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {mapMutations, mapState} from 'vuex'

export default {
  data() {
    return {
      gouwuche: [],
      box: '-1',  //勾选状态
      gx:false
    }
  },
  computed: {
    ...mapState(['uname']),
    isCheckAll() {
      // 每一个元素, 都使用箭头函数判断, 返回真还是假
      console.log(this.gouwuche.every(p => p.checked))
      return this.gouwuche.every(p => p.checked)
    },
  },
  methods: {

    ...mapMutations(['namebaocun']),
    getData() {
      const url = 'zsg/gwcchaxun'
      this.axios.post(url, {
        uname: this.uname
      }).then(res => {
        this.gouwuche = res.data
      })
    },
    //num数量-1
    numjian(index) {
      let formBody = Object.assign(this.gouwuche[index], {});
      this.index = index
      const url = 'zsg/gwcupdate'
      if (formBody.num> 1) {
        this.axios.post(url, {bid: formBody.bid, num: formBody.num - 1}).then(res => {
              this.gouwuche = res.data
              this.getData()
            }
        )
      }
    },
    //num数量+1
    numjia(index) {
      let formBody = Object.assign(this.gouwuche[index], {});
      this.index = index
      const url = 'zsg/gwcupdate'
      this.axios.post(url, {bid: formBody.bid, num: formBody.num + 1})
          .then(res => {
        this.gouwuche = res.data
        this.getData()
      })

    },
    //购物车删除
    del(index) {
      let formBody = Object.assign(this.gouwuche[index], {});
      const url = 'zsg/gwcdelete'
      this.axios.post(url, {bid: formBody.bid}).then(res => {
        if (res.code == 200) {
          this.$message({
            message: '删除成功',
            type: 'success'
          })
        } else {
          this.$message({
            showClose: true,
            message: '删除失败',
            type: 'warning'
          });
        }
        this.getData()
      })
    },
    //删除选中商品
    shanchushangpin() {
      const url = 'zsg/xzcdelete'
      this.axios.post(url, {
        uname:this.uname,
        checkbox:1}).then(res => {
        if (res.code == 200) {
          this.$message({
            message: '删除成功',
            type: 'success'
          })
        } else {
          this.$message({
            showClose: true,
            message: '删除失败',
            type: 'warning'
          });
        }
        this.getData()

      })
    },
    //清空购物车
    qingkonggouwuche() {
      const url = 'zsg/qkcdelete'
      this.axios.post(url, {uname: this.uname}).then(res => {
        if (res.code == 200) {
          this.$message({
            message: '清空购物车成功',
            type: 'success'
          })
        } else {
          this.$message({
            showClose: true,
            message: '清空购物车失败',
            type: 'warning'
          });
        }
        this.getData()
      })
    },
    //商品总计多少件
    allshop() {
      let arr = [...this.gouwuche];
      let num = 0;
      for (var i in this.gouwuche) {
        if (this.gouwuche[i].checkbox) {
          num += parseInt(arr[i].num);
        }
      }
      return num

    },
    //商品总计价格
    allmoney() {
      let arr = [...this.gouwuche];
      let num = 0;
      for (var i in this.gouwuche) {
        if (this.gouwuche[i].checkbox) {
          num += arr[i].name * arr[i].num
        }
      }
      return num
    },
    checkChange(index){
      let formBody = Object.assign(this.gouwuche[index], {});
      const url = 'zsg/gxupdate'
      this.index=index
    this.axios.post(url, {bid:formBody.bid,checkbox:
      formBody.checkbox}).then(res => {

      })

    },
    //勾选全选
    box_go() {
        // 此方法是 勾选框 勾选操作触发的, 如何得到勾选框的 勾选状态?
        // 事件参数

      const url = 'zsg/qxupdate'
      this.axios.post(url, {
        bid:this.bid,
        uname:this.uname,
        checkbox:this.gx}).then(res => {
        this.getData()
      })
    },

  },
  mounted() {
    this.getData()
  },

}
</script>

<style lang="scss" scoped>
.gouwuche {
  margin: auto;
  width: 80%;
}

.tr_size {
  th:not(:first-child) {
    width: 20%;
  }

  th {
    border-bottom: 1px solid #ccc;
  }
}

th {
  line-height: 40px;
}

.commodity {
  th {

    border-bottom: 1px solid #ccc;
  }
}

.inputpeizhi {
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
}
</style>
