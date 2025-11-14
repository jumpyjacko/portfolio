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

            function listener(event: AnimationEvent) {
                if (event.type === "animationend") {
                    moonButton.classList.add("hidden");
                    moonButton.classList.remove("roll-out");
                    sunButton.classList.remove("hidden");
                    sunButton.classList.add("roll-in");
                }

                moonButton.removeEventListener("animationend", listener);
            }
        } else {
            sunButton.addEventListener("animationend", listener);
            sunButton.classList.add("roll-out");

            function listener(event: AnimationEvent) {
                if (event.type === "animationend") {
                    sunButton.classList.add("hidden");
                    sunButton.classList.remove("roll-out");
                    moonButton.classList.remove("hidden");
                    moonButton.classList.add("roll-in");
                }

                sunButton.removeEventListener("animationend", listener);
            }
        }
    }
</script>

<button bind:this={sunButton} onclick={toggleTheme}>
    <img src="/icons/sun.svg" width="64" alt="Sun" class="hover-expand" />
</button>
<button bind:this={moonButton} onclick={toggleTheme}>
    <img src="/icons/moon.svg" width="64" alt="Moon" class="hover-expand" />
</button>

<style>
    .hover-expand { transition: all 0.5s; transform: rotate(45deg); }
    .hover-expand:hover { scale: 110%; }
</style>
