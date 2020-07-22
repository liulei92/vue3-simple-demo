<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    {{ x + ':' + y }}
    <div>
      <input type="text" v-model="data.num1">
      <span>+</span>
      <input type="text" v-model="data.num2">
      <span>=</span>
      <button @click="add">Add</button>
    </div>
    <div>
      <span>add:{{ data.sum }}</span><br/>
      <span>computed:{{ data.result }}</span>
    </div>
    <div>
      <button @click="goAbout">about</button>
      <button @click="storeAdd">store.add</button>
    </div>
  </div>
</template>

<script>
import {
  ref, reactive, computed, watch, watchEffect, onMounted, onUnmounted,
} from 'vue';
import { useRoute, useRouter } from 'vue-router';
import { useStore } from 'vuex';

export default {
  name: 'HelloWorld',
  props: {
    msg: String,
  },
  setup(props, ctx) {
    console.log(props, ctx);
    const router = useRouter(); // 等于 this.$router
    console.log(useRoute().path); // 路径 等于 this.$route

    const store = useStore(); // 等于 this.$store

    const x = ref(0);
    const y = ref(0);

    const data = reactive({
      num1: 0,
      num2: 0,
      sum: 0,
      result: computed(() => parseInt(data.num1, 10) + parseInt(data.num2, 10)),
    });

    const add = () => {
      data.sum = parseInt(data.num1, 10) + parseInt(data.num2, 10);
      // 同時emit
      ctx.emit('send-msg', data.sum);
    };

    const update = (e) => {
      x.value = e.pageX;
      y.value = e.pageY;
    };

    const goAbout = () => {
      router.push('/about');
    };

    const storeAdd = () => {
      store.commit('add');
    };

    watch(() => data.sum, (value) => {
      console.log(value);
    });

    watchEffect(() => {
      console.log(`watchEffect: ${data.sum}`);
    });

    onMounted(() => {
      window.addEventListener('mousemove', update);
    });

    onUnmounted(() => {
      window.removeEventListener('mousemove', update);
    });

    return {
      x,
      y,
      data,
      add,
      goAbout,
      storeAdd,
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
