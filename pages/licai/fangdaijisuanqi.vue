<template>
  <div class="fangdaijisuanqi">
    <el-form ref="form" :model="form" label-width="100%">
      <el-form-item label="还款方式">
        <el-radio-group v-model="type">
          <el-radio-button label="等额本息" />
          <el-radio-button label="等额本金" />
        </el-radio-group>
      </el-form-item>
      <el-form-item :label="`分期时间（${year}年）`">
        <el-slider v-model="year" :min="1" :max="30" />
      </el-form-item>
      <el-form-item label="贷款总额">
        <el-input class="elinput" v-model="money" placeholder="请输入内容"></el-input>
      </el-form-item>
      <el-form-item label="年贷款利率（%）">
        <el-input class="elinput" v-model="interestRate" placeholder="请输入内容"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="success" @click="getResult">计算</el-button>
      </el-form-item>
    </el-form>
    <el-form class="result" v-if="result.length > 0" label-width="180px">
      <el-form-item label="贷款：">
        {{money.toFixed(2)}} 元
      </el-form-item>
      <el-form-item label="总利息：">
        {{(result[0].moneyAll - result[0].moneyBen).toFixed(2)}} 元
      </el-form-item>
      <el-form-item label="每月应还：">
        <template v-if="type === '等额本息'">
          {{result[0].monthPay}} 元
        </template>
        <template v-else>
          见下表
        </template>
      </el-form-item>
      <el-form-item label="总共还款：">
        {{result[0].moneyAll}} 元
      </el-form-item>
    </el-form>
    <br>
    <el-table
      :data="result"
      style="width: 681px;">
      <el-table-column
        prop="month"
        label="月份"
        width="80">
      </el-table-column>
      <el-table-column
        prop="monthPay"
        label="每月还"
        width="120">
      </el-table-column>
      <el-table-column
        prop="moneyAll"
        label="剩余还"
        width="120">
      </el-table-column>
      <el-table-column
        prop="moneyBen"
        label="剩余本金"
        width="120">
      </el-table-column>
      <el-table-column
        prop="benCurr"
        label="还款本金"
        width="120">
      </el-table-column>
      <el-table-column
        prop="lixi"
        label="当月利息"
        width="120">
      </el-table-column>
    </el-table>
  </div>
</template>
<script>
export default {
  data() {
    return {
      form: {},
      year: 20,
      money: 590000,
      interestRate: 5.39,
      type: '等额本息',
      result: [],
    }
  },
  methods: {
    getResult() {
      this.result = []
      if (this.type === '等额本息') {
        // 每月还款
        let monthPay = ((this.money * (this.interestRate / 12 / 100) * Math.pow( (1 + this.interestRate / 12 / 100), this.year * 12) ) / ( Math.pow( (1 + this.interestRate / 12 / 100),this.year * 12 ) -1)).toFixed(2)
        // 总欠款
        let allPay = monthPay * this.year * 12
        let moneyAll = allPay
        let moneyBen = this.money
        for (let i = 0; i < this.year * 12; i++) {
          let lixi = (moneyBen * this.interestRate / 12 / 100).toFixed(2)
          let benCurr = monthPay - lixi
          this.result.push({
            monthPay,
            month: i + 1,
            moneyAll: moneyAll.toFixed(2),
            lixi,
            moneyBen: moneyBen.toFixed(2),
            benCurr: benCurr.toFixed(2),
          })
          moneyAll -= monthPay
          moneyBen -= monthPay - lixi
        }
      } else {
        // 每月还款本金
        let monthPayBen = this.money / (this.year * 12)
        let moneyBen = this.money * 1
        for (var i = 0; i < this.year * 12; i++) {
          let lixi = (moneyBen * this.interestRate / 12 / 100).toFixed(2)
          let monthPay = lixi * 1 + monthPayBen
          this.result.push({
            monthPay: monthPay.toFixed(2),
            month: i + 1,
            lixi,
            moneyBen: moneyBen.toFixed(2),
            benCurr: monthPayBen.toFixed(2),
          })
          moneyBen -= monthPayBen
        }
        // 总共需要还
        let moneyAll = this.money * 1 + (this.result[0].lixi * 1 + this.result[this.result.length - 1].lixi * 1) / 2 * this.year * 12
        this.result[0]['moneyAll'] = moneyAll.toFixed(2)
        for (let i = 1; i < this.result.length; i++) {
          this.result[i]['moneyAll'] = (this.result[i - 1]['moneyAll'] * 1 - 1 * this.result[i - 1]['monthPay']).toFixed(2)
        }
      }
    },
  },
  head() {
    return {
      title: '房贷计算器',
      meta: [
        { hid: 'description', name: 'description', content: '在线房贷计算器' }
      ]
    }
  },
  watch: {
    type() {
      this.getResult()
    },
  },
}
</script>
<style lang="scss">
.fangdaijisuanqi {
  .el-form {
    max-width: 800px;
  }
  .el-form-item__label {
    width: 140px !important;
  }
  .el-form-item__content {
    margin-left: 140px !important;
  }
  .elinput {
    input {
      width: 100%;
    }
  }
  .result {
    .el-form-item {
      margin-bottom: 5px;
    }
  }
}
</style>
