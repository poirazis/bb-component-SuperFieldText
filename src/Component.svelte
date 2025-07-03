<script>
  import { getContext, onDestroy, onMount } from "svelte";
  import {
    SuperButton,
    SuperField,
    CellString,
  } from "@poirazis/supercomponents-shared";

  const { styleable, enrichButtonActions, Provider, builderStore } =
    getContext("sdk");
  const component = getContext("component");
  const allContext = getContext("context");

  const form = getContext("form");
  const formStepContext = getContext("form-step");
  const groupLabelPosition = getContext("field-group");
  const labelWidth = getContext("field-group-label-width");
  const groupColumns = getContext("field-group-columns");
  const groupDisabled = getContext("field-group-disabled");

  export let field = "Text Field";
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
  export let buttons = [];
  export let inFieldGroup;

  export let onChange;
  export let debounced;
  export let debounceDelay;
  export let autofocus;

  export let icon;

  export let role = "formInput";
  export let labelPosition = "fieldGroup";
  export let showDirty;

  let formField;
  let formStep;
  let fieldState;
  let fieldApi;
  let fieldSchema;
  let value = defaultValue;
  let unsubscribe;

  $: unsubscribe = formField?.subscribe((value) => {
    fieldState = value?.fieldState;
    fieldApi = value?.fieldApi;
    fieldSchema = value?.fieldSchema;
  });

  $: formStep = formStepContext ? $formStepContext || 1 : 1;
  $: labelPos =
    groupLabelPosition && labelPosition == "fieldGroup"
      ? groupLabelPosition
      : labelPosition;

  $: formField = form?.formApi.registerField(
    field,
    "string",
    defaultValue,
    disabled,
    readonly,
    validation,
    formStep
  );

  $: value = fieldState?.value ? fieldState.value : defaultValue;
  $: error = fieldState?.error;
  $: _inFieldGroup = groupColumns ? true : false;

  $: if (
    _inFieldGroup !== inFieldGroup &&
    $component.selected &&
    $builderStore.inBuilder
  ) {
    builderStore.actions.updateProp("inFieldGroup", _inFieldGroup);
  }

  $: cellOptions = {
    placeholder: placeholder || field,
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
      flex: span > 6 ? "auto" : "none",
    },
  };

  const handleChange = (newValue) => {
    fieldApi?.setValue(newValue);
    onChange?.({ value: newValue });
  };

  onMount(() => {
    if (form)
      unsubscribe = formField?.subscribe((value) => {
        fieldState = value?.fieldState;
        fieldApi = value?.fieldApi;
        fieldSchema = value?.fieldSchema;
      });
  });

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
  <SuperField {labelPos} {labelWidth} {field} {label} {error} {helpText}>
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
  </SuperField>
</div>
