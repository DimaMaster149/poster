<template>
  <transition name="fade">
    <div class="page-wrap">
      <span class="map-icon__button" @click="showMap = !showMap">
        <img
          src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQ6x-dxYY3EggY7e-qZRsHk3j310YHGLRRWdHi_fvVJgwU-YGgQ"
          class="map-icon__img"
        >
      </span>
      <div class="map__wrap" :class="{'map-hide': !showMap}">
        <date-picker class="map-date" v-model="searchDay" @input="searchEvents"></date-picker>
        <div class="leaflet__wrap" :class="{'hide': !showMap}">
          <l-map id="map" ref="map" :zoom="12" :center="[47.80, 35.165]">
            <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
            <l-marker v-for="marker in this.placemarkers" :key="marker.id" :lat-lng="marker.coords">
              <l-icon iconUrl="./images/markers/map-marker.png" class="icon"></l-icon>
              <l-tooltip>
                <div class="label">
                  <div class="label__image">
                    <img :src="'/images/'+marker.image" alt>
                  </div>
                  <div class="label__text">
                    <div class="label__title">{{marker.title}}</div>
                    <div class="label__description">Описание: {{short(marker.description, 50)}}</div>
                    <div class="label__location">Место: {{marker.location}}</div>
                    <div class="label__date">
                      Время: {{marker.date}}
                      <br>
                      {{marker.time}}
                    </div>
                  </div>
                </div>
              </l-tooltip>
            </l-marker>
          </l-map>
        </div>
      </div>
      <div class="events__wrap">
        <div class="events">
          <div class="events__title">Все события в городе Запорожье {{searchDay}}</div>
          <div class="event-block" v-for="event in this.publishedDailyEvents" :key="event.event_id">
            <div class="event-image__wrap">
              <div class="event-image">
                <img class="event-image__img" :src="'/images/'+event.event_image" alt>
              </div>
            </div>
            <div class="event-about__wrap">
              <div class="event-about">
                <div class="event-about__title">{{event.event_title}}</div>
                <div class="event-about__description">{{event.event_description}}</div>
                <div class="event-about__location">Месторасположение: {{event.event_location}}</div>
                <div class="event-about__date">Дата: {{event.event_date}}</div>
                <div class="event-about__time">Время: {{event.event_time}}</div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import { LMap, LTileLayer, LMarker, LTooltip, LIcon } from "vue2-leaflet";
import L from "vue2-leaflet";
import DatePicker from "./lib/input/DatePicker";

export default {
  name: "Yandex",
  components: {
    DatePicker,
    LMap,
    LTileLayer,
    LMarker,
    LTooltip,
    LIcon
  },

  data() {
    return {
      searchDay: "",
      url: "http://{s}.tile.osm.org/{z}/{x}/{y}.png",
      // url: "https://{s}.tile.openstreetmap.fr/osmfr/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      showMap: false
    };
  },
  created() {
    this.axios
      .get("events/published")
      .then(resp => {
        let events = resp.data;
        this.searchDay =
          events[0] && events[0].event_date
            ? events[0].event_date
            : new Date().toJSON().slice(0, 10);
        this.$store.commit("loadPublishedDailyEvents", events);
      })
      .catch(err => {
        console.log(err);
      });
  },
  computed: {
    publishedDailyEvents() {
      return this.$store.getters.getPublishedDailyEvents;
    },
    placemarkers() {
      let eventArray = [];
      let events = this.$store.getters.getPublishedDailyEvents;
      events.forEach(function(event) {
        let eventObj = {
          id: event.event_id,
          coords: [event.event_lat, event.event_long],
          title: event.event_title,
          description: event.event_description,
          image: event.event_image,
          location: event.event_location,
          date: event.event_date,
          time: event.event_time
        };
        eventArray.push(eventObj);
      });
      return eventArray;
    }
  },
  methods: {
    searchEvents() {
      this.axios
        .get(`/events/published/${this.searchDay}`)
        .then(response => {
          const events = response.data;
          this.$store.commit("loadPublishedDailyEvents", events);
        })
        .catch(function(error) {
          console.log(error);
        });
    },
    short(str, maxLen, separator = " ") {
      if (str.length <= maxLen) return str;
      return str.substr(0, str.lastIndexOf(separator, maxLen));
    }
  }
};
</script>

