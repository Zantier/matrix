<script lang="ts">
  import Matrix from "./matrix.svelte";
  import Radio from "./Radio.svelte";

  let rows1: number = 1 + Math.floor(3*Math.random());

  let cols1: number = 1 + Math.floor(3*Math.random());
  let cols2: number = 1 + Math.floor(3*Math.random());
  let max_val: number = 3;
  let equation = '';

  let matrix1: number[][] = []
  let matrix2: number[][] = []
  let matrix3: number[][] = []
  let selected_i1: number | undefined = undefined;
  let selected_j1: number | undefined = undefined;
  let selected_i2: number | undefined = undefined;
  let selected_j2: number | undefined = undefined;
  let modes = ['Multiply 1', 'Multiply 2', 'Convolution']
  let mode;


  function getRandom() {
    return Math.floor((max_val + 1)*Math.random());
  }

  function createMatrices() {
    matrix1 = [];
    matrix2 = [];
    for (let i = 0; i < rows1; i++) {
      let row = [];
      for (let j = 0; j < cols1; j++) {
        row.push(getRandom());
      }
      matrix1.push(row);
    }
    for (let i = 0; i < cols1; i++) {
      let row = [];
      for (let j = 0; j < cols2; j++) {
        row.push(getRandom());
      }
      matrix2.push(row);
    }
  }

  $: {
    max_val;
    rows1;
    cols1;
    cols2;
    createMatrices();
  }

  // Update matrix3
  $: {
    matrix3 = [];

    for (let i = 0; i < rows1; i++) {
      let row = [];
      for (let j = 0; j < cols2; j++) {
        let sum = 0;
        for (let k = 0; k < cols1; k++) {
          sum += matrix1[i][k] * matrix2[k][j];
        }
        row.push(sum);
      }
      matrix3.push(row);
    }
  }

  $: {
    equation = '...';
    if (selected_i1 !== undefined && selected_j2 !== undefined) {
      equation = '';
      let delim = '';
      for (let i = 0; i < cols1; i++) {
        equation += `${delim}${matrix1[selected_i1][i]}·${matrix2[i][selected_j2]}`;
        delim = ' + ';
      }
      equation += ` = ${matrix3[selected_i1][selected_j2]}`;
    }
  }


  function handle_change1(e: CustomEvent<any>): void {
    selected_i1 = e.detail.i;
    selected_j1 = e.detail.j;
  }
  function handle_change2(e: CustomEvent<any>): void {
    selected_i2 = e.detail.i;
    selected_j2 = e.detail.j;
  }
  function handle_change3(e: CustomEvent<any>): void {
    selected_i1 = e.detail.i;
    selected_j2 = e.detail.j;
  }
</script>

<h1>
  Matrix multiplication
</h1>
<p>
  [Hover over] / [tap] values to see highlighting
</p>
<div>
  Mode:
  <Radio options={modes} bind:selected={mode}></Radio>
</div>
<p>
  Matrix 1 rows <input type="number" bind:value={rows1}>
</p>
<p>
  Matrix 1 cols / Matrix 2 rows <input type="number" bind:value={cols1}>
</p>
<p>
  Matrix 2 cols <input type="number" bind:value={cols2}>
</p>
<p>
  Max value <input type="number" bind:value={max_val}>
</p>

<matrices>
  <Matrix bind:matrix={matrix1} on:change={handle_change1}
    highlight_i={selected_i1} highlight_j={selected_j1}
    highlight_row={selected_i1} highlight_col = {undefined}
  />
  <dot>·</dot>
  <Matrix bind:matrix={matrix2} on:change={handle_change2}
    highlight_i={selected_i2} highlight_j={selected_j2}
    highlight_row={undefined} highlight_col = {selected_j2}
  />
  =
  <Matrix matrix={matrix3} on:change={handle_change3}
    highlight_i={selected_i1} highlight_j={selected_j2}
    highlight_row={undefined}
    highlight_col={undefined}
  />
</matrices>
<equation>
  {equation}
</equation>

<style>
  :root {
    background-color: #202020;
    color: #a0a0a0;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    font-size: 20px;
  }
  input {
    width: 50px;
    background-color: inherit;
    color: inherit;
    border-color: #202020;
    font-size: 30px;
    margin-left: 20px;
  }
  input:focus {
    outline-color: #a0a0a0;
    outline-style: solid;
  }
  matrices {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    gap: 10px;
    font-size: 20px;
  }
  dot {
    font-size: 2em;
    font-family: initial;
  }
  equation {
    display: block;
    padding: 20px 10px;
  }
</style>
