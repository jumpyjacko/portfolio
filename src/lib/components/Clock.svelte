<script lang="ts">
    import { onDestroy, onMount } from "svelte";
    import ThemeToggle from "./ThemeToggle.svelte";

    let marker: HTMLDivElement;

    let timeHours = $state(0);
    let timeMinutes = $state(0);
    let totalMinutes = $derived(timeHours * 60 + timeMinutes);

    let angle = $derived.by(() => {
        let a = totalMinutes;
        if (totalMinutes >= 720) a -= 720;
        return mapRange(a, 0, 720, -85, 85); // NOTE: consider radians
    });

    let interval: any;

    function mapRange(
        x: number,
        inMin: number,
        inMax: number,
        outMin: number,
        outMax: number,
    ): number {
        return ((x - inMin) * (outMax - outMin)) / (inMax - inMin) + outMin;
    }

    function getTime(hasTemporal: boolean): [number, number] {
        let h: number;
        let m: number;

        if (hasTemporal) {
            let now = Temporal.Now.plainTimeISO();
            h = now.hour;
            m = now.minute;
        } else {
            let legacyNow: Date = new Date();
            h = legacyNow.getHours();
            m = legacyNow.getMinutes();
        }

        return [h, m];
    }

    function updateMarker() {
        marker.style.transform = `rotate(${angle}deg)`;
    }

    onMount(() => {
        let hasTemporal: boolean = false;
        try {
            hasTemporal = Temporal.Now.instant() !== undefined;
        } catch (e) {
            console.warn(
                "Temporal API not implemented, falling back to Date API...",
            );
        }

        [timeHours, timeMinutes] = getTime(hasTemporal);
        updateMarker();
        interval = setInterval(() => {
            [timeHours, timeMinutes] = getTime(hasTemporal);
            updateMarker();
        }, 10000);
    });

    onDestroy(() => {
        clearInterval(interval);
    });
</script>

<div bind:this={marker} class="clock -top-1">
    <ThemeToggle />
</div>


<style>
    .clock {
        position: absolute;
        transform-origin: center 35vw;
        transform: rotate(-90deg);
        transition: transform 1s ease-in-out;
    }
</style>
