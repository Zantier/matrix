<script lang="ts">
  import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

  export let editable: boolean = true;
  export let matrix: number[][] = [];
  let selected_i: number | undefined = undefined;
  let selected_j: number | undefined = undefined;
  // Highlight specific cell
  export let highlight_i: number | undefined;
  export let highlight_j: number | undefined;
  export let highlight_height: number;
  export let highlight_width: number;
  // Highlight entire row or column
  export let highlight_row: number | undefined;
  export let highlight_col: number | undefined;

  function changeSelected(i: number | undefined, j: number | undefined) {
		dispatch('change', {
			i: i,
      j: j,
		});
  }

  function enter(i: number, j: number) {
    selected_i = i;
    selected_j = j;
    changeSelected(selected_i, selected_j);
  }

  function leave(i: number, j: number) {
    if (i === selected_i && j === selected_j) {
      selected_i = undefined;
      selected_j = undefined;
      changeSelected(selected_i, selected_j);
    }
  }
</script>

<outer>
  <bracket class="bracket1" />
  <matrix>
    {#each matrix as row, i}
      <row>
        {#each row as num, j}
          {#if editable}
            <input class="val"
              bind:value={matrix[i][j]}
              class:highlight_row={i === highlight_row || j === highlight_col}
              class:highlight={i >= highlight_i && i < highlight_i + highlight_height &&
                j >= highlight_j && j < highlight_j + highlight_width}
              on:mouseenter={() => enter(i, j)} on:mouseleave={() => leave(i, j)} role="textbox" />
          {:else}
            <div class="val"
              class:highlight_row={i === highlight_row || j === highlight_col}
              class:highlight={i >= highlight_i && i < highlight_i + highlight_height &&
                j >= highlight_j && j < highlight_j + highlight_width}
              on:mouseenter={() => enter(i, j)} on:mouseleave={() => leave(i, j)} role="none">
              {matrix[i][j]}
            </div>
          {/if}
        {/each}
      </row>
    {/each}
  </matrix>
  <bracket class="bracket2" />
</outer>

<style>
  * {
    --padding: 20px;
  }
  outer {
    display: flex;
    align-items: stretch;
  }
  bracket {
    position: relative;
    width: var(--padding);
    border: 2px;
    border-radius: 3000px;
    pointer-events: none;
  }
  .bracket1 {
    left: calc(0.5 * var(--padding));
    border-left: solid;
  }
  .bracket2 {
    left: calc(-0.5 * var(--padding));
    border-right: solid;
  }
  row {
    display: block;
  }
  input {
    font: inherit;
  }
  .val {
    background-color: inherit;
    color: inherit;
    border: none;
    display: inline-block;
    line-height: 0;
    height: 0;
    width: calc(var(--padding) * 2);
    padding: var(--padding) 0;
    text-align: center;
  }
  .highlight, .highlight_row {
    background-color: var(--highlight);
  }
</style>
