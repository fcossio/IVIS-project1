<script>
  import { onMount } from 'svelte';
  import TeamSelectBar from './TeamSelectBar.svelte';
  import RadialStackedBar from './RadialStackedBar.svelte';
  import * as d3 from 'd3';

  let ready = false;
  let data = []
  
  let selectedAliases = [];
  let aliasScores = {};

  onMount(async () => {
    data = await d3.csv('data.csv');
    ready = true;
    // Extract skills data only for barplot
  });
  let skills = ["Collab", "Comm", "Eval", "HCI", "Graph", "Code", "UI", "Art", "Math", "Stats", "Viz"]
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

  let skillOrder = ["Code", "Collab", "Comm", "Eval", "HCI", "Graph", "UI", "Art", "Math", "Stats", "Viz"]
  let updateSkillOrder = (skillsData) => {
    skillOrder = skillsData.sort((a, b) => b.total - a.total).map(d => d.skill).reverse();
    return skillOrder;
  };

  $: skillOrder = updateSkillOrder(skillsData);

  // sort by first skill

  let updateAliasScores = (skillOrder,skillsData) => {
    let aliasScores = {};
    const lowestSkill = skillOrder[0]
    data.forEach(d => {
      aliasScores[d.alias] = parseInt(d[`skill${lowestSkill}`]);
    });
    return aliasScores;
  };

  $: aliasScores = updateAliasScores(skillOrder,skillsData);
  

</script>

<main>
  <h1>IVIS Dream Team</h1>
  <p style="text-align:justify; margin-top:1em">
    This app is empowering <b>The Free Team Market</b> by ensuring that you have the information to build your team.
    Potential teammates are sorted in order to complement to your team's weakest skill.
    <br/>
    From an information visualization perspective this app optimizes the ink to information ratio by showing only the most relevant data at each step of the team building process.
    Area is used to encode skill levels. Colors are used to encode the need for a skill or the teammate that contributes this area in the team stats.
  </p>
  {#if ready}
    <TeamSelectBar data={data} aliases={data.map(d => d.alias)} bind:selectedAliases={selectedAliases} aliasScores={aliasScores} skillOrder={skillOrder}/>
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