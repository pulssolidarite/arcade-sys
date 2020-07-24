<template>
  <div class="w-100 h-100">
    <Error
      class="w-100 h-100 position-absolute"
      style="z-index: 999;"
      :visible="errors.visible"
      :title="errors.title"
      :errors="errors.errors"
      @lastView="lastView"
    ></Error>

    <vue-element-loading :active="loading" is-full-screen />
    <div class="w-100 h-100" v-if="!loading">
      <!-- FIRST VIEW -->
      <Start
        :campaigns="campaigns"
        :games="games"
        :session="session"
        @startSession="startSession"
        @saveGame="saveGame"
        @saveCampaign="saveCampaign"
        @error="handleError"
        @nextView="nextView"
        v-if="viewIndex == 0"
      ></Start>

      <!-- SECOND VIEW -->
      <AmountChoice
        :session="session"
        @saveAmount="saveAmount"
        @error="handleError"
        @nextView="nextView"
        @lastView="lastView"
        v-if="viewIndex == 1"
      ></AmountChoice>

      <!-- THIRD VIEW -->
      <Payment
        :session="session"
        @savePayment="savePayment"
        @error="handleError"
        @nextView="nextView"
        @lastView="lastView"
        v-if="viewIndex == 2"
      ></Payment>

      <!-- 4TH VIEW -->
      <!-- <CampaignDetail
        :session="session"
        @error="handleError"
        @nextView="nextView"
        @lastView="lastView"
        v-if="viewIndex == 3"
      ></CampaignDetail> -->

      <!-- 5TH VIEW -->
      <Play
        :session="session"
        @error="handleError"
        @nextView="nextView"
        @lastView="lastView"
        v-if="viewIndex == 3"
      ></Play>

      <!-- 6TH VIEW -->
      <End
        :session="session"
        @error="handleError"
        @nextView="nextView"
        @lastView="lastView"
        v-if="viewIndex == 4"
      ></End>
    </div>
  </div>
</template>

<script>
import VueElementLoading from "vue-element-loading";
import Error from "@/components/Interface/Error.vue";
import Start from "@/components/Interface/Start.vue";
import AmountChoice from "@/components/Interface/AmountChoice.vue";
import Payment from "@/components/Interface/Payment.vue";
import CampaignDetail from "@/components/Interface/CampaignDetail.vue";
import Play from "@/components/Interface/Play.vue";
import End from "@/components/Interface/End.vue";

export default {
  name: "Home",
  components: {
    VueElementLoading,
    Error,
    Start,
    AmountChoice,
    Payment,
    CampaignDetail,
    Play,
    End,
  },
  data: function() {
    return {
      loading: false,
      errors: {
        visible: false,
        title: "",
        errors: {},
      },
      viewIndex: 1,
      maxViewIndex: 4,
      isAdmin: this.$store.getters.isAdmin,
      isLoggedIn: this.$store.getters.isLoggedIn,
      session: {
        terminal: {},
        campaign: {},
        game: {},
        donator: {},
        amount: null,
        start_global: null,
        end_global: null,
        start_time: null,
        end_time: null,
        position_asso: null,
        position_game: null,
        position_amount: null,
      },
      campaigns: {},
      games: {},
    };
  },
  mounted: function() {
    this.session = {
      terminal: {},
      campaign: {},
      game: {},
      donator: {},
      amount: null,
      start_global: null,
      end_global: null,
      start_time: null,
      end_time: null,
      position_asso: null,
      position_game: null,
      position_amount: null,
    };
    this.loading = true;
    // First checking if logged in
    if (!this.isLoggedIn) {
      this.$router.push("/login");
    }

    // Loading all the data from API
    this.loading = true;
    this.$store.commit("startListening");
    this.$http
      .get("terminal/mine/")
      .then((resp) => {
        this.terminal = resp.data.terminal;
        this.campaigns = resp.data.campaigns;
        this.games = resp.data.games;

        // Random appearance of games and campaigns
        this.shuffleArray(this.campaigns);
        this.shuffleArray(this.games);

        // Switching on the terminal
        this.$http.get("terminal/mine/on/");

        // Stop the loading spinner
        this.loading = false;
      })
      .catch((err) => {
        this.errors = {
          visible: true,
          title: "Erreur de chargement",
          errors: err.response.data,
        };
        this.loading = false;
      });
  },
  methods: {
    // CHOICE METHODS
    saveGame: function(payload) {
      this.session.game = payload.game;
      this.session.position_game = payload.indexOf;
    },
    saveCampaign: function(payload) {
      this.session.campaign = payload.campaign;
      this.session.position_asso = payload.indexOf;
    },
    saveAmount: function(payload) {
      this.session.amount = payload.amount;
      this.session.position_amount = payload.indexOf;
    },
    savePayment: function(payload) {
      // Saving the payment right away, avoiding to loose sensitive data
      this.loading = true;
      this.$http
        .post("payment/", payload.payment)
        .then((resp) => {
          if (resp.status == "201") {
            this.loading = false;
            this.nextView();
          }
        })
        .catch((err) => {
          this.loading = false;
          this.errors = {
            visible: true,
            title: "Erreur d'enregistrement",
            errors: err.response.data,
          };
        });
    },

    // SESSION SPECIFIC METHODS
    startSession: function() {
      this.loading = true;
      // Creating a new donator for the session
      this.$http
        .post("donator/", {})
        .then((resp) => {
          this.session.terminal = this.terminal; // Binding the terminal to the session
          this.session.start_global = new Date(); // Starting global timer for session
          this.session.donator = resp.data; // Binding the donator to the session
          this.loading = false;
        })
        .catch((err) => {
          this.errors = {
            visible: true,
            title: "Erreur de session",
            errors: err.response.data,
          };
          this.loading = false;
        });
    },
    startGameSession: function() {
      this.session.start_time = new Date(); // Starting gaming timer for session
      this.$http
        .get("terminal/mine/play/")
        .then()
        .catch((err) => {
          this.errors = {
            visible: true,
            title: "Erreur de status de jeu",
            errors: err.response.data,
          };
        }); // Activating Playing status on backend
    },
    endGameSession: function() {
      this.session.end_time = new Date(); // Ending gaming timer for session
      this.$http.get("terminal/mine/gameover/").catch((err) => {
        this.errors = {
          visible: true,
          title: "Impossible d'éteindre la borne",
          errors: err.response.data,
        };
      });
    },
    endSession: function() {
      this.session.end_global = new Date(); // Ending global timer for session
      this.session.terminal = this.session.terminal.id;
      this.session.donator = this.session.donator.id;
      this.session.campaign = this.session.campaign.id;
      this.session.game = this.session.game.id;

      this.$http.post("session/", this.session).catch((err) => {
        this.errors = {
          visible: true,
          title: "Erreur de sauvegarde de session",
          errors: err.response.data,
        };
      });
    },

    // ERROR SPECIFIC METHODS
    handleError: function(payload) {
      this.errors = payload;
    },

    // ALL GENERAL METHODS
    nextView: function() {
      if (this.viewIndex < this.maxViewIndex) {
        this.viewIndex += 1;
      } else {
        this.viewIndex = 0;
      }
    },
    lastView: function() {
      if (this.viewIndex > 0) {
        this.viewIndex -= 1;
      }
      this.errors = {
        visible: false,
        title: "",
        errors: [],
      };
    },
    shuffleArray: function(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
    },
  },
};
</script>
