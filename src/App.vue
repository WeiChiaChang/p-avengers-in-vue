<template>
  <div id="app">
    <div class="container">
      <Modal
        :scoreType="scoreType"
        :title="modal.title"
        :content="modal.content"
        :visible="showModal"
        @close="showModal = false"
      />
      <Overlay v-if="isLoading" />
      <!-- use the modal component, pass in the prop -->
      <Popup v-if="showPopup" @close="showPopup = false">
        <!--
          you can use custom content here to overwrite
          default content
        -->
        <h3 slot="header">最多只能選 {{currentAvengersFilterLength}} 個啾咪！</h3>
      </Popup>
      <audio ref="audioZero" :src="require('./assets/zero.mp3')"></audio>
      <audio ref="audioPart" :src="require('./assets/pika-fadachai.mp3')"></audio>
      <audio ref="audioAll" :src="require('./assets/aaron-smith.mp3')"></audio>
      <div class="box-container">
        <div class="box box-title">
          <p>
            選取圖片中含有
            <span>復仇者聯盟</span> 的圖片
          </p>
          <!-- <button @click="allScorePlay" type="button">Click Me to Toggle Sound</button> -->
        </div>
        <div
          v-for="(grid, index) in afterShuffledGrids"
          :key="index"
          :style="{ backgroundImage: 'url(' + require('./assets/' + grid.image_url) + ')' }"
          :class="`box box${grid.id} ${selectedGrids.includes(grid.id) ? 'box-selected': ''}`"
          @click="toggleSelectedGrids(grid.id)"
        >
          <!-- TEST ONLY -->
          <!-- <p style="background-color: rgba(255,0,0,.7); padding: 1px 2px; color: white;">{{grid.id}}</p> -->
          <!--  -->
          <div v-if="selectedGrids.includes(grid.id)" class="box-checkbox"></div>
        </div>
        <div class="box-footer">
          <div class="left-wrapper">
            <div @click="randomGenerateGrids(initialGrids)" class="icon icon-refresh"></div>
            <div @click.prevent="playSound" class="icon icon-listen"></div>
            <div class="icon icon-info"></div>
          </div>
          <div @click="toggleModal" class="right-wrapper">
            <div class="validation-btn">
              <div>驗證</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Overlay from './components/overlay'
import Popup from './components/popup'
import Modal from './components/modal'
import guessGrids from './guess.json'

export default {
  data() {
    return {
      initialGrids: guessGrids,
      afterShuffledGrids: [],
      selectedGrids: [],
      isLoading: false,
      modal: {
        title: '搞什麼東西啊？全錯！',
        content: '你真的有在上班嗎？想跟 Geoffrey 學瑜珈魔術嗎？',
      },
      showModal: false,
      showPopup: false,
      scoreType: 'part'
    }
  },
  components: {
    Overlay,
    Popup,
    Modal
  },
  name: 'app',
  computed: {
    currentAvengersFilter() {
      return this.afterShuffledGrids
        .filter(grid => grid.isAvengers)
        .map(grid => grid.id)
    },
    shouldNotMoreThan() {
      if (this.selectedGrids.length >= this.currentAvengersFilterLength) {
        return true
      }
      return false
    },
    currentAvengersFilterLength() {
      return this.currentAvengersFilter.length
    },
    intersectionAvengersLength() {
      return this.selectedGrids.filter(value => this.currentAvengersFilter.includes(value)).length
    }
  },
  watch: {
    showModal(newValue, oldValue) {
      if (newValue !== oldValue) {
        switch (this.scoreType) {
          case 'all':
            this.allScorePlay();
            break;
          case 'part':
            this.partScorePlay();
            break;
          default:
            this.zeroScorePlay();
            break;
        }
      }
    }
  },
  methods: {
    zeroScorePlay() {
      let audio = this.$refs.audioZero;
      if (audio.paused) {
        audio.play();
      } else {
        audio.pause();
        audio.currentTime = 0;
      }
    },
    partScorePlay() {
      let audio = this.$refs.audioPart;
      if (audio.paused) {
        audio.play();
      } else {
        audio.pause();
        audio.currentTime = 0;
      }
    },
    allScorePlay() {
      let audio = this.$refs.audioAll;
      if (audio.paused) {
        audio.play();
      } else {
        audio.pause();
        audio.currentTime = 0;
      }
    },
    toggleModal() {
      this.showModal = !this.showModal;
    },
    toggleSelectedGrids(id) {
      if (this.selectedGrids.includes(id)) {
        let index = this.selectedGrids.indexOf(id)
        this.selectedGrids.splice(index, 1)
      } else {
        if (this.shouldNotMoreThan) {
          return this.showPopup = true
        }

        this.selectedGrids.push(id)
      }

      switch (this.intersectionAvengersLength / this.currentAvengersFilterLength) {
        case 0:
          this.scoreType = 'zero'
          this.modal.title = '搞什麼東西啊？全錯！'
          this.modal.content = '你真的有在上班嗎？想跟 Geoffrey 學瑜珈魔術嗎？'
          break;
        case 1:
          this.scoreType = 'all'
          this.modal.title = 'Ｊ個是 ... 喔喔全對！'
          this.modal.content = '對了，我們最近剛把技術長外包出去，想調薪？哈哈哈哈 ...'
          break;
        default:
          this.scoreType = 'part'
          this.modal.title = `${this.currentAvengersFilterLength} 中 ${this.intersectionAvengersLength}，是社畜呢！`
          this.modal.content = '你是不是想要挑戰 Kevin 的十天最速離職傳說？'
          break;
      }

      // 交集
      // console.log(this.selectedGrids.filter(value => this.currentAvengersFilter.includes(value)))
      // eslint-disable-next-line no-console
      // console.log(`${this.currentAvengersFilterLength} 中 ${this.intersectionAvengersLength}`)
    },
    randomGenerateGrids(data) {
      var temp = []
      while (temp.length !== 9) {
        let randomItem = data[Math.floor(Math.random() * (data.length))]
        if (!temp.includes(randomItem)) {
          temp.push(randomItem)
        }
      }
      // Shuffle it
      for (let i = temp.length - 1; i > 0; i--) {
        let j = Math.floor(Math.random() * (i + 1));
        [temp[i], temp[j]] = [temp[j], temp[i]];
      }

      this.afterShuffledGrids = temp
      this.selectedGrids = []
      this.$forceUpdate()

      this.isLoading = true

      setTimeout(() => {
        this.isLoading = false
      }, 1000)

      // eslint-disable-next-line no-console
      // console.log(this.currentAvengersFilter)

      return this.afterShuffledGrids
    },
    playSound() {
      let audio = new Audio(require('./assets/你眼睛瞎了嗎.mp3'));
      audio.play();
    }
  },
  mounted() {
    this.afterShuffledGrids = this.randomGenerateGrids(this.initialGrids)
  }
}
</script>

