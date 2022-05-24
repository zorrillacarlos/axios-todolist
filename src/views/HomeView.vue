<template>
  <div class="home container">
    <input
      type="text"
      class="form-control my-3"
      v-model="newTask"
      @keyup.enter="addTask"
    />
    <button class="btn btn-primary mx-3" @click="addTask">Agregar Tarea</button>

    <div class="mt-3" v-for="(item, index) in tasks" :key="item.id">
      <div
        role="alert"
        :class="item.state ? 'alert alert-success' : 'alert alert-danger'"
      >
        <div class="d-flex justify-content-between align-items-center">
          <div>{{ index + 1 }} - {{ item.name }} - {{ item.state ? 'Realizado' : 'No realizado' }}</div>
          <div>
            <button class="btn btn-success btn-sm mx-2" @click="editTask(item.id)"> OK </button>
            <button class="btn btn-danger btn-sm" @click="deleteTask(item)">X</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Axios from "axios";
const todolistURL = "http://localhost:3500/todolistado";

export default {
  name: "HomeView",
  data() {
    return {
      tasks: [],
      newTask: "",
    };
  },

  methods: {
    async addTask() {
      const add = await Axios.post(todolistURL, {
        name: this.newTask,
        state: false,
      });
      this.tasks = [...this.tasks, add.data];
      this.newTask = "";
    },

    async editTask(id) {
      await Axios.patch(`${todolistURL}/${id}`,{
          state: true,
        });

      this.tasks = this.tasks.map(element => {
        if (element.id === id) {
          element.name = element.name;
          element.state = true;
        }
        return element;
      });
    },

    async deleteTask (object){
      await Axios.delete (`${todolistURL}/${object.id}`)
      Axios.get (todolistURL).then (respuesta => this.tasks = respuesta.data)
    }

  }, // Termina Methods

  async created() {
    await Axios.get(todolistURL).then(
      (respuesta) => (this.tasks = respuesta.data)
    );
  },
};
</script>

<style></style>
