<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jump Shoot</title>
    <!-- Corrected Google Fonts link -->
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background-color: #222;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            font-family: 'Press Start 2P', cursive;
            color: #eee;
            user-select: none; /* Prevent text selection */
            -webkit-user-select: none; /* Safari */
            -moz-user-select: none; /* Firefox */
            -ms-user-select: none; /* IE10+ */
        }

        #game-container {
            background-color: #333;
            border: 5px solid #555;
            border-radius: 15px;
            overflow: hidden;
            box-shadow: 0 0 30px rgba(0, 255, 255, 0.5), 0 0 60px rgba(255, 0, 255, 0.3);
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
            max-width: 95vw; /* Allow it to take up more width */
            box-sizing: border-box; /* Changed from 'border' to 'border-box' for correct sizing */
            display: none; /* Hidden by default */
        }

        canvas {
            background-color: #000;
            display: block;
            border: 2px solid #555;
            border-radius: 8px;
            width: 100%; /* Make canvas responsive */
            max-width: 1000px; /* Increased max width for desktop */
            height: auto; /* Maintain aspect ratio */
            aspect-ratio: 16 / 9; /* Typical game aspect ratio */
            margin-bottom: 20px;
        }

        #game-controls {
            display: flex;
            justify-content: center;
            gap: 20px;
            flex-wrap: wrap; /* Wrap buttons on small screens */
            width: 100%;
        }

        .control-group {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 10px;
        }

        .player-label {
            font-size: 0.8em;
            color: #ccc;
            margin-bottom: 5px;
        }

        .game-button {
            background-color: #007bff;
            color: white;
            padding: 15px 30px;
            border: none;
            border-radius: 10px;
            font-family: 'Press Start 2P', cursive;
            font-size: 1em;
            cursor: pointer;
            transition: background-color 0.2s ease, transform 0.1s ease, box-shadow 0 0.2s ease;
            box-shadow: 0 5px 0 #0056b3;
            text-shadow: 1px 1px 2px rgba(0,0,0,0.5);
            min-width: 120px; /* Ensure buttons have minimum size */
        }

        .game-button:hover {
            background-color: #0056b3;
            transform: translateY(-2px);
            box-shadow: 0 7px 0 #004085;
        }

        .game-button:active {
            background-color: #004085;
            transform: translateY(3px);
            box-shadow: 0 2px 0 #002c5f;
        }

        .game-button.jump {
            background-color: #28a745;
            box-shadow: 0 5px 0 #1e7e34;
        }

        .game-button.jump:hover {
            background-color: #1e7e34;
            box-shadow: 0 7px 0 #155d28;
        }

        .game-button.jump:active {
            background-color: #155d28;
            box-shadow: 0 2px 0 #0f3d1b;
        }

        .game-button.fireball {
            background-color: #dc3545;
            box-shadow: 0 5px 0 #bd2130;
        }

        .game-button.fireball:hover {
            background-color: #bd2130;
            box-shadow: 0 7px 0 #8d1924;
        }

        .game-button.fireball:active {
            background-color: #8d1924;
            box-shadow: 0 2px 0 #6e131b;
        }

        #message-box {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: rgba(0, 0, 0, 0.85);
            border: 4px solid #00ffff;
            border-radius: 15px;
            padding: 30px 40px;
            text-align: center;
            box-shadow: 0 0 25px rgba(0, 255, 255, 0.7);
            z-index: 100;
            display: none; /* Hidden by default */
            flex-direction: column;
            gap: 20px;
            font-size: 1.2em;
        }

        #message-text {
            color: #fff;
        }

        #restart-button {
            background-color: #ffc107;
            color: #333;
            padding: 12px 25px;
            border: none;
            border-radius: 8px;
            font-family: 'Press Start 2P', cursive;
            font-size: 0.9em;
            cursor: pointer;
            box-shadow: 0 4px 0 #d39e00;
            transition: background-color 0.2s ease, transform 0.1s ease, box-shadow 0.2s ease;
            display: none; /* Hidden by default, shown only after game set win */
        }

        #restart-button:hover {
            background-color: #e0a800;
            transform: translateY(-1px);
            box-shadow: 0 5px 0 #b38700;
        }

        #restart-button:active {
            background-color: #c69500;
            transform: translateY(2px);
            box-shadow: 0 2px 0 #9f7d00;
        }

        .score-display {
            position: absolute;
            top: 90px; /* Below the health/cooldown bars */
            left: 20px;
            color: #eee;
            font-size: 0.9em;
            text-align: left;
            width: 150px; /* Match health bar width */
        }

        .score-display.player2 {
            left: auto;
            right: 20px;
            text-align: right;
        }

        /* Styles for the new menu screen */
        #menu-screen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: rgba(0, 0, 0, 0.9);
            border: 4px solid #00ffff;
            border-radius: 15px;
            padding: 50px 60px;
            text-align: center;
            box-shadow: 0 0 25px rgba(0, 255, 255, 0.7);
            z-index: 200; /* Higher z-index to be on top */
            display: flex;
            flex-direction: column;
            gap: 30px;
        }

        #menu-title {
            font-size: 3em;
            color: #00ffff;
            text-shadow: 0 0 15px rgba(0, 255, 255, 0.9);
            margin-bottom: 20px;
        }

        .menu-button {
            background-color: #007bff;
            color: white;
            padding: 20px 40px;
            border: none;
            border-radius: 10px;
            font-family: 'Press Start 2P', cursive;
            font-size: 1.5em;
            cursor: pointer;
            transition: background-color 0.2s ease, transform 0.1s ease, box-shadow 0.2s ease;
            box-shadow: 0 7px 0 #0056b3;
            text-shadow: 1px 1px 2px rgba(0,0,0,0.5);
            width: 100%;
            max-width: 300px;
            margin: 0 auto;
        }

        .menu-button:hover {
            background-color: #0056b3;
            transform: translateY(-3px);
            box-shadow: 0 10px 0 #004085;
        }

        .menu-button:active {
            background-color: #004085;
            transform: translateY(2px);
            box-shadow: 0 4px 0 #002c5f;
        }

        /* Styles for the new character selection screen */
        #character-select-screen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: rgba(0, 0, 0, 0.9);
            border: 4px solid #00ffff;
            border-radius: 15px;
            padding: 50px 60px;
            text-align: center;
            box-shadow: 0 0 25px rgba(0, 255, 255, 0.7);
            z-index: 200;
            display: none; /* Hidden by default */
            flex-direction: column;
            gap: 30px;
        }

        #character-select-title {
            font-size: 2.5em;
            color: #00ffff;
            text-shadow: 0 0 10px rgba(0, 255, 255, 0.7);
            margin-bottom: 15px;
        }

        .character-option {
            background-color: #444;
            color: white;
            padding: 20px;
            border: 3px solid #777;
            border-radius: 10px;
            font-family: 'Press Start 2P', cursive;
            font-size: 1.2em;
            cursor: pointer;
            transition: background-color 0.2s ease, transform 0.1s ease, box-shadow 0.2s ease, border-color 0.2s ease;
            box-shadow: 0 5px 0 #333;
            text-shadow: 1px 1px 2px rgba(0,0,0,0.5);
            margin-bottom: 15px;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 10px;
            min-width: 250px;
        }

        .character-option:hover {
            background-color: #555;
            transform: translateY(-2px);
            box-shadow: 0 7px 0 #222;
            border-color: #00ffff;
        }

        .character-option:active {
            background-color: #333;
            transform: translateY(3px);
            box-shadow: 0 2px 0 #111;
        }

        .character-description {
            font-size: 0.7em;
            color: #ccc;
            margin-top: 5px;
        }


        @media (max-width: 768px) {
            #game-container {
                padding: 10px;
                max-width: 95vw;
            }

            canvas {
                margin-bottom: 15px;
            }

            #game-controls {
                flex-direction: column;
                gap: 15px;
            }

            .control-group {
                flex-direction: row;
                justify-content: space-around;
                width: 100%;
            }

            .game-button {
                padding: 12px 20px;
                font-size: 0.9em;
                min-width: unset; /* Allow buttons to shrink */
                flex: 1; /* Distribute space evenly */
                margin: 0 5px; /* Add some horizontal margin */
            }

            .player-label {
                display: none; /* Hide player labels on small screens to save space */
            }

            #message-box {
                padding: 20px 30px;
                font-size: 1em;
            }

            #restart-button {
                padding: 10px 20px;
                font-size: 0.8em;
            }

            .score-display {
                top: 75px; /* Adjust for smaller screens */
                font-size: 0.8em;
            }

            #menu-screen, #character-select-screen {
                padding: 30px 40px;
                gap: 20px;
            }

            #menu-title {
                font-size: 2em;
            }

            #character-select-title {
                font-size: 1.8em;
            }

            .menu-button, .character-option {
                padding: 15px 30px;
                font-size: 1.2em;
            }
        }
    </style>
