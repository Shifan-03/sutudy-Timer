# sutudy-Timer
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>কোর্স ফোকাস টাইমার</title>
    <!-- Tailwind CSS CDN --><script src="https://cdn.tailwindcss.com/3.4.1/tailwind.js"></script>
    <!-- Inter Font --><link href="https://fonts.googleapis.com/css2?family=Inter:wght400;600;700;900&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary-color: #3b82f6; /* Blue 500 */
        }
        body {
            /* Background Gradient: Dark Blue/Gray */
            background: linear-gradient(135deg, #2b3a4a 0%, #1a202c 100%); 
            background-attachment: fixed;
            color: #e2e8f0; 
            font-family: 'Inter', sans-serif;
        }
        .glass-container {
            /* Glassmorphism Effect */
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
        .report-card {
            transition: transform 0.3s ease;
        }
        .report-card:hover {
            transform: translateY(-4px);
            box-shadow: 0 8px 18px rgba(0, 0, 0, 0.5); /* Stronger hover shadow */
        }
        @keyframes flip-in {
            /* Animation for timer segments */
            0% { transform: perspective(400px) rotateX(-90deg); opacity: 0; }
            50% { transform: perspective(400px) rotateX(0deg); opacity: 1; }
            100% { transform: perspective(400px) rotateX(0deg); opacity: 1; }
        }
        .shutter-animation {
            animation: flip-in 0.4s ease-out; 
            display: inline-block;
        }

        /* Responsive Adjustments */
        @media (max-width: 640px) {
            #liveClock {
                top: 0.5rem;
                right: 0.5rem;
                padding: 0.5rem 0.75rem;
                font-size: 0.875rem;
            }
            .timer-segment {
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

    <!-- Main Container: max-w-2xl for better mobile and desktop view -->
    <div class="w-full max-w-2xl glass-container rounded-xl p-6 md:p-10">

        <h1 class="text-3xl font-bold text-center text-white mb-2">স্টাডি ট্র্যাকার ও টাইমার</h1>
        
        <!-- Motivation Message Display -->
        <p id="motivationMessage" class="text-center text-amber-300 font-semibold mb-6 h-6">
            পড়ার সেশন শুরু করতে একটি কোর্স নির্বাচন করুন।
        </p>
        
        <!-- --- TIMER VIEW --- -->
        <div id="timerView">
            
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
                    
                    <!-- Hour Segment -->
                    <div class="timer-segment bg-indigo-700 p-3 md:p-4 rounded-xl shadow-2xl min-w-[110px] md:min-w-[150px]">
                        <span id="timerHours" class="timer-display text-6xl md:text-8xl">00</span>
                        <p class="text-xs md:text-sm text-gray-300 font-medium mt-1">ঘন্টা</p>
                    </div>
                    <!-- Separator -->
                    <span class="timer-separator text-5xl md:text-8xl font-thin text-gray-400">:</span>
                    
                    <!-- Minute Segment -->
                    <div class="timer-segment bg-gray-700 p-3 md:p-4 rounded-xl shadow-2xl min-w-[110px] md:min-w-[150px]">
                        <span id="timerMinutes" class="timer-display text-6xl md:text-8xl">00</span>
                        <p class="text-xs md:text-sm text-gray-300 font-medium mt-1">মিনিট</p>
                    </div>
                    <!-- Separator -->
                    <span class="timer-separator text-5xl md:text-8xl font-thin text-gray-400">:</span>

                    <!-- Second Segment -->
                    <div class="timer-segment bg-gray-700 p-3 md:p-4 rounded-xl shadow-2xl min-w-[110px] md:min-w-[150px]">
                        <span id="timerSeconds" class="timer-display text-6xl md:text-8xl">00</span>
                        <p class="text-xs md:text-sm text-gray-300 font-medium mt-1">সেকেন্ড</p>
                    </div>
                </div>
                <!-- Status Message Area --><p id="statusMessage" class="text-red-400 font-semibold mt-4 h-6"></p>
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
            
            <!-- --- REPORT SECTION (Integrated) --- -->
            <div id="reportSection" class="pt-2 border-t border-gray-700 mt-8">
                <div class="bg-indigo-900 text-white p-3 rounded-lg mb-4">
                    <p class="text-sm font-semibold">✅ এই অ্যাপটি ফায়ারবেস ব্যবহার করে আপনার অধ্যয়নের সময় পার্মানেন্টলি সেভ করছে।</p>
                    <p class="text-xs text-yellow-300 mt-1">
                        ⚠️ **ঐতিহাসিক রিপোর্ট লোডিং টাইম কমাতে শুধুমাত্র সর্বশেষ ১০০টি সেশন দেখানো হচ্ছে।**
                    </p>
                </div>
                <h2 class="text-2xl font-bold text-white mb-6 text-center">মোট অধ্যয়নের রিপোর্ট (সর্বশেষ ১০০ সেশন)</h2>
                <div id="reportContainer" class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <!-- Data will be populated by JS listener -->
                    <div class="text-center text-gray-400 col-span-full">ডেটা লোড হচ্ছে...</div>
                </div>
            </div>
            <!-- --- END REPORT SECTION --- -->

            <!-- --- DATA MANAGEMENT SECTION --- -->
            <div id="dataManagementSection" class="pt-6 border-t border-gray-700 mt-8">
                <h2 class="text-xl font-bold text-white mb-4 text-center">ডেটা ম্যানেজমেন্ট</h2>
                <div class="flex flex-col items-center space-y-4">
                    <select id="courseResetSelect" class="w-full max-w-sm p-3 border-2 border-gray-600 rounded-lg text-lg font-medium shadow-sm bg-gray-800 text-white bg-opacity-80 focus:border-red-400 focus:ring focus:ring-red-500 focus:ring-opacity-50 transition duration-150 ease-in-out">
                        <option value="" disabled selected>--- যে কোর্সের ডেটা মুছবেন ---</option>
                    </select>
                    <button id="deleteDataButton" class="btn-primary bg-red-700 hover:bg-red-800 text-white font-bold py-3 px-6 rounded-lg shadow-md transition duration-200 w-full max-w-sm" disabled>
                        🗑️ নির্বাচিত কোর্সের সম্পূর্ণ ডেটা মুছুন
                    </button>
                </div>
            </div>
            <!-- --- END DATA MANAGEMENT SECTION --- -->
        </div>
        <!-- --- END TIMER VIEW --- -->
            
        <div class="mt-8 text-sm text-gray-400 text-center">
            ইউজার আইডি: <span id="userIdDisplay" class="font-mono text-xs bg-gray-800 p-1 rounded">লোডিং...</span>
        </div>
    </div>

    <!-- Confirmation Modal Structure (Hidden by default) -->
    <div id="deleteConfirmationModal" class="fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center z-[100] hidden">
        <div class="bg-gray-800 p-6 rounded-xl shadow-2xl max-w-sm w-full border border-red-500">
            <h3 class="text-xl font-bold text-red-400 mb-3">সতর্কতা! ডেটা মুছে ফেলা হচ্ছে</h3>
            <p class="text-gray-300 mb-4">আপনি কি নিশ্চিত যে আপনি কোর্সের জন্য সমস্ত অধ্যয়নের সময় (লাইফটাইম ডেটা) মুছে ফেলতে চান: <span id="courseToDeleteName" class="font-bold text-white"></span>?</p>
            <p class="text-sm text-yellow-400 mb-6">এই পদক্ষেপটি আর ফিরিয়ে আনা যাবে না।</p>
            <div class="flex justify-end space-x-3">
                <button id="cancelDeleteButton" class="bg-gray-600 hover:bg-gray-700 text-white py-2 px-4 rounded-lg font-semibold transition">বাতিল</button>
                <button id="confirmDeleteButton" class="bg-red-600 hover:bg-red-700 text-white py-2 px-4 rounded-lg font-semibold transition">হ্যাঁ, মুছুন</button>
            </div>
        </div>
    </div>


    <!-- Firebase SDK Imports -->
    <script type="module">
        // Added required imports for querying and batch deleting
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { 
            getFirestore, collection, addDoc, onSnapshot, setLogLevel, 
            query, where, getDocs, writeBatch, doc, orderBy, limit 
        } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
        
        // --- Global Firebase and Application State Variables ---
        let db;
        let auth;
        let userId = 'loading';
        const COLLECTION_NAME = 'study_sessions';

        // Global environment variables (MANDATORY USE)
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : null;
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;


        // Core Timer/Data State
        let timerInterval = null;
        let motivationInterval = null; 
        let startTime = 0;
        let elapsedTime = 0;
        let isRunning = false;
        
        // studyTotals will now be populated by the limited onSnapshot listener
        let studyTotals = {}; 
        
        // Course data
        const courses = [
            { id: 'IS', name: 'Information Systems (IS)' },
            { id: 'TQM', name: 'Total Quality Management (TQM)' },
            { id: 'PYTHON', name: 'Programming (PYTHON)' },
            { id: 'STAT', name: 'Statistics (STAT)' },
            { id: 'HRM', name: 'Human Resource Management (HRM)' },
            { id: 'SBE', name: 'Small Business Entrepreneurship (SBE)' },
            { id: 'ACCOUNTING', name: 'Accounting (হিসাববিজ্ঞান)' },
            { id: 'MBAPREP', name: 'MBA Preparation (এমবিএ প্রস্তুতি)' }
        ];

        // --- Dynamic Color Palette for Courses (Deep Colors) ---
        const courseColors = [
            'blue',     // IS
            'emerald',  // TQM
            'rose',     // PYTHON
            'cyan',     // STAT
            'violet',   // HRM
            'orange',   // SBE
            'teal',     // ACCOUNTING
            'red'       // MBAPREP
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
        const motivationMessage = document.getElementById('motivationMessage'); 
        const liveClockDisplay = document.getElementById('liveClock'); 
        const reportContainer = document.getElementById('reportContainer'); 
        
        // NEW ELEMENTS for Course Reset
        const courseResetSelect = document.getElementById('courseResetSelect');
        const deleteDataButton = document.getElementById('deleteDataButton');
        const deleteConfirmationModal = document.getElementById('deleteConfirmationModal');
        const courseToDeleteName = document.getElementById('courseToDeleteName');
        const cancelDeleteButton = document.getElementById('cancelDeleteButton');
        const confirmDeleteButton = document.getElementById('confirmDeleteButton');

        // TIMER SEGMENTS
        const timerHours = document.getElementById('timerHours');
        const timerMinutes = document.getElementById('timerMinutes');
        const timerSeconds = document.getElementById('timerSeconds');

        let courseIdToDelete = null; // State to hold the ID of the course to be deleted


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
        
        /**
         * Firestore Path Resolver: artifacts/{appId}/users/{userId}/study_sessions
         */
        const getCollectionRef = (dbInstance, uid) => {
            return collection(dbInstance, `artifacts/${appId}/users/${uid}/${COLLECTION_NAME}`);
        };


        /**
         * Function to listen for data (Re-enabled with a LIMIT for speed)
         */
        const setupFirestoreListener = (dbInstance, uid) => {
            const sessionsRef = getCollectionRef(dbInstance, uid);
            
            // Query to get the 100 most recent sessions, ordered by timestamp for fast loading
            const q = query(sessionsRef, orderBy('timestamp', 'desc'), limit(100));

            // Listen for real-time updates
            onSnapshot(q, (snapshot) => {
                let newStudyTotals = {};
                let grandTotalMs = 0;

                snapshot.docs.forEach(doc => {
                    const data = doc.data();
                    const courseId = data.courseId;
                    const durationMs = data.durationMs;

                    if (courseId && durationMs > 0) {
                        if (!newStudyTotals[courseId]) {
                            newStudyTotals[courseId] = 0;
                        }
                        newStudyTotals[courseId] += durationMs;
                        grandTotalMs += durationMs;
                    }
                });

                studyTotals = newStudyTotals;
                renderReport(grandTotalMs);
            }, (error) => {
                console.error("Error setting up Firestore listener:", error);
                statusMessage.textContent = "⚠️ ডেটা লোড করতে ব্যর্থ (সংযোগ ত্রুটি)।";
                statusMessage.classList.remove('text-green-400', 'text-gray-400');
                statusMessage.classList.add('text-red-400');
            });
        };


        /**
         * Core function to delete all sessions for a specific course using a batch write.
         * Note: This deletes ALL sessions, not just the 100 that are displayed.
         * @param {string} courseId The ID of the course whose data should be deleted.
         */
        const deleteCourseData = async (courseId) => {
            if (!db || userId === 'loading' || !courseId) return;

            const sessionsRef = getCollectionRef(db, userId);
            // Query for all documents belonging to the specific course ID
            const q = query(sessionsRef, where('courseId', '==', courseId));

            try {
                const snapshot = await getDocs(q);
                if (snapshot.empty) {
                    statusMessage.textContent = `❌ ${courseId}-এর কোনো ডেটা পাওয়া যায়নি।`;
                    statusMessage.classList.remove('text-green-400', 'text-yellow-400');
                    statusMessage.classList.add('text-red-400');
                    return;
                }

                // Create a batch operation to efficiently delete all found documents
                const batch = writeBatch(db);
                snapshot.docs.forEach((d) => {
                    batch.delete(d.ref);
                });

                await batch.commit();

                const courseName = courses.find(c => c.id === courseId)?.name || courseId;
                statusMessage.textContent = `🗑️ সফলভাবে ${courseName}-এর মোট ${snapshot.size} টি সেশন ডেটা মুছে ফেলা হয়েছে!`;
                statusMessage.classList.remove('text-red-400', 'text-yellow-400');
                statusMessage.classList.add('text-green-400');

                // Reset the select field after successful deletion
                courseResetSelect.value = "";
                deleteDataButton.disabled = true;

            } catch (error) {
                console.error("Error deleting course data:", error);
                statusMessage.textContent = `⚠️ ডেটা মোছার সময় ত্রুটি: ${error.message.substring(0, 50)}...`;
                statusMessage.classList.remove('text-green-400', 'text-yellow-400');
                statusMessage.classList.add('text-red-400');
            }
        };


        // --- Report Rendering Function (Displays data based on the limited studyTotals) ---
        const renderReport = (grandTotalMs = 0) => {
            reportContainer.innerHTML = '';
            
            const totalTimeFormatted = formatTime(grandTotalMs);

            // Grand Total Card (Now dynamic)
            const grandTotalCard = `
                <div class="report-card p-5 rounded-xl shadow-[0_0_20px_rgba(59,130,246,0.3)] bg-indigo-800 col-span-full mb-6">
                    <p class="text-sm font-medium text-indigo-300">মোট অধ্যয়নের সময় (সর্বশেষ ${Object.keys(studyTotals).length}টি কোর্সে)</p>
                    <p class="text-4xl font-extrabold text-white mt-1">${totalTimeFormatted}</p>
                </div>
            `;
            reportContainer.innerHTML += grandTotalCard;

            // Individual Course Reports
            const courseIds = Object.keys(studyTotals).sort((a, b) => studyTotals[b] - studyTotals[a]);

            if (courseIds.length === 0 && grandTotalMs === 0) {
                reportContainer.innerHTML += `
                    <div class="text-center text-gray-400 col-span-full mt-4">
                        কোনো অধ্যয়নের ডেটা নেই। (সর্বশেষ ১০০ সেশনের মধ্যে)
                    </div>
                `;
                return;
            }
            
            // Loop through courses and render cards
            courseIds.forEach(courseId => {
                const durationMs = studyTotals[courseId];
                const formattedDuration = formatTime(durationMs);
                const courseObj = courses.find(c => c.id === courseId);
                const courseName = courseObj ? courseObj.name : courseId;
                
                // Find the color index
                const courseIndex = courses.findIndex(c => c.id === courseId);
                const colorIndex = courseIndex !== -1 ? courseIndex % courseColors.length : 0;
                const color = courseColors[colorIndex];
                
                reportContainer.innerHTML += `
                    <div class="report-card p-4 rounded-xl shadow-lg bg-${color}-700 border border-${color}-600">
                        <p class="text-sm font-medium text-${color}-200">${courseName}</p>
                        <p class="text-2xl font-bold text-white mt-1">${formattedDuration}</p>
                    </div>
                `;
            });
        };


        // --- Motivation Functions ---
        
        const displayMotivation = () => {
            const randomIndex = Math.floor(Math.random() * motivationMessages.length);
            motivationMessage.textContent = motivationMessages[randomIndex];
        };

        const clearMotivation = () => {
            if (motivationInterval) {
                clearInterval(motivationInterval);
                motivationInterval = null;
            }
            // Reset to the default prompt message
            motivationMessage.textContent = "পড়ার সেশন শুরু করতে একটি কোর্স নির্বাচন করুন।"; 
        };


        // --- Timer Functions ---
        
        const updateSegmentWithAnimation = (element, newValue) => {
            const oldValue = element.textContent;
            if (oldValue !== newValue) {
                element.classList.add('shutter-animation');
                element.textContent = newValue;
                setTimeout(() => {
                    element.classList.remove('shutter-animation');
                }, 400); 
            }
        };


        const updateTimer = () => {
            if (isRunning) {
                elapsedTime = Date.now() - startTime;
                const { hours, minutes, seconds } = getFormattedTimeParts(elapsedTime);

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
                statusMessage.classList.remove('text-green-400', 'text-gray-400', 'text-yellow-400');
                statusMessage.classList.add('text-red-400');
                return;
            }
            // Check only if the authentication process has completed
            if (userId === 'loading') {
                statusMessage.textContent = "ডেটাবেস সংযোগের জন্য অপেক্ষা করুন...";
                statusMessage.classList.remove('text-red-400', 'text-green-400');
                statusMessage.classList.add('text-yellow-400');
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

                // Start motivation message rotation
                displayMotivation(); 
                if (motivationInterval) clearInterval(motivationInterval);
                motivationInterval = setInterval(displayMotivation, 60000); 
            }
        };

        const pauseTimer = async () => {
            if (isRunning) {
                clearInterval(timerInterval);
                isRunning = false;
                
                clearMotivation(); 

                const selectedCourse = courseSelect.value;
                const durationMs = elapsedTime;
                
                // --- FIREBASE DATA SAVE LOGIC ---
                if (durationMs > 1000) { 
                    if (db && userId && userId !== 'loading') {
                        const sessionsRef = getCollectionRef(db, userId);
                        try {
                            // Exponential Backoff implementation
                            const MAX_RETRIES = 3;
                            let retries = 0;
                            let saved = false;

                            while (retries < MAX_RETRIES && !saved) {
                                try {
                                    await addDoc(sessionsRef, {
                                        courseId: selectedCourse,
                                        durationMs: durationMs,
                                        timestamp: new Date().toISOString(),
                                        userId: userId,
                                    });
                                    saved = true;
                                    break;
                                } catch (e) {
                                    retries++;
                                    if (retries < MAX_RETRIES) {
                                        const delay = Math.pow(2, retries) * 1000;
                                        await new Promise(resolve => setTimeout(resolve, delay));
                                    } else {
                                        throw e; // Re-throw if max retries reached
                                    }
                                }
                            }

                            if (saved) {
                                statusMessage.textContent = `✅ ${selectedCourse}-এর জন্য সেশন সম্পন্ন: ${formatTime(durationMs)} সময় পার্মানেন্টলি সেভ করা হলো।`;
                                statusMessage.classList.remove('text-red-400', 'text-gray-400', 'text-yellow-400');
                                statusMessage.classList.add('text-green-400');
                            }
                            
                        } catch (e) {
                            console.error("Error adding document to Firestore (after retries): ", e);
                            statusMessage.textContent = `⚠️ ডেটা সেভ করা যায়নি। এরর: ${e.message.substring(0, 50)}...`;
                            statusMessage.classList.remove('text-green-400', 'text-yellow-400');
                            statusMessage.classList.add('text-red-400');
                        }
                    } else {
                        statusMessage.textContent = `⚠️ Firebase সংযোগ নেই, সময় সেভ হয়নি।`;
                        statusMessage.classList.remove('text-green-400', 'text-yellow-400');
                        statusMessage.classList.add('text-red-400');
                    }
                } else {
                     statusMessage.textContent = `সেশনটি খুব ছোট ছিল, কোনো সময় সেভ হয়নি।`;
                     statusMessage.classList.remove('text-green-400', 'text-gray-400', 'text-yellow-400');
                     statusMessage.classList.add('text-red-400');
                }
                // --- END FIREBASE DATA SAVE LOGIC ---


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
                
                // onSnapshot listener handles report update automatically now.
            }
        };

        const resetTimer = () => {
            if (isRunning) {
                clearInterval(timerInterval);
                isRunning = false;
            }
            clearMotivation(); 

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
            statusMessage.classList.remove('text-green-400', 'text-red-400', 'text-yellow-400');
            statusMessage.classList.add('text-gray-400');
        };


        // --- Initialization ---

        async function initFirebaseAndAuth() {
            if (!firebaseConfig) {
                console.error("Firebase config is missing.");
                document.getElementById('userIdDisplay').textContent = 'ত্রুটি: কনফিগ নেই';
                return;
            }

            const app = initializeApp(firebaseConfig);
            db = getFirestore(app);
            auth = getAuth(app);
            setLogLevel('Debug'); // Enable logging for debugging

            try {
                if (initialAuthToken) {
                    await signInWithCustomToken(auth, initialAuthToken);
                } else {
                    await signInAnonymously(auth);
                }
            } catch (error) {
                console.error("Authentication failed:", error);
                document.getElementById('userIdDisplay').textContent = 'অথেন্টিকেশন ব্যর্থ';
                return;
            }

            // Auth state listener: Once authenticated, set user ID and update buttons
            onAuthStateChanged(auth, (user) => {
                if (user) {
                    userId = user.uid;
                    document.getElementById('userIdDisplay').textContent = userId;
                    // Re-enabling the listener with a LIMIT for fast loading.
                    setupFirestoreListener(db, userId); 
                    updateControlButtons(); // Update buttons when DB is ready
                } else {
                    userId = 'guest'; 
                    document.getElementById('userIdDisplay').textContent = userId;
                    console.warn("User is not authenticated (signed in anonymously but no UID found).");
                }
            });
        }

        const initApp = () => {
            // 1. Initialize Firebase/Auth (Async)
            initFirebaseAndAuth();

            // 2. Initialize App Content (Sync)
            populateCourseSelectors(); 
            setupEventListeners();
            updateControlButtons();
            
            // Start the live clock update
            updateLiveClock(); 
            setInterval(updateLiveClock, 1000);
            
            // Initial render placeholder
            renderReport(0); 
        };

        // Function to populate BOTH course selectors
        const populateCourseSelectors = () => {
            const selectors = [courseSelect, courseResetSelect];
            
            selectors.forEach(selector => {
                // Clear existing options (except the disabled one)
                selector.querySelectorAll('option:not([disabled])').forEach(option => option.remove());
                
                courses.forEach(course => {
                    const option = document.createElement('option');
                    option.value = course.id;
                    option.textContent = course.name;
                    selector.appendChild(option);
                });
            });
        };

        const updateControlButtons = () => {
            const isCourseSelected = !!courseSelect.value;
            // Check if DB and user ID are ready (auth completed) before enabling the start button
            const isDbReady = userId !== 'loading' && !!db; 
            
            startButton.disabled = !isDbReady || !isCourseSelected || isRunning;
            pauseButton.disabled = !isRunning;
            resetButton.disabled = !isRunning;

            // Data Management Button
            const isResetCourseSelected = !!courseResetSelect.value;
            deleteDataButton.disabled = !isDbReady || !isResetCourseSelected;
        }

        const setupEventListeners = () => {
            // Timer controls
            startButton.addEventListener('click', startTimer);
            pauseButton.addEventListener('click', pauseTimer);
            resetButton.addEventListener('click', resetTimer);
            
            // Update buttons on course selection change
            courseSelect.addEventListener('change', updateControlButtons);

            // --- Course Reset/Delete Logic ---
            courseResetSelect.addEventListener('change', updateControlButtons);

            deleteDataButton.addEventListener('click', () => {
                courseIdToDelete = courseResetSelect.value;
                if (!courseIdToDelete) return;

                const courseObj = courses.find(c => c.id === courseIdToDelete);
                if (courseObj) {
                    // Set the course name in the modal text
                    courseToDeleteName.textContent = courseObj.name;
                    // Show the modal
                    deleteConfirmationModal.classList.remove('hidden');
                }
            });

            cancelDeleteButton.addEventListener('click', () => {
                // Hide the modal and clear the state
                deleteConfirmationModal.classList.add('hidden');
                courseIdToDelete = null;
            });

            confirmDeleteButton.addEventListener('click', async () => {
                // Hide the modal immediately
                deleteConfirmationModal.classList.add('hidden');
                
                if (courseIdToDelete) {
                    await deleteCourseData(courseIdToDelete);
                }
                courseIdToDelete = null;
                // onSnapshot listener will update the report after deletion
            });
            // --- End Course Reset/Delete Logic ---

        };

        // Start the application after all functions are defined
        window.onload = initApp;
    </script>
</body>
</html>



















