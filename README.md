# Giblings 1.0 — Public Release

**Owner: PilotOfTheCars**

Giblings is a human-presence layer for **GeoFS 3.9**. It adds first-person walking, a visible Gibling avatar, webcam-driven head and arm tracking, Giblings-to-Giblings multiplayer presence, remote passenger boarding, text chat, proximity voice chat, sound design, and experimental aircraft marshalling.

Giblings runs alongside GeoFS. It does not replace GeoFS flight simulation and it does not write Giblings data into GeoFS native multiplayer packets.

> **MARSHALLING IS VERY EARLY ACCESS. BUGS WILL BE PRESENT.** Arm/wand tracking and signal recognition are experimental. The recognition system is informational only and never controls an aircraft.

## Requirements

- GeoFS **3.9**. Giblings 1.0 intentionally refuses to activate on a different GeoFS version.
- A userscript manager such as Tampermonkey.
- A modern Chromium-based browser is recommended.
- Camera permission is required only for head/arm tracking.
- Microphone permission is required only for proximity voice chat.
- Internet access is required for the Giblings P2P layer and for loading the tracking libraries/models when tracking is enabled.

## Installation

1. Disable or remove older Giblings alpha userscripts. Do not run two Giblings versions at the same time.
2. Create a new userscript in your userscript manager.
3. Paste the complete contents of `Giblings_1.0.0_Public.user.js` and save it.
4. Open or reload GeoFS 3.9.
5. The Giblings panel should appear in the upper-right area of the GeoFS page.
6. Use the PLAY, SOCIAL, SETTINGS and DIAGNOSTICS tabs to configure it.

## Controls

| Command | Action |
|---|---|
| `Alt + G` | Enter ground mode / return to your aircraft |
| `W A S D` | Walk |
| `Shift` | Sprint while moving |
| `Mouse` | Look around while pointer lock is active |
| `E` | Board the nearest supported aircraft / exit a remote passenger seat near the ground |
| `Alt + H` | Enable or disable webcam head + arm tracking |
| `Alt + R` | Recenter head tracking |
| `Alt + M` | Toggle VERY EARLY ACCESS marshalling wands and signal recognition |
| `Alt + N` | Join or leave the Giblings public P2P network |
| `V` | Push-to-talk while proximity voice is enabled and PTT is selected |
| `F9` | Open/close Diagnostics |
| `Alt + X` | Panic stop Giblings |
| `F8` | Emergency panic stop; reload GeoFS to re-arm Giblings |

## How ground mode works

`Alt + G` separates the human/player position from the aircraft. Your aircraft remains where it is while Giblings creates an independent first-person ground position and camera. GeoFS aircraft flight controls are isolated while you are walking so normal movement keys do not remotely steer the aircraft.

Walking follows terrain height. `W A S D` moves the Gibling, `Shift` sprints, and the mouse controls the baseline view. With **Forward follows head/view direction** enabled, forward movement follows the final view heading, including tracked head yaw. A subtle camera-only head bob is applied while walking and increases slightly while sprinting. Head bob does not move the authoritative network position.

## The Gibling avatar

Other Giblings users see a simple fully opaque two-ball character: a larger body sphere, a smaller floating head sphere, and two black eyes. The body/head colour is generated from the local GeoFS multiplayer ID when available, using the full 24-bit hex colour range; a persistent Giblings ID is used as fallback.

Your own body and head are hidden in first person so you do not look through your own avatar. Your tracked arm markers remain locally visible so you can see what your arms are doing.

## Head and arm tracking

`Alt + H` asks for camera permission and loads MediaPipe tracking. Face tracking produces head yaw, pitch and roll for the local camera. The Gibling eyes also move with the tracked head direction.

Pose tracking estimates shoulders, elbows, wrists and hand direction. Giblings turns that pose into compact arm coordinates. The visible arms are represented by small body-coloured balls. Tracking continues whether marshalling wands are enabled or not.

Webcam frames stay local. Giblings does not send webcam video to other Giblings clients. When online, it can send compact head/arm pose values so other clients can animate your Gibling.

## Boarding and passenger mode

While walking, approach a nearby aircraft and press `E`.

For your own aircraft, Giblings returns you to the aircraft normally. For a supported remote GeoFS aircraft, Giblings can enter **PASSENGER** mode. Passenger mode is camera-only: the other pilot keeps complete control of their aircraft. Giblings follows the remote aircraft pose and gives the passenger an independent local look/head-tracking camera.

Passenger Giblings are hidden externally while inside the aircraft. `E` exits when Giblings considers the aircraft close enough to the ground; `Alt + G` returns to your own aircraft. If the remote target disappears for long enough, Giblings performs a safe return.

