<template>
  <div class="flex justify-center gap-52 pt-20 ">
    <div class="flex justify-center pt-10">
      <input v-model="search" @input="handleSearch" type="text" class="w-72 ml-6">
      <button @click="handleSearch" class="bg-blue-800 px-5 py-2 text-white">Buscar</button>
    </div>

    <!-- <div class="flex justify-center">
      <input type="checkbox" v-model="form.status">
      <input type="text" v-model="form.description" class="w-72 ml-6">
      <button @click="handleStore" class="bg-blue-800 px-5 py-2 text-white">enviar</button>
    </div> -->
  </div>
  <button @click="handleAdd()" class="absolute bottom-4 right-4 bg-blue-800 border rounded-full p-8">
    <IconPlus class="text-white h-12" />
  </button>
  <div class="bg-stone-50 shadow-xl w-full h-auto m-4 p-10 text-base relative">

    <table class="w-full text-center">
      <!-- <tr>
        <th class="py-8">
          <slot></slot>
        </th>

      </tr>
      <tr v-for="(model, index)  in models" :key="model.id">
        <td>
          <input type="checkbox" @change="({ target: { checked } }) => task.status = checked" :checked="task.status">
        </td>
        <td v-if="modelToBeUpdated?.id === task.id"><input type="text" v-model="task.description"></td>
        <td v-else class="capitalize">{{ task.description }}</td>
        <td>
          <button v-if="modelToBeUpdated?.id === model.id" @click="handleUpdate(model)">Guardar</button>
          <button v-else @click="handleEdit(model)">Editar</button>
          <button @click="handleDelete(model)" class="px-4">Eliminar</button>
        </td>

      </tr> -->

      <thead>
        <tr class="text-2xl text-cyan-900">
          <th v-for="(field, index) in fields" :key="index" @click="handleOrder(field.key)" class="cursor-pointer">{{ field.label }}</th>
          <th>Opciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(model, index) in data" :key="index" class="text-black">
          <td v-for="(field, index) in fields" :key="index">
            <template v-if="modelToBeUpdated?.id === model.id">
              <input v-if="field.type === 'text'" :type="field.type" v-model="model[field.key]" class="  appearance-none
                rounded-none
                px-3
                py-2
                w-72
                border-b border-green-600
                placeholder-gray-500
                text-gray-900
                focus:outline-none focus:ring-secondary-500 focus:ring-1
                sm:text-sm">
              <input v-else :type="field.type" @change="({ target: { checked } }) => model[field.key] = checked"
                :checked="model[field.key]">
            </template>
            <template v-else>
              <template v-if="field.type === 'checkbox'">
                {{ model[field.key] === 0 ? 'No' : 'Si' }}
              </template>
              <template v-else>
                <p class="capitalize">{{ model[field.key] }}</p>
              </template>

            </template>
          </td>
          <td>
            <button v-if="modelToBeUpdated?.id === model.id" @click="handleUpdate()">
              <IconSave class="text-blue-800" />
            </button>
            <button v-else @click="handleEdit(model)">
              <IconEdit class="text-green-800" />
            </button>
            <button @click="handleDelete(model)">
              <IconDelete class="text-red-800" />
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>

  <div class="flex justify-center gap-7 pt-10">
    <button @click="handlePrevious()" class="bg-gray-900 text-white capitalize text-xl py-2 px-4">anterior</button>
    <div class="flex gap-2 items-center text-xl">
      <div v-for="n in 5" :key="n" class="flex">
        <div v-if="currentPage + n - 3 > 0 && currentPage + n - 3 <= totalPage"
          :class="[currentPage === currentPage + n - 3 ? 'text-4xl text-blue-600' : '']">
          {{ currentPage + n - 3 }}
        </div>
      </div>
    </div>

    <button @click="handleNext()" class="bg-gray-900 text-white capitalize text-xl py-2 px-4">siguiente</button>
  </div>
</template>

<script>
import IconPlus from '@/components/icons/IconPlus.vue'
import IconDelete from '@/components/icons/IconDelete.vue'
import IconEdit from '@/components/icons/IconEdit.vue'
import IconSave from '@/components/icons/IconSave.vue'

export default {
  components: {
    IconPlus,
    IconDelete,
    IconEdit,
    IconSave
  },

  props: {
    fields: {
      default: [],
    },

    endpoint: {
      default: null
    }
  },
  data() {
    return {
      data: [],
      modelToBeUpdated: null,
      currentPage: 1,
      totalPage: 1,
      perPage: 5,
      nextPageUrl: null,
      url: 'https://tasks.local/api/' + this.endpoint,
      search: '',
      headers: {
        'Content-Type': 'application/json'
      },
      params:{},

    }
  },

  mounted() {
    this.fetchData()
  },

  methods: {
    async fetch(url, options = {}) {
      return await fetch(url, options).then(response => response.json()).catch(console.log)
    },

    async handleSearch() {
      this.fetchData()
    },

    async fetchData() {
      let url = new URL(this.url)

      this.params.page = this.currentPage
      this.params.search = this.search

      url.search = new URLSearchParams(this.params).toString();

      const response = await this.fetch(url)

      this.data = response.data

      this.perPage = response.per_page
      this.totalPage = response.last_page
      this.nextPageUrl = response.next_page_url
      console.log(response.data)
    },

    handleOrder(column) {
      this.params.orderBy = column
      this.params.asc = !this.params.asc
      this.fetchData()
      
    },

    // crud

    handleAdd() {
      if (
        this.modelToBeUpdated != null
      ) {
        return;
      }
      this.modelToBeUpdated = {
        id: null
      }
      this.fields.forEach(field => {
        let value = null

        if (field.type === 'checkbox') {
          value = false
        }
        if (field.type === 'text') {
          value = ''
        }

        this.modelToBeUpdated[field.key] = value
      })
      this.data = [this.modelToBeUpdated, ...this.data]
      console.log(this.data)
    },

    async handleStore() {
      await this.fetch(this.url, {
        method: 'POST',
        headers: this.headers,
        body: JSON.stringify(this.modelToBeUpdated)
      })
        .finally(() => {
          this.fetchData()
        })
    },


    handleEdit(model) {
      this.modelToBeUpdated = model
    },

    async handleUpdate() {

      if (this.modelToBeUpdated.id == null) {
        this.handleStore()
      }

      await this.fetch(this.url + '/' + this.modelToBeUpdated.id, {
        method: 'PUT',
        headers: this.headers,
        body: JSON.stringify(this.modelToBeUpdated)
      })
        .finally(() => {
          this.fetchData()
        })

      this.modelToBeUpdated = null
    },

    async handleDelete(model) {
      await this.fetch(this.url + '/' + model.id, {
        method: 'DELETE',
        headers: this.headers,
      })
        .finally(() => {
          this.fetchData()
        })
    },

    // pagination

    handlePrevious() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },

    handleNext() {
      if (this.currentPage < this.totalPage && this.nextPageUrl != null) {
        this.currentPage++;
      }

    },

  },

  watch: {
      currentPage() {
        this.fetchData()
        console.log("no sirve")
      }
    }
}
</script>