<script lang="ts">
    import { onDestroy, onMount } from "svelte";
    import ThemeToggle from "./lib/components/ThemeToggle.svelte";

    let mainBird: HTMLDivElement;
    let birdImg: HTMLImageElement;

    function tilt(e: MouseEvent) {
        const rect = mainBird.getBoundingClientRect();
        const x = e.clientX - rect.left;
        const y = e.clientY - rect.top;

        const centerX = rect.width / 2;
        const centerY = rect.height / 2;

        const rotateY = ((x - centerX) / centerX) * 10;
        const rotateX = ((y - centerY) / centerY) * -10;

        birdImg.style.transform = `rotateX(${rotateX}deg) rotateY(${rotateY}deg) scale(1.10)`;
    }

    function reset(e: MouseEvent) {
        birdImg.style.transform = "rotateX(0) rotateY(0) scale(1)";
    }

    onMount(() => {
        mainBird.addEventListener("mousemove", tilt);
        mainBird.addEventListener("mouseleave", reset);
    });

    onDestroy(() => {
        mainBird.removeEventListener("mousemove", tilt);
        mainBird.removeEventListener("mouseleave", reset);
    });
</script>

<ThemeToggle />
<header class="fixed flex flex-row bg-transparent"></header>
<main class="flex flex-col justify-center items-center">
    <div bind:this={mainBird} class="main-bird mt-50">
        <img
            bind:this={birdImg}
            src="/images/birb-bg-no-bg.svg"
            alt="A monochrome, pixel-art bird"
            class="w-fit"
        />
    </div>
    <h1 class="text-4xl text-center mt-5">hi there</h1>
</main>

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
