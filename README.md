# Welcome to gatsby-source-google-spreadsheets 👋

[![Version](https://img.shields.io/npm/v/gatsby-source-google-spreadsheets.svg)](https://www.npmjs.com/package/gatsby-source-google-spreadsheets)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://github.com/butlerx/gatsby-source-google-spreadsheets/blob/master/LICENSE)
[![Twitter: cianbutlerx](https://img.shields.io/twitter/follow/cianbutlerx.svg?style=social)](https://twitter.com/cianbutlerx)

> A source plugin for Gatsby that allows reading data from Google Sheets.

Forked from
[brandonmp/gatsby-source-google-sheets](https://github.com/brandonmp/gatsby-source-google-sheets)
to allow pulling the entire sheet as an objects and using public sheets

Why go through the hassle of setting up a complicated headless CMS when Google
Sheets already has user permissions, revision history, and a powerful UI?

This source plugin for [Gatsby JS](https://github.com/gatsbyjs/gatsby) will turn
any Google Sheets worksheet into a GraphQL type for build-time consumption.

### ✨ [Demo](https://beer.notthe.cloud)

## Install

```sh
yarn add gatsby-source-google-spreadsheets
```

## Usage

### Step 1: set up sheets/permissions

1. Create a
   [Google Service Account](https://developers.google.com/identity/protocols/OAuth2ServiceAccount#creatinganaccount)
   and download the credentials file.
1. Open your google sheet, click "File > Share..." and enter your service
   account's e-mail address (you can find it in the credentials file).

Or If you wish to work with a Google Spreadsheet without authenticating, not
only must the Spreadsheet in question be visible to the web, but it must also
have been explicitly published.

1. Open your google sheet, Click "File > Publish to the web" and Share entire
   sheet or specific worksheets.
1. Click "File > Share" and click "Get Shareable Link", the link should look
   like
   `https://docs.google.com/spreadsheets/d/$SPREADSHEET_ID/edit?usp=sharing`

### Step 2: configure your gatsby project

Standard source plugin installation.

```js
// gatsby-config.js
// ...
{
  resolve: 'gatsby-source-google-spreadsheets',
  options: {
    spreadsheetId: 'get this from the sheet url',
    // This is only needed if you are not using published sheets
    credentials: require('./path-to-credentials-file.json')
  }
},
// OR using environment variables
{
  resolve: 'gatsby-source-google-spreadsheets',
  options: {
    spreadsheetId: 'get this from the sheet url',
    // This is only needed if you are not using published sheets
    credentials: {
      spreadsheetId: process.env.SPREADSHEET_ID,
      credentials: {
        type: 'service_account',
        project_id: process.env.PROJECT_ID,
        private_key_id: process.env.PRIVATE_KEY_ID,
        private_key: process.env.PRIVATE_KEY.replace(/(\\r)|(\\n)/g, '\n'),
        client_email: process.env.CLIENT_EMAIL,
        client_id: '',
        auth_uri: 'https://accounts.google.com/o/oauth2/auth',
        token_uri: 'https://oauth2.googleapis.com/token',
        auth_provider_x509_cert_url: 'https://www.googleapis.com/oauth2/v1/certs',
        client_x509_cert_url: `https://www.googleapis.com/robot/v1/metadata/x509/${process.env.PROJECT_ID}%40appspot.gserviceaccount.com`,
      },
    }
  }
},
// ...
```

The plugin makes the following conversions before feeding Gatsby nodes:

1. Numbers are converted to numbers. Sheets formats numbers as comma-delineated
   strings, so to determine if something is a number, the plugin tests to see if
   the string (a) is non-empty and (b) is composed only of commas, decimals, and
   digits:

```
if (
    "value".replace(/[,\.\d]/g, "").length === 0
      && "value" !== ""
   ) {
    ...assume value is a number and handle accordingly
}
```

2. "TRUE"/"FALSE" converted to boolean true/false
3. empty cells ("" in sheets payload) converted to null
4. Column names are converted to camelcase

A few notes:

1. Not tested with cells of data type dates.
2. Google sheets mangles column names and converts them all to lower case. This
   plugin will convert them to camelcase, so the best convention here is to name
   your columns all lowercase with dashes. e.g. instead of "Column Name 1" or
   "columnName1", prefer "column-name-1"--this last one will be turned into
   "columnName1" in your GatsbyQL graph.

## Troubleshooting

3. If you get the error "No key or keyFile set", make sure you are using a
   Service Account API key and not a simple API key.
4. If you get the error "Cannot read property 'worksheets' of undefined", make
   sure you have shared your spreadsheet with your service account user.

## Author

👤 **Cian Butler <butlerx@notthe.cloud>**

- Website: https://cianbutler.ie
- Twitter: [@cianbutlerx](https://twitter.com/cianbutlerx)
- Github: [@butlerx](https://github.com/butlerx)

## 🤝 Contributing

Contributions, issues and feature requests are welcome!

Feel free to check
[issues page](https://github.com/butlerx/gatsby-source-google-spreadsheets/issues?q=is%3Aissue+is%3Aopen+sort%3Aupdated-desc).

## Show your support

Give a ⭐️ if this project helped you!

## 📝 License

Copyright © 2020
[Cian Butler <butlerx@notthe.cloud>](https://github.com/butlerc).

This project is
[MIT](https://github.com/butlerx/gatsby-source-google-spreadsheets/blob/master/LICENSE)
licensed.
