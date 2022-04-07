<template>
  <tr>
    <td>{{ name }}</td>
    <td>
      <input type="number" v-model="amount" />
    </td>
    <td>${{ price }}</td>
    <td>${{ price * amount }}</td>
    <td>
      <button class="btn btn-danger btn-sm" @click="removeItem(name)">
        <i class="fas fa-trash-alt"></i>
      </button>
    </td>
  </tr>
</template>

<script>
import { watch } from 'vue';

export default {
  props: {
    name: String,
    price: Number,
    amount: Number,
  },
  setup(props, { emit }) {

    const removeItem = () => {
      emit('remove');
    };

    watch(() => props.amount, val => {
      emit('update', {
        name: props.name,
        amount: val
      });

      if (val === 0) {
        removeItem();
      }
    })

    return {
      removeItem
    }
  }
}
</script>