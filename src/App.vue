<template>
  <div id="app">
    <!--
  -->
    <a-scene
      renderer="antialias: false;
                   colorManagement: true;
                   sortObjects: true;
                   physicallyCorrectLights: false;
                   maxCanvasWidth: 256;
                   maxCanvasHeight: 256;"
    >
      <Boid v-for="Boid in boidData" :bData="Boid" :key="Boid.index" />

      <a-sky color="#8ddfff"></a-sky>
    </a-scene>
  </div>
</template>

<script>
import Boid from "./components/Boid";
import aframe from "aframe";

export default {
  name: "App",
  data() {
    return {
      totalBoids: 30,
      maxSize: 20,
      boidSpeed: 0.1,
      boidTurnSpeed: 2,
      boidData: [
        {
          index: 0,
          px: 0,
          py: 1,
          pz: -3,
          rx: 0,
          ry: 0,
          rz: 0,
          targetPx: 0,
          targetPy: 0,
          targetPz: 0,
          targetRx: 0,
          targetRy: 0,
          targetRz: 0,
          colour: "red",
          data: {},
        },
      ],
    };
  },
  components: {
    Boid,
  },
  methods: {
    createBoids() {
      for (let i = 1; i < this.totalBoids; i++) {
        this.boidData.push({
          index: i,
          px: -this.maxSize / 2 + Math.floor(Math.random() * this.maxSize),
          py: -this.maxSize / 2 + Math.floor(Math.random() * this.maxSize),
          pz: -this.maxSize / 4 - Math.floor(Math.random() * this.maxSize),
          rx: Math.floor(Math.random() * 360),
          ry: Math.floor(Math.random() * 360),
          rz: Math.floor(Math.random() * 360),
          targetPx:
            -this.maxSize / 2 + Math.floor(Math.random() * this.maxSize),
          targetPy:
            -this.maxSize / 2 + Math.floor(Math.random() * this.maxSize),
          targetPz:
            -this.maxSize / 2 + Math.floor(Math.random() * this.maxSize),
          targetRx: Math.floor(Math.random() * 360),
          targetRy: Math.floor(Math.random() * 360),
          targetRz: Math.floor(Math.random() * 360),
          colour: "red",
          data: this.boidData,
        });
      }
    },
    distanceBetweenPoints(p1, p2) {
      var a = p2.x - p1.x;
      var b = p2.y - p1.y;
      var c = p2.z - p1.z;

      let ans = Math.sqrt(a * a + b * b + c * c);
      if (ans === 0) {
        return 999999;
      } else {
        return ans;
      }
    },
    turnBoid(currentAngle, targetAngle) {
      if (targetAngle - currentAngle > 20) {
        if (targetAngle - currentAngle > 180)
          currentAngle += this.boidTurnSpeed;
        else currentAngle -= this.boidTurnSpeed;
      }
      return currentAngle;
    },

    normalizeAngle(angle) {
      if (angle > 360) return angle - 360;
      if (angle < 0) return 360 + angle;
      else return angle;
    },
    angleBetweenPoints(p1, p2) {
      var dy = p1.z - p2.z;
      var dx = p1.x - p2.x;
      var thetaX = Math.atan2(dy, dx);
      thetaX *= 180 / Math.PI;
      //thetaX -= 90;
      thetaX = Math.floor(this.normalizeAngle(thetaX));

      dy = p1.z - p2.z;
      dx = p1.y - p2.y;
      var thetaY = Math.atan2(dy, dx);
      thetaY *= 180 / Math.PI;
      //thetaY += 180;
      thetaY = Math.floor(this.normalizeAngle(thetaY));

      dy = p1.x - p2.x;
      dx = p1.y - p2.y;
      var thetaZ = Math.atan2(dy, dx);
      thetaZ *= 180 / Math.PI;
      thetaZ -= 180;
      thetaZ = Math.floor(this.normalizeAngle(thetaZ));
      return { x: thetaX, y: thetaY, z: thetaZ };

      // (180 / Math.PI)
    },
    findMinimum(array) {
      return Math.min.apply(Math, array);
    },
    findClosestBoid(index) {
      var disData = [];
      var p1 = [];
      for (let i = 0; i < this.boidData.length; i++) {
        if (i === index) {
          p1 = {
            x: this.boidData[i].px,
            y: this.boidData[i].py,
            z: this.boidData[i].pz,
          };
        }
      }

      for (let i = 0; i < this.boidData.length; i++) {
        var p2 = {
          x: this.boidData[i].px,
          y: this.boidData[i].py,
          z: this.boidData[i].pz,
        };

        disData[i] = this.distanceBetweenPoints(
          { x: p1.x, y: p1.y, z: p1.z },
          { x: p2.x, y: p2.y, z: p2.z }
        );
      }
      //disData = this.boidData.length;
      //const minimumDisIndexNot0 = (element) =>
      //  (element = );
      let minVal = this.findMinimum(disData);
      let minValIndex = 0;

      for (let index = 0; index < disData.length; index++) {
        //const element = array[index];
        if (disData[index] === minVal) {
          minValIndex = index;
        }
      }
      return minValIndex; //disData.findIndex(minimumDisIndexNot0);
    },
    updateBoids() {
      this.boidData.forEach((element) => {
        //console.log();
        const targetBoid = this.boidData[this.findClosestBoid(element.index)];
        const p1 = { x: element.px, y: element.py, z: element.pz };
        const p2 = { x: targetBoid.px, y: targetBoid.py, z: targetBoid.pz };
        const targetDir = this.angleBetweenPoints(p1, p2);

        //element.rx = this.turnBoid(element.rx, targetDir.x);
        //// element.ry = this.turnBoid(element.ry, targetDir.y);
        // element.rz = this.turnBoid(element.rz, targetDir.z);

        element.targetRx = targetDir.x;
        element.targetRy = targetDir.y;
        element.targetRz = targetDir.z;

        let mX = p2.x - p1.x;
        let mY = p2.y - p1.y;
        let mZ = p2.z - p1.z;

        element.targetPx = mX * 2; //targetBoid.px / 100;
        element.targetPy = mY * 2; //targetBoid.py / 100;
        element.targetPz = mZ * 2; //targetBoid.pz / 100;
        //console.log(element);
      });
    },
  },
  beforeMount() {
    this.createBoids();
  },
  mounted() {
    //window.setInterval(() => {
    //   this.updateBoids();
    //  }, 1000);
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
