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

    let iWidth = $state(window.innerWidth);
    function updateWidth() {
        iWidth = window.innerWidth;
    }

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
        // timer
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

        // HACK: reactive window breakpoints for js
        window.addEventListener("resize", updateWidth);
    });

    onDestroy(() => {
        clearInterval(interval);
        window.removeEventListener("resize", updateWidth);
    });
</script>

<div class="absolute top-8 md:top-1">
    <div
        bind:this={marker}
        class="clock origin-[center_8rem] md:origin-[center_24rem] lg:origin-[center_28rem]"
    >
        <ThemeToggle />
    </div>
    <svg
        class="absolute z-[-1] w-3xs md:w-3xl lg:w-4xl h-auto -translate-x-1/2 orbit-cutout"
        viewBox="0 0 100 100"
    >
        <mask id="orbitMask" mask-type="luminance">
            <rect x="0" y="0" width="100" height="100" fill="white" />
            <rect
                x={iWidth >= 768 ? "45" : "35"}
                y="0"
                width={iWidth >= 768 ? "10" : "30"}
                height="50"
                fill="black"
                transform="rotate({angle} 50 50)"
                style="transition: transform 1s ease-in-out"
            />
            <circle cx="50" cy="100" r="60" fill="black" />
            <rect x="20" y="30" width="60" height="20" fill="white" />
        </mask>
        <g mask="url(#orbitMask)">
            <circle
                cx="50"
                cy="50"
                r="45"
                fill="transparent"
                stroke="currentColor"
                stroke-width="5"
                stroke-dasharray="10 100 10"
                vector-effect="non-scaling-stroke"
            >
                <animateTransform
                    attributeName="transform"
                    begin="0s"
                    dur="60s"
                    type="rotate"
                    from="0 50 50"
                    to="360 50 50"
                    repeatCount="indefinite"
                />
            </circle>

            <circle
                cx="50"
                cy="50"
                r="25"
                fill="transparent"
                stroke="currentColor"
                stroke-width="3"
                stroke-dasharray="20 80 50"
                vector-effect="non-scaling-stroke"
            >
                <animateTransform
                    attributeName="transform"
                    begin="0s"
                    dur="102s"
                    type="rotate"
                    from="0 50 50"
                    to="360 50 50"
                    repeatCount="indefinite"
                />
            </circle>
        </g>
    </svg>
</div>

<style>
    .clock {
        position: absolute;
        width: max-content;
        transform: translateX(-32px) rotate(-90deg);
        transition: transform 1s ease-in-out;
    }
</style>
