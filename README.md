# Ditto CLI

The Ditto CLI helps teams integrate [Ditto](https://dittowords.com/) into their development workflows and build processes.

## Getting Started

You can install the CLI from npm:

```bash
# as a dev dependency (recommended)
npm install --save-dev @dittowords/cli

# as a global package
npm install --global @dittowords/cli
```

The installed binary is named `ditto-cli`. You can execute it directly in `node_modules/.bin/ditto-cli` or using [npx](https://www.npmjs.com/package/npx) (with or without installation) like `npx @dittowords/cli`.

The first time you run the CLI, you'll be asked to provide an API key (found at [https://beta.dittowords.com/account/user](https://beta.dittowords.com/account/user) under **API Keys**).

Once you've successfully authenticated, you’re ready to start fetching copy! You can set up the CLI in multiple directories by running `ditto-cli` and choosing an initial project to sync from.

## Commands

### `pull`

**Usage:** `ditto-cli pull`

**Action:** Pulls text data from projects linked in `config.yml` and stores it in the `ditto/` folder.

The data will be stored in JSON files according to the `format` property specified in `config.yml`. If no format is specified, the default structured format will be used. Every time text is pulled, the existing text data is removed before new data is generated and stored.

For  more details on files generated by the `pull` command, see [JSON Files](#json-files).


### `project add`

**Usage:** `ditto-cli project add`

**Action:** Adds a project to the list of Ditto projects stored in `config.yml`. Running this will allow you to select from a list of projects in your workspace that have developer mode enabled.

### `project remove`

**Usage:** `ditto-cli project remove`

**Action:** Removes a project from the list of Ditto projects stored in `config.yml`. 

## Files

### `ditto/`

This folder houses the configuration file (`ditto/config.yml`) used by the CLI and is also the write destination for any files the CLI generates.

If you run the CLI in a directory that does not contain a `ditto/` folder, the folder and a `config.yml` file will be automatically created.

- #### `config.yml`

    This is the source of truth for a given directory about how the CLI should fetch and store data from Ditto. It includes information about which Ditto projects the CLI should pull text from and in what format the text should be stored.

    This file is managed by the `project add` / `project remove` commands, but can also be updated by manually editing it.

    **Supported properties**
    - `projects` (required) - a list of project names and ids to pull text from (see example)
    - `variants` (optional) - a `true` or `false` value indicating whether or not variant data should be pulled for the specified projects. Defaults to `false` if not specified (will likely default to `true` in future major releases).
    - `format` (optional) - the format the specified projects should be stored in. Acceptable values are `structured` or `flat`. If not specified, the default format containing block and frame data will be used.

    **Example**:
    ```
    projects:
      - name: Ditto Component Library
        id: ditto_component_library
    variants: true
    format: flat
    ```

- #### JSON Files

    The copy pulled from your Ditto projects is saved to JSON files in the `ditto/` folder. 
    
    The number of files present and the convention with which they're named will vary according to whether or not you've specified the `variants` option in `config.yml`. 

    If the `variants` property is not set to `true` in `config.yml`, all text will be stored in a file called `text.json`.

    If the `variants` property is set to `true` in `config.yml`, multiple JSON files will be generated:
    - The file `base.json` will contain the base text for the specified projects
    - The files named after the pattern `[variant-api-id].json` will contain the variant text for the specified projects (for each variant in the workspace).

- #### `index.js`

    An automatically generated driver file that simplifies the process of passing text data to Ditto JavaScript SDKs. 
    ```js
    module.exports = {
        base: require('./base.json'),
        spanish: require('./spanish.json'),
        french: require('./french.json')
    }
    ```

    Example usage:
    ```jsx
    import source from './ditto';

    const App = () => (
      <DittoProvider source={source}>
        ...
      </DittoProvider>
    )
    ```

### `~/.config/ditto`

Your API key is saved to this file in your **home directory**. Changing an API key currently requires opening this file and manually replacing the existing key.

## SDKs

Our SDKs make it easy to integrate the copy pulled from the Ditto CLI into your applications.

- [Ditto React](https://www.npmjs.com/package/ditto-react) - easily integrate Ditto into your React project; includes support for localization using variants.

## Feedback

Have feedback? We’d love to hear it! Message us at [support@dittowords.com](mailto:support@dittowords.com).
