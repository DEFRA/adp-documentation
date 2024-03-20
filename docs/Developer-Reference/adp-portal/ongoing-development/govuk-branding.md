---
title: GovUK Branding for the ADP Portal
summary: GovUK Branding for the ADP Portal
authors:
    - Rozerin Baran
date: 2024-01-22
weight: -11
---

# GovUK Branding for the ADP Portal
The ADP portal is built on Backstage, using React and Typescript on the frontend. This page outlines, the steps taken to incorporate the GOV.UK branding into the ADP Portal.

# Key References: 

- [Backstage Theme Customization](https://backstage.io/docs/getting-started/app-custom-theme/)
- [GOV.UK Frontend](https://frontend.design-system.service.gov.uk/ )
- [GOV.UK Styles](https://design-system.service.gov.uk/styles/)

## Customization of Backstage 

Backstage allows the [customization of the themes](https://backstage.io/docs/getting-started/app-custom-theme/) to a certain extent, for example the font family, colours, logos and icons can be changed following the tutorials. All of the theme changes have been made within the App.tsx file.

## GOV.UK Branding

## 🏗️ Setup
In order to install GOV.UK Frontend you need to meet the some requirements: 
1. Have Node.js installed on your local environment
2. Install [Dart Sass](https://sass-lang.com/dart-sass/)

Once those are successfully installed you can run the following in your terminal within the adp-portal/app/packages/app folder:

`yarn install govuk-frontend --save`

In order to import the GOV.UK styles, two lines of code have been added within the style.module.scss file: 

`$govuk-assets-path: "~govuk-frontend/govuk/assets/";` _// this creates a path to the fonts and images of GOV.UK assets._
`@import "~govuk-frontend/govuk/all";` _// this imports all of the styles which enables the use of colours and typography._

### 🎨 Colour Scheme

The colour scheme is applied through exporting the GOV.UK colours as variables within the style.module.scss file into the Backstage Themes created. Currently there are a few colours that are being used however more variables can be added within the scss file and can be imported within other files. To import the scss file with the styles variables this statement is used in the App.tsx file: 

`import styles from 'style-loader!css-loader?{"modules": {"auto": true}}!sass-loader?{"sassOptions": {"quietDeps": true}}!./style.module.scss';`
> This import statement enables the scss file to load and process. 

The style variables then were used within the custom Backstage themes:

```javascript
const lightTheme = createUnifiedTheme({
...
  primary: {
      main: styles.primaryColour,
    },
});
```

### 🗛 Typography
The font used within the ADP Portal is [GDS Transport](https://designnotes.blog.gov.uk/2015/03/11/can-i-use-the-gov-uk-fonts/) as the portal will be on the gov.uk domain.

To get this working within the style.module.scss file the fonts were imported through assigning it to a scss variable called _govuk-assets-path-font-woff2_ and _govuk-assets-path-font-woff_: 
```scss
$govuk-assets-path-font-woff2: "~govuk-frontend/govuk/assets/fonts/light-94a07e06a1-v2.woff2";
$govuk-assets-path-font-woff: "~govuk-frontend/govuk/assets/fonts/light-f591b13f7d-v2.woff";
```
>As recommended we are serving the assets from the GOV.UK assets folder so that the style stays up to date when there is an update to the GOV.UK frontend.

Then this variable was parsed into the url of the font-face element:

```scss
@font-face {
  font-family: "GDS Transport";
  src:
    url('#{$govuk-assets-path-font-woff2}') format("woff2"),
    url('#{$govuk-assets-path-font-woff}') format("woff")
}
```

To customize the font of the backstage theme, the scss was imported (check the colour scheme section) and used within the fontFamily element of the createUnifiedTheme function:

```javascript
const lightTheme = createUnifiedTheme({
    ...
  fontFamily: "'GDS Transport',arial, sans-serif"
});
```

### 🖼️ Logo
The Logo of the ADP Portal was changed by updating the two files within the src/components/Roots folder.

- LogoFull.tsx - A larger logo used when the Sidebar navigation is opened.
- LogoIcon.tsx - A smaller logo used when the sidebar navigation is closed.

Both DEFRA logos are imported as png and saved within the Root folder.