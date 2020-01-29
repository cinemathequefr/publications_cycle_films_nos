<script>
  import { onMount } from "svelte";
  import showdown from "showdown";
  import _ from "lodash";
  const converter = new showdown.Converter();
  export let data;

  function sel(e) {
    e.target.select();
    copyTextToClipboard(e.target.value);
    // console.log(e.target.value);
  }

  /**
   * nbsp
   * Remplace les espaces en position d'insécable (cas usuels) par un insécable.
   * @param {string} str
   * @param {string} rep Chaîne à utiliser pour exprimer l'espace insécable (par défaut, entité HTML `&nbsp;`).
   * @return {string}
   */
  function nbsp(str, rep = "&nbsp;") {
    if (!str) return;
    let o = str;
    o = o.replace(/(\x20)([\?:!;\xBB])/gi, `${rep}$2`); // Remplace un espace par un espace insécable dans les cas usuels
    o = o.replace(/(\xAB)(\x20)/gi, `$1${rep}`); // Remplace un espace par un espace insécable après un guillemet français ouvrant
    o = o.replace(/(\s–)/gi, `${rep}–`); // Demi-cadratins
    o = o.replace(/(–\s)/gi, `–${rep}`); // Demi-cadratins
    return o;
  }

  /**
   * artTitre
   * @description
   * @param {string|null} art
   * @param {string} titre
   */
  function artTitre(art, titre) {
    return !art ? titre : art === "L'" ? art + titre : art + " " + titre;
  }

  function titreVo(film) {
    if (film.titreNx) {
      return `${film.titreNx} [${artTitre(film.artVo, film.titreVo)}]`;
    } else {
      return `${artTitre(film.artVo, film.titreVo)}`;
    }
  }

  function nop(str) {
    return str.replace(/<\/?p>/gi, "");
  }

  function copyTextToClipboard(text) {
    // https://stackoverflow.com/questions/400212/how-do-i-copy-to-the-clipboard-in-javascript
    if (!navigator.clipboard) {
      fallbackCopyTextToClipboard(text);
      return true;
    } else {
      return navigator.clipboard.writeText(text);
    }
  }

  function fallbackCopyTextToClipboard(text) {
    var textArea = document.createElement("textarea");
    textArea.value = text;
    document.body.appendChild(textArea);
    cursorFocus(textArea);
    textArea.select();

    try {
      var successful = document.execCommand("copy");
      var msg = successful ? "successful" : "unsuccessful";
    } catch (err) {}

    document.body.removeChild(textArea);
  }
</script>

<style>
  .film {
    margin: 0 0 36px 0;
  }

  .bold {
    font-weight: 700;
  }

  input {
    width: 100%;
    border: solid 1px #ccc;
    padding: 4px;
    margin: 0 0 -1px 0;
    transition: 0.5s;
  }

  textarea {
    width: 100%;
    overflow: hidden;
    resize: none;
    border: solid 1px #ccc;
    padding: 4px;
    margin: 0 0 -1px 0;
    transition: 0.5s;
  }

  input:focus,
  textarea:focus {
    background-color: #ff9;
  }

  .hi {
    color: #c36;
  }

  .pk {
    font-size: 0.875rem;
    color: #666;
  }
</style>

<p>
  En se positionnant dans un champ, son contenu est collé automatiquement dans
  le presse-papier (
  <code>Ctrl-C</code>
  inutile).
  <br />
  Le contenu
  <span class="hi bold">rose</span>
  doit être collé dans Novis OS dans l'éditeur de source HTML.
</p>

{#each data as film}
  <div class="film">

    <div class="pk">{film.idFilm}</div>

    <input
      class="bold"
      type="text"
      readonly
      on:click={sel}
      value={artTitre(film.art, film.titre)} />

    {#if film.titreVo}
      <input
        class="bold"
        type="text"
        readonly
        on:click={sel}
        value={titreVo(film)} />
    {/if}

    <input type="text" readonly on:click={sel} value={film.realisateurs} />

    {#if film.generique && film.generique.length > 0}
      <input
        type="text"
        readonly
        on:click={sel}
        value={`Avec ${film.generique.join(', ')}.`} />
    {/if}

    {#if film.adaptation}
      <input
        type="text"
        readonly
        on:click={sel}
        value={nop(nbsp(converter.makeHtml(film.adaptation)))} />
    {/if}

    {#if film.textes}
      {#each film.textes as texte}
        <textarea
          rows="4"
          readonly
          on:click={sel}
          class="hi"
          value={nbsp(converter.makeHtml(texte.texte))} />
      {/each}
    {/if}

  </div>
{/each}
