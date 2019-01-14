<template>
  <v-app>
    <v-toolbar
            color="indigo"
            dark
    >
      <v-toolbar-title>日報作成</v-toolbar-title>
    </v-toolbar>
    <v-tabs fixed-tabs>
      <v-tab :key="1">
        予定
      </v-tab>
      <v-tab :key="2">
        実績
      </v-tab>

      <v-tab-item :key="1">
        <v-container grid-list-md text-xs-center>
          <v-layout row wrap>
            <v-flex xs12 md6>
              <v-card>
                <v-card-title>Input</v-card-title>
                <div class="text-xs-right">
                  <v-btn color="success" v-on:click="addTask(taskPlans)">追加&nbsp;<v-icon>library_add</v-icon></v-btn>
                </div>
                  <draggable v-model="taskPlans" :options="{handle:'.grip-area'}">
                    <v-card v-for="(task, index) in taskPlans" :key="task.id">
                      <v-card-text raised>
                        <v-form>
                          <v-layout wrap>
                            <v-flex md1>
                              <div class="grip-area"></div>
                            </v-flex>
                            <v-flex md4>
                              <v-text-field v-model="task.category" label="カテゴリ"></v-text-field>
                            </v-flex>
                            <v-flex md4>
                             <v-text-field v-model="task.content" label="内容"></v-text-field>
                            </v-flex>
                            <v-flex md2>
                              <v-text-field v-model="task.time" type="number" step="15" min="0" label="時間" suffix="分"></v-text-field>
                            </v-flex>
                            <v-flex md1>
                              <v-btn small icon color="error" v-on:click="removeTask(taskPlans, index)">
                                <v-icon small>delete_forever</v-icon>
                              </v-btn>
                              <v-btn small icon color="success" v-on:click="copyTask(taskPlans, index)">
                                <v-icon small>file_copy</v-icon>
                              </v-btn>
                            </v-flex>
                          </v-layout>
                        </v-form>
                      </v-card-text>
                    </v-card>
                  </draggable>
              </v-card>
            </v-flex>
            <v-flex xs12 md6>
              <v-card>
                <v-card-title>Output</v-card-title>
                <v-layout wrap>
                  <v-flex xs6 offset-md1 md2>
                    <v-text-field type="time" v-model="openingTime" label="開始時刻"></v-text-field>
                  </v-flex>
                  <v-flex xs6 md9>
                    <div class="text-xs-right">
                      <v-btn color="primary" class="clipcopy" data-clipboard-target="#daily-report-plan">
                        コピー&nbsp;<v-icon>file_copy</v-icon>
                      </v-btn>
                    </div>
                  </v-flex>
                  <v-flex xs12 md12>
                    <v-textarea
                            box
                            readonly
                            v-model="dailyReportPlan"
                            :rows="dailyReportPlanRows"
                            id="daily-report-plan"
                    ></v-textarea>
                  </v-flex>
                </v-layout>
              </v-card>
            </v-flex>
          </v-layout>
        </v-container>
      </v-tab-item>

      <v-tab-item :key="2">
        <v-container grid-list-md text-xs-center>
          <v-layout row wrap>
            <v-flex xs12 md6>
              <v-card>
                <v-card-title>Input</v-card-title>
                <div class="text-xs-right">
                  <v-btn class="left" color="success" v-on:click="copyTaskPlans()">予定をコピー&nbsp;<v-icon>file_copy</v-icon></v-btn>
                  <v-btn color="success" v-on:click="addTask(taskResults)">追加&nbsp;<v-icon>library_add</v-icon></v-btn>
                </div>
                <draggable v-model="taskResults" :options="{handle:'.grip-area'}">
                  <v-card v-for="(task, index) in taskResults" :key="task.id">
                    <v-card-text raised>
                      <v-form>
                        <v-layout wrap>
                          <v-flex md1>
                            <div class="grip-area"></div>
                          </v-flex>
                          <v-flex md4>
                            <v-text-field v-model="task.category" label="カテゴリ"></v-text-field>
                          </v-flex>
                          <v-flex md4>
                            <v-text-field v-model="task.content" label="内容"></v-text-field>
                          </v-flex>
                          <v-flex md2>
                            <v-text-field v-model="task.time" type="number" step="15" min="0" label="時間" suffix="分"></v-text-field>
                          </v-flex>
                          <v-flex md1>
                            <v-btn small icon color="error" v-on:click="removeTask(taskResults, index)">
                              <v-icon small>delete_forever</v-icon>
                            </v-btn>
                            <v-btn small icon color="success" v-on:click="copyTask(taskResults, index)">
                              <v-icon small>file_copy</v-icon>
                            </v-btn>
                          </v-flex>
                        </v-layout>
                      </v-form>
                    </v-card-text>
                  </v-card>
                </draggable>
              </v-card>
            </v-flex>
            <v-flex xs12 md6>
              <v-card>
                <v-card-title>Output</v-card-title>
                <v-layout wrap>
                  <v-flex xs6 offset-md1 md2>
                    <v-text-field type="time" v-model="openingTime" label="開始時刻"></v-text-field>
                  </v-flex>
                  <v-flex xs6 md9>
                    <div class="text-xs-right">
                      <v-btn color="primary" class="clipcopy" data-clipboard-target="#daily-report-result">
                        コピー&nbsp;<v-icon>file_copy</v-icon>
                      </v-btn>
                    </div>
                  </v-flex>
                  <v-flex xs12 md12>
                    <v-textarea
                            box
                            readonly
                            v-model="dailyReportResult"
                            :rows="dailyReportResultRows"
                            id="daily-report-result"
                    ></v-textarea>
                  </v-flex>
                </v-layout>
              </v-card>
            </v-flex>
          </v-layout>
        </v-container>
      </v-tab-item>
    </v-tabs>
    <v-snackbar
            v-model="copySuccess"
            top
            right
            :timeout="timeout"
    >
      クリップボードにコピーしました
      <v-btn
              color="pink"
              flat
              @click="copySuccess = false"
      >
        Close
      </v-btn>
    </v-snackbar>

  </v-app>
