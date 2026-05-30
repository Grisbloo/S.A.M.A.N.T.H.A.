Objective: 
Wake up user and open garage door using raspberry pi

Required hardware: 
Rasberry Pi 4B, GIPO speaker and Microphone, GIPO button, connection to an already set up LLM api to provide daily brief, rf transmitter, hat with clock to control power and actual alarm values

Timeline of events: 

Minute before alarm needs to go off
- Raspberry Pi turns on via the HAT, sends a fetch request to Google Gemini API to ask for daily brief
- prepares itself to give the user information and bundles everything waiting for alarm to activate
Alarm goes off
- Raspberry pi runs script to keep the microphone on to allow shut off of alarm via commands such as "Stop", or "Turn off the alarm"
- Raspberry pi also sends the text package into its text to speech function and converts the daily brief from the API to wake the user up
7 or so minutes after the user has shut off the alarm
- Rf transmitter is activated to open the garage door

Side objective:
- Use RF transmitter to have an internal garage door opener (have a small function perhaps connected to a button that opens the garage door
