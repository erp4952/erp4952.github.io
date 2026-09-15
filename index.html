<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>3D Vehicle Portfolio & AR | Anuthep</title>
<script src="https://cdn.tailwindcss.com"></script>

<style>
    * { box-sizing: border-box; }
    html, body { margin:0; width:100%; height:100%; overflow:hidden; }
    body {
        background:#05070a;
        color:#e5e7eb;
        font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
    }

    /* Tab Switcher Styles */
    .tab-content { display: none; width: 100%; height: 100%; position: absolute; inset: 0; }
    .tab-content.active { display: block; }

    #canvas-container {
        position:fixed; inset:0; z-index:0;
        background:
            radial-gradient(circle at 72% 42%, rgba(16,185,129,.10), transparent 28%),
            radial-gradient(circle at 85% 80%, rgba(14,165,233,.07), transparent 24%),
            #05070a;
    }

    canvas { display:block; }

    .glass {
        background:rgba(8,12,17,.74);
        border:1px solid rgba(148,163,184,.16);
        box-shadow:0 20px 60px rgba(0,0,0,.42), inset 0 1px 0 rgba(255,255,255,.035);
        backdrop-filter:blur(18px);
        -webkit-backdrop-filter:blur(18px);
    }

    .glass-soft {
        background:rgba(15,23,42,.42);
        border:1px solid rgba(148,163,184,.12);
        backdrop-filter:blur(12px);
    }

    .btn {
        border:1px solid rgba(100,116,139,.28);
        background:rgba(15,23,42,.58);
        color:#cbd5e1;
        transition:.2s ease;
    }
    .btn:hover {
        transform:translateY(-1px);
        border-color:rgba(52,211,153,.65);
        color:#6ee7b7;
        background:rgba(16,185,129,.10);
    }
    .btn.active {
        border-color:#10b981;
        background:linear-gradient(135deg, rgba(16,185,129,.22), rgba(6,78,59,.20));
        color:#6ee7b7;
        box-shadow:0 0 20px rgba(16,185,129,.08);
    }

    .scanlines {
        position:fixed; inset:0; z-index:2; pointer-events:none;
        opacity:.045;
        background:repeating-linear-gradient(
            to bottom, transparent 0px, transparent 3px,
            rgba(255,255,255,.35) 4px
        );
    }

    .vignette {
        position:fixed; inset:0; z-index:1; pointer-events:none;
        background:radial-gradient(circle, transparent 40%, rgba(0,0,0,.38) 100%);
    }

    .corner {
        position:absolute; width:22px; height:22px; border-color:rgba(52,211,153,.55);
        pointer-events:none;
    }
    .corner.tl { left:-1px; top:-1px; border-left:1px solid; border-top:1px solid; }
    .corner.tr { right:-1px; top:-1px; border-right:1px solid; border-top:1px solid; }
    .corner.bl { left:-1px; bottom:-1px; border-left:1px solid; border-bottom:1px solid; }
    .corner.br { right:-1px; bottom:-1px; border-right:1px solid; border-bottom:1px solid; }

    .mono { font-family:ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, monospace; }
    .tiny { font-size:10px; letter-spacing:.14em; }
    .scroll::-webkit-scrollbar { width:4px; }
    .scroll::-webkit-scrollbar-thumb { background:rgba(52,211,153,.3); border-radius:99px; }

    @media (max-width: 900px) {
        .sidebar { width:320px !important; }
    }
    @media (max-width: 700px) {
        .sidebar { width:calc(100vw - 24px) !important; max-width:390px; }
        .right-panel { display:none !important; }
    }
</style>

<!-- Import Map for Three.js -->
<script type="importmap">
{
  "imports": {
    "three":"https://unpkg.com/three@0.160.0/build/three.module.js",
    "three/addons/":"https://unpkg.com/three@0.160.0/examples/jsm/"
  }
}
</script>

<!-- Scripts for AR.js -->
<script src="https://cdn.jsdelivr.net/npm/aframe@1.6.0/dist/aframe-master.min.js"></script>
<script src="https://cdn.jsdelivr.net/gh/AR-js-org/AR.js@3.4.8/aframe/build/aframe-ar.js"></script>

