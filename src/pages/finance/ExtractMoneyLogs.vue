<template lang="pug">
  .page
    .page-searchbar
      el-row(:gutter='20')
        el-col(:span='6')
          el-input(placeholder='请输入关键词' v-model='searchForm.name' clearable)
        el-col(:span='6')
          el-date-picker(start-placeholder='开始时间' end-placeholder='结束时间' clearable type='daterange' style='width:100%' v-model='date' value-format='yyyy-MM-dd')
        el-col(:span='6')
          el-button(icon='el-icon-search' type='primary' @click='search') 搜索
    .page-content
      .page-table
        el-table(border stripe :data='records')
          el-table-column(type='index')
          el-table-column(label='日期' prop='')
          el-table-column(label='付款ID' prop='businessId')
          el-table-column(label='名称' prop='name')
          el-table-column(label='提现金额' prop='actualAmount')
            template(slot-scope='{row}') {{row.actualAmount/100}}
          el-table-column(label='提现通道' prop='type')
          el-table-column(label='账户信息' prop='withdrawAccount')
      .page-pagination       
        el-pagination(background :total='total' :page-size='size' :current-page='current' @current-change='changePage' layout='prev, pager, next, total, jumper')
</template>

<script>
import mixin from '../../mixin'

export default {
  data() {
    return {
      records: [],
      total: 0,
      size: 10,
      current: 1,

      date: '',
      searchForm: {
        name: '',
        startTime: '',
        endTime: ''  
      }
    }
  },
  mixins: [mixin],
  methods: {
    initSearchParams() {
      const { current, startTime, endTime } = this.$route.query

      this.searchForm.current = current || 1
      this.searchForm.startTime = startTime
      this.searchForm.endTime = endTime

      if(startTime&&endTime) {
        this.date = [startTime, endTime]
      } else {
        this.date = ''
      }
    },
    search() {
      this.changeSearch({
        name: this.searchForm.name,
        startTime: this.date ? this.date[0] : undefined,
        endTime: this.date ? this.date[1] : undefined,
      })
      // this.getList()
    },
    getTableList() {
      const params = this.getSearchParams()

      params.size = this.size
      API.finance.couponsWithdraw(params).then(res => {
        const data = res.data.data
        if (data) {
          this.current = +data.current
          this.records = data.records
          this.total = +data.total
        }
      })
    }
  }
}
</script>
couponsWithdraw
