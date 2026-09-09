<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D & AR Portfolio | Lect.Anuthep Toeiliang</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Model Viewer สำหรับ 3D และ AR Web Standard -->
    <script type="module" src="https://ajax.googleapis.com/ajax/libs/model-viewer/3.4.0/model-viewer.min.js"></script>
    
    <style>
        body { 
            margin: 0; 
            overflow: hidden; 
            background-color: #080a0c; 
            color: #e2e8f0; 
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
        }
        model-viewer {
            width: 100vw;
            height: 100vh;
            position: absolute;
            top: 0;
            left: 0;
            z-index: 1;
            --poster-color: transparent;
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
        /* ปุ่ม AR Custom Style */
        .ar-button {
            background-color: #10b981;
            color: #000;
            font-weight: bold;
            border-radius: 8px;
            padding: 10px 20px;
            border: none;
            position: absolute;
            bottom: 20px;
            right: 20px;
            z-index: 20;
            box-shadow: 0 4px 12px rgba(16, 185, 129, 0.4);
            display: flex;
            align-items: center;
            gap: 8px;
        }
    </style>
</head>
<body class="select-none">

    <!-- 3D & AR Model Viewer Component -->
    <model-viewer 
        id="tank-viewer"
        src="porktank.glb" 
        alt="Mother 3 Pork Tank 3D Model"
        ar
        ar-modes="webxr scene-viewer quick-look"
        camera-controls
        touch-action="pan-y"
        auto-rotate
        shadow-intensity="1.5"
        shadow-softness="0.8"
        exposure="1.2"
        camera-orbit="45deg 75deg 4m">
        
        <!-- ปุ่มกดเข้าโหมด AR เมื่อเปิดบนมือถือ -->
        <button slot="ar-button" class="ar-button">
            <span>📱</span> ส่องดูด้วย AR
        </button>

        <!-- Loading Progress -->
        <div slot="progress-bar" id="loading-bar" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 z-20">
            <div class="tactical-card px-6 py-3 rounded-full flex items-center space-x-3 border border-emerald-500/40">
                <div class="w-4 h-4 border-2 border-emerald-400 border-t-transparent rounded-full animate-spin"></div>
                <span class="text-xs font-mono text-emerald-300 uppercase">Loading Pork Tank 3D...</span>
            </div>
        </div>
    </model-viewer>

    <!-- UI Overlay (ฝั่งซ้าย ไม่บังโมเดล) -->
    <div class="relative z-10 flex h-screen p-4 md:p-6 pointer-events-none">
        
        <!-- Left Sidebar Panel -->
        <aside class="w-full max-w-sm h-full flex flex-col justify-between gap-4 pointer-events-auto overflow-y-auto pr-1">
            
            <div class="space-y-4">
                <!-- Profile Header Card -->
                <header class="tactical-card p-5 rounded-xl border-l-4 border-l-emerald-500">
                    <div class="flex items-center space-x-3">
                        <div class="w-11 h-11 rounded-lg bg-emerald-950/80 border border-emerald-500/40 flex items-center justify-center text-lg font-black text-emerald-400 tracking-wider">
                            AT
                        </div>
                        <div>
                            <h1 class="text-base font-bold tracking-wider text-slate-100 uppercase">Lect.Anuthep Toeiliang</h1>
                            <p class="text-[11px] text-emerald-400 font-semibold tracking-wider uppercase">3D & AR Military Portfolio</p>
                        </div>
                    </div>
                    <p class="mt-3 text-xs text-slate-300 leading-relaxed">
                        แสดงผลงานโมเดล 3D <b>Mother 3 Pork Tank</b> ในรูปแบบ WebGL และ Augmented Reality (AR) ส่องดูขนาดจริงผ่านกล้องมือถือได้ทันที
                    </p>
                </header>

                <!-- Interactive Controls Panel -->
                <section class="tactical-card p-4 rounded-xl space-y-4">
                    <h2 class="text-xs font-mono font-bold text-emerald-400 uppercase tracking-widest flex items-center gap-2">
                        <span>🎯</span> Camera Angles
                    </h2>
                    <div class="grid grid-cols-2 gap-2 text-xs">
                        <button onclick="setCamera('45deg 75deg 4m')" class="btn-tactical active py-2 px-3 rounded-lg text-left">Isometric</button>
                        <button onclick="setCamera('90deg 85deg 4m')" class="btn-tactical py-2 px-3 rounded-lg text-left">Side View</button>
                        <button onclick="setCamera('0deg 85deg 4m')" class="btn-tactical py-2 px-3 rounded-lg text-left">Front View</button>
                        <button onclick="setCamera('0deg 0deg 5m')" class="btn-tactical py-2 px-3 rounded-lg text-left">Top View</button>
                    </div>

                    <div class="border-t border-slate-700/50 pt-3">
                        <h2 class="text-xs font-mono font-bold text-emerald-400 uppercase tracking-widest flex items-center gap-2 mb-2">
                            <span>⚙️</span> Controls
                        </h2>
                        <button id="toggle-rotate" onclick="toggleAutoRotate()" class="btn-tactical active w-full py-2 px-3 rounded-lg text-left text-xs flex justify-between items-center">
                            <span>Auto Rotation</span>
                            <span id="rotate-status" class="text-[10px] bg-emerald-500/20 text-emerald-300 px-1.5 py-0.5 rounded">ON</span>
                        </button>
                    </div>
                </section>
            </div>

            <!-- Bottom Tools -->
            <footer class="tactical-card p-3 rounded-xl space-y-2 text-xs">
                <label for="file-input" class="btn-tactical w-full py-2 px-3 rounded-lg cursor-pointer flex items-center justify-center gap-2 hover:text-white">
                    <span>🪖</span> เปลี่ยนไฟล์โมเดล (.glb)
                </label>
                <input type="file" id="file-input" accept=".glb" class="hidden" onchange="loadCustomModel(event)" />
            </footer>
        </aside>

        <!-- Status Indicator -->
        <div class="ml-auto pointer-events-auto hidden md:block">
            <div class="tactical-card px-4 py-2 rounded-lg flex items-center space-x-2 border border-emerald-500/30">
                <div class="w-2.5 h-2.5 bg-emerald-400 rounded-full animate-pulse"></div>
                <span class="text-[11px] font-mono tracking-wider text-emerald-300 uppercase">AR Ready</span>
            </div>
        </div>

    </div>

    <!-- Interactive Script -->
    <script>
        const viewer = document.getElementById('tank-viewer');

        function setCamera(orbit) {
            viewer.cameraOrbit = orbit;
        }

        function toggleAutoRotate() {
            viewer.autoRotate = !viewer.autoRotate;
            const status = document.getElementById('rotate-status');
            if (viewer.autoRotate) {
                status.innerText = 'ON';
                status.className = 'text-[10px] bg-emerald-500/20 text-emerald-300 px-1.5 py-0.5 rounded';
            } else {
                status.innerText = 'OFF';
                status.className = 'text-[10px] bg-slate-500/20 text-slate-400 px-1.5 py-0.5 rounded';
            }
        }

        function loadCustomModel(event) {
            const file = event.target.files[0];
            if (file) {
                const url = URL.createObjectURL(file);
                viewer.src = url;
            }
        }
    </script>
</body>
</html>
