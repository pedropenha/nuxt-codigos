<template>
  <section id="main">
    <div class="conteudo">
      <div class="pb-3">
        <input v-model="codigo" type="text" class="form-control"/>
      </div>
      <div class="pb-3">
        <button @click="enviarCodigo" :class="status === 200 ? 'btn btn-success w-100' : status === 500 ? 'btn btn-danger w-100' : 'btn btn-info w-100'">Enviar código</button>
      </div>
      <div>
        <table class="table table-dark">
          <thead>
          <tr>
            <th scope="col">ID</th>
            <th scope="col">Codigo</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="cod in codigos" :key="cod.id">
            <td>{{cod.id}}</td>
            <td>{{cod.codigo}}</td>
          </tr>
          </tbody>
        </table>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  async asyncData ({ $axios }) {
    const codigos = await $axios.$get('http://localhost:8000/api/v1/list')
    return { codigos }
  },
  data () {
    return {
      codigo: '',
      codigos: [],
      status: 0
    }
  },
  methods: {
    enviarCodigo () {
      if (this.codigo !== '') {
        this.$axios.post('http://localhost:8000/api/v1/create-code', {
          codigo: this.codigo
        }).then((response) => {
          this.status = response.status
          const self = this
          setTimeout(function () {
            self.status = 0
          }, 2000)
          this.codigos.push(response.data)
          this.codigo = ''
        }).catch((error) => {
          console.log(error)
          this.status = 500
          const self = this
          setTimeout(function () {
            self.status = 0
          }, 2000)
        })
      } else {
        this.status = 500
      }
    }
  }
}
</script>

<style scoped>
#main{
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  display: flex;
  align-content: center;
  justify-content: center;
  align-items: center;
}
.conteudo{
  width: 45%;
}
</style>
