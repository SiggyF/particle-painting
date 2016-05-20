<template>
  <div class="particle-photo">
    <h1>{{ msg }}</h1>
    <canvas>
  </div>
</template>

<script>
import THREE from 'three';
import d3 from 'd3';

export default {

  data () {
    return {
      // note: changing this line won't causes changes
      // with hot-reload because the reloaded component
      // preserves its current state and we are modifying
      // its initial state.
      msg: 'Particle Photo!',
      particleCount: 100000
    };
  },
  ready () {
    const canvas = this.$el.getElementsByTagName('canvas')[0];

    this.scene = new THREE.Scene();
    this.camera = new THREE.PerspectiveCamera(75, canvas.clientWidth / canvas.clientHeight, 1, 10000);
    this.camera.position.z = 300;

    const geometry = new THREE.BoxGeometry(100, 200, 200);
    const material = new THREE.MeshBasicMaterial({
      color: 0xffeeee,
      wireframe: true,
      opacity: 0.3,
      transparent: true
    });

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
      this.camera.position.x = 300 * Math.cos(seconds);
      this.camera.position.y = 300 * Math.sin(seconds);
      this.camera.lookAt(new THREE.Vector3(0, 0, 0));
      for (var particle of this.particleSystem.geometry.vertices) {
        particle.x = particle.color.a * 100;
        particle.y = particle.color.b * 100;
        particle.z = particle.color.l * 100;
      }
      this.particleSystem.geometry.verticesNeedUpdate = true;
      this.render();
    },
    createParticles () {
      console.log(d3);
      const particles = new THREE.Geometry();
      // now create the individual particles
      // Create individual colors
      var colors = [];
      for (var p = 0; p < this.particleCount; p++) {
        // random color
        var particle = new THREE.Vector3(pX, pY, pZ);
        let pX = Math.random() * 500 - 250;
        let pY = Math.random() * 500 - 250;
        let pZ = Math.random() * 500 - 250;
        colors[p] = new THREE.Color();
        colors[p].setHSL(Math.random(), Math.random(), Math.random());
        particle.color = d3.lab(colors[p].r, colors[p].g, colors[p].b);
        particles.vertices.push(particle);
      }
      console.log(particles.vertices[0]);
      particles.colors = colors;

      const material = new THREE.PointsMaterial({
        size: 5,
        opacity: 0.8,
        transparent: true,
        vertexColors: THREE.VertexColors
        // blending: THREE.AdditiveBlending
      });
      // create the particle system
      this.particleSystem = new THREE.Points(
        particles,
        material
      );
      // this.particleSystem.sortParticles = true;
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
  width: 600px;
  height: 400px;
}
</style>
