<template>
    <div class="flex w-full gap-14 bg-black">
        <div class="w-[50%]">
            <HlsPlayer src="/video/kosher/kosher.m3u8" class="w-full h-full object-contain md:object-cover lg:object-cover" />
            <div class="bg-black text-white">HLS video</div>
        </div>

        <div class="w-[50%]">
            <video src="/mux/Kosher.mp4" type="video/mp4"
            class="h-full object-contain md:object-cover lg:object-cover"
            autoplay loop muted controls />
            <div class="bg-black text-white w-ful">MP4 video</div>
        </div>
    </div>
</template>
