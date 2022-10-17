<template>
  <div class="sku">
    <el-form :model="formData" label-width="120px">
      <el-form-item :label="item.title" v-for="(item,index) in specData" :key="index">
        <div class="fairy-spec-table">
          <el-checkbox-group v-model="formData.checkboxGroup1" size="default">
            <el-checkbox :label="it.title" border v-for="(it,idx) in item.child" :key="idx" :checked="it.checked" @change="handleChange(index,idx)"/>
          </el-checkbox-group>
        </div>
      </el-form-item>

      <!-- 动态sku表 -->
      <el-form-item label="SKU表：">
        <div class="fairy-spec-table">
          <el-table
              :data="tableData.data"
              :span-method="objectSpanMethod"
              border
              style="width: 100%"
          >
            <el-table-column prop="id" :label="item" width="80" align="center" v-for="(item,index) in tableData.title" :key="index">
              <template #default="scope">
                {{ scope["row"][index] }}
              </template>
            </el-table-column>
            <el-table-column prop="price" label="销售价格">
              <template #default="scope">
                <el-input v-model="scope.row.price" placeholder="价格"/>
              </template>
            </el-table-column>
            <el-table-column label="市场价格">
              <template #default="scope">
                <el-input v-model="scope.row.cost_price" placeholder="市场价"/>
              </template>
            </el-table-column>
            <el-table-column label="成本价格">
              <template #default="scope">
                <el-input v-model="scope.row.market_price" placeholder="成本价"/>
              </template>
            </el-table-column>
            <el-table-column label="库存">
              <template #default="scope">
                <el-input v-model="scope.row.stock" placeholder="库存"/>
              </template>
            </el-table-column>
          </el-table>
        </div>
      </el-form-item>

      <p>{{ formData }}</p>
      -------
      <p>{{ specData }}</p>
      ----
      <p>{{ tableData }}</p>
    </el-form>
  </div>
</template>
<script setup>
import {onMounted, reactive} from "vue"

const formData = reactive({
  checkboxGroup1: [],
})
/**
 * 选择框
 */
let specData = reactive([
  {
    id: "1",
    title: "颜色",
    child: [
      {id: "1", title: "红", checked: true},
      {id: "2", title: "黄", checked: false},
      {id: "3", title: "蓝", checked: false},
    ],
  }, {
    id: "2",
    title: "尺码",
    child: [
      {id: "4", title: "S", checked: true},
      {id: "5", title: "M", checked: true},
      {id: "6", title: "L", checked: false},
      {id: "7", title: "XL", checked: false},
    ],
  }, {
    id: "3",
    title: "款式",
    child: [
      {id: "8", title: "男款", checked: true},
      {id: "9", title: "女款", checked: true},
    ],
  }, {
    id: "4",
    title: "型号",
    child: [
      {id: "10", title: "s", checked: true},
      {id: "11", title: "m", checked: false},
    ],
  },
])
const tableData = reactive({
  title: [],
  data: [],
})
const handleChange = (index, idx) => {
  //修改状态
  specData[index]["child"][idx]["checked"] = !specData[index]["child"][idx]["checked"]
  //获取显示表
  asyncCheckbox()
}

// const textArr = [["a", "b", "c"], ["d", "e", "f", "g"], ["h", "i", "z"]]
const getCombination = (array) => {
  let resultArray = []
  array.forEach((arrItem) => {
    if (resultArray.length === 0) {
      resultArray = arrItem
    } else {
      const emptyArray = []
      resultArray.forEach((item) => {
        arrItem.forEach((value) => {
          emptyArray.push([...item, value])
        })
      })
      resultArray = emptyArray
    }
  })
  return resultArray
}
const asyncCheckbox = () => {
  let list_data = []
  let data_list_title = []
  specData.forEach((item) => {
    if (item.child.length !== 0) {
      let it_data = []
      item.child.forEach((it) => {
        if (it.checked) {
          it_data.push(it.title)
        }
      })
      if (it_data.length !== 0) {
        list_data.push(it_data)
        data_list_title.push(item.title)
      }
    }
  })
  //循环表格
  let data_list = []
  let table_list = getCombination(list_data)
  console.log(table_list)
  table_list.forEach((item) => {
    let table_i = []
    for (let i = 0; i < item.length; i++) {
      table_i.push(item[i])
    }
    data_list.push({
      ...table_i,
      price: "",
      stock: "",
      cost_price: "",
      image: "",
      market_price: "",
    })
  })

  tableData.title = data_list_title
  tableData.data = data_list
}
onMounted(() => {
  asyncCheckbox()
})
const objectSpanMethod = ({rowIndex, columnIndex}) => {
  if (columnIndex === 0) {
    if (rowIndex % 2 === 0) {
      return {
        rowspan: 2,
        colspan: 1,
      }
    } else {
      return {
        rowspan: 0,
        colspan: 0,
      }
    }
  }
}
</script>
