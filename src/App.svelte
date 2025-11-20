<script lang="ts">
    import MainBird from "./lib/components/MainBird.svelte";
    import Clock from "./lib/components/Clock.svelte";
    import SocialsRow from "./lib/components/SocialsRow.svelte";

    import { onMount } from "svelte";

    let scrollIndicator: SVGElement;

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

    <p class="mt-100 mb-10">theres no content here yet...</p>
</main>
<footer class="hidden md:flex flex-row justify-end fixed bottom-0 right-0 p-1 z-1">
    <a
        href="https://github.com/jumpyjacko/jumpyjacko.github.io"
        target="_blank"
        class="underline"
    >
        source code
    </a>
</footer>
<button
    class="fixed bottom-0 w-full flex justify-center pt-4 pb-1"
    onclick={() => window.scrollBy(0, window.innerHeight / 2)}
    aria-label="Scroll to content"
>
    <svg
        bind:this={scrollIndicator}
        width="60"
        height="35"
        class="animate-bounce"
    >
        <path
            d="M15 15 L30 30 L45 15"
            stroke="currentColor"
            stroke-width="2"
            fill="none"
        />
    </svg>
</button>

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
