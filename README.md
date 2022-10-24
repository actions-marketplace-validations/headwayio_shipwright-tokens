# Shipwright Tokens
 
## Contents
- [Configure: Filter Actions](#configure-filter-actions)
- [Integreate Tailwind CSS](#integrate-tailwind-css)
***
 
## Configure: Filter Actions
Shipwright Tokens is a powerful tool, but you probably don't want it to run for every PR made in your repo. Instead, you likely only want it to run when you've made changes to your design tokens in Figma. This section will show you how to configure your action to run Shipwright Tokens only when you want it.
 
Add one of the below snippets to your `.yml` file that uses Shipwright Tokens.
 
### Filter by Branch Name
This method triggers the Shipwright Tokens action to run whenever a PR is opened from a branch name beginning with `tokens/`. If you want another naming convention in your project, simply replace `tokens` with your preferred name. Using this method, you will need to ensure that you are making commits to a branch beginning with `tokens/`, or your prefered naming convention, from within the Figma Tokens Plug-in.
```
on:
 push:
   branches:
     - 'tokens/**'
```
 
### Filter by Directory/Files Changed
This method triggers the Shipwright Tokens action when a PR is opened that contains changes to any file in the `tokens` directory. If you are saving your tokens in a different directory, replace `tokens` with the path to/name of your directory.
```
on:
 pull_request:
   paths:
     - 'tokens/**'
```
You can further specify which branch the PR is made against if you'd like to. The below example will trigger the Shipwright Token action when a PR is made against `main` while also containing changes to files in the `tokens` directory.
```
on:
 pull_request:
   paths:
     - 'tokens/**'
   branches:
     - 'main'
```
### Default Behavior
Using the provided template, without configuring the filter actions, will cause the Shipwright Tokens action to run on any PR opened against `main`. 
***

## Integrate Tailwind CSS
This section will show you how to configure your Shipwright Tokens action to output a custom Tailwind CSS theme.

### Generate a Custom Theme
Ensure that the `.yml` file that calls Shipwright Tokens includes `tailwind` as the value for the `styleSystem` property:
```
    steps:
      - name: Shipwright Tokens
        uses: headwayio/shipwright-tokens@(release/version)
        with:
          styleSystem: tailwind
```
With this configuration option set, Shipwright Tokens will generate 4 files that will be used in your theme: `colors.json`, `misc.json`, `shadows.json`, and `typography.json`. These files will be generated in the directory that you specify for the `outputFolder` property.

### Using the Custom Theme
With your theme files generated, you will need to configure your `tailwind.config.js` file to use your theme files - [Example Here](./theme_templates/tailwind.config.js)

### Colors, Shadows, and Misc styles:
- Ensure the contents of the generate files are available in `tailwind.config.js`:
```
const colors = require("./yourOutputDirectory/colors");
const shadows = require("./yourOutputDirectory/shadows");
const misc = require("./yourOutputDirectory/misc");
```
- Ensure your config is extending these values:
```
  theme: {
    extend: {
      colors,
      shadows,
      ...misc,
    },
  },
```

### Typography:
- Ensure the contents of the generated typography file is available n `tailwind.config.js`:
```
const typography = require("./yourOutputDirectory/typography");
```
- Import Tailwind CSS's built-in plugin function:
```
const plugin = require("tailwindcss/plugin");
```
- Add this custom plugin, utilizing the `addComponents` function:
```
plugins: [
    plugin(function ({ addBase, addComponents }) {
      addComponents({
        ...typography,
      });
    }),
  ],
```
***