<script>
  // AR.js Component 'fit'
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
    tick(time, dt) { 
      if (this.mixer) this.mixer.update(dt / 1000); 
    }
  });
</script>
</head>

<body class="select-none">

  <!-- TOP MODE SWITCHER NAVBAR -->
  <nav class="fixed top-3 left-1/2 -translate-x-1/2 z-50 glass rounded-full p-1.5 flex gap-2 border border-emerald-500/30">
    <button id="nav-3d" onclick="switchTab('3d')" class="btn active px-4 py-1.5 rounded-full text-xs font-semibold flex items-center gap-2">
      <span>🎨</span> 3D Portfolio
    </button>
    <button id="nav-ar" onclick="switchTab('ar')" class="btn px-4 py-1.5 rounded-full text-xs font-semibold flex items-center gap-2">
      <span>📷</span> AR Camera Mode
    </button>
  </nav>

  <!-- TAB 1: 3D PORTFOLIO -->
  <div id="tab-3d" class="tab-content active">
    <div id="canvas-container"></div>
    <div class="scanlines"></div>
    <div class="vignette"></div>

    <div class="fixed inset-0 z-10 pointer-events-none p-3 md:p-5 pt-16">
        <!-- LEFT UI -->
        <aside class="sidebar pointer-events-auto w-[350px] h-full flex flex-col gap-3">
            <header class="glass rounded-2xl p-5 relative overflow-hidden">
                <span class="corner tl"></span><span class="corner tr"></span>
                <span class="corner bl"></span><span class="corner br"></span>

                <div class="flex items-center gap-3">
                    <div class="w-12 h-12 rounded-xl flex items-center justify-center
                        bg-emerald-950/70 border border-emerald-500/40 text-emerald-300
                        font-black tracking-widest shadow-[0_0_25px_rgba(16,185,129,.12)]">
                        AT
                    </div>
                    <div class="min-w-0">
                        <div class="tiny mono text-emerald-400 mb-1">3D VEHICLE PORTFOLIO</div>
                        <h1 class="font-bold text-lg text-white truncate">Anuthep Toeiliang</h1>
                        <p class="text-[11px] text-slate-400">Hard-Surface • Military • PBR</p>
                    </div>
                </div>

                <p class="mt-4 text-xs leading-relaxed text-slate-300">
                    แฟ้มผลงานโมเดล 3D เน้นงานยานพาหนะ ฮาร์ดเซอเฟส
                    โครงสร้างเชิงกล และวัสดุ PBR พร้อมระบบแสดงผลแบบ Interactive
                </p>

                <div class="mt-4 flex gap-2">
                    <div class="glass-soft rounded-lg px-3 py-2 flex-1">
                        <div class="tiny mono text-slate-500">STATUS</div>
                        <div class="text-xs text-emerald-300 mt-1 flex items-center gap-2">
                            <span class="w-1.5 h-1.5 rounded-full bg-emerald-400 animate-pulse"></span> ONLINE
                        </div>
                    </div>
                    <div class="glass-soft rounded-lg px-3 py-2 flex-1">
                        <div class="tiny mono text-slate-500">RENDER</div>
                        <div class="text-xs text-sky-300 mt-1">WEBGL / PBR</div>
                    </div>
                </div>
            </header>

            <section class="glass rounded-2xl p-4 scroll overflow-y-auto flex-1">
                <div class="flex items-center justify-between mb-3">
                    <h2 class="tiny mono font-bold text-emerald-400">MODEL SELECT</h2>
                    <span id="model-id" class="tiny mono text-slate-500">UNIT-01</span>
                </div>

                <div class="grid grid-cols-3 gap-2">
                    <button class="btn active rounded-xl p-2 text-left" id="model-tank">
                        <div class="text-lg">🛡️</div>
                        <div class="text-[10px] font-semibold mt-1">PORK TANK</div>
                    </button>
                    <button class="btn rounded-xl p-2 text-left" id="model-apc">
                        <div class="text-lg">🚙</div>
                        <div class="text-[10px] font-semibold mt-1">APC</div>
                    </button>
                    <button class="btn rounded-xl p-2 text-left" id="model-drone">
                        <div class="text-lg">✈️</div>
                        <div class="text-[10px] font-semibold mt-1">DRONE</div>
                    </button>
                </div>

                <div class="my-4 border-t border-slate-700/50"></div>

                <h2 class="tiny mono font-bold text-emerald-400 mb-3">CAMERA</h2>
                <div class="grid grid-cols-2 gap-2">
                    <button id="view-iso" class="btn active py-2.5 px-3 rounded-xl text-xs">Isometric</button>
                    <button id="view-side" class="btn py-2.5 px-3 rounded-xl text-xs">Side</button>
                    <button id="view-front" class="btn py-2.5 px-3 rounded-xl text-xs">Front</button>
                    <button id="view-top" class="btn py-2.5 px-3 rounded-xl text-xs">Top</button>
                </div>

                <div class="my-4 border-t border-slate-700/50"></div>

                <h2 class="tiny mono font-bold text-emerald-400 mb-3">DISPLAY</h2>
                <div class="grid grid-cols-2 gap-2">
                    <button id="mat-default" class="btn active py-2.5 px-3 rounded-xl text-xs">PBR Material</button>
                    <button id="mat-wireframe" class="btn py-2.5 px-3 rounded-xl text-xs">Wireframe</button>
                </div>

                <button id="btn-rotate" class="btn active w-full mt-2 py-2.5 px-3 rounded-xl text-xs flex justify-between items-center">
                    <span>Auto Rotation</span>
                    <span id="rotate-status" class="text-[10px] bg-emerald-500/15 text-emerald-300 px-2 py-1 rounded-md">ON</span>
                </button>

                <div class="my-4 border-t border-slate-700/50"></div>

                <h2 class="tiny mono font-bold text-emerald-400 mb-3">LIGHTING</h2>
                <div class="grid grid-cols-3 gap-2">
                    <button id="light-studio" class="btn active py-2 rounded-xl text-[10px]">Studio</button>
                    <button id="light-cyan" class="btn py-2 rounded-xl text-[10px]">Cyan</button>
                    <button id="light-red" class="btn py-2 rounded-xl text-[10px]">Red</button>
                </div>
            </section>

            <footer class="glass rounded-2xl p-3 pointer-events-auto">
                <label for="file-input" class="btn rounded-xl w-full py-3 cursor-pointer flex items-center justify-center gap-2 text-xs">
                    <span>📦</span> เปลี่ยน Model ของฉัน (.GLB / .GLTF / .FBX)
                </label>
                <input id="file-input" type="file" accept=".glb,.gltf,.fbx" class="hidden">
                <div class="text-[9px] text-slate-500 text-center mt-2">
                    ไฟล์ที่เลือกจะแสดงแทนโมเดลปัจจุบันในหน้าเว็บ
                </div>
            </footer>
        </aside>

        <!-- RIGHT HUD -->
        <div class="right-panel pointer-events-none absolute right-5 top-20 hidden lg:block">
            <div class="glass rounded-2xl p-4 w-64">
                <div class="tiny mono text-emerald-400">CURRENT UNIT</div>
                <div id="hud-name" class="text-xl font-bold text-white mt-1">PORK TANK</div>
                <div id="hud-desc" class="text-[11px] text-slate-400 mt-1">Experimental armored vehicle</div>

                <div class="mt-4 space-y-2">
                    <div class="flex justify-between text-[10px] mono"><span class="text-slate-500">POLY / DETAIL</span><span>HIGH</span></div>
                    <div class="h-1 bg-slate-800 rounded-full overflow-hidden"><div class="h-full w-[86%] bg-emerald-400"></div></div>
                    <div class="flex justify-between text-[10px] mono"><span class="text-slate-500">MATERIAL</span><span>PBR</span></div>
                    <div class="h-1 bg-slate-800 rounded-full overflow-hidden"><div class="h-full w-[94%] bg-sky-400"></div></div>
                    <div class="flex justify-between text-[10px] mono"><span class="text-slate-500">PRESENTATION</span><span>REALTIME</span></div>
                    <div class="h-1 bg-slate-800 rounded-full overflow-hidden"><div class="h-full w-[100%] bg-violet-400"></div></div>
                </div>

                <div class="mt-4 pt-3 border-t border-slate-700/50 flex justify-between text-[9px] mono text-slate-500">
                    <span>THREE.JS</span><span>INTERACTIVE</span>
                </div>
            </div>
        </div>

        <!-- bottom center hint -->
        <div class="absolute bottom-5 left-1/2 -translate-x-1/2 pointer-events-none">
            <div class="glass rounded-full px-4 py-2 text-[10px] mono text-slate-400">
                DRAG = ROTATE &nbsp; • &nbsp; WHEEL = ZOOM &nbsp; • &nbsp; CLICK MODEL = INSPECT
            </div>
        </div>
    </div>

    <div id="loading" class="fixed bottom-5 right-5 z-20 glass rounded-xl px-4 py-2 flex items-center gap-2">
        <span id="loading-dot" class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span>
        <span id="loading-text" class="tiny mono text-emerald-300">SCENE READY</span>
    </div>
  </div>

  <!-- TAB 2: AR CAMERA MODE -->
  <div id="tab-ar" class="tab-content">
    <a-scene 
      embedded 
      vr-mode-ui="enabled: false" 
      loading-screen="enabled: false"
      arjs="sourceType: webcam; debugUIEnabled: false; cameraParametersUrl: https://cdn.jsdelivr.net/gh/AR-js-org/AR.js@3.4.8/data/data/camera_para.dat;">

      <!-- Custom Pattern Marker -->
      <a-marker type="pattern" url="pattern-ดีไซน์ที่ยังไม่ได้ตั้งชื่อ (3).patt" smooth="true">
        <a-entity gltf-model="https://sibsansuk.github.io/epona.glb" fit></a-entity>
      </a-marker>

      <a-entity camera></a-entity>
    </a-scene>

    <!-- Credit Footer -->
    <p style="position:fixed; bottom:8px; left:10px; margin:0; padding:4px 8px; border-radius:6px;
     background:#000a; color:#fff; font:12px system-ui, Tahoma, sans-serif; z-index: 40;">
      <a href="https://aitutorialcourse.github.io/tracker.png" target="_blank" style="color:#9ef">marker image</a>
      · Epona by
      <a href="https://sketchfab.com/3d-models/epona-1f1da2940b0d4ddcb4beae1680c47918" target="_blank" style="color:#9ef">Vasian-Digital3D</a>
      · <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" style="color:#9ef">CC BY 4.0</a>
    </p>
  </div>

