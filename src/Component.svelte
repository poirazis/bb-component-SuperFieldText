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
      readonly: readonly || disabled,
      icon,
      debounce: debounced ? debounceDelay : false,
      clearValueIcon,
      role: "formInput", 
    }

  $: $component.styles = {
    ...$component.styles,
    normal: {
      ...$component.styles.normal,
      "flex-direction": labelPos == "left" ? "row" : "column",
      gap: labelPos == "left" ? "0.85rem" : "0rem",
      "grid-column": labelPos ? "span " + span : null,
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
      <label for="superCell" class="superlabel" class:bound={formContext}>
        {label}
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

    {#if fieldState.error}
      <div class="error">
        <span>{fieldState.error}</span>
      </div>
    {/if}
    
  </div>
</Block>

<style>
  .superField {
    display: flex;
    align-items: stretch;
    justify-content: stretch;
    min-width: 0;
  }

  .superField:focus {
    outline: none;
  }
  .superlabel {
    display: flex;
    align-items: flex-start;
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
    justify-items: stretch;
    height: 2rem;
  }
  .warning {
    color: var(--spectrum-global-color-red-500);
    border-color: var(--spectrum-global-color-red-500);
  }

  .spectrum-ActionButton--quiet.warning {
    color: var(--spectrum-global-color-red-500);
    border: none;
  }

  .warning:hover {
    color: #000;
    background-color: var(--spectrum-global-color-red-500);
  }
  .cta {
    color: #fff;
    background-color: var(--primaryColor);
  }

  .disabled {
    color: var(--spectrum-global-color-gray-600);
    background-color: var(--spectrum-global-color-gray-200);
  }

  .superlabel.bound {
    gap: 0.5rem;
  }
</style>




