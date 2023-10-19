<template lang="">
  <div class="infoUser">
    <div class="field" v-for="field in fields" :key="field">
      <template v-if="field !== 'action'">
        <div class="lbl indent">{{ !!labels[field] ? labels[field] : field }}</div>
        <div class="fld">
          <input type="text" class="input" v-model="newItem[field]" />
        </div>
        <div class="clear"></div>
      </template>
    </div>
    <div class="clear"></div>
    <a href="#" @click="appendRow()" class="btn_green top_10">Добавить</a>
  </div>
</template>
<script>
export default {
  name: "InputBox",
  data() {
    return {
      newItem: {
        id: 0,
        name: "",
        param1: "",
        param2: "",
        param3: "",
      },
    };
  },
  props: {
    fields: {
      type: Array,
      required: true,
    },
    labels: {
      type: Object,
      required: true,
    },
  },
};
</script>
<style lang=""></style>
