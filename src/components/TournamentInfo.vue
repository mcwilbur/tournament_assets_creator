<template>
  <div id="tournamentInfo">
    <div id="userInput" class="container">
      <div class="row">
        <div class="col">
          <div id="tournamentInput" class="form-group">
            <label class="form-label">Tournament Name</label>
            <input class="form-control" type="text" v-model="TournamentName" />
            <label class="form-label">Tournament date</label>
            <input
              class="form-control"
              type="datetime-local"
              v-model="TournamentDate"
            />
            <label class="form-label">Game</label>
            <select
              class="form-select"
              v-model="TournamentGame"
              @change="gameChanged()"
            >
              <option value="" selected disabled>Please chose a game</option>
              <option
                v-for="(game, key) in TournamentGames"
                v-bind:key="key"
                :value="key"
              >
                {{ game }}
              </option>
            </select>
            <label class="form-label">Type</label>
            <select
              class="form-select"
              v-model="TournamentType"
              :disabled="TournamentGame === ''"
            >
              <option :value="{ name: '', logo: '' }" selected disabled>
                Please chose a tournament type
              </option>
              <option
                v-for="(type, index) in TournamentTypes[TournamentGame]"
                v-bind:key="index"
                :value="type"
              >
                {{ type.name }}
              </option>
            </select>
            <label class="form-label">Format</label>
            <select
              class="form-select"
              v-model="TournamentFormat"
              :disabled="TournamentType === ''"
            >
              <option value="" selected disabled>---</option>
              <option
                v-for="(format, index) in TournamentFormats[TournamentGame]"
                v-bind:key="index"
                :value="format"
              >
                {{ format }}
              </option>
            </select>
            <label class="form-label">Round style</label>
            <select class="form-select" v-model="TournamentStyle">
              <option value="" selected disabled>
                Please chose a round style
              </option>
              <option
                v-for="(style, index) in TournamentStyles"
                v-bind:key="index"
                :value="style"
              >
                {{ style }}
              </option>
            </select>
          </div>
        </div>
        <div class="col">
          <div id="styleInput" class="form-group">
            <label class="form-label">Background Image</label>
            <input
              class="form-control"
              type="text"
              v-model="CardBackgroundImage"
              @paste="onPaste"
            />
            <label class="form-label">Image format</label>
            <select class="form-select" v-model="ImageStyle">
              <option
                v-for="(format, index) in ImageStyles"
                :key="index"
                :value="index"
              >
                {{ format }}
              </option>
            </select>
            <label class="form-label">Background Color</label>
            <select class="form-select" v-model="BackgroundColor">
              <option
                v-for="(color, index) in BackgroundColors"
                :key="index"
                :value="color"
              >
                {{ color }}
              </option>
            </select>
          </div>
        </div>
      </div>
      <div class="row">
        <div>
          <button class="btn btn-primary" @click="generateImage()">
            Generate Image
          </button>
        </div>
      </div>
      <div id="card">
        <div
          id="cardContainer"
          :class="{ FBTwitterCard: isFBFormat, IGStoryCard: isIGFormat }"
        >
          <div
            id="cardImage"
            :class="{ FBTwitterImage: isFBFormat, IGStoryImage: isIGFormat }"
            ref="card"
          >
            <img id="gameImage" :src="getGameImage" />
            <img id="gameType" :src="getImage(TournamentType.logo, 'types')" />
            <img
              id="backgroundImage"
              :src="getBackgroundImage"
              :title="CardBackgroundImage"
              :alt="CardBackgroundImage"
            />
            <div class="blur"></div>
            <h2 id="formatLabel">Format :</h2>
            <h1 id="format">{{ TournamentFormat }}</h1>
          </div>
          <div
            id="cardText"
            :class="{ FBTwitterText: isFBFormat, IGStoryText: isIGFormat }"
            ref="card"
          >
            <!--img
              id="textBackgroundImage"
              src="@/assets/images/background_TCGVS.jpg"
            /-->
            <h1 id="name">{{ TournamentName }}</h1>
            <h1 id="date">{{ getFormatedDateTime }}</h1>
            <h1 id="style">{{ TournamentStyle }}</h1>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import domtoimage from "dom-to-image";
