<template>
  <li class="node-tree">
    <nuxt-link :to="`/categories/${node.slug}-${node.id}/`">
    	<span class="name">{{ node.name }}</span>
    </nuxt-link>
    	<ul v-if="node.children" >
      		<node v-for="child in node.children" :node="child"></node>
    	</ul>
  </li>
</template>

<script>
export default {
  name: "node",
  props: {
    node: Object
  }
};
</script>