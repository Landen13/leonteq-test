<template>
  <div class="filters">
    <div class="row">
      <template v-for="(value, key) in fields">
        <div class="input" :key="key">
          <label for="">{{ key }}</label>
          <input type="text" v-model="fields[key]">
        </div>
      </template>
    </div>
    <div class="row">
      <label>
        AND (&&)
        <input type="radio" value="every" name="filters-mode" v-model="mode">
      </label>
      <label>
        OR (||)
        <input type="radio" value="some" name="filters-mode" v-model="mode">
      </label>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    fields: {
      type: Object,
      required: true
    },
    mode: {
      type: String,
      validator: value => ['every', 'some'].includes(value),
      default: 'every'
    }
  },
  data () {
    return {
      modeLocal: this.mode,
      fieldsLocal: this.fields
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
