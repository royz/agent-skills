# Creating a Custom Button Variant in Mantine (v7+)

When adding a custom button variant, you must extend the `Button` component in the Mantine theme and provide styling via CSS modules.

## 1. Update the Theme (`theme.ts`)

Extend the `Button` component to use a CSS module for class names:

```tsx
import { createTheme, Button } from '@mantine/core';
import buttonClasses from './Button.module.css';

export const theme = createTheme({
  components: {
    Button: Button.extend({
      classNames: buttonClasses,
    }),
  },
});
```

## 2. Create the CSS Module (`Button.module.css`)

Target the specific variant using `data-variant` attribute.

```css
/* Button.module.css */
.root {
	&[data-variant="success"] {
		background-color: light-dark(alpha(var(--mantine-color-green-8), 0.7), var(--mantine-color-teal-4));

		@mixin hover {
			background-color: light-dark(alpha(var(--mantine-color-green-8), 0.6), var(--mantine-color-teal-4));
		}
	}

	&[data-variant="danger"] {
		background-color: light-dark(alpha(var(--mantine-color-red-6), 0.8), var(--mantine-color-teal-4));

		@mixin hover {
			background-color: light-dark(alpha(var(--mantine-color-red-6), 0.65), var(--mantine-color-teal-4));
		}
	}

	&[data-variant="neutral"] {
		color: var(--mantine-color-dark-5);
		background-color: light-dark(var(--mantine-color-gray-2), var(--mantine-color-red-6));

		@mixin hover {
			color: var(--mantine-color-dark-5);
			background-color: light-dark(var(--mantine-color-gray-3), var(--mantine-color-red-6));
		}
	}
}
```

## 3. Usage

The custom variant can now be used anywhere in the app:

```tsx
import { Button } from '@mantine/core';

function MyComponent() {
  return (
    <Button variant="success">
      Success Button
    </Button>
  );
}
```
