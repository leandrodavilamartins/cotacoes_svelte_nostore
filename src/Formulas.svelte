<script>
  let data = [];
  let selected;
  let items = [];
  let values = [];
  let objs = [];
  let mixObjs = []; // contém os items da mistura
  let compItems = [];
  let compValues = [];

  async function getData() {
    db.collection("formulas")
      .get()
      .then((res) => {
        let docs = res.docs;
        promise = docs.map((doc) => {
          data = [doc.data(), ...data];
          return doc.data();
        });
      });
  }

  $: promise = getData();
  $: console.log(data);
  $: console.log(selected);
  $: selectedFormula = data.filter((d) => {
    return d.nome == selected;
  });
  $: console.log(selectedFormula);
  $: ingredients = selectedFormula.map((i) => {
    return i.ingredientes;
  });
  $: {
    ingredients.forEach((d) => {
      items = Object.keys(d);
      values = Object.values(d);
    });
  }
  $: console.log(items);
  $: console.log(values);
  $: {
    objs = [];
    let counter = items.length;
    for (let i = 0; i < counter; i++) {
      let obj = { items: items[i], values: values[i] };
      objs = [obj, ...objs];
    }
  }
  $: componentes = selectedFormula.map((i) => {
    return i.componentes;
  });
  $: console.log(componentes);
  $: {
    compItems = [];
    compValues = [];
    componentes.forEach((d) => {
      compItems = [...Object.keys(d), ...compItems];
      compValues = [...Object.values(d), ...compValues];
    });
  }
  $: console.log(compItems);
  $: console.log(compValues);
  $: {
    mixObjs = [];
    let counter = compItems.length;
    console.log(counter);
    for (let i = 0; i < counter; i++) {
      let obj = { nome: compItems[i], quantidade: compValues[i] };
      mixObjs = [obj, ...mixObjs];
    }
  }
  $: console.log(mixObjs);
</script>

{#await promise then}
  <div class="selectElement">
    <select class="form-select" bind:value={selected}>
      <option disabled>Escolha fórmula </option>
      {#each data as d}
        <option>{d.nome}</option>
      {/each}
    </select>
  </div>
{/await}
{#await promise then}
  <div class="table-content">
    <table class="table">
      <thead>
        <tr class="table-secondary">
          <th>Pré-mistura</th>
          <th>Quantidade ( kg )</th>
        </tr>
      </thead>
      <tbody>
        {#each objs as obj}
          <tr class="table-primary">
            <td>{obj.items}</td>
            <td>{obj.values}</td>
          </tr>
        {/each}
      </tbody>
      <thead>
        <tr class="table-secondary">
          <th>Mistura</th>
          <th>Quantidade ( kg )</th>
        </tr>
      </thead>
      <tbody>
        {#each mixObjs as obj}
          <tr class="table-warning">
            <td>{obj.nome}</td>
            <td>{obj.quantidade}</td>
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
{/await}

<style>
  .selectElement {
    display: flex;
    justify-content: center;
    margin-top: 5vh;
    width: 40%;
    margin: auto;
    margin-top: 5vh;
  }
  .table-content {
    display: flex;
    margin: auto;
    width: 50%;
  }
</style>
