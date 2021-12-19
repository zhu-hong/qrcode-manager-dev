<script setup>
import { computed, reactive, ref } from '@vue/reactivity'
import Qr from 'qrcode.vue'
import { nanoid } from 'nanoid'
import zip from 'jszip'
import { nextTick } from '@vue/runtime-core'
import htc from 'html2canvas'

const currentQr = ref(null)
const dialogShow = ref(false)
const printer = ref(null)
const checkedItem = ref('')
const selectedQr = reactive([])
const curPage = ref(1)
const pageSize = 5

const qrcodeList = [
  {
    id: nanoid(),
    text: '1',
  },
  {
    id: nanoid(),
    text: '2',
  },
  {
    id: nanoid(),
    text: '3',
  },
  {
    id: nanoid(),
    text: '4',
  },
  {
    id: nanoid(),
    text: '5',
  },
  {
    id: nanoid(),
    text: '6',
  },
  {
    id: nanoid(),
    text: '7',
  },
  {
    id: nanoid(),
    text: '8',
  },
  {
    id: nanoid(),
    text: '9',
  },
  {
    id: nanoid(),
    text: '10',
  },
  {
    id: nanoid(),
    text: '11',
  },
  {
    id: nanoid(),
    text: '12',
  },
  {
    id: nanoid(),
    text: '13',
  },
  {
    id: nanoid(),
    text: '14',
  },
  {
    id: nanoid(),
    text: '15',
  },
  {
    id: nanoid(),
    text: '16',
  },
  {
    id: nanoid(),
    text: '17',
  },
  {
    id: nanoid(),
    text: '18',
  },
  {
    id: nanoid(),
    text: '19',
  },
  {
    id: nanoid(),
    text: '20',
  },
  {
    id: nanoid(),
    text: '21',
  },
  {
    id: nanoid(),
    text: '22',
  },
  {
    id: nanoid(),
    text: '23',
  },
  {
    id: nanoid(),
    text: '24',
  },
  {
    id: nanoid(),
    text: '25',
  },
  {
    id: nanoid(),
    text: '26',
  },
]

const tableData = computed(() => {
  let [len, index, pages] = [qrcodeList.length, 0, []]
  pages.push(null)
  while (index < len) {
    pages.push(qrcodeList.slice(index, index += pageSize))
  }
  return pages[curPage.value]
})

const temList = reactive([
  {
    id: 'c1',
    value: 1,
  },
  {
    id: 'c2',
    value: 2,
  },
  {
    id: 'c3',
    value: 3,
  },
])

const handleSelect = (value) => {
  selectedQr.length = 0
  value.forEach(({ id, text }) => {
    selectedQr.push({ id, text })
  })
}

const showDialog = (value) => {
  currentQr.value = value
  dialogShow.value = true
}

const getQrsZip = () => {
  return new Promise((resolve) => {
    const qrsZip = new zip()

    selectedQr.forEach(async ({ id }, i, set) => {
      const canvas = await htc(document.getElementById(id))
      const file = canvas.toDataURL().split(',')[1]
      qrsZip.file(`${id}.png`, file, { base64: true })
      console.log(id, i, set)

      if (i === set.length - 1) {
        resolve(qrsZip)
      }
    })

  })
}

const downloadQrs = async () => {
  const file = await getQrsZip()
  console.log(file)
  const zipFlie = await file.generateAsync({ type: 'blob' })

  const filePath = URL.createObjectURL(zipFlie)

  const a = document.createElement('a')
  a.download = 'qrcodes'
  a.href = filePath
  a.dispatchEvent(new MouseEvent('click'))
  URL.revokeObjectURL(filePath)
}

const printQrs = () => {
  const doc = printer.value.contentDocument
  doc.open()
  doc.append(document.createElement('div'))

  selectedQr.forEach(({ id }) => {
    const qr = document.getElementById(id)

    doc.children[0].append(qr)
  })
  printer.value.contentWindow.print()
}

const curChange = (value) => {
  curPage.value = value
}
</script>

<template>
  <div style="position: relative;">
    <el-table :data="tableData" @selection-change="handleSelect">
      <el-table-column type="selection" :width="50" />
      <el-table-column label="id">
        <template #default="scope">{{ scope.row.id }}</template>
      </el-table-column>
      <el-table-column label="内容">
        <template #default="scope">{{ scope.row.text }}</template>
      </el-table-column>
      <el-table-column label="预览">
        <template #default="scope">
          <el-button @click="showDialog(scope.row.text)">预览</el-button>
        </template>
      </el-table-column>
    </el-table>
    <div
      style="position: absolute; top: 0; left: 50px; background-color: white; width: calc(100% - 50px);"
      v-show="selectedQr.length"
    >
      <el-button type="primary" @click="downloadQrs">下载</el-button>
      <el-button type="danger" @click="printQrs">打印</el-button>
    </div>
  </div>
  <el-pagination
    layout="prev, pager, next"
    :total="qrcodeList.length"
    :page-size="pageSize"
    @current-change="curChange"
  ></el-pagination>

  <!-- <template v-for="tem in temList" :key="tem.id">
    <input
      style="display: none;"
      type="radio"
      :id="tem.id"
      name="type"
      v-model="checkedItem"
      :value="tem.value"
    />
    <label :for="tem.id" :class="{ 'checked': tem.value === checkedItem }">
      <img src="./assets/logo.png" />
    </label>
  </template>-->

  <!-- 二维码容器用于下载 -->
  <!-- <div style="display: none;"> -->
  <div>
    <template v-for="qr in selectedQr" :key="qr.id">
      <div :id="qr.id">
        <Qr :value="qr.text" />
      </div>
    </template>
  </div>

  <!-- 用于打印 -->
  <iframe ref="printer" style="display: none;" title="printer"></iframe>

  <el-dialog v-model="dialogShow">
    <Qr :value="currentQr" style="width: 100%; height: 100%;" />
  </el-dialog>
</template>

<style>
body {
  margin: 0;
}

.checked {
  display: inline-block;
  border: 3px solid rgb(58, 142, 230);
  border-radius: 3px;
  overflow: hidden;
  position: relative;
}

.checked::after {
  content: "✔️";
  position: absolute;
  bottom: 0;
  right: 0;
}

.checked::before {
  content: "";
  position: absolute;
  bottom: 0;
  right: 0;
  width: 40px;
  height: 40px;
  display: inline-block;
  background-color: rgb(58, 142, 230);
  transform: rotate(45deg) translateX(25px);
}
</style>