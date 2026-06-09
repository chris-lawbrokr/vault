## POPUPS

```bash
caddy run
```

```bash
http://testing.localhost/test/test-clip.html
```

```bash
http://testing.localhost/test/test-popup.html
```

```bash
https://www.figma.com/design/DToWrIpxXc4pW8RmrLErQX/Lawbrokr-Figma-Board?node-id=23105-3658&t=F1Lao4HH3TV84wPi-0
```
## Popup testing routes

```bash
http://testing.localhost/test/test-popup.html
```

```bash
http://testing.localhost/test/test-popup-api.html
```

```bash
popup btn
https://crimeinjurylaw.lawbrokr.com/criminal-defense?engaged_popup=popup_123456789&gclid=test123&utm_source=google_ads&utm_medium=cpc&landed_url=http%3A%2F%2Ftesting.localhost%2Ftest%2Fcrimeandinjurylaw.html%3Fgclid%3Dtest123&engaged_url=http%3A%2F%2Ftesting.localhost%2Ftest%2Fcrimeandinjurylaw.html

popup btn
?engaged_popup=popup_123456789
&gclid=test123&utm_source=google_ads&utm_medium=cpc&landed_url=http%3A%2F%2Ftesting.localhost%2Ftest%2Fcrimeandinjurylaw.html%3Fgclid%3Dtest123&engaged_url=http%3A%2F%2Ftesting.localhost%2Ftest%2Fcrimeandinjurylaw.html

non popup btn
gclid=test123&utm_source=google_ads&utm_medium=cpc&landed_url=http%3A%2F%2Ftesting.localhost%2Ftest%2Fcrimeandinjurylaw.html%3Fgclid%3Dtest123&engaged_url=http%3A%2F%2Ftesting.localhost%2Ftest%2Fcrimeandinjurylaw.html
```
## QA

x - add html directly to the dom then (reference - injectVideoPlayerHTML)
x - add a 3 second delay
### Testing flow

```bash
cmnd Y - purge all saved data
```

Use 
```bash
<script src="/dist/lawbrokr.min.js" type="text/javascript"></script>
```

Run
```
npm run build
```

Restart Caddy
```
caddy run
```

Open 
```
http://testing.localhost/test/test-popup-api.html?gclid=test123
```

---

06092026 - Investigate pop ups issues notes

https://app.clickup.com/t/9003176642/86e1rtdmx

Honeybadger error noise reduction - clip configuration errors
These config cases now `console.warn` locally instead of reporting to Honeybadger:
- `src/clips.js` - missing `video_url` warns and throws without calling `reportError`
- `src/clips.js` - a 204 (No Content) response throws "Clip has no content" without reporting
- `src/lawbrokr.js` - the clip init `.catch()` skips `reportError` when the error
message is a known config issue ("Clip has no content" / "Clip has no video URL")

HLS errors
- `src/clips.js` - the HLS `ERROR` handler (`data.fatal` branch) warns instead of
calling `reportError`; the network/media recovery logic is unchanged



