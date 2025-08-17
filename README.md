# Hyprland Installation Guide for Arch Linux (ThinkPad T480)

## Prerequisites & System Updates

```bash
# Update system first
sudo pacman -Syu

# Install base development tools
sudo pacman -S base-devel git
```

## 1. Install Hyprland and Core Dependencies

```bash
# Install Hyprland from official repos
sudo pacman -S hyprland

# Install essential Wayland components
sudo pacman -S wayland wayland-protocols xdg-desktop-portal-hyprland

# Install additional portal for better app compatibility
sudo pacman -S xdg-desktop-portal-gtk
```

## 2. Graphics Drivers (T480 specific)

The T480 typically has Intel integrated graphics, sometimes with NVIDIA discrete graphics:

```bash
# For Intel graphics (always present on T480)
sudo pacman -S mesa lib32-mesa vulkan-intel lib32-vulkan-intel

# If you have NVIDIA discrete graphics (check with: lspci | grep -i nvidia)
sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils nvidia-settings
```

## 3. Essential Applications

```bash
# Terminal emulator
sudo pacman -S kitty

# Application launcher
sudo pacman -S wofi

# Status bar
sudo pacman -S waybar

# Notification daemon
sudo pacman -S mako

# File manager
sudo pacman -S thunar

# Network manager applet
sudo pacman -S network-manager-applet

# Audio control
sudo pacman -S pavucontrol

# Screenshot tool
sudo pacman -S grim slurp

# Clipboard manager
sudo pacman -S wl-clipboard

# Bluetooth manager GUI
sudo pacman -S blueman

# Authentication agent for GUI apps requiring elevated privileges
sudo pacman -S polkit-gnome
```

## 4. Fonts and Themes

```bash
# Essential fonts
sudo pacman -S ttf-dejavu ttf-liberation noto-fonts noto-fonts-emoji

# Icon theme
sudo pacman -S papirus-icon-theme

# GTK theme
sudo pacman -S arc-gtk-theme
```

## 5. T480-Specific Optimizations

### Power Management

```bash
# Install TLP for better battery life
sudo pacman -S tlp tlp-rdw
sudo systemctl enable tlp
sudo systemctl start tlp

# Install thermald for thermal management
sudo pacman -S thermald
sudo systemctl enable thermald
sudo systemctl start thermald
```

### Audio Setup (T480 specific)

```bash
# Install audio packages
sudo pacman -S pipewire pipewire-alsa pipewire-pulse pipewire-jack wireplumber
sudo systemctl --user enable pipewire pipewire-pulse wireplumber

# Install additional audio tools
sudo pacman -S alsa-utils
```

### WiFi Setup

```bash
# NetworkManager (should already be installed, but ensure it's enabled)
sudo pacman -S networkmanager
sudo systemctl enable NetworkManager
sudo systemctl start NetworkManager

# WiFi utilities
sudo pacman -S iwd
```

### Bluetooth Setup

```bash
# Install Bluetooth packages
sudo pacman -S bluez bluez-utils blueman
sudo systemctl enable bluetooth
sudo systemctl start bluetooth

# Add user to bluetooth group
sudo usermod -a -G bluetooth $USER
```

## 6. Configuration Files Setup

```bash
# Create config directories
mkdir -p ~/.config/hypr
mkdir -p ~/.config/waybar
mkdir -p ~/.config/wofi
mkdir -p ~/.config/mako

# Copy default Hyprland config
cp /usr/share/hyprland/hyprland.conf ~/.config/hypr/
```

## 7. Basic Hyprland Configuration (T480 optimized)

Add this to `~/.config/hypr/hyprland.conf`:

```bash
# T480 specific settings
monitor=eDP-1,1920x1080@60,0x0,1

# Input configuration for T480 trackpad
input {
    kb_layout = us
    follow_mouse = 1
    
    touchpad {
        natural_scroll = true
        disable_while_typing = true
        tap-to-click = true
        drag_lock = true
        middle_button_emulation = false
        clickfinger_behavior = true
        scroll_factor = 1.0
    }
    
    sensitivity = 0
}

# Startup applications
exec-once = waybar
exec-once = mako
exec-once = nm-applet --indicator
exec-once = blueman-applet
exec-once = /usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1

# Auto-start fastfetch in terminal (optional)
# exec-once = kitty -e fastfetch

# Key bindings (basic set)
$mainMod = SUPER

bind = $mainMod, Q, exec, kitty
bind = $mainMod, C, killactive, 
bind = $mainMod, M, exit, 
bind = $mainMod, E, exec, thunar
bind = $mainMod, V, togglefloating, 
bind = $mainMod, R, exec, wofi --show drun
bind = $mainMod, P, pseudo,
bind = $mainMod, J, togglesplit,

# Move focus with mainMod + arrow keys
bind = $mainMod, left, movefocus, l
bind = $mainMod, right, movefocus, r
bind = $mainMod, up, movefocus, u
bind = $mainMod, down, movefocus, d

# Switch workspaces with mainMod + [0-9]
bind = $mainMod, 1, workspace, 1
bind = $mainMod, 2, workspace, 2
bind = $mainMod, 3, workspace, 3
bind = $mainMod, 4, workspace, 4
bind = $mainMod, 5, workspace, 5
bind = $mainMod, 6, workspace, 6
bind = $mainMod, 7, workspace, 7
bind = $mainMod, 8, workspace, 8
bind = $mainMod, 9, workspace, 9
bind = $mainMod, 0, workspace, 10

# Move active window to a workspace with mainMod + SHIFT + [0-9]
bind = $mainMod SHIFT, 1, movetoworkspace, 1
bind = $mainMod SHIFT, 2, movetoworkspace, 2
bind = $mainMod SHIFT, 3, movetoworkspace, 3
bind = $mainMod SHIFT, 4, movetoworkspace, 4
bind = $mainMod SHIFT, 5, movetoworkspace, 5
bind = $mainMod SHIFT, 6, movetoworkspace, 6
bind = $mainMod SHIFT, 7, movetoworkspace, 7
bind = $mainMod SHIFT, 8, movetoworkspace, 8
bind = $mainMod SHIFT, 9, movetoworkspace, 9
bind = $mainMod SHIFT, 0, movetoworkspace, 10

# Scroll through existing workspaces with mainMod + scroll
bind = $mainMod, mouse_down, workspace, e+1
bind = $mainMod, mouse_up, workspace, e-1

# Function keys for T480
bind = , XF86AudioRaiseVolume, exec, pactl set-sink-volume @DEFAULT_SINK@ +5%
bind = , XF86AudioLowerVolume, exec, pactl set-sink-volume @DEFAULT_SINK@ -5%
bind = , XF86AudioMute, exec, pactl set-sink-mute @DEFAULT_SINK@ toggle
bind = , XF86MonBrightnessUp, exec, brightnessctl set +10%
bind = , XF86MonBrightnessDown, exec, brightnessctl set 10%-
```

