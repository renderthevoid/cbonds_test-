<template>
    <div class="hello">

        <div class="wrapper_content">
            <div class="content_scroll">
                <div class="content">
                    <div class="content_header">
                        <h2 class="content_ttl">
                            Новая запись
                        </h2>
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
                            <template v-if="field !== 'action'">
                                <div class="lbl indent">{{ !!labels[field] ? labels[field] : field }}</div>
                                <div class="fld">
                                    <input type="text" class="input" v-model="newItem[field]"/>
                                </div>
                                <div class="clear"></div>
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
                                    <a href="#" @click="removeRow(props.row.id)">Удалить</a>
                                </div>
                            </template>
                        </app-table-new>
                    </div>
                    <div v-else>
                        Нет данных, необходимо добавить
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style src="./style.css"></style>
<script>
import AppTableNew from './components/AppTableNew.vue'

export default {
    name: 'App',
    components: {
        AppTableNew
    },
    data: () => {
        return {
            auth: false,
            labels: {
                id: '#',
                name: 'Наименование',
                param1: 'Параметр 1',
                param2: 'Параметр 2',
                param3: 'Параметр 3',
                action: 'Действие'
            },
            tableFields: ['name', 'param1', 'param2', 'param3', 'action'],
            tableData: [],
            newItem: {
                id: 0,
                name: '',
                param1: '',
                param2: '',
                param3: '',
            },
        }
    },
    methods: {
        appendRow: function () {
            this.newItem.id = 1;
            if (this.tableData.length) {
                this.newItem.id = this.tableData.reduce((acc, curr) => acc.id > curr.id ? acc : curr).id + 1;
            }
            this.tableData.push(this.newItem);
        },
        removeRow: function (id) {
            console.log('Удаление строки #' + id);
            // необходимо реализовать
        },
    },
}

</script>

