<script lang="ts">
    import { onMount } from "svelte";
    import html2canvas from "html2canvas";

    type Tile = {
        on: boolean;
        x: number;
        y: number;
        rotation: number;
        color: string;
    };

    const WIDTH = 1584;
    const HEIGHT = 396;
    const CELL_SIZE = 33;
    const ICON_COUNT = 36;

    let banner: HTMLDivElement;
    let tiles: Tile[] = [];

    // Generate a random color in rgb(255, 255, 255) format
    const randomColor = () => {
        const randomInt = () => Math.floor(Math.random() * 255);
        const r = randomInt();
        const g = randomInt();
        const b = randomInt();
        return `rgb(${r},${g},${b})`;
    };

    // Get a random icon from the icons folder
    const randomIcon = (i: number, j: number) => {
        const ix = Math.floor(Math.random() * ICON_COUNT) + 1;
        const name = "/icons/" + ix + ".svg";
        return name;
    };

    // Generate a tile for the board
    const newTile = (i: number, j: number) => {
        const tile = {
            on: Math.random() > 0.85,
            x: i * CELL_SIZE,
            y: j * CELL_SIZE,
            rotation: 0,
            color: randomIcon(i, j),
        };
        return tile;
    };

    // Uses html2canvas to save the generated banner
    const saveBanner = () => {
        html2canvas(banner).then((canvas) => {
            const link = document.createElement("a");
            link.setAttribute("download", "banner.png");
            link.setAttribute(
                "href",
                canvas
                    .toDataURL("image/png")
                    .replace("image/png", "image/octet-stream")
            );
            link.click();
        });
    };

    onMount(() => {
        for (let i = 1; i < WIDTH / CELL_SIZE - 1; i++) {
            for (let j = 1; j < HEIGHT / CELL_SIZE - 1; j++) {
                const tile = newTile(i, j);
                tiles = [...tiles, tile];
            }
        }
    });
</script>

<div class="container">
    <div
        class="banner"
        bind:this={banner}
        style="width: {WIDTH}px; height: {HEIGHT}px"
    >
        <div
            class="clutter-container"
            style="width: {WIDTH}px; height: {HEIGHT}px"
        >
            {#each tiles as tile}
                {#if tile.on}
                    <img
                        class="tile"
                        src={tile.color}
                        width={CELL_SIZE}
                        height={CELL_SIZE}
                        style="left: {tile.x}px; top: {tile.y}px; transform: rotate({tile.rotation}deg)"
                    />
                {/if}
            {/each}
        </div>
    </div>
    <button on:click={() => saveBanner()}>Save Banner</button>
</div>

<style>
    .banner {
        position: relative;
    }
    .clutter-container {
        position: absolute;
        top: 0;
        left: 0;
        background-color: #33293a;
    }
    .tile {
        position: absolute;
        color: white;
    }
</style>
