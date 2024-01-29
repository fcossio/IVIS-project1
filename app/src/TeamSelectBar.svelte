<!-- Show a multi-select bar that shows all the aliases for the people that you can select -->
<script>
  import { InputChip, Autocomplete} from '@skeletonlabs/skeleton';

  export let selectedAliases = [];
  export let aliases = [];
  export let aliasScores = []

  let inputChip = '';
  let options
  $: options = aliases
    // sort by aliasScores which is a list in the same order
    .sort((a, b) => aliasScores[aliases.indexOf(b)] - aliasScores[aliases.indexOf(a)])
    .map((alias) => {return {label: alias, value: alias}});
  
//   onMount(async () => {
//       options = aliases.map((alias) => {return {label: alias, value: alias}});
//       console.log(aliases);
//   });

  let onInputChipSelect = (event) => {
    console.log(event.detail)
    if (selectedAliases.length > 4){
        console.log("Too many people selected");
    }else {
        selectedAliases = [...selectedAliases, event.detail.label];
        inputChip = '';
    }
  };
  
</script>

<div style="margin-top:2em">
    <InputChip
      bind:input={inputChip}
      bind:value={selectedAliases}
      name="chips"
      placeholder={(selectedAliases.length === 0) ? "Select your name" : selectedAliases.length < 5? "Select your teammates (Sorted by complementariness)":"Your team is complete" }/>
    <div class="card w-full max-w-sm max-h-48 p-4 overflow-y-auto margin-top:1em" tabindex="-1">
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