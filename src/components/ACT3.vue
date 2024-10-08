<template>
    <v-app id="app" class="wrapper">
      <v-app-bar app class="header">
        <v-toolbar-title class="text-center title-text">
          <v-icon left class="title-icon">mdi-format-list-checks</v-icon>
          TO-DO LIST
        </v-toolbar-title>
      </v-app-bar>
     
  
      <v-container class="my-15">
        <v-card class="custom-card-margin mt-15" elevation="8" outlined>
    <v-card-title>
      <v-icon color="primary" class="mr-2">mdi-plus-box-multiple</v-icon>
      <b>Add New Task</b>
    </v-card-title>
  
    <v-card-text>
      <v-form>
        <v-text-field
          v-model="newTask"
          label="Task Name"
          prepend-icon="mdi-format-list-bulleted"
        ></v-text-field>
  
        <v-text-field
          v-model="newTaskDescription"
          label="Task Description"
          prepend-icon="mdi-text"
        ></v-text-field>
  
        <v-row no-gutters>
    <v-col class="d-flex mt-2" cols="auto">
      <v-btn color="primary" x-small @click="addTask" class="mr-2">
        <v-icon>mdi-plus</v-icon>
        Add Task
      </v-btn>
    </v-col>
    <v-col class="d-flex mt-2" cols="auto">
      <v-btn color="red" x-small @click="clearHistory()">
        <v-icon>mdi-trash-can</v-icon>
        Clear lists
      </v-btn>
    </v-col>
  </v-row>
  
  
      </v-form>
    </v-card-text>
  </v-card>
  
  
        <v-divider class="mt-10"></v-divider>
        <v-row>
          <v-col cols="12" md="4" v-for="(task, index) in taskList" :key="index">
            <div v-if="task.status === 'to do'">
              <v-card class="rounded-shaped" :style="{ backgroundColor: task.color }">
                <v-card-title>
                  <v-list-item v-if="task.date && task.time" class="text-subtitle-1">
                    <v-icon color="primary mx-2">mdi-clock-time-eight-outline</v-icon>
                    {{ task.date }} {{ task.time }}
                  </v-list-item>
                  <v-divider :thickness="2"></v-divider>
                  <br>
                  <v-list-item v-if="!task.editable">{{ task.title }}</v-list-item>
                  <v-text-field v-model="task.title" v-else label="Task Name"></v-text-field>
                  <v-list-item class="text-subtitle-1" v-if="!task.editable">{{ task.description }}</v-list-item>
                  <v-text-field v-model="task.description" v-else label="Task Description"></v-text-field>
                </v-card-title>
  
                <v-card-actions class="justify-end text-right">
                  <v-icon color="red" @click="deleteTask(index)">mdi-delete</v-icon>
                  <v-icon color="primary" v-if="!task.editable" @click="editTask(index)">mdi-pencil</v-icon>
                  <v-icon color="primary" v-else @click="updateTask(index)">mdi-check</v-icon>
  
                  <v-dialog v-model="task.colorPickerDialog" width="auto">
                    <template v-slot:activator="{ props }">
                      <v-card-actions color="primary px-0" v-bind="props" @click="task.colorPickerDialog = true">
                        <v-icon color="orange-darken-2" size="25">mdi-palette</v-icon>
                      </v-card-actions>
                    </template>
                    <v-card>
                      <v-card-text>
                        <v-color-picker v-model="task.color"></v-color-picker>
                      </v-card-text>
                      <v-card-actions>
                        <v-btn color="primary" block @click="task.colorPickerDialog = false, updateTask(index)">Save changes</v-btn>
                      </v-card-actions>
                    </v-card>
                  </v-dialog>
                </v-card-actions>
              </v-card>
            </div>
          </v-col>
        </v-row>
      </v-container>
    </v-app>
  </template>
  
  <script>
  export default {
    data() {
      return {
        taskList: this.getData(),
        newTask: '',
        newTaskDescription: ''
      };
    },
    methods: {
      clearHistory() {
        localStorage.clear();
        location.reload();
      },
      getData() {
        const data = localStorage.getItem('todoList');
        return JSON.parse(data) || [];
      },
      saveData(data) {
        localStorage.setItem('todoList', JSON.stringify(data));
      },
      addTask() {
        if (this.newTask !== "") {
          const currentDate = new Date();
          this.taskList.push({
            title: this.newTask,
            description: this.newTaskDescription,
            done: false,
            color: "#ffffff",
            colorPickerDialog: false,
            status: "to do",
            date: `${currentDate.getDate()}/${currentDate.getMonth() + 1}/${currentDate.getFullYear()}`,
            time: `${currentDate.getHours()}:${currentDate.getMinutes()}`
          });
          this.newTask = '';
          this.newTaskDescription = '';
          this.saveData(this.taskList);
        } else {
          alert('Please write a task');
        }
      },
      deleteTask(index) {
        this.taskList.splice(index, 1);
        this.saveData(this.taskList);
      },
      editTask(index) {
        this.taskList[index].editable = true;
      },
      updateTask(index) {
        this.taskList[index].editable = false;
        this.saveData(this.taskList);
      }
    }
  };
  </script>
  
  <style scoped>
  .waitlist, .progress, .done {
    color: white;
    border-radius: 10px;
    margin-bottom: 15px;
  }
  
  .custom-card-margin {
    margin-top: 30px;
  }
  
  @media (max-width: 600px) {
    .v-toolbar-title {
      font-size: 18px;
    }
    .v-btn {
      font-size: 14px;
    }
    .v-card-title {
      font-size: 16px;
    }
    .v-list-item {
      font-size: 14px;
    }
    .waitlist, .progress, .done {
      text-align: center;
    }
  }
  .header {
    background-color: gray;
    color: white; /* Text color */
    height: 64px; /* Adjust height if needed */
  }
  
  .title-text {
    font-size: 24px; /* Adjust font size */
    font-weight: bold; /* Make text bold */
    letter-spacing: 1.5px; /* Optional: Add letter spacing for a more refined look */
  }
  .wrapper{
    background-color: #c8b6a6;
  }
  
  </style>
  