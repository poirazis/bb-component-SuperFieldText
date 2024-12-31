<script>
  import { getContext, onDestroy } from "svelte";
  import CellString from "../../bb_super_components_shared/src/lib/SuperTableCells/CellString.svelte";
  import SuperButton from "../../bb_super_components_shared/src/lib/SuperButton/SuperButton.svelte";
  import SuperFieldLabel from "../../bb_super_components_shared/src/lib/SuperFieldLabel/SuperFieldLabel.svelte";
  import "../../bb_super_components_shared/src/lib/SuperTableCells/CellCommon.css";
  import "../../bb_super_components_shared/src/lib/SuperFieldsCommon.css";

  const { styleable, enrichButtonActions, Provider } = getContext("sdk");
  const component = getContext("component");
  const allContext = getContext("context");

  const formContext = getContext("form");
  const formStepContext = getContext("form-step");
  const groupLabelPosition = getContext("field-group");
  const labelWidth = getContext("field-group-label-width");
  const groupColumns = getContext("field-group-columns");
  const groupDisabled = getContext("field-group-disabled");
  const formApi = formContext?.formApi;

  export let field;
  export let buttons = [];

  export let label;
  export let span = 6;
  export let placeholder;
  export let defaultValue;
  export let template;
  export let disabled;
  export let readonly;
  export let validation;
  export let helpText;
  export let align;

  export let onChange;
  export let debounced;
  export let debounceDelay;
  export let autofocus;

  export let icon;

  export let role;
  export let labelPosition = "fieldGroup";
  export let showDirty;

  let formField;
  let formStep;
  let fieldState;
  let fieldApi;
  let fieldSchema;
  let value;

  $: formStep = formStepContext ? $formStepContext || 1 : 1;
  $: labelPos = label
    ? groupLabelPosition && labelPosition == "fieldGroup"
      ? groupLabelPosition
      : labelPosition
    : false;

  $: formField = formApi?.registerField(
    field,
    "string",
    defaultValue,
    disabled,
    readonly,
    validation,
    formStep
  );

  $: unsubscribe = formField?.subscribe((value) => {
    fieldState = value?.fieldState;
    fieldApi = value?.fieldApi;
    fieldSchema = value?.fieldSchema;
  });

  $: value = fieldState?.value ? fieldState.value : defaultValue;
  $: cellOptions = {
    placeholder,
    defaultValue,
    disabled: disabled || groupDisabled || fieldState?.disabled,
    template,
    readonly: readonly || fieldState?.readonly,
    icon,
    debounce: debounced ? debounceDelay : false,
    align,
    error: fieldState?.error,
    role,
    showDirty,
  };

  $: $component.styles = {
    ...$component.styles,
    normal: {
      ...$component.styles.normal,
      "grid-column": span < 7 ? "span " + span : "span " + groupColumns * 6,
    },
  };

  const handleChange = async (newValue) => {
    value = newValue;
    fieldApi?.setValue(newValue);
    await onChange?.({ value: newValue });
  };

  onDestroy(() => {
    fieldApi?.deregister();
    unsubscribe?.();
  });
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-noninteractive-tabindex -->
<!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
<!-- svelte-ignore a11y-no-static-element-interactions -->
<div use:styleable={$component.styles}>
  <Provider data={{ value }} />
  <div class="superField" class:left-label={labelPos == "left"}>
    <SuperFieldLabel
      {labelPos}
      {labelWidth}
      {label}
      {helpText}
      error={fieldState?.error}
    />
    <div class="inline-cells">
      <CellString
        {cellOptions}
        {value}
        {fieldSchema}
        {autofocus}
        on:change={(e) => handleChange(e.detail)}
      />
      {#if buttons?.length}
        <div class="inline-buttons">
          {#each buttons as { text, onClick, quiet, type, size }}
            <SuperButton
              {quiet}
              {disabled}
              {size}
              {type}
              {text}
              on:click={enrichButtonActions(onClick, $allContext)}
            />
          {/each}
        </div>
      {/if}
    </div>
  </div>
</div>