<!-- TAB SWITCHING LOGIC -->
<script>
  function switchTab(tab) {
    document.getElementById('tab-3d').classList.toggle('active', tab === '3d');
    document.getElementById('tab-ar').classList.toggle('active', tab === 'ar');
    
    document.getElementById('nav-3d').classList.toggle('active', tab === '3d');
    document.getElementById('nav-ar').classList.toggle('active', tab === 'ar');
  }
</script>

<!-- THREE.JS LOGIC -->
<script type="module">
import * as THREE from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';
import { FBXLoader } from 'three/addons/loaders/FBXLoader.js';

let scene, camera, renderer, controls;
let currentModel = null;
let modelType = 'tank';
let isAutoRotate = true;
let wireframe = false;
let currentLightMode = 'studio';

let mainLight, fillLight, rimLight;
let decorative = new THREE.Group();

init();
animate();

function init() {
    const container = document.getElementById('canvas-container');

    scene = new THREE.Scene();
    scene.background = new THREE.Color(0x05070a);
    scene.fog = new THREE.FogExp2(0x05070a, 0.035);

    camera = new THREE.PerspectiveCamera(42, innerWidth / innerHeight, 0.1, 1000);
    camera.position.set(5.5, 3.4, 6.5);

    renderer = new THREE.WebGLRenderer({ antialias:true, alpha:true });
    renderer.setSize(innerWidth, innerHeight);
    renderer.setPixelRatio(Math.min(devicePixelRatio, 2));
    renderer.outputColorSpace = THREE.SRGBColorSpace;
    renderer.toneMapping = THREE.ACESFilmicToneMapping;
    renderer.toneMappingExposure = 1.15;
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.type = THREE.PCFSoftShadowMap;
    container.appendChild(renderer.domElement);

    controls = new OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;
    controls.dampingFactor = .055;
    controls.minDistance = 2.2;
    controls.maxDistance = 18;
    controls.maxPolarAngle = Math.PI * .48;

    setupLights();
    createEnvironment();
    setupUIEvents();
    loadProcedural('tank');

    addEventListener('resize', onWindowResize);
}

