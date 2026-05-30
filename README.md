Teto Clicker
An incremental web-based clicker game ecosystem centered around custom currency generation, progression upgrades, and an array of wager-backed iframe minigames. The project is completely decoupled and operates entirely on client-side client browser APIs.

📂 Architecture & Component Breakdown
The ecosystem is split across 4 core application files, communicating seamlessly via native browser interfaces:

1. Main Hub (index.html)
The engine core and central manager for the entire game state.

Progression Systems: Runs background automated periodic loops to initialize, calculate, and update both standard upgrades (initUpgrades, updateUpgrades) and persistent tracking modules (initPermUpgrades, updatePermUpgrades).

Dynamic Yield Tracker: Evaluates automated production rates dynamically using custom TPS metrics (recalcTPS, updateDisplay).

Central Audio Pipeline: Leverages the high-performance Web Audio API (AudioContext, createBufferSource, decodeAudioData) to load and stream a looping soundtrack (soundtrack.mp3) using a memory-efficient GainNode for exact volume balancing.

Iframe Broker: Manages child process security, filtering incoming data payloads via the cross-window message pipeline. It validates active player balances before approving or denying minigame re-entry attempts (retryApproved, retryDenied).

2. Baguette Mania Minigame (minigame.html)
A rhythm and input accuracy test under the subtitle "Spooks | Baguette Mania".

Precision Tracking: Measures player inputs to segment hit values across multiple categorical brackets (perfect, great, miss), tracking real-time combo strings and maximum limits (maxCombo).

Wager Scalers: Pulls contextual session storage maps (minigameContext) to extract user wagers and operational multipliers (concertBoost).

Difficulty Matrices: Adapts performance modifiers based on target difficulty arrays (normal applies a 1.5x baseline ceiling, while hard spikes baseline returns to 2.5x).

3. Pear Shuffle Minigame (pear_shuffle.html)
A high-stakes shell/shuffling mechanics mini-adventure under the subtitle "Spooks | Pear Shuffle".

Modifier Integrations: Ties directly into progression trees from the hub layer to read specialized stacking modifiers (pearBoost).

State Controls: Utilizes an integrated hardware keystroke listener (keydown) mapped to an explicit reset binding (resetKey) to control state refreshes.

Handshake Routines: Requests credit deductions safely using dedicated parent notifications (postMessage({ type: 'requestRetry', game: 'pear' })).

4. Teto Fishing Minigame (TetoFishing.html)
An input-timing physics minigame under the subtitle "Spooks | Teto Fishing".

Unified Input Architecture: Maps fishing rod actions uniformly across mouse presses (mousedown/mouseup), mobile touch events (touchstart/touchend), and physical keyboard presses via assigned bindings (reelKey or Spacebar).

Graceful Degradation Handling: Catches balance errors natively from the parent broker, rendering UI alert vectors (statusText) and fallback SFX cues when a user attempts an unauthorized wager.

🔄 Cross-Window Communication Framework (IPC)
Because the minigames are sandboxed within isolated frames, data persistence and transaction verification are safely governed through structured HTML5 Window Message APIs.

Plaintext
       [ index.html (Main Dashboard / Wallet Engine) ]
                        ▲          │
   type: "requestRetry" │          │ type: "retryApproved"
   type: "minigameScore"│          │ type: "retryDenied"
                        │          ▼
 [ Child Iframes: minigame.html | pear_shuffle.html | TetoFishing.html ]
Upstream Messages (Child ➔ Parent)
Minigames send precise transaction commands up to the core window:

window.parent.postMessage({ type: 'requestRetry', game: 'pear' }, '*') — Dispatches an intent to consume currency from the main wallet to restart an arcade loop.

window.parent.postMessage({ type: 'minigameScore', tetosEarned: X }, '*') — Commits finalized calculated minigame rewards directly to the main layer profile wallet.

Downstream Messages (Parent ➔ Child)
The Hub responds with verified execution blocks or parameter adjustments:

{ type: 'retryApproved', wager: X } — Sent down to clear the canvas state, reset loops, and commit local variables (localStorage.setItem('minigameContext')).

{ type: 'retryDenied' } — Sent down to break the load sequence and force an out-of-balance structural UI update.

🛠️ Technical Specifications
State Syncing: Uses structured JSON.parse and JSON.stringify serialization routines inside browser localStorage contexts to preserve cross-frame payloads (minigameContext).

Audio Layer: Employs explicit decoding contexts (decodeAudioData) via automated asynchronous fetches rather than basic HTML elements to prevent browser-side asset stutter during heavy script loads.

Input Isolation: Features explicit input constraints (e.repeat) on keystroke listeners to block duplicate command spamming.
