<script lang="ts">
    import { onMount } from "svelte";

    let isDark = $state(false);

    let sunButton: HTMLButtonElement;
    let moonButton: HTMLButtonElement;

    onMount(() => {
        // HACK: fixes site starting on wrong state
        isDark = localStorage.theme !== "dark";

        const currentButton = isDark ? moonButton : sunButton;
        currentButton.classList.add("hidden");
    });

    function toggleTheme() {
        isDark = localStorage.theme === "dark";
        localStorage.theme = isDark ? "light" : "dark";

        document.documentElement.classList.toggle(
            "dark",
            localStorage.theme === "dark" ||
                (!("theme" in localStorage) &&
                    window.matchMedia("(prefers-color-scheme: dark)").matches),
        );

        if (isDark) {
            moonButton.addEventListener("animationend", listener);
            moonButton.classList.add("slide-out");

            function listener(event: AnimationEvent) {
                if (event.type === "animationend") {
                    moonButton.classList.add("hidden");
                    moonButton.classList.remove("slide-out");
                    sunButton.classList.remove("hidden");
                    sunButton.classList.add("slide-in");
                }

                moonButton.removeEventListener("animationend", listener);
            }
        } else {
            sunButton.addEventListener("animationend", listener);
            sunButton.classList.add("slide-out");

            function listener(event: AnimationEvent) {
                if (event.type === "animationend") {
                    sunButton.classList.add("hidden");
                    sunButton.classList.remove("slide-out");
                    moonButton.classList.remove("hidden");
                    moonButton.classList.add("slide-in");
                }

                sunButton.removeEventListener("animationend", listener);
            }
        }
    }
</script>

<button
    bind:this={sunButton}
    onclick={toggleTheme}
    class="absolute"
>
    <img src="/icons/sun.svg" width="64" alt="Sun"/>
</button>
<button
    bind:this={moonButton}
    onclick={toggleTheme}
    class="absolute"
>
    <img src="/icons/moon.svg" width="64" alt="Moon" />
</button>
