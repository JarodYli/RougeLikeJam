<!--------------------------------------------------------------------------------------------
 ---------------------------------------TEMPLATE ---------------------------------------------
--------------------------------------------------------------------------------------------->
<template>
  <div >

    <!-- Social Actions -->
    <!-- <v-circle @click="talk()" :config="talkButton" /> -->

    <v-text :config="player" ref="player"/>

    <!-- Characters and Enemies -->
    <!-- <enemy></enemy> -->

    <!-- <Character :CharacterInfo="CharacterInfo"></Character> -->
    <!-- <v-text :config="characterSpeech" /> -->
  </div>
</template>



<script>
//  ******************************************************************************************************************************
//                                                   SCRIPT
// *******************************************************************************************************************************

//  ******************************************************************************************************************************
//                                                 DEPENDENCIES
// *******************************************************************************************************************************
// import Character from "./Character.vue";
import Enemy from "./Enemy.vue";

export default {

  name: "Player",
  components: {
    Enemy: Enemy,
    // Character: Character
  },
  props: {
    HomeInfo: Object,
    Perimeter: Object,
    boundaries: Array,
    waterArray: Array,
    irrigationArray: Array,
    plantArray: Array,
    steps: Array,
    hoeArray: Array,

    inventoryArray: Array,
  },
// *******************************************************************************************************************************
//                                                       DATA
// *******************************************************************************************************************************
  data() {
    return {
      interval: null,
      filters:[Konva.Filters.Blur],
      plantID: 0,


// ****************************              PLAYER SETTINGS
      playerBoundaries: this.boundaries,

      player: {
        x: this.HomeInfo.x + 20,
        y: this.HomeInfo.y,
        fontSize: 25,
        text: "\u0FC7",
        fill: "#FF5A1E",
        listening: false,
        perfectDrawEnabled: false
      },

      offSet: 20, // --- off sets player image to look more center to tile

      playerLevel: false,
      onPlatform: false,
      

      // ****************************                 dCHARACTER DATA
      // CharacterInfo: {
      //   x: 200,
      //   y: 520,
      //   name: "Rich Hoagie",
      //   replies: [
      //     "",
      //     "I am not feeling very well today.",
      //     "Maybe I'll find time to fix the tank some time this week."
      //   ]
      // },

    };
  },
// *******************************************************************************************************************************
//                                             BOTTOM OF DATA
// *******************************************************************************************************************************

  created() {

    this.player.onload = () => {
      this.$nextTick(() => {
        const node = this.$refs.player.getStage();
        node.cache();
        node.getLayer().batchDraw();
      })
  }
  },
  mounted() {
    console.log('player Z', this.player)
    // this.playerBoundaries.push(this.CharacterInfo);

    // this.characterSpeech.x = this.CharacterInfo.x + 18;
    // this.characterSpeech.y = this.CharacterInfo.y - 10;
     this.interval = setInterval(() => {
          this.whereIAm();

        }, 110);
  },
  computed: {},



// *******************************************************************************************************************************
//                                                       METHODS
// *******************************************************************************************************************************
  methods: {

    // ****************************              PLAYER MOVEMENT
    moveUp() {

        if (this.player.y != this.Perimeter.yUp) {
          var boundaryFound = this.playerBoundaries.find(
            playerBoundaries =>
            playerBoundaries.x == this.player.x &&
            playerBoundaries.y + this.offSet == this.player.y
        );
          // Checks to see if water is the boundary. Allows walls and platforms to be generated next to water
          if(boundaryFound){
            if (boundaryFound.type == "water" ) {
              
              var wallFound = this.playerBoundaries.find(
                playerBoundaries =>
              playerBoundaries.x == this.player.x &&
              playerBoundaries.y + this.offSet == this.player.y &&
              playerBoundaries.type == 'wall');

              if(wallFound && this.playerLevel != false){
                console.log("A wall has been found.")
                this.playerLevel = true;
                return this.player.y -= this.offSet
              }
              else {
                return "Block: invalid boundary type: water";
              }
            }
          }

        var stepFound = this.steps.find(
          steps => steps.x == this.player.x && steps.y + this.offSet == this.player.y
        );

        if (stepFound) {
          this.player.y -= this.offSet
          this.playerLevel = null
          console.log("stepFound: ", this.playerLevel);
          return null
        }

        if ((boundaryFound) && (this.playerLevel == true || this.playerLevel == null)) {
          this.playerLevel = true;
          console.log("Player moved North");
          // console.log("playerBoundaries Array", this.playerBoundaries);

          this.player.y -= this.offSet;
          // this.PlayerLog.text = "x:" + this.player.x + " y:" + this.player.y;

          console.log("On Platform:", this.playerLevel);
          console.log("level 2");
          return "movement completed";
        }

          if (!boundaryFound && this.playerLevel == true) {
            // this.playerLevel = false
          console.log("is the playe on a platform?", this.onPlatform)
          console.log("Player Level Platform block:", this.playerLevel);

          // this.player.y -= this.offSet;
          // this.PlayerLog.text = "x:" + this.player.x + " y:" + this.player.y;
          return null
        }


        if (!boundaryFound && (this.playerLevel == false || this.playerLevel == null)) {
          this.playerLevel = false
          console.log("Player Level ground main:", this.playerLevel);
          // console.log("playerBoundaries Array", this.playerBoundaries);

          this.player.y -= this.offSet;
          // this.PlayerLog.text = "x:" + this.player.x + " y:" + this.player.y;
        }
      }
    },
    moveDown() {
      if (this.player.y != this.Perimeter.yDown) {
        var boundaryFound = this.playerBoundaries.find(
          playerBoundaries =>
            playerBoundaries.x == this.player.x &&
            playerBoundaries.y - this.offSet == this.player.y
        );

         // Checks to see if water is the boundary. Allows walls and platforms to be generated next to water
         if(boundaryFound){
            if (boundaryFound.type == "water" ) {

              var wallFound = this.playerBoundaries.find(
              playerBoundaries =>
              playerBoundaries.x == this.player.x &&
              playerBoundaries.y - this.offSet == this.player.y &&
              playerBoundaries.type == 'wall');

              if(wallFound && this.playerLevel != false){
                console.log("A wall has been found.")
                this.playerLevel = true;
                return this.player.y += this.offSet
              }
              else {
                return "Block: invalid boundary type: water";
              }
            }
          }

        var stepFound = this.steps.find(
          steps => steps.x == this.player.x && steps.y - this.offSet == this.player.y
        );

        if (stepFound) {
           this.player.y += this.offSet
          this.playerLevel = null
          console.log("stepFound:", this.playerLevel);
          return null
        }

        if ((boundaryFound) && (this.playerLevel == true || this.playerLevel == null)) {
          this.playerLevel = true
          console.log("Player moved North");
          // console.log("playerBoundaries Array", this.playerBoundaries);

          this.player.y += this.offSet;
          // this.PlayerLog.text = "x:" + this.player.x + " y:" + this.player.y;

          console.log("On Platform:", this.playerLevel);
          console.log("level 2");
          return "movement completed";
        }

          if (!boundaryFound && this.playerLevel == true) {
          // this.playerLevel = false
          console.log("is the playe on a platform?", this.onPlatform)
          console.log("Player Level Platform block:", this.playerLevel);

          // this.player.y -= this.offSet;
          // this.PlayerLog.text = "x:" + this.player.x + " y:" + this.player.y;
          return null
        }


        if (!boundaryFound && (this.playerLevel == false || this.playerLevel == null)) {
          this.playerLevel = false
          console.log("Player Level ground main:", this.playerLevel);
          // console.log("playerBoundaries Array", this.playerBoundaries);

          this.player.y += this.offSet;
          // this.PlayerLog.text = "x:" + this.player.x + " y:" + this.player.y;
        }
      }
    },
    moveRight() {
      if (this.player.x != this.Perimeter.xRight) {
        var boundaryFound = this.playerBoundaries.find(
          playerBoundaries =>
            playerBoundaries.x - this.offSet == this.player.x &&
            playerBoundaries.y == this.player.y);

        // Checks to see if water is the boundary. Allows walls and platforms to be generated next to water
         if(boundaryFound){
            if (boundaryFound.type == "water" ) {

              var wallFound = this.playerBoundaries.find(
              playerBoundaries =>
              playerBoundaries.x - this.offSet == this.player.x &&
              playerBoundaries.y == this.player.y &&
              playerBoundaries.type == 'wall');

              if(wallFound && this.playerLevel != false){
                console.log("A wall has been found.:",this.playerLevel)
                this.playerLevel = true;
                return this.player.x += this.offSet
              }
              else {
                return "Block: invalid boundary type: water";
              }
            }
          }

        var stepFound = this.steps.find(
          steps => steps.x - this.offSet == this.player.x && steps.y == this.player.y
        );

        if (stepFound) {
           this.player.x += this.offSet
          this.playerLevel = null
          console.log("stepFound:", this.playerLevel);
          return null
        }

        if ((boundaryFound) && (this.playerLevel == true || this.playerLevel == null)) {
          this.playerLevel = true
          console.log("Player moved North");
          // console.log("playerBoundaries Array", this.playerBoundaries);

          this.player.x += this.offSet;
          // this.PlayerLog.text = "x:" + this.player.x + " y:" + this.player.y;

          console.log("On Platform:", this.playerLevel);
          console.log("level 2");
          return "movement completed";
        }

          if (!boundaryFound && this.playerLevel == true) {
          // this.playerLevel = false
          console.log("is the playe on a platform?", this.onPlatform)
          console.log("Player Level Platform block:", this.playerLevel);

          // this.PlayerLog.text = "x:" + this.player.x + " y:" + this.player.y;
          return null
        }


        if (!boundaryFound && (this.playerLevel == false || this.playerLevel == null)) {
          this.playerLevel = false
          console.log("Player Level ground main:", this.playerLevel);
          // console.log("playerBoundaries Array", this.playerBoundaries);

          this.player.x += this.offSet;
          // this.PlayerLog.text = "x:" + this.player.x + " y:" + this.player.y;
        }
      }
    },
    moveLeft() {
      if (this.player.x != this.Perimeter.xLeft) {
        var boundaryFound = this.playerBoundaries.find(
          playerBoundaries =>
            playerBoundaries.x + this.offSet == this.player.x &&
            playerBoundaries.y == this.player.y);


          // Checks to see if water is the boundary. Allows walls and platforms to be generated next to water
          if(boundaryFound){
            if (boundaryFound.type == "water" ) {

              var wallFound = this.playerBoundaries.find(
              playerBoundaries =>
              playerBoundaries.x + this.offSet == this.player.x &&
              playerBoundaries.y == this.player.y &&
              playerBoundaries.type == 'wall');

              if(wallFound && this.playerLevel != false){
                console.log("A wall has been found.")
                this.playerLevel = true;
                return this.player.x -= this.offSet
              }
              else {
                return "Block: invalid boundary type: water";
              }
            }
          }

        var stepFound = this.steps.find(
          steps => steps.x + this.offSet == this.player.x && steps.y == this.player.y
        );

        if (stepFound) {
           this.player.x -= this.offSet
          this.playerLevel = null
          console.log("stepFound:", this.playerLevel);
          return null
        }

        if ((boundaryFound) && (this.playerLevel == true || this.playerLevel == null)) {
          this.playerLevel = true
          console.log("Player moved North");
          // console.log("playerBoundaries Array", this.playerBoundaries);

          this.player.x -= this.offSet;
          // this.PlayerLog.text = "x:" + this.player.x + " y:" + this.player.y;

          console.log("On Platform:", this.playerLevel);
          console.log("level 2");
          return "movement completed";
        }

          if (!boundaryFound && this.playerLevel == true) {
          // this.playerLevel = false
          console.log("is the playe on a platform?", this.onPlatform)
          console.log("Player Level Platform block:", this.playerLevel);

          // this.player.y -= this.offSet;
          // this.PlayerLog.text = "x:" + this.player.x + " y:" + this.player.y;
          return null
        }


        if (!boundaryFound && (this.playerLevel == false || this.playerLevel == null)) {
          this.playerLevel = false
          console.log("Player Level ground main:", this.playerLevel);
          // console.log("playerBoundaries Array", this.playerBoundaries);

          this.player.x -= this.offSet;
          // this.PlayerLog.text = "x:" + this.player.x + " y:" + this.player.y;
        }
      }
    },
    // ****************************              BOTTOM OF PLAYER MOVEMENT METHODS


    // ****************************              CONSTRUCTION METHODS
    buildT(){


       this.$emit("buildHere", {
          x: this.player.x,
          y: this.player.y - this.offSet,
          type:'wall'          
        });

    },
    buildB(){
       this.$emit("buildHere", {
          x: this.player.x ,
          y: this.player.y + this.offSet,
          type:'wall'          
        });

    },
    buildL(){
       this.$emit("buildHere", {
          x: this.player.x - this.offSet,
          y: this.player.y,
          type:'wall'          
        });

    },
    buildR(){
       this.$emit("buildHere", {
          x: this.player.x + this.offSet,
          y: this.player.y,
          type:'wall'          
        });

    },
// ****************************              BOTTOM OF CONSTRUCTION METHODS

feedFire(){
  if (
    this.player.x == this.HomeInfo.x + 20  && this.player.y == this.HomeInfo.y ||
    this.player.x == this.HomeInfo.x - 20 && this.player.y == this.HomeInfo.y ||
    this.player.x == this.HomeInfo.x && this.player.y == this.HomeInfo.y + 20 ||
    this.player.x == this.HomeInfo.x && this.player.y == this.HomeInfo.y - 20 
    )
   {
    this.$emit("feedFire");
  }

},

// ****************************                      GRAB METHODS

grab(){
  var itemFound = (itemFound = this.hoeArray.find(
    item => (item.x == this.player.x && item.y == this.player.y)
  ))

  if (itemFound){
    this.$emit('pickUpItem', {itemFound})
  }
  else if (!itemFound){
    console.log(this.inventoryArray)
    var inventoryFound = (inventoryFound = this.inventoryArray.find(
      item => (item.type == 'hoe')
    ))
    console.log("inve:" ,inventoryFound)
    this.$emit("putDown", {
      inventoryFound
    })
  }
},

// ****************************                   AGRICULTURE METHODS
    plant() {
      var plantFound = (plantFound = this.plantArray.find(
        plant =>
          (plant.x == this.player.x && plant.y == this.player.y) ||
          (plant.x == this.player.x + this.offSet && plant.y == this.player.y) ||
          (plant.x == this.player.x - this.offSet && plant.y == this.player.y) ||
          (plant.x == this.player.x && plant.y == this.player.y + this.offSet) ||
          (plant.x == this.player.x && plant.y == this.player.y - this.offSet) ||
          (plant.x == this.player.x + this.offSet && plant.y == this.player.y - this.offSet) ||
          (plant.x == this.player.x - this.offSet && plant.y == this.player.y + this.offSet) ||
          (plant.x == this.player.x + this.offSet && plant.y == this.player.y + this.offSet) ||
          (plant.x == this.player.x - this.offSet && plant.y == this.player.y - this.offSet)
      ));

    

      var waterFoundLeft = this.waterArray.find(
        water => water.x + this.offSet == this.player.x && water.y == this.player.y
      );
      var waterFoundRight = this.waterArray.find(
        water => water.x - this.offSet == this.player.x && water.y == this.player.y
      );

      var waterFoundUp = this.waterArray.find(
        water => water.x == this.player.x && water.y + this.offSet == this.player.y
      );
      var waterFoundDown = this.waterArray.find(
        water => water.x == this.player.x && water.y - this.offSet == this.player.y
      );

      var irrigatationFoundHere = this.irrigationArray.find(
        water => water.x == this.player.x && water.y == this.player.y
      );

      var irrigatationFoundLeft = this.irrigationArray.find(
        water => water.x + this.offSet == this.player.x && water.y == this.player.y
      );
      var irrigatationFoundRight = this.irrigationArray.find(
        water => water.x - this.offSet == this.player.x && water.y == this.player.y
      );
      var irrigatationFoundUp = this.irrigationArray.find(
        water => water.x == this.player.x && water.y + this.offSet == this.player.y
      );
      var irrigatationFoundDown = this.irrigationArray.find(
        water => water.x == this.player.x && water.y - this.offSet == this.player.y
      );

      if (
        (waterFoundLeft ||
          waterFoundRight ||
          waterFoundUp ||
          waterFoundDown ) && (!irrigatationFoundHere)
      ) {
        // console.log("irrgationFounHere:", irrigatationFoundHere)

        console.log("Player is planting.....");

        this.$emit("plantHere", {
          id: "player Planted" + this.plantID,
          x: this.player.x,
          y: this.player.y,
          seedChance: Math.random() * 100,
          maturity: 'seed',
        });
        this.plantID++;
        return 'Planted in not ideal soil.'
      } 

     
      if((irrigatationFoundLeft ||
          irrigatationFoundRight ||
          irrigatationFoundUp ||
          irrigatationFoundDown ) && (!irrigatationFoundHere)){
          
          this.$emit("plantHere", {
            id: "player Planted" + this.plantID,
            x: this.player.x,
            y: this.player.y,
            seedChance: Math.random() * 100,
            maturity: 'seed',
            soil: 'ideal'
        });
        this.plantID++;
        return 'Planted ideal soil.'
          }
           if (plantFound || irrigatationFoundHere) {
        console.log(
          "There is not enough water in the soil to plant another plant here."
        );
        return "Can not plant here!"
        }
          else {
            
        console.log("Player is planting.....");

        this.$emit("plantHere", {
          id: "player Planted" + this.plantID,
          x: this.player.x,
          y: this.player.y,
          maturity: 'seed',
          seedChance: Math.random() * 100,
          soil: 'ideal'
        });
        this.plantID++;
      }
    },

    irrigate() {
      var hoeFound = this.inventoryArray.find(item => item)
      console.log("hoeFound:", hoeFound)

      if(hoeFound){
      var waterFoundLeft = this.waterArray.find(
        water => water.x + this.offSet == this.player.x && water.y == this.player.y
      );
      var waterFoundRight = this.waterArray.find(
        water => water.x - this.offSet == this.player.x && water.y == this.player.y
      );
      var waterFoundUp = this.waterArray.find(
        water => water.x == this.player.x && water.y + this.offSet == this.player.y
      );
      var waterFoundDown = this.waterArray.find(
        water => water.x == this.player.x && water.y - this.offSet == this.player.y
      );

      var irrigatationFoundLeft = this.irrigationArray.find(
        water => water.x + this.offSet == this.player.x && water.y == this.player.y
      );
      var irrigatationFoundRight = this.irrigationArray.find(
        water => water.x - this.offSet == this.player.x && water.y == this.player.y
      );
      var irrigatationFoundUp = this.irrigationArray.find(
        water => water.x == this.player.x && water.y + this.offSet == this.player.y
      );
      var irrigatationFoundDown = this.irrigationArray.find(
        water => water.x == this.player.x && water.y - this.offSet == this.player.y
      );

      if (
        waterFoundLeft ||
        waterFoundRight ||
        irrigatationFoundLeft ||
        irrigatationFoundRight
      ) {
        console.log("Player is irrigating......");

        this.$emit("irrigateHere", {
          id: this.plantID,
          x: this.player.x,
          y: this.player.y,
          orientation: "horizontal"
        });
        this.plantID++;
      } else if (
        waterFoundUp ||
        waterFoundDown ||
        irrigatationFoundUp ||
        irrigatationFoundDown
      ) {
        this.$emit("irrigateHere", {
          id: this.plantID,
          x: this.player.x,
          y: this.player.y,
          orientation: "veritcal"
        });
        this.plantID++;
      } else {
        console.log("No water found.");
      }
      }
      else {console.log('No Hoe to dig with.')}
    },
    harvest() {
      if (
        this.plantArray.find(
          plant => plant.x === this.player.x && plant.y === this.player.y
        )
      ) {
        var plantToHarvest = this.plantArray.find(
          plant => plant.x === this.player.x && plant.y === this.player.y
        );
        console.log("The plant to harvest", plantToHarvest);
        this.$emit("harvestHere", plantToHarvest);
        console.log("Harvesting....");
      } else {
        console.log("nothing to harvest");
        console.log("plantArray", this.plantArray);
      }
    },
// ****************************              BOTTOM OF AGRICULTURE METHODS

// ****************************              SOCIAL METHODS
    talk() {
      if (
        (this.player.x == this.CharacterInfo.x + this.offSet &&
          this.player.y == this.CharacterInfo.y) ||
        (this.player.x == this.CharacterInfo.x - this.offSet &&
          this.player.y == this.CharacterInfo.y) ||
        (this.player.x == this.CharacterInfo.x &&
          this.player.y == this.CharacterInfo.y + this.offSet) ||
        (this.player.x == this.CharacterInfo.x &&
          this.player.y == this.CharacterInfo.y - this.offSet)
      ) {
        console.log("Talking to player");
        this.characterSpeech.text = this.CharacterInfo.replies[1];
        this.interval = setInterval(() => {
          this.characterSpeech.text = this.CharacterInfo.replies[0];
          clearInterval(this.interval);
        }, 3000);
      }
    },

    whereIAm(){
       this.$emit("playerLocation", {x:this.player.x, y:this.player.y})
    }

// *******************************************************************************************************************************
//                                                BOTTOM OF METHODS
// *******************************************************************************************************************************
  }
};
</script>