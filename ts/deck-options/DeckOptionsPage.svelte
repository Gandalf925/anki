<!--
Copyright: Ankitects Pty Ltd and contributors
License: GNU AGPL, version 3 or later; http://www.gnu.org/licenses/agpl.html
-->
<script lang="ts">
    import ConfigSelector from "./ConfigSelector.svelte";
    import Container from "../components/Container.svelte";
    import Row from "../components/Row.svelte";
    import DailyLimits from "./DailyLimits.svelte";
    import DisplayOrder from "./DisplayOrder.svelte";
    import NewOptions from "./NewOptions.svelte";
    import AdvancedOptions from "./AdvancedOptions.svelte";
    import BuryOptions from "./BuryOptions.svelte";
    import LapseOptions from "./LapseOptions.svelte";
    import TimerOptions from "./TimerOptions.svelte";
    import AudioOptions from "./AudioOptions.svelte";
    import Addons from "./Addons.svelte";

    import type { DeckOptionsState } from "./lib";
    import type { Writable } from "svelte/store";
    import HtmlAddon from "./HtmlAddon.svelte";
    import type { DynamicSvelteComponent } from "../sveltelib/dynamicComponent";

    export let state: DeckOptionsState;
    let addons = state.addonComponents;

    export function auxData(): Writable<Record<string, unknown>> {
        return state.currentAuxData;
    }

    export function addSvelteAddon(component: DynamicSvelteComponent): void {
        $addons = [...$addons, component];
    }

    export function addHtmlAddon(html: string, mounted: () => void): void {
        $addons = [
            ...$addons,
            {
                component: HtmlAddon,
                html,
                mounted,
            },
        ];
    }

    export const options = {};
    export const dailyLimits = {};
    export const newOptions = {};
    export const lapseOptions = {};
    export const buryOptions = {};
    export const displayOrder = {};
    export const timerOptions = {};
    export const audioOptions = {};
    export const addonOptions = {};
    export const advancedOptions = {};
</script>

<ConfigSelector {state} />

<div class="deck-options-page">
    <Container
        breakpoint="sm"
        --gutter-inline="0.25rem"
        --gutter-block="0.5rem"
        class="container-columns"
        api={options}
    >
        <Row class="row-columns">
            <DailyLimits {state} api={dailyLimits} />
        </Row>

        <Row class="row-columns">
            <NewOptions {state} api={newOptions} />
        </Row>

        <Row class="row-columns">
            <LapseOptions {state} api={lapseOptions} />
        </Row>

        {#if state.v3Scheduler}
            <Row class="row-columns">
                <DisplayOrder {state} api={displayOrder} />
            </Row>
        {/if}

        <Row class="row-columns">
            <TimerOptions {state} api={timerOptions} />
        </Row>

        <Row class="row-columns">
            <BuryOptions {state} api={buryOptions} />
        </Row>

        <Row class="row-columns">
            <AudioOptions {state} api={audioOptions} />
        </Row>

        <Row class="row-columns">
            <Addons {state} api={addonOptions} />
        </Row>

        <Row class="row-columns">
            <AdvancedOptions {state} api={advancedOptions} />
        </Row>
    </Container>
</div>

<style lang="scss">
    @use "sass/breakpoints" as bp;

    .deck-options-page {
        overflow-x: hidden;

        @include bp.with-breakpoint("lg") {
            :global(.container) {
                display: block;
            }

            :global(.container-columns) {
                column-count: 2;
                column-gap: 5em;

                :global(.container) {
                    break-inside: avoid;
                }
            }

            :global(.row-columns) {
                display: block;
            }
        }
    }
</style>
