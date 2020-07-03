<template>
  <div class="component">
    <div class="d-flex flex-column h-100 bg-gradient">
        <div
          class="view position-relative d-flex flex-column align-items-center"
        >
          <div class="ribbon left">
            <span class="h3 mr-3 mb-4">Merci !</span>
          </div>

          <div
            class="row d-flex align-items-center justify-content-around mt-5 pt-5 w-100"
          >
            <span
              class="col-4 offset-1 h3 mt-3 text-uppercase text-center d-flex flex-column align-items-center"
            >
            </span>
            <div class="col-2 offset-2 d-flex flex-column justify-content-center align-items-center">
              <div class="logo-circle">
                <img :src="session.campaign.logo" :alt="session.campaign.name" />
              </div>
              <span class="text-white">{{ session.campaign.link }}</span>
            </div>
          </div>
            <div
              class="position-absolute col h-100 w-100 d-flex align-items-center justify-content-center"
            >
              <h2 class="text-white">Merci pour votre don !</h2>
            </div>
          </div>
        </div>
        <div class="big-gamepad bg-gradient d-flex px-4 py-2">
          <div
            class="d-flex align-items-center justify-content-between flex-1 py-2 px-3"
          >
            <img src="@/assets/img/gamepad.svg" width="60" />
            <div class="g-buttons ml-4">
              <div class="row mb-1">
                <span :class="['g-btn']">X</span>
                <span :class="['g-btn']">Y</span>
                <span class="g-btn">L</span>
              </div>
              <div class="row">
                <span :class="['g-btn']">A</span>
                <span :class="['g-btn', b ? 'clicked' : '']">B</span>
                <span class="g-btn">R</span>
              </div>
            </div>
          </div>
          <div class="d-flex align-items-center justify-content-between ml-5">
            <span><span class="g-btn">A</span>Valider</span>
            <span><span class="g-btn">B</span>Accueil</span>
          </div>
        </div>
        <div
          class="small-btn-gamepad d-flex align-items-center justify-content-center p-4 mb-3 pr-5"
        >
          <span><span class="g-btn">B</span>Retour</span>
        </div>
      </div>
  </div>
</template>

<script>
export default {
  name: "End",
  props: ["session"],
  data: function() {
    return {};
  },
  computed: {
    b() {
      return this.$store.state.gamepad.B;
    },
    start() {
      return this.$store.state.gamepad.Start;
    }
  },
  watch: {
    b: function(val) {
      if (val) {
        this.$emit("nextView");
      }
    },
    start: function(val) {
      if (val) {
        this.$emit("nextView");
      }
    }
  },
  mounted: function() {
    this.$store.commit("startListening")
    this.$emit("endGameSession");
    this.$emit("endSession");
    setTimeout(function() {
        this.$emit("nextView");
      }.bind(this), 10000);
  },
  methods: {}
};
</script>
