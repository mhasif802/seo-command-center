# Serve Dashboard

Start a local HTTP server for `output/dashboard.html` and open it in the browser.

Usage: `/serve-dashboard`

## Steps

### 1. Kill any existing server on port 8888
Run:
```bash
fuser -k 8888/tcp 2>/dev/null || true
```

### 2. Start a background HTTP server
From the project root (`/root/seo-command-center`), start Python's built-in HTTP server on port 8888 in the background:
```bash
cd /root/seo-command-center && python3 -m http.server 8888 --bind 127.0.0.1 > /tmp/dashboard-server.log 2>&1 &
echo $! > /tmp/dashboard-server.pid
```

Wait 1 second, then verify the server is running:
```bash
sleep 1 && curl -s -o /dev/null -w "%{http_code}" http://127.0.0.1:8888/output/dashboard.html
```

If the HTTP status is 200, the server is up. If not, read `/tmp/dashboard-server.log` and report the error.

### 3. Output the result

Print:
```
Dashboard server running at:
  http://localhost:8888/output/dashboard.html

To stop the server: kill $(cat /tmp/dashboard-server.pid)
```

### 4. Notes
- The server serves the entire `/root/seo-command-center/` directory, so all relative assets (CSS, JS) referenced by `dashboard.html` will load correctly.
- The server stays running in the background for the duration of the session. It will stop when the terminal session ends.
- Re-running `/serve-dashboard` kills any previously started instance on port 8888 and starts a fresh one.