import { saveAs } from "file-saver";
import moment from "moment";
export default {
  name: "TournamentInfo",
  data() {
    return {
      TournamentName: "LCh April 2022",
      TournamentDate: "2022-04-09T13:30",
      TournamentGame: "",
      TournamentType: { name: "", logo: "" },
      TournamentFormat: "",
      TournamentStyle: "BO1 Swiss",
      TournamentGames: {
        PKMN: "Pokémon",
        YGO: "Yu Gi Oh",
        WSS: "Weiss & Schwarz",
      },
      TournamentTypes: {
        PKMN: [
          { name: "Avant-première", logo: "PKMN_Challenge.png" },
          { name: "League Challenge", logo: "" },
          { name: "League Cup", logo: "" },
          { name: "Level Ball", logo: "" },
          { name: "Amical", logo: "" },
        ],
        YGO: ["WCQ", "OTS", "Sneak", "Advanced", "Legacy", "No-meta"],
        WSS: [],
      },
      TournamentFormats: {
        PKMN: ["Scellé", "Standard", "Étendu", "GLC", "Draft", "Draft Mutante"],
        YGO: ["WCQ", "OTS", "Sneak", "Advanced", "Legacy", "No-meta"],
        WSS: [],
      },
      TournamentStyles: ["BO1 Swiss", "BO3 Swiss + TopX", "Single Elimination"],
      CardBackgroundImage: "",
      ImageStyle: 0,
      ImageStyles: ["Facebook & Twitter", "Instagram Story"],
      BackgroundColor: "transparent",
      BackgroundColors: ["transparent", "white", "black", "light grey", "grey"],
    };
  },
  computed: {
    getGameImage() {
      if (this.TournamentGame === "")
        return "data:image/gif;base64,R0lGODlhAQABAAD/ACwAAAAAAQABAAACADs=";
      return require(`../assets/images/games/logo_${this.TournamentGame}.png`);
    },
    getBackgroundImage() {
      if (this.CardBackgroundImage === "")
        return "data:image/gif;base64,R0lGODlhAQABAAD/ACwAAAAAAQABAAACADs=";
      return this.CardBackgroundImage;
    },
    isFBFormat() {
      if (this.ImageStyle === 0) return true;
      else return false;
    },
    isIGFormat() {
      if (this.ImageStyle === 1) return true;
      else return false;
    },
    getFormatedDateTime() {
      return moment(this.TournamentDate)
        .locale("fr-ch")
        .format("DD MMMM @ HH:mm");
    },
    getFooterHeight()
    {
      if (this.isFBFormat) return 100
      return 50
    }
  },
  methods: {
    gameChanged() {
      this.TournamentType = { name: "", logo: "" };
      this.TournamentFormat = "";
    },
    getImage(imageName, imagePath) {
      if (imageName === "")
        return "data:image/gif;base64,R0lGODlhAQABAAD/ACwAAAAAAQABAAACADs=";
      if (imagePath === "") return require(`..//assets/images/${imageName}`);
      return require(`..//assets/images/${imagePath}/${imageName}`);
    },
    generateImage() {
      domtoimage
        .toBlob(document.getElementById("cardContainer"))
        .then(function (blob) {
          saveAs(blob, "card.png");
        })
        .catch(function () {
          alert(
            "Unable to generate image.\nThis tool does not support images from external domains."
          );
        });
    },
    onPaste(pasteEvent) {
      let items = pasteEvent.clipboardData.items;
      for (var i = 0; i < items.length; i++) {
        if (items[i].type.indexOf("image") !== -1) {
          var blob = items[i].getAsFile();
          this.CardBackgroundImage = window.URL.createObjectURL(blob);
          break;
        }
      }

    },
  },
};
</script>

<style scoped lang="scss">

$footer_height: calc(v-bind(getFooterHeight)*1px);


.FBTwitterCard {
  display: flex;
  flex-direction: row;
  width: 1200px;
  height: 628px;
}
.FBTwitterImage {
  width: 600px;
  height: 628px;
}
.FBTwitterText {
  width: 600px;
  height: 628px;
}
.IGStoryCard {
  display: flex;
  flex-direction: column;
  width: 720px;
  height: 1280px;
}
.IGStoryImage {
  width: 720px;
  height: 640px;
}
.IGStoryText {
  width: 720px;
  height: 640px;
}
#tournamentInfo {
  margin: 15px;

  h1 {
    width: 100%;
    position: absolute;
    font-size: 56px;
    z-index: 2;
    text-shadow: 3px 3px 5px grey;
    color: black;
    -webkit-text-fill-color: white; /* Will override color (regardless of order) */
    -webkit-text-stroke-width: 2px;
    -webkit-text-stroke-color: black;
  }

  h2 {
    width: 100%;
    position: absolute;
    font-size: 48px;
    z-index: 2;
    text-shadow: 3px 3px 5px grey;
    color: black;
    -webkit-text-fill-color: white; /* Will override color (regardless of order) */
    -webkit-text-stroke-width: 2px;
    -webkit-text-stroke-color: black;
  }

  #name {
    top: 10px;
    left: 10px;
    font-weight: bold;
  }
  #date {
    top: 80px;
    left: 10px;
  }
  #type {
    bottom: 10px;
    left: 10px;
  }
  #format {
    bottom: calc($footer_height + 10px);;
    left: 320px;
  }
  #formatLabel {
    bottom: calc($footer_height + 50px);;
    left: 320px;
  }
  #style {
    bottom: 10px;
    right: 10px;
    text-align: right;
  }
}
#cardText {
  position: relative;
  background-color: v-bind(BackgroundColor);
}

#card {
  display: inline-block;
  padding: 20px;
  background-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAIAAAACCAYAAABytg0kAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAABZJREFUeNpi2r9//38gYGAEESAAEGAAasgJOgzOKCoAAAAASUVORK5CYII=);
}

#cardContainer {
  margin: 0px;
}

#textBackgroundImage {
  z-index: 1;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

#cardImage {
  #backgroundImage {
    z-index: 1;
    width: 100%;
    height: 100%;
    object-fit: cover;


  }

      .blur {
      z-index: 5;
      content: "hello";
      position: absolute;
      bottom: 0px;
      left: 0px;
      height: $footer_height;
      width: 100%;
      backdrop-filter: blur(50px);
    }

  #gameImage {
    position: absolute;
    top: 10px;
    margin-left: auto;
    margin-right: auto;
    right: 0;
    left: 0;
    right: 0;
    text-align: center;
  }

  #gameType {
    position: absolute;
    bottom: calc($footer_height + 10px);
    left: 10px;
  }

  position: relative;

  //background-color: white;
}
</style>