## Giblings multiplayer presence

`Alt + N` joins the Giblings public P2P room. This is a separate networking layer from GeoFS native multiplayer. Giblings presence can share the public Gibling identity, display name, position/state, avatar colour, head direction, compact arm pose and marshalling signal state.

The public 1.0 protocol is isolated from the old alpha protocol so incompatible alpha clients do not silently mix with public-release clients.

## Text chat

The SOCIAL tab includes Giblings-to-Giblings text chat. Messages are limited and sanitized by the client. Do not send private information. Text chat is part of the Giblings P2P layer, not GeoFS chat.

## Proximity voice chat

Voice is **off by default** and requires explicit microphone permission. Enable it from SOCIAL. Audio uses WebRTC between Giblings peers. Giblings applies local 3D/HRTF positioning and distance falloff using the Giblings positions, so another player can sound quieter and directionally different as they move around you.

The voice range can be adjusted from 20 m to 200 m. Voice volume is adjustable. If Push-to-talk is enabled, hold `V` to transmit. Giblings itself does not intentionally record or store microphone audio.

## Marshalling — VERY EARLY ACCESS

**BUGS WILL BE PRESENT.** This is the least mature feature in Giblings 1.0.

Arm tracking is always available whenever webcam tracking is active. `Alt + M` adds the two marshalling wands and enables the marshalling recognition display. The included wand has a dark handle and emissive red tip.

The current recognizer uses the tracked arm pose plus a short motion history to attempt recognition of:

- STOP
- STRAIGHT AHEAD
- TURN LEFT
- TURN RIGHT
- SLOW DOWN
- HOLD POSITION

Turn labels are presented from the pilot's point of view. Recognition can be wrong, fail to trigger, or behave differently with camera angle, lighting, partial-body visibility and individual movement style. It is not an aviation-certified marshalling system and must not be treated as one. It never drives throttle, brakes, steering or any other aircraft control.

When online, the compact arm pose and recognized signal ID can be shared so another Giblings 1.0 client can see the arm/wand movement and signal label. Webcam frames are not sent.

## UI and sound

The PLAY tab contains the main movement, tracking, networking and panic controls. SOCIAL contains text and voice. SETTINGS controls walking speed, sprint multiplier, head steering, head bob, display name, labels, HUD, sound effects, footsteps and voice behavior. DIAGNOSTICS exposes the current GeoFS/Giblings state and recent errors for troubleshooting.

Giblings generates its interface sounds and footsteps locally through Web Audio. They can be disabled or adjusted in SETTINGS.

## Panic and failure isolation

`Alt + X` or the PANIC STOP button shuts down Giblings-owned movement/camera/network/tracking state and attempts to return control to normal GeoFS. `F8` is the emergency keyboard panic. After a panic stop, reload the GeoFS page before trying to use Giblings again.

The arm renderer is isolated from the main movement loop where possible. A marshalling/arm rendering failure should not be allowed to intentionally take over aircraft controls.

## Privacy summary

- Camera and microphone are opt-in.
- Webcam frames remain local.
- Giblings sends compact pose/state data, not webcam video.
- Voice uses WebRTC and is not intentionally recorded by Giblings.
- Passenger mode does not transfer aircraft controls.
- Giblings networking is separate from GeoFS native multiplayer.
- Do not share private information through the public P2P chat.

## Known limitations

- GeoFS 3.9 only.
- Giblings 1.0 has not been designed as a GeoFS 4.0 compatibility build.
- Remote passenger behavior depends on remote aircraft data being available to the local GeoFS client.
- Browser camera/microphone permissions and WebRTC behavior vary by browser and system.
- P2P networking can fail because of network/NAT/firewall conditions.
- Marshalling is VERY EARLY ACCESS and bugs WILL be present.
- Marshalling recognition is heuristic/experimental and can misclassify movements.
- This is an independent addon and is not an official GeoFS feature.

## Troubleshooting

If Giblings behaves strangely, first disable all older Giblings scripts and reload GeoFS. Use `F9` to inspect Diagnostics. If camera ownership or input becomes stuck, use `Alt + X` or `F8`, then reload the page. If tracking fails, verify camera permission and retry `Alt + H`. If voice fails, verify microphone permission, confirm both clients are online, and check the SOCIAL voice status.

## Release

**Giblings 1.0.0 Public Release** is the first public-version line. The previous 2.x–5.x builds were internal/alpha development versions and are superseded by this release for public use.

**Owner: PilotOfTheCars**
