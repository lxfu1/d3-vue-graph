<template>
  <Graph msg="graph" />
</template>

<script>
// @ is an alias to /src
// import Graph from '@/components/graph-force-layout.vue';
// import Graph from '@/components/graph-radial-layout.vue';
import Graph from '@/components/graph-custom-position.vue';

export default {
  name: 'Home',
  components: {
    Graph,
  },
};
</script>
