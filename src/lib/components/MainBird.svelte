<script lang="ts">
    import { onDestroy, onMount } from "svelte";

    let mainBird: HTMLDivElement;
    let birdImg: HTMLImageElement;

    let angleX = $state("");
    let angleY = $state("");
    let scale = $state("");

    function updateTransform(transform: string) {
        birdImg.style.transform = transform;
    }

    function tilt(e: MouseEvent) {
        const rect = mainBird.getBoundingClientRect();
        const x = e.clientX - rect.left;
        const y = e.clientY - rect.top;

        const centerX = rect.width / 2;
        const centerY = rect.height / 2;

        const rotateY = ((x - centerX) / centerX) * 10;
        const rotateX = ((y - centerY) / centerY) * -10;

        angleX = `rotateX(${rotateX}deg)`;
        angleY = `rotateY(${rotateY}deg)`;
        scale = "scale(1.1)";
    }

    function reset(e: MouseEvent) {
        angleX = "rotateX(0)";
        angleY = "rotateY(0)";
        scale = "scale(1)";
    }

    function press(e: MouseEvent) {
        scale = "scale(0.9)";
    }

    function release(e: MouseEvent) {
        scale = "scale(1.1)";
    }

    onMount(() => {
        mainBird.addEventListener("mousemove", tilt);
        mainBird.addEventListener("mouseleave", reset);
        mainBird.addEventListener("mousedown", press);
        mainBird.addEventListener("mouseup", release);
    });

    onDestroy(() => {
        mainBird.removeEventListener("mousemove", tilt);
        mainBird.removeEventListener("mouseleave", reset);
        mainBird.removeEventListener("mousedown", press);
        mainBird.removeEventListener("mouseup", release);
    });

    $effect(() => {
        updateTransform(angleX + angleY + scale);
    })
</script>

<div bind:this={mainBird} class="main-bird mt-50 select-none">
    <img
        bind:this={birdImg}
        src="/images/birb-bg-no-bg.svg"
        alt="A monochrome, pixel-art bird"
        class="w-fit"
    />
</div>

<style>
    .main-bird {
        height: calc(1 / 4 * 100%);
        perspective: 1000px;
    }

    .main-bird img {
        transition: transform 0.2s ease;
        transform-style: preserve-3d;
        pointer-events: none;
    }
</style>

