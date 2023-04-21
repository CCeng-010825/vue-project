<template>
  <div>
    <el-table style="width: 100%" border :data="records">
      <el-table-column
        type="index"
        label="序号"
        width="80"
        align="center"
      />
      <el-table-column
        prop="skuName"
        label="名称"
        width="width"
      />
      <el-table-column
        prop="skuDesc"
        label="描述"
        width="width"
      />
      <el-table-column label="默认图片" width="110">
        <template slot-scope="{row}">
          <img
            :src="row.skuDefaultImg"
            alt=""
            style="width: 80px; height: 80px"
          >
        </template>
      </el-table-column>
      <el-table-column prop="weight" label="重量" width="80" />
      <el-table-column prop="price" label="价格" width="80" />
      <el-table-column prop="prop" label="操作" width="width">
        <template slot-scope="{row}">
          <el-button
            v-if="row.isSale == 0"
            type="success"
            icon="el-icon-sort-down"
            size="mini"
            @click="sale(row)"
          />
          <el-button
            v-else
            type="success"
            icon="el-icon-sort-up"
            size="mini"
            @click="cancel(row)"
          />
          <el-button
            type="primary"
            icon="el-icon-edit"
            size="mini"
            @click="edit"
          />
          <el-button
            type="info"
            icon="el-icon-info"
            size="mini"
            @click="getSkuInfo(row)"
          />
          <el-button
            type="danger"
            icon="el-icon-delete"
            size="mini"
          />
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      :current-page="page"
      :page-sizes="[3,10,20]"
      layout="prev, pager, next, jumper,->,sizes,total"
      :total="total"
      style="text-align: center"
      :page-size="limit"
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
    />
    <el-drawer :visible.sync="show" :show-close="false" size="50%">
      <el-row>
        <el-col :span="5">名称</el-col>
        <el-col :span="16">{{ skuInfo.skuName }}</el-col>
      </el-row>
      <el-row>
        <el-col :span="5">描述</el-col>
        <el-col :span="16">{{ skuInfo.skuDesc }}</el-col>
      </el-row>
      <el-row>
        <el-col :span="5">价格</el-col>
        <el-col :span="16">{{ skuInfo.price }}元</el-col>
      </el-row>
      <el-row>
        <el-col :span="5">平台属性</el-col>
        <el-col :span="16">
          <template>
            <el-tag v-for="(attr) in skuInfo.skuAttrValueList" :key="attr.id" type="success" style="margin-right:10px">{{ attr.attrId }}-{{ attr.valueId }}</el-tag>
          </template>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="5">商品图片</el-col>
        <el-col :span="16">
          <el-carousel height="300px">
            <el-carousel-item v-for="item in skuInfo.skuImageList" :key="item.id">
              <img :src="item.imgUrl">
            </el-carousel-item>
          </el-carousel>
        </el-col>
      </el-row>
    </el-drawer>
  </div>
</template>

<script>
export default {
  data() {
    return {
      records: [],
      total: 0,
      page: 1,
      limit: 10,
      show: false,
      skuInfo: {}
    }
  },
  mounted() {
    this.getTableData()
  },
  methods: {
    getTableData(page = 1) {
      this.page = page
      const { limit } = this
      this.$API.sku.reqSkuList(page, limit).then(res => {
        // console.log(res)
        this.records = res.data.records
        this.total = res.data.total
      })
    },
    sale(row) {
      this.$API.sku.reqSale(row.id).then(res => {
        row.isSale = 1
        this.$message({ type: 'success', message: '上架成功' })
      })
    },
    edit(row) {},
    cancel(row) {
      this.$API.sku.reqCancel(row.id).then(res => {
        row.isSale = 0
        this.$message({ type: 'success', message: '下架成功' })
      })
    },
    getSkuInfo(sku) {
      this.show = true
      this.$API.sku.reqSkuById(sku.id).then(res => {
        this.skuInfo = res.data
      })
    },
    handleSizeChange(limit) {
      this.limit = limit
      this.getTableData()
    },
    handleCurrentChange(val) {
      // console.log(val)
      // this.page = val
      this.getTableData(val)
    }
  }

}
</script>

<style>
  .el-carousel__item h3 {
    color: #475669;
    font-size: 14px;
    opacity: 0.75;
    line-height: 150px;
    margin: 0;
  }

  .el-carousel__item:nth-child(2n) {
     background-color: #99a9bf;
  }

  .el-carousel__item:nth-child(2n+1) {
     background-color: #d3dce6;
  }
</style>

<style scoped>
   .el-row .el-col-5{
      font-size:18px;
      text-align:right;
   }
   .el-col{
     margin:10px 10px;
   }

   >>>.el-carousel__button{
    width:10px;
    height:10px;
    background:red;
    border-radius:50%;
  }
</style>
