<template>
  <form-select
    :chosen-option="chosenOption"
    label="Занятие на сегодня"
    placeholder="Ничего не выбрано"
    :options="options"
    @set-value="setValue"
  />
</template>

<script>
import FormSelect from './components/FormSelect.vue'

export default {
  name: 'App',

  components: {
    FormSelect
  },

  data() {
    return {
        chosenOption: null,
        options: [
        {
          id: 0,
          value: 'Читать твиттер',
        },
        {
          id: 1,
          value: 'Плакать',
        },
        {
          id: 2,
          value: 'Самообучение',
        },
        {
          id: 3,
          value: 'Созерцание природы',
        },
      ] 
    }
  },

  methods: {
    setValue(id) {
      this.chosenOption = this.options[id];
    }
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Inter', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: $black;
  margin-top: 60px;
}
</style>
