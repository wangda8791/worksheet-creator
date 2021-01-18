<template>
  <section>
    <h3>Please draw line between word and difinition that matches.</h3>
    <div v-for="item in shuffled" class="row" :key="item.id">
      <div class="col col-sm-4">
        {{ item.word }}
      </div>
      <div class="col col-sm-8">
        {{ item.definition }}
      </div>
    </div>
  </section>
</template>

<script>
import shuffle from "shuffle-array";

export default {
  name: "Worksheet",
  props: {
    data: Array
  },
  computed: {
    shuffled() {
      const words = shuffle(this.data.map(item => item.word));
      return words.map((word, index) => ({
        word,
        definition: this.data[index].definition
      }));
    }
  }
};
</script>

<style scoped lang="scss"></style>
