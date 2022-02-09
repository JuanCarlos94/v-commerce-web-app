<template>
  <div>
    <v-card max-width="1000" class="mx-auto">
      <v-card-title>{{ deal.type }}</v-card-title>
      <v-container>
        <v-row>
          <v-col cols="12" lg="6" md="6" sm="12">
            <v-carousel>
              <div v-if="deal.photos.length === 0">
                <v-carousel-item
                  src="https://anest-iwata.com.br/wp-content/uploads/2016/10/Sem-imagem.png"
                  reverse-transition="fade-transition"
                  transition="fade-transition"
                >
                </v-carousel-item>
              </div>
              <div v-else>
                <v-carousel-item
                  v-for="(photo, i) in deal.photos"
                  :key="i"
                  :src="photo.src"
                  reverse-transition="fade-transition"
                  transition="fade-transition"
                >
                </v-carousel-item>
              </div>
            </v-carousel>
          </v-col>
          <v-col cols="12" lg="6" md="6" sm="12">
            <v-row class="mx-auto">
              <v-col cols="12">
                <v-chip
                  class="mr-2"
                  x-large
                  color="pink"
                  label
                  text-color="white"
                  ><h1>{{ formatValue(deal.value) }}</h1></v-chip
                >
                <v-chip
                  v-if="deal.closed"
                  class="mr-2"
                  color="success"
                  label
                  text-color="white"
                  ><h1>Fechada</h1></v-chip
                >
              </v-col>
            </v-row>
            <v-divider class="mt-3 mb-3"></v-divider>

            <v-card class="mb-4">
              <v-card-title
                ><v-icon large left> mdi-information </v-icon
                ><span>Descrição</span></v-card-title
              >
              <v-card-text class="font-weight-bold">
                <h3>Descrição: {{ deal.description }}</h3>
                <h3 v-if="deal.trade_for">Trocar por: {{ deal.trade_for }}</h3>
              </v-card-text>
            </v-card>
            <v-card>
              <v-card-title
                ><v-icon large left> mdi-map-marker </v-icon
                ><span>Endereço</span></v-card-title
              >
              <v-card-text class="font-weight-bold"
                >{{ deal.location.address }}, {{ deal.location.zip_code }},
                {{ deal.location.city }}/{{ deal.location.state }}</v-card-text
              >
            </v-card>
            <v-row class="mt-2">
              <v-col cols="12">
                <v-chip class="mr-2" color="pink" label text-color="white">
                  Urgência: {{ deal.urgency.type }}</v-chip
                >
                <v-chip
                  class="mr-2"
                  color="pink"
                  label
                  text-color="white"
                  v-if="deal.urgency.limit_date"
                >
                  Data limite: {{ formatDate(deal.urgency.limit_date) }}</v-chip
                >
              </v-col>
            </v-row>
          </v-col>
        </v-row>
      </v-container>
      <v-card-actions v-if="deal.user._id">
        <v-spacer></v-spacer>
        <div v-if="userOwner">
          <v-btn
            class="ma-2"
            color="primary"
            :to="'/editar-negociacao?id=' + deal._id"
            v-if="!bidClosed.value"
            >Editar negociação</v-btn
          >
          <v-btn class="ma-2" color="pink" dark @click="dialogBids = true"
            >Ofertas recebidas</v-btn
          >
          <v-btn
            class="ma-2"
            color="secondary"
            v-if="bidClosed.value"
            @click="loadDeliveryStatus"
            >Status da entrega</v-btn
          >
        </div>
        <div v-else>
          <v-btn
            class="ma-2"
            color="primary"
            v-if="!deal.closed && !bid._id"
            @click="dialogSendBid = true"
            >Enviar oferta</v-btn
          >
          <v-btn class="ma-2" color="secondary" @click="dialogBidSended = true"
            >Ver oferta enviada</v-btn
          >
          <v-btn
            class="ma-2"
            color="secondary"
            @click="loadDeliveryStatusFromDeal"
            v-if="bid.accepted"
            >Status da entrega</v-btn
          >
        </div>
      </v-card-actions>
    </v-card>

    <v-card max-width="1000" class="mx-auto mt-2">
      <v-card-title
        >Mensagens <v-spacer></v-spacer>
        <v-btn color="primary" @click="dialogNewMessage = true"
          >Nova mensagem</v-btn
        ></v-card-title
      >
      <v-card-text>
        <v-card
          color="primary"
          class="mt-2"
          dark
          v-for="(msg, i) in messages"
          :key="i"
        >
          <v-list-item class="mt-2" two-line>
            <v-list-item-content>
              <v-list-item-title
                ><h3>
                  {{ msg.user_id.name }}: {{ msg.title }}
                </h3></v-list-item-title
              >
              <p class="text-justify">{{ msg.message }}</p>
            </v-list-item-content>
          </v-list-item>
        </v-card>
      </v-card-text>
    </v-card>

    <v-dialog v-model="dialogNewMessage" persistent max-width="600">
      <v-card>
        <v-card-title>Nova mensagem</v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="12" md="12">
                <v-text-field
                  label="Título"
                  required
                  v-model="message.title"
                ></v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" sm="12" md="12">
                <v-textarea
                  name="input-7-1"
                  label="Mensagem"
                  v-model="message.message"
                ></v-textarea>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="dialogNewMessage = false"
            >Cancelar</v-btn
          >
          <v-btn color="blue darken-1" text @click="sendMessage">Enviar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog
      v-model="dialogSendBid"
      persistent
      max-width="600"
      v-if="!userOwner"
    >
      <v-card>
        <v-card-title>Enviar oferta</v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="12" md="12">
                <v-textarea
                  name="input-7-1"
                  label="Descrição"
                  v-model="bid.description"
                ></v-textarea>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="6" sm="6" md="4">
                <v-text-field
                  label="Valor"
                  required
                  v-model="bid.value"
                  v-money="money"
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="dialogSendBid = false"
            >Cancelar</v-btn
          >
          <v-btn color="blue darken-1" text @click="sendBid">Enviar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog
      v-model="dialogStatusDelivery"
      persistent
      max-width="800"
      v-if="userOwner || bid.accepted"
    >
      <v-card>
        <v-card-title>Status da entrega</v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <p>
                  Origem: {{ delivery.from.address }},
                  {{ delivery.from.zip_code }}, {{ delivery.from.city }}/{{
                    delivery.from.state
                  }}
                </p>
                <p>
                  Destino: {{ delivery.to.address }},
                  {{ delivery.to.zip_code }}, {{ delivery.to.city }}/{{
                    delivery.to.state
                  }}
                </p>
                <h3>Entrega</h3>
                <v-timeline align-top dense>
                  <v-timeline-item v-for="(step, i) in steps" :key="i" small>
                    <div>
                      <div class="font-weight-normal">
                        <strong>{{ step.location }}</strong>
                        {{ step.incoming_date }} - {{ step.outcoming_date }}
                      </div>
                    </div>
                  </v-timeline-item>
                </v-timeline>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="blue darken-1"
            text
            @click="dialogStatusDelivery = false"
            >Fechar</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog
      v-model="dialogBidSended"
      persistent
      max-width="800"
      v-if="!userOwner"
    >
      <v-card>
        <v-card-title>Oferta enviada</v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-chip
                  class="ma-2"
                  :color="bid.accepted ? 'success' : 'primary'"
                >
                  {{ bid.accepted ? "Aceita" : "Aguardando resposta" }}
                </v-chip>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" sm="12" md="12">
                <h3>Valor: {{ formatValue(bid.value) }}</h3>
                <h3>{{ bid.description }}</h3>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="dialogBidSended = false"
            >Fechar</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="dialogBids" persistent max-width="800" v-if="userOwner">
      <v-card>
        <v-card-title>Ofertas recebidas</v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-card
                  class="mb-2"
                  :color="bid.accepted ? 'success' : 'blue'"
                  v-for="(bid, i) in bids"
                  :key="i"
                >
                  <v-card-title
                    >{{ bid.user_id.name }} - Valor:
                    {{ bid.value }}</v-card-title
                  >
                  <v-card-text>
                    {{ bid.description }}
                  </v-card-text>
                  <v-card-actions v-if="!bidClosed.value">
                    <v-spacer></v-spacer>
                    <div v-if="bidAccepted && bidAccepted._id == bid._id">
                      Confirmar?
                      <v-btn @click="confirmAcceptBid" color="success"
                        >Sim</v-btn
                      >
                      <v-btn @click="cancelAcceptBid" color="red accent-4" dark
                        >Não</v-btn
                      >
                    </div>
                    <v-btn color="primary" @click="acceptBid(bid)" v-else
                      >Aceitar</v-btn
                    >
                  </v-card-actions>
                </v-card>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="dialogBids = false"
            >Fechar</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import { VMoney } from "v-money";
