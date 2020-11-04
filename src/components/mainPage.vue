<template>
  <div class="container">
    <el-input placeholder="Введите доход/расход" v-model="value" type="number" class="input-with-select" min="0">
      <el-button slot="prepend" icon="el-icon-money" @click="addMoney(false)">Расход</el-button>
      <el-button slot="append" icon="el-icon-money" @click="addMoney(true)">Доход</el-button>
    </el-input>
      <h4>Сумма доходов - {{ sumFinance.sum}}</h4>
      <h4>Сумма расходов - {{ sumFinance.minus}}</h4>
    <el-table
        :data="tableData"
        style="width: 100%; margin-top: 15px">
      <el-table-column
          prop="money"
          label="Сумма">
      </el-table-column>
      <el-table-column
          label="Тип доход\расход"
          width="180">
        <template slot-scope="scope">
          <span v-if="scope.row.type" class="income">Доход</span>
          <span v-else class="consumption">Расход</span>
        </template>
      </el-table-column>
      <el-table-column
          align="right">
        <template slot-scope="scope">
          <el-button
              size="mini"
              type="danger"
              @click="handleDelete(scope.$index)">Удалить
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <div class="small">
      <line-chart :chart-data="datacollection"></line-chart>
    </div>

  </div>
</template>

<script>
import LineChart from './LineChart.js'

export default {
  name: "mainPage",
  data() {
    return {
      value: 0,
      tableData: [],
      datacollection: null
    }
  },
  components: {
    LineChart
  },
  methods: {
    fillData () {
      this.datacollection = {
        labels: ['Доход', 'Расход'],
        datasets: [
          {
            label: 'Доход',
            backgroundColor: ['#78d278', '#f56c6c'],
            data: [this.sumFinance.sum, this.sumFinance.minus]
          }
        ]
      }
    },
    addMoney(type) {
      this.tableData.push({
        type: type,
        money: this.value
      })
    },
    handleDelete(index) {
      this.tableData.splice(index, 1)
    },
},
  watch: {
    tableData() {
      localStorage.setItem('tableData', JSON.stringify(this.tableData))
      this.fillData()
    }
  },
  mounted() {
    if (localStorage.getItem('tableData') !== null) {
      this.tableData = JSON.parse(localStorage.getItem('tableData'))
    }
  },
  computed: {
    sumFinance() {
      let sum = 0
      let minus = 0
      this.tableData.forEach((item)=> {
        console.log(item);
        if (item.type){
          sum = sum + (+item.money)
        } else {
          minus = minus + (+item.money)
        }
      })
      return {
        sum,
        minus
      }
    },
  }

}
</script>

<style scoped>
.consumption{
  font-size: 12px;
  display: block;
  text-align: center;
  vertical-align: middle;
  color: white;
  height: 15px;
  max-width: 65px;
  margin: 0 auto;
  padding: 7px;
  border-radius: 5px;
  background-color: #f56c6c;
}
.income{
  font-size: 12px;
  display: block;
  text-align: center;
  vertical-align: middle;
  color: white;
  height: 15px;
  max-width: 65px;
  margin: 0 auto;
  padding: 7px;
  border-radius: 5px;
  background-color: #78d278;
}
.container {
  max-width: 1024px;
  margin: 15px auto;
}


.el-select .el-input {
  width: 110px;
}

.input-with-select .el-input-group__prepend {
  background-color: #fff;
}
.small {
  max-width: 600px;
  margin:  150px auto;
}
</style>