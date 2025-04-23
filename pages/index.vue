<template>
    <div class="flex w-full gap-14 bg-black">
        <HlsPlayer src="/video/kosher/kosher.m3u8" class="bg-white" />
        <video src="/mux/Kosher.mp4" type="video/mp4"
            class="w-[50%] h-full object-contain md:object-cover lg:object-cover "
            autoplay loop muted />
    </div>
</template>
