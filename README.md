# The-Magic-Touch---Hands-Free-UI-Control-System-using-Mediapipe

<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>The Magic Touch: Hands-Free UI Demo</title>
    <style>
        :root {
            --primary-color: #007bff;
            --secondary-color: #6c757d;
            --background-color: #f4f7f6;
            --text-color: #333;
            --container-bg: #ffffff;
            --border-color: #dee2e6;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            background-color: var(--background-color);
            margin: 0;
            padding: 2rem;
            color: var(--text-color);
        }

        /* --- Loader Styles --- */
        #loader {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(255, 255, 255, 0.9);
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            z-index: 100;
            transition: opacity 0.5s;
        }

        #loader.hidden {
            opacity: 0;
            pointer-events: none;
        }

        .loader-content {
            text-align: center;
        }

        .loader-text {
            font-size: 1.2em;
            font-weight: 500;
            color: var(--secondary-color);
            margin-bottom: 1rem;
        }

        #progress-bar-container {
            width: 300px;
            height: 15px;
            background-color: #e9ecef;
            border-radius: 10px;
            overflow: hidden;
            box-shadow: inset 0 1px 3px rgba(0, 0, 0, 0.1);
        }

        #progress-bar {
            width: 0%;
            height: 100%;
            background-color: var(--primary-color);
            border-radius: 10px;
            transition: width 0.2s ease-out;
        }

        /* --- Main App Styles --- */
        h1 {
            color: var(--text-color);
            margin-bottom: 0.5rem;
        }

        .subtitle {
            color: var(--secondary-color);
            margin-bottom: 2rem;
            max-width: 600px;
            text-align: center;
        }

        #app-container {
            display: flex;
            gap: 2rem;
            width: 100%;
            max-width: 1200px;
            align-items: flex-start;
        }

        #video-container {
            position: relative;
            width: 640px;
            height: 480px;
            border-radius: 12px;
            overflow: hidden;
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
            background-color: #000;
        }

        video,
        canvas {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            transform: scaleX(-1);
        }

        #debug-message {
            position: absolute;
            bottom: 10px;
            left: 10px;
            background-color: rgba(0, 0, 0, 0.6);
            color: white;
            padding: 5px 10px;
            border-radius: 5px;
            font-size: 0.9em;
            z-index: 10;
        }

        .controls {
            flex-grow: 1;
            display: flex;
            flex-direction: column;
            gap: 1.5rem;
            background-color: var(--container-bg);
            padding: 2rem;
            border-radius: 12px;
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
        }

        .control-group {
            display: flex;
            flex-direction: column;
        }

        label {
            margin-bottom: 0.75rem;
            font-weight: 600;
        }

        input[type="range"] {
            width: 100%;
            cursor: pointer;
        }

        #scroll-area {
            height: 250px;
            overflow-y: auto;
            border: 1px solid var(--border-color);
            padding: 1rem;
            border-radius: 8px;
            background-color: #f8f9fa;
            line-height: 1.6;
        }

        #pinch-button {
            padding: 12px 20px;
            font-size: 1em;
            font-weight: bold;
            color: white;
            background-color: var(--primary-color);
            border: none;
            border-radius: 8px;
            cursor: pointer;
            text-align: center;
            transition: background-color 0.2s ease, transform 0.1s ease;
        }

        #pinch-button.pinched {
            background-color: #28a745;
            transform: scale(0.95);
        }
    </style>
</head>

