<template>
  <transition name="fade">
    <tr v-if="flow">
      <td class="has-text-centered">
        <span>{{ amount }} {{ currency }}</span>
      </td>

      <td class="has-text-centered">
        <i class="fa fa-arrow-right has-text-danger" v-if="flow.type === 'cost'" />
        <i class="fa fa-arrow-left has-text-success" v-else/>
      </td>

      <td class="has-text-centered">
        <span>{{ this.flow.description }} </span>
      </td>
      
      <td class="has-text-centered">
        <button class="button is-danger is-small" @click="removeRow(index)">
          <i class="fa fa-trash" />
        </button>
      </td>
    </tr>
  </transition>
</template>

<script>
export default {

  // Pretty much a model class 
  props: {
    currency: {
      type: String,
      required: true,
    },
     description: {
      type: String,
      required: false,
    },
   
    index: {
      type: Number,
      required: true,
    },
    removeRow: {
      type: Function,
      required: true,
    },
    flow: {
      type: Object,
      required: true
    }
  },
  
  computed:{
    amount() {
      let price = this.flow.amount.toFixed(2);

      return price.replace('.', ',');
    }
    
  }
}
</script>

<style lang="scss" scoped>
  .fade {
    &-enter {
      opacity: 0;

      &-active {
        transition: opacity 1s;
      }
    }
  }
</style>
