# Architecture

See the main README for the full system diagram.

## Data Flow

1. **Capture script** (Python, runs on Pi or connected machine)
   - Reads frames from picamera2 / OpenCV
   - Streams to Cloudflare RTMP
   - Uploads base64-encoded frame to `POST /api/image`

2. **Backend scheduler** (node-cron)
   - Every 10 min (day) / 4 hrs (night)
   - Fetches latest frame + sensor readings
   - Calls Grok Vision API
   - Parses JSON response
   - Executes GPIO actions via Pi REST API
   - Persists to PostgreSQL
   - Broadcasts update via WebSocket

3. **Frontend dashboard** (Astro + Svelte)
   - Connects to WebSocket on load
   - Receives live updates without polling
   - Renders health score, sensor data, charts, chat

## Scheduling Logic

```typescript
// Day: 10:00 UTC → 18:00 UTC (lights OFF window, least active)
// Night: 18:00 UTC → 10:00 UTC (lights ON window, growth active)

const isDaytime = () => {
  const hour = new Date().getUTCHours();
  return hour >= 10 && hour < 18;
};

cron.schedule(isDaytime() ? '*/10 * * * *' : '0 */4 * * *', runAnalysis);
```

## Database Schema

See `backend/src/db/schema.ts` for full Drizzle ORM definitions.

Tables: `sensor_readings`, `grok_analyses`, `commands`, `chat_messages`, `timelapse_images`
