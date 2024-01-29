<script>
  import { onMount } from 'svelte';
  import TeamSelectBar from './TeamSelectBar.svelte';
  import RadialStackedBar from './RadialStackedBar.svelte';
  import * as d3 from 'd3';

  let ready = false;
  let data = []
  
  let selectedAliases = [];
  let aliasScores = [];

  onMount(async () => {
    data = await d3.csv('data.csv');
    ready = true;
    console.log(data)
    // Extract skills data only for barplot
  });
  let skills = ["Collab", "Comm", "Eval", "HCI", "Graph", "Graph", "Code", "UI", "Art", "Math", "Stats", "Viz"]
  $: skillsData = updateSkillData(selectedAliases);

  let updateSkillData = (selectedAliases) => {
    let skillsData = [];
    skills.forEach(skill => {
      let total = 0;
      let skillD = {skill: skill};
      selectedAliases.forEach((alias)=>{
        data.forEach((d)=>{
          if (d.alias === alias){
            skillD[alias] = parseInt(d[`skill${skill}`]);
            total += parseInt(d[`skill${skill}`]);
          }});
      });
      skillD.total = total;
      skillsData.push(skillD);
    });
    return skillsData;
  };

  let updateAliasScores = skillsData => {
    aliasScores = [];
    let factors = skillsData.map(d => d.total);
    console.log(factors)
    let sum = factors.reduce((a, b) => a + b, 0);
    factors = factors.map(d => 1 - d/sum);
    data.forEach(d => {
      let score = 0;
      skills.forEach(skill => {
        score += parseInt(d[`skill${skill}`]) * factors[skills.indexOf(skill)];
      });
      aliasScores.push(score);
  })
  console.log(aliasScores);
  return aliasScores;};
  $: aliasScores = updateAliasScores(skillsData);
  

</script>

<main>
  <h1>IVIS Dream Team</h1>
  {#if ready}
    <TeamSelectBar aliases={data.map(d => d.alias)} bind:selectedAliases={selectedAliases} aliasScores={aliasScores}/>
    {#if (selectedAliases.length > 0)}
      <RadialStackedBar data={skillsData} />
    
    {:else}
      <div style="height:10em; display: flex; align-items: flex-end;">
        <p>Select the first member to see team stats</p>
      </div>
    {/if}
  {:else}
    <p>Loading data ...</p>
  {/if}
</main>