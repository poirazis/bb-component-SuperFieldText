<script>
  import { getContext , onDestroy} from "svelte";
  import CellString from "../../bb_super_components_shared/src/lib/SuperCell/cells/CellString.svelte";

  const { styleable, Block, BlockComponent, Provider } = getContext("sdk");
  const component = getContext("component");

  const formContext = getContext("form");
  const formStepContext = getContext("form-step");
  const labelPos = getContext("field-group");
  const labelWidth = getContext("field-group-label-width");
  const formApi = formContext?.formApi;

  export let field;
  
  export let customButtons

  export let buttons = [];
  export let buttonsQuiet;

  export let label;
  export let span = 6;
  export let placeholder
  export let defaultValue
  export let template
  export let disabled
  export let readonly
  export let validation

  export let onChange
  export let debounced
  export let debounceDelay

  export let icon
  export let suggestions
  export let clearValueIcon

  let formField;
  let formStep;
  let fieldState;
  let fieldApi;
  let fieldSchema
  let value;
  let cellState
  

  $: formStep = formStepContext ? $formStepContext || 1 : 1;

  $: formField = formApi?.registerField(
    field,
    "string",
    defaultValue,
    disabled,
    readonly,
    validation,
    formStep
  )

  $: unsubscribe = formField?.subscribe((value) => {
    fieldState = value?.fieldState;
    fieldApi = value?.fieldApi;
    fieldSchema = value?.fieldSchema;
  });

  $: value = fieldState?.value ? fieldState.value : defaultValue
  $: cellOptions = { 
      placeholder, 
      defaultValue,
      disabled,
      template,
      suggestions,
      padding: "0.5rem",
      readonly: readonly || disabled,
      icon,
      debounce: debounced ? debounceDelay : false,
      clearValueIcon,
      error: fieldState.error,
      role: "formInput", 
    }

  $: $component.styles = {
    ...$component.styles,
    normal: {
      ...$component.styles.normal,
      "flex-direction": labelPos == "left" ? "row" : "column",
      gap: labelPos == "left" ? "0.5rem" : "0rem",
      "grid-column": labelPos ? "span " + span : "span 1",
      "--label-width":
        labelPos == "left" ? (labelWidth ? labelWidth : "6rem") : "auto",
    },
  };
  
  const handleChange = ( newValue ) => {
    onChange?.({value: newValue});
    fieldApi?.setValue(newValue);
  }

  onDestroy(() => {
    fieldApi?.deregister()
    unsubscribe?.()
  })

</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-noninteractive-tabindex -->
<Block>
  <div
    class="superField"
    on:focus={cellState.focus} 
    tabindex="0"
    use:styleable={$component.styles}  
  >
    {#if label}
      <label for="superCell"
        class="superlabel"
        style:flex-direction={labelPos == "left" ? "column" : "row"}

      >
        {label} 
        {#if fieldState.error}
          <div class="error">
            <span>{fieldState.error}</span>
          </div>
        {/if}
      </label>
    {/if}

    <div class="inline-cells">
      <CellString
        bind:cellState
        {cellOptions}
        {value}
        {fieldSchema}
        on:change={(e) => handleChange(e.detail)}
        on:blur={cellState.lostFocus}
      />
      {#if customButtons && buttons?.length}
        <div
          class="spectrum-ActionGroup spectrum-ActionGroup--compact spectrum-ActionGroup--sizeM"
          class:spectrum-ActionGroup--quiet={buttonsQuiet}
        >
          <Provider data={ {value}} >
            {#each buttons as { text, onClick }}
              <BlockComponent
                type = "plugin/bb-component-SuperButton"
                props = {{
                  size: "M",
                  text,
                  onClick
                }}>
                </BlockComponent>
              {/each}
          </Provider>
        </div>
      {/if}
    </div>


    
  </div>
</Block>

<style>
  .superField {
    flex: auto;
    width: 100%;
    display: flex;
    align-items: stretch;
    min-width: 0;
  }

  .superField:focus {
    outline: none;
  }
  .superlabel {
    display: flex;
    justify-content: space-between;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    min-width: var(--label-width);
    max-width: var(--label-width);
    font-size: 13px;
    line-height: 1.75rem;
    font-weight: 400;
    color: var(--spectrum-global-color-gray-700);
  }

  .inline-cells {
    flex: 1;
    display: flex;
    justify-content: stretch;
    height: 2rem;
  }

  .error {
    font-size: 12px;
    line-height: 1.75rem;
    color: var(--spectrum-global-color-red-700);
  }
</style>




