# erp4952.github.io
Anuthep
<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Portfolio | Lect.Anuthep Toeiliang</title>
    <!-- Tailwind CSS สำหรับจัด Styling ส่วน UI -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { margin: 0; overflow: hidden; background-color: #0d1117; color: #f0f6fc; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        #canvas-container { width: 100vw; height: 100vh; position: absolute; top: 0; left: 0; z-index: 1; }
        .glass-card {
            background: rgba(22, 27, 34, 0.75);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.1);
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
    <div class="relative z-10 flex flex-col justify-between h-screen p-6 md:p-10 pointer-events-none">
        
        <!-- Header / Profile Info -->
        <header class="glass-card p-6 rounded-2xl max-w-md pointer-events-auto shadow-2xl transition-all duration-300 hover:border-blue-500/50">
            <div class="flex items-center space-x-4">
                <div class="w-12 h-12 rounded-full bg-gradient-to-tr from-blue-600 to-indigo-400 flex items-center justify-center text-xl font-bold text-white shadow-lg">
                    AT
                </div>
                <div>
                    <h1 class="text-xl font-bold tracking-wide text-white">Lect.Anuthep Toeiliang</h1>
                    <p class="text-sm text-blue-400 font-medium">3D Artist & Lecturer</p>
                </div>
            </div>
            <p class="mt-4 text-xs text-gray-300 leading-relaxed">
                แฟ้มสะสมผลงาน 3D และงานโมเดลเชิงเทคนิค เน้นการจัดแสง PBR และรายละเอียดพื้นผิว (PBR Metallic-Roughness Workflow)
            </p>
        </header>

        <!-- Loading Indicator -->
        <div id="loading" class="self-center glass-card px-6 py-3 rounded-full flex items-center space-x-3 pointer-events-auto shadow-lg">
            <div class="w-4 h-4 border-2 border-blue-400 border-t-transparent rounded-full animate-spin"></div>
            <span class="text-sm font-medium text-gray-200" id="loading-text">กำลังโหลดโมเดล 3D...</span>
        </div>

        <!-- Controls / Helper Footer -->
        <footer class="flex flex-col md:flex-row justify-between items-end md:items-center gap-4">
            <div class="glass-card px-4 py-2 rounded-xl text-xs text-gray-400 pointer-events-auto">
                <span class="text-blue-400 font-semibold">Controls:</span> คลิกซ้ายหมุนดูโมเดล | คลิกขวาเลื่อนตำแหน่ง | สกรอลล์เมาส์ซูม
            </div>
            
            <!-- File Upload Input (สำหรับเปลี่ยนโมเดล .glb / .gltf เองได้ทันที) -->
            <div class="glass-card p-3 rounded-xl pointer-events-auto flex items-center space-x-3">
                <label for="file-input" class="text-xs font-semibold text-gray-300 cursor-pointer hover:text-white transition">
                    📂 ทดลองเปลี่ยนโมเดล (.glb)
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

        init();
        animate();

        function init() {
            const container = document.getElementById('canvas-container');

            // 1. Scene setup
            scene = new THREE.Scene();
            scene.background = new THREE.Color(0x0d1117);

            // 2. Camera setup
            camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 100);
            camera.position.set(0, 1.2, 3);

            // 3. Renderer setup (รองรับ PBR & Tone Mapping)
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
            controls.maxPolarAngle = Math.PI / 2 + 0.1; // ป้องกันกล้องมุดใต้พื้นมากเกินไป

            // 5. Lighting Setup (เน้นการขับเงา PBR Metallic & Roughness)
            const ambientLight = new THREE.AmbientLight(0xffffff, 0.6);
            scene.add(ambientLight);

            // Main Directional Light (Key Light)
            const dirLight = new THREE.DirectionalLight(0xffffff, 2.5);
            dirLight.position.set(5, 8, 5);
            dirLight.castShadow = true;
            dirLight.shadow.mapSize.width = 2048;
            dirLight.shadow.mapSize.height = 2048;
            scene.add(dirLight);

            // Rim Light (ไฟด้านหลังสร้างมิติเส้นขอบ)
            const rimLight = new THREE.DirectionalLight(0x3b82f6, 3.0);
            rimLight.position.set(-5, 3, -5);
            scene.add(rimLight);

            // 6. HDRI Environment Map (เพื่อให้โมเดล PBR มีแสงสะท้อนที่สมจริง)
            new RGBELoader()
                .setPath('https://threejs.org/examples/textures/equirectangular/')
                .load('royal_esplanade_1k.hdr', function (texture) {
                    texture.mapping = THREE.EquirectangularReflectionMapping;
                    scene.environment = texture;
                });

            // 7. Ground Shadow Plane
            const shadowPlane = new THREE.Mesh(
                new THREE.PlaneGeometry(10, 10),
                new THREE.ShadowMaterial({ opacity: 0.4 })
            );
            shadowPlane.rotation.x = -Math.PI / 2;
            shadowPlane.position.y = 0;
            shadowPlane.receiveShadow = true;
            scene.add(shadowPlane);

            // 8. Load Default Sample GLTF/GLB Model
            const loader = new GLTFLoader();
            // ตัวอย่างใช้โมเดล Helmet เพื่อโชว์วัสดุ PBR (โลหะ, ผ้า, กระจก)
            loader.load(
                'https://threejs.org/examples/models/gltf/DamagedHelmet/GlTF-Binary/DamagedHelmet.glb',
                function (gltf) {
                    setupLoadedModel(gltf.scene);
                    document.getElementById('loading').style.display = 'none';
                },
                function (xhr) {
                    const percent = Math.round((xhr.loaded / xhr.total) * 100);
                    document.getElementById('loading-text').innerText = `กำลังโหลดโมเดล 3D... ${percent}%`;
                },
                function (error) {
                    console.error('An error occurred loading the model:', error);
                    document.getElementById('loading-text').innerText = 'ไม่สามารถโหลดโมเดลตัวอย่างได้';
                }
            );

            // Window Resize Listener
            window.addEventListener('resize', onWindowResize);

            // Local File Upload Listener
            document.getElementById('file-input').addEventListener('change', handleFileUpload);
        }

        // ฟังก์ชันจัดการโมเดลที่โหลดเข้ามา (จัดกึ่งกลาง + ปรับเงา)
        function setupLoadedModel(model) {
            if (currentModel) scene.remove(currentModel);

            currentModel = model;

            // จัดตำแหน่งโมเดลให้อยู่จุดศูนย์กลาง
            const box = new THREE.Box3().setFromObject(model);
            const center = box.getCenter(new THREE.Vector3());
            const size = box.getSize(new THREE.Vector3());

            model.position.x += (model.position.x - center.x);
            model.position.y += (model.position.y - center.y) + (size.y / 2);
            model.position.z += (model.position.z - center.z);

            // ปรับระยะกล้องตามขนาดโมเดล
            const maxDim = Math.max(size.x, size.y, size.z);
            camera.position.set(0, size.y * 0.8, maxDim * 2.2);
            controls.target.set(0, size.y * 0.5, 0);
            controls.update();

            // เปิดใช้งานการทอดเงาและรับแสง PBR ทุกชิ้นส่วน
            model.traverse((child) => {
                if (child.isMesh) {
                    child.castShadow = true;
                    child.receiveShadow = true;
                }
            });

            scene.add(model);
        }

        // ฟังก์ชันรองรับการอัปโหลดไฟล์ GLB/GLTF ของตนเอง
        function handleFileUpload(event) {
            const file = event.target.files[0];
            if (!file) return;

            const url = URL.createObjectURL(file);
            const loader = new GLTFLoader();

            document.getElementById('loading').style.display = 'flex';
            document.getElementById('loading-text').innerText = 'กำลังโหลดโมเดลของคุณ...';

            loader.load(url, (gltf) => {
                setupLoadedModel(gltf.scene);
                document.getElementById('loading').style.display = 'none';
                URL.revokeObjectURL(url);
            });
        }

        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        }

        function animate() {
            requestAnimationFrame(animate);

            // หมุนโมเดลช้าๆ เพิ่มความมีมิติ
            if (currentModel) {
                currentModel.rotation.y += 0.003;
            }

            controls.update();
            renderer.render(scene, camera);
        }
    </script>
</body>
</html>
