<template>
  <div id="app">
    <div class="container">
      <div class="box-container">
        <div class="box box-title">
          <p>
            選取圖片中含有
            <span>復仇者聯盟</span>的圖片
          </p>
        </div>
        <div
          v-for="(grid, index) in afterShuffledGrids"
          :key="index"
          :style="{ backgroundImage: 'url('+require('./assets/' + grid.image_url) + ')' }"
          :class="`box box${grid.id} ${selectedGrids.includes(grid.id) ? 'box-selected': ''}`"
          @click="toggleSelectedGrids(grid.id)"
        >
          <div v-if="selectedGrids.includes(grid.id)" class="box-checkbox"></div>
        </div>
        <div class="box-footer">
          <div class="left-wrapper">
            <div @click="randomGenerateGrids(initialGrids)" class="icon icon-refresh"></div>
            <div class="icon icon-listen"></div>
            <div class="icon icon-info"></div>
          </div>
          <div class="right-wrapper">
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
import guessGrids from './guess.json'

export default {
  data() {
    return {
      initialGrids: guessGrids,
      afterShuffledGrids: [],
      selectedGrids: []
    }
  },
  name: 'app',
  methods: {
    toggleSelectedGrids(id) {
      if (this.selectedGrids.includes(id)) {
        let index = this.selectedGrids.indexOf(id)
        this.selectedGrids.splice(index, 1)
      } else {
        this.selectedGrids.push(id)
      }
    },
    randomGenerateGrids(data) {
      var temp = []
      while (temp.length !== 9) {
        let randomItem = data[Math.floor(Math.random() * (data.length - 1))]
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
      this.$forceUpdate()
      return this.afterShuffledGrids
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
  // overflow:hidden;
}

.container {
  background-color: white;
  width: 385px;
  height: 620px;
  margin: 0 auto;
  margin-top: 1em;
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
  // .box001 {
  //   background-image: url('./assets/001_square_bill.jpg');
  // }
  // .box002 {
  //   background-image: url('./assets/002_square_daniel.jpg');
  // }
  // .box003 {
  //   background-image: url('./assets/003_square_danny.png');
  // }
  // .box004 {
  //   background-image: url('./assets/004_square_geoferry.png');
  // }
  // .box005 {
  //   background-image: url('./assets/005_square_jennifer.jpg');
  // }
  // .box006 {
  //   background-image: url('./assets/006_square_kevin.jpg');
  // }
  // .box007 {
  //   background-image: url('./assets/007_square_pascal.jpg');
  // }
  // .box008 {
  //   background-image: url('./assets/008_square_peter.png');
  // }
  // .box009 {
  //   background-image: url('./assets/009_square_rocoo.jpg');
  // }
  // .box010 {
  //   background-image: url('./assets/010_square_ryan.jpg');
  // }
  // .box011 {
  //   background-image: url('./assets/011_square_winston.jpg');
  // }
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
