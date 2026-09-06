<div id="top"></div>

<h1 align="center">Autonomous Capture the Flag RoboCar</h1>

<!-- PROJECT LOGO -->
<br />
<div align="center">
  <a href="https://jacobsschool.ucsd.edu/">
    <img src="https://github.com/user-attachments/assets/1553fb49-6e27-4bc8-8958-e252f010d7d2" alt="UC San Diego Jacobs School of Engineering Logo" width="400">
  </a>

  <h3>ECE/MAE 148 Final Project</h3>
  <p>Team 7 — Summer Session II 2026</p>

  <img src="https://github.com/user-attachments/assets/f152939b-df00-4d88-a925-a94eef3d66d5" alt="Team 7 Autonomous Capture the Flag RoboCar" width="554">
</div>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li><a href="#overview">Overview</a>
      <ul>
        <li><a href="#key-features">Key Features</a></li>
        <li><a href="#final-demonstration">Final Demonstration</a></li>
      </ul>
    </li>
    <li><a href="#team-members">Team Members</a></li>
    <li><a href="#project-goals">Project Goals</a>
      <ul>
        <li><a href="#must-have-features">Must-Have Features</a></li>
        <li><a href="#nice-to-have-features">Nice-to-Have Features</a></li>
      </ul>
    </li>
    <li><a href="#system-architecture">System Architecture</a>
      <ul>
        <li><a href="#final-capture-sequence">Final Capture Sequence</a></li>
      </ul>
    </li>
    <li><a href="#technologies-used">Technologies Used</a></li>
    <li><a href="#how-to-run">How to Run</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#operation-steps">Operation Steps</a></li>
      </ul>
    </li>
    <li><a href="#project-results">Project Results</a>
      <ul>
        <li><a href="#what-worked">What Worked</a></li>
        <li><a href="#challenges">Challenges</a></li>
        <li><a href="#lessons-learned">Lessons Learned</a></li>
      </ul>
    </li>
    <li><a href="#future-improvements">Future Improvements</a></li>
    <li><a href="#final-project-documentation">Final Project Documentation</a></li>
    <li><a href="#robot-design">Robot Design</a>
      <ul>
        <li><a href="#cad-parts">CAD Parts</a>
          <ul>
            <li><a href="#custom-designed-parts">Custom-Designed Parts</a></li>
            <li><a href="#open-source-part">Open-Source Part</a></li>
          </ul>
        </li>
        <li><a href="#electronic-hardware">Electronic Hardware</a></li>
        <li><a href="#software">Software</a></li>
      </ul>
    </li>
    <li><a href="#generative-ai-use">Generative AI Use</a>
      <ul>
        <li><a href="#llm-prompts-used-during-this-project">LLM Prompts</a></li>
      </ul>
    </li>
    <li><a href="#repository-contents">Repository Contents</a></li>
    <li><a href="#contact">Contact</a></li>
  </ol>
</details>


---

## Overview

This project develops an autonomous RoboCar that follows a recorded RTK GPS route while searching for designated colored flags. An OAK-D camera and YOLO object-detection model recognize pink, yellow, orange, and blue flags. When a flag is first detected, the system announces its color, temporarily overrides GPS navigation, approaches and centers on the flag slowly, stops once it is extremely close, and announces that the flag has been captured.

The team originally planned to capture flags by driving through and knocking them down. During final testing, the enlarged, wind-resistant flag stands made the run-over method impractical and could disturb the vehicle's path. The final demonstration therefore defined capture as a controlled close-range stop.

### Key Features

- **RTK GPS navigation:** Records and autonomously follows a manually driven route.
- **YOLO flag detection:** Recognizes pink, yellow, orange, and blue flags using the OAK-D camera.
- **Autonomous approach:** Centers on the detected flag while approaching at a controlled speed.
- **Final capture behavior:** Stops at close range instead of driving through the larger flag stand.
- **Audio feedback:** Announces "[color] flag detected" and "[color] flag captured" at the appropriate mission transitions.
- **Control arbitration:** Allows the flag controller to override GPS steering and throttle only during an active flag mission.

### Final Demonstration

The final demonstration shows the RoboCar following its GPS-guided route, detecting a target with YOLO, announcing the detected color, approaching slowly, stopping at close range, and announcing the capture.

