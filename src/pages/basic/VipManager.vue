<template lang="pug">
  .page
    .page-searchbar
      el-row(:gutter='15')
        el-col(:span='6')
          el-input(placeholder='请输入关键词' v-model='searchForm.params' clearable)
        el-col(:span='6')
          el-button(icon='el-icon-search' type='primary' @click='search') 搜索
    .page-content
      .page-table
        el-table(border stripe :data='records')
          el-table-column(type='index')
          el-table-column(label='ID' prop='id')
          el-table-column(label='微信openId' prop='openid')
          el-table-column(label='支付宝' prop='alipayId')
          el-table-column(label='手机号' prop='phone')
          el-table-column(label='昵称' prop='nickName')
          el-table-column(label='实名' prop='realName')
          el-table-column(label='性别' prop='sex')
          el-table-column(label='推荐人ID' prop='referrerId')
          el-table-column(label='操作' width='280')
            template(slot-scope='{row}')
              el-button(type='text' @click='getMemberFan(row.id)') 推荐关系
              el-button(type='text' @click='getPaymentLogs(row.id)') 支付记录
              el-button(type='text' @click='getScoreLogs(row.id)') 积分明细
              el-button(type='text' @click='getCouponsLogs(row.id)') 优惠金明细
      .page-pagination       
        el-pagination(background :total='total' :page-size='size' :current-page='current' @current-change='changePage' layout='prev, pager, next, total, jumper')
    .p-dialogs
      .p-dialog1
        el-dialog(:visible.sync='dialog.isDialogShow1' title='推荐关系')
          div(v-if='fansData')
            div(v-if='fansData.four')
              span(class='m-r30') 上4级
              span(class='m-r30') ID：{{fansData.four.id}}
              span(class='m-r30') 姓名：{{fansData.four.realName}}
            div(v-if='fansData.three')
              span(class='m-r30') 上3级
              span(class='m-r30') ID：{{fansData.three.id}}
              span(class='m-r30') 姓名：{{fansData.three.realName}}
            div(v-if='fansData.two')
              span(class='m-r30') 上2级
              span(class='m-r30') ID：{{fansData.two.id}}
              span(class='m-r30') 姓名：{{fansData.two.realName}}
            div(v-if='fansData.one')
              span(class='m-r30') 上1级
              span(class='m-r30') ID：{{fansData.one.id}}
              span(class='m-r30') 姓名：{{fansData.one.realName}}
            div(v-if='fansData.own')
              span(class='m-r30') 本人
              span(class='m-r30') ID：{{fansData.own.id}}
              span(class='m-r30') 姓名：{{fansData.own.realName}}
            el-tree(:data='fansData.fans' :props='fansProps')
              div(slot-scope='{node, data}')
                span(class='m-r30') 下{{data.level}}级
                span(class='m-r30') ID：{{data.id}}
                span 姓名：{{data.realName}}
                //- span(@click='getNode(node)') node
      .p-dialog2
        el-dialog(:visible.sync='dialog.isDialogShow2' title='支付记录' :data='records2')
          el-table(border stripe)
            el-table-column(type='index')
            el-table-column(label='交易时间' prop='payTime')
            el-table-column(label='数据来源' prop='sourceType')
            el-table-column(label='支付金额' prop='payTime')
            el-table-column(label='商品')
            el-table-column(label='累计支付')
      .p-dialog3
        el-dialog(:visible.sync='dialog.isDialogShow3' title='积分明细')
          el-table(border stripe)
            el-table-column(type='index')
            el-table-column(label='发生时间')
            //- 操作类型：1-购买会员卡；2-直推奖；3-团队奖；4-代理分润 5-推荐商家分润
            el-table-column(label='积分来源' prop='operationType')
            el-table-column(label='发生事由' prop='remark')
            el-table-column(label='数额' prop='changedValue')
            el-table-column(label='实时余额' prop='balance')
      .p-dialog4
        el-dialog(:visible.sync='dialog.isDialogShow4' title='优惠金明细')
          el-table(border stripe :data='records4')
            el-table-column(type='index')
            el-table-column(label='发生时间')
            el-table-column(label='优惠金来源' prop='operationType')
            el-table-column(label='行为' prop='type')
            el-table-column(label='对象')
            el-table-column(label='数据' prop='changedValue')
            el-table-column(label='实时余额' prop='balance')
</template>

<script>
import mixin from '../../mixin'

export default {
  data() {
    return {
      records: [],
      records2: [],
      records3: [],
      records4: [],
      total: 0,
      size: 10,
      current: 1,

      searchForm: {
        params: '',
      },
      dialog: {
        isDialogShow1: false,
        isDialogShow2: false,
        isDialogShow3: false,
        isDialogShow4: false
      },
      fansData: null,
      fansProps: {
        label: 'nickName',
        children: 'children'
      }
    }
  },
  mixins: [mixin],
  methods: {
    getNode(node) {
      console.log('node', node)
    },
    getMemberFan(memberId) {
      this.fansData = null
      this.dialog.isDialogShow1 = true
      API.basic.getMemberFan({memberId}).then(res => {
        this.fansData = res.data.data
      })
    },
    getScoreLogs(memberId) {
      this.dialog.isDialogShow3 = true
      API.basic.getScoreLogs({memberId, current: 1, size: 1000}).then(res => {
        this.records3 = res.data.data.records
      })
    },
    getCouponsLogs(memberId) {
      this.dialog.isDialogShow4 = true
      API.basic.getPaymentLogs({memberId, current: 1, size: 1000}).then(res => {
        this.records4 = res.data.data.records
      })
    },
    getPaymentLogs(memberId) {
      this.dialog.isDialogShow2 = true
      API.basic.getPaymentLogs({memberId, current: 1, size: 1000}).then(res => {
        this.records2 = res.data.data.records
      })
    },
    initSearchParams() {
      const {current, params} = this.$route.query

      this.searchForm.params = params
      this.searchForm.current = current || 1
    },
    getTableList() {
      const params = this.getSearchParams()

      params.size = this.size
      API.basic.getVipList(params).then(res => {
        const data = res.data.data
        if (data) {
          this.current = +data.current
          this.records = data.records
          this.total = +data.total
        }
      })
    },
    search() {
      this.changeSearch({
        params: this.searchForm.params
      })
      // this.getTableList()
    }
  }
}
</script>

<style lang="less" scoped>
.m-r30 {
  margin-right: 30px;
}
</style>
