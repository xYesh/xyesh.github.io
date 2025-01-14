---
{"tags":["docker","raspberrypi","syncthing"],"projects":null,"url":["https://www.tomshardware.com/reviews/raspberry-pi-headless-setup-how-to,6028.html"],"type":"Guide","Description":null,"Areas":"HowTos","publish":true,"PassFrontmatter":true,"created":"2025-01-14T15:25:42.680+05:30","updated":"2025-01-14T16:46:47.023+05:30"}
---

# Installing docker
[Installing Docker on the Raspberry Pi - Pi My Life Up](https://pimylifeup.com/raspberry-pi-docker/)

---
# Installing Syncthing
To run Syncthing as a systemd service, follow these steps:
### 1. **Check if Syncthing is Installed**
Ensure Syncthing is installed and accessible from your system's `$PATH`:
```bash
syncthing --version
```
### 2. **Set Up Syncthing as a Service**

#### For the Current User
1. Create a `systemd` service file in your user directory:
```bash
   mkdir -p ~/.config/systemd/user
   nano ~/.config/systemd/user/syncthing.service
```

2. Add the following content to the file:
```ini
   [Unit]
   Description=Syncthing - Open Source Continuous File Synchronization
   Documentation=https://docs.syncthing.net/
   After=network.target

   [Service]
   ExecStart=/usr/bin/syncthing serve --no-browser --home=/home/yesh/Documents/Syncthing/config
   Restart=on-failure
   SuccessExitStatus=3 4
   RestartForceExitStatus=3 4
   Environment=STNORESTART=yes

   [Install]
   WantedBy=default.target
```

   Adjust `/usr/bin/syncthing` if the binary is in a different location, and ensure the `--home` path matches your desired configuration directory.

3. Reload systemd to recognize the new service:
   ```bash
   systemctl --user daemon-reload
   ```

4. Enable the service to start at boot:
   ```bash
   systemctl --user enable syncthing
   ```

5. Start the service:
   ```bash
   systemctl --user start syncthing
   ```

6. Check the service status:
   ```bash
   systemctl --user status syncthing
   ```

7. Once syncthing is installed, go to the config folder specified above and enable UI from `0.0.0.0` instead of `127.0.0.1`. 

---

