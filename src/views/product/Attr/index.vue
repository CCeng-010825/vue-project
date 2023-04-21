<template>
  <div class="page-container">
    <el-card style="margin: 20px 0px">
      <el-form ref="category" :model="cForm" :inline="true" size="small">
        <el-form-item label="一级分类">
          <el-select
            v-model="cForm.category1Id"
            placeholder="请选择"
            :disabled="show"
            @change="handler1"
          >
            <el-option v-for="(item,index) in category1Idata" :key="index" :value="item.id" :label="item.name" />
          </el-select>
        </el-form-item>
        <el-form-item label="二级分类">
          <el-select
            v-model="cForm.category2Id"
            placeholder="请选择"
            :disabled="show"
            @change="handler2"
          >
            <el-option v-for="(item,index) in category2Idata" :key="index" :value="item.id" :label="item.name" />
          </el-select>
        </el-form-item>
        <el-form-item label="三级分类">
          <el-select
            v-model="cForm.category3Id"
            placeholder="请选择"
            :disabled="show"
            @change="handler3"
          >
            <el-option v-for="(item,index) in category3Idata" :key="index" :value="item.id" :label="item.name" />
          </el-select>
        </el-form-item>
      </el-form>
    </el-card>
    <el-card>
      <div v-show="isShowTable">
        <el-button
          type="primary"
          icon="el-icon-plus"
          :disabled="!cForm.category3Id"
          @click="add"
        >添加属性
        </el-button>
        <el-table :data="tableData" style="width: 100%" border>
          <el-table-column type="index" label="序号" width="80" align="center" />
          <el-table-column prop="attrName" label="属性名称" width="150" />
          <el-table-column prop="prop" label="属性值列表" width="width">
            <template slot-scope="{row}">
              <!-- {{ row }} -->
              <el-tag v-for="item in row.attrValueList" :key="item.id" type="success" style="margin: 5px 20px">
                {{ item.valueName }}
              </el-tag>
            </template>
          </el-table-column>
          <el-table-column label="操作" width="150">
            <template slot-scope="{row}">
              <el-button
                type="warning"
                icon="el-icon-edit"
                size="mini"
                @click="updateAttr(row)"
              />
              <el-button
                type="danger"
                icon="el-icon-delete"
                size="mini"
              />
            </template>
          </el-table-column>
        </el-table>
      </div>
      <div v-show="!isShowTable">
        <div class="header">
          <el-form ref="form" :model="attrInfo" :inline="true" label-width="80px" size="small">
            <el-form-item label="属性名">
              <el-input v-model="attrInfo.attrName" />
            </el-form-item>
          </el-form>
        </div>
        <div class="button">
          <el-button
            type="primary"
            icon="el-icon-plus"
            :disabled="!attrInfo.attrName"
            @click="addAttrValue"
          >添加属性值</el-button>
          <el-button @click="isShowTable = true">取消</el-button>
        </div>
        <el-table :data="attrInfo.attrValueList" border style="width: 100%; margin: 20px 0px">
          <el-table-column align="center" type="index" label="序号" width="80" />
          <el-table-column width="width" prop="prop" label="属性值名称">
            <template slot-scope="{row,$index}">
              <el-input
                v-if="row.flag"
                :ref="$index"
                v-model="row.valueName"
                placeholder="请输入属性值名称"
                size="mini"
                @blur="look(row)"
              />
              <span
                v-else
                style="display: block"
                @click="toEdit(row, $index)"
              >{{ row.valueName }}</span>
            </template>
          </el-table-column>
          <el-table-column width="width" prop="prop" label="操作">
            <template slot-scope="{ row, $index }">
              <!-- 气泡确认框 -->
              <el-popconfirm :title="`确定删除${row.valueName}?`" @onConfirm="deleteAttrValue($index)">
                <el-button
                  slot="reference"
                  type="danger"
                  icon="el-icon-delete"
                  size="mini"
                />
              </el-popconfirm>
            </template>
          </el-table-column>
        </el-table>
        <el-button type="primary" :disabled="attrInfo.attrValueList.length<1" @click="addOrUpdateAttr">保存</el-button>
        <el-button @click="isShowTable = true">取消</el-button>
      </div>
    </el-card>
  </div>
</template>

<script>
import cloneDeep from 'lodash/cloneDeep'

