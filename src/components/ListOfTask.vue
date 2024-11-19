<script setup lang="ts">
import { ref, computed } from "vue";

interface Task {
  id: number;
  text: string;
  status: "Обычная" | "Срочная" | "Выполненная";
}

let taskId = 0;

const newTask = ref<string>("");
const tasks = ref<Task[]>([]);
const editingIndex = ref<number | null>(null);
const editedTask = ref<string>("");
const filter = ref<string>("Все");

const filters = ["Все", "Обычная", "Срочная", "Выполненная"];

const addTask = () => {
  const trimmedTask = newTask.value.trim();
  if (trimmedTask && !tasks.value.some((task) => task.text === trimmedTask)) {
    tasks.value.push({ id: taskId++, text: trimmedTask, status: "Обычная" });
    newTask.value = "";
  }
};

const removeTask = (index: number) => {
  tasks.value.splice(index, 1);
};

const startEditing = (index: number) => {
  editingIndex.value = index;
  editedTask.value = tasks.value[index].text;
};

const saveTask = (index: number) => {
  if (editedTask.value.trim()) {
    tasks.value[index].text = editedTask.value.trim();
  }
  cancelEditing();
};

const cancelEditing = () => {
  editingIndex.value = null;
  editedTask.value = "";
};

const updateTaskStatus = (index: number, event: Event) => {
  const target = event.target as HTMLSelectElement;
  if (target) {
    tasks.value[index].status = target.value as Task["status"];
  }
};

const filteredTasks = computed(() =>
  filter.value === "Все"
    ? tasks.value
    : tasks.value.filter((task) => task.status === filter.value)
);
</script>

<template>
  <div>
    <h1>Список задач</h1>

    <div>
      <input v-model.trim="newTask" type="text" placeholder="Введите текст" />
      <button type="button" :disabled="!newTask.trim()" @click="addTask">
        Добавить задачу
      </button>
    </div>

    <div>
      <button
        v-for="item in filters"
        :key="item"
        type="button"
        :class="[{ active: filter === item }]"
        @click="filter = item"
      >
        {{ item }}
      </button>
    </div>

    <p v-if="tasks.length === 0">Список задач пуст</p>

    <ul>
      <li v-for="(task, index) in filteredTasks" :key="task.id">
        <template v-if="editingIndex === index">
          <input
            v-model="editedTask"
            type="text"
            placeholder="Редактируйте задачу"
          />
          <button type="button" @click="saveTask(index)">Сохранить</button>
          <button type="button" @click="cancelEditing">Отменить</button>
        </template>
        <template v-else>
          <span>{{ task.text }}</span>
          <select @change="updateTaskStatus(index, $event)">
            <option value="Обычная">Обычная</option>
            <option value="Срочная">Срочная</option>
            <option value="Выполненная">Выполненная</option>
          </select>
          <button type="button" @click="startEditing(index)">
            Редактировать
          </button>
          <button type="button" @click="removeTask(index)">Удалить</button>
        </template>
      </li>
    </ul>

    <p v-if="filteredTasks.length === 0">Нет задач для отображения</p>
  </div>
</template>

<style lang="scss" scoped>
$primary-color: #ff6f61;
$secondary-color: #4a90e2;
$background-color: #f9f9f9;
$button-hover: darken($primary-color, 10%);
$input-border: lighten($secondary-color, 30%);
$box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);

body {
  font-family: "Arial", sans-serif;
  background-color: $background-color;
  color: #333;
  margin: 0;
  padding: 0;
  line-height: 1.6;
}

h1 {
  text-align: center;
  color: $primary-color;
  margin: 20px 0;
  font-size: 2rem;
}

div {
  max-width: 800px;
  margin: 20px auto;
  padding: 20px;
  background: #fff;
  border-radius: 8px;
  box-shadow: $box-shadow;
}

input[type="text"] {
  width: calc(100% - 140px);
  padding: 10px;
  margin-right: 10px;
  border: 2px solid $input-border;
  border-radius: 4px;
  font-size: 1rem;
  outline: none;
  transition: border-color 0.3s;

  &:focus {
    border-color: $primary-color;
  }
}

button {
  padding: 10px 20px;
  margin: 5px;
  border: none;
  border-radius: 4px;
  background: $primary-color;
  color: #fff;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.3s;

  &:hover {
    background-color: $button-hover;
  }

  &:disabled {
    background: lighten($primary-color, 20%);
    cursor: not-allowed;
  }

  &.active {
    background: $secondary-color;
  }
}

ul {
  list-style: none;
  padding: 0;

  li {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 15px;
    margin: 10px 0;
    border: 1px solid $input-border;
    border-radius: 4px;
    box-shadow: $box-shadow;

    span {
      flex: 1;
      margin-right: 10px;
      font-size: 1rem;
      color: #333;
    }

    select {
      padding: 5px 10px;
      border: 1px solid $input-border;
      border-radius: 4px;
      background: #fff;
      font-size: 1rem;
      cursor: pointer;

      &:focus {
        outline: none;
        border-color: $secondary-color;
      }
    }
  }
}

p {
  text-align: center;
  font-size: 1.2rem;
  color: $secondary-color;
  margin: 20px 0;
}

div > div {
  margin-bottom: 20px;
}
</style>
