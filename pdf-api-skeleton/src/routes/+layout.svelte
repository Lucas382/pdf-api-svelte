<script lang="ts">
    import "../app.postcss";

    // Floating UI for Popups
    import {
        computePosition,
        autoUpdate,
        flip,
        shift,
        offset,
        arrow,
    } from "@floating-ui/dom";
    import { Avatar, storePopup } from "@skeletonlabs/skeleton";
    storePopup.set({ computePosition, autoUpdate, flip, shift, offset, arrow });

    import { AppBar, AppShell } from "@skeletonlabs/skeleton";
</script>

<AppShell>
    <svelte:fragment slot="header">
        <AppBar>
            <svelte:fragment slot="lead">
                <strong class="text-xl uppercase">PDF to Json</strong>
            </svelte:fragment>
            <svelte:fragment slot="trail">
                <!-- <Avatar
                    width="w-14"
                    border="border-4 border-surface-300-600-token hover:!border-primary-500"
                    cursor="cursor-pointer"
                /> -->
            </svelte:fragment>
        </AppBar>
    </svelte:fragment>
    <slot />
</AppShell>
