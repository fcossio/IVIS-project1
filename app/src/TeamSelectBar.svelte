<!-- Show a multi-select bar that shows all the aliases for the people that you can select -->
<script>
  import { InputChip, Autocomplete} from '@skeletonlabs/skeleton';

  export let selectedAliases = [];
  export let aliases = [];
  export let aliasScores = [];
  export let data = [];
  export let skillOrder = [];

  let inputChip = '';
  let options
  $: options = aliases
    // sort by aliasScores which is a list in the same order
    .sort((a, b) => aliasScores[b] - aliasScores[a])
    .map((alias) => {return {label: make_card(alias, data), value: alias}})
  

  // function showTooltip(evt, text) {
  //   let tooltip = document.getElementById("tooltip");
  //   tooltip.innerHTML = text;
  //   tooltip.style.display = "block";
  //   tooltip.style.left = evt.pageX + 10 + 'px';
  //   tooltip.style.top = evt.pageY + 10 + 'px';
  // }

  // function hideTooltip() {
  //   var tooltip = document.getElementById("tooltip");
  //   tooltip.style.display = "none";
  // }
  const colors = ["#607c8a", "#5a8a98", "#5499a2", "#4fa7a7", "#53b5a8", "#60c3a4", "#76cf9c", "#92da91", "#b3e486", "#d8ec7b", "#fff175"]
  
  let make_card = (alias, data) => {
    let d = data[data.map(d => d.alias).indexOf(alias)];
    
    return `<div class="card flex flex-row">
      <div class="flex flex-col justify-center items-center"  style="flex: 0.75;">${alias}</div>
      <div class="flex flex-col"  style="flex: 1;">
        <svg  height=100px width=100%>
          <rect x=0 y=0 width=${d['skill'+skillOrder[0]]*10}% height=8% fill=${colors[10]}>
            <title>${skillOrder[0]}</title>
          </rect>
          <text x=0 y=8% font-size=9>More Need</text>
          <rect x=0 y=9% width=${d['skill'+skillOrder[1]]*10}% height=8% fill=${colors[9]} >
            <title>${skillOrder[1]}</title>
          </rect>
          <rect x=0 y=18% width=${d['skill'+skillOrder[2]]*10}% height=8% fill=${colors[8]} >
            <title>${skillOrder[2]}</title>
          </rect>
          <rect x=0 y=27% width=${d['skill'+skillOrder[3]]*10}% height=8% fill=${colors[7]} >
            <title>${skillOrder[3]}</title>
          </rect>
          <rect x=0 y=36% width=${d['skill'+skillOrder[4]]*10}% height=8% fill=${colors[6]} >
            <title>${skillOrder[4]}</title>
          </rect>
          <rect x=0 y=45% width=${d['skill'+skillOrder[5]]*10}% height=8% fill=${colors[5]} >
            <title>${skillOrder[5]}</title>
          </rect>
          <rect x=0 y=54% width=${d['skill'+skillOrder[6]]*10}% height=8% fill=${colors[4]} >
            <title>${skillOrder[6]}</title>
          </rect>
          <rect x=0 y=63% width=${d['skill'+skillOrder[7]]*10}% height=8% fill=${colors[3]} >
            <title>${skillOrder[7]}</title>
          </rect>
          <rect x=0 y=72% width=${d['skill'+skillOrder[8]]*10}% height=8% fill=${colors[2]} >
            <title>${skillOrder[8]}</title>
          </rect>
          <rect x=0 y=81% width=${d['skill'+skillOrder[9]]*10}% height=8% fill=${colors[1]} >
            <title>${skillOrder[9]}</title>
          </rect>
          <rect x=0 y=90% width=${d['skill'+skillOrder[10]]*10}% height=8% fill=${colors[0]} >
            <title>${skillOrder[10]}</title>
          </rect>
          <text x=0 y=98% font-size=9>Less Need</text>
        </svg>
      </div>
    </div>`
  }

  let onInputChipSelect = (event) => {
    if (selectedAliases.length > 4){}
    else {
        selectedAliases = [...selectedAliases, event.detail.value];
        inputChip = '';
    }
  };
  
</script>

<div style="margin-top:2em">
    <InputChip
      bind:input={inputChip}
      bind:value={selectedAliases}
      name="chips"
      placeholder={(selectedAliases.length === 0) ? "Select your name (or type it)" : selectedAliases.length < 5? `Potential teammates sorted by ${skillOrder[0]} skill`:"Your team is complete" }/>
    <div class="card card-hover w-full max-w-sm max-h-60 p-4 overflow-y-auto margin-top:1em" tabindex="-1">
        <Autocomplete
            bind:input={inputChip}
            options={(selectedAliases.length < 5)? options: []}
            denylist={selectedAliases}
            whitelist={options}
            on:selection={onInputChipSelect}
            emptyState={(selectedAliases.length === 5)? "" : "Nickname not found"}
        />
  </div>
</div>