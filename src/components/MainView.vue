<template>
  <transition-group>
    <b-alert show variant="danger" v-show="show">{{ warning_text }}</b-alert>
  </transition-group>
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
    <b-button  variant="outline-danger" v-if="textArray !== ''" @click="clearAll">Очистить</b-button>
    <b-button  variant="outline-danger" v-else disabled>Очистить</b-button>
  </div>
  <div v-show="ready">
    <v-network-graph
        class="graph"
        :nodes="nodes"
        :edges="edges"
        :layouts="layouts"
    >
      <template #edge-label="{ edge, ...slotProps }">
        <v-edge-label :text="edge.label" align="center" vertical-align="above" v-bind="slotProps" />
      </template>
    </v-network-graph>
  </div>
</template>

<script>
import make_http from "@/api/base";

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
      textArray: "",
      show: false,
      warning_text: "",
      nodes: {
        node1: { name: "Node 1" },
        node2: { name: "Node 2" },
        node3: { name: "Node 3" },
        node4: { name: "Node 4" },
      },
      edges: {
        edge1: { source: "node1", target: "node2" },
        edge2: { source: "node2", target: "node3" },
        edge3: { source: "node3", target: "node4" },
      },
      layouts: {
          nodes: {
            node1: { x: 0, y: 0 },
            node2: { x: 50, y: 50 },
            node3: { x: 100, y: 0 },
            node4: { x: 150, y: 50 },
          }
        },
      ready: false
    }
  },
  methods: {
    closeAlert() {
      this.show = false;
      this.warning_text = "";
    },
    clearFields() {
      this.temp.f1 = "";
      this.temp.r = "";
      this.temp.f2 = "";
    },
    addText() {
      if (this.temp.f1 === this.temp.f2) {
        this.warning_text = "Петли не допустимы!";
        this.show = true;
        this.clearFields();
        setTimeout(this.closeAlert, 2000);
        return;
      }
      var obj = { ...this.temp };
      this.array.push(obj);
      this.textArray += `${this.temp.f1},` + `${this.temp.r},` + `${this.temp.f2}\n`;
      this.clearFields();
    },
    async sendData() {
      try {
        const response = await make_http.post(`api/makegraph`, {
          data: this.array
        });
        if (response.status === 200) {
          localStorage.setItem("nodes", JSON.stringify(response.data.response.nodes));
          localStorage.setItem("edges", JSON.stringify(response.data.response.edges));
          localStorage.setItem("layouts", JSON.stringify(response.data.response.layouts));
          this.router.push("/GraphView");
        }
      } catch (e){
        this.warning_text = "Ошибка отправки запроса!"
        this.show = true;
        setTimeout(this.closeAlert, 2000);
      } finally {
        this.nodes = JSON.parse(localStorage.getItem("nodes"));
        this.edges = JSON.parse(localStorage.getItem("edges"));
        this.layouts = JSON.parse(localStorage.getItem("layouts"));
        this.ready = true;
      }
    },
    clearAll() {
      this.array = [];
      this.clearFields();
      this.textArray = "";
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