<div align="center">
  <a href="https://youtu.be/sbvSZNVWww0">
    <img src="https://img.youtube.com/vi/sbvSZNVWww0/hqdefault.jpg" alt="Team 7 Final Demonstration" width="650">
  </a>
  <p><a href="https://youtu.be/sbvSZNVWww0">Watch the final demonstration on YouTube</a></p>
</div>

---

## Team Members

| Team Member | Major | Email |
|---|---|---|
| Maisarah Fahmy | Electrical Engineering | [m4fahmy@ucsd.edu](mailto:m4fahmy@ucsd.edu) |
| Grisvian Tandy | Electrical Engineering | [gtandy@ucsd.edu](mailto:gtandy@ucsd.edu) |
| Pragnya Korti | Mechanical Engineering | [pkorti@ucsd.edu](mailto:pkorti@ucsd.edu) |

---

## Project Goals

### Must-Have Features

1. Autonomously complete GPS-guided laps while searching for a flag.
2. Detect and identify the designated flag color using the OAK-D camera and YOLO.
3. Navigate toward and center on the detected flag.
4. Approach slowly and stop at close range to complete the final capture behavior.
5. Announce the flag color when detected and announce when it is captured.
6. Use generative AI for code generation, debugging, and system integration.

### Nice-to-Have Features

| Feature | Final Status |
|---|---|
| Return to the starting point and stop automatically | Not completed; retained as future work |
| Remember previously searched areas | Not completed; retained as a future search improvement |
| Detection across changing lighting conditions | Partially completed; YOLO was more reliable than fixed OpenCV thresholds |
| Support multiple designated flag colors | Completed for pink, yellow, orange, and blue |

---

## System Architecture

The system combines GPS-based global navigation with camera-based local flag control. GPS controls the RoboCar normally. Once YOLO confirms a flag, the flag mission temporarily supplies the steering and throttle commands. The detected bounding-box center determines steering, while bounding-box area estimates proximity.

```text
POINT ONE RTK GPS
        |
        v
FOLLOW RECORDED ROUTE
        |
        v
OAK-D CAMERA -> YOLO FLAG DETECTION
        |
        v
FLAG MISSION OVERRIDES GPS
        |
        v
VESC STEERING + THROTTLE
```

### Final Capture Sequence

```text
FOLLOW GPS ROUTE
        |
        v
YOLO CONFIRMS FLAG COLOR
        |
        v
ANNOUNCE "[COLOR] FLAG DETECTED"
        |
        v
APPROACH SLOWLY + CENTER FLAG
        |
        v
FLAG REACHES CLOSE-RANGE THRESHOLD
        |
        v
STOP VEHICLE
        |
        v
ANNOUNCE "[COLOR] FLAG CAPTURED"
```

The earlier drive-through, knock-down, and reverse sequence was replaced for the final video because the redesigned flag stands were too large and stable for a consistent run-over capture.

---

## Technologies Used

- Raspberry Pi 5
- DonkeyCar 5.3.0
- Python
- Point One RTK GPS
- OAK-D camera and DepthAI
- Ultralytics YOLO
- OpenCV during early prototyping
- VESC motor and steering controller
- Logitech F710 controller
- Jabra SPEAK 510 USB speaker
- `espeak` and ALSA audio playback

---

## How to Run

### Prerequisites

- Raspberry Pi 5 with the Team 7 DonkeyCar environment
- Point One RTK GPS connected and receiving corrections
- OAK-D camera connected
- VESC connected and calibrated
- Logitech F710 in **X mode**
- Jabra USB speaker connected
- YOLO weights available in the project directory

Typical Team 7 device assignments:

```text
Point One runner:       /dev/ttyUSB0
DonkeyCar GPS NMEA:     /dev/ttyUSB1
VESC:                   /dev/ttyACM0
Logitech F710:          /dev/input/js0
Jabra speaker:          plughw:2,0
```

USB device numbers may change after reconnecting hardware.

### Operation Steps

#### 1. Start Point One RTK corrections

```bash
cd ~/quectel/p1_runner
conda activate py37
python3 bin/runner.py --device-id <DEVICE_ID> --polaris <POLARIS_PASSWORD> --device-port /dev/ttyUSB0
```

