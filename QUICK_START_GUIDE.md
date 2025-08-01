# AwakeCam Quick Start Guide

## Prerequisites Checklist

### Hardware Requirements
- [ ] Raspberry Pi (3B+ or newer recommended)
- [ ] Raspberry Pi Camera Module (v1, v2, v3, or HQ)
- [ ] MicroSD card (16GB minimum, 32GB recommended)
- [ ] Power supply for Raspberry Pi
- [ ] Speakers or headphones for audio alerts
- [ ] Network connection (Ethernet or WiFi)

### Software Requirements
- [ ] Raspberry Pi OS (Bullseye or newer)
- [ ] Python 3.8 or newer
- [ ] Internet connection for package installation

---

## Step 1: System Setup

### 1.1 Enable Camera Interface
```bash
sudo raspi-config
# Navigate to: Interface Options → Camera → Enable
sudo reboot
```

### 1.2 Update System
```bash
sudo apt update && sudo apt upgrade -y
```

### 1.3 Install System Dependencies
```bash
# Install ngrok for public URL tunneling
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null
echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list
sudo apt update && sudo apt install ngrok

# Install audio dependencies
sudo apt install alsa-utils pulseaudio -y
```

---

## Step 2: Project Setup

### 2.1 Clone/Download Project
```bash
# If using git
git clone <repository-url>
cd awakecam

# Or download and extract project files to a directory
```

### 2.2 Install Python Dependencies
```bash
# Create virtual environment (recommended)
python -m venv awakecam-env
source awakecam-env/bin/activate

# Install required packages
pip install flask picamera2 opencv-python pygame ultralytics requests

# Alternative: if requirements.txt exists
pip install -r requirements.txt
```

### 2.3 Verify Required Files
Ensure these files are in your project directory:
- [ ] `awakecam.py` - Main application
- [ ] `best.pt` - YOLO model for drowsiness detection
- [ ] `drowsiness-detected.mpeg` - Audio alert for drowsy state
- [ ] `sleep-detected.mpeg` - Audio alert for sleep state
- [ ] HTML files: `intro.html`, `main.html`, `register.html`, etc.
- [ ] CSS files: `dashboard.css`, `register.css`, etc.
- [ ] `raspberry-pi-logo.png` - Logo image

---

## Step 3: Configuration

### 3.1 Test Camera
```bash
# Test camera functionality
libcamera-hello --preview

# If camera works, you should see a preview window
# Press Ctrl+C to exit
```

### 3.2 Configure Audio
```bash
# List audio devices
aplay -l

# Test audio output
speaker-test -t wav -c 2

# If no sound, configure audio output
sudo raspi-config
# Navigate to: Advanced Options → Audio → Select output device
```

### 3.3 Setup ngrok (Optional for Remote Access)
```bash
# Sign up at https://ngrok.com and get your authtoken
ngrok config add-authtoken YOUR_AUTHTOKEN_HERE
```

---

## Step 4: Running the Application

### 4.1 Start the Application
```bash
# Navigate to project directory
cd /path/to/awakecam

# Activate virtual environment if using one
source awakecam-env/bin/activate

# Run the application
python awakecam.py
```

### 4.2 Expected Output
```
Ngrok Public URL: https://abc123.ngrok.io
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://192.168.1.100:5000
```

### 4.3 Access the Application
- **Local**: http://localhost:5000
- **Network**: http://[PI_IP_ADDRESS]:5000
- **Public**: Use the ngrok URL shown in output

---

## Step 5: Testing the System

### 5.1 Video Feed Test
1. Open web browser and navigate to application URL
2. You should see the main interface with live video feed
3. Position yourself in front of the camera
4. Verify that face detection works (green rectangle around face)
5. Check that drowsiness classification appears with confidence scores

### 5.2 Audio Alert Test
1. Simulate drowsy/sleep state in front of camera
2. Verify audio alerts play when drowsiness is detected
3. Check that alerts don't repeat too frequently (3-second minimum interval)

### 5.3 Web Interface Test
1. Navigate through different pages:
   - Landing page (`intro.html`)
   - Registration (`register.html`)
   - Contact page (`contact.html`)
   - Pricing page (`pricing.html`)
2. Test registration form validation
3. Verify responsive design on different screen sizes

---

## Step 6: Customization Options

### 6.1 Camera Settings
Edit `awakecam.py` to modify camera configuration:
```python
# Change resolution
preview_config = picam2.create_preview_configuration(
    main={"size": (800, 600), "format": 'RGB888'}  # Higher resolution
)

# Or lower resolution for better performance
preview_config = picam2.create_preview_configuration(
    main={"size": (480, 320), "format": 'RGB888'}  # Lower resolution
)
```