<style lang="scss">
body {
  background-color: black;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  // overflow:hidden;
}

.container {
  position: relative;
  background-color: white;
  width: 385px;
  // height: 595px;
  // margin: 0 auto;
  // margin-top: 1em;
  .box-container {
    // border: 2px solid black;
    display: flex;
    flex-wrap: wrap;
    max-width: 380px;
    margin: 0 auto;
  }
  .box {
    flex: 1 1 calc(33.3333% - 8px);
    margin: 4px;
    background: #e9d2ff;
    background-repeat: no-repeat;
    background-position: center center;
    background-size: cover;
    position: relative;
    &.box-selected {
      -webkit-transform: scale(0.9);
      -moz-transform: scale(0.9);
      -ms-transform: scale(0.9);
      -o-transform: scale(0.9);
      transform: scale(0.9);
      transition: 0.1s ease;
    }
    .box-checkbox {
      display: block;
      width: 30px;
      height: 30px;
      background-repeat: no-repeat;
      bottom: 0;
      left: -12px;
      right: 0;
      top: -12px;
      -webkit-transform: scale(1.1);
      -moz-transform: scale(1.1);
      -ms-transform: scale(1.1);
      -o-transform: scale(1.1);
      transform: scale(1.1);
      position: absolute;
      background: url("./assets/icon/check-box.png");
    }
  }
  .box:after {
    content: "";
    display: block;
    padding-bottom: 100%;
  }
  .box-title {
    height: 130px;
    flex-basis: 100%;
    background: #4a90e2;
    margin-top: 8px;
    p {
      color: #ffffff;
      font-size: 1.2rem;
      padding: 1rem;
      span {
        font-weight: bolder;
      }
    }
  }
  .box-footer {
    order: 2;
    height: 50px;
    flex-basis: 100%;
    background: #ffffff;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px 4px;
    border-radius: 4px;
    .left-wrapper {
      display: flex;
      .icon {
        margin: 0 3px;
        background-size: 32px 32px;
        cursor: pointer;
        height: 24px;
        opacity: 0.55;
        width: 24px;
        padding: 0;
        border: 0;
        background-repeat: no-repeat;
        background-position: center;
        &.icon-refresh {
          background: url("./assets/icon/refresh_black.png");
        }
        &.icon-listen {
          background: url("./assets/icon/audio_black.png");
        }
        &.icon-info {
          background: url("./assets/icon/info_black.png");
        }
      }
    }
    .right-wrapper {
      .validation-btn {
        background-color: #4a90e2;
        padding: 8px 30px;
        font-size: 14px;
        color: #ffffff;
        cursor: pointer;
      }
    }
  }
}
</style>
