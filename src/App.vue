<template>
  <div class="hello">
    <div class="wrapper_content">
      <div class="content_scroll">
        <div class="content">
          <div class="content_header">
            <h2 class="content_ttl">Новая запись</h2>
            <div class="left_20">
              <div v-if="auth" class="alertGreen">
                Вы авторизованы <a href="#" @click="auth = !auth" class="item"> [Выйти]</a>
              </div>
              <div v-else class="alertRed">
                Вы неавторизованы <a href="#" @click="auth = !auth" class="item"> [Войти]</a>
              </div>
            </div>
          </div>
          <div class="infoUser">
            <div class="field" v-for="field in tableFields" :key="field">
              <template v-if="field !== 'action' && field !== 'summary'">
                <div class="lbl indent">{{ !!labels[field] ? labels[field] : field }}</div>
                <div class="fld">
                  <input type="text" class="input" v-model="newItem[field]" />
                </div>
                <div
                  class="clear"
                  :style="{ color: 'red' }"
                  v-if="newItem[field].length && !testString(newItem[field]) && !newItem.name"
                >
                  Только числовые
                </div>
              </template>
            </div>
            <div class="clear"></div>
            <a href="#" @click="appendRow()" class="btn_green top_10">Добавить</a>
          </div>
          <div class="content_header top_20">
            <h2 class="content_ttl">Таблица с данными</h2>
          </div>
          <div v-if="tableData.length">
            <app-table-new
              :data="tableData"
              :fields="tableFields"
              :fields_labels="labels"
              :no_hidden_empty_cols="true"
            >
              <template v-slot:field="props">
                  <div v-if="props.field === 'action'">
                    <a href="#" @click="removeRow(props.row.id)" v-if="props.row.name !== 'Сумма'">
                      Удалить
                    </a>
                    <a href="#" v-else></a>
                  </div>
              </template>
            </app-table-new>
          </div>
          <div v-else>Нет данных, необходимо добавить</div>
        </div>
      </div>
    </div>
  </div>
</template>

<style src="./style.css"></style>
<script>
import AppTableNew from "./components/AppTableNew.vue";

export default {
  name: "App",
  components: {
    AppTableNew,
    // InputBox,
  },
  data: () => {
    return {
      auth: false,
      labels: {
        id: "#",
        name: "Наименование",
        param1: "Параметр 1",
        param2: "Параметр 2",
        param3: "Параметр 3",
        action: "Действие",
      },
      tableFields: ["name", "param1", "param2", "param3", "action"],
      tableData: [],
      newItem: {
        id: 0,
        name: "",
        param1: "",
        param2: "",
        param3: "",
      },
      // regexp: /^\d+(?:[.,]\d{1,})?$/,
      // regexp: /^(0|[1-9]\d*)([.,]\d+)?/,
      regexp: /^[+-]?(?:\d+\.?\d*|\.\d+)$/,
      codeName: " asterisks",
      sum: 0,
      array: [],
    };
  },
  watch: {
    auth() {
      if (this.auth) {
        this.tableData.map((obj) => {
          obj.param2 = obj.param2.replace(this.codeName, "");
          obj.param3 = obj.param3.replace(this.codeName, "");
        });
      } else {
        this.tableData.map((obj) => {
          obj.param2 = `${obj.param2} ${this.codeName}`;
          obj.param3 = `${obj.param3} ${this.codeName}`;
        });
      }
    },
  },
  computed: {
    paramsArray() {
      return Object.values(this.newItem)
        .filter((item) => typeof item === "string")
        .slice(1);
    },
    isInputsValid() {
      return this.paramsArray.every((item) => item.length && this.regexp.test(item));
    },
    sumParam1() {
      return this.tableData.reduce((acc, curr) => acc + parseFloat(curr.param1), 0).toFixed(2);
    },
    sumParam2() {
      return this.tableData.reduce((acc, curr) => acc + parseFloat(curr.param2), 0).toFixed(2);
    },
    sumParam3() {
      return this.tableData.reduce((acc, curr) => acc + parseFloat(curr.param3), 0).toFixed(2);
    },
    sumArray() {
      return {
        id: 100,
        name: "Сумма",
        param1: this.sumParam1,
        param2: !this.auth ? this.sumParam2 + this.codeName : this.sumParam2,
        param3: !this.auth ? this.sumParam3 + this.codeName : this.sumParam3,
        action: "-"
      };
    },
    sumItemIndex() {
      return this.tableData.length;
    },
  },
  methods: {
    testString(text) {
      return this.regexp.test(text);
    },
    appendRow() {
      this.newItem.id = 1;
      if (this.tableData.length) {
        this.newItem.id =
          this.tableData.reduce((acc, curr) => (acc.id > curr.id ? acc : curr)).id + 1;
      }
      if (this.isInputsValid ) {
        this.tableData.pop();

        this.tableData.push({
          ...this.newItem,
          param1: Number(this.newItem.param1).toFixed(2),
          param2: !this.auth
            ? `${Number(this.newItem.param2).toFixed(2)} ${this.codeName}`
            : Number(this.newItem.param2).toFixed(2),
          param3: !this.auth
            ? `${Number(this.newItem.param3).toFixed(2)} ${this.codeName}`
            : Number(this.newItem.param3).toFixed(2),
        });

        this.newItem = {
          id: 0,
          name: "",
          param1: "",
          param2: "",
          param3: "",
        };

        this.tableData.push(this.sumArray);
      }
    },
    removeRow(id) {
      this.tableData = this.tableData.filter((item) => item.id !== id);
      this.tableData.pop();
      this.tableData.push(this.sumArray);
      if (this.tableData.length === 1) {
        this.tableData = []
      }
    },
  },
};
</script>