### 6.2 Detection Sensitivity
Adjust face detection parameters:
```python
# More sensitive detection
faces = face_cascade.detectMultiScale(gray, scaleFactor=1.05, minNeighbors=3)

# Less sensitive detection
faces = face_cascade.detectMultiScale(gray, scaleFactor=1.3, minNeighbors=8)
```

### 6.3 Alert Timing
Modify alert intervals:
```python
alert_interval = 5  # Change to 5 seconds between alerts
```

### 6.4 Network Configuration
Update video feed URL in `main.html`:
```html
<!-- Replace with your Pi's IP address -->
<img src="http://YOUR_PI_IP:5000/video_feed" width="640" height="480">
```

---

## Troubleshooting

### Common Issues and Solutions

#### Camera Not Working
**Problem**: Camera not detected or permission denied
**Solutions**:
```bash
# Check camera is enabled
sudo raspi-config → Interface Options → Camera

# Check camera detection
vcgencmd get_camera

# Should show: supported=1 detected=1

# Fix permissions
sudo usermod -a -G video $USER
sudo reboot
```

#### No Audio Output
**Problem**: Audio alerts not playing
**Solutions**:
```bash
# Check audio devices
aplay -l

# Test audio
speaker-test -t wav

# Configure audio output
sudo raspi-config → Advanced Options → Audio

# Install/reinstall audio packages
sudo apt install alsa-utils pulseaudio pavucontrol
```

#### Low Performance/Lag
**Problem**: Video feed is slow or laggy
**Solutions**:
1. Reduce camera resolution in `awakecam.py`
2. Increase GPU memory split:
   ```bash
   sudo raspi-config → Advanced Options → Memory Split → 128
   ```
3. Use faster SD card (Class 10 or better)
4. Ensure adequate power supply (3A for Pi 4)

#### Network Connection Issues
**Problem**: Can't access from other devices
**Solutions**:
```bash
# Check Pi's IP address
hostname -I

# Ensure Flask is running on all interfaces (0.0.0.0)
# This should already be set in awakecam.py

# Check firewall (usually not an issue on Pi OS)
sudo ufw status
```

#### ngrok Issues
**Problem**: ngrok tunnel not working
**Solutions**:
```bash
# Check ngrok installation
ngrok version

# Test ngrok manually
ngrok http 5000

# Check authtoken setup
ngrok config check

# Fallback: use local network access only
```

#### Model Loading Errors
**Problem**: YOLO model fails to load
**Solutions**:
1. Verify `best.pt` file exists in project directory
2. Check file permissions:
   ```bash
   ls -la best.pt
   chmod 644 best.pt
   ```
3. Re-download model file if corrupted
4. Check available disk space

---

## Performance Optimization

### For Better Frame Rate
1. **Lower Resolution**: Reduce camera resolution to 480x320 or 320x240
2. **Skip Frames**: Process every 2nd or 3rd frame for detection
3. **GPU Memory**: Increase GPU memory split to 128MB or 256MB
4. **Overclocking**: Enable moderate overclocking in `raspi-config`

### For Better Accuracy
1. **Higher Resolution**: Use 640x480 or higher camera resolution
2. **Better Lighting**: Ensure adequate lighting conditions
3. **Model Tuning**: Adjust detection thresholds
4. **Multiple Models**: Use ensemble of models for better accuracy

### For Lower Latency
1. **Direct Connection**: Use Ethernet instead of WiFi when possible
2. **Local Access**: Access via local network instead of ngrok
3. **Reduce Processing**: Skip unnecessary image processing steps
4. **Optimize Code**: Use more efficient algorithms

---

## Next Steps

### Development
1. **Backend Integration**: Implement user registration backend
2. **Database**: Add user management and logging
3. **Mobile App**: Create companion mobile application
4. **Cloud Integration**: Add cloud storage for alerts and data

### Production Deployment
1. **SSL Certificate**: Add HTTPS support for production
2. **Authentication**: Implement proper user authentication
3. **Monitoring**: Add system monitoring and logging
4. **Backup**: Implement data backup strategies

### Advanced Features
1. **Multiple Cameras**: Support for multiple camera feeds
2. **GPS Integration**: Add real GPS tracking functionality
3. **Machine Learning**: Improve drowsiness detection accuracy
4. **Integration**: Connect with vehicle systems

---

## Support

### Getting Help
1. **Documentation**: Refer to `API_DOCUMENTATION.md` and `COMPONENT_REFERENCE.md`
2. **Logs**: Check terminal output for error messages
3. **System Logs**: Use `journalctl` for system-level issues
4. **Community**: Search for similar issues online

### Reporting Issues
When reporting issues, include:
- Raspberry Pi model and OS version
- Python version and package versions
- Complete error messages
- Steps to reproduce the issue
- Hardware configuration details

---

This quick start guide should get you up and running with AwakeCam. For detailed technical information, refer to the comprehensive API documentation and component reference guides.