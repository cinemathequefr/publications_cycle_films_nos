<script>
  import { onMount } from "svelte";
  import Cycle from "./Cycle.svelte";
  import _ from "lodash";

  let cycles = ((b) =>
    _(b)
      .map((c) => [
        c[1],
        `https://raw.githubusercontent.com/cinemathequefr/Publications_cycles/main/data/PROG78 Décembre 2020-février 2021/PROG78_CYCL${c[0]} ${c[1]}/editable/PROG78_CYCL${c[0]}_FILMS_EDIT ${c[1]}.json`,
      ])
      .sortBy((c) => _.kebabCase(c[0]))
      .value())([
    [13, "Cinéma d'avant-garde"],
    [14, "Cinéma bis"],
    [18, "Conservatoire des techniques cinématographiques"],
    [54, "Courts métrages de la Fémis"],
    [140, "Aujourd'hui le cinéma"],
    [143, "Fenêtre sur les collections"],
    [152, "Parlons cinéma avec Bertrand Belin"],
    [237, "Artavazd Pelechian"],
    [268, "Paulo Branco"],
    [460, "Hugo Santiago"],
    [472, "Archi Vives"],
    [486, "Alain Resnais"],
    [487, "Voyage dans le cinéma français des années 1950"],
    [501, "Plein les yeux 4"],
    [504, "Richard Brooks"],
    [507, "Dino Risi"],
    [516, "Friedrich Wilhelm Murnau"],
    [517, "Jacques Rozier"],
    [519, "Mia Hansen-Løve"],
    [520, "Séances Jeune public"],
    [522, "Netflix"],
    [525, "Le ciné-club"],
    [526, "Nicole Garcia"],
    [527, "Louis de Funès"],
    [531, "Méliès"],
    [99520, "Séances Jeune public"],
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
