# Super Field - Text

A versatile text input field component for Budibase applications with advanced validation, formatting, event handling, and customization options.

## 🚀 Features

### Input Configuration

- **Field Binding**: Connect to data fields with automatic label generation
- **Placeholder Text**: Customizable placeholder for user guidance
- **Default Values**: Pre-populated text values
- **Input Masking**: Pattern-based input restrictions (advanced feature)

### Validation & Formatting

- **Built-in Validation**: String validation rules with custom error messages
- **Value Formatting**: Template-based text transformation and display formatting
- **Help Text**: Contextual help and instructions for users

### User Experience

- **Event Handling**: On change events with full context access
- **Auto-focus**: Automatic input focus on component load
- **Debounced Input**: Performance-optimized input with configurable delay
- **Suggestions**: Autocomplete functionality (when enabled)
- **Inline Icons**: Visual indicators and interactive elements

### Styling & Layout

- **Text Alignment**: Left, center, or right alignment options
- **Label Positioning**: Flexible label placement (above, left, or hidden)
- **Field Modes**: Form input or inline editing styles
- **Responsive Sizing**: Configurable width and span settings
- **Visual States**: Disabled, readonly, and dirty state indicators

### Advanced Interactions

- **Button Integration**: Custom action buttons within the input field
- **Conditional Logic**: Dynamic behavior based on other field values
- **Accessibility**: Full keyboard navigation and screen reader support

## 📝 Usage Instructions

### Basic Setup

1. Add the Super Field - Text component to your form or screen
2. Bind to a data field or configure as a standalone input
3. Set label, placeholder, and default value as needed
4. Configure validation rules for data integrity

### Advanced Configuration

- **Formatting**: Use template strings for value transformation
- **Events**: Attach actions to the onChange event for dynamic responses
- **Validation**: Set up comprehensive validation with custom messages
- **Styling**: Customize appearance with alignment, sizing, and positioning options

### Common Use Cases

- **User Registration**: Name, email, and address fields
- **Search Inputs**: With debouncing for performance
- **Data Entry Forms**: With validation and formatting
- **Inline Editing**: For table or list modifications

## 🔧 Configuration Options

| Setting        | Type     | Description                        |
| -------------- | -------- | ---------------------------------- |
| Field          | String   | Data field binding                 |
| Label          | String   | Display label text                 |
| Placeholder    | String   | Input placeholder text             |
| Default Value  | String   | Pre-filled value                   |
| Format Value   | Template | Value formatting template          |
| Help Text      | String   | Help/instruction text              |
| Validation     | Rules    | Input validation configuration     |
| Alignment      | Select   | Text alignment (left/center/right) |
| Autofocus      | Boolean  | Auto-focus on load                 |
| Debounced      | Boolean  | Enable input debouncing            |
| Disabled       | Boolean  | Disable input interaction          |
| Read Only      | Boolean  | Read-only mode                     |
| Field Mode     | Select   | Form or inline input style         |
| Label Position | Select   | Label placement                    |
| Size           | Number   | Component width span               |

## 📋 Events

### On Change

Triggered when the input value changes.

**Context:**

- `value`: The current input value
- `field`: The bound field information

## 🎨 Styling

The component supports Budibase's standard styling system including:

- Size variants (XS, S, M, L)
- Color theming
- Custom CSS classes
- Responsive breakpoints

## 🔍 Best Practices

- Use placeholders for guidance without cluttering the label
- Implement validation early to improve user experience
- Consider debouncing for search or filter inputs
- Use help text for complex formatting requirements
- Test accessibility with keyboard navigation
