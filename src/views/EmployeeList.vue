<template>
  <div class="container">
    <!-- パンくずリスト -->
    <nav>
      <div class="nav-wrapper">
        <div class="col s12 teal">
          <a class="breadcrumb">従業員リスト</a>
        </div>
      </div>
    </nav>
    <div>従業員数:{{ getEmployeeCount }}人</div>
    <div id="pagination">
      <span class="page-change-btn" v-on:click="showPrev">前のページへ</span>
      <span class="page-change-btn" v-on:click="showNext">次のページへ</span>
    </div>
    <div class="row">
      <table class="striped">
        <thead>
          <tr>
            <th>名前</th>
            <th>入社日</th>
            <th>扶養人数</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="employee of dispEmployees" v-bind:key="employee.id">
            <td>
              <router-link :to="'/employeeDetail/' + employee.id">{{
                employee.name
              }}</router-link>
            </td>
<<<<<<< .merge_file_BSZDgi
            <td>{{ employee.hireDate }}</td>
=======
            <td>{{ employee.salaryStringPretty }}</td>
            <td>{{ employee.formatHireDate }}</td>
>>>>>>> .merge_file_FQLBl3
            <td>{{ employee.dependentsCount }}人</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import { Employee } from "@/types/employee";
/**
 * 従業員一覧を表示する画面.
 */
@Component
export default class EmployeeList extends Vue {
  // 従業員一覧
  private currentEmployeeList: Array<Employee> = [];
  // 従業員数
  private employeeCount = 0;
  //10件表示
  private page = 0;
  //表示する枚数
  private dispEmployeesCount = 10;
  private isStartPage = true;
  private isEndPage = false;


  /**
   * Vuexストアのアクション経由で非同期でWebAPIから従業員一覧を取得する.
   *
   * @remarks
   * Vueインスタンスが生成されたタイミングで
   * Vuexストア内のアクションを呼ぶ(ディスパッチする)。
   * ライフサイクルフックのcreatedイベント利用。
   *
   * 取得してからゲットするため、async awaitを利用している。
   */
  async created(): Promise<void> {
    if( this.$store.getters.getLogedInFrag === false ){
      this.$router.push("/loginAdmin");
    }
    await this.$store.dispatch("getEmployeeList");

    // 従業員一覧情報をVuexストアから取得
    // 非同期で外部APIから取得しているので、async/await使わないとGetterで取得できない
    // ページング機能実装のため最初の10件に絞り込み
    this.currentEmployeeList = this.$store.getters.getAllEmployees;
  }
  /**
   * 現在表示されている従業員一覧の数を返す.
   *
   * @returns 現在表示されている従業員一覧の数
   */
  get getEmployeeCount(): number {
    return this.currentEmployeeList.length;
  }
  ////////////////////////////////ここから下がページネーション処理///////////////////////////////////////
  /**
   * ページの始まりかどうか確認するメソッド.
   * @returns スタートかどうかを判断するFrag
   */
  startPageOrNot(): boolean{
    return this.page === 0;
  }
   /**
   * 最後の始まりかどうか確認するメソッド.
   * @remarks ページを求める剰余(this.page)を利用して、全件数よりも数が多くなったら最後のページと判断する。
   * @returns 最後のページかどうかを判断するFrag
   */
  endPageOrNot(): boolean{
    return (this.page + 1) * this.dispEmployeesCount >= this.currentEmployeeList.length;
  }
  /**
   * 前ページに戻るメソッド.
   */
  showPrev (): void{
    if(this.startPageOrNot()){
      this.isStartPage = true;
      return;
    }
    this.page--;
  }
  /**
   * 次のページに進むメソッド.
   */
  showNext (): void{
    if( this.endPageOrNot() ){
      this.isEndPage = true;
      return;
    }
    this.page++;
    this.isStartPage = false;
  }
  /**
   * 表示させるページのgetter.
   */
  get dispEmployees (): Array<Employee>{
    let startPage = this.page * this.dispEmployeesCount;
    return this.currentEmployeeList.slice(startPage, startPage + this.dispEmployeesCount)
  }
}
</script>

<style scoped>
.page-change-btn{
  color: green;
  margin: 0 10px;
  cursor: pointer;
}
.searchForm {
  margin-bottom: 20px;
  width: 450px;
  margin: 0 auto;
}

.searchBtn {
  display: block;
  width: 150px;
  margin: 0 auto;
}
</style>
