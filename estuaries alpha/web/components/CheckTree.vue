<template>
  <li class="node-checktree">
  <input type="checkbox">
       <span class="name">{{ node.name }}</span>
      <ul v-if="node.children" >
        <input type="checkbox">
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