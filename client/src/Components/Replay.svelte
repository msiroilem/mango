<script lang="ts">
  import { onMount } from 'svelte';
  import Match from '../Components/Match.svelte';
  import util from '../util/util';
  export let matchId;

  let error = '';
  let buttonDisabled = false;
  let matchSummary = null;

  onMount(async () => {
    try {
      const response = await fetch(
        `http://localhost:8080/matches/summary/${matchId}`,
      );
      matchSummary = await response.json();
      processMatchSummary();
    } catch (err) {
      console.error(err);
      error = err;
    }
  });

  const processMatchSummary = () => {
    matchSummary = { ...matchSummary, valvePlayers: [] };
    for (const key in matchSummary) {
      if (key.includes('Hero')) {
        matchSummary['valvePlayers'].push(util.getValveName(matchSummary[key]));
      }
    }
    matchSummary = matchSummary;
  };

  const parseReplay = async () => {
    buttonDisabled = true;
    try {
      const response = await fetch(`http://localhost:8080/replays/${matchId}`, {
        method: 'POST',
      });

      matchSummary = await response.json();
      processMatchSummary();

      
    } catch (error) {
      console.error(error);
    }
  };
</script>

<div class="replay">
  <Match {matchId} {matchSummary} />
  {#if error && !buttonDisabled}
    <button disabled={buttonDisabled} on:click={parseReplay}
      >Parse Replay</button
    >
  {/if}
</div>

<style>
  .replay {
    margin-bottom: 20px;
  }
</style>
