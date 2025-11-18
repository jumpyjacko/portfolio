<script lang="ts">
    import { onMount } from "svelte";
    import { theme } from "../state/theme.svelte";

    let sunButton: HTMLButtonElement;
    let moonButton: HTMLButtonElement;

    onMount(() => {
        const notCurrentButton = theme.isDark ? sunButton : moonButton;
        notCurrentButton.classList.add("hidden");
    });

    function toggleTheme() {
        theme.isDark = !$state.snapshot(theme.isDark);
        localStorage.theme = theme.isDark ? "dark" : "light";

        document.documentElement.classList.toggle(
            "dark",
            localStorage.theme === "dark" ||
                (!("theme" in localStorage) &&
                    window.matchMedia("(prefers-color-scheme: dark)").matches),
        );

        if (!theme.isDark) {
            moonButton.addEventListener("animationend", listener);
            moonButton.classList.add("roll-out");
            moonButton.classList.add("pointer-events-none");
            sunButton.classList.add("pointer-events-none");

            function listener(event: AnimationEvent) {
                if (event.type === "animationend") {
                    moonButton.classList.add("hidden");
                    moonButton.classList.remove("roll-out");
                    sunButton.classList.remove("hidden");
                    sunButton.classList.add("roll-in");
                    moonButton.classList.remove("pointer-events-none");
                    sunButton.classList.remove("pointer-events-none");
                }

                moonButton.removeEventListener("animationend", listener);
            }
        } else {
            sunButton.addEventListener("animationend", listener);
            sunButton.classList.add("roll-out");
            sunButton.classList.add("pointer-events-none");
            moonButton.classList.add("pointer-events-none");

            function listener(event: AnimationEvent) {
                if (event.type === "animationend") {
                    sunButton.classList.add("hidden");
                    sunButton.classList.remove("roll-out");
                    moonButton.classList.remove("hidden");
                    moonButton.classList.add("roll-in");
                    moonButton.classList.remove("pointer-events-none");
                    sunButton.classList.remove("pointer-events-none");
                }

                sunButton.removeEventListener("animationend", listener);
            }
        }
    }
</script>

<button bind:this={sunButton} onclick={toggleTheme}>
    <img src="/icons/sun.svg" alt="Sun" class="theme-button w-16 select-none" />
</button>
<button bind:this={moonButton} onclick={toggleTheme}>
    <img src="/icons/moon.svg" alt="Moon" class="theme-button w-16 select-none" />
</button>

<style>
    .theme-button {
        transition: all 0.5s;
        transform: translateY(-25%) rotate(45deg);

        @media (width >= 48rem) {
            transform: translateY(10%) rotate(45deg);
        }
        @media (width >= 64rem) {
            transform: translateY(20%) rotate(45deg);
        }
    }
    .theme-button:hover {
        scale: 110%;
    }

    :global {
        .roll-in,
        .roll-out {
            animation-duration: 250ms;
            animation-timing-function: ease-out;
            animation-fill-mode: forwards;
        }

        .roll-in {
            animation-name: roll-in;
        }

        .roll-out {
            animation-name: roll-out;
        }

        @keyframes roll-in {
            from {
                transform: translateX(-40px) rotate(-0.25turn);
            }
            to {
                transform: translateX(0) rotate(0turn);
            }
        }

        @keyframes roll-out {
            from {
                transform: translateX(0) rotate(0turn);
            }
            to {
                transform: translateX(-40px) rotate(-0.25turn);
            }
        }
    }
</style>
