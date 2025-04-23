<script setup>
import Hls from 'hls.js';

const video = ref(null);
const props = defineProps({
    src: String, // Pass the .m3u8 video URL
});

onMounted(() => {
    if (Hls.isSupported()) {
        const hls = new Hls({
            maxBufferLength: 10,  // Reduce buffering to ~10s of video
            maxMaxBufferLength: 15,  // Prevents over-buffering
            liveSyncDuration: 5,  // Syncs closer to real playback (for live streams)
        });
        hls.loadSource(props.src);
        hls.attachMedia(video.value);
        hls.on(Hls.Events.MANIFEST_PARSED, () => {
            video.value.muted = true;  // Ensure autoplay works without user interaction
            video.value.loop = true;  // Ensure looping
            video.value.play();
        });
    } else if (video.value.canPlayType('application/vnd.apple.mpegurl')) {
        video.value.src = props.src;
        video.value.muted = true;  // Ensure autoplay works without user interaction
        video.value.loop = true;
        video.value.addEventListener('loadedmetadata', () => {
            video.value.play();
        });
    }
});

const unmute = () => {
    video.value.muted = false;
};
</script>

<template>
    <div>
        <video ref="video" autoplay loop playsinline></video>
        <button @click="unmute">Unmute</button>
    </div>
</template>
