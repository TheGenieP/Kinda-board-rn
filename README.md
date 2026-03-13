# Video Downloader

A secure video downloader service with authentication and YouTube bot detection bypass.

## Features

- 🔒 Session-based authentication with bcrypt password hashing
- 🎥 Downloads videos from YouTube and other platforms
- 🔄 Auto-fallback to Invidious for YouTube bot detection
- 🍪 Optional cookie support for private/age-restricted videos
- 💾 Persistent session storage
- 🔐 Environment variable configuration

## Setup

### Render Secret File Setup

1. Create a file named `credentials.json` with your credentials:
   ```json
   {
     "username": "your_username",
     "password": "your_password"
   }
   ```

2. On Render.com:
   - Go to your service dashboard
   - Navigate to **"Secret Files"** section
   - Click **"Add Secret File"**
   - Set **Filename:** `/etc/secrets/credentials`
   - Paste the contents of your `credentials.json`
   - Click **"Save"**

3. Redeploy your service

The password will be automatically hashed with bcrypt on startup.

### Default Credentials

If no secret file is configured:
- **Username:** `thepythoncoder6` (case insensitive)
- **Password:** `Qwertyuiop!`

⚠️ **Change these for production using Render Secret Files!**

### ProxyScrape Setup (Optional)

You can use ProxyScrape as your primary proxy source for YouTube fallback.

- Set environment variable: `PROXYSCRAPE_API_KEY=<your_key>` **or** add a Render Secret File at `/etc/secrets/proxyscrape_api_key` containing only the key.
- If neither is set, the app uses multiple free sources (ProxyScrape + GeoNode + Proxy-List.download) automatically.

Optional controls:
- For the second-resort service, this app is compatible with `https://github.com/zararashraf/youtube-video-downloader-api.git` once you deploy it and set the URL env var.
- If YouTube keeps blocking, open the video in CroxyProxy and export fresh YouTube cookies into the app.
- `PREFER_FREE_PROXIES=true` (default) to try free proxies (ProxyScrape/GeoNode/Proxy-List.download) before Webshare
- `MAX_PROXY_ATTEMPTS=5` to cap retries per proxy source
- `SECONDARY_DOWNLOADER_API=https://<your-deployed-youtube-video-downloader-api>` to attempt a secondary service after yt-dlp + proxies fail
- `YOUTUBE_VIDEO_DOWNLOADER_API_URL=https://<your-deployed-youtube-video-downloader-api>` (alias env name for the same fallback)

## Installation

```bash
pip install -r requirements.txt
```

## Run

```bash
bash start.sh
```

Or manually:

```bash
uvicorn server:app --host 0.0.0.0 --port 8000
```

## Security Features

- ✅ Bcrypt password hashing (not plaintext)
- ✅ Secure httponly session cookies
- ✅ Render Secret Files for credential storage
- ✅ Persistent session storage across restarts
- ✅ 24-hour session expiry
- ✅ Server-side authentication validation

## API Endpoints

- `POST /api/login` - Login with username/password
- `POST /api/logout` - Logout and clear session
- `POST /api/download` - Download video (requires auth)
- `GET /api/check-auth` - Check authentication status
- `GET /api/file/{filename}` - Download file

## Usage

1. Navigate to the site
2. Login with credentials
3. Paste video URL
4. (Optional) Add cookies for YouTube if needed
5. Click Download
6. Download the file

## YouTube Bot Detection

If YouTube blocks the download:
1. The service automatically tries Invidious fallback
2. If that fails, you can provide YouTube cookies in Netscape format
3. See [yt-dlp cookie guide](https://github.com/yt-dlp/yt-dlp/wiki/FAQ#how-do-i-pass-cookies-to-yt-dlp)
