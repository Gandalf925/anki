<!--
Copyright: Ankitects Pty Ltd and contributors
License: GNU AGPL, version 3 or later; http://www.gnu.org/licenses/agpl.html
-->
<script lang="ts">
    import TitledContainer from "./TitledContainer.svelte";
    import SwitchRow from "./SwitchRow.svelte";
    import * as tr from "../lib/ftl";
    import type { DeckOptionsState } from "./lib";

    export let state: DeckOptionsState;
    export let api: Record<string, never>;

    let config = state.currentConfig;
    let defaults = state.defaults;
</script>

<TitledContainer title={tr.deckConfigBuryTitle()} {api}>
    <SwitchRow
        bind:value={$config.buryNew}
        defaultValue={defaults.buryNew}
        markdownTooltip={tr.deckConfigBuryTooltip()}
    >
        {tr.deckConfigBuryNewSiblings()}
    </SwitchRow>

    <SwitchRow
        bind:value={$config.buryReviews}
        defaultValue={defaults.buryReviews}
        markdownTooltip={tr.deckConfigBuryTooltip()}
    >
        {tr.deckConfigBuryReviewSiblings()}
    </SwitchRow>
</TitledContainer>
