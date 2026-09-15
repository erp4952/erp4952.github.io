<!doctype html>
<html lang="th">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Epona AR</title>
<style>body { margin: 0; overflow: hidden; }</style>

<script src="https://cdn.jsdelivr.net/npm/aframe@1.6.0/dist/aframe-master.min.js"></script>
<script src="https://cdn.jsdelivr.net/gh/AR-js-org/AR.js@3.4.8/aframe/build/aframe-ar.js"></script>
<script>
// ย่อ/วางโมเดลให้พอดี marker แล้วเล่นอนิเมชันแรก  (แก้ 1.8 = ความกว้างโมเดล)
AFRAME.registerComponent('fit', {
  init() {
    this.el.addEventListener('model-loaded', e => {
      const model = e.detail.model, THREE = AFRAME.THREE;
      const box = new THREE.Box3().setFromObject(model);
      const size = box.getSize(new THREE.Vector3());
      const center = box.getCenter(new THREE.Vector3());
      model.position.set(-center.x, -box.min.y, -center.z);
      this.el.object3D.scale.setScalar(1.8 / Math.max(size.x, size.y, size.z));
      if (model.animations.length) {
        this.mixer = new THREE.AnimationMixer(model);
        this.mixer.clipAction(model.animations[0]).play();
      }
    });
  },
  tick(time, dt) { if (this.mixer) this.mixer.update(dt / 1000); }
});
</script>
</head>

<body>
<a-scene embedded vr-mode-ui="enabled: false" loading-screen="enabled: false"
  arjs="sourceType: webcam; debugUIEnabled: false; cameraParametersUrl: https://cdn.jsdelivr.net/gh/AR-js-org/AR.js@3.4.8/data/data/camera_para.dat;">

  <a-marker preset="hiro" smooth="true">
    <a-entity gltf-model="https://sibsansuk.github.io/epona.glb" fit></a-entity>
  </a-marker>

  <a-entity camera></a-entity>
</a-scene>

<p style="position:fixed; bottom:8px; left:10px; margin:0; padding:4px 8px; border-radius:6px;
   background:#000a; color:#fff; font:12px system-ui, Tahoma, sans-serif;">
  <a href="https://raw.githubusercontent.com/AR-js-org/AR.js/master/data/images/HIRO.jpg" target="_blank" style="color:#9ef">marker</a>
  · Epona by
  <a href="https://sketchfab.com/3d-models/epona-1f1da2940b0d4ddcb4beae1680c47918" target="_blank" style="color:#9ef">Vasian-Digital3D</a>
  · <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" style="color:#9ef">CC BY 4.0</a>
</p>
</body>
</html>
