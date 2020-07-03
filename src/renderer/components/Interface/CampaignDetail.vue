<template>
  <div class="component">
    <div class="d-flex flex-column h-100 bg-gradient">
      <div class="view position-relative d-flex flex-column align-items-center">
        <div class="ribbon left">
          <span class="h3 mr-3 mb-4">Merci !</span>
        </div>

        <div
          class="row d-flex align-items-center justify-content-around pt-5 w-100"
        >
          <span
            class="col-4 offset-1 h3 mt-3 text-uppercase text-center d-flex flex-column align-items-center"
          >
          </span>
          <div class="col-2 offset-5">
            <div class="logo-circle">
              <img :src="session.campaign.logo" :alt="session.campaign.title" />
            </div>
          </div>
        </div>
        <div class="container my-3 text-center" style="height: 550px">
          <youtube
            :video-id="session.campaign.video"
            :player-vars="playerVars"
            style="width: 100%; height: 100%;"
            ref="youtube"
            @ended="playerEnded()"
          ></youtube>
        </div>
        <!--
        <div class="container mb-5 pb-3 mt-2 text-center">
          <div class="progress mb-5" style="height: 30px;">
            <div
              class="progress-bar bg-warning"
              role="progressbar"
              :style="{ width: timer + '%' }"
              :aria-valuenow="timer"
              aria-valuemin="0"
              :aria-valuemax="duration"
            >
              Chargement..
            </div>
          </div>
        </div>
        -->
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "CampaignDetail",
  props: ["session"],
  data: function() {
    return {
      timer: 0,
      time: 0,
      duration: 0,
      playerVars: {
        autoplay: 1,
        controls: 0,
        modestbranding: 1,
        showinfo: 0,
        rel: 0,
      },
    };
  },
  methods: {
    playerEnded: function() {
      this.$emit("nextView");
    },
    gotoPlay: function() {
      setInterval(() => {
        this.$emit("nextView");
      }, 600);
    },
  },
};
</script>
