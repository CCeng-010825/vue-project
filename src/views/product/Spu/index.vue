<template>
  <div>
    <el-card style="margin: 20px 0px">
      <!-- 三级联动已经是全局组件了 -->
      <CategorySelect
        :show="scene != 0"
        @getCategoryId="getCategoryId"
      />
    </el-card>
    <el-card>
      <div v-show="scene===0">
        <el-button
          type="primary"
          icon="el-icon-plus"
          :disabled="!category3Id"
          @click="addSpu"
        >添加SPU</el-button>
        <el-table style="width: 100%" border :data="records">
          <el-table-column type="index" label="序号" width="80" align="center" />
          <el-table-column prop="spuName" label="SPU名称" width="width" />
          <el-table-column prop="description" label="SPU描述" width="width" />
          <el-table-column prop="prop" label="操作" width="width">
            <template slot-scope="{ row }">
              <!-- 这里按钮将来用hintButton替换 -->
              <hint-button
                type="success"
                icon="el-icon-plus"
                size="mini"
                title="添加sku"
                @click="addSku(row)"
              />
              <hint-button
                type="warning"
                icon="el-icon-edit"
                size="mini"
                title="修改spu"
                @click="updateSpu(row)"
              />
              <hint-button
                type="info"
                icon="el-icon-info"
                size="mini"
                title="查看当前spu全部sku列表"
                @click="handler(row)"
              />
              <el-popconfirm
                title="这是一段内容确定删除吗？"
                @onConfirm="deleteSpu(row)"
              >
                <hint-button
                  slot="reference"
                  type="danger"
                  icon="el-icon-delete"
                  size="mini"
                  title="删除spu"
                />
              </el-popconfirm>
            </template>
          </el-table-column>
        </el-table>
        <el-pagination
          style="text-align: center"
          :current-page="page"
          :page-sizes="[3, 5, 10]"
          :page-size="limit"
          layout="prev, pager, next, jumper,->, sizes,total"
          :total="total"
          @current-change="getSpuList"
          @size-change="handleSizeChange"
        />
      </div>
      <SpuForm
        v-show="scene == 1"
        ref="spu"
        @changeScene="changeScene"
      />
      <SkuForm
        v-show="scene == 2"
        ref="sku"
        @changeScenes="changeScenes"
      />
    </el-card>
    <el-dialog
      :title="`${spu.spuName}的sku列表`"
      :visible.sync="dialogTableVisible"
      :before-close="close"
    >
      <!-- table展示sku列表-->
      <el-table v-loading="loading" :data="skuList" style="width: 100%" border>
        <el-table-column prop="skuName" label="名称" width="width" />
        <el-table-column prop="price" label="价格" width="width" />
        <el-table-column prop="weight" label="重量" width="width" />
        <el-table-column label="默认图片" width="width">
          <template slot-scope="{row}">
            <img :src="row.skuDefaultImg" alt="" style="width:100px;height:100px;">
          </template>
        </el-table-column>
      </el-table>
    </el-dialog>
  </div>
</template>

<script>
import SpuForm from './SpuForm'
import SkuForm from './SkuForm'
export default {
  components: { SpuForm, SkuForm },
  data() {
    return {
      show: false,
      scene: 0,
      records: [],
      category1Id: '',
      category2Id: '',
      category3Id: '',
      page: 1,
      total: 0,
      limit: 10,
      dialogTableVisible: false,
      spu: {},
      skuList: [], // 存储的是SKU列表的数据
      loading: true
    }
  },
  methods: {
    getCategoryId({ categoryId, level }) {
      // console.log(categoryId)
      // console.log(level)
      if (level === 1) {
        this.category1Id = categoryId
        this.category2Id = ''
        this.category3Id = ''
        // console.log(this.category2Id)
      } else if (level === 2) {
        this.category2Id = categoryId
        this.category3Id = ''
      } else {
        this.category3Id = categoryId
        this.getSpuList()
      }
    },
    addSpu() {
      this.scene = 1
      this.$refs.spu.addSpuData(this.category3Id)
    },
    close(done) {
      console.log(done)
    },
    changeScenes() {},
    changeScene({ scene, flag }) {
      // console.log(scene)
      // console.log(flag)
      this.scene = scene
      if (flag === '修改') {
        this.getSpuList(this.page)
      } else {
        this.getSpuList()
      }
    },
    addSku(row) {},
    updateSpu(row) {
      // console.log(row)
      this.scene = 1
      this.$refs.spu.initSpuData(row)
    },
    handler(row) {},
    deleteSpu(row) {
      this.$API.spu.reqDeleteSpu(row.id).then(res => {
        this.$message({ type: 'success', message: '删除成功' })
        this.getSpuList(this.records.length > 1 ? this.page : this.page - 1)
      })
    },
    handleSizeChange(limit) {
      this.limit = limit
      this.getSpuList()
    },
    getSpuList(pages = 1) {
      // console.log('getSpuList')
      this.page = pages
      const { page, limit, category3Id } = this
      this.$API.spu.reqSpuList(page, limit, category3Id).then(res => {
        this.total = res.data.total
        this.records = res.data.records
      })
    }
  }

}
</script>

<style>

</style>
