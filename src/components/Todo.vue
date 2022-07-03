<script setup>
import { ref } from "@vue/reactivity";
import { v4 as uuid } from "uuid";
import { onMounted, watch } from "@vue/runtime-core";
import draggable from "vuedraggable";
const text = ref("");
const notify = ref("");

const todos = ref([
  {
    id: uuid(),
    text: "text.value",
    done: false,
    date: new Date().toUTCString().slice(0, -7),
    notify: "",
  },
]);
const addTodo = () => {
  if (text.value !== "" && notify.value !== "") {
    todos.value.unshift({
      id: uuid(),
      text: text.value,
      done: false,
      date: new Date().toUTCString().slice(0, -7),
      notify: notify.value,
    });
    console.log(notify.value);
    text.value = "";
  }
};

watch(
  todos,
  (newValue) => {
    localStorage.setItem("tasks", JSON.stringify(newValue));
  },
  { deep: true }
);

onMounted(() => {
  todos.value = JSON.parse(localStorage.getItem("tasks") || []);
  if (todos.value.length != 0) {
    let date = new Date().toTimeString().slice(0, -40);
    if (date == todos.value[0].notify) {
      new Notification("Todo", {
        body: todos.value[0].text,
      });
    }
  }
});

const deleteTodo = (id) => {
  todos.value = todos.value.filter((text) => text.id !== id);
};
</script>

<template>
  <header>
    <div class="">Todo App</div>
  </header>
  <main>
    <form @submit.prevent="addTodo">
      <input v-model="text" type="text" placeholder="What do you want to do" />
      <input id="time" v-model="notify" type="time" />
      <input type="submit" value="Add Task" />
    </form>
    <draggable class="card_container" v-model="todos" item-key="id">
      <template #item="{ element }">
        <div>
          <div class="card" :class="[{ done: element.done }, ``]">
            <div>
              <small>{{ element.date }}</small>
            </div>
            <div>
              <small class="text-[1.8rem]"
                >Time to remind me <b>{{ element.notify }}</b>
              </small>
            </div>
            <div>
              <label :for="element.text">
                {{ element.text }}
              </label>
              <input
                type="checkbox"
                :id="element.text"
                v-model="element.done"
              />
            </div>
            <button @click="deleteTodo(element.id)">delete</button>
          </div>
        </div>
      </template>
    </draggable>
  </main>
</template>

<style scoped>
header {
  font-size: 30px;
  color: rgb(224, 216, 223);
  font-weight: 700;
}
form {
  width: 100%;
  max-width: 680px;
  display: flex;
  gap: 1rem;
  margin: 1rem auto;
}
form input[type="text"],
form input[type="time"] {
  width: 100%;
  max-width: 500px;
  background: rgb(43, 30, 41);
  outline: none;
  padding: 0.5rem;
  border-radius: 0.3rem;
  color: rgb(224, 216, 223);
}
form input[type="submit"] {
  width: 30%;
  background: rgb(0, 141, 12);
  border-radius: 0.3rem;
  padding: 0.5rem;
  max-width: 500px;
  color: rgb(224, 216, 223);
  font-weight: 700;
}
.card {
  background: rgb(43, 30, 41);
  padding: 2rem;
  display: flex;
  flex-direction: column;
  margin-bottom: 1rem;
  border-radius: 0.3rem;
  color: rgb(225, 218, 223);
  gap: 0.5rem;
}
.card button {
  background: red;
  padding: 0.5rem;
  border-radius: 0.3rem;
}
.card label {
  font-size: 22px;
  text-transform: capitalize;
  font-weight: 600;
  cursor: pointer;
}

.done label {
  text-decoration: line-through;
}
input[type="checkbox"] {
  display: none;
}

@media screen and (min-width: 680px) {
  .card_container {
    display: grid;
    gap: 1rem;
    grid-template-columns: repeat(3, minmax(100px, 1fr));
  }
  .card {
    /* max-width: 360px; */
    width: 100%;
    /* max-height: 380px; */
    height: 100%;
  }
}
</style>
