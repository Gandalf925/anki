<!--
Copyright: Ankitects Pty Ltd and contributors
License: GNU AGPL, version 3 or later; http://www.gnu.org/licenses/agpl.html
-->
<script context="module" lang="ts">
    import { CodeMirror as CodeMirrorLib } from "./code-mirror";

    export interface CodeMirrorAPI {
        readonly editor: CodeMirrorLib.EditorFromTextArea;
    }
</script>

<script lang="ts">
    import { createEventDispatcher, getContext } from "svelte";
    import type { Writable } from "svelte/store";
    import storeSubscribe from "../sveltelib/store-subscribe";
    import { directionKey } from "../lib/context-keys";
    import { lightCodeMirrorTheme, darkCodeMirrorTheme } from "./code-mirror";
    import { pageTheme } from "../sveltelib/theme";

    export let configuration: CodeMirror.EditorConfiguration;
    export let code: Writable<string>;

    const direction = getContext<Writable<"ltr" | "rtl">>(directionKey);
    const defaultConfiguration = {
        direction: $direction,
        rtlMoveVisually: true,
    };

    let codeMirror: CodeMirror.EditorFromTextArea;
    $: codeMirror?.setOption("direction", $direction);

    function setValue(content: string): void {
        codeMirror.setValue(content);
    }

    const { subscribe, unsubscribe } = storeSubscribe(code, setValue, false);
    const dispatch = createEventDispatcher();

    function openCodeMirror(textarea: HTMLTextAreaElement): void {
        codeMirror = CodeMirrorLib.fromTextArea(textarea, {
            ...defaultConfiguration,
            ...configuration,
        });

        // TODO passing in the tabindex option does not do anything: bug?
        codeMirror.getInputField().tabIndex = 0;

        let ranges: CodeMirrorLib.Range[] | null = null;

        codeMirror.on("change", () => dispatch("change", codeMirror.getValue()));
        codeMirror.on("mousedown", () => {
            ranges = null;
        });
        codeMirror.on("focus", () => {
            if (ranges) {
                try {
                    codeMirror.setSelections(ranges);
                } catch {
                    ranges = null;
                    codeMirror.setCursor(codeMirror.lineCount(), 0);
                }
            }
            unsubscribe();
            dispatch("focus");
        });
        codeMirror.on("blur", () => {
            ranges = codeMirror.listSelections();
            subscribe();
            dispatch("blur");
        });

        subscribe();
    }

    $: codeMirror?.setOption(
        "theme",
        $pageTheme.isDark ? darkCodeMirrorTheme : lightCodeMirrorTheme,
    );

    export const api = Object.create(
        {},
        {
            editor: { get: () => codeMirror },
        },
    ) as CodeMirrorAPI;
</script>

<div class="code-mirror">
    <textarea tabindex="-1" hidden use:openCodeMirror />
</div>

<style lang="scss">
    .code-mirror :global(.CodeMirror) {
        height: auto;
    }
</style>
