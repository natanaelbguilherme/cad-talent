<template>
  <form @submit.prevent="handleSubmit" class="form-new">
    <h1>cadastro de talento</h1>

    <label for="name">Nome Completo</label>
    <input id="name" v-model="name" />
    {{ this.errors.name }}

    <label for="email">Email</label>
    <input id="email" v-model="email" />
    {{ this.errors.email }}

    <label for="date">Data de nascimento</label>
    <input type="date" id="date" v-model="date_birth" />

    <label for="phone">Whatsapp</label>
    <input id="phone" v-model="phone" />

    <label for="area">Area de interesse</label>
    <select v-model="area">
      <option value="frontend">Frontend</option>
      <option value="backend">Backend</option>
      <option value="fullstack">Fullstack</option>
    </select>
    {{ this.errors.area }}

    <label for="nivel">Nivel</label>
    <select v-model="nivel">
      <option value="junior">Junior</option>
      <option value="pleno">Pleno</option>
      <option value="senior">Senior</option>
    </select>

    <p>Selecione suas habilidades</p>

    {{ skills.length }}

    <div v-if="area === 'frontend' || area === 'fullstack'">
      <label><input type="checkbox" v-model="skills" value="HTML" />HTML</label>
      <label><input type="checkbox" v-model="skills" value="CSS" /> CSS</label>
      <label><input type="checkbox" v-model="skills" value="JAVASCRIPT" /> JAVASCRIPT</label>
      <label><input type="checkbox" v-model="skills" value="VUE" /> VUE</label>
    </div>

    <div v-if="area === 'backend' || area === 'fullstack'">
      <label><input type="checkbox" v-model="skills" value="Node" />Node</label>
      <label> <input type="checkbox" v-model="skills" value="Php" /> Php</label>
      <label><input type="checkbox" v-model="skills" value="Laravel" /> Laravel</label>
      <label><input type="checkbox" v-model="skills" value="Java" /> Java</label>
    </div>

    <label>Carta de apresentacao</label>
    <textarea v-model="apresentation"></textarea>

    <button type="submit">Cadastrar</button>
  </form>
</template>

<script>
import * as yup from 'yup'
import { captureErrorYup } from '../../../utils/captureErroYup'
import axios from 'axios'

export default {
  data() {
    return {
      name: '',
      email: '',
      date_birth: '',
      phone: '',
      area: '',
      nivel: '',
      skills: [],
      apresentation: '',
      errors: {}
    }
  },
  methods: {
    handleSubmit() {
      try {
        // montar o schema
        const schema = yup.object().shape({
          name: yup.string().required('O nome é obrigatório').min(10, 'O nome é pequeno demais'),
          email: yup.string().email('Email não inválido').required('Email é obrigatório'),
          area: yup.string().required('A area é obrigatorio')
        })

        // passa os dados para validacao
        schema.validateSync(
          {
            name: this.name,
            email: this.email,
            area: this.area
          },
          { abortEarly: false }
        )

        axios({
          url: 'http://localhost:3000/api/talent',
          method: 'POST',
          data: {
            name: this.name,
            email: this.email,
            date_birth: this.date_birth,
            phone: this.phone,
            area: this.area,
            nivel: this.nivel,
            skills: this.skills,
            bio: this.apresentation
          }
        })
          .then(() => {
            alert('CADASTRADO com sucesso')
          })
          .catch(() => {
            alert('houve um erro ao cadastrar')
          })

        /*

        fetch('http://localhost:3000/api/talent', {
          method: 'POST',
          body: JSON.stringify({
            name: this.name,
            email: this.email,
            date_birth: this.date_birth,
            phone: this.phone,
            area: this.area,
            nivel: this.nivel,
            skills: this.skills,
            bio: this.apresentation
          }),
          headers: {
            Accept: 'application/json',
            'Content-Type': 'application/json'
          }
        })
          .then((response) => {
            if (response.ok === false) {
              throw new Error()
            }
            return response.json()
          })
          .then(() => {
            alert('CADASTRADO com sucesso')
          })
          .catch(() => {
            alert('houve um erro ao cadastrar')
          })

          */
        // .............resto codigo ........
      } catch (error) {
        if (error instanceof yup.ValidationError) {
          // certeza que foi um erro do yup
          this.errors = captureErrorYup(error)
        }
      }
    }
  },
  watch: {
    area(newValue, oldValue) {
      if (newValue !== oldValue) {
        this.skills = []
      }
    }
  }
}
</script>

<style scoped>
.form-new {
  display: flex;
  flex-direction: column;
}
</style>


