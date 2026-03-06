<script>
    import { onMount, onDestroy } from "svelte";
    import { windowStore } from "../../lib/stores/windowStore";

    const base = import.meta.env.BASE_URL;

    // ═══════════════════════════════════════════════════════════════════════
    // 🎯 DATA-DRIVEN GUIDE SYSTEM
    // This system provides contextual guidance for navigating the portfolio
    // ═══════════════════════════════════════════════════════════════════════

    // Expression mappings - each emotion has a corresponding memoji
    const expressions = {
        default: "happy.png",
        waving: "hugging.png",
        left: "winking.png",
        right: "grinning.png",
        top: "star_eye.png",
        bottom: "thinking.png",
        hover: "lovely.png",
        victory: "victory.png",
        happy: "happy_winking.png",
        angry: "angry.png",
        badWord: "bad_word.png",
        mindblown: "mind_blowing.png",
        excited: "star_eye.png",
        curious: "thinking.png",
        proud: "victory.png",
        friendly: "grinning.png",
        love: "lovely.png",
    };

    // 🎭 GUIDE MESSAGE DATABASE
    // Each category contains messages with matching expressions
    const guideData = {
        // Initial welcome messages when user lands on site
        welcome: [
            { text: "Hey there! I'm Shyam! 👋", expression: "waving" },
            { text: "Welcome to my portfolio! ✨", expression: "excited" },
            { text: "I'll be your guide today!", expression: "happy" },
            { text: "Ready to explore? Let's go!", expression: "waving" },
        ],

        // General idle messages to keep engagement
        idle: [
            {
                text: "Click on the dock below to start! 👇",
                expression: "friendly",
            },
            {
                text: "Try the colorful icons at the bottom!",
                expression: "waving",
            },
            {
                text: "Each icon opens something cool! ✨",
                expression: "excited",
            },
            { text: "Don't be shy, explore around!", expression: "friendly" },
            { text: "Hover over me for a surprise! 😊", expression: "love" },
            { text: "You can drag me around too!", expression: "happy" },
        ],

        // Context-specific guidance for each app/section
        appGuide: {
            about: {
                hint: {
                    text: "Tap 'About Me' to learn who I am! 👤",
                    expression: "curious",
                },
                active: {
                    text: "Here's my story! Scroll to discover more 📖",
                    expression: "proud",
                },
                explore: [
                    {
                        text: "This is where you get to know me!",
                        expression: "friendly",
                    },
                    {
                        text: "Scroll down for the full journey! ⬇️",
                        expression: "excited",
                    },
                    {
                        text: "Fun facts about me inside! 🎯",
                        expression: "waving",
                    },
                ],
            },
            experience: {
                hint: {
                    text: "Select 'Journey' to see my path! 🗺️",
                    expression: "curious",
                },
                active: {
                    text: "My professional journey awaits! 🚀",
                    expression: "proud",
                },
                explore: [
                    {
                        text: "Every step shaped who I am! 🌟",
                        expression: "excited",
                    },
                    {
                        text: "Check out my timeline below! ⏳",
                        expression: "friendly",
                    },
                    { text: "Experience that matters! �", expression: "proud" },
                ],
            },
            portfolio: {
                hint: {
                    text: "Open 'Work' to see my projects! 💼",
                    expression: "excited",
                },
                active: {
                    text: "Here's what I've built! Take a look! 👀",
                    expression: "proud",
                },
                explore: [
                    {
                        text: "Each project tells a story! 📚",
                        expression: "friendly",
                    },
                    {
                        text: "Click any project for details! 🔍",
                        expression: "curious",
                    },
                    {
                        text: "Design + Code = Magic! ✨",
                        expression: "excited",
                    },
                ],
            },
            contact: {
                hint: {
                    text: "Click 'Contact' to message Shyam! 💬",
                    expression: "love",
                },
                active: {
                    text: "Let's connect! I'd love to hear from you! 💌",
                    expression: "love",
                },
                explore: [
                    {
                        text: "Don't be a stranger, say hi! 👋",
                        expression: "waving",
                    },
                    {
                        text: "Email, LinkedIn, or just a hello! 📧",
                        expression: "friendly",
                    },
                    {
                        text: "I reply to every message! 💬",
                        expression: "happy",
                    },
                ],
            },
            resume: {
                hint: {
                    text: "View 'Resume' for my credentials! 📄",
                    expression: "proud",
                },
                active: {
                    text: "My qualifications at a glance! 🎓",
                    expression: "proud",
                },
                explore: [
                    {
                        text: "Download my resume anytime! 📥",
                        expression: "friendly",
                    },
                    {
                        text: "Skills, education & more inside! 📋",
                        expression: "curious",
                    },
                    { text: "The professional me! 👨‍💼", expression: "proud" },
                ],
            },
            welcome: {
                hint: {
                    text: "Click 'Welcome' for the intro! 🏠",
                    expression: "waving",
                },
                active: {
                    text: "The grand welcome experience! 🎉",
                    expression: "excited",
                },
                explore: [
                    {
                        text: "This is where it all begins! ✨",
                        expression: "excited",
                    },
                    {
                        text: "Get the full intro here! 🌟",
                        expression: "waving",
                    },
                ],
            },
        },

        // Step-by-step onboarding for first-time visitors
        onboarding: [
            {
                text: "Welcome! I'm Shyam Prasadh! 👋",
                expression: "waving",
                step: 1,
            },
            {
                text: "I'm a UX Designer & Developer 🎨",
                expression: "proud",
                step: 2,
            },
            {
                text: "See those icons at the bottom?",
                expression: "curious",
                step: 3,
            },
            {
                text: "That's the dock! Click one to start!",
                expression: "excited",
                step: 4,
            },
            {
                text: "Try 'About Me' first! 👤",
                expression: "friendly",
                step: 5,
            },
        ],

        // Reactions when user interacts
        interactions: {
            hover: { text: "Hey you! 💖", expression: "love" },
            click: { text: "Yay! You clicked me! 🎉", expression: "victory" },
            drag: { text: "Wheee! This is fun! 🎢", expression: "excited" },
            release: { text: "That was a ride! 😄", expression: "happy" },
        },

        // Achievement/milestone messages
        achievements: {
            firstApp: {
                text: "Great! You opened your first app! 🎉",
                expression: "victory",
            },
            explorer: {
                text: "You're a natural explorer! 🗺️",
                expression: "excited",
            },
            completedTour: {
                text: "You've seen it all! Thank you! 🙏",
                expression: "love",
            },
        },

        // Contextual tips based on time spent
        tips: [
            {
                text: "Pro tip: All windows are draggable! 🖱️",
                expression: "curious",
            },
            {
                text: "Try right-clicking the desktop! 🖱️",
                expression: "curious",
            },
            {
                text: "Did you notice the control center? ⚙️",
                expression: "curious",
            },
            {
                text: "The menu bar has cool stuff too! ☝️",
                expression: "friendly",
            },
        ],
    };

    // Current guide state
    let currentGuidePhase = "welcome"; // 'welcome', 'onboarding', 'idle', 'appContext'
    let onboardingStep = 0;
    let visitedApps = new Set();
    let lastActiveApp = null;

    // Helper to get a random item from an array
    function getRandomItem(arr) {
        return arr[Math.floor(Math.random() * arr.length)];
    }

    // Get contextual message based on current state
    function getContextualMessage() {
        // Check if there's an active window
        const activeWindow = currentWindows.find((w) => !w.minimized);

        if (activeWindow && guideData.appGuide[activeWindow.id]) {
            const appGuide = guideData.appGuide[activeWindow.id];

            // If this is a new app, show the active message
            if (lastActiveApp !== activeWindow.id) {
                lastActiveApp = activeWindow.id;
                visitedApps.add(activeWindow.id);
                return appGuide.active;
            }

            // Otherwise, show exploration tips
            return getRandomItem(appGuide.explore);
        }

        // No active window - guide them to start
        if (
            visitedApps.size === 0 &&
            onboardingStep < guideData.onboarding.length
        ) {
            return guideData.onboarding[onboardingStep];
        }

        // Show tips occasionally
        if (Math.random() < 0.3 && visitedApps.size > 0) {
            return getRandomItem(guideData.tips);
        }

        // Default to idle messages
        return getRandomItem(guideData.idle);
    }

    // Legacy compatibility - greetings and messages arrays
    const greetings = guideData.welcome.map((g) => g.text);
    const messages = guideData.idle.map((m) => m.text);

    let currentExpression = expressions.waving;
    let currentMessage = greetings[0];
    let messageIndex = 0;
    let showBubble = true;
    let isWaving = true;

    // Emotional Escalation State
    let emotionalState = "normal"; // 'normal', 'annoyed', 'angry', 'climax'
    let dragStartTime = 0;
    let dragDuration = 0;
    let tintIntensity = 0; // 0 to 1
    let isRecovering = false;
    let showFeelingBetter = false;
    let isMindBlown = false;

    // Emoji Effects
    let popEmojis = []; // { id, angle, distance, scale, opacity }
    let orbitAngle = 0;
    let showOrbit = false;

    let angryTimeout;
    let recoveryInterval;
    let orbitZIndex = 30;

    // Position state
    let posX = 85; // Start on right side (percentage)
    let posY = 40;
    let targetX = 85;
    let targetY = 40;

    // Track if windows are open
    let hasActiveWindows = false;
    let currentWindows = [];
    let unsubscribe;

    // Cursor tracking
    let tiltX = 0;
    let tiltY = 0;

    // Animation & Timeout tracking
    let animationFrame;
    let waveInterval;
    let messageInterval;
    let moveInterval;
    let interactionTimeouts = [];

    function clearInteractionTimeouts() {
        interactionTimeouts.forEach((t) => clearTimeout(t));
        interactionTimeouts = [];
    }

    function addTimeout(timer, duration) {
        const id = setTimeout(timer, duration);
        interactionTimeouts.push(id);
        return id;
    }

    // Element reference
    let widgetElement;
    let isHovering = false;
    let isDragging = false;
    let mouseX = 0;
    let mouseY = 0;
    let isFleeing = false;

    // Check if on mobile
    function isMobile() {
        return window.innerWidth <= 768;
    }

    function snapToSide() {
        if (isMobile() || isDragging) return;

        if (hasActiveWindows && currentWindows.length > 0) {
            // Find the first visible window to avoid
            const visibleWindows = currentWindows.filter((w) => !w.minimized);
            const windowData = visibleWindows[0];

            if (windowData) {
                const windowCenterX =
                    windowData.x + (windowData.width || 800) / 2;
                const screenCenter = window.innerWidth / 2;

                // Go to opposite side of the window
                if (windowCenterX < screenCenter) {
                    targetX = 88;
                    targetY = 35;
                } else {
                    targetX = 12;
                    targetY = 35;
                }
            }
        } else {
            // No windows - if we were dragged significantly away from sides, return to a random home
            if (posX > 25 && posX < 75) {
                pickNewTarget();
            }
        }
    }

    // Subscribe to window store to know when windows are open
    function checkWindows(state) {
        // On mobile, CSS handles positioning
        if (isMobile()) return;

        const prevWindows = [...currentWindows];
        currentWindows = state.windows;
        const visibleWindows = currentWindows.filter((w) => !w.minimized);
        hasActiveWindows = visibleWindows.length > 0;

        // Check if a new window was opened
        const newWindow = visibleWindows.find(
            (w) => !prevWindows.some((p) => p.id === w.id && !p.minimized),
        );

        if (newWindow && guideData.appGuide[newWindow.id]) {
            // Show contextual guide for the newly opened app
            const appGuide = guideData.appGuide[newWindow.id];

            // Track first app achievement
            if (visitedApps.size === 0) {
                // First app opened - show achievement after brief delay
                addTimeout(() => {
                    const achievement = guideData.achievements.firstApp;
                    currentMessage = achievement.text;
                    currentExpression = expressions[achievement.expression];
                    showBubble = true;
                }, 2500);
            }

            // Show the welcome message for this app
            lastActiveApp = newWindow.id;
            visitedApps.add(newWindow.id);
            currentMessage = appGuide.active.text;
            currentExpression = expressions[appGuide.active.expression];
            showBubble = true;
            isWaving = false;

            // Check if user has explored most apps
            if (
                visitedApps.size ===
                Object.keys(guideData.appGuide).length - 1
            ) {
                addTimeout(() => {
                    const explorer = guideData.achievements.explorer;
                    currentMessage = explorer.text;
                    currentExpression = expressions[explorer.expression];
                    showBubble = true;
                }, 3000);
            }
        }

        if (!isDragging) {
            snapToSide();
        }
    }

    function handleMouseMove(e) {
        // Block ALL mouse interactions during drag, recovery, or any emotional state
        if (
            !widgetElement ||
            isDragging ||
            isRecovering ||
            emotionalState !== "normal" ||
            showFeelingBetter
        )
            return;

        const clientX = e.type.includes("touch")
            ? e.touches[0].clientX
            : e.clientX;
        const clientY = e.type.includes("touch")
            ? e.touches[0].clientY
            : e.clientY;

        const rect = widgetElement.getBoundingClientRect();
        const centerX = rect.left + rect.width / 2;
        const centerY = rect.top + rect.height / 2;

        const deltaX = clientX - centerX;
        const deltaY = clientY - centerY;

        mouseX = (clientX / window.innerWidth) * 100;
        mouseY = (clientY / window.innerHeight) * 100;

        // Tilt effect (max 20 degrees)
        const maxTilt = 20;
        const maxDistance = isMobile() ? 200 : 500;

        tiltY = Math.min(
            Math.max((deltaX / maxDistance) * maxTilt, -maxTilt),
            maxTilt,
        );
        tiltX = Math.min(
            Math.max(-(deltaY / maxDistance) * maxTilt, -maxTilt),
            maxTilt,
        );

        // Change expression based on cursor direction (when not waving)
        if (!isHovering && !isWaving && !isFleeing) {
            const angle = Math.atan2(deltaY, deltaX) * (180 / Math.PI);
            if (angle > -45 && angle <= 45) {
                currentExpression = expressions.right;
            } else if (angle > 45 && angle <= 135) {
                currentExpression = expressions.bottom;
            } else if (angle > -135 && angle <= -45) {
                currentExpression = expressions.top;
            } else {
                currentExpression = expressions.left;
            }
        }
    }

    function handleClick() {
        // Block clicks during drag, recovery, or any emotional state
        if (
            emotionalState !== "normal" ||
            isDragging ||
            isRecovering ||
            showFeelingBetter
        )
            return;

        // Use interaction data for click
        const clickData = guideData.interactions.click;
        currentExpression = expressions[clickData.expression];
        currentMessage = clickData.text;
        showBubble = true;
        isWaving = false;

        addTimeout(() => {
            currentExpression = expressions.default;
            showContextualGuide();
        }, 2000);
    }

    function handleMouseEnter() {
        // Block hover during drag, recovery, or any emotional state
        if (
            emotionalState !== "normal" ||
            isDragging ||
            isRecovering ||
            showFeelingBetter
        )
            return;
        isHovering = true;
        isWaving = false;

        // Use interaction data for hover
        const hoverData = guideData.interactions.hover;
        currentExpression = expressions[hoverData.expression];
        currentMessage = hoverData.text;
        showBubble = true;
    }

    function handleMouseLeave() {
        isHovering = false;
        currentExpression = expressions.default;
        tiltX = 0;
        tiltY = 0;
    }

    // ═══════════════════════════════════════════════════════════════════════
    // 🎯 CONTEXTUAL GUIDE - Shows appropriate message based on current state
    // ═══════════════════════════════════════════════════════════════════════
    function showContextualGuide() {
        const guide = getContextualMessage();
        currentMessage = guide.text;
        currentExpression =
            expressions[guide.expression] || expressions.default;
        showBubble = true;

        // Progress onboarding if applicable
        if (
            visitedApps.size === 0 &&
            onboardingStep < guideData.onboarding.length - 1
        ) {
            onboardingStep++;
        }
    }

    // Legacy function - now calls contextual guide
    function cycleMessage() {
        if (isWaving) {
            // During wave, show welcome messages
            const welcomeMsg = getRandomItem(guideData.welcome);
            currentMessage = welcomeMsg.text;
            currentExpression = expressions[welcomeMsg.expression];
        } else {
            // Show contextual guide based on app state
            showContextualGuide();
        }
    }

    function doWave() {
        // Block wave during drag, recovery, or emotional states
        if (
            emotionalState !== "normal" ||
            isDragging ||
            isRecovering ||
            showFeelingBetter
        )
            return;
        // Wave animation - get random welcome message with matching expression
        isWaving = true;

        const welcomeMsg = getRandomItem(guideData.welcome);
        currentExpression = expressions[welcomeMsg.expression];
        currentMessage = welcomeMsg.text;
        showBubble = true;

        addTimeout(() => {
            if (!isHovering && !isDragging) {
                currentExpression = expressions.happy;
            }
        }, 800);

        addTimeout(() => {
            if (!isHovering && !isDragging) {
                currentExpression = expressions.waving;
            }
        }, 1200);

        addTimeout(() => {
            isWaving = false;
            if (!isHovering && !isDragging) {
                currentExpression = expressions.default;
                // After waving, show contextual guide
                showContextualGuide();
            }
        }, 2500);
    }

    function pickNewTarget() {
        // On mobile, CSS handles positioning
        if (isMobile()) return;
        if (hasActiveWindows) return; // Don't random move when windows open

        // Random position within bounds
        targetX = 15 + Math.random() * 70;
        targetY = 20 + Math.random() * 45;
    }

    function animate() {
        // Smooth movement towards target + Run away logic
        // Block flee behavior during recovery
        if (
            !isDragging &&
            emotionalState === "normal" &&
            !isRecovering &&
            !showFeelingBetter
        ) {
            let moveX = targetX;
            let moveY = targetY;

            // Repelling force from cursor
            const distCursorX = posX - mouseX;
            const distCursorY = posY - mouseY;
            const distCursor = Math.sqrt(
                distCursorX * distCursorX + distCursorY * distCursorY,
            );

            const fleeThreshold = 30; // Larger awareness radius
            const fleeStrength = 6.0; // Explosive push away

            if (distCursor < fleeThreshold && !isHovering) {
                isFleeing = true;
                currentExpression = expressions.top; // Alert face
                // Calculate flee direction
                const angle = Math.atan2(distCursorY, distCursorX);
                const push = (fleeThreshold - distCursor) * fleeStrength;

                // Add repelling force to current target (clamped)
                moveX += Math.cos(angle) * push;
                moveY += Math.sin(angle) * push;
            } else {
                isFleeing = false;
            }

            const dx = moveX - posX;
            const dy = moveY - posY;

            // Movement speed (running speed vs normal floating)
            const speed = isFleeing ? 0.25 : isMobile() ? 0.05 : 0.02;
            posX += dx * speed;
            posY += dy * speed;

            // Clamp position with margin
            const marginX = isMobile() ? 5 : 8;
            const marginY = isMobile() ? 8 : 12;
            posX = Math.max(marginX, Math.min(100 - marginX, posX));
            posY = Math.max(marginY, Math.min(100 - marginY, posY));
        }

        // Update orbit angle and depth
        if (showOrbit) {
            orbitAngle += 0.04;
            // Orbit z-index: front when sin > 0, back when sin < 0
            orbitZIndex = Math.sin(orbitAngle) > 0 ? 30 : 5;
        }

        animationFrame = requestAnimationFrame(animate);
    }

    // Drag functionality
    let dragStartX, dragStartY;

    function startDrag(e) {
        isDragging = true;
        isRecovering = false;
        showFeelingBetter = false;
        const clientX = e.type.includes("touch")
            ? e.touches[0].clientX
            : e.clientX;
        const clientY = e.type.includes("touch")
            ? e.touches[0].clientY
            : e.clientY;
        dragStartX = clientX - (posX / 100) * window.innerWidth;
        dragStartY = clientY - (posY / 100) * window.innerHeight;
        dragStartTime = Date.now();

        if (recoveryInterval) clearInterval(recoveryInterval);
        clearInteractionTimeouts(); // Stop all pending "happy" resets

        // Immediate visual feedback: Remove happy expression instantly
        isWaving = false;
        isHovering = false;
        currentExpression = expressions.top; // Serious/Alert state
        emotionalState = "normal";

        // Hide bubble during drag initially
        showBubble = false;

        window.addEventListener("mousemove", onDrag);
        window.addEventListener("mouseup", stopDrag);
        window.addEventListener("touchmove", onDrag);
        window.addEventListener("touchend", stopDrag);
    }

    function triggerClimaxEffects() {
        // Fill the entire circle with 😡 emojis - EXTREME density for 100% coverage
        const count = 75;
        const newEmojis = Array.from({ length: count }, (_, i) => {
            // Use a slightly more uniform random distribution to actually fill gaps
            const r = Math.sqrt(Math.random()) * 92;
            const theta = Math.random() * 2 * Math.PI;
            return {
                id: Date.now() + i + Math.random(),
                x: Math.cos(theta) * r,
                y: Math.sin(theta) * r,
                scale: 0,
                opacity: 0,
                rotate: Math.random() * 360,
                delay: Math.random() * 400,
            };
        });

        popEmojis = [...popEmojis, ...newEmojis];

        // Animate them in
        newEmojis.forEach((emoji) => {
            setTimeout(() => {
                popEmojis = popEmojis.map((e) =>
                    e.id === emoji.id
                        ? { ...e, scale: 1.0 + Math.random() * 0.5, opacity: 1 }
                        : e,
                );
            }, emoji.delay);
        });

        showOrbit = true;
    }

    function onDrag(e) {
        if (!isDragging) return;
        const clientX = e.type.includes("touch")
            ? e.touches[0].clientX
            : e.clientX;
        const clientY = e.type.includes("touch")
            ? e.touches[0].clientY
            : e.clientY;

        posX = ((clientX - dragStartX) / window.innerWidth) * 100;
        posY = ((clientY - dragStartY) / window.innerHeight) * 100;

        dragDuration = Date.now() - dragStartTime;

        // Emotional Escalation Logic
        if (dragDuration < 800) {
            // Early drag phase: FORCE serious expression
            emotionalState = "normal";
            currentExpression = expressions.top;
            tintIntensity = 0;
        } else if (dragDuration < 3000) {
            // Annoyed phase
            if (emotionalState !== "annoyed") {
                emotionalState = "annoyed";
                currentExpression = expressions.bottom; // Thinking/Concerned
                currentMessage = "Wait... what are you doing? 🤨";
                showBubble = true;
            }
            // Smoothly move from 0 to 0.4
            tintIntensity = ((dragDuration - 800) / 2200) * 0.4;
        } else if (dragDuration < 5500) {
            // Angry phase
            if (emotionalState !== "angry") {
                emotionalState = "angry";
                currentExpression = expressions.angry;
                currentMessage = "HEY! STOP IT! 😤";
                showBubble = true;
            }
            tintIntensity = 0.4 + ((dragDuration - 3000) / 2500) * 0.4; // 0.4 to 0.8
        } else {
            // Climax phase
            if (emotionalState !== "climax") {
                emotionalState = "climax";
                currentExpression = expressions.badWord;
                currentMessage = "STAWPPPP!!! 🤬";
                showBubble = true;
                // triggerClimaxEffects(); // Muted as requested
            }
            tintIntensity = 0.8;
        }

        posX = Math.max(8, Math.min(92, posX));
        posY = Math.max(12, Math.min(70, posY));

        targetX = posX;
        targetY = posY;
    }

    function stopDrag() {
        isDragging = false;
        window.removeEventListener("mousemove", onDrag);
        window.removeEventListener("mouseup", stopDrag);
        window.removeEventListener("touchmove", onDrag);
        window.removeEventListener("touchend", stopDrag);

        if (emotionalState !== "normal") {
            isRecovering = true;
            const prevState = emotionalState;

            // Climax state: Mind blown!
            if (prevState === "climax" || prevState === "angry") {
                isMindBlown = true;
                currentExpression = expressions.mindblown;
                currentMessage = "WOAHHH! 🤯";
                showBubble = true;
            }

            // Fade out emojis
            popEmojis = popEmojis.map((e) => ({
                ...e,
                opacity: 0,
                scale: 0,
            }));

            // Gracefully stop orbit
            showOrbit = false;

            // Delay to allow 'Mind Blown' expression to sink in
            setTimeout(() => {
                // Recovery sequence
                recoveryInterval = setInterval(() => {
                    tintIntensity -= 0.05;
                    if (tintIntensity <= 0) {
                        tintIntensity = 0;
                        emotionalState = "normal";
                        isRecovering = false;
                        clearInterval(recoveryInterval);

                        // Show "Feeling better" message while STILL in Mind Blown state or transitioning
                        showFeelingBetter = true;
                        currentMessage = "I'm feeling better now... 😮‍💨";
                        showBubble = true;

                        // Final return to happy only after the message is gone
                        addTimeout(() => {
                            showFeelingBetter = false;
                            isMindBlown = false;
                            currentExpression = expressions.default;
                            if (!isHovering && !isWaving) {
                                cycleMessage();
                            }
                        }, 3000);
                    }
                }, 100);
            }, 1000);
        } else {
            showBubble = true;
        }

        dragDuration = 0;

        // Ensure it snaps back to a side if windows are open
        snapToSide();
    }

    onMount(() => {
        window.addEventListener("mousemove", handleMouseMove);
        window.addEventListener("touchmove", handleMouseMove, {
            passive: true,
        });

        // Set initial mobile position
        if (isMobile()) {
            posX = 85;
            posY = 80;
            targetX = 85;
            targetY = 80;
        }
        unsubscribe = windowStore.subscribe(checkWindows);

        // ═══════════════════════════════════════════════════════════════════════
        // 🎬 ONBOARDING SEQUENCE - Welcome new users with a guided intro
        // ═══════════════════════════════════════════════════════════════════════

        // Set initial onboarding message
        const firstOnboarding = guideData.onboarding[0];
        currentMessage = firstOnboarding.text;
        currentExpression = expressions[firstOnboarding.expression];
        showBubble = true;

        // Initial wave after brief delay
        setTimeout(doWave, 1500);

        // Progress through onboarding steps automatically
        setTimeout(() => {
            if (visitedApps.size === 0) {
                onboardingStep = 1;
                showContextualGuide();
            }
        }, 4000);

        setTimeout(() => {
            if (visitedApps.size === 0) {
                onboardingStep = 2;
                showContextualGuide();
            }
        }, 8000);

        // Wave periodically (every 20 seconds when idle)
        waveInterval = setInterval(() => {
            if (
                !isHovering &&
                !isDragging &&
                emotionalState === "normal" &&
                !showFeelingBetter &&
                !hasActiveWindows // Don't wave when user is exploring apps
            ) {
                doWave();
            }
        }, 20000);

        // Cycle contextual messages every 6 seconds
        messageInterval = setInterval(() => {
            if (
                !isHovering &&
                !isWaving &&
                emotionalState === "normal" &&
                !showFeelingBetter &&
                !isRecovering &&
                !isDragging
            ) {
                showContextualGuide();
            }
        }, 6000);

        // Pick new movement target every 10 seconds (only when no windows)
        moveInterval = setInterval(() => {
            if (
                !hasActiveWindows &&
                !isDragging &&
                emotionalState === "normal"
            ) {
                pickNewTarget();
            }
        }, 10000);

        // Start animation loop
        animate();
    });

    onDestroy(() => {
        window.removeEventListener("mousemove", handleMouseMove);
        window.removeEventListener("touchmove", handleMouseMove);
        if (unsubscribe) unsubscribe();
        clearInterval(waveInterval);
        clearInterval(messageInterval);
        clearInterval(moveInterval);
        cancelAnimationFrame(animationFrame);
    });
