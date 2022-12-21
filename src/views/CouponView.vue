<template>
  <div class="data-map">
    <el-button round @click="handleAdd" class="add-btn">添加
    </el-button>
    <el-table
      :data="coupons"
      :header-cell-style="{ textAlign: 'center' }"
      :cell-style="{ textAlign: 'center' }"
      style="border-radius: 8px"
    >
      <el-table-column
        prop="couponId"
        label="ID"
        width="120">
      </el-table-column>
      <el-table-column
        label="图片"
        width="200">
        <template slot-scope="scope">
          <img :src=getSrc(scope.row.couponImages) alt="加载失败"
               style="height: 100px; width: 160px;border-radius: 4px">
        </template>
      </el-table-column>
      <el-table-column
        prop="couponDesc"
        label="描述"
        width="300">
      </el-table-column>
      <el-table-column
        prop="couponValue"
        label="价值"
        width="120">
      </el-table-column>
      <el-table-column
        prop="couponAvailable"
        label="是否可用"
        width="120">
      </el-table-column>
      <el-table-column
        prop="createTime"
        label="插入时间"
        width="150">
      </el-table-column>
      <el-table-column
        prop="updateTime"
        label="更改时间"
        width="150">
      </el-table-column>
      <el-table-column
        label="操作"
        width="200">
        <template slot-scope="scope">
          <el-button @click="handleEdit(scope.row) ;editFormVisible = true" type="primary" size="small">编辑
          </el-button>
          <el-button @click="handleDelete(scope.row)" type="danger" size="small">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 编辑弹框 -->
    <el-dialog title="修改" width="50%" :visible.sync="editFormVisible" style="border-radius: 4px">
      <el-form :model="temp_item">
        <el-form-item label="ID" :label-width="formLabelWidth">
          <el-input disabled="true" v-model="temp_item.couponId" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="图片" :label-width="formLabelWidth">
          修改前：<br>
          <el-image :src=getSrc(temp_item.couponImages)
                    style="height: 150px;width: 150px;border-radius: 4px"></el-image>
          <br>
          修改后：
          <br>
          <el-upload
            action="#"
            list-type="picture-card"
            :auto-upload="false"
            :on-change="handleChange"
            :limit="1"
          >
            <i slot="default" class="el-icon-plus"></i>
          </el-upload>
        </el-form-item>
        <el-form-item label="描述" :label-width="formLabelWidth">
          <el-input v-model="temp_item.couponDesc" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="价值" :label-width="formLabelWidth">
          <el-input v-model="temp_item.couponValue" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="可用" :label-width="formLabelWidth">
          <el-input v-model="temp_item.couponAvailable" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="danger"
                   @click="editFormVisible = false"
        >取 消
        </el-button>
        <el-button type="primary"
                   @click="editFormVisible = false;
                   handleEditConfirm(temp_item)"
        >确 定
        </el-button>
      </div>
    </el-dialog>
    <!--新增-->
    <el-dialog title="新增" width="50%" :visible.sync="addFormVisible" style="border-radius: 4px">
      <el-form :model="temp_item">
        <el-form-item label="图片" :label-width="formLabelWidth">
          <el-upload
            action="#"
            list-type="picture-card"
            :auto-upload="false"
            :on-change="handleChange"
            :limit="1"
          >
            <i slot="default" class="el-icon-plus"></i>
          </el-upload>
        </el-form-item>
        <el-form-item label="描述" :label-width="formLabelWidth">
          <el-input v-model="temp_item.couponDesc" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="价值" :label-width="formLabelWidth">
          <el-input v-model="temp_item.couponValue" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="可用" :label-width="formLabelWidth">
          <el-input v-model="temp_item.couponAvailable" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="danger"
                   @click="addFormVisible = false"
        >取 消
        </el-button>
        <el-button type="primary"
                   @click="addFormVisible = false;
                   handleAddConfirm(temp_item)"
        >确 定
        </el-button>
      </div>
    </el-dialog>
  </div>

