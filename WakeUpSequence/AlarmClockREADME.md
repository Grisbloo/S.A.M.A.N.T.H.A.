# S.A.M.A.N.T.H.A. Alarm Clock Module

## Objective
Provide a gentle, sensory-friendly wake-up experience using a dynamically generated daily brief instead of a traditional alarm, and automate the deployment of the garage door. The system prioritizes high reliability (SRE failsafes) and non-blocking hardware interactions.

## Required Hardware
* **Raspberry Pi 4B** (Core processing)
* **HAT with RTC (Real-Time Clock)** (To control power and strict alarm scheduling)
* **GPIO Speaker** (For audio playback)
* **GPIO Microphone** (For voice commands)
* **GPIO Physical Button** (For manual garage door operation)
* **RF Transmitter** (To interface with the garage door)
* **LLM API Connection** (Google Gemini API for daily brief generation)

---

## Timeline of Events

### 1. Pre-Wake Initialization (1 Minute Before Alarm)
* Raspberry Pi boots/wakes via the HAT scheduling.
* **GPIO Initialization:** Maps physical pins for the mic, speaker, button, and RF transmitter.
* **API Fetch & SRE Failsafe:** Sends a fetch request to the LLM API for the daily brief.
    * *Success:* Bundles the API text into the Text-to-Speech (TTS) function and preps the audio.
    * *Failure (Timeout/Network Drop):* Catches the exception and defaults to a locally stored, pre-recorded MP3 greeting to ensure the alarm still fires.

### 2. The Wake-Up Sequence (Alarm Time)
* **Volume Ramping:** The audio playback begins at 10% volume and smoothly scales up to the maximum comfort limit over the first 30 seconds.
* **Interactive Loop:** * The Raspberry Pi keeps the microphone hot to listen for shut-off commands (e.g., "Stop", "Turn off the alarm").
    * If the brief finishes and no shut-off command is heard, the system loops a gentle ambient chime (or repeats the offline greeting) until the user verbally confirms they are awake.

### 3. The Garage Door Automation (7 Minutes Post-Wake)
* Once the alarm is successfully terminated via voice command, an **Asynchronous Timer** begins counting down 7 minutes in the background. (This ensures the main script remains active and doesn't freeze).
* Upon timer completion, the RF transmitter fires to open the garage door.

---

## Side Objective: Manual Override
* **Inside-the-House Garage Door Opener:** Utilize a physical GPIO button mapped to an edge-detecting interrupt function. Pressing this button fires the RF transmitter to open/close the garage door immediately, entirely independent of the alarm's automated 7-minute timer.

## Failsafes & Cleanup
* **GPIO Cleanup:** Regardless of how the script terminates (success, user cancellation, or error), a cleanup function runs to reset all GPIO pins. This guarantees the RF transmitter isn't left in a "high/broadcasting" state.
