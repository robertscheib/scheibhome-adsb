# Plan: Enable and Configure OpenWebRX

We will add the **OpenWebRX** service to the Docker Compose stack, allowing you to tune the secondary SDR (`978`) manually using an interactive web interface.

### 1. Stopping the Airband Scanner
Since you only have one secondary SDR, we must stop the `airband` container before running OpenWebRX so that they do not conflict over the hardware:
```bash
docker compose stop airband
```

### 2. Proposed Changes to `docker-compose.yml`
We will add the new `openwebrx` service:

```yaml
  openwebrx:
    image: jketterl/openwebrx:stable
    container_name: openwebrx
    restart: unless-stopped
    ports:
      - "8073:8073"
    devices:
      - "/dev/bus/usb:/dev/bus/usb"
    volumes:
      - /opt/adsb/openwebrx:/var/lib/openwebrx
    tmpfs:
      - /tmp/openwebrx
```

* **Port 8073:** Exposes the OpenWebRX web interface.
* **devices:** Passes through the host USB bus so it can claim the `978` SDR.
* **tmpfs:** Offloads high-frequency waterfall metadata writes to RAM to protect the SSD/SD card.

---

### 3. Execution & Verification Steps

1. **Apply the docker-compose changes** inside `/home/robert/projects/ads-b/docker-compose.yml`.
2. **Synchronize changes** to the DietPi guest VM (`10.10.1.71`).
3. **Stop `airband`** and start the new `openwebrx` container:
   ```bash
   docker compose stop airband
   docker compose up -d openwebrx
   ```
4. **Create Admin User:**
   To access the settings panel and restrict the container to the `978` SDR (ignoring the busy `1090` SDR), we will create a command-line admin account inside the container:
   ```bash
   docker exec -it openwebrx openwebrx admin adduser admin
   ```
   *(I will run this non-interactively and prompt for/assign a secure temporary password, or set it via a command line script).*
5. **Access the Web Interface:**
   Open your browser and navigate to:
   * **OpenWebRX Tuner:** `http://10.10.1.71:8073/`
