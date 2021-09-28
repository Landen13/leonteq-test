<template>
  <div class="pager" v-if="pagesQty">
    <button
      class="pager__button prev"
      :disabled="!showPrevBtn"
      @click="currentPage -= 1"
    >&lt;</button>
    <ul class="pager__pages">
      <li v-for="page in pagesQty" :key="page">
        <button
          class="pager__button"
          :disabled="page - 1 === currentPage"
          @click="currentPage = page - 1"
        >{{ page }}</button>
      </li>
    </ul>
    <button
      class="pager__button next"
      :disabled="!showNextBtn"
      @click="currentPage += 1"
    >&gt;</button>
  </div>
</template>

<script>
export default {
  name: 'Pager',
  props: {
    perPage: {
      type: Number,
      default: 20
    },
    totalItems: {
      type: Number,
      required: true
    },
    initialPage: {
      type: Number,
      default: 0
    }
  },
  data () {
    return {
      currentPage: this.initialPage
    }
  },
  computed: {
    pagesQty () {
      return Math.ceil(this.totalItems / this.perPage)
    },
    showPrevBtn () { return this.currentPage > 0 },
    showNextBtn () { return this.currentPage < this.pagesQty - 1 }
  },
  watch: {
    currentPage (val) {
      this.$emit('goto', val)
    },
    initialPage (val) {
      this.currentPage = val
    }
  }
}
</script>

<style lang="scss" scoped>
.pager {
  padding: 20px;
  display: flex;
  background-color: #f2f2f2;

  &__button {
    display: inline-block;
    vertical-align: middle;
    cursor: pointer;
    border: 1px solid #ccc;
    border-radius: 4px;
    height: 30px;
    font-size: 14px;
    line-height: 19px;
    text-align: center;
    background-color: #eaeaea;
    min-width: 30px;
    padding: 0 3px;

    margin: 0 5px;

    &:first-child {margin-left: 0;}
    &:last-child {margin-right: 0;}

    &:disabled {
      opacity: 0.5;
      cursor: default;
    }
    &:hover:not(:disabled):not(:active) {
      background-color: #efeded;
      border-color: #aaa;
    }
    &:active:not(:disabled) {
      position: relative;
      top: 2px;
      background-color: #ccc;
    }
  }

  &__pages {
    list-style: none;
    padding: 0;
    margin: 0;
    display: flex;
    & > * {
      margin: 0 3px;
      &:first-child { margin-left: 0; }
      &:last-child { margin-right: 0; }
    }
  }
}
</style>
