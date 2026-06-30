---
name: mantine
description: Integrates the Mantine component library into a React project. Asks the user which packages to install and uses the correct package manager.
---

# Mantine Integration Skill

You are executing the Mantine integration skill. Follow these steps sequentially:

1. **Ask User for Packages**: Use the `ask_question` tool to determine which Mantine packages the user wants to install.
   - Provide a question asking which Mantine packages they'd like to add.
   - Set `is_multi_select` to `true`.
   - Provide the following options:
     - "@mantine/core"
     - "@mantine/hooks"
     - "@mantine/form"
     - "@mantine/dates"
     - "@mantine/charts"
     - "@mantine/notifications"
     - "@mantine/code-highlight"
     - "@mantine/tiptap"
     - "@mantine/dropzone"
     - "@mantine/carousel"
     - "@mantine/spotlight"
     - "@mantine/modals"
     - "@mantine/nprogress"
   
2. **Determine Package Manager**: Detect the package manager used in the active project by checking the root directory for lockfiles.
   - `pnpm-lock.yaml` -> use `pnpm add`
   - `yarn.lock` -> use `yarn add`
   - `package-lock.json` -> use `npm install`
   - `bun.lockb` -> use `bun add`
   - If no lockfile is found, use the user's global rule preference (`pnpm`) or ask the user.

3. **Install Packages**: Using the `run_command` tool, execute the installation command for the packages selected by the user. If the current project is part of a monorepo then install the packages within the particular project, not in the root dir.

4. **PostCSS Setup**: Mantine relies on PostCSS for its styles. Install `postcss` and `postcss-preset-mantine` as dev dependencies (e.g., `pnpm add -D postcss postcss-preset-mantine`). Then, create a `postcss.config.cjs` (or `postcss.config.mjs`) file in the project directory with the following content:
   ```javascript
      module.exports = {
        plugins: {
          'postcss-preset-mantine': {},
          'postcss-simple-vars': {
            variables: {
              'mantine-breakpoint-xs': '36em',
              'mantine-breakpoint-sm': '48em',
              'mantine-breakpoint-md': '62em',
              'mantine-breakpoint-lg': '75em',
              'mantine-breakpoint-xl': '88em',
            },
          },
        },
      };
   ```
   *(Use `export default` for `.mjs` files if the project uses ES modules).*

5. **Theme Setup**: Create a `theme.ts` file (e.g., in `src/styles/theme.ts` or `src/theme.ts`) with the following content:
   ```javascript
   import { createTheme } from '@mantine/core';

   export const theme = createTheme({
     /** Put your mantine theme override here */
   });
   ```

6. **MantineProvider and Component Setup**: Modify the application's entry point to wrap the app in `<MantineProvider theme={theme}>`.
   - If `@mantine/notifications` is installed, add the `<Notifications />` component inside `<MantineProvider>`.
   - If `@mantine/modals` is installed, add the `<ModalsProvider>` component inside `<MantineProvider>`.
   
   Example structure:
   ```jsx
   import { MantineProvider } from '@mantine/core';
   import { Notifications } from '@mantine/notifications';
   import { ModalsProvider } from '@mantine/modals';
   import { theme } from './styles/theme'; // Adjust path

   function App() {
     return (
       <MantineProvider theme={theme}>
         <Notifications />
         <ModalsProvider>
           {/* Your app here */}
         </ModalsProvider>
       </MantineProvider>
     );
   }
   ```

7. **CSS Imports**: You must import the styles for all installed Mantine packages in the application's entry point.
   - `@mantine/core/styles.css` must ALWAYS be imported first.
   - Then import the styles for other packages (e.g., `@mantine/notifications/styles.css`, `@mantine/carousel/styles.css`, etc.).
   
   **For TanStack Start / TanStack Router projects:**
   Styles must be imported as URLs and injected into the `links` array in the root route context.
   ```jsx
   import appCss from "../styles.css?url" // The base styles
   import mantineCoreCss from "@mantine/core/styles.css?url"
   import mantineNotificationCss from "@mantine/notifications/styles.css?url"
   // import other installed package styles here...

   export const Route = createRootRouteWithContext<MyRouterContext>()({
     head: () => ({
       meta: [
         // ...
       ],
       links: [
         {
           rel: 'stylesheet',
           href: mantineCoreCss, // This must be at the top
         },
         {
           rel: 'stylesheet',
           href: mantineNotificationCss,
         },
         {
           rel: 'stylesheet',
           href: appCss, // This must be at the bottom
         },
       ],
     }),
     shellComponent: RootDocument,
   })
   ```

8. **VS Code Settings**: To ensure PostCSS recognition of Mantine CSS variables, update or create the `.vscode/settings.json` file in the project root with the following configuration:
   ```json
   {
     "cssVariables.lookupFiles": [
       "**/*.css",
       "**/*.scss",
       "**/*.sass",
       "**/*.less",
       "node_modules/@mantine/core/styles.css"
     ]
   }
   ```

9. **VS Code Extensions**: To improve the developer experience with PostCSS and CSS variables, update or create the `.vscode/extensions.json` file in the project root to recommend the following extensions:
   ```json
   {
     "recommendations": [
       "vunguyentuan.vscode-css-variables",
       "vunguyentuan.vscode-postcss"
     ]
   }
   ```

10. **SSR Setup (Hydration Warnings)**: If the application uses server-side rendering (e.g., TanStack Start, Next.js), you must add `ColorSchemeScript` to the `<head>` and spread `mantineHtmlProps` on the `<html>` element to prevent hydration mismatches.
    - Import them from `@mantine/core`.
    - For TanStack Start, this is typically done in `src/routes/__root.tsx` (in the `RootDocument` component).
    
    Example:
    ```jsx
    import { ColorSchemeScript, mantineHtmlProps } from '@mantine/core';

    function RootDocument() {
      return (
        <html lang="en" {...mantineHtmlProps}>
          <head>
            <meta charSet="UTF-8" />
            <meta name="viewport" content="width=device-width, initial-scale=1.0" />
            <title>My awesome app</title>
            <ColorSchemeScript />
          </head>
          <body>{/* Your app here */}</body>
        </html>
      );
    }
    ```
