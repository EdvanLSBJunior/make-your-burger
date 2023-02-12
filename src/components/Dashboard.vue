<template>
  <div id="burger-table">
    <Message :msg="msg" v-show="msg" />
    <div>
      <div id="burger-table-heading">
        <div class="order-id">#:</div>
        <div>Cliente:</div>
        <div>Pão:</div>
        <div>Carne:</div>
        <div>Opcionais:</div>
        <div>Ações:</div>
      </div>
    </div>
    <div id="burger-table-rows" v-for="burger in burgers" :key="burger.id">
      <div class="burger-table-row">
        <div class="order-number">{{ burger.id }}</div>
        <div>{{ burger.nome }}</div>
        <div>{{ burger.pao }}</div>
        <div>{{ burger.carne }}</div>
        <div>
          <ul>
            <li v-for="(optional, index) in burger.opcionais" :key="index">
              {{ optional }}
            </li>
          </ul>
        </div>
        <div>
          <select
            name="status"
            class="status"
            @change="updateBurger($event, burger.id)"
          >
            <option value="" disabled selected>Selecione</option>
            <option
              v-for="stat in status"
              :key="stat.id"
              :value="stat.tipo"
              :selected="burger.status == stat.tipo"
            >
              {{ stat.tipo }}
            </option>
          </select>
          <button class="delete-btn" @click="deleteBurger(burger.id)">
            Cancelar
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Message from "../components/Message.vue";

export default {
  name: "Dashboard",
  components: {
    Message,
  },

  data() {
    return {
      burgers: null,
      burger_id: null,
      status: [],
      msg: null,
    };
  },

  methods: {
    async getPedidos() {
      const req = await fetch("http://localhost:3000/burgers");

      const data = await req.json();

      this.burgers = data;

      this.getStatus();
    },

    async getStatus() {
      const req = await fetch("http://localhost:3000/status");

      const data = await req.json();

      this.status = data;
    },

    async deleteBurger(id) {
      // Obs: este metodo passando o id na url trocando o metodo da requisicao e especifico do Json Service.
      const req = await fetch(`http://localhost:3000/burgers/${id}`, {
        method: "DELETE",
      });

      const res = await req.json();

      this.msg = `O pedido foi removido com sucesso!`;
      setTimeout(() => (this.msg = ""), 3000);

      this.getPedidos();
    },

    async updateBurger(event, id) {
      const option = event.target.value;

      const dataJson = JSON.stringify({ status: option });

      const req = await fetch(`http://localhost:3000/burgers/${id}`, {
        method: "PATCH",
        headers: { "Content-Type": "application/json" },
        body: dataJson,
      });

      const res = await req.json();

      this.msg = `Pedido Nº ${res.id} atualizado para ${res.status}!`;
      setTimeout(() => (this.msg = ""), 3000);
    },
  },

  mounted() {
    this.getPedidos();
  },
};
</script>

<style scoped>
#burger-table {
  max-width: 1200px;
  min-height: 500px;
  margin: 0 auto;
}

#burger-table-heading,
#burger-table-rows,
.burger-table-row {
  display: flex;
  flex-wrap: wrap;
}

#burger-table-heading {
  font-weight: bold;
  padding: 12px;
  border-bottom: 3px solid #333;
}

#burger-table-heading div,
.burger-table-row div {
  width: 19%;
}

.burger-table-row {
  width: 100%;
  padding: 12px;
  border-bottom: 1.5px solid #fcba03;
}

#burger-table-heading .order-id,
.burger-table-row .order-number {
  width: 5%;
}

select {
  padding: 10px 5px;
  margin-right: 12px;
  margin-bottom: 12px;
}

.delete-btn {
  background-color: #222;
  color: #fcba03;
  font-weight: bold;
  border: 2px solid #222;
  padding: 10px;
  font-size: 16px;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.5s;
  border-radius: 5px;
}

.delete-btn:hover {
  background-color: #db6464;
  color: #222;
}
</style>