<body>

    <div id="loader">
        <div class="loader-content">
            <span class="loader-text">Loading AI Model... 🧠</span>
            <div id="progress-bar-container">
                <div id="progress-bar"></div>
            </div>
            <span id="progress-text" class="loader-text" style="margin-top: 1rem; display: block;">0%</span>
        </div>
    </div>

    <h1>The Magic Touch ✨</h1>
    <p class="subtitle">A Hands-Free Interface Demo using MediaPipe. Control the UI with your hand!</p>

    <div id="app-container">
        <div id="video-container">
            <video id="webcam" autoplay playsinline></video>
            <canvas id="hand-canvas"></canvas>
            <div id="debug-message">Initializing...</div>
        </div>

        <div class="controls">
            <div class="control-group">
                <label for="magic-slider">Hands-Free Slider</label>
                <input type="range" id="magic-slider" min="0" max="100" value="50">
            </div>

            <div class="control-group">
                <label for="scroll-area">Hands-Free Scroller</label>
                <div id="scroll-area">
                    <h3>What is MediaPipe?</h3>
                    <p>MediaPipe is Google's open-source framework for building fast, cross-platform machine learning
                        pipelines. It offers ready-to-use solutions for tasks like hand tracking and face detection that
                        run entirely in the browser, making advanced AI accessible to web developers.</p>
                    <p>The Hand Landmarker model detects 21 key points on your hand in real-time. We use the 'x'
                        coordinate of your index finger to control the slider and the 'y' coordinate to scroll. For the
                        button, we calculate the distance between your thumb and index finger to detect a "pinch"
                        gesture.</p>
                    <p>This technology opens up new frontiers for web accessibility, allowing users with motor
                        impairments to navigate the web. It also has exciting applications in augmented reality, gaming,
                        and creating more intuitive, natural user interfaces for everyone.</p>
                </div>
            </div>

            <div class="control-group">
                <label for="pinch-button">"Pinch" to Click Me!</label>
                <button id="pinch-button">Click Me</button>
            </div>
        </div>
    </div>

    <script type="module">
        import { HandLandmarker, FilesetResolver, DrawingUtils } from "https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision@0.10.22-rc.20250304/vision_bundle.mjs";

        // --- DOM Elements ---
        const video = document.getElementById('webcam');
        const canvasElement = document.getElementById('hand-canvas');
        const canvasCtx = canvasElement.getContext('2d');
        const debugMessage = document.getElementById('debug-message');
        const slider = document.getElementById('magic-slider');
        const scrollArea = document.getElementById('scroll-area');
        const pinchButton = document.getElementById('pinch-button');
        const loader = document.getElementById('loader');
        const progressBar = document.getElementById('progress-bar');
        const progressText = document.getElementById('progress-text');

        let handLandmarker;
        let runningMode = "VIDEO";
        let webcamRunning = false;
        const modelUrl = 'https://storage.googleapis.com/mediapipe-models/hand_landmarker/hand_landmarker/float16/1/hand_landmarker.task';

        // --- 1. Load the Model with Progress ---
        const loadModel = async () => {
            try {
                const response = await fetch(modelUrl);
                if (!response.ok) throw new Error(`HTTP error! Status: ${response.status}`);

                const totalSize = Number(response.headers.get('content-length'));
                let loadedSize = 0;

                const reader = response.body.getReader();
                const stream = new ReadableStream({
                    start(controller) {
                        function push() {
                            reader.read().then(({ done, value }) => {
                                if (done) {
                                    controller.close();
                                    return;
                                }
                                loadedSize += value.length;
                                const percentage = Math.round((loadedSize / totalSize) * 100);
                                progressBar.style.width = `${percentage}%`;
                                progressText.innerText = `${percentage}%`;
                                controller.enqueue(value);
                                push();
                            });
                        }
                        push();
                    }
                });

                const modelBuffer = await new Response(stream).arrayBuffer();
                initializeHandLandmarker(modelBuffer);

            } catch (error) {
                console.error("Failed to load model:", error);
                loader.innerText = "Failed to load AI Model. Please refresh the page.";
            }
        };
        loadModel();

        // --- 2. Initialize HandLandmarker ---
        const initializeHandLandmarker = async (modelBuffer) => {
            progressText.innerText = 'Initializing AI Engine...';
            const vision = await FilesetResolver.forVisionTasks("https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision@0.10.22-rc.20250304/wasm");

            handLandmarker = await HandLandmarker.createFromOptions(vision, {
                baseOptions: {
                    modelAssetBuffer: new Uint8Array(modelBuffer),
                    delegate: "GPU"
                },
                runningMode: runningMode,
                numHands: 1
            });

            loader.classList.add('hidden');
            debugMessage.innerText = "Model loaded. Enable webcam to start.";
            enableWebcam();
        };

        // --- 3. Enable Webcam & Start Loop ---
        const enableWebcam = () => {
            navigator.mediaDevices.getUserMedia({ video: true }).then((stream) => {
                video.srcObject = stream;
                video.addEventListener("loadeddata", () => {
                    webcamRunning = true;
                    predictWebcam();
                });
            }).catch(err => {
                console.error("Webcam access error:", err);
                debugMessage.innerText = "Please allow webcam access.";
            });
        };

        // --- 4. Prediction Loop ---
        let lastVideoTime = -1;
        const predictWebcam = async () => {
            if (!webcamRunning || !handLandmarker) return;

            if (video.currentTime !== lastVideoTime) {
                lastVideoTime = video.currentTime;
                const results = handLandmarker.detectForVideo(video, performance.now());
                processResults(results);
            }
            window.requestAnimationFrame(predictWebcam);
        };

        // --- 5. Process Results & Control UI ---
        const processResults = (results) => {
            canvasElement.width = video.videoWidth;
            canvasElement.height = video.videoHeight;
            canvasCtx.save();
            canvasCtx.clearRect(0, 0, canvasElement.width, canvasElement.height);

            if (results.landmarks && results.landmarks.length > 0) {
                const landmarks = results.landmarks[0];
                const drawingUtils = new DrawingUtils(canvasCtx);
                drawingUtils.drawLandmarks(landmarks, { color: "#FF0000", lineWidth: 2, radius: 4 });
                drawingUtils.drawConnectors(landmarks, HandLandmarker.HAND_CONNECTIONS, { color: "#00FF00", lineWidth: 3 });

                const indexFingerTip = landmarks[8];
                const thumbTip = landmarks[4];

                slider.value = (1 - indexFingerTip.x) * 100;

                const scrollValue = indexFingerTip.y * (scrollArea.scrollHeight - scrollArea.clientHeight);
                scrollArea.scrollTop = scrollValue;

                const distance = Math.hypot(indexFingerTip.x - thumbTip.x, indexFingerTip.y - thumbTip.y);
                const pinchThreshold = 0.04;

                if (distance < pinchThreshold) {
                    if (!pinchButton.classList.contains('pinched')) {
                        pinchButton.classList.add('pinched');
                        pinchButton.click();
                    }
                } else {
                    pinchButton.classList.remove('pinched');
                }
                debugMessage.innerText = `Pinch Distance: ${distance.toFixed(3)}`;
            } else {
                debugMessage.innerText = "No hand detected. Show me your hand! 🖐️";
            }
            canvasCtx.restore();
        };

        pinchButton.addEventListener('click', () => {
            console.log("Pinch click triggered!");
            // You can replace alert with a less disruptive action for a smooth demo
            // alert("Button Clicked via Pinch Gesture! 🤏"); 
        });
    </script>
</body>

</html>
