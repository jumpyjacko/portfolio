<script lang="ts">
    import MainBird from "./lib/components/MainBird.svelte";
    import Clock from "./lib/components/Clock.svelte";
    import SocialsRow from "./lib/components/SocialsRow.svelte";

    import { theme } from "./lib/state/theme.svelte";
    import { onMount } from "svelte";

    let scrollIndicator: SVGElement;

    let textColour = $derived(
        theme.isDark
            ? "oklch(0.9644 0.0282 74.31)"
            : "oklch(0.2316 0.0373 288.04)",
    );

    function removeScrollIndicator() {
        scrollIndicator.classList.remove("animate-bounce");
        scrollIndicator.classList.add("slide-down");
        window.removeEventListener("scroll", removeScrollIndicator);
    }

    onMount(() => {
        window.addEventListener("scroll", removeScrollIndicator);
    });
</script>

<main class="flex flex-col justify-center items-center">
    <Clock />
    <MainBird />
    <h1 class="text-2xl md:text-4xl text-center mt-5">
        hi, i'm jackson,<br />a software developer
    </h1>

    <div class="h-8"></div>
    <SocialsRow />

    <p class="mt-100 mb-10">
        theres no content here yet...
    </p>
</main>
<footer class="w-full flex flex-row justify-end fixed bottom-0 p-1">
    <a href="https://github.com/jumpyjacko/portfolio" target="_blank" class="underline">
        source code
    </a>
</footer>
<div class="fixed bottom-0 w-full flex justify-center pb-1 z-[-1]">
    <svg
        bind:this={scrollIndicator}
        width="60"
        height="60"
        class="animate-bounce"
    >
        <path
            d="M15 15 L30 30 L45 15"
            stroke={textColour}
            stroke-width="2"
            fill="none"
        />
    </svg>
</div>

<style>
    :global {
        .slide-down {
            animation-duration: 250ms;
            animation-timing-function: ease-in-out;
            animation-fill-mode: forwards;
            animation-name: slide-down;
        }

        @keyframes slide-down {
            from {
                transform: translateY(0);
            }
            to {
                transform: translateY(50px);
            }
        }
    }
</style>