function setupLights() {
    scene.add(new THREE.HemisphereLight(0x9fb7c9, 0x080b0e, 1.4));

    mainLight = new THREE.DirectionalLight(0xfff4df, 4.0);
    mainLight.position.set(6, 10, 7);
    mainLight.castShadow = true;
    mainLight.shadow.mapSize.set(2048,2048);
    mainLight.shadow.camera.left = -10;
    mainLight.shadow.camera.right = 10;
    mainLight.shadow.camera.top = 10;
    mainLight.shadow.camera.bottom = -10;
    scene.add(mainLight);

    fillLight = new THREE.DirectionalLight(0x38bdf8, 1.8);
    fillLight.position.set(-7, 5, -5);
    scene.add(fillLight);

    rimLight = new THREE.PointLight(0x10b981, 35, 12, 2);
    rimLight.position.set(0, 5, -3);
    scene.add(rimLight);
}

function createEnvironment() {
    const floor = new THREE.Mesh(
        new THREE.CircleGeometry(16, 96),
        new THREE.MeshStandardMaterial({ color:0x070b0f, roughness:.88, metalness:.15 })
    );
    floor.rotation.x = -Math.PI/2;
    floor.position.y = -.02;
    floor.receiveShadow = true;
    scene.add(floor);

    const grid = new THREE.GridHelper(30, 30, 0x1f4b43, 0x10201e);
    grid.position.y = 0;
    grid.material.transparent = true;
    grid.material.opacity = .38;
    scene.add(grid);

    for (let r of [2.3, 3.4, 5.0]) {
        const ring = new THREE.Mesh(
            new THREE.RingGeometry(r-.006, r+.006, 96),
            new THREE.MeshBasicMaterial({ color:0x10b981, transparent:true, opacity:r===3.4?.18:.08, side:THREE.DoubleSide })
        );
        ring.rotation.x = -Math.PI/2;
        ring.position.y = .015;
        decorative.add(ring);
    }

    for (const x of [-7, 7]) {
        const pillar = new THREE.Mesh(
            new THREE.BoxGeometry(.08, 5, .08),
            new THREE.MeshBasicMaterial({ color:0x10b981, transparent:true, opacity:.32 })
        );
        pillar.position.set(x,2.5,-1.5);
        decorative.add(pillar);
    }

    for (let i=0;i<14;i++) {
        const dot = new THREE.Mesh(
            new THREE.SphereGeometry(.025, 8, 8),
            new THREE.MeshBasicMaterial({ color:i%2?0x38bdf8:0x34d399 })
        );
        dot.position.set((Math.random()-.5)*14, 1.5+Math.random()*5, (Math.random()-.5)*10);
        decorative.add(dot);
    }
    scene.add(decorative);

    const pedestal = new THREE.Mesh(
        new THREE.CylinderGeometry(3.7, 4.2, .18, 96),
        new THREE.MeshStandardMaterial({ color:0x0c1419, roughness:.5, metalness:.65 })
    );
    pedestal.position.y=.08;
    pedestal.receiveShadow=true;
    pedestal.castShadow=true;
    scene.add(pedestal);

    const edge = new THREE.Mesh(
        new THREE.TorusGeometry(3.72, .018, 8, 128),
        new THREE.MeshBasicMaterial({color:0x10b981, transparent:true, opacity:.45})
    );
    edge.rotation.x=-Math.PI/2;
    edge.position.y=.18;
    scene.add(edge);
}

