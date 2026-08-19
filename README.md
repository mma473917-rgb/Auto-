# ⁠index.html
<!DOCTYPE html>
<html lang="my">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Atomic Explorer - Educational Interactive 3D</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background-color: #030712;
            font-family: 'Pyidaungsu', 'Segoe UI', Tahoma, sans-serif;
            color: #ffffff;
            user-select: none;
        }

        #webcam {
            position: absolute;
            bottom: 20px;
            right: 20px;
            width: 160px;
            height: 120px;
            border-radius: 12px;
            transform: scaleX(-1);
            border: 2px solid #00d2ff;
            box-shadow: 0 0 15px rgba(0, 210, 255, 0.4);
            z-index: 10;
            object-fit: cover;
        }

        .ui-panel {
            position: absolute;
            background: rgba(10, 20, 38, 0.85);
            border: 1px solid rgba(0, 210, 255, 0.4);
            border-radius: 14px;
            padding: 16px;
            backdrop-filter: blur(10px);
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.5);
            z-index: 10;
        }

        #title-panel {
            top: 20px;
            left: 20px;
            width: 320px;
        }

        #detail-panel {
            bottom: 20px;
            left: 20px;
            width: 340px;
        }

        h2, h3 {
            margin: 0 0 8px 0;
            color: #00d2ff;
            font-size: 18px;
        }

        .subtitle {
            font-size: 13px;
            color: #9ca3af;
            margin-bottom: 12px;
        }

        .btn-group {
            display: flex;
            gap: 8px;
            margin-bottom: 12px;
        }

        .btn {
            flex: 1;
            background: rgba(0, 210, 255, 0.15);
            border: 1px solid #00d2ff;
            color: #fff;
            padding: 8px;
            border-radius: 8px;
            cursor: pointer;
            text-align: center;
            font-size: 12px;
            transition: 0.3s;
        }

        .btn:hover {
            background: rgba(0, 210, 255, 0.4);
        }

        .legend {
            display: flex;
            justify-content: space-between;
            margin-top: 10px;
            padding-top: 10px;
            border-top: 1px solid rgba(255, 255, 255, 0.1);
        }

        .legend-item {
            display: flex;
            align-items: center;
            gap: 6px;
            font-size: 12px;
            cursor: pointer;
        }

        .dot {
            width: 14px;
            height: 14px;
            border-radius: 50%;
            display: inline-block;
        }

        .accordion {
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 8px;
            margin-top: 8px;
            overflow: hidden;
        }

        .accordion-header {
            padding: 10px;
            cursor: pointer;
            font-weight: bold;
            font-size: 13px;
            display: flex;
            justify-content: space-between;
        }

        .accordion-content {
            padding: 10px;
            font-size: 12px;
            line-height: 1.6;
            color: #d1d5db;
            display: none;
            border-top: 1px solid rgba(255, 255, 255, 0.05);
        }

        .gesture-status {
            position: absolute;
            top: 20px;
            right: 20px;
            background: rgba(0, 210, 255, 0.2);
            border: 1px solid #00d2ff;
            padding: 8px 16px;
            border-radius: 20px;
            font-size: 13px;
            z-index: 10;
        }
    </style>

    <!-- Essential Libraries -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@mediapipe/camera_utils/camera_utils.js" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/@mediapipe/hands/hands.js" crossorigin="anonymous"></script>