</head>
<body>
    <div id="game-container">
        <canvas id="gameCanvas" width="1000" height="562.5"></canvas> <!-- Increased canvas size (16:9 aspect ratio) -->
        <div id="game-controls">
            <div class="control-group">
                <span class="player-label">Player 1 (A/S)</span>
                <button id="player1JumpBtn" class="game-button jump">JUMP</button>
                <button id="player1FireBtn" class="game-button fireball">FIREBALL</button>
            </div>
            <div class="control-group" id="player2-controls">
                <span class="player-label">Player 2 (K/L)</span>
                <button id="player2JumpBtn" class="game-button jump">JUMP</button>
                <button id="player2FireBtn" class="game-button fireball">FIREBALL</button>
            </div>
        </div>
    </div>

    <div id="message-box">
        <span id="message-text"></span>
        <button id="restart-button">RESTART</button>
    </div>

    <!-- Main Menu Screen -->
    <div id="menu-screen">
        <h1 id="menu-title">JUMP_SHOOT</h1>
        <button id="onePlayerBtn" class="menu-button">1 PLAYER</button>
        <button id="twoPlayerBtn" class="menu-button">2 PLAYERS</button>
    </div>

    <!-- New Character Selection Screen -->
    <div id="character-select-screen">
        <h1 id="character-select-title">SELECT YOUR CHARACTER</h1>
        <button id="charStandardBtn" class="character-option">
            Standard Character
            <span class="character-description">Basic fireball, reliable.</span>
        </button>
        <button id="charBouncingBtn" class="character-option">
            Bouncing Character
            <span class="character-description">Fireball bounces off the ground.</span>
        </button>
        <button id="charCurvingBtn" class="character-option">
            Curving Character
            <span class="character-description">Fireball curves based on player's jump.</span>
        </button>
        <button id="charReflectBtn" class="character-option">
            Reflect Character
            <span class="character-description">Reflects opponent's fireballs.</span>
        </button>
    </div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        const player1JumpBtn = document.getElementById('player1JumpBtn');
        const player1FireBtn = document.getElementById('player1FireBtn');
        const player2JumpBtn = document.getElementById('player2JumpBtn');
        const player2FireBtn = document.getElementById('player2FireBtn');
        const player2Controls = document.getElementById('player2-controls'); // Get the player 2 controls group

        const messageBox = document.getElementById('message-box');
        const messageText = document.getElementById('message-text');
        const restartButton = document.getElementById('restart-button');

        const menuScreen = document.getElementById('menu-screen');
        const onePlayerBtn = document.getElementById('onePlayerBtn');
        const twoPlayerBtn = document.getElementById('twoPlayerBtn');
        const gameContainer = document.getElementById('game-container');

        const characterSelectScreen = document.getElementById('character-select-screen');
        const characterSelectTitle = document.getElementById('character-select-title');
        const charStandardBtn = document.getElementById('charStandardBtn');
        const charBouncingBtn = document = document.getElementById('charBouncingBtn');
        const charCurvingBtn = document.getElementById('charCurvingBtn');
        const charReflectBtn = document.getElementById('charReflectBtn');


        // Game parameters
        const GRAVITY = 0.8; // Increased gravity for a "heavier" feel
        const JUMP_VELOCITY = -20; // Increased jump velocity to compensate for heavier gravity
        const PLAYER_WIDTH = 50;
        const PLAYER_HEIGHT = 80;
        const PLAYER_SPEED = 5; // horizontal speed (not used directly with current input scheme, but good to have)
        const FIREBALL_RADIUS = 10;
        const FIREBALL_SPEED = 10;
        const FIREBALL_DAMAGE = 10;
        const MAX_HEALTH = 100;
        const FIREBALL_COOLDOWN_TIME = 2000; // 2 seconds cooldown for fireballs (increased from 500ms)

        // Super Meter Parameters
        const MAX_SUPER_METER = 100;
        const SUPER_METER_GROWTH_RATE = 0.2; // How fast the super meter grows per frame
        const SUPER_FIREBALL_RADIUS = 25; // Larger radius for super fireball
        const SUPER_FIREBALL_DAMAGE = 25; // More damage for super fireball
        const SUPER_FIREBALL_COLOR = '#ff0'; // Bright yellow for super fireball
        const SUPER_EFFECT_PULSE_SPEED = 0.005; // Speed of the super glow pulse (lower is slower)
        const SUPER_EFFECT_MAX_ALPHA = 0.5; // Max transparency of the super glow

        // Hit indicator parameters
        const HIT_EFFECT_DURATION = 200; // How long the hit effect lasts in milliseconds
        const HIT_COLOR = '#ff0000'; // Red for hit indication

        // New constant for consistent bouncing fireball velocity
        const FIREBALL_BOUNCE_VELOCITY = -10; // Consistent upward velocity for bouncing fireball

        // Timer parameters
        const ROUND_TIME_LIMIT = 60; // 60 seconds per round
        let currentRoundTime = ROUND_TIME_LIMIT; // Current time remaining in the round

        // Character Definitions
        const characters = {
            standard: {
                color: '#00ffff', // Cyan
                fireballType: 'standard',
                fireballColor: '#ff8c00', // Dark orange
                fireballRadius: FIREBALL_RADIUS,
                fireballDamage: FIREBALL_DAMAGE,
                fireballSpeed: FIREBALL_SPEED,
                fireballStrength: 10 // Base strength for collisions
            },
            bouncing: {
                color: '#ff00ff', // Magenta
                fireballType: 'bouncing',
                fireballColor: '#00ff00', // Green for bouncing
                fireballRadius: FIREBALL_RADIUS * 1.2, // Slightly larger bouncing fireball
                fireballDamage: FIREBALL_DAMAGE,
                fireballSpeed: FIREBALL_SPEED * 0.8, // Slightly slower bouncing fireball
                bounceFactor: -0.7, // Not directly used for vertical bounce anymore, but kept
                fireballStrength: 12 // Slightly stronger
            },
            curving: { // Curving Character
                color: '#ff6600', // Orange
                fireballType: 'curving',
                fireballColor: '#00ccff', // Light blue for curving
                fireballRadius: FIREBALL_RADIUS,
                fireballDamage: FIREBALL_DAMAGE,
                fireballSpeed: FIREBALL_SPEED,
                // These values control the initial direction and how strongly the fireball curves.
                // Adjust these to fine-tune the curve.
                // For ground shot: starts up, curves further up (anti-gravity)
                initialUpwardCurveDy: -0.4, // Initial vertical velocity when shooting from ground (negative for up)
                upwardCurveAcceleration: -0.09, // Negative acceleration pulls it upwards (anti-gravity)

                // For air shot: starts down, curves further down (gravity)
                initialDownwardCurveDy: 0.04,  // Initial vertical velocity when shooting from air (positive for down)
                downwardCurveAcceleration: 0.09, // Positive acceleration pulls it downwards (gravity)
                fireballStrength: 10
            },
            reflect: { // Reflect Character
                color: '#800080', // Purple
                fireballType: 'reflect', // Special type for reflect character
                fireballColor: '#800080', // Reflected fireballs will be purple
                reflectSpeedMultiplier: 1.5, // Reflected fireball is 50% faster
                reflectSizeMultiplier: 1.5,  // Reflected fireball is 50% larger
                reflectDamageMultiplier: 1.0, // Reflected fireball does normal damage
                reflectCooldownTime: FIREBALL_COOLDOWN_TIME, // Same cooldown as shooting
                superReflectSpeedMultiplier: 0.5, // Super reflected fireball is slower
                superReflectSizeMultiplier: 2.5,  // Super reflected fireball is much larger
                superReflectDamageMultiplier: 1.5, // Super reflected fireball does 50% more damage
                superReflectFireballStrength: 50, // High strength for super reflected fireball
                // Shield properties for reflect character
                shieldColor: 'rgba(255, 255, 255, 0.7)', // Semi-transparent white shield
                shieldWidth: 10,
                shieldHeight: 75,
                shieldOffsetX: -5, // Offset from player's edge for visual overlap
                shieldActiveDuration: 300, // How long the shield stays active after button press (milliseconds)
                superMeterGainOnActivate: 5, // New property: super meter gained on activation
                superMeterDrainPerSecond: 10 // Super meter drains by this amount per second while reflecting
            }
        };

        // Game state variables
        let gameState = 'menu'; // 'menu', 'charSelectP1', 'charSelectP2', 'playing', 'roundOver', 'gameOver', 'player1Win'
        let gameRunning = false;
        let lastFrameTime = 0;
        const frameRate = 1000 / 60; // Target 60 FPS
        let isTwoPlayerMode = false;

        // Player objects
        let player1 = {};
        let player2 = {};
        let fireballs = [];

        // Round system variables
        let player1RoundsWon = 0;
        let player2RoundsWon = 0;
        const ROUNDS_TO_WIN = 2; // Number of rounds a player needs to win the game set

        // Selected Characters
        let selectedCharacterTypeP1 = null;
        let selectedCharacterTypeP2 = null;

        // AI Progression Variables
        const aiOpponents = ['standard', 'bouncing', 'curving', 'reflect']; // Sequence of AI opponents
        let currentOpponentIndex = 0; // Index of the current AI opponent in the array

        /**
         * Transitions the game state and updates screen visibility.
         * @param {string} newState - The new state to transition to.
         */
        function transitionToState(newState) {
            gameState = newState;
            menuScreen.style.display = 'none';
            characterSelectScreen.style.display = 'none';
            gameContainer.style.display = 'none';
            messageBox.style.display = 'none'; // Hide message box by default on state change

            // Hide or show player 2 controls based on game mode
            if (isTwoPlayerMode) {
                player2Controls.style.display = 'flex';
            } else {
                player2Controls.style.display = 'none';
            }

            switch (newState) {
                case 'menu':
                    menuScreen.style.display = 'flex';
                    break;
                case 'charSelectP1':
                    characterSelectScreen.style.display = 'flex';
                    characterSelectTitle.textContent = "PLAYER 1: SELECT YOUR CHARACTER";
                    break;
                case 'charSelectP2':
                    characterSelectScreen.style.display = 'flex';
                    characterSelectTitle.textContent = "PLAYER 2: SELECT YOUR CHARACTER";
                    break;
                case 'playing':
                    gameContainer.style.display = 'flex';
                    // initializeGame() is now called from the character selection logic
                    break;
                case 'roundOver':
                    gameContainer.style.display = 'flex'; // Keep game container visible
                    // Message box will be shown by showMessage, and then resetRound will be called
                    break;
                case 'gameOver': // Used for player 1 loss or player 2 win in 2-player
                case 'player1Win': // New state for player 1 winning the entire campaign
                    gameContainer.style.display = 'flex'; // Keep game container visible for final score
                    // Message box will be shown by showMessage, and then restart button will be visible
                    break;
            }
            // Always render on state change to ensure correct initial display
            render(performance.now());
        }

        /**
         * Initializes or resets the game state for a new game set (or campaign start for 1-player).
         */
        function initializeGame() {
            // Reset round scores for a new game set
            player1RoundsWon = 0;
            player2RoundsWon = 0;
            if (!isTwoPlayerMode) {
                currentOpponentIndex = 0; // Reset opponent index for new 1-player campaign
                selectedCharacterTypeP2 = aiOpponents[currentOpponentIndex]; // Set first AI opponent
            }
            resetRound(); // Call resetRound to set up the first round
            restartButton.style.display = 'none'; // Hide restart button
            gameRunning = true; // Ensure game is running after initialization
            // Ensure canvas is correctly sized when game starts
            resizeCanvas();
        }

        /**
         * Resets the game state for a new round (keeps round scores for current set).
         */
        function resetRound() {
            // Player 1 setup based on selected character
            const p1Char = characters[selectedCharacterTypeP1];
            player1 = {
                x: canvas.width * 0.15,
                y: canvas.height - PLAYER_HEIGHT - 20,
                width: PLAYER_WIDTH,
                height: PLAYER_HEIGHT,
                color: p1Char.color,
                dx: 0,
                dy: 0,
                onGround: false,
                health: MAX_HEALTH,
                isJumping: false,
                canShoot: (p1Char.fireballType !== 'reflect'), // Reflect character cannot shoot with 'fireball' button
                facing: 'right',
                lastFireballTime: 0, // Used for cooldown for both shooting and reflecting
                superMeter: 0,
                isSuperReady: false,
                fireballType: p1Char.fireballType,
                fireballColor: p1Char.fireballColor,
                fireballRadius: p1Char.fireballRadius,
                fireballDamage: p1Char.fireballDamage,
                fireballSpeed: p1Char.fireballSpeed,
                fireballStrength: p1Char.fireballStrength || 10, // Default strength
                // Reflect specific properties
                reflectSpeedMultiplier: p1Char.reflectSpeedMultiplier || 1,
                reflectSizeMultiplier: p1Char.reflectSizeMultiplier || 1,
                reflectDamageMultiplier: p1Char.reflectDamageMultiplier || 1,
                superReflectSpeedMultiplier: p1Char.superReflectSpeedMultiplier || 1,
                superReflectSizeMultiplier: p1Char.superReflectSizeMultiplier || 1,
                superReflectDamageMultiplier: p1Char.superReflectDamageMultiplier || 1,
                superReflectFireballStrength: p1Char.superReflectFireballStrength || 50,
                reflectCooldownTime: p1Char.reflectCooldownTime || FIREBALL_COOLDOWN_TIME,
                // Shield properties for reflect character
                shieldActive: false,
                shieldStartTime: 0,
                shieldCurrentAlpha: 0,
                shieldActiveDuration: p1Char.shieldActiveDuration || 0, // Default to 0 if not defined
                superMeterGainOnActivate: p1Char.superMeterGainOnActivate || 0, // Default to 0 if not defined
                superMeterDrainPerSecond: p1Char.superMeterDrainPerSecond || 0, // Drain rate
                lastHitTime: 0, // New: for hit indicator
                superEffectActive: false, // New: for super available indicator
                superEffectStartTime: 0, // New: for super available indicator timing
                currentFireballCooldown: FIREBALL_COOLDOWN_TIME, // Dynamic cooldown
                currentSuperMeterGrowthRate: SUPER_METER_GROWTH_RATE // Dynamic growth rate
            };

            // Player 2 setup based on selected character (if two-player mode) or current AI opponent
            const p2Char = characters[selectedCharacterTypeP2];
            player2 = {
                x: canvas.width * 0.85 - PLAYER_WIDTH,
                y: canvas.height - PLAYER_HEIGHT - 20,
                width: PLAYER_WIDTH,
                height: PLAYER_HEIGHT,
                color: p2Char.color,
                dx: 0,
                dy: 0,
                onGround: false,
                health: MAX_HEALTH,
                isJumping: false,
                canShoot: (p2Char.fireballType !== 'reflect'), // Reflect character cannot shoot with 'fireball' button
                facing: 'left',
                lastFireballTime: 0, // Used for cooldown for both shooting and reflecting
                superMeter: 0,
                isSuperReady: false,
                fireballType: p2Char.fireballType,
                fireballColor: p2Char.fireballColor,
                fireballRadius: p2Char.fireballRadius,
                fireballDamage: p2Char.fireballDamage,
                fireballSpeed: p2Char.fireballSpeed,
                fireballStrength: p2Char.fireballStrength || 10, // Default strength
                // Reflect specific properties
                reflectSpeedMultiplier: p2Char.reflectSpeedMultiplier || 1,
                reflectSizeMultiplier: p2Char.reflectSizeMultiplier || 1,
                reflectDamageMultiplier: p2Char.reflectDamageMultiplier || 1,
                superReflectSpeedMultiplier: p2Char.superReflectSpeedMultiplier || 1,
                superReflectSizeMultiplier: p2Char.superReflectSizeMultiplier || 1,
                superReflectDamageMultiplier: p2Char.superReflectDamageMultiplier || 1,
                superReflectFireballStrength: p2Char.superReflectFireballStrength || 50,
                reflectCooldownTime: p2Char.reflectCooldownTime || FIREBALL_COOLDOWN_TIME,
                // Shield properties for reflect character
                shieldActive: false,
                shieldStartTime: 0,
                shieldCurrentAlpha: 0,
                shieldActiveDuration: p2Char.shieldActiveDuration || 0, // Default to 0 if not defined
                superMeterGainOnActivate: p2Char.superMeterGainOnActivate || 0, // Default to 0 if not defined
                superMeterDrainPerSecond: p2Char.superMeterDrainPerSecond || 0, // Drain rate
                lastHitTime: 0, // New: for hit indicator
                superEffectActive: false, // New: for super available indicator
                superEffectStartTime: 0, // New: for super available indicator timing
                currentFireballCooldown: FIREBALL_COOLDOWN_TIME, // Dynamic cooldown
                currentSuperMeterGrowthRate: SUPER_METER_GROWTH_RATE // Dynamic growth rate
            };

            currentRoundTime = ROUND_TIME_LIMIT; // Reset timer for new round
            fireballs = [];
            gameRunning = true; // Game logic resumes
            messageBox.style.display = 'none'; // Hide message box
            gameState = 'playing'; // Set game state back to playing
        }

        /**
         * Draws a player on the canvas.
         * @param {object} player - The player object to draw.
         */
        function drawPlayer(player, currentTime) {
            // Apply hit effect (temporary red flash)
            const defaultColor = characters[player.fireballType].color;
            let displayColor = defaultColor;
            if (currentTime - player.lastHitTime < HIT_EFFECT_DURATION) {
                displayColor = HIT_COLOR;
            }

            // Draw super available indicator glow
            if (player.isSuperReady) {
                if (!player.superEffectActive) {
                    player.superEffectActive = true;
                    player.superEffectStartTime = currentTime;
                }
                const elapsed = currentTime - player.superEffectStartTime;
                // Use sine wave for pulsating effect
                const pulseAlpha = (Math.sin(elapsed * SUPER_EFFECT_PULSE_SPEED) * 0.5 + 0.5) * SUPER_EFFECT_MAX_ALPHA;

                ctx.save();
                ctx.globalAlpha = pulseAlpha;
                ctx.fillStyle = SUPER_FIREBALL_COLOR;
                ctx.beginPath();
                ctx.arc(player.x + player.width / 2, player.y + player.height / 2,
                        Math.max(player.width, player.height) * 1.2, 0, Math.PI * 2); // Larger radius for glow
                ctx.fill();
                ctx.restore();
            } else {
                player.superEffectActive = false;
            }


            ctx.fillStyle = displayColor;
            ctx.fillRect(player.x, player.y, player.width, player.height);

            // Simple head/body indicator
            ctx.fillStyle = '#fff'; // White for head
            ctx.beginPath();
            ctx.arc(player.x + player.width / 2, player.y + player.height / 4, player.width / 4, 0, Math.PI * 2);
            ctx.fill();

            // Draw eye indicating direction
            ctx.fillStyle = '#000'; // Black for eye
            if (player.facing === 'right') {
                ctx.beginPath();
                ctx.arc(player.x + player.width * 0.7, player.y + player.height * 0.2, 3, 0, Math.PI * 2);
                ctx.fill();
            } else {
                ctx.beginPath();
                ctx.arc(player.x + player.width * 0.3, player.y + player.height * 0.2, 3, 0, Math.PI * 2);
                ctx.fill();
            }
        }

        /**
         * Draws a fireball on the canvas.
         * @param {object} fireball - The fireball object to draw.
         */
        function drawFireball(fireball) {
            ctx.fillStyle = fireball.color;
            ctx.beginPath();
            ctx.arc(fireball.x, fireball.y, fireball.radius, 0, Math.PI * 2);
            ctx.fill();
            // Add a glow effect
            ctx.shadowBlur = 10;
            ctx.shadowColor = fireball.color;
            ctx.fillStyle = fireball.color;
            ctx.beginPath();
            ctx.arc(fireball.x, fireball.y, fireball.radius * 0.8, 0, Math.PI * 2);
            ctx.fill();
            ctx.shadowBlur = 0; // Reset shadow
        }

        /**
         * Draws the health bar for a player.
         * @param {object} player - The player object.
         * @param {number} x - X coordinate for the health bar.
         * @param {number} y - Y coordinate for the health bar.
         */
        function drawHealthBar(player, x, y) {
            const barWidth = 150;
            const barHeight = 20;
            const healthPercentage = player.health / MAX_HEALTH;
            const currentHealthWidth = barWidth * healthPercentage;

            // Background of health bar
            ctx.fillStyle = '#555';
            ctx.fillRect(x, y, barWidth, barHeight);

            // Current health
            ctx.fillStyle = player.health > 50 ? '#28a745' : (player.health > 20 ? '#ffc107' : '#dc3545');
            ctx.fillRect(x, y, currentHealthWidth, barHeight);

            // Border
            ctx.strokeStyle = '#eee';
            ctx.lineWidth = 2;
            ctx.strokeRect(x, y, barWidth, barHeight);

            // Health text
            ctx.fillStyle = '#eee';
            ctx.font = '14px "Press Start 2P"';
            ctx.textAlign = 'center';
            ctx.fillText(`${player.health}`, x + barWidth / 2, y + barHeight / 2 + 5);
        }

        /**
         * Draws the fireball cooldown bar for a player.
         * @param {object} player - The player object.
         * @param {number} x - X coordinate for the cooldown bar.
         * @param {number} y - Y Y coordinate for the cooldown bar.
         * @param {number} currentTime - The current time in milliseconds.
         */
        function drawCooldownBar(player, x, y, currentTime) {
            const barWidth = 150;
            const barHeight = 10;
            const timeElapsed = currentTime - player.lastFireballTime;
            // Use player's dynamic cooldown for calculation
            const cooldownRemaining = Math.max(0, player.currentFireballCooldown - timeElapsed);
            const cooldownPercentage = 1 - (cooldownRemaining / player.currentFireballCooldown); // 0 to 1

            // Background of cooldown bar
            ctx.fillStyle = '#555';
            ctx.fillRect(x, y, barWidth, barHeight);

            // Cooldown progress
            ctx.fillStyle = '#007bff'; // Blue for cooldown
            ctx.fillRect(x, y, barWidth * cooldownPercentage, barHeight);

            // Border
            ctx.strokeStyle = '#eee';
            ctx.lineWidth = 1;
            ctx.strokeRect(x, y, barWidth, barHeight);

            // Label
            ctx.fillStyle = '#eee';
            ctx.font = '10px "Press Start 2P"';
            ctx.textAlign = 'center';
            ctx.fillText("FIREBALL", x + barWidth / 2, y + barHeight / 2 + 4);
        }

        /**
         * Draws the super meter bar for a player.
         * @param {object} player - The player object.
         * @param {number} x - X coordinate for the super meter bar.
         * @param {number} y - Y coordinate for the super meter bar.
         */
        function drawSuperMeter(player, x, y) {
            const barWidth = 150;
            const barHeight = 10;
            const meterPercentage = player.superMeter / MAX_SUPER_METER;

            // Background of meter bar
            ctx.fillStyle = '#555';
            ctx.fillRect(x, y, barWidth, barHeight);

            // Meter progress
            ctx.fillStyle = player.isSuperReady ? SUPER_FIREBALL_COLOR : '#8a2be2'; // Yellow when full, blue-violet otherwise
            ctx.fillRect(x, y, barWidth * meterPercentage, barHeight);

            // Border
            ctx.strokeStyle = '#eee';
            ctx.lineWidth = 1;
            ctx.strokeRect(x, y, barWidth, barHeight);

            // Label
            ctx.fillStyle = '#eee';
            ctx.font = '10px "Press Start 2P"';
            ctx.textAlign = 'center';
            ctx.fillText("SUPER", x + barWidth / 2, y + barHeight / 2 + 4);
        }

        /**
         * Draws the round scores.
         */
        function drawRoundScores() {
            ctx.fillStyle = '#eee';
            ctx.font = '16px "Press Start 2P"';
            ctx.textAlign = 'left';
            ctx.fillText(`P1 Wins: ${player1RoundsWon}`, 20, 90); // Below P1 bars

            // Only draw P2 score if in two-player mode
            if (isTwoPlayerMode) {
                ctx.textAlign = 'right';
                ctx.fillText(`P2 Wins: ${player2RoundsWon}`, canvas.width - 20, 90); // Below P2 bars
            } else {
                ctx.textAlign = 'right';
                ctx.fillText(`AI: ${characters[selectedCharacterTypeP2].color.toUpperCase()}`, canvas.width - 20, 90); // Display AI character type
            }
        }

        /**
         * Draws the reflect shield for a player.
         * @param {object} player - The player object with shield properties.
         */
        function drawShield(player) {
            if (player.fireballType === 'reflect' && player.shieldActive && player.shieldCurrentAlpha > 0) {
                ctx.save();
                ctx.globalAlpha = player.shieldCurrentAlpha;
                ctx.fillStyle = characters.reflect.shieldColor;

                let shieldX = player.x;
                // Position shield based on player facing direction
                if (player.facing === 'right') {
                    shieldX = player.x + player.width - characters.reflect.shieldOffsetX;
                } else {
                    shieldX = player.x - characters.reflect.shieldWidth + characters.reflect.shieldOffsetX;
                }
                const shieldY = player.y;
                const shieldWidth = characters.reflect.shieldWidth;
                const shieldHeight = characters.reflect.shieldHeight;

                ctx.fillRect(shieldX, shieldY, shieldWidth, shieldHeight);
                ctx.restore();
            }
        }

        /**
         * Draws the round timer bar in the top middle of the canvas.
         */
        function drawTimerBar() {
            const barTotalWidth = canvas.width * 0.6; // 60% of canvas width
            const barHeight = 20;
            const barY = 20; // Position near the top

            // Calculate current width based on time remaining
            const timePercentage = Math.max(0, currentRoundTime / ROUND_TIME_LIMIT);
            const currentBarWidth = barTotalWidth * timePercentage;

            // Calculate X position to make it shrink from both sides to the center
            const barX = (canvas.width - currentBarWidth) / 2;

            // Determine color based on time remaining
            let barColor = '#28a745'; // Green
            if (currentRoundTime <= 30) {
                barColor = '#ffc107'; // Yellow
            }
            if (currentRoundTime <= 15) {
                barColor = '#dc3545'; // Red
            }

            // Draw background of the timer bar
            ctx.fillStyle = '#555';
            ctx.fillRect((canvas.width - barTotalWidth) / 2, barY, barTotalWidth, barHeight);

            // Draw the current time portion
            ctx.fillStyle = barColor;
            ctx.fillRect(barX, barY, currentBarWidth, barHeight);

            // Draw border
            ctx.strokeStyle = '#eee';
            ctx.lineWidth = 2;
            ctx.strokeRect((canvas.width - barTotalWidth) / 2, barY, barTotalWidth, barHeight);

            // Draw time text
            ctx.fillStyle = '#eee';
            ctx.font = '14px "Press Start 2P"';
            ctx.textAlign = 'center';
            ctx.fillText(`${Math.ceil(currentRoundTime)}s`, canvas.width / 2, barY + barHeight / 2 + 5);
        }


        /**
         * Checks for collision between two circles.
         * @param {object} circle1 - First circle object with x, y, radius.
         * @param {object} circle2 - Second circle object with x, y, radius.
         * @returns {boolean} True if circles are colliding, false otherwise.
         */
        function checkCircleCollision(circle1, circle2) {
            const dx = circle1.x - circle2.x;
            const dy = circle1.y - circle2.y;
            const distance = Math.sqrt(dx * dx + dy * dy);
            return distance < (circle1.radius + circle2.radius);
        }

        /**
         * Checks for collision between a circle and a rectangle.
         * @param {object} circle - Circle object with x, y, radius.
         * @param {object} rect - Rectangle object with x, y, width, height.
         * @returns {boolean} True if colliding, false otherwise.
         */
        function checkCircleRectCollision(circle, rect) {
            // Find the closest point on the rectangle to the center of the circle
            let testX = circle.x;
            let testY = circle.y;

            if (circle.x < rect.x) {
                testX = rect.x;
            } else if (circle.x > rect.x + rect.width) {
                testX = rect.x + rect.width;
            }

            if (circle.y < rect.y) {
                testY = rect.y;
            } else if (circle.y > rect.y + rect.height) {
                testY = rect.y + rect.height;
            }

            // Calculate the distance between the closest point and the circle's center
            const distX = circle.x - testX;
            const distY = circle.y - testY;
            const distance = Math.sqrt((distX * distX) + (distY * distY));

            // Check if the distance is less than the circle's radius
            return distance <= circle.radius;
        }

        /**
         * Simple AI logic for player 2.
         * @param {object} aiPlayer - The AI controlled player (player2).
         * @param {object} opponentPlayer - The human player (player1).
         * @param {number} currentTime - The current time in milliseconds.
         */
        function simpleAI(aiPlayer, opponentPlayer, currentTime) {
            // AI decision making frequency (to avoid too rapid actions)
            const AI_THINK_INTERVAL = 200; // milliseconds
            if (currentTime - (aiPlayer.lastAITime || 0) < AI_THINK_INTERVAL) {
                return;
            }
            aiPlayer.lastAITime = currentTime;

            const distanceX = Math.abs(aiPlayer.x - opponentPlayer.x);
            const distanceY = Math.abs(aiPlayer.y - opponentPlayer.y);

            // AI Jump Logic
            // If opponent is significantly higher, or if AI is stuck, try to jump
            if (aiPlayer.onGround && (opponentPlayer.y < aiPlayer.y - 50 || Math.random() < 0.01)) { // Random jump
                jump(aiPlayer);
            }

            // AI Shoot/Reflect Logic
            const canPerformAction = (currentTime - aiPlayer.lastFireballTime >= aiPlayer.currentFireballCooldown);
            const isFireballApproaching = fireballs.some(fb =>
                fb.shooter !== aiPlayer &&
                Math.abs(fb.y - aiPlayer.y) < aiPlayer.height && // Fireball roughly at player's height
                ((aiPlayer.facing === 'left' && fb.x < aiPlayer.x && fb.dx > 0) || // Fireball approaching from right
                 (aiPlayer.facing === 'right' && fb.x > aiPlayer.x && fb.dx < 0)) && // Fireball approaching from left
                Math.abs(fb.x - aiPlayer.x) < 300 // Within a certain horizontal range
            );

            if (aiPlayer.fireballType === 'reflect') {
                // Reflect character AI: Prioritize reflecting over attacking
                if (isFireballApproaching && canPerformAction) { // 'canPerformAction' here means 'canActivateShield'
                    shootFireball(aiPlayer); // This will activate the shield
                } else if (canPerformAction && Math.random() < 0.05) { // Randomly activate shield if no fireball approaching
                    // This makes the AI less predictable and sometimes preemptively shields
                    shootFireball(aiPlayer);
                }
            } else {
                // Normal character AI: Shoot if opponent is in range and not on cooldown
                if (canPerformAction && distanceX < canvas.width * 0.7 && Math.random() < 0.3) { // Shoot if opponent is within 70% of screen width
                    shootFireball(aiPlayer);
                }
            }
        }


        /**
         * Updates the game state (positions, collisions, etc.).
         */
        function update() {
            if (!gameRunning) return; // Only update game logic if game is running

            const currentTime = performance.now();
            const deltaTimeInSeconds = (currentTime - lastFrameTime) / 1000;

            // Update round timer (now applies to both modes)
            currentRoundTime -= deltaTimeInSeconds;

            // Adjust fireball cooldown and super meter growth based on timer
            [player1, player2].forEach(player => {
                if (currentRoundTime <= 15) {
                    player.currentFireballCooldown = FIREBALL_COOLDOWN_TIME * 0.1; // 10% faster
                    player.currentSuperMeterGrowthRate = SUPER_METER_GROWTH_RATE * 25; // 10% faster
                } else if (currentRoundTime <= 30) {
                    player.currentFireballCooldown = FIREBALL_COOLDOWN_TIME * 0.5; // 5% faster
                    player.currentSuperMeterGrowthRate = SUPER_METER_GROWTH_RATE * 10; // 5% faster
                } else {
                    player.currentFireballCooldown = FIREBALL_COOLDOWN_TIME; // Base rate
                    player.currentSuperMeterGrowthRate = SUPER_METER_GROWTH_RATE; // Base rate
                }
            });


            if (currentRoundTime <= 0) {
                currentRoundTime = 0;
                gameRunning = false; // Stop game logic

                let winnerMessage = "";
                if (player1.health > player2.health) {
                    player1RoundsWon++;
                    winnerMessage = "Player 1 Wins Round (Time Out)!";
                } else if (player2.health > player1.health) {
                    player2RoundsWon++;
                    winnerMessage = "Player 2 Wins Round (Time Out)!";
                } else {
                    winnerMessage = "Round Draw (Time Out)!";
                }

                if (isTwoPlayerMode) {
                    if (player1RoundsWon >= ROUNDS_TO_WIN) {
                        transitionToState('gameOver');
                        showMessage("Player 1 Wins the Game Set!", true);
                    } else if (player2RoundsWon >= ROUNDS_TO_WIN) {
                        transitionToState('gameOver');
                        showMessage("Player 2 Wins the Game Set!", true);
                    } else {
                        transitionToState('roundOver');
                        showMessage(winnerMessage);
                        setTimeout(resetRound, 2000); // Reset round after 2 seconds
                    }
                } else { // 1-Player Mode: Time out in 1-player mode
                    // In 1-player, if time runs out, it's a loss for player 1, or a draw if health is equal
                    if (player1.health > player2.health) {
                        player1RoundsWon++;
                        if (player1RoundsWon >= ROUNDS_TO_WIN) {
                            currentOpponentIndex++;
                            player1RoundsWon = 0;
                            player2RoundsWon = 0;
                            if (currentOpponentIndex < aiOpponents.length) {
                                selectedCharacterTypeP2 = aiOpponents[currentOpponentIndex];
                                transitionToState('roundOver');
                                showMessage(`You defeated ${selectedCharacterTypeP2.toUpperCase()}! Next opponent!`);
                                setTimeout(resetRound, 2000);
                            } else {
                                transitionToState('player1Win');
                                showMessage("CONGRATULATIONS! YOU WIN THE CAMPAIGN!", true);
                            }
                        } else {
                            transitionToState('roundOver');
                            showMessage("You won the round (Time Out)!");
                            setTimeout(resetRound, 2000);
                        }
                    } else { // Player 2 (AI) wins or it's a draw
                        player2RoundsWon++;
                        transitionToState('gameOver'); // Game over for player 1
                        showMessage("GAME OVER! Time Out!", true);
                    }
                }
                return; // Stop further updates for this frame if time is out
            }


            // Update player 1
            player1.dy += GRAVITY;
            player1.y += player1.dy;
            // Keep player 1 within canvas bounds
            if (player1.y + player1.height > canvas.height - 20) {
                player1.y = canvas.height - player1.height - 20;
                player1.dy = 0;
                player1.onGround = true;
                player1.isJumping = false;
            } else {
                player1.onGround = false;
            }

            // Update player 2 (manual or AI)
            player2.dy += GRAVITY;
            player2.y += player2.dy;
            // Keep player 2 within canvas bounds
            if (player2.y + player2.height > canvas.height - 20) {
                player2.y = canvas.height - player2.height - 20;
                player2.dy = 0;
                player2.onGround = true;
                player2.isJumping = false;
            } else {
                player2.onGround = false;
            }

            // If in 1-player mode, apply AI logic for player 2
            if (!isTwoPlayerMode) {
                simpleAI(player2, player1, currentTime);
            }

            // Update shield state for reflect characters
            [player1, player2].forEach(player => {
                if (player.fireballType === 'reflect' && player.shieldActive) {
                    const elapsed = currentTime - player.shieldStartTime;
                    if (elapsed >= player.shieldActiveDuration) {
                        player.shieldActive = false;
                        player.shieldCurrentAlpha = 0;
                    } else {
                        // Fade out effect
                        player.shieldCurrentAlpha = 1 - (elapsed / player.shieldActiveDuration);
                        // Drain super meter while shield is active
                        player.superMeter -= player.superMeterDrainPerSecond * deltaTimeInSeconds;
                        player.superMeter = Math.max(0, player.superMeter); // Ensure it doesn't go below 0
                        if (player.superMeter <= 0) { // Deactivate shield if meter runs out
                            player.shieldActive = false;
                            player.shieldCurrentAlpha = 0;
                        }
                    }
                }
            });


            // Update Super Meters
            [player1, player2].forEach(player => {
                // Super meter grows passively, regardless of cooldown, unless shield is active (for reflect char)
                if (!(player.fireballType === 'reflect' && player.shieldActive)) {
                    player.superMeter += player.currentSuperMeterGrowthRate; // Use dynamic growth rate
                    player.superMeter = Math.min(player.superMeter, MAX_SUPER_METER); // Cap at max
                }
                player.isSuperReady = (player.superMeter >= MAX_SUPER_METER);
            });


            // Mark fireballs for removal
            const fireballsToRemove = new Set();

            // Update fireballs and check for player collisions
            fireballs.forEach((fireball, index) => {
                fireball.x += fireball.dx;

                // Handle bouncing fireball physics
                if (fireball.fireballType === 'bouncing') {
                    fireball.dy += GRAVITY; // Apply gravity to fireball
                    fireball.y += fireball.dy;

                    // Bounce off ground
                    if (fireball.y + fireball.radius > canvas.height - 20) {
                        fireball.y = canvas.height - 20 - fireball.radius;
                        fireball.dy = FIREBALL_BOUNCE_VELOCITY; // Set to consistent upward bounce velocity
                    }
                    // Bounce off top (optional, if you want it to bounce off ceiling)
                    if (fireball.y - fireball.radius < 0) {
                        fireball.y = fireball.radius;
                        fireball.dy = -FIREBALL_BOUNCE_VELOCITY; // Bounce down from top
                    }
                } else if (fireball.fireballType === 'curving') {
                    // CURVING FIREBALL LOGIC: Apply continuous vertical acceleration for the curve
                    fireball.dy += fireball.curveAcceleration;
                    fireball.y += fireball.dy;

                    // Remove if out of vertical bounds (curving fireballs don't bounce)
                    if (fireball.y + fireball.radius < 0 || fireball.y - fireball.radius > canvas.height) {
                        fireballsToRemove.add(index);
                    }
                }


                // Fireball collision with players
                let targetPlayer = null;
                // Determine the target player based on who shot the fireball
                if (fireball.shooter === player1) {
                    targetPlayer = player2;
                } else if (fireball.shooter === player2) {
                    targetPlayer = player1;
                }

                if (targetPlayer) {
                    // REFLECT CHARACTER LOGIC: Check for reflect ability and shield collision
                    // A fireball can only be reflected if:
                    // 1. The target player is the 'reflect' character.
                    // 2. The fireball was NOT shot by the target player (cannot reflect your own).
                    // 3. The reflect shield is currently active.
                    if (targetPlayer.fireballType === 'reflect' && fireball.shooter !== targetPlayer && targetPlayer.shieldActive) {
                        // Calculate shield's current bounding box for collision
                        let shieldX = targetPlayer.x;
                        if (targetPlayer.facing === 'right') {
                            shieldX = targetPlayer.x + targetPlayer.width - characters.reflect.shieldOffsetX;
                        } else {
                            shieldX = targetPlayer.x - characters.reflect.shieldWidth + characters.reflect.shieldOffsetX;
                        }
                        const shieldY = targetPlayer.y;
                        const shieldWidth = characters.reflect.shieldWidth;
                        const shieldHeight = characters.reflect.shieldHeight;

                        const shieldRect = { x: shieldX, y: shieldY, width: shieldWidth, height: shieldHeight };

                        // Check fireball collision with shield
                        if (checkCircleRectCollision(fireball, shieldRect)) {
                            // Determine if it's a super reflect
                            let speedMult = targetPlayer.reflectSpeedMultiplier;
                            let sizeMult = targetPlayer.reflectSizeMultiplier;
                            let damageMult = targetPlayer.reflectDamageMultiplier;
                            let newStrength = fireball.strength * 1.2; // Increase strength on reflect

                            if (targetPlayer.isSuperReady) {
                                speedMult = targetPlayer.superReflectSpeedMultiplier;
                                sizeMult = targetPlayer.superReflectSizeMultiplier;
                                damageMult = targetPlayer.superReflectDamageMultiplier;
                                newStrength = targetPlayer.superReflectFireballStrength; // Set to a high fixed strength for super reflected
                                targetPlayer.superMeter = 0; // Reset super meter after super reflect
                                targetPlayer.isSuperReady = false;
                            }

                            // Apply reflection properties
                            fireball.dx *= -speedMult; // Reverse direction and increase speed
                            fireball.radius *= sizeMult; // Increase size
                            fireball.damage *= damageMult; // Apply damage multiplier
                            fireball.shooter = targetPlayer; // Fireball now belongs to the reflector
                            fireball.color = targetPlayer.fireballColor; // Change color to reflector's fireball color
                            fireball.fireballType = 'standard'; // Reflected fireballs behave like standard ones (no more bounce/curve)
                            fireball.dy = 0; // Reset vertical velocity for reflected fireball to simplify trajectory
                            fireball.strength = newStrength; // Update strength

                            targetPlayer.superMeter = Math.min(MAX_SUPER_METER, targetPlayer.superMeter + 20); // Increase super meter on successful reflect (e.g., +20)

                            // Deactivate shield immediately on successful reflect
                            targetPlayer.shieldActive = false;
                            targetPlayer.shieldCurrentAlpha = 0;

                            // Do NOT add to fireballsToRemove, as it's reflected
                            return; // Skip normal damage application for this fireball
                        }
                    }

                    // Normal collision (if not reflected by shield, or reflect on cooldown, or not a reflect character)
                    if (fireball.x < targetPlayer.x + targetPlayer.width &&
                        fireball.x + fireball.radius > targetPlayer.x &&
                        fireball.y < targetPlayer.y + targetPlayer.height &&
                        fireball.y + fireball.radius > targetPlayer.y
                    ) {
                        targetPlayer.health -= fireball.damage;
                        targetPlayer.lastHitTime = currentTime; // Update last hit time
                        if (targetPlayer.health < 0) targetPlayer.health = 0;
                        fireballsToRemove.add(index); // Mark for removal
                    }
                }

                // Remove fireball if out of horizontal bounds (for standard, bouncing, and curving fireballs)
                if ((fireball.x + fireball.radius < 0 || fireball.x - fireball.radius > canvas.width)) {
                    fireballsToRemove.add(index); // Mark for removal
                }
            });

            // Check for fireball-fireball collisions (only if two players are involved)
            // This applies to 2-player mode, or 1-player mode if AI shoots and player shoots
            for (let i = 0; i < fireballs.length; i++) {
                for (let j = i + 1; j < fireballs.length; j++) {
                    const fb1 = fireballs[i];
                    const fb2 = fireballs[j];

                    // Only check collision if they are from different shooters
                    if (fb1.shooter !== fb2.shooter) {
                        if (checkCircleCollision(fb1, fb2)) {
                            // Implement strength-based collision logic
                            if (fb1.strength > fb2.strength) {
                                fireballsToRemove.add(j); // fb1 stronger, fb2 removed
                                fb1.strength -= fb2.strength; // Reduce strength
                            } else if (fb2.strength > fb1.strength) {
                                fireballsToRemove.add(i); // fb2 stronger, fb1 removed
                                fb2.strength -= fb1.strength; // Reduce strength
                            } else {
                                // Strengths are equal or both are super: remove both
                                fireballsToRemove.add(i);
                                fireballsToRemove.add(j);
                            }
                        }
                    }
                }
            }


            // Filter out fireballs marked for removal
            fireballs = fireballs.filter((_, index) => !fireballsToRemove.has(index));


            // Check round end condition
            if (player1.health <= 0) {
                gameRunning = false; // Pause game logic
                player2RoundsWon++; // AI wins a round or Player 2 wins a round

                if (isTwoPlayerMode) {
                    if (player2RoundsWon >= ROUNDS_TO_WIN) {
                        transitionToState('gameOver'); // Transition to game over state
                        showMessage("Player 2 Wins the Game Set!", true); // True to show restart button
                    } else {
                        transitionToState('roundOver'); // New state for round end
                        showMessage("Player 2 Wins Round!");
                        setTimeout(resetRound, 2000); // Reset round after 2 seconds
                    }
                } else { // 1-Player Mode: Player 1 lost
                    transitionToState('gameOver');
                    showMessage("GAME OVER! You were defeated!", true); // Show restart button
                }
            } else if (player2.health <= 0) {
                gameRunning = false; // Pause game logic
                player1RoundsWon++; // Player 1 wins a round

                if (isTwoPlayerMode) {
                    if (player1RoundsWon >= ROUNDS_TO_WIN) {
                        transitionToState('gameOver'); // Transition to game over state
                        showMessage("Player 1 Wins the Game Set!", true); // True to show restart button
                    } else {
                        transitionToState('roundOver'); // New state for round end
                        showMessage("Player 1 Wins Round!");
                        setTimeout(resetRound, 2000); // Reset round after 2 seconds
                    }
                } else { // 1-Player Mode: AI defeated
                    if (player1RoundsWon >= ROUNDS_TO_WIN) {
                        // Player 1 won the set against the current AI opponent
                        currentOpponentIndex++; // Move to the next AI opponent
                        player1RoundsWon = 0; // Reset rounds for the new set
                        player2RoundsWon = 0; // Reset AI rounds for the new set

                        if (currentOpponentIndex < aiOpponents.length) {
                            // More opponents left, transition to next opponent
                            selectedCharacterTypeP2 = aiOpponents[currentOpponentIndex];
                            transitionToState('roundOver'); // Use roundOver state to show message
                            showMessage(`You defeated ${selectedCharacterTypeP2.toUpperCase()}! Next opponent!`);
                            setTimeout(resetRound, 2000); // Reset round with new AI
                        } else {
                            // All opponents defeated! Player 1 wins the campaign
                            transitionToState('player1Win');
                            showMessage("CONGRATULATIONS! YOU WIN THE CAMPAIGN!", true); // Show restart button
                        }
                    } else {
                        // Player 1 won the round, but not the set yet
                        transitionToState('roundOver');
                        showMessage("You won the round!");
                        setTimeout(resetRound, 2000); // Reset round to continue against current AI
                    }
                }
            }
        }

        /**
         * Renders all game elements on the canvas.
         */
        function render(currentTime) {
            ctx.clearRect(0, 0, canvas.width, canvas.height); // Clear canvas

            // Only draw game elements if in 'playing', 'roundOver', 'gameOver', or 'player1Win' state
            if (gameState === 'playing' || gameState === 'roundOver' || gameState === 'gameOver' || gameState === 'player1Win') {
                // Draw ground
                ctx.fillStyle = '#663300'; // Brown
                ctx.fillRect(0, canvas.height - 20, canvas.width, 20);

                // Draw players
                drawPlayer(player1, currentTime);
                drawPlayer(player2, currentTime); // Always draw player 2, even if AI

                // Draw fireballs
                fireballs.forEach(drawFireball);

                // Draw health bars
                drawHealthBar(player1, 20, 20);
                drawHealthBar(player2, canvas.width - 20 - 150, 20); // Right aligned

                // Draw cooldown bars
                drawCooldownBar(player1, 20, 45, currentTime); // Below health bar
                drawCooldownBar(player2, canvas.width - 20 - 150, 45, currentTime); // Below health bar

                // Draw super meters
                drawSuperMeter(player1, 20, 60); // Below cooldown bar
                drawSuperMeter(player2, canvas.width - 20 - 150, 60); // Below cooldown bar

                // Draw round scores
                drawRoundScores();
                // Draw timer bar (now applies to both modes)
                drawTimerBar();

                // Draw shields for reflect characters
                drawShield(player1);
                drawShield(player2);
            }
        }

        /**
         * The main game loop.
         */
        function gameLoop(currentTime) {
            requestAnimationFrame(gameLoop); // Always request the next frame

            // Only update game logic if in 'playing' state
            if (gameState === 'playing') {
                const deltaTime = currentTime - lastFrameTime;
                if (deltaTime >= frameRate) {
                    update();
                    lastFrameTime = currentTime;
                }
            }
            render(currentTime); // Always render based on current state
        }

        /**
         * Player jumps.
         * @param {object} player - The player object that is jumping.
         */
        function jump(player) {
            if (player.onGround) {
                player.dy = JUMP_VELOCITY;
                player.onGround = false;
                player.isJumping = true;
            }
        }

        /**
         * Player shoots a fireball or activates reflect.
         * @param {object} shooter - The player object.
         */
        function shootFireball(shooter) {
            const currentTime = performance.now();

            // If it's a reflect character, activate shield or shoot super fireball
            if (shooter.fireballType === 'reflect') {
                if (shooter.isSuperReady) {
                    // Shoot Super Fireball (large, slow, high strength)
                    fireballs.push({
                        x: shooter.facing === 'right' ? shooter.x + shooter.width : shooter.x,
                        y: shooter.y + shooter.height / 2,
                        radius: SUPER_FIREBALL_RADIUS * 1.5, // Even larger
                        color: SUPER_FIREBALL_COLOR,
                        dx: shooter.facing === 'right' ? FIREBALL_SPEED * 0.5 : -FIREBALL_SPEED * 0.5, // Slower
                        dy: 0,
                        shooter: shooter,
                        damage: SUPER_FIREBALL_DAMAGE * 1.5, // More damage
                        isSuper: true,
                        fireballType: 'standard', // Super is always standard
                        strength: characters.reflect.superReflectFireballStrength // Use defined super strength
                    });
                    shooter.superMeter = 0; // Reset super meter after use
                    shooter.isSuperReady = false;
                    shooter.lastFireballTime = currentTime; // Apply cooldown after super
                } else {
                    // Activate shield (normal reflect ability)
                    if (currentTime - shooter.lastFireballTime < shooter.reflectCooldownTime) {
                        return; // Still on cooldown
                    }
                    shooter.lastFireballTime = currentTime; // Start reflect cooldown
                    shooter.shieldActive = true;
                    shooter.shieldStartTime = currentTime;
                    shooter.shieldCurrentAlpha = 1; // Start fully opaque
                    // Gain a small amount of super meter just for activating the shield
                    shooter.superMeter = Math.min(MAX_SUPER_METER, shooter.superMeter + (shooter.superMeterGainOnActivate || 0));
                }
                return; // Return early for reflect character
            }

            // CURVING FIREBALL LOGIC: Determine initial vertical velocity and curve acceleration
            let initialDy = 0;
            let curveAcc = 0;
            if (shooter.fireballType === 'curving') {
                const curvingCharProps = characters.curving;
                if (shooter.onGround) {
                    initialDy = curvingCharProps.initialUpwardCurveDy;
                    curveAcc = curvingCharProps.upwardCurveAcceleration;
                } else { // In air
                    initialDy = curvingCharProps.initialDownwardCurveDy;
                    curveAcc = curvingCharProps.downwardCurveAcceleration;
                }
            }

            // Check if super fireball is ready for non-reflect characters
            if (shooter.isSuperReady) {
                fireballs.push({
                    x: shooter.facing === 'right' ? shooter.x + shooter.width : shooter.x,
                    y: shooter.y + shooter.height / 2,
                    radius: SUPER_FIREBALL_RADIUS,
                    color: SUPER_FIREBALL_COLOR,
                    dx: shooter.facing === 'right' ? shooter.fireballSpeed * 1.2 : -shooter.fireballSpeed * 1.2, // Use character's speed
                    dy: (shooter.fireballType === 'bouncing') ? FIREBALL_BOUNCE_VELOCITY : initialDy, // Initial vertical velocity for bouncing or curving
                    shooter: shooter,
                    damage: SUPER_FIREBALL_DAMAGE,
                    isSuper: true, // Mark as super fireball
                    fireballType: shooter.fireballType, // Inherit fireball type
                    bounceFactor: characters[shooter.fireballType].bounceFactor, // Inherit bounce factor
                    curveAcceleration: curveAcc, // Pass curve acceleration for curving fireballs
                    strength: MAX_SUPER_METER // Super fireballs have max strength
                });
                shooter.superMeter = 0; // Reset super meter after use
                shooter.isSuperReady = false; // Reset super ready flag
            } else {
                // Normal fireball logic
                // Use player's dynamic cooldown for check
                if (currentTime - shooter.lastFireballTime < shooter.currentFireballCooldown) {
                    return; // Still on cooldown
                }

                fireballs.push({
                    x: shooter.facing === 'right' ? shooter.x + shooter.width : shooter.x,
                    y: shooter.y + shooter.height / 2,
                    radius: shooter.fireballRadius, // Use character's radius
                    color: shooter.fireballColor, // Use character's color
                    dx: shooter.facing === 'right' ? shooter.fireballSpeed : -shooter.fireballSpeed, // Use character's speed
                    dy: (shooter.fireballType === 'bouncing') ? FIREBALL_BOUNCE_VELOCITY : initialDy, // Initial vertical velocity for bouncing or curving
                    shooter: shooter,
                    damage: shooter.fireballDamage, // Use character's damage
                    isSuper: false, // Mark as normal fireball
                    fireballType: shooter.fireballType, // Inherit fireball type
                    bounceFactor: characters[shooter.fireballType].bounceFactor, // Inherit bounce factor
                    curveAcceleration: curveAcc, // Pass curve acceleration for curving fireballs
                    strength: shooter.fireballStrength // Use character's base strength
                });
            }
            shooter.lastFireballTime = currentTime; // Update last shot time for cooldown
            // Super meter is only reset when super is used or when any fireball is shot by *non-reflect* characters
            if (shooter.fireballType !== 'reflect') {
                shooter.superMeter = 0;
            }
        }

        /**
         * Displays a message box with game status (e.g., winner).
         * @param {string} message - The message to display.
         * @param {boolean} showRestartBtn - Whether to show the restart button.
         */
        function showMessage(message, showRestartBtn = false) {
            messageText.textContent = message;
            messageBox.style.display = 'flex';
            if (showRestartBtn) {
                restartButton.style.display = 'block';
            } else {
                restartButton.style.display = 'none';
            }
        }

        /**
         * Event listener for keyboard input.
         */
        document.addEventListener('keydown', (e) => {
            if (gameState !== 'playing') return; // Only allow input during playing state

            switch (e.key) {
                case 'a': // Player 1 Jump
                case 'A':
                    jump(player1);
                    break;
                case 's': // Player 1 Fireball/Reflect
                case 'S':
                    shootFireball(player1);
                    break;
                case 'k': // Player 2 Jump (only if 2-player mode)
                case 'K':
                    if (isTwoPlayerMode) jump(player2);
                    break;
                case 'l': // Player 2 Fireball/Reflect (only if 2-player mode)
                case 'L':
                    if (isTwoPlayerMode) shootFireball(player2);
                    break;
            }
        });

        // Event listeners for button clicks
        player1JumpBtn.addEventListener('click', () => {
            if (gameState === 'playing') jump(player1);
        });
        player1FireBtn.addEventListener('click', () => {
            if (gameState === 'playing') shootFireball(player1);
        });
        player2JumpBtn.addEventListener('click', () => {
            if (gameState === 'playing' && isTwoPlayerMode) jump(player2);
        });
        player2FireBtn.addEventListener('click', () => {
            if (gameState === 'playing' && isTwoPlayerMode) shootFireball(player2);
        });

        // Restart button functionality
        restartButton.addEventListener('click', () => {
            transitionToState('menu'); // Go back to main menu
            gameRunning = false; // Ensure game logic is paused
            selectedCharacterTypeP1 = null; // Reset selections
            selectedCharacterTypeP2 = null;
            currentOpponentIndex = 0; // Reset AI progression
            player1RoundsWon = 0; // Reset round wins
            player2RoundsWon = 0; // Reset round wins
        });

        // Menu button listeners
        onePlayerBtn.addEventListener('click', () => {
            isTwoPlayerMode = false;
            transitionToState('charSelectP1');
        });

        twoPlayerBtn.addEventListener('click', () => {
            isTwoPlayerMode = true;
            transitionToState('charSelectP1');
        });

        // Character Selection button listeners
        charStandardBtn.addEventListener('click', () => {
            if (gameState === 'charSelectP1') {
                selectedCharacterTypeP1 = 'standard';
                if (isTwoPlayerMode) {
                    transitionToState('charSelectP2');
                } else {
                    initializeGame(); // Initialize game for 1-player mode
                    transitionToState('playing');
                }
            } else if (gameState === 'charSelectP2') {
                selectedCharacterTypeP2 = 'standard';
                initializeGame(); // Initialize game for 2-player mode
                transitionToState('playing');
            }
        });

        charBouncingBtn.addEventListener('click', () => {
            if (gameState === 'charSelectP1') {
                selectedCharacterTypeP1 = 'bouncing';
                if (isTwoPlayerMode) {
                    transitionToState('charSelectP2');
                } else {
                    initializeGame(); // Initialize game for 1-player mode
                    transitionToState('playing');
                }
            } else if (gameState === 'charSelectP2') {
                selectedCharacterTypeP2 = 'bouncing';
                initializeGame(); // Initialize game for 2-player mode
                transitionToState('playing');
            }
        });

        // Curving Character button listener
        charCurvingBtn.addEventListener('click', () => {
            if (gameState === 'charSelectP1') {
                selectedCharacterTypeP1 = 'curving';
                if (isTwoPlayerMode) {
                    transitionToState('charSelectP2');
                } else {
                    initializeGame(); // Initialize game for 1-player mode
                    transitionToState('playing');
                }
            } else if (gameState === 'charSelectP2') {
                selectedCharacterTypeP2 = 'curving';
                initializeGame(); // Initialize game for 2-player mode
                transitionToState('playing');
            }
        });

        // Reflect Character button listener
        charReflectBtn.addEventListener('click', () => {
            if (gameState === 'charSelectP1') {
                selectedCharacterTypeP1 = 'reflect';
                if (isTwoPlayerMode) {
                    transitionToState('charSelectP2');
                } else {
                    initializeGame(); // Initialize game for 1-player mode
                    transitionToState('playing');
                }
            } else if (gameState === 'charSelectP2') {
                selectedCharacterTypeP2 = 'reflect';
                initializeGame(); // Initialize game for 2-player mode
                transitionToState('playing');
            }
        });


        // Handle canvas resizing for responsiveness
        function resizeCanvas() {
            const gameContainer = document.getElementById('game-container');
            let containerWidth;

            // Temporarily make gameContainer visible if hidden to get correct dimensions
            const wasHidden = gameContainer.style.display === 'none';
            if (wasHidden) {
                gameContainer.style.display = 'flex';
            }

            containerWidth = gameContainer.clientWidth - 40; // Account for padding

            if (wasHidden) {
                gameContainer.style.display = 'none'; // Hide it again if it was originally hidden
            }

            const aspectRatio = 16 / 9;
            canvas.width = Math.min(containerWidth, 1000); // Max width of 1000px
            canvas.height = canvas.width / aspectRatio;

            // Re-position players and elements if game is running or just starting
            if (gameRunning || gameState === 'playing' || gameState === 'roundOver' || gameState === 'gameOver' || gameState === 'player1Win') {
                // Only reset round if the game is not completely over and we are in a game state
                if (gameState !== 'gameOver' && gameState !== 'player1Win') {
                    resetRound(); // Reset round to re-position players based on new canvas size
                } else {
                    // If game is over or player 1 wins, just re-render to adjust elements without resetting state
                    render(performance.now());
                }
            }
            // Always render on resize to adjust elements
            render(performance.now());
        }

        // Listen for window resize events
        window.addEventListener('resize', resizeCanvas);

        // Initial setup
        window.onload = function() {
            transitionToState('menu'); // Start at the main menu
            requestAnimationFrame(gameLoop); // Start the game loop
        };
    </script>
</body>
</html>