function mat(color, rough=.35, metal=.7) {
    return new THREE.MeshStandardMaterial({ color, roughness:rough, metalness:metal });
}

function box(group, size, pos, material, radius=0) {
    let geo = radius ? new THREE.BoxGeometry(size[0],size[1],size[2],4,4,4) : new THREE.BoxGeometry(...size);
    const m = new THREE.Mesh(geo, material);
    m.position.set(...pos);
    m.castShadow=m.receiveShadow=true;
    group.add(m);
    return m;
}

function createProceduralTank() {
    const g = new THREE.Group();
    const red = mat(0x9f1d24,.28,.72);
    const red2 = mat(0xd32f35,.25,.62);
    const dark = mat(0x111a20,.55,.88);
    const black = mat(0x05080b,.78,.45);
    const metal = mat(0x5b6770,.22,.95);

    box(g,[2.5,.72,3.25],[0,.72,0],red);
    box(g,[2.15,.28,2.2],[0,1.2,-.1],red2);

    const turret = new THREE.Mesh(new THREE.CylinderGeometry(.92,1.08,.62,24),red);
    turret.position.set(0,1.55,-.05); turret.castShadow=true; g.add(turret);

    const cannon = new THREE.Mesh(new THREE.CylinderGeometry(.13,.16,2.8,20),metal);
    cannon.rotation.x=Math.PI/2; cannon.position.set(0,1.56,1.35); cannon.castShadow=true; g.add(cannon);

    for (const x of [-1.48,1.48]) {
        box(g,[.42,.78,3.45],[x,.42,0],black);
        for(let i=0;i<7;i++){
            const wheel=new THREE.Mesh(new THREE.CylinderGeometry(.29,.29,.18,20),metal);
            wheel.rotation.z=Math.PI/2;
            wheel.position.set(x + (x>0?-.24:.24),.42,-1.25+i*.42);
            wheel.castShadow=true; g.add(wheel);
        }
    }

    const nose = new THREE.Mesh(new THREE.CylinderGeometry(.43,.43,.12,24),red2);
    nose.rotation.x=Math.PI/2; nose.position.set(0,1.56,1.63); g.add(nose);
    for (const x of [-.15,.15]) {
        const hole=new THREE.Mesh(new THREE.CylinderGeometry(.055,.055,.13,12),dark);
        hole.rotation.x=Math.PI/2; hole.position.set(x,1.56,1.69); g.add(hole);
    }

    const antenna = new THREE.Mesh(new THREE.CylinderGeometry(.025,.025,.8,8),metal);
    antenna.position.set(.72,2.05,-.45); g.add(antenna);
    const beacon = new THREE.Mesh(new THREE.SphereGeometry(.07,12,12),new THREE.MeshBasicMaterial({color:0x10b981}));
    beacon.position.set(.72,2.45,-.45); g.add(beacon);

    return g;
}