</template>

<script>
import axios from 'axios'

export default {
  name: 'CouponView',
  computed: {},
  data () {
    return {
      uid: 13,
      baseUrl: 'http://43.139.97.111:8080',
      editFormVisible: false,
      addFormVisible: false,
      formLabelWidth: '80px',
      dialogImageUrl: '',
      disabled: false,
      dialogVisible: false,
      temp_item: {
        couponId: 0,
        couponImages: '',
        couponDesc: '',
        couponValue: 0,
        couponAvailable: '',
        createTime: '',
        updateTime: ''
      },
      coupons: [
        {
          couponId: '',
          couponImages: '',
          couponDesc: '',
          couponValue: '',
          couponAvailable: '',
          createTime: '',
          updateTime: ''
        }
      ],
      temp_data: ''
    }
  },
  methods: {
    queryAll () {
      const url = this.baseUrl + '/coupon/queryAll'
      // 发送请求:将数据返回到一个回到函数中
      const that = this
      // 并且响应成功以后会执行then方法中的回调函数
      axios.get(url).then(function (result) {
        that.coupons = result.data.data
      })
    },
    getSrc (iconPath) {
      return this.baseUrl + iconPath
    },
    handleAdd () {
      this.addFormVisible = true
      this.temp_item = {
        couponId: 0,
        couponImages: '',
        couponDesc: '12345538587',
        couponValue: 9.99,
        couponAvailable: '1',
        createTime: '',
        updateTime: ''
      }
    },
    handleEdit (item) {
      console.log(item)
      this.temp_item = item
      // this.$forceUpdate()
    },
    handleDelete (item) {
      const that = this
      axios.delete(this.baseUrl + '/coupon/deleteById', {
        params: { id: item.couponId }
      }).then(function (response) {
        console.log(JSON.stringify(response.data))
        that.queryAll()
      })
        .catch(function (error) {
          console.log(error)
        })
    },
    handleEditConfirm (item) {
      const that = this
      item.couponImages = this.temp_data.data[0]
      const data = JSON.stringify(item)
      console.log(data)
      const config = {
        method: 'put',
        url: this.baseUrl + '/coupon/update',
        headers: {
          'Content-Type': 'application/json'
        },
        data: data
      }

      axios(config)
        .then(function (response) {
          console.log(JSON.stringify(response.data))
          that.queryAll()
        })
        .catch(function (error) {
          console.log(error)
        })
    },
    handleAddConfirm (item) {
      const that = this
      item.couponImages = this.temp_data.data[0]
      const data = JSON.stringify(item)
      console.log(data)
      const config = {
        method: 'post',
        url: this.baseUrl + '/coupon/insert',
        headers: {
          'Content-Type': 'application/json'
        },
        data: data
      }
      axios(config)
        .then(function (response) {
          console.log(JSON.stringify(response.data))
          that.queryAll()
        })
        .catch(function (error) {
          console.log(error)
        })
    },
    handleChange (file) {
      const fd = new FormData()
      fd.append('files', file.raw)// 传文件
      fd.append('uid', this.uid)// id
      axios
        .post(this.baseUrl + '/files/upload', fd, { headers: { 'content-type': 'multipart/form-data' } })
        .then(res => {
          console.log(res.data.data[0])
          this.temp_data = res.data
        })
        .catch(err => {
          console.log(err)
        })
    }
  },
  created () {
    this.queryAll()
  }
  // ,
  // updated () {
  //   this.queryAll()
  // }
}
</script>
<style scoped lang="less">
.data-map {
  background-color: #1ccdae;
  padding: 10px;
  border-radius: 8px;
}

.add-btn {
  padding: 6px 15px;
  width: 80px;
  margin: 0 0 10px 8px;
  tab-size: 20px;
}

.add-btn:hover {
  color: white;
  font-weight: 500;
  background-color: #e82c5f;
}
</style>