</head>
<body>

    <video id="webcam" autoplay playsinline></video>

    <div class="gesture-status" id="gesture-status">
        👋 စနစ်: Pinch လုပ်၍ Zoom ချိန်ပါ
    </div>

    <!-- Title & Navigation Control Panel -->
    <div class="ui-panel" id="title-panel">
        <h2>အက်တမ် စူးစမ်းလေ့လာရေး</h2>
        <div class="subtitle">(Atomic Explorer)</div>
        
        <div style="font-size: 12px; margin-bottom: 6px;">ထိတွေ့မှုနှင့် အမူအရာထိန်းချုပ်မှု Controls:</div>
        <div class="btn-group">
            <div class="btn" onclick="zoomCamera(6)">🔍 ချဲ့တွင်းကြည့်ရန် (Zoom In)</div>
            <div class="btn" onclick="zoomCamera(18)">🔍 ကျုံ့ကြည့်ရန် (Zoom Out)</div>
        </div>

        <div class="legend">
            <div class="legend-item" onclick="showParticleInfo('proton')">
                <span class="dot" style="background: #ff3355;"></span> ပရိုတွန် (+)
            </div>
            <div class="legend-item" onclick="showParticleInfo('neutron')">
                <span class="dot" style="background: #22c55e;"></span> နျူထရွန် (n)
            </div>
            <div class="legend-item" onclick="showParticleInfo('electron')">
                <span class="dot" style="background: #00d2ff;"></span> အီလက်ထရွန် (-)
            </div>
        </div>
    </div>

    <!-- Detailed Information Panel -->
    <div class="ui-panel" id="detail-panel">
        <div style="font-weight: bold; font-size: 14px; margin-bottom: 8px;">အသေးစိတ်အချက်အလက်များ:</div>

        <div class="accordion">
            <div class="accordion-header" onclick="toggleAccordion('acc-neutron')">
                🟢 အမှုန်: နျူထရွန် (Neutron) <span id="arrow-acc-neutron">▼</span>
            </div>
            <div class="accordion-content" id="acc-neutron">
                • <b>ဒြပ်ထု (Mass):</b> 1.6749 × 10⁻²⁷ kg<br>
                • <b>လျှပ်စစ်ဓာတ် (Charge):</b> 0 (Neutral)<br>
                • <b>တည်နေရာ:</b> နျူကလီးယားစ်အတွင်း
            </div>
        </div>

        <div class="accordion">
            <div class="accordion-header" onclick="toggleAccordion('acc-proton')">
                🔴 အမှုန်: ပရိုတွန် (Proton) <span id="arrow-acc-proton">►</span>
            </div>
            <div class="accordion-content" id="acc-proton">
                • <b>ဒြပ်ထု (Mass):</b> 1.6726 × 10⁻²⁷ kg<br>
                • <b>လျှပ်စစ်ဓာတ် (Charge):</b> +1.602 × 10⁻¹⁹ C (+)<br>
                • <b>တည်နေရာ:</b> နျူကလီးယားစ်အတွင်း
            </div>
        </div>

        <div class="accordion">
            <div class="accordion-header" onclick="toggleAccordion('acc-electron')">
                🔵 အမှုန်: အီလက်ထရွန် (Electron) <span id="arrow-acc-electron">►</span>
            </div>
            <div class="accordion-content" id="acc-electron">
                • <b>ဒြပ်ထု (Mass):</b> 9.1093 × 10⁻³¹ kg<br>
                • <b>လျှပ်စစ်ဓာတ် (Charge):</b> -1.602 × 10⁻¹⁹ C (-)<br>
                • <b>တည်နေရာ:</b> ပြင်ပပတ်လမ်းကြောင်း
            </div>
        </div>
    </div>

    <script>
        // --- 1. Three.js Setup ---
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer({ antialias: true });

        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        document.body.appendChild(renderer.domElement);

        camera.position.set(0, 0, 16);

        // Lighting
        const ambientLight = new THREE.AmbientLight(0xffffff, 0.7);
        scene.add(ambientLight);

        const light1 = new THREE.PointLight(0xffffff, 1.2);
        light1.position.set(10, 15, 10);
        scene.add(light1);

        const light2 = new THREE.PointLight(0x00d2ff, 0.8);
        light2.position.set(-10, -15, -10);
        scene.add(light2);

        // Groups
        const atomGroup = new THREE.Group();
        const nucleusGroup = new THREE.Group();
        atomGroup.add(nucleusGroup);
        scene.add(atomGroup);

        // Materials & Geometries
        const sphereGeo = new THREE.SphereGeometry(0.45, 32, 32);
        const electronGeo = new THREE.SphereGeometry(0.22, 32, 32);

        const protonMat = new THREE.MeshPhongMaterial({ color: 0xff3355, shininess: 80 });
        const neutronMat = new THREE.MeshPhongMaterial({ color: 0x22c55e, shininess: 80 });
        const electronMat = new THREE.MeshPhongMaterial({ color: 0x00d2ff, emissive: 0x0088cc, shininess: 100 });

        const interactiveObjects = [];

        // Nucleus Creation
        const nucleusLayout = [
            { mat: protonMat, type: 'proton', pos: [0.35, 0.35, 0.35] },
            { mat: protonMat, type: 'proton', pos: [-0.35, -0.35, -0.35] },
            { mat: neutronMat, type: 'neutron', pos: [-0.35, 0.35, -0.2] },
            { mat: neutronMat, type: 'neutron', pos: [0.35, -0.35, 0.2] }
        ];

        nucleusLayout.forEach(item => {
            const mesh = new THREE.Mesh(sphereGeo, item.mat);
            mesh.position.set(...item.pos);
            mesh.userData = { type: item.type };
            nucleusGroup.add(mesh);
            interactiveObjects.push(mesh);
        });

        // Orbits & Electrons
        const orbits = [
            { rx: 5.5, ry: 3.5, rotZ: Math.PI / 4, speed: 0.025 },
            { rx: 3.5, ry: 5.5, rotZ: -Math.PI / 4, speed: 0.03 },
            { rx: 6, ry: 6, rotZ: 0, speed: 0.02 }
        ];

        const electrons = [];

        orbits.forEach(o => {
            const curve = new THREE.EllipseCurve(0, 0, o.rx, o.ry, 0, 2 * Math.PI, false, 0);
            const points = curve.getPoints(100);
            const orbitGeo = new THREE.BufferGeometry().setFromPoints(points);
            const orbitMat = new THREE.LineBasicMaterial({ color: 0x00d2ff, transparent: true, opacity: 0.3 });
            const orbitLine = new THREE.LineLoop(orbitGeo, orbitMat);

            const orbitGroup = new THREE.Group();
            orbitGroup.rotation.z = o.rotZ;
            orbitGroup.add(orbitLine);

            const elMesh = new THREE.Mesh(electronGeo, electronMat);
            elMesh.userData = { type: 'electron' };
            orbitGroup.add(elMesh);

            atomGroup.add(orbitGroup);
            interactiveObjects.push(elMesh);

            electrons.push({ mesh: elMesh, rx: o.rx, ry: o.ry, speed: o.speed, angle: Math.random() * Math.PI * 2 });
        });

        // Animation Loop
        function animate() {
            requestAnimationFrame(animate);

            electrons.forEach(el => {
                el.angle += el.speed;
                el.mesh.position.x = el.rx * Math.cos(el.angle);
                el.mesh.position.y = el.ry * Math.sin(el.angle);
            });

            nucleusGroup.rotation.y += 0.005;
            nucleusGroup.rotation.x += 0.003;

            renderer.render(scene, camera);
        }
        animate();

        // --- 2. Interactive UI Functions ---
        let targetZ = 16;
        function zoomCamera(z) {
            targetZ = z;
        }

        function updateCamera() {
            camera.position.z = THREE.MathUtils.lerp(camera.position.z, targetZ, 0.08);
            requestAnimationFrame(updateCamera);
        }
        updateCamera();

        function toggleAccordion(id) {
            const contents = document.querySelectorAll('.accordion-content');
            contents.forEach(c => {
                if (c.id === id) {
                    const isVisible = c.style.display === 'block';
                    c.style.display = isVisible ? 'none' : 'block';
                    document.getElementById(`arrow-${id}`).innerText = isVisible ? '►' : '▼';
                } else {
                    c.style.display = 'none';
                    const otherId = c.id;
                    document.getElementById(`arrow-${otherId}`).innerText = '►';
                }
            });
        }

        function showParticleInfo(type) {
            toggleAccordion(`acc-${type}`);
            if (type === 'proton' || type === 'neutron') {
                zoomCamera(6);
            } else {
                zoomCamera(14);
            }
        }

        // Raycasting Click
        const raycaster = new THREE.Raycaster();
        const mouse = new THREE.Vector2();

        window.addEventListener('pointerdown', (e) => {
            if (e.target.tagName !== 'CANVAS') return;

            mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;

            raycaster.setFromCamera(mouse, camera);
            const intersects = raycaster.intersectObjects(interactiveObjects);

            if (intersects.length > 0) {
                const particleType = intersects[0].object.userData.type;
                showParticleInfo(particleType);
            }
        });

        // --- 3. MediaPipe Hand Gesture Control ---
        const videoElement = document.getElementById('webcam');

        function onResults(results) {
            if (results.multiHandLandmarks && results.multiHandLandmarks.length > 0) {
                const landmarks = results.multiHandLandmarks[0];
                const index = landmarks[8];
                const thumb = landmarks[4];

                const distance = Math.hypot(index.x - thumb.x, index.y - thumb.y, index.z - thumb.z);

                // Map Distance to Camera Zoom
                targetZ = THREE.MathUtils.mapLinear(distance, 0.04, 0.25, 4.5, 20);
                targetZ = THREE.MathUtils.clamp(targetZ, 4.5, 20);

                document.getElementById('gesture-status').innerText = "👌 Hand Gesture: ချိတ်ဆက်ထားသည်";
            } else {
                document.getElementById('gesture-status').innerText = "👋 စနစ်: Pinch လုပ်၍ Zoom ချိန်ပါ";
            }
        }

        const hands = new Hands({
            locateFile: (file) => `https://cdn.jsdelivr.net/npm/@mediapipe/hands/${file}`
        });

        hands.setOptions({
            maxNumHands: 1,
            modelComplexity: 1,
            minDetectionConfidence: 0.6,
            minTrackingConfidence: 0.6
        });

        hands.onResults(onResults);

        const cameraFeed = new Camera(videoElement, {
            onFrame: async () => {
                await hands.send({ image: videoElement });
            },
            width: 320,
            height: 240
        });

        cameraFeed.start();

        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });
    </script>
</body>
</html

 
