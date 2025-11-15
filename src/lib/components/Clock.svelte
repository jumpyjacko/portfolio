<script lang="ts">
    import { onDestroy, onMount } from "svelte";
    import ThemeToggle from "./ThemeToggle.svelte";
    import { theme } from "../state/theme.svelte";

    let textColour = $derived(
        theme.isDark
            ? "oklch(0.9644 0.0282 74.31)"
            : "oklch(0.2316 0.0373 288.04)",
    );

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
        marker.style.transform = `translateX(-32px) rotate(${angle}deg)`;
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

<div class="absolute top-0">
    <svg
        class="absolute z-[-1] w-auto h-[70vw] -translate-x-1/2 -translate-y-1/2"
        viewBox="-0 0 100 100"
    >
        <circle
            cx="50"
            cy="100"
            r="46"
            fill="transparent"
            stroke={textColour}
            stroke-width="0.5"
            stroke-dasharray="1,10,1"
        >
            <animateTransform
                attributeName="transform"
                begin="0s"
                dur="60s"
                type="rotate"
                from="0 50 100"
                to="360 50 100"
                repeatCount="indefinite"
            />
        </circle>
        <!-- <circle cx="50" cy="100.5" r="1" fill="magenta" /> -->
    </svg>
    <div bind:this={marker} class="clock">
        <ThemeToggle />
        <!-- <div -->
        <!--     class="absolute h-100 bg-blue-500 rounded-full clock-arm" -->
        <!-- ></div> -->
    </div>
    <!-- <div -->
    <!--     class="absolute w-2 h-2 bg-red-500 rounded-full" -->
    <!--     style="top: 35vw; left: 50%; transform: translate(-50%, -50%);" -->
    <!-- ></div> -->
</div>

<style>
    .clock {
        position: absolute;
        width: max-content;
        transform-origin: center 35vw;
        transform: translateX(-32px) rotate(-90deg);
        transition: transform 1s ease-in-out;
    }
</style>
