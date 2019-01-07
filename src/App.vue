<template>
  <v-app>
    <v-toolbar
            color="indigo"
            dark
    >
      <v-toolbar-title>日報作成</v-toolbar-title>
    </v-toolbar>
    <v-container grid-list-md text-xs-center>
      <v-layout row wrap>
        <v-flex xs12 md6>
          <v-card>
            <v-card-title>Input</v-card-title>
            <div class="text-xs-right">
              <v-btn color="success" v-on:click="addTask">追加&nbsp;<v-icon>library_add</v-icon></v-btn>
            </div>
              <draggable v-model="tasks" :options="{handle:'.grip-area'}">
                <v-card v-for="(task, index) in tasks" :key="task.id">
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
                          <v-btn small icon color="error" v-on:click="removeTask(index)">
                            <v-icon small>delete_forever</v-icon>
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
                  <v-btn color="primary" class="clipcopy" data-clipboard-target="#daily-report">
                    コピー&nbsp;<v-icon>file_copy</v-icon>
                  </v-btn>
                </div>
              </v-flex>
              <v-flex xs12 md12>
                <v-textarea
                        box
                        readonly
                        v-model="dailyReport"
                        :rows="dailyReportRows"
                        id="daily-report"
                ></v-textarea>
              </v-flex>
            </v-layout>
          </v-card>
        </v-flex>
      </v-layout>
    </v-container>
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
        tasks: [],
        openingTime: "09:30"
      }
    },
    computed: {
      dailyReport: function () {
        let dailyReportContents = ""
        let breakDownArray = {}

        let date = new Date ()
        let month = date.getMonth() + 1
        let day = date.getDate()
        let dayOfWeek = ["日", "月", "火", "水", "木", "金", "土"][date.getDay()]
        let plansTitle = "＜" + month + "/" + day + "(" + dayOfWeek + ")の予定" + "＞" + "\n"

        dailyReportContents += plansTitle

        let startTime = this.openingTime
        this.tasks.forEach(function (task) {
          let startTimeNumber = startTime.split(':')
          let endDate = new Date(2019, 1, 1, Number(startTimeNumber[0]), Number(startTimeNumber[1]))
          endDate.setMinutes(endDate.getMinutes() + Number(task.time))
          let endTime = ("0" + endDate.getHours()).slice(-2) + ":" + ("0" + endDate.getMinutes()).slice(-2)
          let taskTerm = startTime + "~" + endTime

          let taskContent = taskTerm + " 【" + task.category + "】" + task.content + "\n"

          startTime = endTime
          dailyReportContents += taskContent

          if (task.category in breakDownArray) {
            breakDownArray[task.category] += Number(task.time)
          } else {
            breakDownArray[task.category] = Number(task.time)
          }
        })
        dailyReportContents += "\n"

        let totalTime = 0
        dailyReportContents += "＜内訳＞" + "\n"
        Object.keys(breakDownArray).forEach(function(key) {
          let hour = Math.round(this[key] * 100 / 60) / 100
          totalTime += hour
          let breakDownContent = "【" + key + "】" + hour + "H" + "\n"
          dailyReportContents += breakDownContent
        }, breakDownArray);
        let breakDownTotal = "\n" + "合計：" + totalTime + "H" + "\n"
        dailyReportContents += breakDownTotal

        return dailyReportContents
      },
      dailyReportRows: function () {
        return this.dailyReport.split("\n").length
      }
    },
    methods: {
      addTask () {
        this.tasks.push({id: this.idIndex, category: '', content: '', time: 0})
        this.idIndex++
      },
      removeTask (index) {
        this.tasks.splice(index, 1)
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