</script>

<div
    class="widget-container"
    class:waving={isWaving}
    class:is-dragging={isDragging}
    bind:this={widgetElement}
    style="left: {posX}%; top: {posY}%;"
>
    <!-- Speech Bubble -->
    {#if showBubble}
        <div
            class="speech-bubble"
            class:wave-bubble={isWaving}
            class:feeling-better={showFeelingBetter}
            style="
                --bubble-scale: {emotionalState === 'climax'
                ? '1.25'
                : emotionalState === 'angry'
                  ? '1.1'
                  : '1'};
            "
        >
            <p>{currentMessage}</p>
            <div class="bubble-tail"></div>
        </div>
    {/if}

    <!-- Orbiting Emoji -->
    {#if showOrbit}
        <div
            class="orbit-emoji"
            style="
                transform: translate(
                    {Math.cos(orbitAngle) * 130}px,
                    {Math.sin(orbitAngle) * 30}px
                ) scale({0.8 + Math.sin(orbitAngle) * 0.4});
                z-index: {orbitZIndex};
                opacity: {isRecovering ? 0 : 1};
            "
        >
            😵‍💫
        </div>
    {/if}

    <!-- Main Memoji -->
    <div
        class="memoji-avatar"
        class:wave-animation={isWaving}
        class:angry-state={emotionalState === "angry" ||
            emotionalState === "climax"}
        class:pop-in={emotionalState === "angry" && dragDuration < 3100}
        role="button"
        tabindex="0"
        style="
            transform: perspective(600px) rotateX({tiltX}deg) rotateY({tiltY}deg);
            --tint-opacity: {tintIntensity};
            --tint-color: {emotionalState === 'annoyed'
            ? '255, 165, 0'
            : emotionalState === 'normal'
              ? '255, 255, 255'
              : '200, 40, 40'};
            --shake-amount: {emotionalState === 'climax'
            ? '4px'
            : emotionalState === 'angry'
              ? '2px'
              : '0px'};
        "
        on:mouseenter={handleMouseEnter}
        on:mouseleave={handleMouseLeave}
        on:click={handleClick}
        on:keydown={(e) => e.key === "Enter" && handleClick()}
        on:mousedown={startDrag}
        on:touchstart={startDrag}
    >
        <!-- Popping Emojis Inside (Muted as requested)
        {#each popEmojis as emoji (emoji.id)}
            <div
                class="pop-emoji"
                style="
                    left: calc(50% + {emoji.x}px);
                    top: calc(50% + {emoji.y}px);
                    transform: translate(-50%, -50%) scale({emoji.scale}) rotate({emoji.rotate ||
                    0}deg);
                    opacity: {emoji.opacity};
                "
            >
                😡
            </div>
        {/each}
        -->

        <img
            src="{base}memojis/{currentExpression}"
            alt="Shyam's Memoji"
            class="memoji-img"
            draggable="false"
        />

        <!-- Glow effect -->
        <div
            class="memoji-glow"
            class:angry-glow={emotionalState === "angry" ||
                emotionalState === "climax"}
        ></div>
    </div>
</div>

<style>
    .widget-container {
        position: absolute;
        transform: translate(-50%, -50%);
        z-index: 25;
        display: flex;
        flex-direction: column;
        align-items: center;
        pointer-events: none;
        /* Default jiggly position transition for snapping/floating */
        transition:
            left 0.25s cubic-bezier(0.175, 0.885, 0.32, 1.275),
            top 0.25s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        will-change: left, top;
    }

    .widget-container.is-dragging {
        /* Disable transition during active drag for buttery smoothness */
        transition: none !important;
    }

    /* Speech Bubble */
    .speech-bubble {
        position: relative;
        background: rgba(255, 255, 255, 0.95);
        backdrop-filter: blur(20px);
        -webkit-backdrop-filter: blur(20px);
        border-radius: 24px;
        padding: 14px 22px;
        margin-bottom: 15px;
        box-shadow: 0 8px 32px rgba(0, 0, 0, 0.15);
        animation: bubbleFadeIn 0.5s ease-out;
        pointer-events: auto;
        max-width: 220px;
        transform: scale(var(--bubble-scale, 1));
        transition: transform 0.2s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    }

    .speech-bubble.wave-bubble {
        animation: bubbleBounce 0.6s ease-out;
    }

    .speech-bubble p {
        margin: 0;
        font-size: 17px;
        font-weight: 600;
        color: #1d1d1f;
        text-align: center;
        line-height: 1.4;
    }

    .speech-bubble.feeling-better {
        animation: feelingBetterFade 3s forwards;
    }

    @keyframes feelingBetterFade {
        0% {
            opacity: 0;
            transform: translateY(10px);
        }
        20% {
            opacity: 1;
            transform: translateY(0);
        }
        80% {
            opacity: 1;
            transform: translateY(0);
        }
        100% {
            opacity: 0;
            transform: translateY(-10px);
        }
    }

    .bubble-tail {
        position: absolute;
        bottom: -12px;
        left: 50%;
        transform: translateX(-50%);
        width: 0;
        height: 0;
        border-left: 14px solid transparent;
        border-right: 14px solid transparent;
        border-top: 14px solid rgba(255, 255, 255, 0.95);
    }

    @keyframes bubbleFadeIn {
        from {
            opacity: 0;
            transform: translateY(10px) scale(0.9);
        }
        to {
            opacity: 1;
            transform: translateY(0) scale(1);
        }
    }

    @keyframes bubbleBounce {
        0% {
            transform: scale(0.8);
        }
        50% {
            transform: scale(1.1);
        }
        100% {
            transform: scale(1);
        }
    }

    /* Main Memoji - Smaller visual size but easier to grab */
    .memoji-avatar {
        position: relative;
        width: 150px; /* Reduced from 200px */
        height: 150px; /* Reduced from 200px */
        background: linear-gradient(
            145deg,
            rgba(255, 255, 255, 0.95),
            rgba(255, 255, 255, 0.8)
        );
        backdrop-filter: blur(30px);
        -webkit-backdrop-filter: blur(30px);
        border-radius: 50%;
        display: flex;
        align-items: center;
        justify-content: center;
        cursor: grab;
        transition:
            transform 0.15s ease-out,
            box-shadow 0.3s ease,
            background 0.8s ease-in-out;
        box-shadow:
            0 20px 60px rgba(0, 0, 0, 0.2),
            0 0 0 2px rgba(255, 255, 255, 0.6) inset;
        pointer-events: auto;
        user-select: none;
        overflow: visible;
        /* Background tint instead of overlay */
        background: linear-gradient(
                145deg,
                rgba(var(--tint-color), var(--tint-opacity)),
                rgba(var(--tint-color), calc(var(--tint-opacity) * 0.7))
            ),
            linear-gradient(
                145deg,
                rgba(255, 255, 255, 0.95),
                rgba(255, 255, 255, 0.8)
            );
        background-blend-mode: overlay;
    }

    /* Invisible expander creates a large draggable hit area around the memoji */
    .memoji-avatar::after {
        content: "";
        position: absolute;
        top: -50px;
        left: -50px;
        right: -50px;
        bottom: -50px;
        border-radius: 50%;
        cursor: grab;
        z-index: 10;
    }

    /* Ensure cursor changes during all interaction modes */
    .memoji-avatar:hover:not(.is-dragging),
    .memoji-avatar::after:hover:not(.widget-container.is-dragging *) {
        cursor: grab;
    }

    /* .pop-emoji {
        position: absolute;
        font-size: 32px;
        pointer-events: none;
        z-index: 10;
        animation: emojiPop 0.6s cubic-bezier(0.175, 0.885, 0.32, 1.275)
            infinite alternate;
    }
 
    @keyframes emojiPop {
        0% {
            transform: translate(-50%, -50%) scale(0.8);
        }
        100% {
            transform: translate(-50%, -50%) scale(1.2);
        }
    } */

    .orbit-emoji {
        position: absolute;
        font-size: 32px;
        pointer-events: none;
        transition: opacity 0.3s ease;
    }

    .memoji-avatar.pop-in {
        animation: popIn 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    }

    @keyframes popIn {
        0% {
            transform: scale(1);
        }
        50% {
            transform: scale(1.15);
        }
        100% {
            transform: scale(1);
        }
    }

    /* Wave animation on the avatar */
    .memoji-avatar.wave-animation {
        animation: waveMotion 0.8s ease-in-out 3;
    }

    @keyframes waveMotion {
        0%,
        100% {
            transform: perspective(600px) rotate(0deg);
        }
        25% {
            transform: perspective(600px) rotate(10deg) translateY(-5px);
        }
        75% {
            transform: perspective(600px) rotate(-10deg) translateY(-5px);
        }
    }

    .memoji-avatar:hover {
        box-shadow:
            0 25px 70px rgba(0, 0, 0, 0.25),
            0 0 0 3px rgba(255, 255, 255, 0.7) inset,
            0 0 50px rgba(103, 126, 234, 0.3);
    }

    .memoji-avatar:active {
        cursor: grabbing;
        transform: scale(0.95) !important;
    }

    .memoji-avatar.angry-state {
        box-shadow:
            0 20px 60px rgba(255, 0, 0, 0.3),
            0 0 0 2px rgba(255, 100, 100, 0.6) inset !important;
        animation: shake 0.15s ease-in-out infinite;
    }

    @keyframes shake {
        0%,
        100% {
            transform: perspective(600px) translate(0, 0) rotate(0deg);
        }
        25% {
            transform: perspective(600px)
                translate(var(--shake-amount), var(--shake-amount))
                rotate(0.4deg);
        }
        75% {
            transform: perspective(600px)
                translate(
                    calc(-1 * var(--shake-amount)),
                    calc(-1 * var(--shake-amount))
                )
                rotate(-0.4deg);
        }
    }

    .memoji-img {
        width: 88%;
        height: 88%;
        object-fit: contain;
        pointer-events: none;
        filter: drop-shadow(0 4px 12px rgba(0, 0, 0, 0.12));
        position: relative;
        z-index: 2;
    }

    /* Glow effect behind memoji */
    .memoji-glow {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        width: 250%;
        height: 250%;
        background: radial-gradient(
            circle,
            rgba(103, 126, 234, 0.2) 0%,
            rgba(118, 75, 162, 0.12) 25%,
            transparent 60%
        );
        pointer-events: none;
        z-index: -1;
        animation: glowPulse 3s ease-in-out infinite;
    }

    .memoji-glow.angry-glow {
        background: radial-gradient(
            circle,
            rgba(255, 0, 0, 0.4) 0%,
            rgba(255, 0, 0, 0.2) 25%,
            transparent 60%
        );
        animation: none; /* Stop pulsing when angry */
    }

    @keyframes glowPulse {
        0%,
        100% {
            opacity: 0.7;
            transform: translate(-50%, -50%) scale(1);
        }
        50% {
            opacity: 1;
            transform: translate(-50%, -50%) scale(1.15);
        }
    }

    /* Floating shadow */
    .widget-container::before {
        content: "";
        position: absolute;
        bottom: -35px;
        left: 50%;
        transform: translateX(-50%);
        width: 120px;
        height: 25px;
        background: radial-gradient(
            ellipse,
            rgba(0, 0, 0, 0.18),
            transparent 70%
        );
        border-radius: 50%;
        animation: shadowPulse 3s ease-in-out infinite;
    }

    @keyframes shadowPulse {
        0%,
        100% {
            transform: translateX(-50%) scale(1);
            opacity: 0.6;
        }
        50% {
            transform: translateX(-50%) scale(0.75);
            opacity: 0.35;
        }
    }

    /* Waving container bounce */
    .widget-container.waving {
        animation: containerBounce 0.8s ease-in-out 2;
    }

    @keyframes containerBounce {
        0%,
        100% {
            transform: translate(-50%, -50%);
        }
        50% {
            transform: translate(-50%, -55%);
        }
    }

    /* Mobile Responsive - Smaller widget for mobile */
    @media (max-width: 768px) {
        .widget-container {
            /* Remove fixed positioning to allow dragging */
            z-index: 50;
        }

        .widget-container::before {
            /* Hide the shadow on mobile */
            display: none;
        }

        .memoji-avatar {
            width: 80px;
            height: 80px;
            box-shadow:
                0 10px 30px rgba(0, 0, 0, 0.2),
                0 0 0 2px rgba(255, 255, 255, 0.5) inset;
        }

        .memoji-glow {
            width: 180%;
            height: 180%;
        }

        .speech-bubble {
            max-width: 160px;
            padding: 10px 14px;
            margin-bottom: 10px;
            border-radius: 18px;
        }

        .speech-bubble p {
            font-size: 13px;
        }

        .bubble-tail {
            border-left: 10px solid transparent;
            border-right: 10px solid transparent;
            border-top: 10px solid rgba(255, 255, 255, 0.95);
            bottom: -10px;
        }
    }

    /* Tablet adjustments */
    @media (min-width: 769px) and (max-width: 1023px) {
        .memoji-avatar {
            width: 160px;
            height: 160px;
        }

        .speech-bubble {
            max-width: 180px;
            padding: 12px 18px;
        }

        .speech-bubble p {
            font-size: 15px;
        }
    }
</style>
