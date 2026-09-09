<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Portfolio | Lect.Anuthep Toeiliang</title>
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
        /* Tactical Glassmorphism Style */
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
                แฟ้มสะสมผลงานโมเดล 3D สายยานเกราะและงานฮาร์ดเซอเฟส (Hard-Surface Modeling) เน้นรายละเอียดพื้นผิวโลหะ รอยถลอก และการจัดแสงด้วยระบบ PBR
            </p>
        </header>

        <!-- Loading Indicator -->
        <div id="loading" class="self-center tactical-card px-6 py-3 rounded-full flex items-center space-x-3 pointer-events-auto border border-emerald-500/30">
            <div class="w-4 h-4 border-2 border-emerald-400 border-t-transparent rounded-full animate-spin"></div>
            <span class="text-xs font-mono tracking-wider text-emerald-300 uppercase" id="loading-text">Loading 3D Model...</span>
        </div>

        <!-- Controls / Footer -->
        <footer class="flex flex-col md:flex-row justify-between items-end md:items-center gap-4">
            <div class="tactical-card px-4 py-2 rounded-lg text-xs text-slate-400 pointer-events-auto border-l-2 border-emerald-500">
                <span class="text-emerald-400 font-bold uppercase">Controls:</span> คลิกซ้ายหมุน | คลิกขวาเลื่อน | สกรอลล์ซูม
            </div>
            
            <!-- File Upload Input (สำหรับเปลี่ยนโมเดล .glb ของตนเอง) -->
            <div class="tactical-card p-3 rounded-lg pointer-events-auto flex items-center space-x-3 hover:border-emerald-500/50 transition">
                <label for="file-input" class="text-xs font-mono text-emerald-300 cursor-pointer hover:text-white transition flex items-center gap-2">
                    <span>🪖</span> อัปโหลดไฟล์โมเดล (.glb)
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
        import { RGBELoader } from 'three/addons/loaders/RGBELoader.js';

        let scene, camera, renderer, controls, currentModel;

        // ลิงก์โมเดลผ่าน CDN ที่เปิดรับ CORS แน่นอน 100%
        const primaryModelURL = 'https://cdn.jsdelivr.net/gh/mrdoob/three.js@dev/examples/models/gltf/DamagedHelmet/GlTF-Binary/DamagedHelmet.glb';

        init();
        animate();

        function init() {
            const container = document.getElementById('canvas-container');

            // 1. Scene setup
            scene = new THREE.Scene();
            scene.background = new THREE.Color(0x0c0f12);

            // 2. Camera setup
            camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 100);
            camera.position.set(3, 2, 4);

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

            // 5. Lighting Setup (PBR Metallic & Roughness Highlights)
            const ambientLight = new THREE.AmbientLight(0xffffff, 0.6);
            scene.add(ambientLight);

            const dirLight = new THREE.DirectionalLight(0xfffaed, 3.0);
            dirLight.position.set(6, 10, 5);
            dirLight.castShadow = true;
            dirLight.shadow.mapSize.width = 2048;
            dirLight.shadow.mapSize.height = 2048;
            scene.add(dirLight);

            const rimLight = new THREE.DirectionalLight(0x718096, 2.5);
            rimLight.position.set(-6, 4, -6);
            scene.add(rimLight);

            // 6. HDRI Environment Map (เพื่อเงาสะท้อนโลหะ)
            new RGBELoader()
                .setPath('https://cdn.jsdelivr.net/gh/mrdoob/three.js@dev/examples/textures/equirectangular/')
                .load('royal_esplanade_1k.hdr', function (texture) {
                    texture.mapping = THREE.EquirectangularReflectionMapping;
                    scene.environment = texture;
                });

            // 7. Ground Grid & Shadow Plane
            const gridHelper = new THREE.GridHelper(20, 20, 0x546e7a, 0x263238);
            gridHelper.position.y = 0;
            scene.add(gridHelper);

            const shadowPlane = new THREE.Mesh(
                new THREE.PlaneGeometry(20, 20),
                new THREE.ShadowMaterial({ opacity: 0.5 })
            );
            shadowPlane.rotation.x = -Math.PI / 2;
            shadowPlane.position.y = -0.01;
            shadowPlane.receiveShadow = true;
            scene.add(shadowPlane);

            // 8. โหลดโมเดล 3D
            loadModel(primaryModelURL);

            window.addEventListener('resize', onWindowResize);
            document.getElementById('file-input').addEventListener('change', handleFileUpload);
        }

        function loadModel(url) {
            const loader = new GLTFLoader();
            document.getElementById('loading').style.display = 'flex';
            document.getElementById('loading-text').innerText = 'Loading 3D Model...';

            loader.load(
                url,
                function (gltf) {
                    setupLoadedModel(gltf.scene);
                    document.getElementById('loading').style.display = 'none';
                },
                function (xhr) {
                    if (xhr.total > 0) {
                        const percent = Math.round((xhr.loaded / xhr.total) * 100);
                        document.getElementById('loading-text').innerText = `Loading... ${percent}%`;
                    }
                },
                function (error) {
                    console.error('Error loading model:', error);
                    document.getElementById('loading-text').innerText = 'Failed to load model';
                }
            );
        }

        function setupLoadedModel(model) {
            if (currentModel) scene.remove(currentModel);

            currentModel = model;

            // จัดตำแหน่งและขนาดโมเดลให้อยู่กึ่งกลางหน้าจออัตโนมัติ
            const box = new THREE.Box3().setFromObject(model);
            const center = box.getCenter(new THREE.Vector3());
            const size = box.getSize(new THREE.Vector3());

            model.position.x += (model.position.x - center.x);
            model.position.y += (model.position.y - box.min.y);
            model.position.z += (model.position.z - center.z);

            const maxDim = Math.max(size.x, size.y, size.z);
            camera.position.set(size.x * 1.5, size.y * 1.2, maxDim * 2.0);
            controls.target.set(0, size.y * 0.5, 0);
            controls.update();

            // เปิดใช้งานระบบ PBR ชัดเจนทุก Mesh
            model.traverse((child) => {
                if (child.isMesh) {
                    child.castShadow = true;
                    child.receiveShadow = true;
                    if (child.material) {
                        child.material.envMapIntensity = 1.5;
                    }
                }
            });

            scene.add(model);
        }

        function handleFileUpload(event) {
            const file = event.target.files[0];
            if (!file) return;

            const url = URL.createObjectURL(file);
            const loader = new GLTFLoader();

            document.getElementById('loading').style.display = 'flex';
            document.getElementById('loading-text').innerText = 'Loading custom GLB...';

            loader.load(url, (gltf) => {
                setupLoadedModel(gltf.scene);
                document.getElementById('loading').style.display = 'none';
                URL.revokeObjectURL(url);
            }, undefined, (err) => {
                console.error(err);
                document.getElementById('loading-text').innerText = 'Invalid GLB file';
            });
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
