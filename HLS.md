# HLS Video Streaming in Nuxt 3

This guide explains how to set up video streaming in a Nuxt 3 project using `.m3u8` files and `hls.js`.

## 1. **Convert MP4 to HLS (.m3u8)**

Use FFmpeg to split a large MP4 file into smaller `.ts` segments and generate an HLS playlist.

### **FFmpeg Command**
```sh
ffmpeg -i /path/to/video.mp4 -codec: copy -start_number 0 -hls_time 10 -hls_list_size 0 -f hls output.m3u8
```

### **Explanation**
- `-i /path/to/video.mp4` → Input file.
- `-codec: copy` → No re-encoding, keeps original quality.
- `-start_number 0` → Numbering starts from 0.
- `-hls_time 10` → Each segment is ~10 seconds.
- `-hls_list_size 0` → Keeps all `.ts` files in the playlist.
- `-f hls output.m3u8` → Outputs an HLS playlist.

## 2. **Example of the Generated `.m3u8` File**
```m3u8
#EXTM3U
#EXT-X-VERSION:3
#EXT-X-TARGETDURATION:10
#EXT-X-MEDIA-SEQUENCE:0
#EXTINF:10.000000,
easify-v0.ts
#EXTINF:10.000000,
easify-v1.ts
#EXTINF:10.000000,
easify-v2.ts
#EXTINF:4.666667,
easify-v3.ts
#EXT-X-ENDLIST
```

## 3. **Nuxt 3 Component for HLS Video Playback**
Create a reusable Vue component `HlsPlayer.vue` to play HLS videos.

```vue
<script setup>
import Hls from 'hls.js';
import { ref, onMounted } from 'vue';

const video = ref(null);
const props = defineProps({
    src: String, // Pass the .m3u8 video URL
});

onMounted(() => {
    if (Hls.isSupported()) {
        const hls = new Hls({
            maxBufferLength: 10,  // Reduce buffering to ~10s
            maxMaxBufferLength: 15,  // Prevents over-buffering
            liveSyncDuration: 5,  // Syncs closer to real playback
        });
        hls.loadSource(props.src);
        hls.attachMedia(video.value);
        hls.on(Hls.Events.MANIFEST_PARSED, () => {
            video.value.muted = true;  // Ensures autoplay
            video.value.loop = true;  // Enables looping
            video.value.play();
        });
    } else if (video.value.canPlayType('application/vnd.apple.mpegurl')) {
        video.value.src = props.src;
        video.value.muted = true;
        video.value.loop = true;
        video.value.addEventListener('loadedmetadata', () => {
            video.value.play();
        });
    }
});
</script>

<template>
    <div>
        <video ref="video" autoplay loop playsinline></video>
    </div>
</template>
```

## 4. **Usage in a Page**
Use the `HlsPlayer` component in your Nuxt 3 page.

```vue
<template>
  <div>
    <HlsPlayer src="/path/to/output.m3u8" />
  </div>
</template>
```

## 5. **Expected Behavior**
- **Autoplays** the video when the page loads.
- **Loops indefinitely**.
- **No controls** are displayed.
- Works on **desktop & mobile**.

## 6. **Troubleshooting**
- If autoplay doesn’t work, ensure the video is **muted**.
- If buffering is slow, adjust `maxBufferLength` in `Hls.js` settings.
- If the `.m3u8` file isn’t loading, check file paths and CORS policies.

---
This setup enables seamless HLS video streaming in Nuxt 3. 🚀

