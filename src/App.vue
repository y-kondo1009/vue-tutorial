<script setup>
import { reactive, ref, computed, onMounted, watch } from 'vue'
import ChildComp from './components/ChildComp.vue'
import SlotComp from './components/SlotComp.vue'

/* 宣言的レンダリング */
//reactive()APIを利用しリアクティブな状態を宣言可能（リアクティブ->値が監視され変更が検知される。）
const counter = reactive({ count: 0 })

console.log(counter.count) // 0
counter.count++ //最終的に1がレンダリングされる。
const message = ref('Hello World!')
console.log(message.value) // "Hello World!"
message.value = 'Changed' //最終的にChangedがレンダリングされる。

/* 属性バインディング */
const dynamicId = ref('id-001')
const dynamicClass = ref('cl-001')
const redClass = ref('redColor')


/* イベントリスナー */
const count = ref(0)
function increment () {
  count.value++
}

/* フォームバインディング */
const text = ref('')
const text2 = ref('')
function onInput (e) {
  text.value = e.target.value
}

/* 条件付きレンダリング */
const isHappy = ref(true)
function toggle () {
  isHappy.value = !isHappy.value
}

/* リストレンダリング */
let id = 0
const newTodo = ref('')
const hideCompleted = ref(false)
const todos = ref([
  { id: id++, text: 'Learn HTML', done: true },
  { id: id++, text: 'Learn JavaScript', done: true },
  { id: id++, text: 'Learn Vue', done: false }
])
// computedプロパティーを利用することによって、他のリアクティブな値を利用し.valueを返却するrefを作成可能
const filteredTodos = computed(() => {
  return hideCompleted.value
    ? todos.value.filter((t) => !t.done)
    : todos.value
})
function addTodo () {
  todos.value.push({ id: id++, text: newTodo.value })
  newTodo.value = ''
}
function removeTodo (todo) {
  todos.value = todos.value.filter((t) => t !== todo)
}

/* ライフサイクルとテンプレート参照 */
const pElementRef = ref(null)
onMounted(() => {
  pElementRef.value.textContent = 'マウント完了。'
})

/* ウォッチャー */
const todoId = ref(1)
const todoData = ref(null)
async function fetchData () {
  todoData.value = null
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/posts/${todoId.value}`
  )
  todoData.value = await res.json()
}
fetchData()
// watch関数は、第1引数に与えられた要素に変更が加えられるたびにコールバック関数を呼び出すことができる。
watch(todoId, fetchData)

/* コンポーネント */
const sendChildMsg = ref('')
const receivedChildMsg = ref('No child msg yet')
</script>

<template>
  <h1>Reactive</h1>
  <!-- mustaches構文にてリアクティブステートの利用可能-->
  <p>Count is: {{ counter.count }}</p>
  <p>{{ message }}</p>

  <!-- mustachesはJSの式であれば変数以外も使用可能-->
  <p>{{ message.split('').reverse().join(' ') }}</p>
  <hr>

  <h1>属性バインディング</h1>
  <!-- 前述のmustachesは値のみに使用。属性を動的に設定したい場合はv-bindディレクティブを使う。-->
  <p v-bind:id="dynamicId">Val1</p>
  <p v-bind:class="dynamicClass">Val2</p>
  <!-- v-bindは省略記法あり。-->
  <p :class="dynamicClass">Val3</p>
  <p :class="redClass">Val4</p>
  <hr>


  <h1>イベントリスナー</h1>
  <p>count->{{ count }}</p>
  <!-- v-onディレクティブにてDOMイベントの利用が可能。-->
  <button v-on:click="increment">BUTTON-A</button>
  <!-- v-onは省略記法あり。-->
  <button @click="increment">BUTTON-B</button>
  <hr>


  <h1>フォームバインディング</h1>
  <p>text->{{ text }}</p>
  <!-- v-bind と v-on を一緒に使うことで、双方向バインディング作成可能-->
  <input :value="text" @input="onInput">

  <p>text2->{{ text2 }}</p>
  <!--v-modelを利用するとイベントハンドラなしで双方向バインディングが作成できる。-->
  <input v-model="text2">
  <hr>


  <h1>条件付きレンダリング</h1>
  <button @click="toggle">Toggle</button>
  <!--v-ifにて表示の有無の制御が可能-->
  <p v-if="isHappy">🎊🎊😀🎊🎊🎊</p>
  <p v-else>😭😭😭😭😭😭</p>
  <hr>


  <h1>リストレンダリング</h1>
  <form @submit.prevent="addTodo">
    <input v-model="newTodo" required placeholder="new todo">
    <button>Add Todo</button>
  </form>
  <!--v-forにて配列をレンダリングすることができる-->
  <ul>
    <li v-for="todo in filteredTodos" :key="todo.id">
      <input type="checkbox" v-model="todo.done">
      <span :class="{ done: todo.done }">{{ todo.text }}</span>
      <button @click="removeTodo(todo)">X</button>
    </li>
  </ul>
  <button @click="hideCompleted = !hideCompleted">
    {{ hideCompleted ? 'Show all' : 'Hide completed' }}
  </button>
  <hr>

  <h1>ライフサイクルとテンプレート参照</h1>
  <!--ref属性を設定することにより、テンプレート参照が要求可能。
    今回はonMounted関数によりコンポーネントマウント後にテキストの書き換えを行なっている。-->
  <p ref="pElementRef">hello!!!!表示されないこの値。</p>
  <hr>


  <h1>ウォッチャー</h1>
  <p>Todo id: {{ todoId }}</p>
  <button @click="todoId++" :disabled="!todoData">Fetch next todo</button>
  <p v-if="!todoData">Loading...</p>
  <pre v-else>{{ todoData }}</pre>
  <hr>

  <h1>コンポーネント</h1>
  <input v-model="sendChildMsg">
  <!-- 他ファイルをインポートすることによって下記のように呼び出してインクルード可能 -->
  <!-- propsを子ファイルに定義すれば引数を渡すこともできる。 -->
  <ChildComp :msg="sendChildMsg" @response="(msg) => receivedChildMsg = msg" />
  <p>{{ receivedChildMsg }}</p>
  <hr>


  <h1>スロット</h1>
  <!-- 子ファイルにてslotタグを設定すれば、要素をそのまま渡すことができる。 -->
  <SlotComp>
    <p>ZUDON</p>
  </SlotComp>
  <SlotComp></SlotComp>
  <hr>

</template>

<style>
.redColor {
  color: red;
}

.done {
  text-decoration: line-through;
}
</style>