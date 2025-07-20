
```python
"""
Eye-Controlled Mouse using Mediapipe, OpenCV & PyAutoGUI

This project tracks your eye movements using your webcam and lets you control your mouse pointer and clicks with just your eyes.

------------------------------------------
📦 Dependencies:
- opencv-python
- mediapipe
- pyautogui

Install them using:
    pip install opencv-python mediapipe pyautogui

------------------------------------------
💡 How It Works:
- Tracks the right eye's iris (landmarks 474–478) for mouse movement.
- Detects blinks using the left eye (landmarks 145 & 159) to simulate clicks.
- Moves mouse across screen and performs click when blinking.

------------------------------------------
🖥️ Usage:
1. Ensure your webcam is connected.
2. Run this script:
       python eye_mouse.py
3. Move your eyes to control the cursor.
4. Blink your left eye to click.

⚠️ Notes:
- Best used in well-lit environments.
- Avoid wearing glasses for more accuracy.
- Tested on Windows.

Author: Subbarayudu Chittiboina
"""

import cv2
import mediapipe as mp
import pyautogui

# Initialize camera and tools
cam = cv2.VideoCapture(0)
face_mesh = mp.solutions.face_mesh.FaceMesh(refine_landmarks=True)
screen_w, screen_h = pyautogui.size()

# Real-time loop
while True:
    _, frame = cam.read()
    frame = cv2.flip(frame, 1)
    rgb_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
    output = face_mesh.process(rgb_frame)
    landmark_points = output.multi_face_landmarks
    frame_h, frame_w, _ = frame.shape

    if landmark_points:
        landmarks = landmark_points[0].landmark

        # Cursor Movement via Right Eye
        for id, landmark in enumerate(landmarks[474:478]):
            x = int(landmark.x * frame_w)
            y = int(landmark.y * frame_h)
            cv2.circle(frame, (x, y), 3, (0, 255, 0))
            if id == 1:
                screen_x = screen_w * landmark.x
                screen_y = screen_h * landmark.y
                pyautogui.moveTo(screen_x, screen_y)

        # Blink Detection via Left Eye
        left = [landmarks[145], landmarks[159]]
        for landmark in left:
            x = int(landmark.x * frame_w)
            y = int(landmark.y * frame_h)
            cv2.circle(frame, (x, y), 3, (0, 255, 255))

        if (left[0].y - left[1].y) < 0.004:
            pyautogui.click()
            pyautogui.sleep(1)

    cv2.imshow('Eye Controlled Mouse', frame)
    cv2.waitKey(1)
```

---

### ✅ After Saving the File:

* Save as `eye_mouse.py`
* Open terminal or command prompt
* Run it with:

```bash
python eye_mouse.py
```


