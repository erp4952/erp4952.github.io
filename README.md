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
            background-color: #080a0c; 
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
        /* Tactical Glassmorphism Style */
        .tactical-card {
            background: rgba(13, 17, 23, 0.88);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 1px solid rgba(84, 110, 122, 0.25);
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.6);
        }
        .btn-tactical {
            background: rgba(30, 41, 59, 0.6);
            border: 1px solid rgba(51, 65, 85, 0.8);
            color: #cbd5e1;
            transition: all 0.2s ease;
        }
        .btn-tactical:hover {
            background: rgba(16, 185, 129, 0.2);
            border-color: #10b981;
            color: #34d399;
        }
        .btn-tactical.active {
            background: rgba(16, 185, 129, 0.3);
            border-color: #10b981;
            color: #34d399;
            font-weight: 600;
        }
        .custom-scroll::-webkit-scrollbar {
            width: 4px;
        }
        .custom-scroll::-webkit-scrollbar-thumb {
            background: rgba(52, 211, 153, 0.3);
            border-radius: 4px;
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

    <!-- UI Overlay (ฝั่งซ้าย ไม่บังโมเดล) -->
    <div class="relative z-10 flex h-screen p-4 md:p-6 pointer-events-none">
        
        <!-- Left Sidebar Panel -->
        <aside class="w-full max-w-sm h-full flex flex-col justify-between gap-4 pointer-events-auto custom-scroll overflow-y-auto pr-1">
            
            <div class="space-y-4">
                <!-- Profile Header Card -->
                <header class="tactical-card p-5 rounded-xl border-l-4 border-l-emerald-500">
                    <div class="flex items-center space-x-3">
                        <div class="w-11 h-11 rounded-lg bg-emerald-950/80 border border-emerald-500/40 flex items-center justify-center text-lg font-black text-emerald-400 tracking-wider">
                            AT
                        </div>
                        <div>
                            <h1 class="text-base font-bold tracking-wider text-slate-100 uppercase">Lect.Anuthep Toeiliang</h1>
                            <p class="text-[11px] text-emerald-400 font-semibold tracking-wider uppercase">3D Hard-Surface & Military Vehicles</p>
                        </div>
                    </div>
                    <p class="mt-3 text-xs text-slate-300 leading-relaxed">
                        แฟ้มสะสมผลงานโมเดล 3D ยานเกราะ Mother 3 Pork Tank และงานฮาร์ดเซอเฟส เน้นรายละเอียดโครงสร้าง PBR และพื้นผิวโลหะ
                    </p>
                </header>

                <!-- Interactive Controls Panel -->
                <section class="tactical-card p-4 rounded-xl space-y-4">
                    <h2 class="text-xs font-mono font-bold text-emerald-400 uppercase tracking-widest flex items-center gap-2">
                        <span>🎯</span> Camera Angles
                    </h2>
                    <div class="grid grid-cols-2 gap-2 text-xs">
                        <button id="view-iso" class="btn-tactical active py-2 px-3 rounded-lg text-left">Isometric</button>
                        <button id="view-side" class="btn-tactical py-2 px-3 rounded-lg text-left">Side View</button>
                        <button id="view-front" class="btn-tactical py-2 px-3 rounded-lg text-left">Front View</button>
                        <button id="view-top" class="btn-tactical py-2 px-3 rounded-lg text-left">Top View</button>
                    </div>

                    <div class="border-t border-slate-700/50 pt-3">
                        <h2 class="text-xs font-mono font-bold text-emerald-400 uppercase tracking-widest flex items-center gap-2 mb-2">
                            <span>🎨</span> Material Inspection
                        </h2>
                        <div class="grid grid-cols-2 gap-2 text-xs">
                            <button id="mat-default" class="btn-tactical active py-2 px-3 rounded-lg text-left">PBR Default</button>
                            <button id="mat-wireframe" class="btn-tactical py-2 px-3 rounded-lg text-left">Wireframe</button>
                        </div>
                    </div>

                    <div class="border-t border-slate-700/50 pt-3">
                        <h2 class="text-xs font-mono font-bold text-emerald-400 uppercase tracking-widest flex items-center gap-2 mb-2">
                            <span>⚙️</span> Scene Options
                        </h2>
                        <button id="btn-rotate" class="btn-tactical active w-full py-2 px-3 rounded-lg text-left text-xs flex justify-between items-center">
                            <span>Auto Rotation</span>
                            <span id="rotate-status" class="text-[10px] bg-emerald-500/20 text-emerald-300 px-1.5 py-0.5 rounded">ON</span>
                        </button>
                    </div>
                </section>
            </div>

            <!-- Bottom Tools -->
            <footer class="tactical-card p-3 rounded-xl space-y-2 text-xs">
                <label for="file-input" class="btn-tactical w-full py-2 px-3 rounded-lg cursor-pointer flex items-center justify-center gap-2 hover:text-white">
                    <span>🪖</span> โหลดไฟล์ของคุณ (.glb / .fbx)
                </label>
                <input type="file" id="file-input" accept=".glb,.gltf,.fbx" class="hidden" />
            </footer>
        </aside>

        <!-- Status Tag -->
        <div class="ml-auto pointer-events-auto hidden md:block">
            <div id="loading" class="tactical-card px-4 py-2 rounded-lg flex items-center space-x-2 border border-emerald-500/30">
                <div class="w-2.5 h-2.5 bg-emerald-400 rounded-full animate-pulse"></div>
                <span class="text-[11px] font-mono tracking-wider text-emerald-300 uppercase" id="loading-text">Scene Ready</span>
            </div>
        </div>

    </div>

    <!-- Three.js Logic Script -->
    <script type="module">
        import * as THREE from 'three';
        import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
        import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';
        import { FBXLoader } from 'three/addons/loaders/FBXLoader.js';

        let scene, camera, renderer, controls, currentModel;
        let isAutoRotate = true;

        init();
        animate();

        function init() {
            const container = document.getElementById('canvas-container');

            // 1. Scene
            scene = new THREE.Scene();
            scene.background = new THREE.Color(0x080a0c);

            // 2. Camera
            camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);
            
            // 3. Renderer
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

            // 5. Lighting
            const ambientLight = new THREE.AmbientLight(0xffffff, 0.8);
            scene.add(ambientLight);

            const mainLight = new THREE.DirectionalLight(0xfffaed, 3.0);
            mainLight.position.set(8, 12, 6);
            mainLight.castShadow = true;
            scene.add(mainLight);

            const fillLight = new THREE.DirectionalLight(0x38bdf8, 1.5);
            fillLight.position.set(-6, 6, -4);
            scene.add(fillLight);

            // 6. Ground Grid & Shadow
            const gridHelper = new THREE.GridHelper(25, 25, 0x334155, 0x0f172a);
            gridHelper.position.y = 0;
            scene.add(gridHelper);

            const shadowPlane = new THREE.Mesh(
                new THREE.PlaneGeometry(25, 25),
                new THREE.ShadowMaterial({ opacity: 0.6 })
            );
            shadowPlane.rotation.x = -Math.PI / 2;
            shadowPlane.position.y = -0.001;
            shadowPlane.receiveShadow = true;
            scene.add(shadowPlane);

            // พยายามโหลดไฟล์ porktank.glb ใน Repo ก่อน ถ้าไม่มีจะสร้าง Procedural Tank
            loadModelFile('porktank.glb');

            setupUIEvents();
            window.addEventListener('resize', onWindowResize);
        }

        function loadModelFile(url) {
            const ext = url.split('.').pop().toLowerCase();
            document.getElementById('loading-text').innerText = 'Loading Pork Tank...';

            if (ext === 'fbx') {
                const loader = new FBXLoader();
                loader.load(
                    url,
                    (fbx) => setupLoadedModel(fbx),
                    undefined,
                    () => createProceduralTank()
                );
            } else {
                const loader = new GLTFLoader();
                loader.load(
                    url,
                    (gltf) => setupLoadedModel(gltf.scene),
                    undefined,
                    () => createProceduralTank()
                );
            }
        }

        function setupLoadedModel(model) {
            if (currentModel) scene.remove(currentModel);
            currentModel = model;

            const box = new THREE.Box3().setFromObject(currentModel);
            const center = box.getCenter(new THREE.Vector3());
            const size = box.getSize(new THREE.Vector3());

            currentModel.position.x += (currentModel.position.x - center.x);
            currentModel.position.y += (currentModel.position.y - box.min.y);
            currentModel.position.z += (currentModel.position.z - center.z);

            currentModel.traverse((child) => {
                if (child.isMesh) {
                    child.castShadow = true;
                    child.receiveShadow = true;
                }
            });

            scene.add(currentModel);
            
            const maxDim = Math.max(size.x, size.y, size.z);
            updateCameraPosition(maxDim * 1.5, maxDim * 1.2, maxDim * 1.8);
            document.getElementById('loading-text').innerText = 'Pork Tank Loaded';
        }

        function createProceduralTank() {
            if (currentModel) scene.remove(currentModel);

            const tankGroup = new THREE.Group();
            const bodyMat = new THREE.MeshStandardMaterial({ color: 0xb91c1c, roughness: 0.3, metalness: 0.5 }); // แดง Pork Tank
            const metalMat = new THREE.MeshStandardMaterial({ color: 0x1e293b, roughness: 0.2, metalness: 0.9 });
            const trackMat = new THREE.MeshStandardMaterial({ color: 0x0f172a, roughness: 0.8, metalness: 0.3 });

            // Body
            const bodyMesh = new THREE.Mesh(new THREE.BoxGeometry(2.2, 0.8, 3.0), bodyMat);
            bodyMesh.position.y = 0.6;
            tankGroup.add(bodyMesh);

            // Turret (Pig Nose Shape)
            const turretMesh = new THREE.Mesh(new THREE.CylinderGeometry(0.8, 1.0, 0.7, 16), bodyMat);
            turretMesh.position.set(0, 1.2, -0.1);
            tankGroup.add(turretMesh);

            // Cannon
            const barrelMesh = new THREE.Mesh(new THREE.CylinderGeometry(0.1, 0.12, 2.0, 16), metalMat);
            barrelMesh.rotation.x = Math.PI / 2;
            barrelMesh.position.set(0, 1.25, 1.1);
            tankGroup.add(barrelMesh);

            // Tracks
            [-1.2, 1.2].forEach(x => {
                const trackMesh = new THREE.Mesh(new THREE.BoxGeometry(0.4, 0.6, 3.2), trackMat);
                trackMesh.position.set(x, 0.3, 0);
                tankGroup.add(trackMesh);
            });

            currentModel = tankGroup;
            scene.add(currentModel);
            updateCameraPosition(3.5, 2.5, 4.5);
            document.getElementById('loading-text').innerText = 'Procedural Tank Loaded';
        }

        function updateCameraPosition(camX, camY, camZ) {
            if (!currentModel) return;
            const box = new THREE.Box3().setFromObject(currentModel);
            const size = box.getSize(new THREE.Vector3());
            const offsetX = window.innerWidth > 768 ? 0.8 : 0; // เยื้องขวาไม่ให้ UI บัง

            controls.target.set(offsetX, size.y * 0.5, 0);
            camera.position.set(camX + offsetX, camY, camZ);
            controls.update();
        }

        function setupUIEvents() {
            // Camera Buttons
            document.getElementById('view-iso').addEventListener('click', (e) => {
                setActiveBtn(e.target, ['view-iso', 'view-side', 'view-front', 'view-top']);
                updateCameraPosition(3.5, 2.5, 4.5);
            });
            document.getElementById('view-side').addEventListener('click', (e) => {
                setActiveBtn(e.target, ['view-iso', 'view-side', 'view-front', 'view-top']);
                updateCameraPosition(5.0, 1.2, 0.0);
            });
            document.getElementById('view-front').addEventListener('click', (e) => {
                setActiveBtn(e.target, ['view-iso', 'view-side', 'view-front', 'view-top']);
                updateCameraPosition(0.0, 1.2, 5.0);
            });
            document.getElementById('view-top').addEventListener('click', (e) => {
                setActiveBtn(e.target, ['view-iso', 'view-side', 'view-front', 'view-top']);
                updateCameraPosition(0.0, 6.0, 0.01);
            });

            // Material Buttons
            document.getElementById('mat-default').addEventListener('click', (e) => {
                setActiveBtn(e.target, ['mat-default', 'mat-wireframe']);
                toggleWireframe(false);
            });
            document.getElementById('mat-wireframe').addEventListener('click', (e) => {
                setActiveBtn(e.target, ['mat-default', 'mat-wireframe']);
                toggleWireframe(true);
            });

            // Auto Rotation Toggle
            document.getElementById('btn-rotate').addEventListener('click', (e) => {
                isAutoRotate = !isAutoRotate;
                const statusSpan = document.getElementById('rotate-status');
                statusSpan.innerText = isAutoRotate ? 'ON' : 'OFF';
                statusSpan.className = isAutoRotate 
                    ? 'text-[10px] bg-emerald-500/20 text-emerald-300 px-1.5 py-0.5 rounded' 
                    : 'text-[10px] bg-slate-500/20 text-slate-400 px-1.5 py-0.5 rounded';
            });

            // File Upload
            document.getElementById('file-input').addEventListener('change', (e) => {
                const file = e.target.files[0];
                if (!file) return;

                const url = URL.createObjectURL(file);
                loadModelFile(url);
            });
        }

        function toggleWireframe(enable) {
            if (!currentModel) return;
            currentModel.traverse((child) => {
                if (child.isMesh && child.material) {
                    child.material.wireframe = enable;
                }
            });
        }

        function setActiveBtn(target, groupIds) {
            groupIds.forEach(id => {
                const btn = document.getElementById(id);
                if (btn) btn.classList.remove('active');
            });
            if (target) target.classList.add('active');
        }

        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        }

        function animate() {
            requestAnimationFrame(animate);

            if (currentModel && isAutoRotate) {
                currentModel.rotation.y += 0.002;
            }

            controls.update();
            renderer.render(scene, camera);
        }
    </script>
</body>
</html>
