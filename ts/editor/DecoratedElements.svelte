<!--
Copyright: Ankitects Pty Ltd and contributors
License: GNU AGPL, version 3 or later; http://www.gnu.org/licenses/agpl.html
-->
<script context="module" lang="ts">
    import {
        CustomElementArray,
        DecoratedElementConstructor,
    } from "../editable/decorated";
    import contextProperty from "../sveltelib/context-property";

    const decoratedElements = new CustomElementArray<DecoratedElementConstructor>();

    const key = Symbol("decoratedElements");
    const [set, getDecoratedElements, hasDecoratedElements] =
        contextProperty<CustomElementArray<DecoratedElementConstructor>>(key);

    export { getDecoratedElements, hasDecoratedElements };
</script>

<script lang="ts">
    set(decoratedElements);
</script>

<slot />
