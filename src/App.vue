<template>
  <div class="bg-slate-100 min-h-screen">
    <base-tab v-model="currentTab" :tabs="['Tareas', 'Productos']" />

    <template v-if="currentTab === 0">
      <TableModel endpoint="tasks" :fields="[
        {
          label: 'Completado',
          key: 'status',
          type: 'checkbox'
        },
        {
          label: 'Descripcion',
          key: 'description',
          type: 'text'
        }
      ]" />
    </template>


    <template v-if="currentTab === 1">
      <TableModel endpoint="products" :fields="[
        {
          label: 'Nombre',
          key: 'name',
          type: 'text'
        },
        {
          label: 'Precio',
          key: 'price',
          type: 'text'
        },
      
      ]" />
    </template>
    <!-- <div class="grid grid-cols-2 py-20">
      <div class="flex justify-center">
        <input type="checkbox" v-model="form.status">
        <input type="text" v-model="form.description" class="w-72 ml-6">
        <button @click="handleStore" class="bg-blue-800 px-5 py-2 text-white">enviar</button>
      </div>

      
    </div> -->
    <!-- <div class="grid grid-cols-1 px-10">
      <div class="grid grid-cols-2 gap-x-8">
        <div v-for="(user, index)  in users" :key="index" class="">
          <div class=" bg-stone-50 shadow-xl w-full h-auto m-4 p-10 text-base grid grid-cols-12">
            <div class="col-span-10">
              <h1>Primer Nombre: {{user.name.first}}</h1>
              <h1>Segundo Nombre: {{user.name.last}}</h1>
              <h1>Email: {{user.email}}</h1>
            </div>
            <div class="col-span-2">
              <button>editar</button>
            </div>

          </div>
        </div>
      </div>

      <div class="bg-stone-50 shadow-xl w-full h-auto m-4 p-10 text-base">
        <table class="w-full text-center">
          <tr>
            <th v-for="(tableTh, index) in tableThs" :key="index" class="py-8">{{ tableTh }}</th>

          </tr>
          <tr v-for="(task, index)  in tasks" :key="task.id">
            <td>
              <input type="checkbox" @change="({ target: { checked } }) => task.status = checked"
                :checked="task.status">
            </td>
            <td v-if="edit?.id === task.id"><input type="text" v-model="task.description"></td>
            <td v-else class="capitalize">{{ task.description }}</td>
            <td>
              <button v-if="edit?.id === task.id" @click="handleUpdate(task)">Guardar</button>
              <button v-else @click="handleEdit(task)">Editar</button>
              <button @click="handleDelete(task)" class="px-4">Eliminar</button>
            </td>

          </tr>
        </table>
      </div>
    </div> -->
  </div>
</template>

<script>
import BaseTab from '@/components/BaseTab.vue'
import TableModel from '@/components/TableModel.vue'

export default {
  components: {
    BaseTab,
    TableModel
  },
  data() {
    return {
      currentTab: 0,
      // nextPageUrl: null,
      // perPage: 5,
      // totalPage: 1,
      // currentPage: 1,
      // edit: null,
      // search: '',
      // tableThs: ['Estado', 'Descripcion', 'Opciones'],
      // paginates: ['1', '2', '3', '4', '5'],
      // users: [],
      // tasks: [],
      // form: {
      //   status: false,
      //   description: ''
      // },
      // usersUrl: 'https://randomuser.me/api?results=10',
      // tasksUrl: 'https://tasks.local/api/tasks',
      // headers: {
      //   'Content-Type': 'application/json'
      // },
    }

  },

  mounted() {

  },

  // methods: {
  //   async fetch(url, options = {}) {
  //     return await fetch(url, options).then(response => response.json()).catch(console.log)
  //   },


  //   // async fetchUsers() {
  //   //   const data = await fetch('https://randomuser.me/api?results=10').then(response => response.json()).catch(console.log)
  //   //   this.users = data.results
  //   //   console.log(this.users)
  //   // },

  //   async fetchUsers() {
  //     const data = await this.fetch(this.usersUrl)
  //     this.users = data.results
  //     console.log(this.users)
  //   },

  //   async handleSearch() {
  //     this.fetchTasks()
  //   },


  //   async fetchTasks() {
  //     let url = new URL(this.tasksUrl)
  //     url.search = new URLSearchParams({
  //       page: this.currentPage,
  //       search: this.search
  //     }).toString();

  //     const response = await this.fetch(url)

  //     this.tasks = response.data

  //     this.perPage = response.per_page
  //     this.totalPage = response.last_page
  //     this.nextPageUrl = response.next_page_url

  //     console.log(this.tasks)
  //     console.log(response)
  //   },


  //   async handleStore() {
  //     await this.fetch(this.tasksUrl, {
  //       method: 'POST',
  //       headers: this.headers,
  //       body: JSON.stringify(this.form)
  //     })
  //       .finally(() => {
  //         this.fetchTasks()
  //       })
  //   },

  //   handleEdit(task) {
  //     this.edit = task
  //     console.log(this.edit)
  //   },

  //   async handleUpdate(task) {

  //     await this.fetch(this.tasksUrl + '/' + task.id, {
  //       method: 'PUT',
  //       headers: this.headers,
  //       body: JSON.stringify(task)
  //     })
  //       .finally(() => {
  //         this.fetchTasks()
  //       })
  //     this.edit = null
  //   },

  //   async handleDelete(task) {
  //     await this.fetch(this.tasksUrl + '/' + task.id, {
  //       method: 'DELETE',
  //       headers: this.headers,
  //     })
  //       .finally(() => {
  //         this.fetchTasks()
  //       })
  //   },

  //   handlePrevious() {
  //     if (this.currentPage > 1) {
  //       this.currentPage--;
  //     }
  //     console.log(this.currentPage)
  //   },

  //   handleNext() {
  //     if (this.currentPage >= this.currentPage && this.nextPageUrl != null) {
  //       this.currentPage++;
  //       console.log(this.perPage)
  //     }

  //     console.log(this.nextPageUrl)
  //     console.log("hola soy el total", this.totalPage)

  //   },

  //   handleSetCurrentPage(page) {
  //     this.currentPage = page
  //   }

  // },

  // watch: {
  //   currentPage() {
  //     this.fetchTasks()
  //   }
  // }

}
</script>
  
