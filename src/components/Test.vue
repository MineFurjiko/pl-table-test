<template>
  <div class="main">
    <div class="header">
      <el-pagination
        class="pagination"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :page-sizes="[100, 500, 1000, 5000]"
        :current-page.sync="page"
        :page-size.sync="pageSize"
        layout="sizes, prev, pager, next"
        :total="total"
      >
      </el-pagination>
      <div>已添加 {{ addListCount }} 项</div>
      <el-button type="text" size="mini" @click="addToList">添加当前页</el-button>
    </div>
    <pl-table
      class="plTable"
      size="small"
      stripe
      use-virtual
      :data="filteredList"
      :height="tableHeight"
      @selection-change="onSelectionChange"
    >
      <pl-table-column type="selection" width="55" fixed />
      <pl-table-column label="序号" type="index" :index="i => (page - 1) * pageSize + 1 + i" width="100" fixed />
      <pl-table-column label="流水号" prop="sn" />
      <pl-table-column label="姓名" prop="name" v-if="showName" />
      <pl-table-column label="内容" prop="text" show-overflow-tooltip />
      <pl-table-column label="类别" prop="type" :formatter="_type_formatter" />
      <pl-table-column fixed="right" label="操作" width="100" align="center">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="show(scope.row)" :disabled="snList.includes(scope.row.sn)">查看</el-button>
        </template>
      </pl-table-column>
    </pl-table>
  </div>
</template>

<script>
import Mock from 'mockjs'

export default {
  name: 'Test',
  computed: {
    tableHeight() {
      return window.innerHeight - (20 + 40)
    },
    addListCount() {
      return this.addList ? this.addList.length : 0
    },
    snList() {
      return this.addList.map(i => i.sn)
    },
    filteredList() {
      return this.list.filter(i => i.type != 0)
    }
  },
  data() {
    return {
      total: 5000,
      page: 1,
      pageSize: 100,
      list: [],
      showName: true,

      addList: []
    }
  },
  methods: {
    mockData(page = 1, pageSize = 100) {
      /** @type Array */
      let md = this.__md
      if (!md) {
        this.__md = md = Mock.mock({
          [`list|${this.total}`]: [
            {
              sn: '@id',
              name: '@cname',
              text: '@cparagraph',
              type: '@integer(0, 2)'
            }
          ]
        }).list
      }

      const start = Math.max(page - 1, 0) * pageSize
      const end = page * pageSize
      return md.slice(start, end)
    },
    fetchData() {
      this.list = this.mockData(this.page, this.pageSize)
    },
    handleSizeChange() {
      this.fetchData()
    },
    handleCurrentChange() {
      this.fetchData()
    },
    show(i) {
      console.log(i)
    },
    addToList() {
      for (const i of this.list) {
        if (!this.addList.includes(i)) {
          this.addList.push(i)
        }
      }
    },
    onSelectionChange(arr) {
      console.log(arr.length)
    },
    _type_formatter(a, b, c, d) {
      return { 0: '类型A', 1: '类型B', 2: '类型C' }[c]
    }
  },
  created() {
    this.fetchData()
  }
}
</script>

<style lang="scss" scoped>
.main {
  padding: 0 20px 20px;
  .header {
    margin-top: 4px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    .pagination {
      height: 32px;
    }
  }
}
</style>
