<template>
  <div>
    <el-form ref="form" label-width="80px" :model="spu">
      <el-form-item label="SPU名称">
        <el-input v-model="spu.spuName" placeholder="SPU名称" />
      </el-form-item>
      <el-form-item label="品牌">
        <el-select v-model="spu.tmId" placeholder="请选择品牌">
          <el-option
            v-for="(tm, index) in tradeMarkList"
            :key="index"
            :label="tm.tmName"
            :value="tm.id"
          />
        </el-select>
      </el-form-item>
      <el-form-item label="SPU描述">
        <el-input
          v-model="spu.description"
          type="textarea"
          rows="4"
          placeholder="描述"
        />
      </el-form-item>
      <el-form-item label="SPU图片">
        <el-upload
          action="/dev-api/admin/product/fileUpload"
          list-type="picture-card"
          :on-preview="handlePictureCardPreview"
          :on-remove="handleRemove"
          :on-success="handlerSuccess"
          :file-list="spuImageList"
        >
          <i class="el-icon-plus" />
        </el-upload>
        <el-dialog :visible.sync="dialogVisible">
          <img width="100%" :src="dialogImageUrl" alt="">
        </el-dialog>
      </el-form-item>
      <el-form-item label="销售属性">
        <el-select
          v-model="attrIdAndAttrName"
          :placeholder="`还有${unSelectSaleAttr.length}未选择`"
        >
          <el-option
            v-for="(unselect) in unSelectSaleAttr"
            :key="unselect.id"
            :label="unselect.name"
            :value="`${unselect.id}:${unselect.name}`"
          />
        </el-select>
        <el-button
          type="primary"
          icon="el-icon-plus"
          :disabled="!attrIdAndAttrName"
          @click="addSaleAttr"
        >添加销售属性</el-button>
        <el-table style="width: 100%" border :data="spu.spuSaleAttrList">
          <el-table-column
            type="index"
            label="序号"
            width="80px"
            align="center"
          />
          <el-table-column prop="saleAttrName" label="属性名" width="width" />
          <el-table-column label="属性值名称列表">
            <template slot-scope="{row}">
              <el-tag
                v-for="(tag, index) in row.spuSaleAttrValueList"
                :key="tag.id"
                closable
                :disable-transitions="false"
                @close="row.spuSaleAttrValueList.splice(index, 1)"
              >{{ tag.saleAttrValueName }}</el-tag>
              <el-input
                v-if="row.inputVisible"
                ref="saveTagInput"
                v-model="row.inputValue"
                class="input-new-tag"
                size="small"
                @blur="handleInputConfirm(row)"
              />
              <el-button
                v-else
                class="button-new-tag"
                size="small"
                @click="addSaleAttrValue(row)"
              >添加</el-button>
            </template>
          </el-table-column>
          <el-table-column prop="prop" label="操作" width="width">
            <template slot-scope="{ $index }">
              <el-button
                type="danger"
                icon="el-icon-delete"
                size="mini"
                @click="spu.spuSaleAttrList.splice($index, 1)"
              />
            </template>
          </el-table-column>
        </el-table>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="addOrUpdateSpu">保存</el-button>
        <el-button @click="cancel">取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      spu: {
        spuName: '',
        tmId: '',
        description: '',
        spuSaleAttrList: [],
        spuImageList: [],
        category3Id: ''
      },
      tradeMarkList: [],
      spuImageList: [],
      // unSelectSaleAttr: [],
      saleAttrList: [],
      dialogVisible: false,
      dialogImageUrl: '',
      attrIdAndAttrName: ''
    }
  },
  computed: {
    unSelectSaleAttr() {
      // console.log(this.saleAttrList)
      // console.log(this.spu.spuSaleAttrList)
      // return []
      const result = this.saleAttrList.filter((item) => {
        // every数组的方法，会返回一个布尔值【真，假的】
        // console.log(item)
        return this.spu.spuSaleAttrList.every((item1) => {
          // console.log(item1)
          return item.name !== item1.saleAttrName
        })
      })
      return result
    }
  },
  methods: {
    handlePictureCardPreview(file) {
    //   console.log(file)
      this.dialogImageUrl = file.url
      this.dialogVisible = true
    },
    handleRemove(file, fileList) {
      // file参数:代表的是删除的那个张图片
      // fileList:照片墙删除某一张图片以后，剩余的其他的图片
      console.log(file, fileList, 22222)
      // 收集照片墙图片的数据
      // 对于已有的图片【照片钱中显示的图片：有name、url字段的】，因为照片墙显示数据务必要有这两个属性
      // 对于服务器而言，不需要name、url字段，将来对于有的图片的数据在提交给服务器的时候，需要处理的
      this.spuImageList = fileList
    },
    addSpuData(id) {
      console.log(id)
      this.spu.category3Id = id
      this.$API.spu.reqTradeMarkList().then(res => {
        this.tradeMarkList = res.data
      })
      this.$API.spu.reqBaseSaleAttrList().then(res => {
        this.saleAttrList = res.data
      })
    },
    initSpuData(spu) {
      this.$API.spu.reqSpu(spu.id).then(res => {
        // console.log(res)
        this.spu = res.data
      })
      this.$API.spu.reqTradeMarkList().then(res => {
        this.tradeMarkList = res.data
      })
      this.$API.spu.reqSpuImageList(spu.id).then(res => {
        const listArr = res.data
        // console.log(listArr)
        listArr.forEach((item) => {
          item.name = item.imgName
          item.url = item.imgUrl
        })
        this.spuImageList = listArr
      })
      this.$API.spu.reqBaseSaleAttrList().then(res => {
        this.saleAttrList = res.data
      })
    },
    handlerSuccess(response, file, fileList) {
      // 收集图片的信息
      this.spuImageList = fileList
    },
    addSaleAttr() {
      // 添加销售属性
      // const [baseSaleAttrId, saleAttrName] = this.attrIdAndAttrName.split(":");
      const [baseSaleAttrId, saleAttrName] = this.attrIdAndAttrName.split(':')
      // console.log(this.attrIdAndAttrName)
      const newSaleAttr = {
        baseSaleAttrId,
        saleAttrName,
        spuSaleAttrValueList: []
      }
      this.spu.spuSaleAttrList.push(newSaleAttr)
      this.attrIdAndAttrName = ''
    },
    handleInputConfirm(row) {
      console.log(row)
      const { baseSaleAttrId, inputValue } = row
      if (inputValue.trim() === '') {
        this.$message('属性值不能为空')
        return
      }
      const result = row.spuSaleAttrValueList.every(item =>
      // return item
      // if (item.saleAttrValueName !== inputValue) {
      //   return item
      // }

        item.saleAttrValueName !== inputValue

      )
      console.log(result)
      if (!result) {
        return
      }
      const newSaleAttrValue = { baseSaleAttrId, saleAttrValueName: inputValue }
      // 新增
      row.spuSaleAttrValueList.push(newSaleAttrValue)
      // 修改inputVisible为false，不就显示button
      row.inputVisible = false
    },
    addSaleAttrValue(row) {
      // console.log(row)
      this.$set(row, 'inputVisible', true)
      this.$set(row, 'inputValue', '')
    },
    addOrUpdateSpu() {
      this.spu.spuImageList = this.spuImageList.map((item) => {
        return {
          imageName: item.name,
          imageUrl: (item.response && item.response.data) || item.url
        }
      })
      this.$API.spu.reqAddOrUpdateSpu(this.spu).then(res => {
        this.$message({ type: 'success', message: '保存成功' })
        this.$emit('changeScene', {
          scene: 0,
          flag: this.spu.id ? '修改' : '添加'
        })
      })
      Object.assign(this._data, this.$options.data())
    },
    cancel() {
      this.$emit('changeScene', { scene: 0, flag: '' })
      // 清理数据
      // Object.assign:es6中新增的方法可以合并对象
      // 组件实例this._data,可以操作data当中响应式数据
      // this.$options可以获取配置对象，配置对象的data函数执行，返回的响应式数据为空的
      Object.assign(this._data, this.$options.data())
    }
  }

}
</script>

<style>
.el-tag + .el-tag {
  margin-left: 10px;
}
.button-new-tag {
  margin-left: 10px;
  height: 32px;
  line-height: 30px;
  padding-top: 0;
  padding-bottom: 0;
}
.input-new-tag {
  width: 90px;
  margin-left: 10px;
  vertical-align: bottom;
}
</style>
