<template>
  <div class="about">
    <h1>This is an about page</h1>
  </div>
</template>

<script>
import * as d3 from "d3";
export default {
  name: "AboutView",
  created() {
    var test = d3.select("div.about");
    console.log("test", test);
  },
};
</script>
