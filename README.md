<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Tank Portfolio | Lect.Anuthep Toeiliang</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { 
            margin: 0; 
            overflow: hidden; 
            background-color: #0c0f12; 
            color: #e2e8f0; 
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
        }
        #canvas-container { 
            width: 100vw; 
            height: 100vh; 
            position: absolute; 
            top: 0; 
            left: 0; 
            z-index: 1; 
        }
        .tactical-card {
            background: rgba(18, 22, 28, 0.85);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 1px solid rgba(84, 110, 122, 0.3);
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.5);
        }
        .tactical-border {
            border-left: 3px solid #84a98c;
        }
    </style>
    <!-- Import Maps สำหรับ Three.js ES Modules -->
    <script type="importmap">
    {
        "imports": {
            "three": "https://unpkg.com/three@0.160.0/build/three.module.js",
            "three/addons/": "https://unpkg.com/three@0.160.0/examples/jsm/"
        }
    }
    </script>
</head>
<body class="select-none">

    <!-- 3D Canvas Container -->
    <div id="canvas-container"></div>

    <!-- UI Overlay -->
    <div class="relative z-10 flex flex-col justify-between h-screen p-6 md:p-8 pointer-events-none">
        
        <!-- Header / Profile Info -->
        <header class="tactical-card tactical-border p-5 rounded-r-2xl rounded-l-sm max-w-md pointer-events-auto shadow-2xl">
            <div class="flex items-center space-x-4">
                <div class="w-12 h-12 rounded-lg bg-emerald-900/60 border border-emerald-500/40 flex items-center justify-center text-xl font-black text-emerald-400 tracking-wider">
                    AT
                </div>
                <div>
                    <h1 class="text-lg font-bold tracking-wider text-slate-100 uppercase">Lect.Anuthep Toeiliang</h1>
                    <p class="text-xs text-emerald-400 font-semibold tracking-widest uppercase">3D Hard-Surface & Military Vehicles</p>
                </div>
            </div>
            <p class="mt-3 text-xs text-slate-300 leading-relaxed">
                แฟ้มสะสมผลงานโมเดล 3D สายยานเกราะและงานฮาร์ดเซอเฟส (Hard-Surface Modeling) เน้นรายละเอียดพื้นผิวโลหะ และการจัดแสงด้วยระบบ PBR
            </p>
        </header>

        <!-- Status Indicator -->
        <div id="loading" class="self-center tactical-card px-6 py-3 rounded-full flex items-center space-x-3 pointer-events-auto border border-emerald-500/30">
            <div class="w-3 h-3 bg-emerald-400 rounded-full animate-pulse"></div>
            <span class="text-xs font-mono tracking-wider text-emerald-300 uppercase" id="loading-text">3D Scene Ready</span>
        </div>

        <!-- Controls / Footer -->
        <footer class="flex flex-col md:flex-row justify-between items-end md:items-center gap-4">
            <div class="tactical-card px-4 py-2 rounded-lg text-xs text-slate-400 pointer-events-auto border-l-2 border-emerald-500 flex items-center gap-3">
                <div><span class="text-emerald-400 font-bold uppercase">Controls:</span> คลิกซ้ายหมุน | คลิกขวาเลื่อน | สกรอลล์ซูม</div>
                <!-- Preset Switcher -->
                <button id="btn-procedural" class="px-2 py-1 bg-emerald-800/50 hover:bg-emerald-700 text-emerald-200 rounded border border-emerald-500/40 transition">Procedural Tank</button>
                <button id="btn-helmet" class="px-2 py-1 bg-slate-800/50 hover:bg-slate-700 text-slate-300 rounded border border-slate-600/40 transition">Damaged Helmet</button>
            </div>
            
            <!-- File Upload Input (สำหรับเปลี่ยนโมเดล .glb ของตนเอง) -->
            <div class="tactical-card p-3 rounded-lg pointer-events-auto flex items-center space-x-3 hover:border-emerald-500/50 transition">
                <label for="file-input" class="text-xs font-mono text-emerald-300 cursor-pointer hover:text-white transition flex items-center gap-2">
                    <span>🪖</span> อัปโหลดไฟล์ของคุณ (.glb)
                </label>
                <input type="file" id="file-input" accept=".glb,.gltf" class="hidden" />
            </div>
        </footer>
    </div>

    <!-- Three.js Logic Script -->
    <script type="module">
        import * as THREE from 'three';
        import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
        import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';

        let scene, camera, renderer, controls, currentModel;

        init();
        animate();

        function init() {
            const container = document.getElementById('canvas-container');

            // 1. Scene setup
            scene = new THREE.Scene();
            scene.background = new THREE.Color(0x0c0f12);

            // 2. Camera setup
            camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 100);
            camera.position.set(4, 3, 5);

            // 3. Renderer setup
            renderer = new THREE.WebGLRenderer({ antialias: true });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            renderer.toneMapping = THREE.ACESFilmicToneMapping;
            renderer.toneMappingExposure = 1.2;
            renderer.shadowMap.enabled = true;
            renderer.shadowMap.type = THREE.PCFSoftShadowMap;
            container.appendChild(renderer.domElement);

            // 4. Orbit Controls
            controls = new OrbitControls(camera, renderer.domElement);
            controls.enableDamping = true;
            controls.dampingFactor = 0.05;
            controls.maxPolarAngle = Math.PI / 2 - 0.01;

            // 5. Tactical PBR Lighting
            const ambientLight = new THREE.AmbientLight(0xffffff, 0.7);
            scene.add(ambientLight);

            const mainLight = new THREE.DirectionalLight(0xfffaed, 3.0);
            mainLight.position.set(8, 12, 6);
            mainLight.castShadow = true;
            mainLight.shadow.mapSize.width = 2048;
            mainLight.shadow.mapSize.height = 2048;
            scene.add(mainLight);

            const fillLight = new THREE.DirectionalLight(0x38bdf8, 1.5);
            fillLight.position.set(-6, 6, -4);
            scene.add(fillLight);

            const rimLight = new THREE.DirectionalLight(0x34d399, 2.0);
            rimLight.position.set(0, 4, -8);
            scene.add(rimLight);

            // 6. Grid & Ground Shadow
            const gridHelper = new THREE.GridHelper(20, 20, 0x546e7a, 0x1e293b);
            gridHelper.position.y = 0;
            scene.add(gridHelper);

            const shadowPlane = new THREE.Mesh(
                new THREE.PlaneGeometry(20, 20),
                new THREE.ShadowMaterial({ opacity: 0.5 })
            );
            shadowPlane.rotation.x = -Math.PI / 2;
            shadowPlane.position.y = -0.001;
            shadowPlane.receiveShadow = true;
            scene.add(shadowPlane);

            // 7. โหลด Procedural Tank เป็นโมเดลเริ่มต้นทันที (การันตีแสดงผล 100%)
            createProceduralTank();

            // Event Listeners
            window.addEventListener('resize', onWindowResize);
            document.getElementById('file-input').addEventListener('change', handleFileUpload);
            document.getElementById('btn-procedural').addEventListener('click', createProceduralTank);
            document.getElementById('btn-helmet').addEventListener('click', () => {
                loadCDNModel('https://cdn.jsdelivr.net/gh/mrdoob/three.js@dev/examples/models/gltf/DamagedHelmet/GlTF-Binary/DamagedHelmet.glb');
            });
        }

        // สร้างโมเดลรถถัง PBR 3D ขึ้นมาด้วยโค้ดแบบ 100% ไม่พึ่งพาไฟล์ภายนอก
        function createProceduralTank() {
            if (currentModel) scene.remove(currentModel);

            const tankGroup = new THREE.Group();

            // PBR Materials
            const bodyMat = new THREE.MeshStandardMaterial({
                color: 0x2e3d32,
                roughness: 0.4,
                metalness: 0.6
            });
            const metalMat = new THREE.MeshStandardMaterial({
                color: 0x1e293b,
                roughness: 0.2,
                metalness: 0.9
            });
            const trackMat = new THREE.MeshStandardMaterial({
                color: 0x0f172a,
                roughness: 0.8,
                metalness: 0.3
            });

            // 1. Chassis (ตัวรถ)
            const bodyGeo = new THREE.BoxGeometry(2.2, 0.6, 3.2);
            const bodyMesh = new THREE.Mesh(bodyGeo, bodyMat);
            bodyMesh.position.y = 0.5;
            bodyMesh.castShadow = true;
            bodyMesh.receiveShadow = true;
            tankGroup.add(bodyMesh);

            // 2. Turret (ป้อมปืน)
            const turretGeo = new THREE.CylinderGeometry(0.8, 1.0, 0.5, 12);
            const turretMesh = new THREE.Mesh(turretGeo, bodyMat);
            turretMesh.position.set(0, 1.0, -0.2);
            turretMesh.castShadow = true;
            tankGroup.add(turretMesh);

            // 3. Cannon Barrel (ลำกล้องปืน)
            const barrelGeo = new THREE.CylinderGeometry(0.08, 0.1, 2.2, 16);
            const barrelMesh = new THREE.Mesh(barrelGeo, metalMat);
            barrelMesh.rotation.x = Math.PI / 2;
            barrelMesh.position.set(0, 1.05, 1.1);
            barrelMesh.castShadow = true;
            tankGroup.add(barrelMesh);

            // 4. Tracks & Wheels (สายพานและล้อ)
            [-1.15, 1.15].forEach(x => {
                const trackGeo = new THREE.BoxGeometry(0.4, 0.6, 3.4);
                const trackMesh = new THREE.Mesh(trackGeo, trackMat);
                trackMesh.position.set(x, 0.3, 0);
                trackMesh.castShadow = true;
                tankGroup.add(trackMesh);

                // Wheels
                for(let z = -1.3; z <= 1.3; z += 0.65) {
                    const wheelGeo = new THREE.CylinderGeometry(0.25, 0.25, 0.45, 16);
                    const wheelMesh = new THREE.Mesh(wheelGeo, metalMat);
                    wheelMesh.rotation.z = Math.PI / 2;
                    wheelMesh.position.set(x, 0.25, z);
                    wheelMesh.castShadow = true;
                    tankGroup.add(wheelMesh);
                }
            });

            currentModel = tankGroup;
            scene.add(currentModel);

            camera.position.set(3.5, 2.5, 4.5);
            controls.target.set(0, 0.6, 0);
            controls.update();

            document.getElementById('loading-text').innerText = 'Procedural Tank Loaded';
        }

        // ฟังก์ชันโหลดโมเดลจาก CDN พร้อมระบบความปลอดภัย
        function loadCDNModel(url) {
            const loader = new GLTFLoader();
            document.getElementById('loading-text').innerText = 'Loading GLB...';

            loader.load(
                url,
                (gltf) => {
                    if (currentModel) scene.remove(currentModel);
                    currentModel = gltf.scene;

                    const box = new THREE.Box3().setFromObject(currentModel);
                    const center = box.getCenter(new THREE.Vector3());
                    const size = box.getSize(new THREE.Vector3());

                    currentModel.position.x += (currentModel.position.x - center.x);
                    currentModel.position.y += (currentModel.position.y - box.min.y);
                    currentModel.position.z += (currentModel.position.z - center.z);

                    currentModel.traverse((c) => {
                        if(c.isMesh) {
                            c.castShadow = true;
                            c.receiveShadow = true;
                        }
                    });

                    scene.add(currentModel);

                    const maxDim = Math.max(size.x, size.y, size.z);
                    camera.position.set(size.x * 1.5, size.y * 1.2, maxDim * 2.0);
                    controls.target.set(0, size.y * 0.5, 0);
                    controls.update();

                    document.getElementById('loading-text').innerText = 'GLB Model Loaded';
                },
                undefined,
                (err) => {
                    console.error('Failed to load CDN model, falling back to procedural tank', err);
                    createProceduralTank();
                }
            );
        }

        function handleFileUpload(event) {
            const file = event.target.files[0];
            if (!file) return;

            const url = URL.createObjectURL(file);
            loadCDNModel(url);
        }

        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        }

        function animate() {
            requestAnimationFrame(animate);

            if (currentModel) {
                currentModel.rotation.y += 0.002;
            }

            controls.update();
            renderer.render(scene, camera);
        }
    </script>
</body>
</html>