function createProceduralAPC() {
    const g=new THREE.Group();
    const body=mat(0x46545a,.42,.82), dark=mat(0x11171b,.65,.75), glass=mat(0x102d35,.16,.7);
    box(g,[2.9,1.05,4.1],[0,.8,0],body);
    box(g,[2.35,.72,2.1],[0,1.55,-.2],body);
    box(g,[2.05,.38,.9],[0,1.98,.55],glass);
    for(const x of [-1.55,1.55]){
        box(g,[.36,.9,4.2],[x,.48,0],dark);
        for(let i=0;i<6;i++){
            const w=new THREE.Mesh(new THREE.CylinderGeometry(.32,.32,.22,18),metalForAPC());
            w.rotation.z=Math.PI/2; w.position.set(x + (x>0?-.2:.2),.48,-1.25+i*.5); w.castShadow=true; g.add(w);
        }
    }
    const turret=new THREE.Mesh(new THREE.CylinderGeometry(.62,.75,.42,20),dark);
    turret.position.set(0,2.05,-.25); turret.castShadow=true; g.add(turret);
    const gun=new THREE.Mesh(new THREE.CylinderGeometry(.09,.11,2.0,16),metalForAPC());
    gun.rotation.x=Math.PI/2; gun.position.set(0,2.08,.8); g.add(gun);
    return g;
}
function metalForAPC(){ return mat(0x68757c,.28,.92); }