export default {
  data() {
    return {
      cateryData: [],
      cForm: {
        category1Id: '',
        category2Id: '',
        category3Id: ''
      },
      attrInfo: {
        attrName: '',
        attrValueList: [
          // 属性值，因为属性值可以有多个因此用数组，每一个属性值都是一个对象需要attrId，valueName
        ],
        categoryId: 0, // 三级分类的id
        categoryLevel: 3 // 因为服务器也需要区分几级id

      },
      tableData: [],
      show: false,
      isShowTable: true,
      category1Idata: [],
      category2Idata: [],
      category3Idata: []
    }
  },
  mounted() {
    this.get1List()
  },
  methods: {
    get1List() {
      // 获取一级分类的请求：不需要携带参数
      this.$API.attr.reqCategory1List().then(res => {
        this.category1Idata = res.data
      })
    },
    handler1(val) {
    //   console.log(val)
      this.cForm.category2Id = ''
      this.cForm.category3Id = ''
      this.category2Idata = []
      this.category3Idata = []
      this.$API.attr.reqCategory2List(val).then(res => {
        this.category2Idata = res.data
      })
    },
    handler2(val) {
      this.cForm.category3Id = ''
      this.category3Idata = []
      this.$API.attr.reqCategory3List(val).then(res => {
        this.category3Idata = res.data
      })
    },
    handler3() {
      const { category1Id, category2Id, category3Id } = this.cForm
      // 获取属性列表的数据
      this.$API.attr.reqAttrList(
        category1Id,
        category2Id,
        category3Id
      ).then(res => {
        // this.attrList = result.data;
        // console.log(res)
        this.tableData = res.data
      })
    },
    add() {
      this.isShowTable = false
      this.show = true
      // 清除数据
      // 收集三级分类的id
      this.attrInfo = {
        attrName: '', // 属性名
        attrValueList: [
          // 属性值，因为属性值可以有多个因此用数组，每一个属性值都是一个对象需要attrId，valueName
        ],
        categoryId: this.cForm.category3Id, // 三级分类的id
        categoryLevel: 3 // 因为服务器也需要区分几级id
      }
    },
    updateAttr(row) {
      this.isShowTable = false
      this.show = true
      // row.flag = false
      // console.log(row)
      this.attrInfo = cloneDeep(row)
      this.attrInfo.attrValueList.map(item =>
        this.$set(item, 'flag', false)
      )
    },
    addAttrValue() {
      this.attrInfo.attrValueList.push({
        attrId: this.attrInfo.id,
        valueName: '',
        flag: true
      })
      this.$nextTick(() => {
        // console.log(this.$refs)
        // console.log(this.$refs[this.attrInfo.attrValueList.length - 1])
        this.$refs[this.attrInfo.attrValueList.length - 1].focus()
      })
    },
    look(row) {
      // console.log(row)
      if (row.valueName.trim() === '') {
        this.$message('请你输入一个正常的属性值')
        return
      }
      const repeat = this.attrInfo.attrValueList.some(item => {
        // if (item.valueName === row.valueName) {
        //   return true
        // }
        if (row !== item) {
          // console.log('pp')
          return row.valueName === item.valueName
        }
      })
      console.log(repeat)
      if (repeat) return
      row.flag = false
    },
    toEdit(row, index) {
      // console.log(row, index)
      row.flag = true
      // this.$refs[index]
      this.$nextTick(() => {
        // console.log(this.$refs[index])
        this.$refs[index].focus()
      })
    },
    deleteAttrValue(index) {
      // 当前删除属性值的操作是不需要发请求的
      this.attrInfo.attrValueList.splice(index, 1)
    },
    addOrUpdateAttr() {
      // 整理参数:1,如果用户添加很多属性值，且属性值为空的不应该提交给服务器
      // 提交给服务器数据当中不应该出现flag字段
      this.attrInfo.attrValueList = this.attrInfo.attrValueList.filter(item => {
        // 过滤掉属性值不是空的
        if (item.valueName !== '') {
          // 删除掉flag属性
          delete item.flag
          return true
        }
      })
      try {
        // 发请求
        console.log(this.attrInfo)
        this.$API.attr.reqAddOrUpdateAttr(this.attrInfo).then(() => {
          this.isShowTable = true
          // 提示消失
          this.$message({ type: 'success', message: '保存成功' })
          // 保存成功以后需要再次获取平台属性进行展示
          this.handler3()
        })
        // 展示平台属性的信号量进行切换
      } catch (error) {
        // this.$message('保存失败')
      }
    }
  }

}
</script>

<style>

</style>
