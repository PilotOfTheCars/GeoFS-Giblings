# Giblings

**Get out of the cockpit.**

Giblings is a human-presence layer for **GeoFS 3.9**. It turns the simulator world into somewhere you can actually walk around, meet other Giblings, board another player's aircraft as a passenger, talk over proximity voice, and — if you're feeling brave — try experimental aircraft marshalling.

The aircraft simulation stays GeoFS. **Giblings adds the people.**

> [!WARNING]
> **Marshalling is VERY EARLY ACCESS. Bugs will be present.** Signal recognition is experimental and informational only. It never controls an aircraft.

## What can it do?

- **Leave the aircraft and walk around** in first person with terrain-following movement and sprinting.
- **Become a Gibling** — a deliberately simple floating-head-and-body avatar with moving eyes and one tracked ball per arm.
- **Use webcam head + arm tracking** so your Gibling can mirror your real movement. Webcam frames stay local.
- **See other Giblings** through a separate Giblings-to-Giblings multiplayer presence layer.
- **Board nearby aircraft as a passenger** while the actual pilot keeps complete control.
- **Text chat + proximity voice chat** with spatial positioning and distance falloff.
- **Use your arms without marshalling enabled** whenever webcam tracking is active.
- **Try aircraft marshalling** with tracked arms, two wands and experimental gesture recognition.
- **Panic out safely** if something goes wrong without intentionally taking over GeoFS aircraft controls.

Giblings networking is separate from GeoFS native multiplayer; it does not write Giblings state into GeoFS multiplayer packets.

## Requirements

- **GeoFS 3.9** — this release intentionally targets 3.9 only.
- A userscript manager such as Tampermonkey.
- A modern Chromium-based browser is recommended.
- Camera permission only if you want head/arm tracking.
- Microphone permission only if you want proximity voice.
- Internet access for Giblings P2P networking and for loading tracking libraries/models when tracking is enabled.

## Installation

1. Disable older Giblings alpha/development scripts. Running two versions at once is a very effective way to create possessed Giblings.
2. Open your userscript manager and create a new userscript.
3. Paste the complete contents of `Giblings_1.0.2_Public.user.js` and save it.
4. Open/reload **GeoFS 3.9**.
5. The Giblings panel should appear in the upper-right area of the simulator.

## Controls

| Command | Action |
|---|---|
| `Alt + G` | Enter ground mode / return to your aircraft |
| `W A S D` | Walk |
| `Shift` | Sprint |
| `Mouse` | Look while pointer lock is active |
| `E` | Board nearest supported aircraft / exit passenger mode near the ground |
| `Alt + H` | Toggle webcam head + arm tracking |
| `Alt + R` | Recenter head tracking |
| `Alt + M` | Toggle **VERY EARLY ACCESS** marshalling |
| `Alt + N` | Join/leave the Giblings public P2P network |
| `V` | Push-to-talk when PTT voice is enabled |
| `F9` | Diagnostics |
| `Alt + X` | Panic stop Giblings |
| `F8` | Emergency panic stop; reload GeoFS to re-arm |

## Walking around

`Alt + G` separates your Gibling position from your aircraft. The aircraft stays where it is while you get an independent ground position and first-person camera. Walking follows terrain height, and the normal movement keys are isolated so walking does not remotely steer your aircraft.

Forward movement can follow your final view direction, including tracked head yaw. A subtle local camera head-bob is added while walking and sprinting without changing your authoritative multiplayer position.

## The Gibling

The public Gibling is intentionally recognizable and intentionally ridiculous:

- one large opaque body sphere
- one smaller floating head sphere
- two black eyes
- **one sphere per arm**
- a player colour generated from the available GeoFS multiplayer ID, with a persistent Giblings ID as fallback

Your own body and head stay hidden in first person, while your arm balls remain visible so you can see what you're doing.

## Head + arm tracking

Press `Alt + H` to enable tracking. Giblings loads MediaPipe tracking after permission is granted.

Face tracking drives local head yaw, pitch and roll, and the Gibling's eyes respond to head direction. Pose tracking estimates shoulders, elbows, wrists and hand direction, then turns that into compact arm coordinates for the two visible arm balls.

