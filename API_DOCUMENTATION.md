# AwakeCam API Documentation

## Table of Contents
1. [Project Overview](#project-overview)
2. [Flask Web API](#flask-web-api)
3. [Python Functions](#python-functions)
4. [JavaScript Functions](#javascript-functions)
5. [Web Components](#web-components)
6. [Installation & Setup](#installation--setup)
7. [Usage Examples](#usage-examples)
8. [Configuration](#configuration)

## Project Overview

AwakeCam is a real-time drowsiness detection system built with:
- **Backend**: Flask (Python) with computer vision processing
- **Frontend**: HTML/CSS/JavaScript with Bootstrap
- **Hardware**: Raspberry Pi with camera module
- **AI/ML**: YOLO model for drowsiness classification, OpenCV for face detection
- **Networking**: ngrok for public URL tunneling

The system monitors drivers in real-time, detects drowsiness/sleep states, and provides audio alerts.

---

## Flask Web API

### Base URL
- Local: `http://localhost:5000`
- Public: Dynamically generated ngrok URL

### Endpoints

#### `GET /`
**Description**: Main application endpoint that serves the video monitoring interface.

**Response**: HTML page with live video feed and controls

**Example**:
```bash
curl http://localhost:5000/
```

#### `GET /video_feed`
**Description**: Real-time video stream endpoint with drowsiness detection overlay.

**Response**: Multipart HTTP stream (MJPEG format)
- Content-Type: `multipart/x-mixed-replace; boundary=frame`
- Continuous stream of JPEG frames with detection annotations

**Features**:
- Face detection using Haar cascades
- Drowsiness classification (Normal/Drowsy/Sleep)
- Confidence scores displayed
- Audio alerts for drowsy/sleep states

**Example**:
```html
<img src="http://localhost:5000/video_feed" alt="Live Stream">
```

---

## Python Functions

### Core Application Functions

#### `start_ngrok()`
**Description**: Starts ngrok tunnel for public access to the Flask application.

**Parameters**: None

**Returns**: None (runs ngrok as subprocess)

**Example**:
```python
start_ngrok()
# Starts ngrok tunnel on port 5000
```

#### `get_public_url(max_retries=10, delay=1)`
**Description**: Retrieves the public HTTPS URL from ngrok's local API.

**Parameters**:
- `max_retries` (int, optional): Maximum number of retry attempts. Default: 10
- `delay` (int, optional): Delay between retries in seconds. Default: 1

**Returns**: 
- `str`: Public HTTPS URL if successful
- `str`: "http://localhost:5000" if ngrok unavailable

**Example**:
```python
public_url = get_public_url()
print(f"Access your app at: {public_url}")
```

#### `generate()` (Video Stream Generator)
**Description**: Generator function that processes camera frames and yields MJPEG stream.

**Yields**: Bytes in multipart format for HTTP streaming

**Processing Pipeline**:
1. Capture frame from Picamera2
2. Convert RGB to BGR for OpenCV
3. Detect faces using Haar cascade
4. Classify drowsiness state using YOLO model
5. Draw bounding boxes and labels
6. Trigger audio alerts if needed
7. Encode frame as JPEG and yield

**Internal Variables**:
- `last_drowsy`: Timestamp of last drowsiness alert
- `last_sleep`: Timestamp of last sleep alert
- `alert_interval`: Minimum seconds between alerts (3s)

---

## JavaScript Functions

### Password Management

#### `togglePassword(checkbox)`
**Description**: Toggles password field visibility between text and password type.

**Parameters**:
- `checkbox` (HTMLElement): Checkbox element with `data-target` attribute

**Usage**:
```html
<input type="checkbox" data-target="password" onchange="togglePassword(this)">
<input type="password" id="password">
```

**Example**:
```javascript
// Automatically called by checkbox change event
togglePassword(document.getElementById('show-password-checkbox'));
```

### User Management

#### `class Users`
**Description**: User data model class for registration system.

**Constructor Parameters**:
- `username` (string): User's chosen username
- `first_name` (string): User's first name
- `last_name` (string): User's last name  
- `email` (string): User's email address
- `password` (string): User's password

**Example**:
```javascript
const user = new Users('john_doe', 'John', 'Doe', 'john@example.com', 'password123');
console.log(user.username); // 'john_doe'
```

#### `rregistered(event)`
**Description**: Handles user registration form submission with validation.

**Parameters**:
- `event` (Event): Form submission event

**Returns**: 
- `boolean`: false if validation fails, prevents form submission

**Validation Rules**:
- All fields must be filled
- Password and confirm password must match

**Example**:
```html
<form onsubmit="return rregistered(event)">
  <!-- form fields -->
</form>
```

**Behavior**:
- Shows alert for validation errors
- Creates Users object for successful registration
- Redirects to main.html on success
- TODO: Backend integration needed

---

## Web Components

### Navigation Dashboard
**File**: Used across all HTML pages
**Description**: Responsive navigation header with brand logo and menu items.

**Structure**:
```html
<section class="dashboard bg-light position-sticky top-0 w-100">
  <div class="container">
    <div class="row bs5-grid-row">
      <div class="col-lg-1" id="logo"><!-- Logo --></div>
      <div class="col-lg-8" id="title"><!-- Brand Name --></div>
      <div class="col-lg-1" id="contact"><!-- Contact Link --></div>
      <div class="col-lg-1" id="login"><!-- Login Link --></div>
      <div class="col-lg-1" id="register"><!-- Register Link --></div>
    </div>
  </div>
</section>
```

**Navigation Items**:
- **Logo**: Raspberry Pi logo, links to intro.html
- **Brand**: "Awake Cam™", links to intro.html
- **Contact**: Links to contact.html
- **Login**: Links to Login_page.html
- **Register**: Links to register.html

### Registration Form Component
**File**: register.html
**Description**: User registration form with client-side validation.

**Fields**:
- Username (required)
- First Name (required)
- Last Name (required)
- Email (required)
- Password (required)
- Confirm Password (required)
- Show Password checkbox

**Features**:
- Bootstrap styling
- Real-time validation
- Password visibility toggle
- Form submission handling

### Video Monitoring Interface
**File**: main.html
**Description**: Real-time video monitoring dashboard.

**Components**:
- Live video stream display (640x480)
- Location mapping section (placeholder)
- Control panel header

**Stream Configuration**:
```html
<img src="http://192.168.215.61:5000/video_feed" 
     width="640" height="480" 
     alt="Live Stream" 
     style="border:2px solid #000; border-radius:10px;">
```

### Product Showcase
**File**: pricing.html
**Description**: Product catalog with external marketplace links.

**Features**:
- Responsive grid layout
- External product links (Tokopedia, Shopee)
- Product images and descriptions

---

## Installation & Setup

### Prerequisites
```bash
# Python dependencies
pip install flask picamera2 opencv-python pygame ultralytics requests

# System requirements
sudo apt-get install ngrok  # For public URL tunneling
```

### Hardware Requirements
- Raspberry Pi (3B+ or newer recommended)
- Raspberry Pi Camera Module
- Speakers or headphones for audio alerts
- MicroSD card (16GB+)

### Audio Files
Place these audio files in the project root:
- `drowsiness-detected.mpeg`: Alert sound for drowsy state
- `sleep-detected.mpeg`: Alert sound for sleep state

### Model Files
- `best.pt`: YOLO model for drowsiness classification
- `haarcascade_frontalface_default.xml`: OpenCV face detection cascade (included with OpenCV)

### Configuration
1. Update camera configuration in `awakecam.py`:
```python
preview_config = picam2.create_preview_configuration(
    main={"size": (600, 400), "format": 'RGB888'}
)
```

2. Update video feed URL in `main.html`:
```html
<img src="http://YOUR_PI_IP:5000/video_feed">
```

---

## Usage Examples

### Starting the Application
```bash
# Run the Flask application
python awakecam.py

# Output will show:
# Ngrok Public URL: https://abc123.ngrok.io
# * Running on all addresses (0.0.0.0)
# * Running on http://127.0.0.1:5000
```

### Accessing the Interface
1. **Local Access**: `http://localhost:5000`
2. **Network Access**: `http://[PI_IP]:5000`
3. **Public Access**: Use the displayed ngrok URL

### Integration Example
```python
import requests

# Get video frame
response = requests.get('http://localhost:5000/video_feed', stream=True)
for chunk in response.iter_content(chunk_size=1024):
    # Process video chunks
    pass
```

### JavaScript Integration
```javascript
// Monitor registration form
document.getElementById('registration-form').addEventListener('submit', rregistered);

// Toggle password visibility
document.getElementById('show-password').addEventListener('change', function() {
    togglePassword(this);
});
```

---

## Configuration

### Detection Parameters
```python
# Face detection sensitivity
scaleFactor = 1.1      # Image pyramid scaling
minNeighbors = 5       # Minimum neighbor rectangles

# Alert timing
alert_interval = 3     # Seconds between alerts

# Model classes
class_names = ['Normal', 'Drowsy', 'Sleep']
```

### Camera Settings
```python
# Resolution and format
main={"size": (600, 400), "format": 'RGB888'}

# YOLO input size
resized = cv2.resize(roi, (224, 224))
```

### Network Configuration
```python
# Flask server
app.run(host='0.0.0.0', port=5000)

# ngrok tunnel
subprocess.Popen(["ngrok", "http", "5000"])
```

### Styling Configuration
The project uses Bootstrap 5.3.5 with custom CSS files:
- `dashboard.css`: Navigation styling
- `register.css`: Registration form styling
- `intro.css`: Landing page styling
- `pricing.css`: Product page styling
- `contact.css`: Contact page styling
- `help.css`: Help/FAQ styling

---

## Error Handling

### Common Issues

1. **Camera Not Found**:
```python
# Check if camera is properly connected
picam2 = Picamera2()
# Will raise exception if camera unavailable
```

2. **ngrok Connection Failed**:
```python
# Fallback to localhost if ngrok fails
return "http://localhost:5000"
```

3. **Model Loading Issues**:
```python
# Ensure model files exist
if not os.path.exists('best.pt'):
    raise FileNotFoundError("YOLO model not found")
```

4. **Audio File Missing**:
```python
# Check audio files exist before loading
if os.path.exists("drowsiness-detected.mpeg"):
    drowsy_sound = pygame.mixer.Sound("drowsiness-detected.mpeg")
```

### Debugging Tips

1. **Enable Flask Debug Mode**:
```python
app.run(host='0.0.0.0', port=5000, debug=True)
```

2. **Check ngrok Status**:
```bash
curl http://localhost:4040/api/tunnels
```

3. **Monitor Console Output**:
- Watch for detection confidence scores
- Monitor alert timestamps
- Check for OpenCV/camera errors

---

## Performance Optimization

### Frame Rate Optimization
- Adjust camera resolution: Lower resolution = higher FPS
- Optimize detection frequency: Skip frames if needed
- Use threading for audio alerts

### Memory Management
- Limit frame buffer size
- Clean up OpenCV matrices
- Monitor memory usage on Raspberry Pi

### Network Optimization
- Adjust JPEG compression quality
- Implement frame rate limiting
- Use efficient streaming protocols

---

This documentation covers all public APIs, functions, and components in the AwakeCam system. For additional support or feature requests, refer to the contact information in the web interface.