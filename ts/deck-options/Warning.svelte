<!--
Copyright: Ankitects Pty Ltd and contributors
License: GNU AGPL, version 3 or later; http://www.gnu.org/licenses/agpl.html
-->
<script lang="ts">
    import { slide } from "svelte/transition";
    import Row from "../components/Row.svelte";
    import { withoutUnicodeIsolation } from "../lib/i18n/";

    export let warning: string;
</script>

{#if warning}
    <Row>
        <div class="col-12 alert alert-warning mb-0" in:slide out:slide>
            {withoutUnicodeIsolation(warning)}
        </div>
    </Row>
{/if}
