<script>
  import { onMount } from "svelte";
  import Cycle from "./Cycle.svelte";
  import _ from "lodash";

  let cycles = (b =>
    _(b)
      .map(c => [
        c[1],
        `https://raw.githubusercontent.com/cinemathequefr/Publications_cycles/master/data/PROG67 Mars-mai 2020/PROG67_CYCL${c[0]} ${c[1]}/editable/PROG67_CYCL${c[0]}_FILMS_EDIT ${c[1]}.json`
      ])
      .sortBy(c => _.kebabCase(c[0]))
      .value())([
    [11, "Séances spéciales"],
    [13, "Cinéma d'avant-garde"],
    [14, "Cinéma bis"],
    // [18, "Conservatoire des techniques cinématographiques"],
    [140, "Aujourd'hui le cinéma"],
    [143, "Fenêtre sur les collections"],
    [152, "Parlons cinéma avec Bertrand Belin"],
    [409, "Don Siegel"],
    [458, "Hiroshi Shimizu"],
    [460, "Hugo Santiago"],
    [463, "Andreï Kontchalovski"],
    [455, "Louis de Funès"],
    [456, "Gérard Oury"],
    [459, "Elizabeth Taylor"],
    [472, "Archi Vives"],
    [475, "Jean-Daniel Pollet"],
    // [485, "Séances Jeune public"],
    [486, "Alain Resnais"],
    [487, "Cinéma français des années 1950"],
    [488, "Philip Kaufman"]
  ]);

  let data = [];

  onMount(async () => {
    await fetchData({ target: { value: cycles[0][1] } });
  });

  async function fetchData(e) {
    console.log(e.target.value);

    data = await fetch(e.target.value);
    data = await data.json();

    data = _(data)
      .sortBy(d => _.kebabCase(d.titre))
      .value();
  }
</script>

<style>
  select {
    margin: 12px 0;
    min-width: 400px;
  }
</style>

<section class="menu">
  <div class="container">
    <select on:change={fetchData}>
      {#each cycles as cycle}
        <option value={cycle[1]}>{cycle[0]}</option>
      {/each}
    </select>

  </div>
</section>

<section>

  <div class="container">
    {#if data.length > 0}
      <Cycle {data} />
    {/if}
  </div>
</section>
