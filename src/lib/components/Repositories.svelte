<script lang="ts">
    import { onMount } from 'svelte';
    import { fetchRepository, type RepositoryData } from '../scripts/helpers';
  import Repository from './Repository.svelte';

    export let data: string[];

    let repositories: Promise<(RepositoryData|null)[]> = Promise.resolve([]);

    onMount(async () => {
        repositories = Promise.all(data.map(r => fetchRepository(r)));
    })
</script>

<div class="repositories">
    <h1>Repositories</h1>
    {#await repositories}
        <div class="skeleton">
            {#each data as _, i}
                <div class="skeleton-item" style="animation-delay: {i * 0.5}s;"></div>
            {/each}
        </div>
    {:then repositories}
        <div class="content">
            {#each repositories as repository}
                {#if repository}
                    <div class="repository-container">
                        <Repository data={repository}/>
                    </div>
                {/if}
            {/each}
        </div>
    {/await}
</div>

<style lang="scss">
    @import '$lib/styles/variables.scss';

    $width: 212px;
    $height: 150px;

    .repositories {
        display: flex;
        flex-direction: column;
        margin-bottom: 1rem;
        gap: 1rem;

        h1 {
            margin: 0;
            color: rgba($primary, $alpha: 0.8);
            font-size: 1.7rem;
        }

        .skeleton {
            display: flex;
            overflow: auto;
            gap: 1rem;

            .skeleton-item {
                flex-shrink: 0;
                height: $height;
                width: $width;
                background-color: rgba($secondary, $alpha: 0.1);
                border-radius: 1rem;
                animation: skeleton 1s infinite alternate;
            }
        }

        .content {
            display: flex;
            overflow: auto;
            gap: 1rem;

            .repository-container {
                width: $width;
                height: $height;
            }
        }
    }

    @keyframes skeleton {
        0% {
            background-color: rgba($secondary, $alpha: 0.1);
        }
        100% {
            background-color: rgba($secondary, $alpha: 0.15);
        }
    }
</style>