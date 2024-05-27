<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <p>↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓</p>
  <!-- Slotタグは親から渡されたコンテンツが表示される。 -->
  <slot>
    <!-- Slotタグで囲った要素はコンテンツが親から渡されなかった時に表示される。 -->
    <p>コンテンツがない場合はこれが表示される</p>
  </slot>
  <p>↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑</p>
</template>