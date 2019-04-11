<template>
  <div class="fenqishengduoshao">
    <el-form ref="form" :model="form" label-width="180px">
      <el-form-item label="分期时间（月数）">
        <el-input v-model="month" placeholder="请输入内容"></el-input>
      </el-form-item>
      <el-form-item label="每月投资收益（‰）">
        <el-input v-model="income" placeholder="请输入内容"></el-input>
      </el-form-item>
      <el-form-item label="总金额">
        <el-input v-model="money" placeholder="请输入内容"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="success" @click="getResult">计算</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="result"
      style="width: 100%">
      <el-table-column
        prop="month"
        label="月份"
        width="80">
      </el-table-column>
      <el-table-column
        prop="money"
        label="每月还款"
        width="120">
      </el-table-column>
      <el-table-column
        prop="blance"
        label="剩余金额"
        width="120">
      </el-table-column>
      <el-table-column
        prop="income"
        label="投资收益"
        width="180">
      </el-table-column>
    </el-table>
    <br>
  </div>
</template>
<script>
export default {
  data() {
    return {
      month: '12',
      depreciation: '5',
      money: 10000,
      income: 3,
      form: {},
      result: [],
    }
  },
  methods: {
    getResult() {
      this.result = []
      let money = (this.money / this.month).toFixed(2)
      let frugally = 0
      for (let i = 0; i < this.month; i++) {
        let blance = (this.money - money * i).toFixed(2)
        let income = (blance * (this.income / 1000)).toFixed(2)
        frugally += 1 * income
        this.result.push({
          month: i + 1,
          money,
          blance,
          income,
        })
      }
      this.result.push({
        money: '',
        blance: '',
        income: `省：${frugally.toFixed(2)}元`,
      })
    },
  },
  head() {
    return {
      title: '免利息分期省多少',
      meta: [
        { hid: 'description', name: 'description', content: '6月免息省多少，12月免息省多少，24个月免息省多少，3年免息省多少' }
      ]
    }
  },
}
</script>
<style lang="scss">
.fenqishengduoshao {
  input {
    width: 180px;
  }
}
</style>
