<script lang="ts">
    // Initialize a 9x9 grid with null values (representing empty cells)
    let grid: (number | null)[][] = Array(9).fill(null).map(() => Array(9).fill(null));
    let invalidMove = false;
    let win = false;

    // Sets to keep track of numbers in each row, column, and 3x3 subgrid
    let rows: Set<number>[] = Array(9).fill(null).map(() => new Set());
    let cols: Set<number>[] = Array(9).fill(null).map(() => new Set());
    let subgrids: Set<number>[] = Array(9).fill(null).map(() => new Set());

    function initializeAlmostWinningGrid() {
        return [
            [5, 3, 4, 6, 7, 8, 9, 1, 2],
            [6, 7, 2, 1, 9, 5, 3, 4, 8],
            [1, 9, 8, 3, 4, 2, 5, 6, 7],
            [8, 5, 9, 7, 6, 1, 4, 2, 3],
            [4, 2, 6, 8, 5, 3, 7, 9, 1],
            [7, 1, 3, 9, 2, 4, 8, 5, 6],
            [9, 6, 1, 5, 3, 7, 2, 8, null],
            [2, 8, 7, 4, 1, 9, 6, 3, 5],
            [3, 4, 5, 2, 8, 6, 1, 7, 9]
        ];
    }

    function populateSets() {
        for (let i = 0; i < 9; i++) {
            for (let j = 0; j < 9; j++) {
                const value = grid[i][j];
                if (value !== null) {
                    rows[i].add(value);
                    cols[j].add(value);
                    const subgridIndex = Math.floor(i / 3) * 3 + Math.floor(j / 3);
                    subgrids[subgridIndex].add(value);
                }
            }
        }
    }

    function isValidMove(row: number, col: number, value: number): boolean {
        if (value < 1 || value > 9) return false;

        const subgridIndex = Math.floor(row / 3) * 3 + Math.floor(col / 3);
        return !(rows[row].has(value) || cols[col].has(value) || subgrids[subgridIndex].has(value));
    }

    function checkWin() {
        for (let i = 0; i < 9; i++) {
            if (rows[i].size !== 9 || cols[i].size !== 9 || subgrids[i].size !== 9) {
                console.log('You lose!');
                return false;
            }
        }
        console.log('You win!');
        return true;
    }

    function handleInput(row: number, col: number, event: Event) {
        const input = event.target as HTMLInputElement;
        const value = parseInt(input.value);

        if (isNaN(value)) {
            // Clear the cell if input is not a number
            clearCell(row, col);
            return;
        }

        if (isValidMove(row, col, value)) {
            invalidMove = false;

            // Remove old value from sets if it exists
            clearCell(row, col);
            
            // Add new value to grid and sets
            grid[row][col] = value;
            rows[row].add(value);
            cols[col].add(value);
            const subgridIndex = Math.floor(row / 3) * 3 + Math.floor(col / 3);
            subgrids[subgridIndex].add(value);

            // Check for win
            win = checkWin();
        } else {
            invalidMove = true;
            // Revert input if move is invalid
            input.value = grid[row][col]?.toString() || '';
        }

        // Trigger reactivity
        grid = grid;
    }

    function clearCell(row: number, col: number) {
        const oldValue = grid[row][col];
        if (oldValue !== null) {
            rows[row].delete(oldValue);
            cols[col].delete(oldValue);
            const subgridIndex = Math.floor(row / 3) * 3 + Math.floor(col / 3);
            subgrids[subgridIndex].delete(oldValue);
        }
        grid[row][col] = null;
    }

    populateSets();
</script>

<style lang="postcss">
    .sudoku-grid {
        @apply inline-grid grid-cols-9 gap-0 border-2 border-black;
    }
    .cell {
        @apply w-12 h-12 border border-gray-300 flex items-center justify-center;
    }
    .cell:nth-child(3n) {
        @apply border-b-2 border-b-black;
    }
    .row:nth-child(3n) .cell {
        @apply border-r-2 border-r-black;
    }
    input {
		@apply w-full h-full text-center text-xl font-bold;
    }
</style>

<div class="sudoku-grid">
  {#each grid as row, i}
    <div class="row">
      {#each row as cell, j}
        <div class="cell">
          <input
            type="text"
            inputmode="numeric"
            pattern="[1-9]*"
            value={cell === null ? '' : cell}
            on:input={(e) => handleInput(i, j, e)}
          />
        </div>
      {/each}
    </div>
  {/each}
</div>

{#if invalidMove}
    <div class="text-red-500 font-bold text-xl">Invalid move!</div>
{/if}
{#if win}
  <div class="text-green-500 font-bold text-xl">You win!</div>
{/if}