# sutudy-Timer
<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>কোর্স ফোকাস টাইমার</title>
    <!-- Tailwind CSS CDN --><script src="https://cdn.tailwindcss.com"></script>
    <!-- Inter Font --><link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700;900&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary-color: #3b82f6; /* Blue 500 */
        }
        body {
            background: linear-gradient(135deg, #2b3a4a 0%, #1a202c 100%); 
            background-attachment: fixed;
            color: #e2e8f0; 
            font-family: 'Inter', sans-serif;
        }
        .glass-container {
            background-color: rgba(255, 255, 255, 0.1); 
            backdrop-filter: blur(15px); 
            -webkit-backdrop-filter: blur(15px);
            border: 1px solid rgba(255, 255, 255, 0.15); 
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.3); 
        }
        .timer-display { 
            font-weight: 900;
            color: #f7fafc; 
            text-shadow: 2px 2px 8px rgba(0, 0, 0, 0.25); 
            line-height: 1; 
        }
        .btn-primary {
            transition: all 0.3s ease-in-out;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
        }
        .btn-primary:hover {
            transform: translateY(-3px);
            box-shadow: 0 6px 15px rgba(0, 0, 0, 0.3);
        }
        /* --- REPORT CARD GLOW EFFECT START --- */
        .report-card {
            backdrop-filter: blur(5px);
            -webkit-backdrop-filter: blur(5px);
            border: 1px solid rgba(255, 255, 255, 0.1); 
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
            transition: all 0.3s ease-in-out; /* Changed to 'all' for better transition */
            color: #ffffff;
            position: relative;
        }
        .report-card:hover {
            transform: translateY(-8px) scale(1.02); /* More lift and slight expansion */
            /* Stronger, diffused blue shadow to simulate glow */
            box-shadow: 0 10px 30px rgba(59, 130, 246, 0.4), /* Primary Blue glow */
                        0 0 15px rgba(255, 255, 255, 0.1); /* White highlight */
            border-color: rgba(59, 130, 246, 0.5); /* Highlight border */
        }
        /* --- REPORT CARD GLOW EFFECT END --- */
        .modal-content {
            background-color: rgba(30, 41, 59, 0.95); 
            backdrop-filter: blur(8px);
            -webkit-backdrop-filter: blur(8px);
            border: 1px solid rgba(255, 255, 255, 0.2);
            color: #e2e8f0;
        }
        .modal-content .text-gray-900 {
            color: #f7fafc;
        }

        /* --- NEW: Shutter/Flip Animation CSS for Digits --- */
        @keyframes flip-in {
            0% { transform: perspective(400px) rotateX(-90deg); opacity: 0; }
            50% { transform: perspective(400px) rotateX(0deg); opacity: 1; }
            100% { transform: perspective(400px) rotateX(0deg); opacity: 1; }
        }
        .shutter-animation {
            animation: flip-in 0.4s ease-out; 
            display: inline-block;
        }

        /* --- Custom Gradients for Reports --- */
        .grad-total { background: linear-gradient(135deg, #1f2937 0%, #4b5563 100%); }
        .grad-1 { background: linear-gradient(135deg, #FFA000 0%, #CC8000 100%); }
        .grad-2 { background: linear-gradient(135deg, #101235 0%, #000223 100%); }
        .grad-3 { background: linear-gradient(135deg, #996633 0%, #7C521B 100%); }
        .grad-4 { background: linear-gradient(135deg, #5D8055 0%, #76A66A 100%); }
        .grad-5 { background: linear-gradient(135deg, #A8B530 0%, #C4CD5C 100%); }
        .grad-6 { background: linear-gradient(135deg, #B30015 0%, #E6001B 100%); }
        .grad-7 { background: linear-gradient(135deg, #005A7A 0%, #007AAD 100%); }
        .grad-8 { background: linear-gradient(135deg, #5C6A78 0%, #7D8893 100%); }

        /* Responsive Adjustments */
        @media (max-width: 640px) {
            #liveClock {
                top: 0.5rem;
                right: 0.5rem;
                padding: 0.5rem 0.75rem;
                font-size: 0.875rem;
            }
            .timer-segment {
                /* Increased size for better mobile display, matching the larger desktop size */
                min-width: 100px; 
                padding: 0.5rem 0.25rem;
            }
        }
        
    </style>
</head>
<body class="min-h-screen flex items-center justify-center p-4">

    <!-- Live Clock Display (Top Right) --><div id="liveClock" class="fixed top-4 right-4 text-white text-lg font-semibold bg-gray-900 bg-opacity-70 p-3 rounded-xl shadow-lg z-50 backdrop-blur-sm transition duration-300 hover:bg-opacity-90 border border-gray-700">
        লোডিং...
    </div>

    <!-- Main Container: Changed max-w-4xl to max-w-2xl -->
    <div class="w-full max-w-2xl glass-container rounded-xl p-6 md:p-10">

        <h1 class="text-3xl font-bold text-center text-white mb-2">স্টাডি ট্র্যাকার</h1>
        
        <!-- NEW: Motivation Message Display -->
        <p id="motivationMessage" class="text-center text-amber-300 font-semibold mb-8 h-6">
            পড়ার সেশন শুরু হলে এখানে অনুপ্রেরণা দেখতে পাবেন।
        </p>

        <!-- Loading Indicator for Firebase --><div id="loading" class="text-center text-lg text-gray-400 mb-4">
            <p>ডেটাবেস লোড হচ্ছে... অনুগ্রহ করে অপেক্ষা করুন।</p>
        </div>

        <!-- --- NAVIGATION TABS --- -->
        <div id="appTabs" class="hidden flex justify-center mb-8 border-b-2 border-gray-700">
            <!-- Timer Tab: Default Indigo style -->
            <button id="timerTab" data-view="timer" class="p-3 px-6 text-lg font-semibold transition duration-200">
                ⏱️ টাইমার
            </button>
            <!-- Report Tab: Unique Amber style -->
            <button id="reportTab" data-view="report" class="p-3 px-6 text-lg font-semibold transition duration-200">
                📊 রিপোর্ট
            </button>
        </div>
        <!-- --- END NAVIGATION TABS --- -->


        <!-- --- 1. TIMER VIEW (Default) --- -->
        <div id="timerView" class="hidden">
            
            <!-- Course Selector --><div class="mb-8 flex flex-col items-center">
                <label for="courseSelect" class="text-xl font-semibold text-gray-200 mb-3">
                    বর্তমানে যে কোর্সটি পড়ছেন:
                </label>
                <select id="courseSelect" class="w-full max-w-sm p-3 border-2 border-gray-600 rounded-lg text-lg font-medium shadow-sm focus:border-blue-400 focus:ring focus:ring-blue-500 focus:ring-opacity-50 transition duration-150 ease-in-out bg-gray-800 text-white bg-opacity-80">
                    <option value="" disabled selected>--- একটি কোর্স নির্বাচন করুন ---</option>
                    <!-- Options will be populated by JS --></select>
            </div>

            <!-- Large Timer Display (Segmented) -->
            <div class="text-center mb-10">
                <div class="flex justify-center items-center space-x-2 md:space-x-4 select-none">
                    
                    <!-- Hour Segment: Increased font size and min-width -->
                    <div class="timer-segment bg-indigo-700 p-3 md:p-4 rounded-xl shadow-2xl min-w-[110px] md:min-w-[150px]">
                        <span id="timerHours" class="timer-display text-6xl md:text-8xl">00</span>
                        <p class="text-xs md:text-sm text-gray-300 font-medium mt-1">ঘন্টা</p>
                    </div>
                    <!-- Separator -->
                    <span class="timer-separator text-5xl md:text-8xl font-thin text-gray-400">:</span>
                    
                    <!-- Minute Segment: Increased font size and min-width -->
                    <div class="timer-segment bg-gray-700 p-3 md:p-4 rounded-xl shadow-2xl min-w-[110px] md:min-w-[150px]">
                        <span id="timerMinutes" class="timer-display text-6xl md:text-8xl">00</span>
                        <p class="text-xs md:text-sm text-gray-300 font-medium mt-1">মিনিট</p>
                    </div>
                    <!-- Separator -->
                    <span class="timer-separator text-5xl md:text-8xl font-thin text-gray-400">:</span>

                    <!-- Second Segment: Increased font size and min-width -->
                    <div class="timer-segment bg-gray-700 p-3 md:p-4 rounded-xl shadow-2xl min-w-[110px] md:min-w-[150px]">
                        <span id="timerSeconds" class="timer-display text-6xl md:text-8xl">00</span>
                        <p class="text-xs md:text-sm text-gray-300 font-medium mt-1">সেকেন্ড</p>
                    </div>
                </div>
                <p id="statusMessage" class="text-red-400 font-semibold mt-4 h-6"></p>
            </div>
            <!-- End of Segmented Timer Display -->

            <!-- Control Buttons --><div class="flex justify-center space-x-4 mb-12">
                <button id="startButton" class="btn-primary bg-green-600 hover:bg-green-700 text-white font-bold py-3 px-6 rounded-lg shadow-md transition duration-200" disabled>
                    ▶️ শুরু করুন
                </button>
                <button id="pauseButton" class="btn-primary bg-red-600 hover:bg-red-700 text-white font-bold py-3 px-6 rounded-lg shadow-md transition duration-200" disabled>
                    ⏸️ থামান
                </button>
                <button id="resetButton" class="btn-primary bg-gray-600 hover:bg-gray-700 text-white font-bold py-3 px-6 rounded-lg shadow-md transition duration-200" disabled>
                    🔄 রিসেট
                </button>
            </div>
        </div>
        <!-- --- END TIMER VIEW --- -->


        <!-- --- 2. REPORT VIEW (Initially Hidden) --- -->
        <div id="reportView" class="hidden mt-8 pt-2">
            <h2 class="text-2xl font-bold text-white mb-6 text-center">মোট অধ্যয়নের রিপোর্ট</h2>
            <div id="reportContainer" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                <!-- Reports will be inserted here -->
                <div class="text-center text-gray-400 col-span-full">রিপোর্ট লোড হচ্ছে...</div>
            </div>
        </div>
        <!-- --- END REPORT VIEW --- -->
            
        <div class="mt-8 text-sm text-gray-400 text-center">
            ইউজার আইডি: <span id="userIdDisplay" class="font-mono text-xs bg-gray-800 p-1 rounded"></span>
        </div>
    </div>

    <!-- Confirmation Modal for Reset (Hidden by default) --><div id="confirmationModal" class="fixed inset-0 bg-gray-900 bg-opacity-75 hidden flex items-center justify-center p-4 z-50">
        <div class="bg-white rounded-xl shadow-2xl p-6 w-full max-w-sm transform transition-all duration-300 scale-100 modal-content">
            <h3 class="text-xl font-bold text-gray-900 mb-4">⚠️ নিশ্চিত করুন</h3>
            <p class="text-gray-700 mb-6">
                আপনি কি নিশ্চিত যে আপনি কোর্স **<span id="modalCourseName" class="font-bold text-red-400"></span>** এর জন্য সেভ করা সমস্ত ডেটা স্থায়ীভাবে মুছে ফেলতে চান? এই অ্যাকশনটি পূর্বাবস্থায় ফেরানো যাবে না।
            </p>
            <div class="flex justify-end space-x-3">
                <button id="cancelResetButton" class="bg-gray-600 hover:bg-gray-500 text-white font-medium py-2 px-4 rounded-lg transition duration-150">
                    না, বাতিল করুন
                </button>
                <button id="confirmResetButton" class="bg-red-600 hover:bg-red-700 text-white font-bold py-2 px-4 rounded-lg shadow-md transition duration-150">
                    হ্যাঁ, রিসেট করুন
                </button>
            </div>
        </div>
    </div>

    <!-- Firebase SDKs --><script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, addDoc, onSnapshot, collection, query, serverTimestamp, setLogLevel, where, getDocs, deleteDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Global variables provided by the environment (MUST be used)
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        // --- Core Variables ---
        let app, db, auth;
        let userId = null;
        let timerInterval = null;
        let motivationInterval = null; // NEW: Motivation interval
        let startTime = 0;
        let elapsedTime = 0;
        let isRunning = false;
        let courseIdToReset = null; 
        
        // State for current view
        let currentView = 'timer'; // 'timer' or 'report'
        
        // Course data
        const courses = [
            { id: 'IS', name: 'Information Systems (IS)', gradient: 'grad-1' },
            { id: 'TQM', name: 'Total Quality Management (TQM)', gradient: 'grad-2' },
            { id: 'PYTHON', name: 'Programming (PYTHON)', gradient: 'grad-3' },
            { id: 'STAT', name: 'Statistics (STAT)', gradient: 'grad-4' },
            { id: 'HRM', name: 'Human Resource Management (HRM)', gradient: 'grad-5' },
            { id: 'SBE', name: 'Small Business Entrepreneurship (SBE)', gradient: 'grad-6' },
            { id: 'ACCOUNTING', name: 'Accounting (হিসাববিজ্ঞান)', gradient: 'grad-7' },
            { id: 'MBAPREP', name: 'MBA Preparation (এমবিএ প্রস্তুতি)', gradient: 'grad-8' }
        ];
        
        // --- Motivation Data ---
        const motivationMessages = [
            "ফোকাসড থাকো, প্রতিটি মিনিট মূল্যবান!",
            "এখনকার পরিশ্রম, ভবিষ্যতের আনন্দ।",
            "এক ধাপ, এক মুহূর্ত—এগিয়ে চলো!",
            "তোমার লক্ষ্যের দিকে মন দাও। তুমি পারবে!",
            "পড়ার অভ্যাস তোমাকে বিজয়ী করবে।",
            "জ্ঞানই শক্তি, আর তুমি সেটাই অর্জন করছো।",
            "প্রতিটি সেশন তোমাকে আরও একধাপ এগিয়ে নিয়ে যাচ্ছে।",
            "ধৈর্য ধরো, লক্ষ্য তোমার হাতেই!",
            "বিরতি শেষে আবার নতুন উদ্যমে শুরু করো।",
        ];

        // --- DOM Elements ---
        const courseSelect = document.getElementById('courseSelect');
        const startButton = document.getElementById('startButton');
        const pauseButton = document.getElementById('pauseButton');
        const resetButton = document.getElementById('resetButton');
        const statusMessage = document.getElementById('statusMessage');
        const motivationMessage = document.getElementById('motivationMessage'); // NEW: Motivation message element
        const reportContainer = document.getElementById('reportContainer');
        const loadingDiv = document.getElementById('loading');
        const appTabsDiv = document.getElementById('appTabs'); 
        const userIdDisplay = document.getElementById('userIdDisplay');
        const liveClockDisplay = document.getElementById('liveClock'); 

        // VIEWS
        const timerView = document.getElementById('timerView'); 
        const reportView = document.getElementById('reportView'); 
        
        // TABS
        const timerTab = document.getElementById('timerTab'); 
        const reportTab = document.getElementById('reportTab'); 


        // TIMER SEGMENTS
        const timerHours = document.getElementById('timerHours');
        const timerMinutes = document.getElementById('timerMinutes');
        const timerSeconds = document.getElementById('timerSeconds');

        // Modal Elements
        const confirmationModal = document.getElementById('confirmationModal');
        const confirmResetButton = document.getElementById('confirmResetButton');
        const cancelResetButton = document.getElementById('cancelResetButton');


        // --- Utility Functions ---

        const getFormattedTimeParts = (ms) => {
            const totalSeconds = Math.floor(ms / 1000);
            const hours = String(Math.floor(totalSeconds / 3600)).padStart(2, '0');
            const minutes = String(Math.floor((totalSeconds % 3600) / 60)).padStart(2, '0');
            const seconds = String(totalSeconds % 60).padStart(2, '0');
            return { hours, minutes, seconds, totalSeconds };
        };

        const formatTime = (ms) => {
            const { hours, minutes, seconds } = getFormattedTimeParts(ms);
            return `${hours}:${minutes}:${seconds}`;
        };

        const updateLiveClock = () => {
            const now = new Date();
            const timeOptions = { hour: '2-digit', minute: '2-digit', second: '2-digit', hour12: true };
            const dateOptions = { day: 'numeric', month: 'short', year: 'numeric' };

            const timeString = now.toLocaleTimeString('bn-BD', timeOptions);
            const dateString = now.toLocaleDateString('bn-BD', dateOptions);

            liveClockDisplay.innerHTML = `
                <div class="text-sm text-gray-300 font-normal">${dateString}</div>
                <div class="text-xl font-bold">${timeString}</div>
            `;
        };
        
        // --- View Switching Function for Unique Report Tab ---
        const switchView = (viewName) => {
            currentView = viewName;
            
            // Define common classes to remove from both tabs before setting new state
            const resetClasses = ['border-indigo-500', 'border-amber-500', 'border-transparent', 'text-white', 'text-gray-400', 'hover:border-gray-500', 'hover:border-amber-400', 'shadow-lg', 'shadow-amber-500/30'];

            // 1. Timer Tab (Indigo)
            if (viewName === 'timer') {
                timerView.classList.remove('hidden');
                reportView.classList.add('hidden');

                // Timer Active Style (Indigo border)
                timerTab.classList.remove(...resetClasses);
                timerTab.classList.add('border-b-4', 'border-indigo-500', 'text-white');

                // Report Inactive Style (Gray text, subtle Amber hover)
                reportTab.classList.remove(...resetClasses);
                reportTab.classList.add('border-b-4', 'border-transparent', 'text-gray-400', 'hover:border-amber-400');
                
            // 2. Report Tab (Amber) - UNIQUE STYLE
            } else if (viewName === 'report') {
                timerView.classList.add('hidden');
                reportView.classList.remove('hidden');

                // Timer Inactive Style (Gray text, subtle Gray hover)
                timerTab.classList.remove(...resetClasses);
                timerTab.classList.add('border-b-4', 'border-transparent', 'text-gray-400', 'hover:border-gray-500');
                
                // Report Active Style (Amber border + Glow for uniqueness)
                reportTab.classList.remove(...resetClasses);
                reportTab.classList.add('border-b-4', 'border-amber-500', 'text-white', 'shadow-lg', 'shadow-amber-500/30');
            }
        };


        // Renders the total study time report
        const renderReport = (totals) => {
            reportContainer.innerHTML = '';
            
            const totalStudyTime = courses.reduce((acc, course) => acc + (totals[course.id] || 0), 0);
            const totalFormatted = formatTime(totalStudyTime);
            
            // Total Summary Card
            reportContainer.innerHTML = `
                <div class="grad-total border-l-4 border-indigo-400 rounded-lg p-4 shadow-sm col-span-full md:col-span-1 lg:col-span-1 report-card">
                    <p class="text-sm font-medium text-gray-300">মোট অধ্যয়নের সময়</p>
                    <p class="text-2xl font-bold text-white">${totalFormatted}</p>
                </div>
            `;
            
            courses.forEach(course => {
                const totalMs = totals[course.id] || 0;
                const formattedTime = formatTime(totalMs);
                const percentage = totalStudyTime > 0 ? ((totalMs / totalStudyTime) * 100).toFixed(1) : 0;
                
                const card = document.createElement('div');
                card.className = `report-card p-4 rounded-lg ${course.gradient}`; 
                card.innerHTML = `
                    <p class="text-base font-semibold text-gray-100">${course.name}</p>
                    <p class="text-xl font-extrabold text-white mt-1">${formattedTime}</p>
                    <div class="mt-2 h-2 bg-gray-700 rounded-full">
                        <div class="h-full bg-indigo-400 rounded-full" style="width: ${percentage}%;"></div> 
                    </div>
                    <div class="flex justify-between items-center mt-3">
                        <p class="text-xs text-gray-300">${percentage}% মোট সময়ের</p>
                        <button data-course-id="${course.id}" class="reset-btn text-xs text-red-400 hover:text-red-300 font-medium py-1 px-2 rounded hover:bg-white hover:bg-opacity-10 transition duration-150">
                            টোটাল রিসেট করুন 🗑️
                        </button>
                    </div>
                `;
                reportContainer.appendChild(card);
            });
            reportContainer.className = "grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4";
        };

        // --- Motivation Functions (NEW) ---
        
        const displayMotivation = () => {
            const randomIndex = Math.floor(Math.random() * motivationMessages.length);
            motivationMessage.textContent = motivationMessages[randomIndex];
        };

        const clearMotivation = () => {
            if (motivationInterval) {
                clearInterval(motivationInterval);
                motivationInterval = null;
            }
            motivationMessage.textContent = "পড়ার সেশন শুরু হলে এখানে অনুপ্রেরণা দেখতে পাবেন।";
        };


        // --- Timer Functions ---
        
        // Helper function to update segment with animation
        const updateSegmentWithAnimation = (element, newValue) => {
            const oldValue = element.textContent;
            if (oldValue !== newValue) {
                // 1. Apply animation class
                element.classList.add('shutter-animation');
                
                // 2. Update the content immediately after applying the class
                element.textContent = newValue;

                // 3. Remove animation class after the duration of the animation (400ms)
                setTimeout(() => {
                    element.classList.remove('shutter-animation');
                }, 400); 
            }
        };


        const updateTimer = () => {
            if (isRunning) {
                elapsedTime = Date.now() - startTime;
                const { hours, minutes, seconds } = getFormattedTimeParts(elapsedTime);

                // Update individual segments using the new animation logic
                if(timerHours && timerMinutes && timerSeconds) {
                    updateSegmentWithAnimation(timerHours, hours); 
                    updateSegmentWithAnimation(timerMinutes, minutes); 
                    updateSegmentWithAnimation(timerSeconds, seconds);
                }
            }
        };

        const startTimer = () => {
            const selectedCourse = courseSelect.value;
            if (!selectedCourse) {
                statusMessage.textContent = "অনুগ্রহ করে একটি কোর্স নির্বাচন করুন!";
                return;
            }
            statusMessage.textContent = "";

            if (!isRunning) {
                isRunning = true;
                startTime = Date.now() - elapsedTime;
                timerInterval = setInterval(updateTimer, 1000);
                
                startButton.disabled = true;
                pauseButton.disabled = false;
                resetButton.disabled = false;
                courseSelect.disabled = true;

                startButton.textContent = "পড়া চলছে...";

                // NEW: Start motivation message rotation
                displayMotivation(); // Display first message immediately
                if (motivationInterval) clearInterval(motivationInterval);
                motivationInterval = setInterval(displayMotivation, 60000); // Update every 1 minute (60000ms)
                
            }
        };

        const pauseTimer = async () => {
            if (isRunning) {
                clearInterval(timerInterval);
                isRunning = false;
                
                clearMotivation(); // NEW: Clear motivation interval

                const selectedCourse = courseSelect.value;
                const durationMs = elapsedTime;

                if (durationMs > 1000) { 
                    await saveSession(selectedCourse, durationMs);
                    statusMessage.textContent = `✅ ${selectedCourse}-এর জন্য সময় সেভ করা হলো: ${formatTime(durationMs)}`;
                    statusMessage.classList.remove('text-red-400', 'text-gray-600');
                    statusMessage.classList.add('text-green-400');
                } else {
                     statusMessage.textContent = `সেশনটি খুব ছোট ছিল, সেভ করা হয়নি।`;
                     statusMessage.classList.remove('text-green-400', 'text-gray-600');
                     statusMessage.classList.add('text-red-400');
                }

                elapsedTime = 0;
                if(timerHours && timerMinutes && timerSeconds) {
                    timerHours.textContent = "00";
                    timerMinutes.textContent = "00";
                    timerSeconds.textContent = "00";
                }
                
                startButton.disabled = false;
                pauseButton.disabled = true;
                resetButton.disabled = true;
                courseSelect.disabled = false;
                startButton.textContent = "▶️ শুরু করুন";
            }
        };

        const resetTimer = () => {
            if (isRunning) {
                clearInterval(timerInterval);
                isRunning = false;
            }
            clearMotivation(); // NEW: Clear motivation interval

            elapsedTime = 0;
            if(timerHours && timerMinutes && timerSeconds) {
                timerHours.textContent = "00";
                timerMinutes.textContent = "00";
                timerSeconds.textContent = "00";
            }

            startButton.disabled = false;
            pauseButton.disabled = true;
            resetButton.disabled = true;
            courseSelect.disabled = false;
            startButton.textContent = "▶️ শুরু করুন";
            statusMessage.textContent = "টাইমার রিসেট করা হয়েছে (এই সেশনের সময় সেভ হয়নি)।";
            statusMessage.classList.remove('text-green-400');
            statusMessage.classList.add('text-gray-400');
        };

        // --- Confirmation Modal Functions ---

        const showConfirmationModal = (courseId) => {
            courseIdToReset = courseId;
            const courseName = courses.find(c => c.id === courseId)?.name || courseId;
            document.getElementById('modalCourseName').textContent = courseName;
            confirmationModal.classList.remove('hidden');
        };

        const hideConfirmationModal = () => {
            confirmationModal.classList.add('hidden');
            courseIdToReset = null;
        };
        
        // --- Firestore Functions ---

        const getStudySessionsCollectionRef = () => {
            return collection(db, `artifacts/${appId}/public/data/study_sessions`);
        };

        const saveSession = async (courseId, durationMs) => {
            try {
                const sessionData = {
                    userId: userId, 
                    course: courseId,
                    durationMs: durationMs,
                    timestamp: serverTimestamp() 
                };
                await addDoc(getStudySessionsCollectionRef(), sessionData);
                console.log("Session saved successfully to Firestore.");
            } catch (error) {
                console.error("Error saving session to Firestore:", error);
            }
        };

        const resetCourseTotal = async () => {
            if (!courseIdToReset || !db) {
                hideConfirmationModal();
                return;
            }

            const courseId = courseIdToReset;
            hideConfirmationModal(); 

            statusMessage.textContent = `Deleting records for ${courseId}...`;
            statusMessage.classList.remove('text-red-400', 'text-green-400', 'text-gray-400');
            statusMessage.classList.add('text-yellow-400');

            try {
                const q = query(getStudySessionsCollectionRef(), where('userId', '==', userId), where('course', '==', courseId));
                const querySnapshot = await getDocs(q);

                if (querySnapshot.empty) {
                    statusMessage.textContent = `❌ ${courseId} এর জন্য কোনো ডেটা পাওয়া যায়নি।`;
                    statusMessage.classList.remove('text-yellow-400');
                    statusMessage.classList.add('text-red-400');
                    return;
                }

                const deletePromises = [];
                querySnapshot.forEach((doc) => {
                    deletePromises.push(deleteDoc(doc.ref));
                });

                await Promise.all(deletePromises);

                statusMessage.textContent = `✅ ${courseId} এর মোট ${querySnapshot.size} টি সেশন রিসেট করা হয়েছে।`;
                statusMessage.classList.remove('text-yellow-400', 'text-red-400');
                statusMessage.classList.add('text-green-400');
            } catch (error) {
                console.error("Error resetting course total:", error);
                statusMessage.textContent = `❌ ডেটা রিসেট করার সময় ত্রুটি: ${error.message}`;
                statusMessage.classList.remove('text-yellow-400');
                statusMessage.classList.add('text-red-400');
            }
            courseIdToReset = null;
        };


        // Real-time listener for report data
        const setupReportListener = () => {
            if (!db || !userId) return;

            const q = query(getStudySessionsCollectionRef(), where('userId', '==', userId));

            onSnapshot(q, (querySnapshot) => {
                const totals = {};
                querySnapshot.forEach((doc) => {
                    const data = doc.data();
                    if (data.userId === userId) { 
                        const courseId = data.course;
                        const duration = data.durationMs || 0;
                        totals[courseId] = (totals[courseId] || 0) + duration;
                    }
                });
                renderReport(totals);
            }, (error) => {
                console.error("Error setting up Firestore listener:", error);
            });
        };

        // --- Initialization ---

        const initFirebase = async () => {
            try {
                app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);
                setLogLevel('error'); 

                // 1. Handle Authentication
                await new Promise(resolve => {
                    const unsubscribe = onAuthStateChanged(auth, async (user) => {
                        if (!user && initialAuthToken) {
                             await signInWithCustomToken(auth, initialAuthToken);
                        } else if (!user) {
                            await signInAnonymously(auth);
                        }
                        
                        userId = auth.currentUser?.uid || crypto.randomUUID();
                        userIdDisplay.textContent = userId;
                        unsubscribe(); 
                        resolve();
                    });
                });
                
                // 2. Initialize App Content
                populateCourseSelector();
                setupEventListeners();
                setupReportListener(); 

                loadingDiv.classList.add('hidden');
                appTabsDiv.classList.remove('hidden');
                
                // Set initial view to Timer
                switchView('timer'); 
                updateControlButtons();
                
                // Start the live clock update
                updateLiveClock(); 
                setInterval(updateLiveClock, 1000);


            } catch (error) {
                console.error("Firebase initialization failed:", error);
                loadingDiv.textContent = `Firebase লোড করতে সমস্যা হয়েছে: ${error.message}`;
            }
        };

        const populateCourseSelector = () => {
            courseSelect.querySelectorAll('option:not([disabled])').forEach(option => option.remove());
            
            courses.forEach(course => {
                const option = document.createElement('option');
                option.value = course.id;
                option.textContent = course.name;
                courseSelect.appendChild(option);
            });
        };

        const updateControlButtons = () => {
            const isCourseSelected = !!courseSelect.value;
            startButton.disabled = !isCourseSelected || isRunning;
            pauseButton.disabled = !isRunning;
            resetButton.disabled = !isRunning;
        }

        const setupEventListeners = () => {
            startButton.addEventListener('click', startTimer);
            pauseButton.addEventListener('click', pauseTimer);
            resetButton.addEventListener('click', resetTimer);
            courseSelect.addEventListener('change', updateControlButtons);

            // Tab switching listeners
            timerTab.addEventListener('click', () => switchView('timer'));
            reportTab.addEventListener('click', () => switchView('report'));


            // Event Delegation for dynamic reset buttons in the report
            reportContainer.addEventListener('click', (e) => {
                if (e.target.classList.contains('reset-btn')) {
                    const courseId = e.target.getAttribute('data-course-id');
                    showConfirmationModal(courseId);
                }
            });

            // Listeners for the confirmation modal buttons
            confirmResetButton.addEventListener('click', resetCourseTotal);
            cancelResetButton.addEventListener('click', hideConfirmationModal);
        };

        // Start the application
        initFirebase();
    </script>
</body>
</html>
