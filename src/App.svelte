<script>
  import Table from "..";
  
  let list = [];
  let marge = 20;
  let containerWidth = 1000;

  for (let i = 0; i < 20; i++) {
    list.push({
      e1: i,
      e2: "Bla bla bla",
      e3: "434344"
    });
  }

  function changec2() {
    list[0].e2 += "and re bla bla";
  }
  function changec3() {
    list[0].e3 += "and re bla bla";
  }
  function changemarge() {
    marge += 10;
  }
</script>

<style>
  #app {
    font-family: "Avenir", Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
    margin-top: 60px;
  }
  :global(body) {
    overflow-x: hidden;
    overflow-y: hidden;
  }

  thead {
    background-color: aliceblue;
  }
  tbody {
    background-color: antiquewhite;
  }

  :global(table),
  th,
  td {
    border: 1px solid black;
  }

  td {
    padding-left: 10px;
  }
  td.c2 {
    width: 200px;
  }
  th.c1 {
    width: 30px;
  }
  th.c2 {
    width: 300px;
  }
  :global(.tablecontainer) {
    color: red;
  }
</style>

<div id="app">
  <p>
    Resize the browser to see the vertical scrollbar attached only to the table
    body.
  </p>
  <p>
    Here, the table width is limited by {containerWidth}px and the column #2
    width is fixed to 200px
  </p>
  <div id="container" style="max-width:{containerWidth}px">
    <button on:click={changec2}>Add text in c2</button>
    <button on:click={changec3}>Add text in c3</button>
    <button on:click={changemarge}>Add 10px to bottom-marge</button>
    <Table class="tablecontainer" bottomMarge={marge}>
      <thead slot="thead">
        <th class="c1">C1</th>
        <th class="c2">C2C2C2C2C2C2C2C2C2C2C2 C2C2C2C2C2C2C2C2C2C2C2</th>
        <th class="c3">C3</th>
      </thead>
      <tbody slot="tbody">
        {#each list as item, index}
          <tr>
            <td class="c1">{item.e1}</td>
            <td class="c2">{item.e2}</td>
            <td class="c3">{item.e3}</td>
          </tr>
        {/each}
      </tbody>
    </Table>
  </div>
</div>
