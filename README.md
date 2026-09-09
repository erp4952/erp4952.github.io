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
        /* Custom Scrollbar สำหรับ Panel ซ้ายกรณีจอเล็ก */
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

    <!-- UI Overlay: แบ่งเป็น Panel ฝั่งซ้ายเพื่อไม่ให้บังโมเดล -->
    <div class="relative z-10 flex h-screen p-4 md:p-6 pointer-events-none">
        
        <!-- Left Sidebar Panel (ข้อมูลประวัติ + ปุ่มกดควบคุมทั้งหมด) -->
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
                        แฟ้มสะสมผลงานโมเดล 3D สายยานเกราะและงานฮาร์ดเซอเฟส (Hard-Surface Modeling) เน้นรายละเอียดโครงสร้าง PBR และพื้นผิวโลหะ
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
                            <span>🎨</span> Material & Inspection
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

            <!-- Bottom Tools (Switch Preset / Upload) -->
            <footer class="tactical-card p-3 rounded-xl space-y-2 text-xs">
                <div class="flex gap-2">
                    <button id="btn-procedural" class="btn-tactical active flex-1 py-1.5 rounded-lg text-center">Tank Preset</button>
                    <button id="btn-helmet" class="btn-tactical flex-1 py-1.5 rounded-lg text-center">Helmet Preset</button>
                </div>
                
                <label for="file-input" class="btn-tactical w-full py-2 px-3 rounded-lg cursor-pointer flex items-center justify-center gap-2 hover:text-white">
                    <span>🪖</span> โหลดไฟล์ของคุณ (.glb)
                </label>
                <input type="file" id="file-input" accept=".glb,.gltf" class="hidden" />
            </footer>
        </aside>

        <!-- Loading / Status Tag (มุมขวาบน ไม่บังสายตา) -->
        <div class="ml-auto pointer-events-auto">
            <div id="loading" class="tactical-card px-4 py-2 rounded-lg flex items-center space-x-2 border border-emerald-500/30">
                <div class="w-2.5 h-2.5 bg-emerald-400 rounded-full animate-pulse"></div>
                <span class="text-[11px] font-mono tracking-wider text-emerald-300 uppercase" id="loading-text">Ready</span>
            </div>
        </div>

    </div>

    <!-- Three.js Logic Script -->
    <script type="module">
        import * as THREE from 'three';
        import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
        import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';

        let scene, camera, renderer, controls, currentModel;
        let isAutoRotate = true;
        let isWireframe = false;
        let originalMaterials = new Map();

        init();
        animate();

        function init() {
            const container = document.getElementById('canvas-container');

            // 1. Scene setup
            scene = new THREE.Scene();
            scene.background = new THREE.Color(0x080a0c);

            // 2. Camera setup
            camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 100);
            
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

            // Load Initial Model
            createProceduralTank();

            // Event Listeners Setup
            setupUIEvents();
            window.addEventListener('resize', onWindowResize);
        }

        // สั่งเปลี่ยนตำแหน่งจุดศูนย์กลางของโมเดลให้เยื้องไปทางขวา เพื่อไม่ให้ UI ซ้ายบัง
        function updateModelOffsetAndCamera(camX, camY, camZ) {
            if (!currentModel) return;

            const box = new THREE.Box3().setFromObject(currentModel);
            const center = box.getCenter(new THREE.Vector3());
            const size = box.getSize(new THREE.Vector3());

            // ปรับจุดรับกล้อง (Target) ให้เยื้องขวาเล็กน้อย บนหน้าจอคอม
            const offsetX = window.innerWidth > 768 ? 0.8 : 0; 
            
            controls.target.set(offsetX, size.y * 0.5, 0);
            camera.position.set(camX + offsetX, camY, camZ);
            controls.update();
        }

        function createProceduralTank() {
            if (currentModel) scene.remove(currentModel);
            originalMaterials.clear();

            const tankGroup = new THREE.Group();

            const bodyMat = new THREE.MeshStandardMaterial({ color: 0x2e3d32, roughness: 0.4, metalness: 0.6 });
            const metalMat = new THREE.MeshStandardMaterial({ color: 0x1e293b, roughness: 0.2, metalness: 0.9 });
            const trackMat = new THREE.MeshStandardMaterial({ color: 0x0f172a, roughness: 0.8, metalness: 0.3 });

            // Body
            const bodyMesh = new THREE.Mesh(new THREE.BoxGeometry(2.2, 0.6, 3.2), bodyMat);
            bodyMesh.position.y = 0.5;
            bodyMesh.castShadow = true;
            bodyMesh.receiveShadow = true;
            tankGroup.add(bodyMesh);

            // Turret
            const turretMesh = new THREE.Mesh(new THREE.CylinderGeometry(0.8, 1.0, 0.5, 12), bodyMat);
            turretMesh.position.set(0, 1.0, -0.2);
            turretMesh.castShadow = true;
            tankGroup.add(turretMesh);

            // Cannon
            const barrelMesh = new THREE.Mesh(new THREE.CylinderGeometry(0.08, 0.1, 2.2, 16), metalMat);
            barrelMesh.rotation.x = Math.PI / 2;
            barrelMesh.position.set(0, 1.05, 1.1);
            barrelMesh.castShadow = true;
            tankGroup.add(barrelMesh);

            // Tracks & Wheels
            [-1.15, 1.15].forEach(x => {
                const trackMesh = new THREE.Mesh(new THREE.BoxGeometry(0.4, 0.6, 3.4), trackMat);
                trackMesh.position.set(x, 0.3, 0);
                trackMesh.castShadow = true;
                tankGroup.add(trackMesh);

                for(let z = -1.3; z <= 1.3; z += 0.65) {
                    const wheelMesh = new THREE.Mesh(new THREE.CylinderGeometry(0.25, 0.25, 0.45, 16), metalMat);
                    wheelMesh.rotation.z = Math.PI / 2;
                    wheelMesh.position.set(x, 0.25, z);
                    wheelMesh.castShadow = true;
                    tankGroup.add(wheelMesh);
                }
            });

            currentModel = tankGroup;
            scene.add(currentModel);

            // บันทึก Material ไว้สลับโหมด Wireframe
            currentModel.traverse((child) => {
                if (child.isMesh) originalMaterials.set(child, child.material);
            });

            updateModelOffsetAndCamera(3.5, 2.5, 4.5);
            document.getElementById('loading-text').innerText = 'Procedural Tank Loaded';
        }

        function loadCDNModel(url) {
            const loader = new GLTFLoader();
            document.getElementById('loading-text').innerText = 'Loading GLB...';

            loader.load(
                url,
                (gltf) => {
                    if (currentModel) scene.remove(currentModel);
                    originalMaterials.clear();
                    
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
                            originalMaterials.set(c, c.material);
                        }
                    });

                    scene.add(currentModel);

                    const maxDim = Math.max(size.x, size.y, size.z);
                    updateModelOffsetAndCamera(size.x * 1.5, size.y * 1.2, maxDim * 2.0);
                    document.getElementById('loading-text').innerText = 'Model Loaded';
                },
                undefined,
                (err) => {
                    console.error(err);
                    createProceduralTank();
                }
            );
        }

        function setupUIEvents() {
            // Camera Buttons
            document.getElementById('view-iso').addEventListener('click', (e) => {
                setActiveBtn(e.target, ['view-iso', 'view-side', 'view-front', 'view-top']);
                updateModelOffsetAndCamera(3.5, 2.5, 4.5);
            });
            document.getElementById('view-side').addEventListener('click', (e) => {
                setActiveBtn(e.target, ['view-iso', 'view-side', 'view-front', 'view-top']);
                updateModelOffsetAndCamera(5.0, 1.2, 0.0);
            });
            document.getElementById('view-front').addEventListener('click', (e) => {
                setActiveBtn(e.target, ['view-iso', 'view-side', 'view-front', 'view-top']);
                updateModelOffsetAndCamera(0.0, 1.2, 5.0);
            });
            document.getElementById('view-top').addEventListener('click', (e) => {
                setActiveBtn(e.target, ['view-iso', 'view-side', 'view-front', 'view-top']);
                updateModelOffsetAndCamera(0.0, 6.0, 0.01);
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
                if (isAutoRotate) {
                    statusSpan.innerText = 'ON';
                    statusSpan.className = 'text-[10px] bg-emerald-500/20 text-emerald-300 px-1.5 py-0.5 rounded';
                    e.currentTarget.classList.add('active');
                } else {
                    statusSpan.innerText = 'OFF';
                    statusSpan.className = 'text-[10px] bg-slate-500/20 text-slate-400 px-1.5 py-0.5 rounded';
                    e.currentTarget.classList.remove('active');
                }
            });

            // Model Switchers
            document.getElementById('btn-procedural').addEventListener('click', (e) => {
                setActiveBtn(e.target, ['btn-procedural', 'btn-helmet']);
                createProceduralTank();
            });
            document.getElementById('btn-helmet').addEventListener('click', (e) => {
                setActiveBtn(e.target, ['btn-procedural', 'btn-helmet']);
                loadCDNModel('https://cdn.jsdelivr.net/gh/mrdoob/three.js@dev/examples/models/gltf/DamagedHelmet/GlTF-Binary/DamagedHelmet.glb');
            });

            // Upload Input
            document.getElementById('file-input').addEventListener('change', (e) => {
                const file = e.target.files[0];
                if (file) {
                    const url = URL.createObjectURL(file);
                    loadCDNModel(url);
                }
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
