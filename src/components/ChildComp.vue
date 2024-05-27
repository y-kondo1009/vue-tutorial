<!-- eslint-disable vue/multi-word-component-names -->
<script setup>
const props = defineProps({
  msg: String
})
const emit = defineEmits(['response'])
emit('response', '子からの贈り物')
</script>
<template>
  <p>これは別ファイルに書かれています。</p>
  <p>親から渡されたmsg->{{ msg || 'メッセージなし' }}</p>
</template>