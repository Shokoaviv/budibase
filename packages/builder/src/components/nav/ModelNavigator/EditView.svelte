<script>
  import { getContext } from "svelte"
  import { backendUiStore } from "builderStore"
  import { notifier } from "builderStore/store/notifications"
  import { DropdownMenu, Button, Icon, Input, Select } from "@budibase/bbui"
  import { FIELDS } from "constants/backend"
  import DeleteViewModal from "components/database/DataTable/modals/DeleteView.svelte"

  const { open, close } = getContext("simple-modal")

  export let view

  let anchor
  let dropdown

  let editing
  let originalName = view.name

  function showEditor() {
    editing = true
  }

  function hideEditor() {
    dropdown.hide()
    editing = false
    close()
  }

  const deleteView = () => {
    open(
      DeleteViewModal,
      {
        onClosed: close,
        viewName: view.name,
      },
      { styleContent: { padding: "0" } }
    )
  }

  function save() {
    backendUiStore.actions.views.save({
      originalName,
      ...view,
    })
    notifier.success("Renamed View Successfully.")
    hideEditor()
  }
</script>

<div bind:this={anchor} on:click={dropdown.show}>
  <i class="ri-more-line" />
</div>
<DropdownMenu bind:this={dropdown} {anchor} align="left">
  {#if editing}
    <h5>Edit View</h5>
    <div class="container">
      <Input placeholder="View Name" thin bind:value={view.name} />
    </div>
    <footer>
      <div class="button-margin-3">
        <Button secondary on:click={hideEditor}>Cancel</Button>
      </div>
      <div class="button-margin-4">
        <Button primary on:click={save}>Save</Button>
      </div>
    </footer>
  {:else}
    <ul>
      <li on:click={showEditor}>
        <Icon name="edit" />
        Edit
      </li>
      <li data-cy="delete-view" on:click={deleteView}>
        <Icon name="delete" />
        Delete
      </li>
    </ul>
  {/if}
</DropdownMenu>

<style>
  h5 {
    padding: var(--spacing-xl) 0 0 var(--spacing-xl);
    margin: 0;
    font-weight: 500;
  }

  .container {
    padding: var(--spacing-xl);
  }

  ul {
    padding: var(--spacing-xl) 0 0 var(--spacing-xl);
    list-style: none;
    padding-left: 0;
    margin: 0;
    padding: var(--spacing-s) 0;
  }

  li {
    display: flex;
    font-family: var(--font-sans);
    font-size: var(--font-size-xs);
    color: var(--ink);
    padding: var(--spacing-s) var(--spacing-m);
    margin: auto 0px;
    align-items: center;
    cursor: pointer;
  }

  li:hover {
    background-color: var(--grey-2);
  }

  li:active {
    color: var(--blue);
  }

  footer {
    padding: 20px;
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;
    gap: 20px;
    background: var(--grey-1);
    border-bottom-left-radius: 0.5rem;
    border-bottom-left-radius: 0.5rem;
  }

  .button-margin-1 {
    grid-column-start: 1;
    display: grid;
  }

  .button-margin-3 {
    grid-column-start: 3;
    display: grid;
  }

  .button-margin-4 {
    grid-column-start: 4;
    display: grid;
  }
</style>
