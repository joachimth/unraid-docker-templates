# Overview

This repo holds the XML and PNGs used for unRAID's [Community Apps](https://forums.unraid.net/topic/38582-plug-in-community-applications/) plugin. Apps are added to CA manually by the admin, and then references these templates and PNGs in its inventory for users to install.

# References

* Other plugin maintainer's XML files: https://github.com/linuxserver/docker-templates/tree/master/linuxserver.io https://github.com/dlandon/apps.plugins/ https://github.com/bergware/dynamix-plugins/ https://github.com/coppit/unraid-plugin-metadata
* Main unRAID forum post on Plugin Templates: https://forums.unraid.net/topic/42808-plugin-templates-for-ca-appstore/


# PhantomSDR-Plus Unraid Template

High-performance WebSDR template for Unraid Community Applications, supporting 100+ concurrent users with GPU acceleration.

## 🚀 Quick Installation

### Method 1: Community Applications (Recommended)
1. Install **Community Applications** plugin if not already installed
2. Go to **Apps** tab in Unraid WebUI
3. Search for "PhantomSDR-Plus"
4. Click **Install** and configure settings
5. Click **Apply** to download and start

### Method 2: Manual Template Installation
1. Download `phantomsdr-plus.xml` to your server
2. Place it in `/boot/config/plugins/dockerMan/templates-user/`
3. Go to **Docker** tab → **Add Container**
4. Select "PhantomSDR-Plus" from template dropdown

### Method 3: Private Template (Testing)
```bash
# Create private templates directory
mkdir -p /boot/config/plugins/community.applications/private/phantomsdr-plus

# Download template
wget -O /boot/config/plugins/community.applications/private/phantomsdr-plus/phantomsdr-plus.xml \
  https://raw.githubusercontent.com/joachimth/unraid-docker-templates/master/phantomsdr-plus.xml
```

## ⚙️ Required Configuration

### Essential Settings
- **Config Directory**: `/mnt/user/appdata/phantomsdr-plus/config`
- **Logs Directory**: `/mnt/user/appdata/phantomsdr-plus/logs`
- **WebUI Port**: `8080` (or your preferred port)
- **USB Access**: `/dev/bus/usb` (DO NOT CHANGE - required for SDR hardware)

### Hardware Detection
```bash
# Verify your SDR device is detected
lsusb | grep -E "(RTL|HackRF|Realtek)"

# Example output:
# Bus 001 Device 004: ID 0bda:2838 Realtek Semiconductor Corp. RTL2838 DVB-T
```

## 🔧 Advanced Configuration

### GPU Acceleration (Optional)
For handling 100+ concurrent users:

1. **Enable GPU** in container settings: `ENABLE_GPU=true`
2. **Install OpenCL** on your Unraid system:
   ```bash
   # For Intel GPUs
   /usr/bin/install-intel-opencl
   
   # For NVIDIA GPUs  
   # Install nvidia plugin via Community Applications
   ```

3. **Increase Max Users**: Set to `200` or higher for GPU setups

### Performance Tuning
```bash
# Create config file
mkdir -p /mnt/user/appdata/phantomsdr-plus/config
cat > /mnt/user/appdata/phantomsdr-plus/config/config.toml << EOF
[server]
port = 8080
max_users = 100
htmlroot = "/app/html"

[sdr] 
device = "rtlsdr"
frequency = 145000000
sample_rate = 3200000
gain = 40

[gpu]
enabled = false  # Set to true for GPU acceleration
device = "auto"

[logging]
level = "info"
file = "/app/logs/phantomsdr.log"
EOF
```

## 📊 Performance Expectations

| Hardware Setup | CPU Usage | Concurrent Users | Notes |
|----------------|-----------|------------------|-------|
| Ryzen 5 2600 (CPU Only) | 38-40% | 50-75 | RX888 @ 64MHz |
| Intel i5-6500T + GPU | 10-12% | 100+ | OpenCL enabled |
| High-end + GPU | 5-8% | 200+ | Optimal setup |

## 🛠️ Troubleshooting

### Common Issues

**SDR Device Not Found**
```bash
# Check USB permissions
ls -la /dev/bus/usb/

# Restart container with fresh USB access
docker restart phantomsdr-plus
```

**Web Interface Not Loading**
```bash
# Check container logs
docker logs phantomsdr-plus

# Verify port is not in use
netstat -tlnp | grep 8080
```

**High CPU Usage**
- Enable GPU acceleration if available
- Reduce `max_users` setting
- Check for multiple SDR devices conflicting

### Debug Mode
Enable detailed logging:
```bash
# Set environment variable
LOG_LEVEL=DEBUG
```

## 📁 File Structure

```
/mnt/user/appdata/phantomsdr-plus/
├── config/
│   ├── config.toml              # Main configuration
│   └── site_information.json    # Site metadata
├── logs/
│   └── phantomsdr.log          # Application logs
└── html/                       # Optional custom frontend
```

## 🔗 Links

- **GitHub Repository**: https://github.com/joachimth/PhantomSDR-Plus_docker
- **Original PhantomSDR-Plus**: https://github.com/Steven9101/PhantomSDR-Plus
- **Docker Hub**: https://hub.docker.com/r/joachimth/docker-phantomsdr-plus
- **Support Forum**: https://github.com/joachimth/PhantomSDR-Plus_docker/issues

## 📄 Template Standards

This template follows Unraid Community Applications best practices:

- ✅ **Support Link**: GitHub Issues for community support
- ✅ **Project Link**: Links to original PhantomSDR-Plus project  
- ✅ **Proper Categories**: Network:Other Tools:Utility MediaApp:Other
- ✅ **Overview vs Description**: Different content for each section
- ✅ **Icon**: Direct link to PNG icon file
- ✅ **Developer Profile**: Includes ca_profile.xml for maintainer info
- ✅ **Version Requirements**: Minimum Unraid 6.9.0
- ✅ **Security**: Non-privileged container with device access rules

## 🤝 Contributing

To contribute improvements to this template:

1. Fork the repository
2. Make your changes
3. Test on a local Unraid system
4. Submit a pull request

For template guidelines, see: https://forums.unraid.net/topic/38619-docker-template-xml-schema/
