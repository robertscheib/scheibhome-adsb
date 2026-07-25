# Plan: Configure and Enable VHF Voice Airband (Option 1)

We will configure the `airband` service inside `/opt/adsb/docker-compose.yml` to run in scanner mode, targeting the local airport frequencies for Bolingbrook's Clow International Airport (1C5).

### 1. Frequencies to Monitor
Since Clow International Airport is General Aviation, traffic is sporadic. We will scan a cluster of local frequencies to maximize the chance of hearing transmissions:
* **`122.900 MHz`**: Clow CTAF / Advisory (Local traffic calls)
* **`122.975 MHz`**: Clow UNICOM (Airport operations)
* **`119.350 MHz`**: Chicago Approach / Departure (Highly active air traffic control for the region)
* **`126.675 MHz`**: Lewis University Airport AWOS (Automated weather broadcast)

### 2. Proposed Changes to `docker-compose.yml`
We will remove the `donotstart` profile from the `airband` service, switch to automated configuration mode, configure the frequencies, and bind it to the `978` SDR:

```diff
   airband:
-    profiles:
-      - donotstart
     image: ghcr.io/sdr-enthusiasts/docker-rtlsdrairband:latest_nohealthcheck
     container_name: airband
     restart: unless-stopped
     device_cgroup_rules:
       - 'c 189:* rwm'
     ports:
       - 8000:8000
     environment:
-      - RTLSDRAIRBAND_CUSTOMCONFIG=true
+      - RTLSDRAIRBAND_CUSTOMCONFIG=false
+      - RTLSDRAIRBAND_MODE=scan
+      - RTLSDRAIRBAND_FREQS=122.900,122.975,119.350,126.675
+      - RTLSDRAIRBAND_SERIAL=978
+      - ICECAST_SOURCE_PASSWORD=${ICECAST_SOURCE_PASSWORD}
+      - ICECAST_ADMIN_PASSWORD=${ICECAST_ADMIN_PASSWORD}
     volumes:
-      - /opt/adsb/rtlsdr-airband:/run/rtlsdr-airband
       - /dev/bus/usb:/dev/bus/usb
```

---

### 3. Execution & Verification Steps
1. **Apply the docker-compose changes** inside `/home/robert/projects/ads-b/docker-compose.yml`.
2. **Synchronize changes** to the DietPi guest VM (`10.10.1.71`).
3. **Stop `dump978`** since we are shifting the secondary `978` SDR to VHF Airband:
   ```bash
   docker compose stop dump978
   ```
4. **Start the `airband` service**:
   ```bash
   docker compose up -d airband
   ```
5. **Verify logs**:
   Ensure `rtlsdr-airband` successfully claims the `978` SDR and connects to the integrated Icecast server.
6. **Listening In:**
   You can listen to the live stream directly from your web browser or audio players (VLC, etc.) by navigating to:
   * **Icecast Dashboard:** `http://10.10.1.71:8000/`
   * **Live Stream:** Typically at `http://10.10.1.71:8000/stream.mp3` or similar mountpoint listed on the dashboard.