Keep this terminal running.

#### 2. Start DonkeyCar

From another SSH terminal:

```bash
conda deactivate
source ~/env/bin/activate
cd ~/gpscar
python3 manage.py drive
```

#### 3. Record a GPS route

Remain in `user` mode:

1. Place the RoboCar at the physical starting point.
2. Press **B** to reset the GPS origin.
3. Confirm `pos/x` and `pos/y` are near zero.
4. Press **LB** to begin recording.
5. Manually drive one smooth lap.
6. Press **LB** again to stop recording.
7. Press **RB** to save the path.

The route is stored in `donkey_path.csv`.

#### 4. Run the autonomous mission

1. Return the vehicle to the original starting point.
2. Press **B** to reset the GPS origin again.
3. Press **A** to load the saved path if necessary.
4. Press **START** to enter `local_angle` and verify steering.
5. Press **START** again to enter `local` mode.
6. The RoboCar follows the GPS route and searches for flags.
7. When a flag is confirmed, the flag mission controls the approach and capture sequence.

The final close-range controller is stored in `flag_override_alterative.py`. The repository also retains `flag_override_new.py`, which documents the earlier drive-through and reverse approach.

---

## Project Results

### What Worked

- The RoboCar recorded and followed a GPS route autonomously.
- YOLO detected the four mission flag colors more reliably than fixed OpenCV color thresholds.
- The flag controller could override GPS commands and steer toward a detected flag.
- The vehicle approached the target slowly and stopped once the flag was extremely close.
- The Jabra speaker announced the detected flag color and the completed capture.
- The final software combined GPS navigation, flag detection, approach control, stopping, and audio feedback.

### Challenges

- Fixed OpenCV color thresholds failed under changing sunlight, shadows, weather, and time of day.
- OAK-D exposure and resolution behavior sometimes reduced detection consistency.
- Loss of a stable RTK fix could cause oscillation and move the target outside the camera's field of view.
- The original flags were too flimsy, while the improved stands became too large for reliable run-over capture.
- The team changed the final capture behavior to a controlled close-range stop to match the redesigned stands.

### Lessons Learned

1. Real-world lighting, weather, GPS accuracy, and physical design strongly affect autonomous behavior.
2. Testing navigation, detection, audio, and capture separately makes integration easier to debug.
3. YOLO was more robust outdoors than fixed OpenCV color thresholds.
4. Reliable, repeatable behavior is more valuable than a mechanically complex capture sequence.

---

## Future Improvements

- Expand the YOLO dataset across additional lighting conditions, distances, and viewing angles.
- Improve OAK-D exposure and resolution settings.
- Continue tuning GPS PID parameters and smooth steering during sharp corrections.
- Improve recovery after missed detections or temporary RTK instability.
- Expand environmental scanning and avoid repeatedly searching the same areas.
- Improve the flag and capture mechanism for greater stability and repeatability.
- Complete reliable return-to-home behavior.

---

## Final Project Documentation

