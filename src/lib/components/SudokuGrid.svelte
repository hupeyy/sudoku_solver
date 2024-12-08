<script lang="ts">
    import { Input } from "$lib/components/ui/input/index.js"
    let grid: number[][] = Array(9).fill(0).map(() => Array(9).fill(0));
</script>

<style lang="postcss">
    .sudoku-grid {
        @apply inline-grid grid-cols-9 border border-black;
    }
    .cell {
        @apply w-20 h-20 flex items-center justify-center border border-gray-300;
    }

    .cell:nth-child(3n) {
        @apply border-b-2 border-b-black;
    }

    .row:nth-child(3n) .cell {
        @apply border-r-2 border-r-black;
    }
</style>

<div class="sudoku-grid">
    {#each grid as row, i}
        <div class="row">
            {#each row as cell, j}
                <div class="cell">
                    <input bind:value={grid[i][j]} class="w-full h-full text-center" />
                </div>
            {/each}
        </div>
    {/each}
</div>