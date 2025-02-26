<!--
Copyright: Ankitects Pty Ltd and contributors
License: GNU AGPL, version 3 or later; http://www.gnu.org/licenses/agpl.html
-->
<script lang="ts">
    import IconButton from "../components/IconButton.svelte";
    import WithShortcut from "../components/WithShortcut.svelte";

    import * as tr from "../lib/ftl";
    import { wrapInternal } from "../lib/wrap";
    import { withButton } from "../components/helpers";
    import { ellipseIcon } from "./icons";
    import { get } from "svelte/store";
    import { getNoteEditor } from "./OldEditorAdapter.svelte";
    import type { RichTextInputAPI } from "./RichTextInput.svelte";

    const noteEditor = getNoteEditor();
    const { focusInRichText, activeInput } = noteEditor;

    const clozePattern = /\{\{c(\d+)::/gu;
    function getCurrentHighestCloze(increment: boolean): number {
        let highest = 0;

        for (const field of noteEditor.fields) {
            const content = field.editingArea?.content;
            const fieldHTML = content ? get(content) : "";

            const matches: number[] = [];
            let match: RegExpMatchArray | null = null;

            while ((match = clozePattern.exec(fieldHTML))) {
                matches.push(Number(match[1]));
            }

            highest = Math.max(highest, ...matches);
        }

        if (increment) {
            highest++;
        }

        return Math.max(1, highest);
    }

    $: richTextAPI = $activeInput as RichTextInputAPI;

    async function onCloze(event: KeyboardEvent | MouseEvent): Promise<void> {
        const highestCloze = getCurrentHighestCloze(!event.getModifierState("Alt"));
        const richText = await richTextAPI.element;
        wrapInternal(richText, `{{c${highestCloze}::`, "}}", false);
    }

    $: disabled = !$focusInRichText;
</script>

<WithShortcut shortcut="Control+Alt?+Shift+C" let:createShortcut let:shortcutLabel>
    <IconButton
        tooltip="{tr.editingClozeDeletion()} {shortcutLabel}"
        {disabled}
        on:click={onCloze}
        on:mount={withButton(createShortcut)}
    >
        {@html ellipseIcon}
    </IconButton>
</WithShortcut>
