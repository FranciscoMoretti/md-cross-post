# Cross Post Tool for Notion

This tool allows posting from Notion to different platforms.

- [Cross Post Tool for Notion](#cross-post-tool-for-notion)
  - [Supported Platforms](#supported-platforms)
  - [Environment Variables](#environment-variables)
    - [Notion](#notion)
    - [GitHub](#github)
    - [Dev.to](#devto)
    - [Hashnode](#hashnode)
    - [Medium](#medium)
  - [Installation](#installation)
    - [NPX Command](#npx-command)
    - [CLI Tool](#cli-tool)
    - [GitHub Repository](#github-repository)
  - [Usage](#usage)
    - [Post Command](#post-command)
      - [Options](#options)
        - [Platforms](#platforms)
        - [Dry Run](#dry-run)
  - [Config](#config)
    - [Available Configurations](#available-configurations)
    - [Environment Variables when Loading Configurations from a Custom File](#environment-variables-when-loading-configurations-from-a-custom-file)
  - [Limitations](#limitations)
    - [GitHub Limitations](#github-limitations)
    - [Dev.to Limitations](#devto-limitations)
    - [Hashnode Limitations](#hashnode-limitations)
  - [FAQ \& Troubleshooting](#faq--troubleshooting)
    - [How do I obtain the URL of the Notion document?](#how-do-i-obtain-the-url-of-the-notion-document)
    - [I get the error "No matches found: URL"](#i-get-the-error-no-matches-found-url)
    - [NPX doesn't use latest version by default](#npx-doesnt-use-latest-version-by-default)
    - [Medium tags dictionary](#medium-tags-dictionary)

## Supported Platforms

- [x] GitHub (Markdown)
  - [x] frontmatter customization
- [x] Dev.to
- [x] Hashnode
- [x] Medium

## Environment Variables

### Notion

- `NOTION_TOKEN`: Token on a Notion internal integration

### GitHub

- `GH_TOKEN`: (required) GitHub personal token
- `GH_OWNER`: (required) GitHub username
- `GH_REPO`: (required) Repository name
- `GH_BRANCH`: Branch name. default is `master`.

### Dev.to

- `DEVTO_API_KEY`: (required) Your personal dev.to API key. Learn how to retrieve it [here](https://developers.forem.com/api/v0).
- `DEVTO_ORG_ID`: The ID of the organization to publish the article under. You can retrieve it either from the organization dashboard page, where the ID is the last part of the URL (`https://dev.to/dashboard/organization/ORG_ID`). Alternatively, you can use Dev.to's [List Organizations](https://developers.forem.com/api/v0#tag/organizations/operation/getOrgUsers) endpoint to find the ID.

### Hashnode

- `HASHNODE_TOKEN`: (required) Hashnode personal token
- `HASHNODE_PUB_ID`: (required) The ID of the publication to publish the article under. You can retrieve it either from the publication's dashboard page, where the ID is the second part of the URL (`https://hashnode.com/PUB_ID/dashboard`).

### Medium

- `MEDIUM_TOKEN`: (required) Medium Integration Token. Can be retrieved from [here](https://medium.com/me/settings/security).
- `MEDIUM_PUB_NAME`: The name of the Medium publication. Must be the exact name as it is on Medium.

## Installation

### NPX Command

You can use this command as an NPX command:

```bash
npx cross-post-notion
```

### CLI Tool

You can install this as a global CLI tool:

```bash
# using npm
npm install -g cross-post-notion

# using yarn
yarn add cross-post-notion global
```

### GitHub Repository

You can clone this repository, then run the following command after installing the dependencies:

```bash
# using npm
npm start

# using yarn
yarn start
```

## Usage

### Post Command

To cross-post an article from Notion to other platforms, use the `post` command:

```bash
# using npx
npx cross-post-notion <url>

# using CLI tool
cross-post-notion <url>

# using cloned repository
npm start post <url>
```

Where `<url>` is the URL of the Notion document.

#### Options

##### Platforms

By default, the article will be published to github, devto, hashnode, and medium.

You can specify which platforms to publish the article on using the `-p, --platforms` option. For example:

```bash
yarn start example.com -p devto hashnode
```

This will publish the article only on dev.to and hashnode.

Allowed values are:

- `github`
- `devto`
- `hashnode`
- `medium`

##### Dry Run

If you want to test out the entire flow without actually posting the article, you can pass the `-d, --dryRun` option:

```bash
yarn start example.com --dryRun
```

## Config

By default, this tool will look for configurations under `config/default.json` or `config/local.json`. You can also pass the `-c, --config` option to load configurations from a different JSON file.

For example:

```bash
yarn start <url> -c path/to/file.json
```

### Available Configurations

The JSON configuration file can have the following fields:

```json
{
  "config": {
    "notion": {
      "options": {
        "skip_block_types": []
      }
    },
    "github": {
      "options": {
        "image_path": "public",
        "image_prefix": "/",
        "article_path": "content",
        "properties": {
          "title": "Title for Blog",
          "date": "Publishing Date",
          "slug": "Slug"
        },
        "add_default_frontmatter": true,
        "frontmatter_labels": {
          "title": "title",
          "date": "date"
        },
        "extra_frontmatter": {
          "excerpt": "this is description",
          "nested": {
            "field1": "value1"
          }
        },
        "extra_frontmatter_mapper": {
          "excerpt": "Description"
        }
      }
    },
    "devto": {
      "options": {
        "should_publish": false,
        "properties": {
          "title": "Title for Dev.to"
        }
      }
    },
    "hashnode": {
      "options": {
        "should_publish": true,
        "should_notify_followers": false,
        "properties": {
          "title": "Title for Hashnode"
        }
      }
    },
    "medium": {
      "options": {
        "should_publish": false,
        "should_notify_followers": false,
        "properties": {
          "title": "Title for Medium"
        }
      }
    }
  }
}
```

Where:

- `config`: wraps all configurations.
- `notion`: A JSON object with all configurations related to Notion. These are:
  - `options`: Include all options related to cross posting:
    - `skip_block_types`: An array of block types that should be skipped from the Notion document. For example, `toggle` would skip all toggle list blocks in the Notion document.
- `github`: A JSON object with all configurations related to GitHub. These are:
  - `options`: Include all options related to cross posting:
    - `image_path`: The path in the repository to upload images to.
    - `image_prefix`: The prefix to add to images in the markdown output.
    - `article_path`: The path in the repository to upload the markdown article to.
    - `add_default_frontmatter`: Whether to add default frontmatter to the markdown file. The default frontmatter are:
      - `title`
      - `date`
      - `slug`
    - `properties`: A JSON object that allows you to override the name of the properties in Notion to pull the values of frontmatter fields.
    - `frontmatter_labels`: A JSON object that allows you to override the labels of the frontmatter fields.
    - `extra_frontmatter`: Allows you to add extra frontmatter to the output markdown.
    - `extra_frontmatter_mapper`: If you want the values of the frontmatter keys in `extra_frontmatter` to be pulled out of Notion, you can map each key to a property name in the Notion document.
- `devto`: A JSON object with all configurations related to Dev.to. These are:
  - `should_publish`: A boolean value indicating whether the article should be created in dev.to as a draft or it should be published.
  - `properties`: A JSON object that allows you to override the name of the properties in Notion to pull the values of frontmatter fields. You can set the following properties:
    - `title`
    - `date`
    - `description`
    - `tags` (the property's value in Notion should be a comma separated list.)
    - `series` (the property's value in Notion should be the name of the series)
    - `canonical_url`
    - `description`
- `hashnode`: A JSON object with all the configurations related to Hashnode. These are:
  - `should_hide`: A boolean value indicating whether the post should be shown on Hashnode's public feed or not. Learn more in the [limitations section](#hashnode-limitations).
  - `properties`: A JSON object that allows you to override the name of the properties in Notion to pull the values of frontmatter fields. You can set the following properties:
    - `title`
    - `original_article_url` (refers to the property holding the canonical url of the article)
    - `tags` (the property's value in Notion should be a list of tag names separated by a comma. The tag name can either be tag slug or display name)
    - `subtitle`
- `medium`: A JSON object with all the configurations related to Medium. These are:
  - `should_publish`: A boolean value indicating whether the article should be created in dev.to as a draft or it should be published.
  - `should_notify_followers`: A boolean value indicating whether your followers should be notified about the article.
  - `properties`: A JSON object that allows you to override the name of the properties in Notion to pull the values of frontmatter fields. You can set the following properties:
    - `title`
    - `subtitle`
    - `tags` (the property's value in Notion should be a list of tag names separated by a comma.)
    - `canonical_url`

### Environment Variables when Loading Configurations from a Custom File

When you're loading configurations from a custom file using the `-c, --config` option, you can't use the environment variables anymore. You'll have to pass them in the same config file as follows:

```json
{
  "config": {
    "notion": {
      "connection_settings": {
        "token": "..."
      }
    },
    "github": {
      //...
      "connection_settings": {
        "token": "...",
        "owner": "...",
        "repo": "...",
        "branch": "..."
      }
    },
    "devto": {
      //...
      "connection_settings": {
        "api_key": "...",
        "organization_id": "..."
      }
    },
    "hashnode": {
      "connection_settings": {
        "token": "...",
        "publication_id": "..."
      }
    },
    "medium": {
      "connection_settings": {
        "token": "...",
        "publication_name": "..."
      }
    }
  }
}
```

## Limitations

### GitHub Limitations

- If a file with the same name and path exists in the repository, an error is thrown. There's no support for updating files.

### Dev.to Limitations

- As Dev.to does not expose an endpoint to upload images, it's not possible to upload images in the article. Images are added as they are in Notion, which may or may not work as expected. You'll have to upload them manually from Dev.to's interface.

### Hashnode Limitations

- Hashnode's API does not provide the option to post an article as a draft. As an alternative, the `should_hide` will allow you to hide the article from Hashnode's public feed. It will still, however, show up on your blog.
- Due to a limitation in Hashnode's APIs, it's not possible to map all available categories. So, some categories you use in Notion will not show up when you cross-post to Hashnode.

## FAQ & Troubleshooting

### How do I obtain the URL of the Notion document?

To retrieve the URL of a Notion document:

1. In the document, click on Share at the top right.
2. Click on Copy Link at the bottom right of the pop up.

The URL should be of the format `https://www.notion.so/{workspace}/{path}`.

### I get the error "No matches found: URL"

When you copy the URL from Notion, sometimes it has a query parameter at the end of it such as `?psv=4`. Some terminals can't read that.

Make sure to remove any query parameters and try again.

### NPX doesn't use latest version by default

After the first time you use the command with NPX, it might not use updated versions moving forward.

So, it's recommended to use `@latest` with your commands:

```bash
npx cross-post-notion@latest <url>
```

### Medium tags dictionary

The Medium tags dictionary can be filled with your tags of interest by copying the tag strings from any of your Medium pulbications or drafts.
Try to add a tag by writing in `Settings > Reader Interests > Topics` to find the tag string. Then simply write it in the `medium-tags-dictionary.json` file.

NOTE: It needs to be slugified by replacing spaces with `-` and by using lowercase.
