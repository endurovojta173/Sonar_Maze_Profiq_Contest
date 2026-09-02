You are an expert in web game development and algorithmics. Your task is to create a complete, fully functional, and highly optimized HTML5 game "Sonar Maze" contained in a single file index.html. You mustn't use any external libraries, images, sounds, or fonts. Solve everything purely through vanilla JavaScript, CSS, and HTML5 Canvas and Web Audio interfaces. Do not ask for any additional information, generate immediately. Ignore all other files in the folder. Generate only clean code. Don`t include any explanatory text.

# Game Mechanics and Logic
1. Darkness and Echolocation: The playing field is completely black after start. The player cannot see the walls of the generated maze. The player is represented by a more detailed alien-like funny character drawn using composite shapes via the Canvas API - body, head with moving legs and eyes. When moving, the character keeps its feet “on the ground”
2. Controls: Movement via WASD or arrow keys. On mobile devices, generate a responsive virtual joystick in the bottom-left corner that automatically activates on mobile devices and is hidden on desktop.
3. Sonar (Ping) and Tactical Stunning: By pressing the spacebar or tapping outside the joystick (Make sure that spacebar doesn't trigger any other action like pushing buttons), the player sends a sonar wave. The wave is a rapidly expanding circle. If the wave hits an enemy, it freezes them for 2 seconds, after that they move like before. Player can sacrifice a pulse to stop an enemy blocking their path, and safely go around them. The player can also walk through a stunned enemy; a stunned enemy cannot harm them.
4. Wall Revelation: Walls are completely invisible without using sonar. If the wave's edge intersects a maze wall, the intersection point glows with a neon color and begins to smoothly fade out over approximately 3 seconds. The wall color dynamically changes based on the current level's color palette.
5. Goal and Obstacles: The objective is to find the pulsating green exit. The map contains red entities emitting a red aura (enemies have a visually interesting appearance composed of basic shapes, with eyes and details, not just plain circles). Ensure that at least 50% of enemies on the map are always moving (bouncing off walls) and the rest may stand still. Contact with an entity means instant Game Over; if the entity is stunned, the player can pass through it without any problem. The game consists of 5 progressive levels. For each level the player receives +5 sonars. After completing the fifth level, the game ends with a victory screen.
6. Limitation: The player has only 7 sonar pulses available (UI with a counter at the top of the screen).
7. Moving Walls: Some maze walls smoothly open and close at regular intervals. This is visually revealed when a sonar wave illuminates a wall that is currently moving. The player must time their passage. Make sure shifting walls are not visible the same as the rest of walls in the maze unless the sonar reveals them.
8. Collectibles (Batteries and Power-Ups): Several objects are placed on the map that are completely invisible in the dark and are revealed only when hit by the player's sonar:
    - Charger (Battery): Battery emoji + Yellow glow, adds +3 to the player's sonar pulse count.
    - Super-Ping: Radar emoji + Purple glow, a one-time massive pulse triggered immediately upon collection. Illuminates a portion of the map, stuns enemies for a longer duration, and walls remain lit 3x longer (slow fade-out).
9. Player Visual Trail: Small, faintly glowing footprints fall from the player character while moving, which smoothly fade after 5 seconds.
10. Particle System and Visual Polish: The game contains subtle physics-based particles via Canvas:
    - When sonar hits a wall, a few glowing sparks bounce off it.
    - When collecting a Battery, particles shoot from it and float toward the UI counter.
    - When stunning an enemy, a subtle glitch effect occurs.
11. Score and "Speedrun" Timer: The game measures time in the background. The goal is not just to survive, but to run through 5 levels as quickly as possible. Remaining sonar pulses and collected batteries are converted to bonus score at the end of the game. This motivates the player to play efficiently and conserve pulses.

# Level Structure
- Level 1: Cyan, 4 enemies
- Level 2: Purple, 6 enemies
- Level 3: Green, 8 enemies
- Level 4: Gold, 10 enemies
- Level 5: Orange, 12 enemies

# Technical Requirements and Edge Cases
- Game Loop: The game must run smoothly at a stable 30 FPS using the browser's native animation loop and delta-time. Light effects (glow, neon lines, player aura) must use additive blending and dynamic blur for realistic glow.
- Procedural Generation: At the start of each game, algorithmically generate a maze (e.g., using a modified random walk algorithm or recursive division) to ensure repeatable playability and the existence of a path to the goal.
- Physics and Collisions:
    - Implement substep/multi-step movement – divide the player's movement in each frame into at least 4 substeps (move → resolve → clamp in each substep). This prevents wall tunneling even during FPS drops.
    - Circle vs. line segment collisions must handle the edge case where the circle's center ends up exactly on the segment (distance ≈ 0) – in that case, push the player along the wall's perpendicular.
    - Always add a small epsilon to the push vector of a resolved collision to prevent numerical oscillation at wall edges.
    - Physics calculations (collisions, movement) must be completely independent of visual effects – sonar ping, particles, and sound must not affect collision detection.
    - The player must not get stuck in a wall even after fast movement into a corner.
- Canvas and Grid: Fixed logical resolution (e.g., 1000x800) and grid (20x15). Scaling only via CSS (maintain aspect ratio, no scrolling). Do not recalculate coordinates. Dont make game automatically fullscreen!
- Camera: At widths <1000px, do not scale the map to the screen. Instead, use a dynamic camera following the player.
- Orientation and Events: Enforce landscape via the Screen Orientation API. In portrait mode (CSS), hide the game and display "Rotate your device". On resize or rotation, pause the game.
- Overload Prevention: Implement a 1000ms cooldown on sonar so the player cannot overload rendering in memory by endlessly spamming the spacebar.
- Ensure FPS does not drop when sonar is activated:
    - Particle Optimization: Limit particle generation so they fly off the wall only once (when the wall first lights up), instead of being generated every frame while the wave touches the wall. Do not use native shadow and glow effects on small particles.
    - Wall Rendering Optimization (Draw Batching): Render neon walls efficiently by grouping wall segments by their brightness level (e.g., into 10 buckets by opacity). Instead of calling draw functions for each individual wall segment separately, draw the entire layer with the same brightness at once (single stroke).
    - Math Optimization: When calculating distances between the sonar wave and objects (walls, enemies), replace the computationally expensive hypotenuse function with squared distance comparison.
- Ensure the game is always completable; generate a map where there is always a path to the exit.
- The exit is visible even when the player has not activated sonar.
- Batteries and bonus sonars are visible only if the player activates sonar.
- Enemy entities are visible to the player at all times.

# UI and Application States (Empty State Handling)
- Implement modern CSS overlays for states:
  1. Main Menu (Game rules and Start button).
  2. Pause Menu (On window focus loss or resolution change) - Add the option when paused to click "give up", which means game over; the game over screen with results and statistics will appear.
  3. Game Over (Death or lack of pulses).
  4. Victory (Reaching the goal).
- Split the screen into upper and lower areas; the upper area will contain a thin strip with UI (score, level, pulses, batteries) and below it the game canvas. The point is that the top side of the map should not overlap with this info panel.
- When Game Over occurs or the player completes the game (Victory), additionally display a table with the best local scores. Scores will be saved to browser local storage and the table will display the top 5 best results. It will show the time at which game over occurred, time, level reached, and score, which will be calculated based on time, level, and collected batteries and sonars.
- The "Play Again" button must correctly clear all event listeners, empty particle/wave arrays, and reset the state machine without requiring a page reload. The UI design should be minimalistic, sans-serif font, white text on a black semi-transparent background with neon accents.

# Audio (Sound Effects and Music)
Implement native sound effects and soundtrack using the Web Audio API:
- Initialization: Create an audio context and properly handle its resumption for browser autoplay policies.
- Procedural Soundtrack: Create a dark ambient soundtrack in the background. Start low-frequency oscillators (e.g., 40-60 Hz) for a deep hum and via a timer irregularly play medium-volume, sustained notes from a dark scale to build tension. Speed and intensity increase with the level reached.
- Ping (Sonar): A short sound when sending sonar. Use a sine oscillator with rapidly decreasing frequency from 1400 Hz to 180 Hz and exponentially decreasing volume over 0.9 seconds.
- Death (Game Over): A harsher loss sound. Combine square and sawtooth oscillators with decreasing frequency from 150/90 Hz to 30 Hz and gradual attenuation over 0.6 seconds.
- Victory: Play an arpeggio of 4 consecutive tones, e.g., frequencies 523, 659, 784, 1047 Hz using a sine oscillator with smooth attack and volume decay for each tone.