## 8. Additional Utilities for T480

```bash
# Brightness control
sudo pacman -S brightnessctl

# Battery monitoring
sudo pacman -S acpi

# System monitoring (using btop and fastfetch as requested)
sudo pacman -S btop fastfetch
```

## 9. Display Manager Setup (Login Screen)

```bash
# Install SDDM display manager
sudo pacman -S sddm

# Enable SDDM to start at boot
sudo systemctl enable sddm

# Create SDDM configuration
sudo mkdir -p /etc/sddm.conf.d
```

Create `/etc/sddm.conf.d/10-wayland.conf`:

```ini
[General]
DisplayServer=wayland
GreeterEnvironment=QT_WAYLAND_SHELL_INTEGRATION=layer-shell

[Wayland]
SessionDir=/usr/share/wayland-sessions
```

## 10. Configure Hyprland Session

```bash
# Verify Hyprland session file exists
ls /usr/share/wayland-sessions/hyprland.desktop

# If it doesn't exist, create it
sudo tee /usr/share/wayland-sessions/hyprland.desktop > /dev/null <<EOF
[Desktop Entry]
Name=Hyprland
Comment=An intelligent dynamic tiling Wayland compositor
Exec=Hyprland
Type=Application
EOF
```

## 11. Post-Installation Tweaks

### GTK Theme Configuration

Create `~/.config/gtk-3.0/settings.ini`:

```ini
[Settings]
gtk-theme-name=Arc-Dark
gtk-icon-theme-name=Papirus-Dark
gtk-font-name=DejaVu Sans 10
```

### Environment Variables

Add to `~/.config/hypr/hyprland.conf`:

```bash
env = XCURSOR_SIZE,24
env = QT_QPA_PLATFORM,wayland
env = QT_WAYLAND_DISABLE_WINDOWDECORATION,1
env = GDK_BACKEND,wayland,x11
env = MOZ_ENABLE_WAYLAND,1
env = QT_AUTO_SCREEN_SCALE_FACTOR,1
```

## Hardware Verification Commands

### Test Audio

```bash
# Test speakers
speaker-test -t wav -c 2

# List audio devices
pactl list short sinks
pactl list short sources

# Test microphone (record 5 seconds)
arecord -d 5 test.wav && aplay test.wav
```

### Test Bluetooth

```bash
# Check Bluetooth status
systemctl status bluetooth
bluetoothctl show

# Scan for devices (in bluetoothctl)
bluetoothctl
# Then run: scan on, devices, pair <MAC_ADDRESS>, connect <MAC_ADDRESS>
```

### Test WiFi

```bash
# Check WiFi status
nmcli device wifi list
nmcli connection show

# Connect to WiFi network
nmcli device wifi connect "SSID" password "password"
```

## Troubleshooting T480 Specific Issues

1. **Screen tearing**: Add `misc { vfr = true }` to Hyprland config
1. **NVIDIA issues**: If you have hybrid graphics, consider using `nvidia-prime`
1. **Trackpoint**: May need additional configuration in input section
1. **Docking station**: Configure additional monitors as needed
1. **Audio not working**: Ensure PipeWire services are running: `systemctl --user status pipewire pipewire-pulse`
1. **Bluetooth not working**: Check service status: `sudo systemctl status bluetooth`
1. **WiFi not working**: Restart NetworkManager: `sudo systemctl restart NetworkManager`

## Final Steps

1. **Reboot your system**: `sudo reboot`
1. **At the login screen**: Select Hyprland from the session menu (usually in the bottom-right corner)
1. **Login**: Enter your credentials and Hyprland will start automatically
1. **Test hardware**: Use the verification commands above to ensure audio, WiFi, and Bluetooth work
1. **Open terminal**: Press `Super + Q` and run `fastfetch` to see your system info
1. **Monitor system**: Press `Super + Q` and run `btop` to check system resources

## Useful Commands

```bash
# Check Hyprland version
hyprctl version

# Reload Hyprland config
hyprctl reload

# List active windows
hyprctl clients

# Monitor information
hyprctl monitors
```