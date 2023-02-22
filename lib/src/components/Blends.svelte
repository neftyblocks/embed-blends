<svelte:options tag="nefty-blend-group" />

<script lang="ts">
    import { useCountDown } from '@mvdschee/use';
    import { onMount } from 'svelte';
    import { getBlends, settings } from '../store';

    // PROPS
    export let blends = undefined;
    export let now = new Date().getTime();

    // STORES
    settings.subscribe(async ({ atomic_url, collection }) => {
        blends = await getBlends({
            atomic_url,
            collection,
        });
    });

    // METHODS
    onMount(() => {
        const interval = setInterval(() => {
            now = new Date().getTime();
        }, 1000);

        return () => clearInterval(interval);
    });

    const displayTime = (time, now, end = false) => {
        const countdown = useCountDown(time, now);

        if (countdown === '0') {
            return end ? 'no end' : 'live';
        } else {
            return end ? `ending ${countdown}` : countdown;
        }
    };
</script>

{#if blends}
    <div class="blends-group">
        {#each blends as blend}
            <div class="blends-item">
                <header>
                    <figure>
                        <img src={blend.image} alt={blend.name} />
                    </figure>
                    <article>
                        <time>
                            {displayTime(blend.start_time, now)} · {displayTime(
                                blend.end_time,
                                now,
                                true
                            )}
                        </time>
                        <h3>{blend.name}</h3>
                    </article>
                </header>
                <main>
                    <div class="requirments">
                        {#each blend.items as item}
                            <figure>
                                <img src={item.image} alt={item.name} />
                            </figure>
                        {/each}
                    </div>
                </main>
                <footer>
                    <button>Blend</button>
                </footer>
            </div>
        {/each}
    </div>
{:else if blends === null}
    <div>An error happend</div>
{:else}
    <div>loading...</div>
{/if}

<style lang="scss">
    @import '../global.scss';

    .blends-group {
        display: grid;
        grid-template-columns: repeat(
            auto-fill,
            minmax(var(--nb-card-size-min), var(--nb-card-size-max))
        );
        gap: var(--nb-gap);
    }

    .blends-item {
        display: flex;
        flex-direction: column;
        background-color: var(--nb-bg-card);
        border-radius: var(--nb-radius);
        border: var(--nb-border-size) solid var(--nb-border);
        margin-top: 80px;

        figure {
            overflow: hidden;
        }

        img {
            width: 100%;
            height: 100%;
            object-fit: contain;
        }

        time {
            color: var(--nb-color-secondary);
            font-size: var(--nb-font-size--small);
        }

        header {
            display: flex;
            align-items: center;
            padding: 12px 12px 12px 112px;
            position: relative;

            figure {
                width: 94px;
                height: 140px;
                position: absolute;
                top: -80px;
                left: 6px;
            }

            img {
                object-position: bottom;
            }
        }

        main {
            padding: 12px 0;
            margin: 6px 6px 0;
            border-top: var(--nb-border-size) solid var(--nb-border);
        }

        .requirments {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(60px, 1fr));
            gap: 5px;

            figure {
                width: 60px;
                height: 60px;
                padding: 2px;
                background-color: rgba(0, 0, 0, 0.2);
                border: var(--nb-border-size) dotted var(--nb-border-card);
            }
        }

        footer {
            padding: 12px 6px;
            margin-top: auto;
            background-color: var(--nb-bg-footer);
            border-radius: 0 0 var(--nb-radius) var(--nb-radius);
        }

        button {
            width: 100%;
            padding: 6px;
            border: none;
            border-radius: var(--nb-radius);
            background-color: var(--nb-bg-button);
            color: var(--nb-color-button);
            cursor: pointer;
        }
    }
</style>