</template>

<script>
  import draggable from 'vuedraggable'
  import clipboard from 'clipboard'

  export default {
    name: 'app',
    components: {
      draggable
    },
    data () {
      return {
        clipBoard: null,
        copySuccess: false,
        timeout: 2000,
        idIndex: 1,
        taskPlans: [],
        taskResults: [],
        openingTime: "09:30"
      }
    },
    watch: {
      idIndex: function (val) {
        localStorage.idIndex = val
      },
      taskPlans: {
        handler: function (val) {
          localStorage.taskPlans = JSON.stringify(val)
        },
        deep: true
      },
      taskResults: {
        handler: function (val) {
          localStorage.taskResults = JSON.stringify(val)
        },
        deep: true
      },
      openingTime: function (val) {
        localStorage.openingTime = val
      }
    },
    computed: {
      dailyReportPlan: function () {
        return this.generateDailyReportPlan()
      },
      dailyReportResult: function () {
        let dailyReportContents = ""

        dailyReportContents += this.generateDailyReportPlan()
        dailyReportContents += "\n"

        let dayString = this.generateDayString()
        let resultTitle = "＜" + dayString + "の実績" + "＞" + "\n"
        dailyReportContents += resultTitle

        let startTime = this.openingTime
        dailyReportContents += this.generateTaskContents(this.taskResults, startTime)
        dailyReportContents += "\n"

        let planBreakDownArray = this.generateBreakDownArray(this.taskPlans);
        let resultBreakDownArray = this.generateBreakDownArray(this.taskResults);
        let totalTime = 0
        dailyReportContents += "＜実績の内訳＞" + "\n"
        Object.keys(resultBreakDownArray).forEach(function(key) {
          let hour = Math.round(this[key] * 100 / 60) / 100
          totalTime += hour

          let breakDownContent = ""
          if (key in planBreakDownArray) {
            let difference = Math.round((this[key] - planBreakDownArray[key]) * 100 / 60) / 100
            let differenceString = difference > 0 ? ("+" + String(difference)) : String(difference)
            breakDownContent = "【" + key + "】" + hour + "H (" + differenceString + "H)" + "\n"
          } else {
            breakDownContent = "【" + key + "】" + hour + "H (+" + String(hour) + "H)" + "\n"
          }
          dailyReportContents += breakDownContent
        }, resultBreakDownArray);
        let breakDownTotal = "\n" + "合計：" + totalTime + "H" + "\n"
        dailyReportContents += breakDownTotal

        return dailyReportContents
      },
      dailyReportPlanRows: function () {
        return this.dailyReportPlan.split("\n").length
      },
      dailyReportResultRows: function () {
        return this.dailyReportResult.split("\n").length
      }
    },
    methods: {
      addTask (tasks) {
        tasks.push({id: this.idIndex, category: '', content: '', time: 0})
        this.idIndex++
      },
      removeTask (tasks, index) {
        tasks.splice(index, 1)
      },
      copyTask (tasks, index) {
        let copyTask = JSON.stringify(tasks[index])
        copyTask = JSON.parse(copyTask)
        copyTask.id = this.idIndex
        this.idIndex++

        tasks.splice(index, 0, copyTask)
      },
      copyTaskPlans() {
        // 参照渡しされないように一度文字列化
        let tmpTasks = JSON.stringify(this.taskPlans)
        this.taskResults = JSON.parse(tmpTasks)
      },
      generateDayString() {
        let date = new Date ()
        let month = date.getMonth() + 1
        let day = date.getDate()
        let dayOfWeek = ["日", "月", "火", "水", "木", "金", "土"][date.getDay()]
        let dayString = month + "/" + day + "(" + dayOfWeek + ")"

        return dayString
      },
      generateTaskContents(tasks, startTime) {
        let taskContents = "";

        tasks.forEach(function (task) {
          let startTimeNumber = startTime.split(':')
          let endDate = new Date(2019, 1, 1, Number(startTimeNumber[0]), Number(startTimeNumber[1]))
          endDate.setMinutes(endDate.getMinutes() + Number(task.time))
          let endTime = ("0" + endDate.getHours()).slice(-2) + ":" + ("0" + endDate.getMinutes()).slice(-2)
          let taskTerm = startTime + "~" + endTime

          let taskContent = taskTerm + " 【" + task.category + "】" + task.content + "\n"

          startTime = endTime
          taskContents += taskContent
        })

        return taskContents
      },
      generateBreakDownArray(tasks) {
        let breakDownArray = {}

        tasks.forEach(function (task) {
          if (task.category in breakDownArray) {
            breakDownArray[task.category] += Number(task.time)
          } else {
            breakDownArray[task.category] = Number(task.time)
          }
        })

        return breakDownArray
      },
      generateDailyReportPlan() {
        let dailyReportContents = ""

        let dayString = this.generateDayString()
        let planTitle = "＜" + dayString + "の予定" + "＞" + "\n"
        dailyReportContents += planTitle

        let startTime = this.openingTime
        dailyReportContents += this.generateTaskContents(this.taskPlans, startTime)
        dailyReportContents += "\n"

        let breakDownArray = this.generateBreakDownArray(this.taskPlans);
        let totalTime = 0
        dailyReportContents += "＜予定の内訳＞" + "\n"
        Object.keys(breakDownArray).forEach(function(key) {
          let hour = Math.round(this[key] * 100 / 60) / 100
          totalTime += hour
          let breakDownContent = "【" + key + "】" + hour + "H" + "\n"
          dailyReportContents += breakDownContent
        }, breakDownArray);
        let breakDownTotal = "\n" + "合計：" + totalTime + "H" + "\n"
        dailyReportContents += breakDownTotal

        return dailyReportContents
      }
    },
    mounted : function(){
      // -- クリップボード設定
      this.clipBoard = new clipboard('.clipcopy')
      this.clipBoard.on('success', function(e) {
        this.copySuccess = true
        e.clearSelection()
      }, this);
      // -- クリップボード設定ここまで

      // ローカルストレージ取得
      if (localStorage.idIndex) {
        this.idIndex = localStorage.idIndex
      }
      if (localStorage.taskPlans) {
        this.taskPlans = JSON.parse(localStorage.taskPlans)
      }
      if (localStorage.taskResults) {
        this.taskResults = JSON.parse(localStorage.taskResults)
      }
      if (localStorage.openingTime) {
        this.openingTime = localStorage.openingTime
      }

    },
  }
</script>

<style>
.grip-area {
  width: 100%;
  height: 100%;
  background: #fff;
  background-image: radial-gradient(#bbb 20%, transparent 0);
  background-position: 0 0, 1px 5px;
  background-size: 3px 3px;
}
</style>
