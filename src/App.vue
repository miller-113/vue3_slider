<template>
  <div>
    <carousel ref="mainCarousel" :itemsToShow="4" :wrapAround="true" :transition="500">
      <slide v-for="(video, index) in videos" :key="index">
        <img style="height: 200px; width: 200px; background-color: aquamarine;" :src="video.thumbnail" @click="openPopup(video, index)" />
      </slide>

      <template #addons>
        <Pagination />
      </template>
    </carousel>

    <!-- Обычная пагинация -->
    <div class="pagination">
      <vue-awesome-paginate
        :total-items="videos.length"
        v-model="currentPage"
        :items-per-page="4"
        :max-pages-shown="5"
        :show-breakpoint-buttons="false"
        :show-jump-buttons="true"
      />
    </div>

    <template v-if="popupVisible">
      <div class="popup-overlay">
        <div class="popup">
          <vueVimeoPlayer class="iframeCont" :video-id="currentVideo.id" autoplay />
              <!-- Пагинация для открытия видео во всплывающем окне -->
          <div class="popup-pagination">
            <div v-for="(video, index) in videos" :key="index" @click="openPopup(video, index)" :class="{ active: index === currentVideoIndex }"></div>
          </div>
          <button class="closeBtn" @click="closePopup">X</button>
        </div>
      </div>
    </template>
  </div>
</template>

<script>
import 'vue3-carousel/dist/carousel.css';
import { Carousel, Slide, Pagination } from 'vue3-carousel';
import { vueVimeoPlayer } from 'vue-vimeo-player';
import axios from 'axios';
import VueAwesomePaginate from "vue-awesome-paginate";

export default {
  name: 'App',
  components: {
    Carousel,
    Slide,
    Pagination,
    vueVimeoPlayer,
    VueAwesomePaginate
  },
  data() {
    return {
      videos: [
        { id: '824804225', thumbnail: '' },
        { id: '524933864', thumbnail: '' },
        { id: '515302320', thumbnail: '' },
        { id: '437802063', thumbnail: '' },
        { id: '435680298', thumbnail: '' },
      ],
      popupVisible: false,
      currentVideo: null,
      currentVideoIndex: null,
    };
  },
  methods: {
    openPopup(video, index) {
      this.currentVideo = video;
      this.popupVisible = true;
      this.currentVideoIndex = index;
    },
    closePopup() {
      this.popupVisible = false;
    },
  },
  created() {
    this.videos.forEach(video => {
      axios.get(`https://vimeo.com/api/v2/video/${video.id}.json`)
        .then(response => {
          const videoData = response.data[0];
          // Присваиваем данные о миниатюре для каждого видео
          video.thumbnail = videoData.thumbnail_large;
          // Можно также присвоить другие свойства видео, если нужно
        })
        .catch(error => {
          console.error(`Error fetching data for video with ID ${video.id}:`, error);
        });
    });
  }
};
</script>

<style scoped>
.popup-pagination {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

.popup-pagination div {
  width: 10px;
  height: 10px;
  background-color: #ccc;
  border-radius: 50%;
  margin: 0 5px;
  cursor: pointer;
}



.popup-pagination .active {
  background-color: #333;
}

.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.popup {
  background-color: white;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
  text-align: center;
}



.carousel__slide {
  padding: 5px;
}

.carousel__viewport {
  perspective: 2000px;
}

.carousel__track {
  transform-style: preserve-3d;
}

.carousel__slide--sliding {
  transition: 0.5s;
}

.carousel__slide {
  opacity: 0.9;
  transform: rotateY(-20deg) scale(0.9);
}

.carousel__slide--active ~ .carousel__slide {
  transform: rotateY(20deg) scale(0.9);
}

.carousel__slide--prev {
  opacity: 1;
  transform: rotateY(-10deg) scale(0.95);
}

.carousel__slide--next {
  opacity: 1;
  transform: rotateY(10deg) scale(0.95);
}

.carousel__slide--active {
  opacity: 1;
  transform: rotateY(0) scale(1.1);
}

.popup {
  height: 100%;
  width: 100%;
  background-color: rgba(0, 0, 0, .85);
}
.closeBtn{
  border-color: chartreuse;
  position: absolute;
  right: 20px;
  border-radius: 50%;
  background-color: black;
  border: 1px solid darkblue;
  color: white;
  top: 20px;
  font-weight: 800;
  cursor: pointer;
}
.iframeCont{
  width: 100%;
  height: 85%;
}

</style>
<style>

  iframe{
    height: 100%;
  }
</style>
