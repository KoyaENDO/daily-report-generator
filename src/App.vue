<template>
  <v-app>
    <v-toolbar color="indigo" dark>
      <v-toolbar-title>日報作成</v-toolbar-title>
    </v-toolbar>
    <v-tabs fixed-tabs>
      <v-tab :key="1">予定</v-tab>
      <v-tab :key="2">実績</v-tab>

      <v-tab-item :key="1">
        <v-container grid-list-md text-xs-center>
          <v-layout row wrap>
            <v-flex xs12 md6>
              <v-card>
                <v-card-title>Input</v-card-title>
                <div class="text-xs-right">
                  <v-btn class="left" color="error" v-on:click="reset()">
                    予定と実績を全て削除&nbsp;&nbsp;
                    <v-icon>delete_forever</v-icon>
                  </v-btn>
                  <v-btn class="center" color="warning" v-on:click="restore()">
                    復元&nbsp;&nbsp;
                    <v-icon>restore</v-icon>
                  </v-btn>
                  <v-btn color="success" v-on:click="addTask(taskPlans)">
                    追加&nbsp;
                    <v-icon>library_add</v-icon>
                  </v-btn>
                </div>
                <draggable
                  v-model="taskPlans"
                  :options="{ handle: '.grip-area' }"
                >
                  <v-card v-for="(task, index) in taskPlans" :key="index">
                    <v-card-text raised>
                      <v-form>
                        <v-layout wrap>
                          <v-flex md1>
                            <div class="grip-area"></div>
                          </v-flex>
                          <v-flex md4>
                            <v-text-field
                              v-model="task.category"
                              label="カテゴリ"
                            ></v-text-field>
                          </v-flex>
                          <v-flex md4>
                            <v-text-field
                              v-model="task.content"
                              label="内容"
                            ></v-text-field>
                          </v-flex>
                          <v-flex md2>
                            <v-text-field
                              v-model="task.time"
                              type="number"
                              step="15"
                              min="0"
                              label="時間"
                              suffix="分"
                            ></v-text-field>
                          </v-flex>
                          <v-flex md1>
                            <v-btn
                              small
                              icon
                              color="error"
                              v-on:click="removeTask(taskPlans, index)"
                            >
                              <v-icon small>delete_forever</v-icon>
                            </v-btn>
                            <v-btn
                              small
                              icon
                              color="success"
                              v-on:click="copyTask(taskPlans, index)"
                            >
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
                    <v-text-field
                      type="time"
                      v-model="openingTime"
                      label="開始時刻"
                    ></v-text-field>
                  </v-flex>
                  <v-flex xs6 md9>
                    <div class="text-xs-right">
                      <v-btn
                        color="primary"
                        class="clipcopy"
                        data-clipboard-target="#daily-report-plan"
                      >
                        コピー&nbsp;
                        <v-icon>file_copy</v-icon>
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
                  <v-btn
                    class="left"
                    color="success"
                    v-on:click="copyTaskPlans()"
                  >
                    予定から実績を作成&nbsp;&nbsp;
                    <v-icon>input</v-icon>
                  </v-btn>
                  <v-btn color="success" v-on:click="addTask(taskResults)">
                    追加&nbsp;
                    <v-icon>library_add</v-icon>
                  </v-btn>
                </div>
                <draggable
                  v-model="taskResults"
                  :options="{ handle: '.grip-area' }"
                >
                  <v-card v-for="(task, index) in taskResults" :key="index">
                    <v-card-text raised>
                      <v-form>
                        <v-layout wrap>
                          <v-flex md1>
                            <div class="grip-area"></div>
                          </v-flex>
                          <v-flex md4>
                            <v-text-field
                              v-model="task.category"
                              label="カテゴリ"
                            ></v-text-field>
                          </v-flex>
                          <v-flex md4>
                            <v-text-field
                              v-model="task.content"
                              label="内容"
                            ></v-text-field>
                          </v-flex>
                          <v-flex md2>
                            <v-text-field
                              v-model="task.time"
                              type="number"
                              step="15"
                              min="0"
                              label="時間"
                              suffix="分"
                            ></v-text-field>
                          </v-flex>
                          <v-flex md1>
                            <v-btn
                              small
                              icon
                              color="error"
                              v-on:click="removeTask(taskResults, index)"
                            >
                              <v-icon small>delete_forever</v-icon>
                            </v-btn>
                            <v-btn
                              small
                              icon
                              color="success"
                              v-on:click="copyTask(taskResults, index)"
                            >
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
                    <v-text-field
                      type="time"
                      v-model="openingTime"
                      label="開始時刻"
                    ></v-text-field>
                  </v-flex>
                  <v-flex xs6 md9>
                    <div class="text-xs-right">
                      <v-btn
                        color="primary"
                        class="clipcopy"
                        data-clipboard-target="#daily-report-result"
                      >
                        コピー&nbsp;
                        <v-icon>file_copy</v-icon>
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
    <v-snackbar v-model="copySuccess" top right :timeout="timeout">
      クリップボードにコピーしました
      <v-btn color="pink" flat @click="copySuccess = false">Close</v-btn>
    </v-snackbar>
    <v-snackbar v-model="deleteSuccess" top right :timeout="timeout">
      全てのタスクを削除しました
      <v-btn color="pink" flat @click="deleteSuccess = false">Close</v-btn>
    </v-snackbar>
    <v-snackbar v-model="restoreSuccess" top right :timeout="timeout">
      ひとつ前の予定と実績を復元しました
      <v-btn color="pink" flat @click="restoreSuccess = false">Close</v-btn>
    </v-snackbar>
    <v-snackbar v-model="noPreviousData" top right :timeout="timeout">
      削除されたものがないため、復元できませんでした
      <v-btn color="pink" flat @click="restoreSuccess = false">Close</v-btn>
    </v-snackbar>
  </v-app>