function createProceduralDrone() {
    const g=new THREE.Group();
    const body=mat(0x202a30,.25,.9), accent=mat(0x10b981,.25,.72), dark=mat(0x070a0d,.7,.65);
    const core=new THREE.Mesh(new THREE.SphereGeometry(.72,32,20),body);
    core.scale.set(1.25,.45,1.7); core.position.y=1.6; core.castShadow=true; g.add(core);

    for(const z of [-1,1]){
        const arm=new THREE.Mesh(new THREE.BoxGeometry(3.2,.16,.22),dark);
        arm.position.set(0,1.55,z*.65); arm.rotation.y=z*.15; arm.castShadow=true; g.add(arm);
        for(const x of [-1.25,1.25]){
            const motor=new THREE.Mesh(new THREE.CylinderGeometry(.25,.25,.3,20),dark);
            motor.position.set(x,1.72,z*.65); motor.castShadow=true; g.add(motor);
            const prop=new THREE.Mesh(new THREE.TorusGeometry(.55,.018,8,64),new THREE.MeshBasicMaterial({color:0x38bdf8,transparent:true,opacity:.55}));
            prop.position.set(x,1.9,z*.65); g.add(prop);
        }
    }
    const eye=new THREE.Mesh(new THREE.SphereGeometry(.12,16,16),new THREE.MeshBasicMaterial({color:0x10b981}));
    eye.position.set(0,1.5,1.42); g.add(eye);
    return g;
}

function loadProcedural(type) {
    if(type==='tank') setupLoadedModel(createProceduralTank(),'PORK TANK','Experimental armored vehicle','UNIT-01');
    if(type==='apc') setupLoadedModel(createProceduralAPC(),'APC','Armored personnel carrier','UNIT-02');
    if(type==='drone') setupLoadedModel(createProceduralDrone(),'RECON DRONE','Unmanned tactical vehicle','UNIT-03');
}

function loadModelFile(url) {
    setStatus('LOADING MODEL...');
    const ext=url.split('?')[0].split('.').pop().toLowerCase();
    const loader=ext==='fbx' ? new FBXLoader() : new GLTFLoader();

    loader.load(url,
        asset=>{
            const model=ext==='fbx' ? asset : asset.scene;
            setupLoadedModel(model,'CUSTOM MODEL','User supplied 3D asset','CUSTOM');
            setStatus('CUSTOM MODEL LOADED');
        },
        xhr=>{
            if(xhr.total) setStatus('LOADING '+Math.round(xhr.loaded/xhr.total*100)+'%');
        },
        err=>{
            console.error(err);
            setStatus('LOAD FAILED — USING PORK TANK');
            loadProcedural('tank');
        }
    );
}

function setupLoadedModel(model,name,desc,id) {
    if(currentModel) scene.remove(currentModel);
    currentModel=model;

    const box3=new THREE.Box3().setFromObject(model);
    const center=box3.getCenter(new THREE.Vector3());
    const size=box3.getSize(new THREE.Vector3());
    const maxDim=Math.max(size.x,size.y,size.z)||1;

    model.position.sub(center);
    model.position.y += size.y/2;
    model.scale.multiplyScalar(4.0/maxDim);

    model.traverse(child=>{
        if(child.isMesh){
            child.castShadow=true;
            child.receiveShadow=true;
            if(child.material){
                if(Array.isArray(child.material)) child.material.forEach(m=>m.side=THREE.FrontSide);
                else child.material.side=THREE.FrontSide;
            }
        }
    });

    scene.add(model);
    modelType=name.toLowerCase().includes('tank')?'tank':name.toLowerCase().includes('apc')?'apc':name.toLowerCase().includes('drone')?'drone':'custom';

    document.getElementById('hud-name').textContent=name;
    document.getElementById('hud-desc').textContent=desc;
    document.getElementById('model-id').textContent=id;

    setCamera('iso');
}

