# Project Overview
**App Name:** EchoVoice
**Description:** A mobile application providing AI-driven, post-stroke speech therapy for adults. It features interactive exercises, personalized therapy plans, real-time speech recognition feedback, and gamification to help users regain speech abilities privately and affordably. 
**Tech Stack:** Flutter (Frontend), Firebase via MCP (Auth, Firestore, Storage), TensorFlow Lite (On-device ML for speech analysis).

# Technical Architecture & Core Directives
1. **Firebase Integration (MCP):** Define and implement an optimized Firestore schema to handle Users (Patients/Therapists), Sessions, Exercise Results, and Progress tracking. Handle user authentication (Email/Password & Google Sign-in).
2. **Audio Storage:** All recorded patient audio (`.wav` format) during exercises must be uploaded to Firebase Storage and linked to the corresponding session document in Firestore so therapists can review them later.
3. **On-Device ML (TFLite):** The app will utilize an on-device `.tflite` model for evaluating speech. Integrate the `tflite_flutter` package. The model expects audio input and expected text, and outputs a JSON-like map containing: `clarity_score`, `expected_phrase`, `spoken_phrase`, `feedback`, and `model_confidence`.
4. **Linkage:** Automatically link Patients to Therapists in Firestore based on the email address provided by the therapist during their onboarding, provided the patient is registered.

# Screen Requirements & User Flows

## 1. Onboarding Flow
* **Splash Screen:** Display the EchoVoice logo and the catchphrase: "Finding your voice, one word at a time."
* **Auth Screen:** Login and Registration page supporting Email/Password and "Continue with Google".
* **Role Selection:** After successful signup, prompt the user to select their role: "I am a Patient" or "I am a Therapist".

## 2. Patient Onboarding
* **Profile Setup:** Collect First Name, Last Name, Age.
* **Caregiver Toggle:** Include a checkbox for "Managed by Caregiver". If checked, enable "Self-Guided Mode" allowing immediate access to all exercises without waiting for therapist assignments.
* **Needs Assessment:** Radio buttons for practice frequency (Daily, Few times a week, Rarely) and Primary Goal (Improve articulation, Better word recall, Increase speech fluency).

## 3. Therapist Onboarding
* **Professional Details:** Collect Full Name, Specialization (e.g., Speech Pathologist), and Clinic/Hospital.
* **Patient Linking:** Prompt "Add Your First Patient". Collect Patient Name, Condition Severity (dropdown), and Patient Email. Automatically link the accounts in Firestore if the patient email exists.

## 4. Patient Dashboard
* **Layout:** Top App Bar (Welcome back, [Name] + Profile icon) and Bottom Navigation (Home, Exercises, Progress, Settings).
* **Active Session Card:** "Resume Session" button showing current module progress.
* **Daily Progress Summary:** A mini line chart showing accuracy score trends over the week.
* **Exercises Grid:** Cards for specific modules (Articulation, Listening, Rhythm, Memory).
* **Games Grid:** Links to interactive games ("Word Repeat" and "Picture Naming").
* **Achievements:** Badges for streaks (e.g., "7 Day Streak") and milestones ("100 Words Spoken").

## 5. Therapist Dashboard
* **Layout:** Bottom Navigation (Patients, Library, Profile).
* **Overview Stats:** Cards displaying "Active Patients" count and "Needs Review" count.
* **Attention Needed:** Alert cards for patients struggling with specific tasks (e.g., "Struggling with 'R' sounds"), showing latest score, streak, and an "Assign Exercise" button.
* **Patient List:** Scrollable list of all linked patients with progress bars indicating overall recovery progress.
* **Assign Exercise Flow:** Opens a view identical to the "Self-Guided Mode / Phrase Practice Selection" allowing the therapist to push specific phrase categories to the patient's queue.

## 6. Core Exercises & Games
* **Phrase Practice Selection:** A grid UI categorizing phrases (Common, Food/Drink, Feelings, Medical) and displaying specific phrase buttons (e.g., "Hello, how are you?").
* **Phrase Practice Execution:**
    * Displays target phrase.
    * "Listen to Example" button (plays TTS of the phrase).
    * Microphone recording button.
    * **Feedback Logic:** Once recorded, pass audio to the TFLite model. Provide general feedback based strictly on the `clarity_score` returned by the model. Do not highlight specific letters. Show a visual accuracy score bar.
    * Upload `.wav` to Firebase Storage.
* **Word Repeat Challenge:** * Displays target word/sound.
    * Must feature a **real-time generated waveform** visualizer on the screen while the user speaks.
    * Compare "Ideal Pattern" waveform visually against "Your Attempt" waveform post-recording.
* **Picture Naming Game:**
    * Displays an AI-generated/stock image (e.g., an Apple).
    * Prompt: "Name this object".
    * Microphone button to record guess, verified via TFLite model or standard speech-to-text.

## 7. Progress & Reporting
* **Daily Progress Report:** Detailed view showing Total Practice Time, Average Accuracy, a large line chart for 7-day Accuracy Trend, and a breakdown of today's activity by exercise type.
* **Progress Tracker:** View showing current attempt percentage, weekly trend, and a historical list comparing the expected phrase vs. the user's spoken phrase. Includes a "Share Report with Therapist" action.
abc