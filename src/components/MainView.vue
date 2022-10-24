<template>
  <div class="container">
    <h1>Создание графа знаний</h1>
    <div>
      <b-form-input v-model="temp.f1" placeholder="Факт1"></b-form-input>
    </div>
    <div>
      <b-form-input v-model="temp.r" placeholder="Отношение"></b-form-input>
    </div>
    <div>
      <b-form-input v-model="temp.f2" placeholder="Факт2"></b-form-input>
    </div>
    <b-form-textarea
        id="textarea"
        v-model="textArray"
        placeholder=""
        rows="3"
        max-rows="6"
    ></b-form-textarea>
    <b-button @click="addText" variant="outline-primary" v-if="temp.f1 !=='' && temp.r !== '' && temp.f2 !== ''">Добавить отношение</b-button>
    <b-button @click="addText" variant="outline-primary" v-else disabled>Добавить отношение</b-button>
    <b-button  variant="outline-success" v-if="textArray !== ''" @click="sendData">Создать граф</b-button>
    <b-button  variant="outline-warning" v-else disabled>Создать граф</b-button>
  </div>
</template>

<script>

export default {
  name: "MainView",
  title: "Observer",
  components: {

  },
  data() {
    return {
      temp: {
        f1: "",
        r: "",
        f2: ""
      },
      array: [],
      textArray: ""
    }
  },
  methods: {
    addText() {
      var obj = { ...this.temp };
      this.array.push(obj);
      this.textArray += `${this.temp.f1},` + `${this.temp.r},` + `${this.temp.f2}\n`;
      this.temp.f1 = "";
      this.temp.r = "";
      this.temp.f2 = "";
    },
    sendData() {
      console.log(this.array);
    }
  }
}
</script>

<style scoped>
.xyu {
  width: 100%;
  position: absolute;
  top: 50%;
  transform: translate(0, -50%);
  border: 5px solid #FFFF00;
  padding: 10px;
}
</style>