<style scoped>
@import "~leaflet/dist/leaflet.css";
.page-wrap {
  display: flex;
  width: 100%;
  height: calc(100vh - 60px);
}

.map__wrap {
  width: 68%;
  position: fixed;
  left: 0;
  top: 60px;
  bottom: 0;
  z-index: 1;
}

.map-date {
  position: absolute;
  z-index: 999;
  right: 2%;
  margin-top: 11px;
}

.leaflet__wrap {
  width: 100%;
  height: 100%;
}

.leaflet-marker-icon {
  width: 35px !important;
  height: 38px !important;
}

.label {
  display: flex;
  flex-direction: row;
}

.label__text {
  display: flex;
  flex-direction: column;
  padding-left: 5px;
  word-break: break-all;
  word-wrap: break-word;
  overflow-wrap: break-word;
}

.map-icon__button {
  display: none;
}

.label__image {
  width: 120px;
  height: auto;
}

.label__image img {
  width: 100%;
  height: auto;
}

.label__title {
  font-size: 14px;
  text-align: center;
}

.label__description {
  word-break: break-all;
  word-wrap: break-word;
  overflow-wrap: break-word;
}

.events__wrap {
  width: 100%;
  height: auto;
  padding: 70px 20px 20px calc(68% + 20px);
  box-sizing: border-box;
}

.events {
  display: flex;
  flex-direction: column;
}

.events__title {
  width: 100%;
  font-size: 18px;
  text-align: center;
  font-weight: 600;
  font-family: Roboto, sans-serif;
  margin: 25px 0 10px 0;
}

.event-block {
  width: 100%;
  margin: 20px 0 15px 0;
  display: flex;
  flex-direction: column;
}
.event-image__wrap {
  width: 100%;
  height: auto;
}
.event-image__img {
  width: 100%;
  height: auto;
  max-height: 425px;
}
.event-image__wrap {
  width: 100%;
  height: auto;
  max-height: 425px;
}
.event-about {
  font-size: 16px;
  font-family: Roboto, sans-serif;
  font-weight: 400;
  word-wrap: break-word;
}
.event-about__title {
  font-size: 18px;
  font-weight: 500;
  padding-bottom: 10px;
}
.leaflet-container .leaflet-overlay-pane svg,
.leaflet-container .leaflet-marker-pane img,
.leaflet-container .leaflet-shadow-pane img,
.leaflet-container .leaflet-tile-pane img,
.leaflet-container img.leaflet-image-layer,
.leaflet-container .leaflet-tile {
  /* max-width: none !important; */
  width: auto !important;
  max-height: none !important;
}

@media screen and (max-width: 768px) {
  .page-wrap {
    flex-direction: column;
    height: 500px;
  }
  .map-icon__button {
    width: 20px;
    height: 20px;
    position: fixed;
    top: 15px;
    z-index: 9;
    right: 35px;
    display: block;
  }

  .map-icon__img {
    width: 100%;
    height: 100%;
  }

  .map__wrap,
  .events__wrap {
    width: 100%;
  }
  .map__wrap {
    bottom: 0;
    height: auto;
  }
  .map-hide {
    bottom: auto;
    height: auto;
    background: papayawhip;
    width: 100%;
    height: 46px;
    /* box-shadow: 0 2px 3px rgba(0, 0, 0, 0.1); */
    box-sizing: border-box;
    padding: 7px 0 9px;
    z-index: 0;
  }
  .map-hide input {
    left: 20px;
    top: 0;
  }
  .hide {
    display: none;
  }
  .events__wrap {
    padding: 80px 20px 20px;
  }
}
</style>