**Tracking your arms does not require marshalling mode.** `Alt + M` only adds the marshalling wands and recognition layer.

Webcam frames remain on your device. Giblings only needs compact pose values for remote animation when you're online.

## Passenger mode

Walk close to an aircraft and press `E`.

With your own aircraft, Giblings returns you normally. With a supported remote aircraft, Giblings can enter **PASSENGER** mode. The other pilot remains the pilot — passenger mode is camera-only and never transfers aircraft control.

Your passenger camera follows the remote aircraft while keeping an independent local look/head-tracking view. Passenger Giblings are hidden externally while inside the aircraft. If the target disappears for long enough, Giblings attempts a safe return.

## Multiplayer + chat

`Alt + N` joins the Giblings public P2P layer. Compatible clients can share Gibling presence, position/state, colour, display name, head direction, compact arm pose and marshalling state.

### 1.0.2 multiplayer recovery

Version 1.0.2 hardens multiplayer presence against temporary packet gaps, background-tab throttling and reconnects. Remote Giblings now move through ACTIVE, STALE and DISCONNECTED recovery states instead of being permanently deleted after a short timeout. A low-rate heartbeat, periodic host snapshots, reliable PeerJS data transport, immediate reconnect re-announcement and persistent-ID migration allow a returning Gibling to recover without reloading GeoFS or toggling the network off and on.

The **SOCIAL** tab contains Giblings text chat and proximity voice. This is separate from GeoFS chat.

## Proximity voice

Voice is **off by default** and requires explicit microphone permission.

When enabled, voice uses WebRTC between Giblings peers. Local 3D/HRTF positioning and distance falloff make other players sound directional and quieter as they move farther away. Voice range and volume are adjustable; with push-to-talk selected, hold `V` to transmit.

Giblings does not intentionally record or store microphone audio.

## Marshalling — VERY EARLY ACCESS

> [!CAUTION]
> **BUGS WILL BE PRESENT. This is the least mature part of the release.**

`Alt + M` adds two marshalling wands to your tracked arms and enables the experimental recognition display. Current recognition attempts include:

- STOP
- STRAIGHT AHEAD
- TURN LEFT
- TURN RIGHT
- SLOW DOWN
- HOLD POSITION

Turn labels are shown from the pilot's point of view. Recognition can be wrong or fail entirely depending on movement, lighting, camera angle and body visibility. It is **not** an aviation-certified marshalling system and it never drives throttle, brakes, steering or any other aircraft control.

## Privacy

Camera and microphone access are opt-in. Webcam frames stay local. Online tracking sends compact pose/state data rather than webcam video. Passenger mode never transfers aircraft control. Voice uses WebRTC and is not intentionally recorded by Giblings.

As with any public chat system, don't send private information through Giblings text chat.

## Panic + diagnostics

If Giblings starts behaving strangely, `Alt + X` shuts down Giblings-owned movement, camera, networking and tracking state and attempts to return normal control to GeoFS. `F8` is the emergency keyboard panic. Reload GeoFS before re-arming Giblings after a panic stop.

`F9` opens Diagnostics and is the first place to look when reporting a bug.

## Known limitations

- GeoFS 3.9 only for this release line.
- Remote passenger mode depends on the remote aircraft data available to the local GeoFS client.
- Browser camera/microphone permissions and WebRTC behavior vary by browser and OS.
- P2P connectivity can fail because of NAT/firewall/network conditions.
- Marshalling is experimental and can misclassify movements.
- Giblings is an independent community addon, not an official GeoFS feature.

## Troubleshooting

Start by disabling every older Giblings userscript and reloading GeoFS. Check `F9` Diagnostics. For stuck camera/input state, use `Alt + X` or `F8` and reload. For tracking problems, verify camera permission and retry `Alt + H`. For voice problems, verify microphone permission and make sure both clients are online in the SOCIAL tab.

## Version

Current public build: **Giblings 1.0.2**.

The earlier 2.x–5.x numbered builds were development/alpha builds. The public version line was intentionally reset for the first public release.
