<script>
  import { getContext, onDestroy } from "svelte";
  import {
    SuperButton,
    SuperField,
    CellString,
  } from "@poirazis/supercomponents-shared";

  const {
    styleable,
    enrichButtonActions,
    Provider,
    builderStore,
    processStringSync,
  } = getContext("sdk");
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

  export let onChange;
  export let debounced;
  export let debounceDelay;
  export let autofocus;
  export let invisible = false;

  export let icon;

  export let role = "formInput";
  export let labelPosition = "fieldGroup";
  export let showDirty;

  export let controlType;

  let formField;
  let formStep;
  let fieldState;
  let fieldApi;
  let fieldSchema;
  let value = defaultValue;
  let unsubscribe;

  $: setDefaultValue(defaultValue);

  $: unsubscribe = formField?.subscribe((value) => {
    fieldState = value?.fieldState;
    fieldApi = value?.fieldApi;
    fieldSchema = value?.fieldSchema;
  });

  $: formStep = formStepContext ? $formStepContext || 1 : 1;
  $: labelPos =
    groupLabelPosition !== undefined && labelPosition == "fieldGroup"
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

  $: error = fieldState?.error;
  $: value = fieldState?.value;
  $: fieldSchema?.type == "longform" && !controlType
    ? (controlType = "textarea")
    : (controlType = controlType);

  $: cellOptions = {
    placeholder: placeholder || field,
    defaultValue,
    disabled: disabled || groupDisabled || fieldState?.disabled,
    template,
    readonly: readonly || fieldState?.readonly,
    icon: icon ? "ph ph-" + icon : undefined,
    debounce: debounced ? debounceDelay : false,
    align,
    error: fieldState?.error,
    role,
    showDirty,
    controlType,
  };

  $: $component.styles = {
    ...$component.styles,
    normal: {
      ...$component.styles.normal,
      "grid-column": groupColumns ? `span ${span}` : "unset",
      "grid-row":
        controlType == "textarea"
          ? labelPosition == "left"
            ? "span 2"
            : "span 2"
          : "span 1",
      overflow: "hidden",
    },
  };

  const handleChange = (newValue) => {
    if (!form) value = newValue;
    fieldApi?.setValue(newValue);
    onChange?.();
  };

  const setDefaultValue = (val) => {
    value = val;
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
<div
  use:styleable={$component.styles}
  class:invisible
  class:inBuilder={$builderStore.inBuilder}
>
  <Provider data={{ value }} />
  <SuperField
    tall={controlType == "textarea"}
    multirow={controlType == "textarea"}
    height={controlType == "textarea" && labelPos == "above"
      ? "7.5rem"
      : controlType == "textarea"
        ? "4rem"
        : "auto"}
    {labelPos}
    {labelWidth}
    {field}
    {label}
    {error}
    {helpText}
  >
    <CellString
      {cellOptions}
      {value}
      {fieldSchema}
      {autofocus}
      on:change={(e) => handleChange(e.detail)}
    />
    {#if buttons?.length}
      <div class="inline-buttons">
        {#each buttons as { icon, onClick, ...rest }}
          <SuperButton
            {...rest}
            icon={"ph ph-" + icon}
            disabled={processStringSync(
              rest.disabledTemplate ?? "",
              $allContext
            ) === true ||
              disabled ||
              groupDisabled ||
              fieldState?.disabled}
            onClick={enrichButtonActions(onClick, $allContext)}
          />
        {/each}
      </div>
    {/if}
  </SuperField>
</div>

<style>
  .invisible {
    display: none;
  }

  .invisible.inBuilder {
    display: block;
    opacity: 0.6;
  }
</style>
