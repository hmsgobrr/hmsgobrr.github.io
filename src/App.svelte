<script>
    import { onMount } from "svelte";

    // a bunch of variables defining the snow and how it falls
    const SNOWFLAKES_COUNT = 300;
    const SNOWFLAKE_MIN_SCALE = 0.1;
    const MELTING_SPEED = 1.12;
    const WIND_FORCE = 0.01;
    const FALL_SPEED = 0.15;
    // const SNOW_ICONS = ["❆", "❅", "❄"];
    const TARGET_FPS = 30;

    const MS_BETWEEN_FRAMES = 1000 / TARGET_FPS;

    // this function generates the random configuration with all necessary values
    function randomSnowflakeConfig(i) {
        return {
            scale: SNOWFLAKE_MIN_SCALE + Math.random() * (1 - SNOWFLAKE_MIN_SCALE),
            x: -20 + Math.random() * 120,
            y: -100 + Math.random() * 200,
            rotation: Math.floor(Math.random() * 360),
            // snowIcon: SNOW_ICONS[i % SNOW_ICONS.length],
            opacity: 0.999,
        };
    }

    // actially generating the snowflakes
    let snowflakes = new Array(SNOWFLAKES_COUNT)
        .fill()
        .map((_, i) => randomSnowflakeConfig(i))
        .sort((a, b) => a.scale - b.scale);

    // in onMount we define the loop function and start our animationFrame loop.
    onMount(async () => {
        let frame, lastTime;

        function loop(timestamp) {
            frame = requestAnimationFrame(loop);

            const elapsed = timestamp - lastTime;
            lastTime = timestamp;

            let framesCompleted = elapsed / MS_BETWEEN_FRAMES;

            if (isNaN(framesCompleted)) {
                framesCompleted = 1;
            }

            snowflakes = snowflakes.map((flake) => {
                if (flake.y >= 90) {
                    flake.opacity = Math.pow(flake.opacity, MELTING_SPEED);
                } else {
                    flake.y += FALL_SPEED * flake.scale * framesCompleted;
                    flake.x += WIND_FORCE * flake.scale * framesCompleted;
                    flake.rotation += 1 * framesCompleted;
                }
                if (flake.opacity <= 0.02) {
                    flake.y = -20;
                    flake.x = -20 + Math.random() * 120;
                    flake.opacity = 0.999;
                }
                return flake;
            });
        }

        loop();

        return () => cancelAnimationFrame(frame);
    });
</script>

<div class="snowframe" aria-hidden="true">
    {#each snowflakes as flake}
        <img
            src="res/doge.png"
            alt="doge.png"
            class="snowflake"
            style={`
				opacity: ${flake.opacity};
				transform: scale(${flake.scale / 16}) rotate(${flake.rotation}deg);
				left: ${flake.x}%;
				top: calc(${flake.y - 20}% - ${flake.scale}rem)
			`}
        />
    {/each}
</div>

<main class="vh-100">
    <nav class="navbar navbar-dark bg-dark">
        <div class="container-fluid">
            <span class="navbar-brand mb-0 h1 mx-auto">HmsGoBrr</span>
        </div>
    </nav>
    <div
        class="mid d-flex justify-content-start flex-column position-absolute top-50 start-50 translate-middle"
    >
        <h1>Make Games and Random Things For Fun</h1>
        <div class="links d-flex justify-content-between mt-3">
            <a href="https://github.com/hmsgobrr/" target="_blank">
                <button type="button" class="btn btn-light">
                    Github
                    <img src="res/github.png" alt="github.png" width="20" height="20" />
                </button>
            </a>
            <a href="https://hmsgobrr.itch.io/" target="_blank">
                <button type="button" class="btn btn-light">
                    itch.io
                    <img
                        class="itchio"
                        src="res/itchio.png"
                        alt="itchio.png"
                        width="20"
                        height="20"
                    />
                </button>
            </a>
        </div>
    </div>
</main>

<style>
    @import url("https://fonts.googleapis.com/css2?family=Quicksand:wght@600&display=swap");

    :global(body) {
        min-height: 100%;
    }

    :global(html) {
        height: 100%;
    }

    main {
        font-family: "Quicksand", sans-serif;
        z-index: 100;
    }

    a {
        width: max-content;
    }

    .itchio {
        filter: brightness(0%);
    }

    nav {
        letter-spacing: 2px;
    }

    .mid {
        width: 360px;
    }

    .snowframe {
        position: absolute;
        top: 0;
        right: 0;
        bottom: 0;
        left: 0;
        overflow: hidden;
    }

    .snowflake {
        position: absolute;
        z-index: 0;
        overflow: hidden;
    }
</style>
