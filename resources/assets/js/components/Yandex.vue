<template>
<transition name="fade">
    <div class="page-wrap">
        <div class="map__wrap">
            <date-picker
            class="map-date"
            v-model="searchDay"
            @input="searchEvents"
            ></date-picker>
            <div class="leaflet__wrap">
                <l-map id="map" ref="map"
                    :zoom="12"
                    :center="[47.80, 35.165]">
                    <l-tile-layer :url="url" :attribution = "attribution"></l-tile-layer>
                    <l-marker v-for="marker in this.placemarkers" :key="marker.id" :lat-lng="marker.coords">
                        <l-icon iconUrl="./images/markers/map-marker.png" class="icon"></l-icon>
                        <l-tooltip>
                            <div class="label">
                                <div class="label__image">
                                    <img :src="'/images/'+marker.image" alt="">
                                </div>
                                <div class="label__text ">
                                    <div class="label__title">{{marker.title}}</div>
                                    <div class="label__description">Описание: {{short(marker.description, 50)}}</div>
                                    <div class="label__location">Место: {{marker.location}}</div>
                                    <div class="label__date">Время: {{marker.date}} <br/> {{marker.time}}</div>
                                </div>
                            </div>
                        </l-tooltip>
                    </l-marker>
                </l-map>
            </div>
        </div>
        <div class="events__wrap">
            <div class="events">
                <div class="events__title">
                    Все события в городе Запорожье {{searchDay}}.
                </div>
                <div class="event-block" v-for="event in this.publishedDailyEvents" :key="event.event_id">
                    <div class="event-image__wrap">
                        <div class="event-image">
                            <img class="event-image__img" :src = "'/images/'+event.event_image" alt="">
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
    import { LMap, LTileLayer, LMarker, LTooltip, LIcon} from 'vue2-leaflet';
    import L from 'vue2-leaflet';
    import DatePicker from './lib/input/DatePicker'

    export default {
        name: "Yandex",
        components: {
            DatePicker,
            LMap, LTileLayer, LMarker, LTooltip, LIcon
            },

        data(){
            return{
                searchDay: '',
                url:'http://{s}.tile.osm.org/{z}/{x}/{y}.png',
                attribution:'&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
            }
        },
        created:
            function () {
                this.axios.get("events/published")
                    .then(resp =>{
                        let events = resp.data;
                        this.searchDay = events[0].event_date;
                        this.$store.commit('loadPublishedDailyEvents', events);
                    }).catch(err => {
                    console.log(err);
                });

                // const apiGetCurrentGeoPosition = () => {
                //     let userPosition;
                //     const geoOptions = {
                //         maximumAge: 5 * 60 * 1000,
                //         timeout: 5000
                //     }
                //     const geoSuccess = (position) => {
                //         const {latitude, longitude } = position.coords;
                //     }
                //     const geoError = (error) => {
                //         const errorMessage = document.createElement('div');
                //         errorMessage.classList.add('message');
                //         switch (error.code) {
                //             case 0:
                //                 errorMessage.innerHTML = '<span>Oops, something being wrong. Please, try again!</span>';
                //                 break;
                //             case 1:
                //                 errorMessage.innerHTML = '<span>Access is denied!</span>';
                //                 break;
                //             case 2:
                //                 errorMessage.innerHTML = '<span>Unable to locate device. Please, try again!</span>';
                //                 break;
                //             case 3:
                //                 errorMessage.innerHTML = '<span>Waiting limit exceeded. Please, try again!</span>';
                //                 break;
                //         }
                //         errorMessageContainer.appendChild(errorMessage);
                //         hideSpinner(error);

                //     }
                //     navigator.geolocation.getCurrentPosition(geoSuccess, geoError, geoOptions);
                // }
                // apiGetCurrentGeoPosition();
            },
        computed: {
            //Работает только когда используется компонентом
            publishedDailyEvents: function() {
                console.log('computed', this.$store.getters.getPublishedDailyEvents);
                return this.$store.getters.getPublishedDailyEvents;
            },
            placemarkers: function(){
                let eventArray = [];
                let events = this.$store.getters.getPublishedDailyEvents;
                events.forEach(function(event){

                    let eventObj = {
                        id: event.event_id,
                        coords: [event.event_lat, event.event_long],
                        title: event.event_title,
                        description: event.event_description,
                        image:event.event_image,
                        location:event.event_location,
                        date : event.event_date,
                        time : event.event_time
                    };
                    eventArray.push(eventObj);
                });
                console.log('computed array', eventArray);
                return eventArray;
            },
        },
        methods:{
            searchEvents(){
                this.axios.get(`/events/published/${this.searchDay}`)
                    .then((response)=> {
                        const events = response.data;
                        this.$store.commit('loadPublishedDailyEvents', events);
                    })
                    .catch(function (error) {
                        console.log(error);
                    });
            },
            short(str, maxLen, separator = ' ') {
                if (str.length <= maxLen) return str;
                return str.substr(0, str.lastIndexOf(separator, maxLen));
            }
        }
    }
</script>

<style scoped>
@import '~leaflet/dist/leaflet.css';

.page-wrap{
    display: flex;
    width: 100%;
    height:calc(100vh - 60px);
}

.map__wrap{
    width:68%;
    position:relative;
    
}

.map-date{
    position: absolute;
    z-index: 999;
    right: 2%;
    margin-top: 11px;
}

.leaflet__wrap{
    width:100%;
    height: 100%;
}

.leaflet-marker-icon{
    width:35px !important;
    height:38px !important;
}

.label{
    display:flex;
    flex-direction: row;
}

.label__text{
    display:flex;
    flex-direction: column;
    padding-left: 5px;
    word-break: break-all;
    word-wrap:break-word;
    overflow-wrap: break-word;
}

.label__image{
    width:120px;
    height: auto;
}

.label__image img{
    width:100%;
    height: auto;
}

.label__title{
    font-size:14px;
    text-align: center;
}

.label__description{
    word-break: break-all;
    word-wrap:break-word;
    overflow-wrap: break-word;
}

.events__wrap{
    width:32%;
    height: auto;
    padding: 10px 20px 20px 20px;
}

.events{
    display:flex;
    flex-direction: column;
}

.events__title{
    width:100%;
    font-size:18px;
    text-align: center;
    font-weight: 600;
    font-family: Roboto, sans-serif;
    margin:0 0 10px 0;
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
.event-image__img{
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
.leaflet-container .leaflet-overlay-pane svg, .leaflet-container .leaflet-marker-pane img, .leaflet-container .leaflet-shadow-pane img, .leaflet-container .leaflet-tile-pane img, .leaflet-container img.leaflet-image-layer, .leaflet-container .leaflet-tile {
    /* max-width: none !important; */
    width: auto !important;
    max-height: none !important;
}

@media screen and (max-width: 768px) {
.page-wrap{
    flex-direction: column;
    height: 500px;
}
.map__wrap, .events__wrap{
    width:100%;
}
.map__wrap{
    height: 500px;
}
}
</style>