- [Final Project Presentation](https://canva.link/cs8pddsk6u9s53z) — includes the final Gantt chart
- [Initial Project Proposal](https://docs.google.com/presentation/d/1PcwKYoQy9qChWULbBPM9G7U1BvT1Eh_F-7xbM_thogo/edit?usp=sharing)
- [Progress Update 1](https://docs.google.com/presentation/d/1O7TpyJ2FOgLmPWjsEIvTT7KbvIdUvB2rIri6aobAf58/edit?usp=sharing)
- [Progress Update 2](https://docs.google.com/presentation/d/1aCJ6-ilCrfEd1O_nXW9QFnMKlvR-987CgDM7sW6m3Tc/edit?usp=sharing)
- [Progress Update 3](https://docs.google.com/presentation/d/1OJnH7KM3IYKkB1JIBEP8HNq9V_1NIHCVKdMrLFokIOc/edit?usp=sharing)
- [Final Demonstration Video](https://youtu.be/sbvSZNVWww0)

---

## Robot Design

### CAD Parts

#### Custom-Designed Parts

| Part | CAD Model | Description |
|---|---|---|
| Onboard Electronics Enclosure | <img src="https://github.com/user-attachments/assets/b33839b7-614a-4bc9-a7dc-1aeca7dc9e2e" alt="Onboard Electronics Enclosure" width="300" /> | Houses the vehicle's wiring, cooling fan, and most components, excluding the battery and VESC. |
| Adjustable OAK-D Camera Mount | <img src="https://github.com/user-attachments/assets/88be0d97-1b29-4433-9604-9f34e996385b" alt="Adjustable OAK-D Camera Mount" width="300" /> | Holds the OAK-D camera and allows its viewing angle to be adjusted and secured with screws. |
| Flagpole Support Base | <img src="https://github.com/user-attachments/assets/3dfd835d-e0c0-46f6-9a8e-d3fc1752e695" alt="Flagpole Support Base" width="300" /> | Holds the bamboo flagpoles upright and improves stability during outdoor testing. |
| Modular Component Mounting Plate | <img src="https://github.com/user-attachments/assets/ce3d77d3-cd70-4083-94dd-b94ffad9d6cb" alt="Modular Component Mounting Plate" width="300" /> | Provides the main mounting surface and screw holes for securely attaching RoboCar components. |

#### Open-Source Part

| Part | CAD Model | Source |
|---|---|---|
| Raspberry Pi 5 Mach 1 Case | <img src="https://github.com/user-attachments/assets/97af9fba-b145-4633-b658-dade33594773" alt="Top of Raspberry Pi 5 Mach 1 Case" width="250" /><br><br><img src="https://github.com/user-attachments/assets/508b19d6-ad04-45c7-98a0-ca625f44a2d0" alt="Bottom of Raspberry Pi 5 Mach 1 Case" width="250" /> | Downloaded from [MakerWorld](https://makerworld.com/en/models/648246-raspberry-pi-5-mach-1-case#profileId-575928); not designed by Team 7. |

### Electronic Hardware

The following circuit diagram shows the connections between the RoboCar's main electronic components.

<p align="center">
  <img src="https://github.com/user-attachments/assets/ce92d7f5-5fea-404b-b286-188b4dab94e4" alt="Team 7 RoboCar Electronic Circuit Diagram" width="800">
</p>

| Component | Purpose |
|---|---|
| Raspberry Pi 5 | Main onboard computer |
| Point One RTK GPS | Localization and GPS-route navigation |
| VESC | Steering and throttle control |
| Logitech F710 | Manual driving and mission controls |
| OAK-D camera | YOLO flag detection |
| Jabra SPEAK 510 | Detection and capture announcements |
| Cooling fan | Airflow inside the electronics enclosure |
| Battery | Vehicle and component power |

### Software

#### Embedded System

The autonomous system runs on a Raspberry Pi 5 mounted onboard the RoboCar. The Raspberry Pi connects to the GPS receiver, OAK-D camera, VESC, controller, and USB speaker. Most electronics and wiring are protected inside the custom onboard electronics enclosure, which also contains a cooling fan.

#### DonkeyCar

DonkeyCar provides the main vehicle pipeline for steering, throttle, controller input, GPS path following, and autonomous driving. Team 7 extended the pipeline with YOLO flag detection, flag-control arbitration, close-range capture behavior, and audio feedback.

#### YOLO Flag Detection

The OAK-D camera supplies images to a YOLO model trained to recognize pink, yellow, orange, and blue flags. Each detection provides a class, confidence, bounding box, center position, and area. The controller uses the center for steering and the bounding-box area as a proximity estimate.

---

## Generative AI Use

Generative AI was used throughout development to generate and refine code, debug software and hardware-integration problems, interpret error messages, and assist with integrating the GPS, YOLO detection, vehicle-control, and audio subsystems.

### LLM Prompts Used During This Project

<details>
  <summary>View the prompts used during development</summary>

#### 1. GPS Autonomous Navigation

Set up the robot to use **PointOne RTK GPS** for autonomous navigation. The system should be able to record a manually driven route, save that route, reset its origin at the starting position, and then autonomously follow the recorded GPS loop. The GPS system should remain the robot's normal navigation method whenever no flag mission is active.

#### 2. Camera-Based Flag Detection

Use the **OAK-D camera** to detect mission flags while the robot is driving. The required flag colors are **pink, yellow, orange, and blue**. Detection was initially implemented using OpenCV/color thresholding, but the final approach should use a **YOLO object-detection model**. Detection should identify the flag's color, location in the camera image, approximate size, and confidence without unnecessary filtering or overly restrictive detection rules.

#### 3. GPS and Flag-Mission Integration

The robot should continuously follow its GPS route until a valid flag is detected. Once a flag is confirmed, the flag mission should temporarily **override GPS steering and throttle commands**. After the flag mission finishes, control should automatically return to GPS navigation so the robot can continue following the original loop.

#### 4. Flag Approach and Steering

After detecting a flag, the robot should actively drive toward it rather than simply stopping or waiting. The camera should determine whether the flag is to the left or right of the center of the image, and the robot should steer accordingly while continuing to move forward. As the flag becomes larger in the camera image, the system should recognize that the robot is getting closer.

#### 5. Flag Capture Sequence

The original requested behavior was:

**Detect flag — announce "[color] flag spotted" — approach and center on flag — stop for approximately 2 seconds — drive forward through the flag — determine that the flag has been knocked down/captured — announce "[color] flag captured" — reverse to clear the flag — return to GPS navigation.**

**Final design change:** The larger flag stands made the run-over method impractical. The final demonstration instead used: **detect flag — announce "[color] flag detected" — approach slowly and center — stop at close range — announce "[color] flag captured."**

#### 6. Lost-Flag Behavior

If the flag disappears while the robot is still approaching and has not reached the capture stage, the robot should eventually abandon the attempt and return to GPS rather than wandering around searching for it. No sweeping or dedicated flag-search behavior is required. If the flag disappears after the robot has already begun driving through it, that disappearance can be used as evidence that the flag has been knocked down.

#### 7. Audio Feedback

A **Jabra USB speaker** should provide audible mission feedback. Each of the four colors should have both a detected and captured announcement, such as **"blue flag detected"** and **"blue flag captured."** The announcements should correspond to actual mission-state transitions.

#### 8. Standalone Flag Testing

The flag-detection system should also be testable independently from the GPS/autonomous-driving system. A camera-only test should allow YOLO detection and the associated flag information/audio to be checked without starting GPS navigation or commanding the motors.

#### 9. Safety and Implementation Requirements

The existing working GPS navigation should be preserved while flag functionality is added. Changes should be minimal and focused rather than restructuring the entire DonkeyCar system. Flag control should only override navigation during an active autonomous flag mission, and testing should distinguish clearly between **camera-only tests that cannot move the robot** and **integrated tests capable of commanding steering and throttle**.

</details>

---

## Repository Contents

### Capture Controller Versions

Two flag-controller versions are included to document the development of the capture system:

- **Version 1** used the original drive-through method. The RoboCar approached the flag, paused, drove through and knocked it down, announced the capture, reversed, and returned control to GPS navigation.
- **Version 2** was created after the flag stands were enlarged for better stability. The RoboCar announces the detected color, approaches the flag slowly, stops at close range, announces the capture, and then returns control to GPS.

Version 2 represents the behavior shown in the final demonstration.

| File | Purpose |
|---|---|
| `manage.py` | Main DonkeyCar pipeline and subsystem integration |
| `flag_override_alterative.py` | **Version 2 - Final demonstrated method.** Uses the slow approach, close-range stop, capture announcement, and return to GPS. |
| `flag_override_new.py` | **Version 1 - Original method.** Uses the pause, drive-through, knock-down, reverse, and GPS-return sequence. |
| `myconfig.py` | GPS, VESC, controller, throttle, steering, and PID configuration |
| `train.py` | YOLO model-training script |
| `weights.pt` | Trained YOLO flag-detection weights |
| `donkey_path.csv` | Recorded GPS route |
| `calibrate.py` | Vehicle calibration utility |

---

## Contact

- Maisarah Fahmy - [m4fahmy@ucsd.edu](mailto:m4fahmy@ucsd.edu)
- Grisvian Tandy - [gtandy@ucsd.edu](mailto:gtandy@ucsd.edu)
- Pragnya Korti - [pkorti@ucsd.edu](mailto:pkorti@ucsd.edu)

<p align="right"><a href="#top">Back to top</a></p>
