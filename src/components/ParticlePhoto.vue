<template>
  <div class="particle-photo">
    <h1>{{ msg }}</h1>
    <canvas>
  </div>
</template>

<script>
import THREE from 'three';

console.log(THREE);
export default {

  data () {
    return {
      // note: changing this line won't causes changes
      // with hot-reload because the reloaded component
      // preserves its current state and we are modifying
      // its initial state.
      msg: 'Particle Photo!',
      particleCount: 10000
    };
  },
  ready () {
    const canvas = this.$el.getElementsByTagName('canvas')[0];

    this.scene = new THREE.Scene();
    this.camera = new THREE.PerspectiveCamera(75, canvas.clientWidth / canvas.clientHeight, 1, 10000);
    this.camera.position.z = 0;

    const geometry = new THREE.BoxGeometry(200, 200, 200);
    const material = new THREE.MeshBasicMaterial({ color: 0xff0000, wireframe: true });

    this.mesh = new THREE.Mesh(geometry, material);
    this.scene.add(this.mesh);

    this.renderer = new THREE.WebGLRenderer({
      canvas: canvas
    });
    this.renderer.setSize(canvas.clientWidth, canvas.clientHeight);
    this.createParticles();
    this.animate();
  },
  methods: {
    render () {
      this.renderer.render(this.scene, this.camera);
    },
    animate () {
      window.requestAnimationFrame(this.animate);
      let seconds = (new Date()).valueOf() / 1000;
      this.camera.rotation.x = -Math.cos(seconds);
      this.camera.rotation.y = -Math.sin(seconds);

      this.camera.rotation.z = 0.0 * Math.PI;

      this.camera.position.x = 300 * Math.cos(seconds);
      this.camera.position.y = 300 * Math.sin(seconds);
      // this.particleSystem.rotation.x += 0.01;
      this.render();
    },
    createParticles () {
      const texture = new THREE.TextureLoader().load('static/vangogh.jpg');
      texture.wrapS = THREE.RepeatWrapping;
      texture.wrapT = THREE.RepeatWrapping;
      // texture.repeat.set(4, 4);
      const particles = new THREE.Geometry();
      const material = new THREE.PointsMaterial({
        size: Math.random() * 3,
        color: 0x33FFFFFF,
        transparent: true,
        alphaTest: 0.4
      });
            // now create the individual particles
      for (var p = 0; p < this.particleCount; p++) {
        // create a particle with random
        // position values, -250 -> 250
        let pX = Math.random() * 500 - 250;
        let pY = Math.random() * 500 - 250;
        let pZ = Math.random() * 500 - 250;
        let particle = new THREE.Vector3(pX, pY, pZ);
        // add it to the geometry
        particles.vertices.push(particle);
      }
      // create the particle system
      this.particleSystem = new THREE.Points(
        particles,
        material
      );

      // add it to the scene
      this.scene.add(this.particleSystem);
    }
  }

};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1 {
  color: #42b983;
}
canvas {
  width: 500px;
  height: 300px;
}
</style>