</template>

<script>
import draggable from "vuedraggable";
import clipboard from "clipboard";

export default {
  name: "app",
  components: {
    draggable,
  },
  data() {
    return {
      clipBoard: null,
      copySuccess: false,
      deleteSuccess: false,
      restoreSuccess: false,
      timeout: 2000,
      taskPlans: [],
      taskResults: [],
      previousTaskPlans: [],
      previousTaskResults: [],
      noPreviousData: false,
      openingTime: "09:30",
    };
  },
  watch: {
    taskPlans: {
      handler: function (val) {
        localStorage.taskPlans = JSON.stringify(val);
      },
      deep: true,
    },
    taskResults: {
      handler: function (val) {
        localStorage.taskResults = JSON.stringify(val);
      },
      deep: true,
    },
    previousTaskPlans: {
      handler: function (val) {
        localStorage.previousTaskPlans = JSON.stringify(val);
      },
      deep: true,
    },
    previousTaskResults: {
      handler: function (val) {
        localStorage.previousTaskResults = JSON.stringify(val);
      },
      deep: true,
    },
    openingTime: function (val) {
      localStorage.openingTime = val;
    },
  },
  computed: {
    dailyReportPlan: function () {
      return this.generateDailyReportPlan();
    },
    dailyReportResult: function () {
      let dailyReportContents = "";

      dailyReportContents += this.generateDailyReportPlan();
      dailyReportContents += "\n";

      let dayString = this.generateDayString();
      let resultTitle = "＜" + dayString + "の実績" + "＞" + "\n";
      dailyReportContents += resultTitle;

      let startTime = this.openingTime;
      dailyReportContents += this.generateTaskContents(
        this.taskResults,
        startTime
      );
      dailyReportContents += "\n";

      let planBreakDownArray = this.generateBreakDownArray(this.taskPlans);
      let resultBreakDownArray = this.generateBreakDownArray(this.taskResults);
      let totalTime = 0;
      dailyReportContents += "＜実績の内訳＞" + "\n";

      // 実績をもとに内訳を算出
      Object.keys(resultBreakDownArray).forEach(function (key) {
        let hour = Math.round((this[key] * 100) / 60) / 100;
        totalTime += hour;

        let breakDownContent = "";
        if (key in planBreakDownArray) {
          let difference =
            Math.round(((this[key] - planBreakDownArray[key]) * 100) / 60) /
            100;
          let differenceString =
            difference > 0
              ? "+" + difference.toFixed(2)
              : difference.toFixed(2);
          breakDownContent =
            "【" +
            key +
            "】" +
            hour.toFixed(2) +
            "H (" +
            differenceString +
            "H)" +
            "\n";
        } else {
          breakDownContent =
            "【" +
            key +
            "】" +
            hour.toFixed(2) +
            "H (+" +
            hour.toFixed(2) +
            "H)" +
            "\n";
        }
        dailyReportContents += breakDownContent;
      }, resultBreakDownArray);

      // 予定をもとに実績になかったものの内訳を算出
      Object.keys(planBreakDownArray).forEach(function (key) {
        if (!(key in resultBreakDownArray)) {
          let hour = Math.round((this[key] * 100) / 60) / 100;

          let breakDownContent =
            "【" + key + "】0.0H (-" + hour.toFixed(2) + "H)" + "\n";
          dailyReportContents += breakDownContent;
        }
      }, planBreakDownArray);

      let breakDownTotal = "\n" + "合計：" + totalTime.toFixed(2) + "H" + "\n";
      dailyReportContents += breakDownTotal;

      return dailyReportContents;
    },
    dailyReportPlanRows: function () {
      return this.dailyReportPlan.split("\n").length;
    },
    dailyReportResultRows: function () {
      return this.dailyReportResult.split("\n").length;
    },
  },
  methods: {
    reset() {
      this.storePreviousData();
      this.taskPlans = [];
      this.taskResults = [];
      this.openingTime = "09:30";
      this.deleteSuccess = true;
    },
    restore() {
      if (this.previousTaskPlans.length || this.previousTaskResults.length) {
        this.taskPlans = this.previousTaskPlans;
        this.taskResults = this.previousTaskResults;
        this.restoreSuccess = true;
      } else {
        this.noPreviousData = true;
      }
    },
    storePreviousData() {
      // 参照渡しを防ぐ
      let tmpTasks = JSON.stringify(this.taskPlans);
      let tmpResults = JSON.stringify(this.taskResults);
      this.previousTaskPlans = JSON.parse(tmpTasks);
      this.previousTaskResults = JSON.parse(tmpResults);
    },
    addTask(tasks) {
      tasks.push({ category: "", content: "", time: 0 });
    },
    async removeTask(tasks, index) {
      this.storePreviousData();
      tasks.splice(index, 1);
    },
    copyTask(tasks, index) {
      let copyTask = JSON.stringify(tasks[index]);
      copyTask = JSON.parse(copyTask);

      tasks.splice(index, 0, copyTask);
    },
    copyTaskPlans() {
      // 参照渡しされないように一度文字列化
      let tmpTasks = JSON.stringify(this.taskPlans);
      this.taskResults = JSON.parse(tmpTasks);
    },
    generateDayString() {
      let date = new Date();
      let month = date.getMonth() + 1;
      let day = date.getDate();
      let dayOfWeek = ["日", "月", "火", "水", "木", "金", "土"][date.getDay()];
      let dayString = month + "/" + day + "(" + dayOfWeek + ")";

      return dayString;
    },
    generateTaskContents(tasks, startTime) {
      let taskContents = "";

      tasks.forEach(function (task) {
        let startTimeNumber = startTime.split(":");
        let endDate = new Date(
          2019,
          1,
          1,
          Number(startTimeNumber[0]),
          Number(startTimeNumber[1])
        );
        endDate.setMinutes(endDate.getMinutes() + Number(task.time));
        let endTime =
          ("0" + endDate.getHours()).slice(-2) +
          ":" +
          ("0" + endDate.getMinutes()).slice(-2);
        let taskTerm = startTime + "~" + endTime;

        let taskContent =
          taskTerm + " 【" + task.category + "】" + task.content + "\n";

        startTime = endTime;
        taskContents += taskContent;
      });

      return taskContents;
    },
    generateBreakDownArray(tasks) {
      let breakDownArray = {};

      tasks.forEach(function (task) {
        if (task.category in breakDownArray) {
          breakDownArray[task.category] += Number(task.time);
        } else {
          breakDownArray[task.category] = Number(task.time);
        }
      });

      return breakDownArray;
    },
    generateDailyReportPlan() {
      let dailyReportContents = "";

      let dayString = this.generateDayString();
      let planTitle = "＜" + dayString + "の予定" + "＞" + "\n";
      dailyReportContents += planTitle;

      let startTime = this.openingTime;
      dailyReportContents += this.generateTaskContents(
        this.taskPlans,
        startTime
      );
      dailyReportContents += "\n";

      let breakDownArray = this.generateBreakDownArray(this.taskPlans);
      let totalTime = 0;
      dailyReportContents += "＜予定の内訳＞" + "\n";
      Object.keys(breakDownArray).forEach(function (key) {
        let hour = Math.round((this[key] * 100) / 60) / 100;
        totalTime += hour;
        let breakDownContent = "【" + key + "】" + hour.toFixed(2) + "H" + "\n";
        dailyReportContents += breakDownContent;
      }, breakDownArray);
      let breakDownTotal = "\n" + "合計：" + totalTime.toFixed(2) + "H" + "\n";
      dailyReportContents += breakDownTotal;

      return dailyReportContents;
    },
  },
  mounted: function () {
    // -- クリップボード設定
    this.clipBoard = new clipboard(".clipcopy");
    this.clipBoard.on(
      "success",
      function (e) {
        this.copySuccess = true;
        e.clearSelection();
      },
      this
    );
    // -- クリップボード設定ここまで

    // ローカルストレージ取得

    if (localStorage.taskPlans) {
      this.taskPlans = JSON.parse(localStorage.taskPlans);
    }
    if (localStorage.taskResults) {
      this.taskResults = JSON.parse(localStorage.taskResults);
    }
    if (localStorage.previousTaskPlans) {
      this.previousTaskPlans = JSON.parse(localStorage.previousTaskPlans);
    }
    if (localStorage.previousTaskResults) {
      this.taskResults = JSON.parse(localStorage.taskResults);
    }
    if (localStorage.openingTime) {
      this.openingTime = localStorage.openingTime;
    }
  },
};
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
