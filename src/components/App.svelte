<script>
  import { onMount } from "svelte";
  import Cycle from "./Cycle.svelte";
  import _ from "lodash";

  let cycles = ((b) =>
    _(b)
      .map((c) => [
        c[1],
        `https://raw.githubusercontent.com/cinemathequefr/Publications_cycles/main  /data/PROG78 Décembre 2020-février 2021/PROG78_CYCL${c[0]} ${c[1]}/editable/PROG78_CYCL${c[0]}_FILMS_EDIT ${c[1]}.json`,
      ])
      .sortBy((c) => _.kebabCase(c[0]))
      .value())([
    [486, "Alain Resnais"],
    [501, "Plein les yeux 4"],
    [504, "Richard Brooks"],
    [507, "Dino Risi"],
    [531, "Méliès"],
    // [11, "Séances spéciales"],
    // [13, "Cinéma d'avant-garde"],
    // [14, "Cinéma bis"],
    // [18, "Conservatoire des techniques cinématographiques"],
    // [140, "Aujourd'hui le cinéma"],
    // [152, "Parlons cinéma avec Nicolas Pariser"],
    // [463, "Andreï Kontchalovski"],
    // [409, "Don Siegel"],
    // [459, "Elizabeth Taylor"],
    // [456, "Gérard Oury"],
    // [458, "Hiroshi Shimizu"],
    // [461, "Léonide Moguy"],
    // [472, "Archi Vives"],
    // [499, "Jean-Claude Carrière"],
    // [506, "Louis de Funès"],
    // [498, "Luis Buñuel"],
    // [508, "Semaine de la critique"],
    // [510, "Séances Jeune public"],
    // [511, "Hommage à Serge Korber"],
    // [512, "American Fringe 5"],
    // [513, "Pionnières du cinéma soviétique"],
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
      .sortBy((d) => _.kebabCase(d.titre))
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
