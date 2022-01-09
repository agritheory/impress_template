<template>
	<div
		class="slide"
		:style="conf.style"
		v-html="content"
	></div>
</template>
<style>
.slide{
  position:absolute;
  transform-origin:center;
  box-sizing:border-box;
}
</style>

<script>
export default {
  props: ['content', 'conf'],
	mounted(){
		console.log(this.$props.conf)
	}
}
</script>