function setCamera(type) {
    if(!currentModel) return;
    const p={
        iso:[5.6,3.5,6.5],
        side:[6.8,2.0,0.1],
        front:[0.1,2.1,6.8],
        top:[0.1,8.2,.1]
    }[type]||[5.6,3.5,6.5];

    controls.target.set(0,1.15,0);
    camera.position.set(...p);
    controls.update();
}

function setActive(ids,target) {
    ids.forEach(id=>document.getElementById(id)?.classList.remove('active'));
    document.getElementById(target)?.classList.add('active');
}

function toggleWire(enable) {
    wireframe=enable;
    if(!currentModel)return;
    currentModel.traverse(c=>{
        if(c.isMesh && c.material){
            const materials=Array.isArray(c.material)?c.material:[c.material];
            materials.forEach(m=>m.wireframe=enable);
        }
    });
}

function setStatus(text){ document.getElementById('loading-text').textContent=text; }

function setLighting(mode) {
    currentLightMode=mode;
    setActive(['light-studio','light-cyan','light-red'],'light-'+mode);
    if(mode==='studio'){
        mainLight.color.set(0xfff4df); fillLight.color.set(0x38bdf8); rimLight.color.set(0x10b981);
    } else if(mode==='cyan'){
        mainLight.color.set(0xd9f8ff); fillLight.color.set(0x22d3ee); rimLight.color.set(0x06b6d4);
    } else {
        mainLight.color.set(0xffe4d5); fillLight.color.set(0xfb7185); rimLight.color.set(0xef4444);
    }
}

function setupUIEvents() {
    document.getElementById('model-tank').onclick=()=>{ setActive(['model-tank','model-apc','model-drone'],'model-tank'); loadProcedural('tank'); setStatus('PORK TANK READY'); };
    document.getElementById('model-apc').onclick=()=>{ setActive(['model-tank','model-apc','model-drone'],'model-apc'); loadProcedural('apc'); setStatus('APC READY'); };
    document.getElementById('model-drone').onclick=()=>{ setActive(['model-tank','model-apc','model-drone'],'model-drone'); loadProcedural('drone'); setStatus('RECON DRONE READY'); };

    const views=['view-iso','view-side','view-front','view-top'];
    document.getElementById('view-iso').onclick=()=>{setActive(views,'view-iso');setCamera('iso');};
    document.getElementById('view-side').onclick=()=>{setActive(views,'view-side');setCamera('side');};
    document.getElementById('view-front').onclick=()=>{setActive(views,'view-front');setCamera('front');};
    document.getElementById('view-top').onclick=()=>{setActive(views,'view-top');setCamera('top');};

    document.getElementById('mat-default').onclick=()=>{setActive(['mat-default','mat-wireframe'],'mat-default');toggleWire(false);};
    document.getElementById('mat-wireframe').onclick=()=>{setActive(['mat-default','mat-wireframe'],'mat-wireframe');toggleWire(true);};

    document.getElementById('btn-rotate').onclick=()=>{
        isAutoRotate=!isAutoRotate;
        const s=document.getElementById('rotate-status');
        s.textContent=isAutoRotate?'ON':'OFF';
        s.className=isAutoRotate
            ?'text-[10px] bg-emerald-500/15 text-emerald-300 px-2 py-1 rounded-md'
            :'text-[10px] bg-slate-500/15 text-slate-400 px-2 py-1 rounded-md';
        document.getElementById('btn-rotate').classList.toggle('active',isAutoRotate);
    };

    document.getElementById('light-studio').onclick=()=>setLighting('studio');
    document.getElementById('light-cyan').onclick=()=>setLighting('cyan');
    document.getElementById('light-red').onclick=()=>setLighting('red');

    document.getElementById('file-input').onchange=e=>{
        const file=e.target.files[0];
        if(!file)return;
        const url=URL.createObjectURL(file);
        loadModelFile(url);
    };
}

function onWindowResize(){
    camera.aspect=innerWidth/innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(innerWidth,innerHeight);
}

function animate(){
    requestAnimationFrame(animate);
    if(currentModel && isAutoRotate) currentModel.rotation.y += .0025;
    decorative.rotation.y += .00012;
    controls.update();
    renderer.render(scene,camera);
}
</script>
</body>
</html>