export default {
  name: "Deals",
  middleware: "auth",
  data: () => ({
    deal: {
      type: "",
      description: "",
      value: 0,
      location: {
        lat: 0,
        lng: 0,
        address: "",
        city: "",
        state: "",
        zip_code: "",
      },
      urgency: {
        type: "",
        limit_date: "",
      },
      photos: [],
      user: {
        _id: null,
      },
    },
    messages: [],
    message: {
      user_id: "",
      deal_id: "",
      title: "",
      message: "",
    },
    marker: true,
    dialogSendBid: false,
    dialogStatusDelivery: false,
    bid: {
      accepted: false,
      description: "",
      value: "",
      user_id: "",
    },
    delivery: {
      from: {
        lat: 0,
        lng: 0,
        address: "",
        city: "",
        state: "",
        zip_code: "",
      },
      to: {
        lat: 0,
        lng: 0,
        address: "",
        city: "",
        state: "",
        zip_code: "",
      },
      value: 0,
    },
    steps: [],
    dialogBids: false,
    bids: [],
    bidAccepted: null,
    userOwner: false,
    dialogNewMessage: false,
    bidClosed: {
      user_id: "",
      accepted: true,
      value: 0,
      description: "",
    },
    dialogBidSended: false,
    money: {
      decimal: ",",
      thousands: ".",
      precision: 2,
      masked: true /* doesn't work with directive */,
    },
  }),
  methods: {
    sendBid() {
      this.bid.user_id = this.$auth.user._id;
      this.$axios
        .post("/deal/" + this.deal._id + "/bid", this.bid)
        .then((res) => {
          this.dialogSendBid = false;
        })
        .catch((err) => {
          console.log(err);
          alert("Erro ao enviar oferta, tente novamente.");
        });
    },
    async loadDeal(id) {
      await this.$axios
        .get("/deal/" + id)
        .then((res) => {
          this.deal = res.data.deal;
          this.userOwner = this.$auth.user._id === this.deal.user._id;
        })
        .catch((err) => {
          if (err.response && err.response.data && err.response.data.msg) {
            console.log(err.response.data.msg);
          }
          alert("Erro ao carregar negociação, tente novamente.");
        });
      await this.loadMessages();
    },
    acceptBid(bid) {
      this.bidAccepted = bid;
    },
    confirmAcceptBid() {
      this.$axios
        .put(
          "/deal/" + this.deal._id + "/bid/" + this.bidAccepted._id + "/accept",
          this.bidAccepted
        )
        .then((res) => {
          if (res.status === 200) {
            window.location.reload(true);
          }
        })
        .catch((err) => {
          console.log(err);
          alert("Erro ao confirmar oferta, tente novamente.");
        });
    },
    cancelAcceptBid() {
      this.bidAccepted = null;
    },
    async loadBids() {
      await this.$axios
        .get("/deal/" + this.deal._id + "/bid")
        .then((res) => {
          this.bids = res.data;
        })
        .catch((err) => {
          alert("Erro ao carregar ofertas, tente novamente.");
          console.log(err);
        });
      await this.verifyBidAccepted();
    },
    formatValue(val) {
      var formatter = new Intl.NumberFormat("pt-BR", {
        style: "currency",
        currency: "BRL",
      });
      return formatter.format(val);
    },
    sendMessage() {
      this.message.user_id = this.$auth.user._id;
      this.message.deal_id = this.deal._id;
      this.$axios
        .post("/deal/" + this.deal._id + "/message", this.message)
        .then((res) => {
          this.message = {
            user_id: "",
            deal_id: "",
            title: "",
            message: "",
          };
          this.dialogNewMessage = false;
          this.loadMessages();
        })
        .catch((err) => {
          console.log(err);
          alert("Erro ao enviar mensagem, tente novamente.");
        });
    },
    loadMessages() {
      this.$axios
        .get("/deal/" + this.deal._id + "/message")
        .then((res) => {
          this.messages = res.data;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    async verifyBidAccepted() {
      this.bidClosed = await this.bids.filter((bid) => {
        return bid.accepted == true;
      });
      if (this.bidClosed.length !== 0) {
        this.bidClosed = this.bidClosed[0];
      }
    },
    loadDeliveryStatus() {
      console.log("/deal/" + "/delivery");
      this.$axios
        .post("/deal/" + this.deal._id + "/delivery", {
          user_id: this.bidClosed.user_id,
        })
        .then((res) => {
          this.delivery = res.data.delivery;
          this.steps = res.data.steps;
          this.dialogStatusDelivery = true;
        })
        .catch((err) => {
          console.log(err);
          alert("Erro ao carregar informações de entrega, tente novamente.");
        });
    },
    loadDeliveryStatusFromDeal() {
      this.$axios
        .get("/deal/" + this.deal._id + "/delivery")
        .then((res) => {
          this.delivery = res.data.delivery;
          this.steps = res.data.steps;
          this.dialogStatusDelivery = true;
        })
        .catch((err) => {
          console.log(err);
          alert("Erro ao carregar informações de entrega, tente novamente.");
        });
    },
    async loadBid() {
      await this.$axios
        .get("/deal/" + this.deal._id + "/user/bid")
        .then((res) => {
          this.bid = res.data.bid;
        })
        .catch((err) => {
          console.log(err);
          alert("Erro ao carregar oferta, tente novamente.");
        });
    },
    formatDate(val) {
      let vals = val.slice(0, 10).split("-");
      return vals[2] + "/" + vals[1] + "/" + vals[0];
    },
  },
  async created() {
    await this.loadDeal(this.$route.query.id);
    if (this.userOwner) {
      this.loadBids();
    } else {
      await this.loadBid();
    }
  },
  directives: {
    money: VMoney,
  },
};
</script>

<style>
</style>