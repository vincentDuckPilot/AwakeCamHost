# AwakeCam - Real-Time Drowsiness Detection System

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://python.org)
[![Flask](https://img.shields.io/badge/Flask-2.0+-green.svg)](https://flask.palletsprojects.com)
[![OpenCV](https://img.shields.io/badge/OpenCV-4.0+-red.svg)](https://opencv.org)
[![YOLO](https://img.shields.io/badge/YOLO-Ultralytics-yellow.svg)](https://ultralytics.com)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

A comprehensive real-time drowsiness detection system built for Raspberry Pi with advanced computer vision and web interface capabilities.

## 🚀 Features

- **Real-Time Detection**: Live video processing with face detection and drowsiness classification
- **AI-Powered**: YOLO-based drowsiness detection with high accuracy
- **Audio Alerts**: Immediate audio notifications for drowsy and sleep states
- **Web Interface**: Modern, responsive web dashboard with Bootstrap styling
- **Remote Access**: ngrok integration for public URL access
- **Cross-Platform**: Works on Raspberry Pi 3B+ and newer models
- **User Management**: Registration system with client-side validation
- **Mobile-Friendly**: Responsive design for all device types

## 📋 Table of Contents

- [Quick Start](#quick-start)
- [Documentation](#documentation)
- [Installation](#installation)
- [Usage](#usage)
- [API Reference](#api-reference)
- [Hardware Requirements](#hardware-requirements)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## 🚀 Quick Start

1. **Hardware Setup**: Connect Raspberry Pi Camera Module and audio output
2. **Install Dependencies**: 
   ```bash
   pip install flask picamera2 opencv-python pygame ultralytics requests
   ```
3. **Run Application**:
   ```bash
   python awakecam.py
   ```
4. **Access Interface**: Open browser to `http://localhost:5000`

For detailed setup instructions, see [QUICK_START_GUIDE.md](QUICK_START_GUIDE.md).

## 📚 Documentation

### Complete Documentation Suite
- **[API Documentation](API_DOCUMENTATION.md)** - Comprehensive API reference with examples
- **[Component Reference](COMPONENT_REFERENCE.md)** - Detailed technical component guide  
- **[Quick Start Guide](QUICK_START_GUIDE.md)** - Step-by-step setup instructions

### Key APIs and Functions

#### Flask Web API
- `GET /` - Main application interface
- `GET /video_feed` - Real-time MJPEG video stream with detection overlay

#### Core Python Functions
- `start_ngrok()` - Initialize public URL tunneling
- `get_public_url()` - Retrieve ngrok public URL
- `generate()` - Video stream generator with AI processing

#### JavaScript Functions
- `togglePassword()` - Password visibility toggle
- `rregistered()` - Form validation and user registration
- `Users` class - User data model

## 🛠 Installation

### Prerequisites
- Raspberry Pi (3B+ or newer)
- Raspberry Pi Camera Module
- Python 3.8+
- Raspberry Pi OS (Bullseye or newer)

### System Dependencies
```bash
# Enable camera interface
sudo raspi-config

# Install ngrok
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc
echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list
sudo apt update && sudo apt install ngrok

# Install audio support
sudo apt install alsa-utils pulseaudio -y
```

### Python Dependencies
```bash
# Create virtual environment (recommended)
python -m venv awakecam-env
source awakecam-env/bin/activate

# Install packages
pip install flask picamera2 opencv-python pygame ultralytics requests
```

### Required Files
Ensure these files are present:
- `awakecam.py` - Main application
- `best.pt` - YOLO drowsiness detection model
- `drowsiness-detected.mpeg` - Drowsy state audio alert
- `sleep-detected.mpeg` - Sleep state audio alert
- HTML/CSS files for web interface
- `raspberry-pi-logo.png` - Brand logo

## 🎯 Usage

### Starting the Application
```bash
python awakecam.py
```

### Accessing the Interface
- **Local**: `http://localhost:5000`
- **Network**: `http://[PI_IP]:5000`  
- **Public**: Use displayed ngrok URL

### Web Interface Pages
- **Landing Page** (`intro.html`) - Project overview and navigation
- **Main Dashboard** (`main.html`) - Live video monitoring interface
- **Registration** (`register.html`) - User account creation
- **Contact** (`contact.html`) - Contact information
- **Pricing** (`pricing.html`) - Product showcase

### Detection States
- **Normal**: Driver is alert and attentive
- **Drowsy**: Driver shows signs of drowsiness (audio alert triggered)
- **Sleep**: Driver appears to be sleeping (audio alert triggered)

## 🔧 API Reference

### REST Endpoints

#### GET /
Returns the main application interface with live video feed.

**Response**: HTML page with embedded video stream

#### GET /video_feed  
Provides real-time MJPEG video stream with AI detection overlay.

**Response**: Multipart HTTP stream
- Content-Type: `multipart/x-mixed-replace; boundary=frame`
- Features: Face detection, drowsiness classification, confidence scores

### Python API

#### Core Functions
```python
# Network tunneling
start_ngrok()                    # Start ngrok tunnel
get_public_url(max_retries=10)   # Get public URL

# Video processing
generate()                       # Video stream generator
```

### JavaScript API

#### Form Handling
```javascript
// Password visibility toggle
togglePassword(checkbox)

// Registration validation  
rregistered(event)

// User data model
new Users(username, first_name, last_name, email, password)
```

## 💻 Hardware Requirements

### Minimum Requirements
- **Raspberry Pi**: 3B+ or newer
- **Camera**: Raspberry Pi Camera Module (any version)
- **Storage**: 16GB MicroSD card (Class 10)
- **Power**: 2.5A power supply (3A for Pi 4)
- **Audio**: Speakers or headphones
- **Network**: Ethernet or WiFi connection

### Recommended Setup
- **Raspberry Pi 4** (4GB RAM) for best performance
- **Camera Module v2 or v3** for better image quality
- **32GB MicroSD card** for additional storage
- **Heatsink/Fan** for thermal management
- **Ethernet connection** for stable network performance

## ⚙️ Configuration

### Camera Settings
```python
# Modify in awakecam.py
preview_config = picam2.create_preview_configuration(
    main={"size": (600, 400), "format": 'RGB888'}
)
```

### Detection Parameters
```python
# Face detection sensitivity
faces = face_cascade.detectMultiScale(gray, scaleFactor=1.1, minNeighbors=5)

# Alert timing
alert_interval = 3  # seconds between alerts
```

### Network Configuration
```python
# Flask server settings
app.run(host='0.0.0.0', port=5000)
```

## 🔍 Troubleshooting

### Common Issues

#### Camera Not Working
```bash
# Check camera status
vcgencmd get_camera

# Enable camera interface
sudo raspi-config → Interface Options → Camera
```

#### Audio Issues
```bash
# Test audio output
speaker-test -t wav -c 2

# Configure audio device
sudo raspi-config → Advanced Options → Audio
```

#### Performance Issues
- Reduce camera resolution for higher frame rates
- Increase GPU memory split: `sudo raspi-config → Advanced Options → Memory Split`
- Use faster SD card (Class 10 or better)

#### Network Access Issues
```bash
# Check Pi IP address
hostname -I

# Verify Flask is accessible
curl http://localhost:5000
```

For comprehensive troubleshooting, see [QUICK_START_GUIDE.md](QUICK_START_GUIDE.md#troubleshooting).

## 🏗 System Architecture

### Backend Stack
- **Flask**: Web framework and API server
- **Picamera2**: Raspberry Pi camera interface
- **OpenCV**: Computer vision and image processing
- **YOLO (Ultralytics)**: Deep learning drowsiness detection
- **pygame**: Audio alert system
- **ngrok**: Public URL tunneling

### Frontend Stack
- **HTML5**: Semantic markup and structure
- **CSS3**: Styling and responsive design
- **Bootstrap 5.3.5**: UI framework and components
- **JavaScript**: Client-side validation and interactivity

### AI/ML Pipeline
1. **Frame Capture**: Picamera2 captures RGB frames
2. **Face Detection**: OpenCV Haar cascades detect faces
3. **Preprocessing**: Resize and format for YOLO model
4. **Classification**: YOLO model predicts drowsiness state
5. **Alert System**: Audio alerts triggered based on classification
6. **Visualization**: Bounding boxes and labels overlaid on video

## 🤝 Contributing

We welcome contributions! Here's how to get started:

### Development Setup
1. Fork the repository
2. Create a feature branch: `git checkout -b feature-name`
3. Make your changes with proper documentation
4. Test thoroughly on Raspberry Pi hardware
5. Submit a pull request

### Contribution Guidelines
- Follow Python PEP 8 style guidelines
- Add comprehensive documentation for new features
- Include unit tests where applicable
- Update relevant documentation files
- Test on actual Raspberry Pi hardware

### Areas for Contribution
- **Backend**: User authentication, database integration
- **Frontend**: Mobile app development, UI improvements
- **AI/ML**: Model accuracy improvements, new detection features
- **Hardware**: Support for additional camera modules
- **Documentation**: Tutorials, examples, translations

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **OpenCV** community for computer vision tools
- **Ultralytics** for YOLO implementation
- **Raspberry Pi Foundation** for hardware platform
- **Flask** team for web framework
- **Bootstrap** team for UI components

## 📞 Support

### Getting Help
- **Documentation**: Check the comprehensive docs in this repository
- **Issues**: Report bugs via GitHub Issues
- **Discussions**: Join community discussions for questions

### Contact Information
For business inquiries or partnership opportunities, visit the contact page in the web interface.

---

**AwakeCam** - Making roads safer through intelligent drowsiness detection technology.