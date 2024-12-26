
# Obsidian-envelope-plugin Mkdocs Template

Key example:

```json
{
  "github": {
    "branch": "main",
    "automaticallyMergePR": true,
    "dryRun": {
      "enable": false,
      "folderName": "enveloppe"
    },
    "tokenPath": "%configDir%/plugins/%pluginID%/env",
    "api": {
      "tiersForApi": "Github Free/Pro/Team (default)",
      "hostname": ""
    },
    "workflow": {
      "commitMessage": "[PUBLISHER] Merge",
      "name": ""
    },
    "verifiedRepo": true
  },
  "upload": {
    "behavior": "obsidian",
    "defaultName": "docs",
    "rootFolder": "",
    "yamlFolderKey": "",
    "frontmatterTitle": {
      "enable": false,
      "key": "title"
    },
    "replaceTitle": [],
    "replacePath": [],
    "autoclean": {
      "includeAttachments": true,
      "enable": true,
      "excluded": [
        "docs/assets/js",
        "docs/assets/meta",
        "docs/assets/css",
        "docs/tags.md",
        "docs/home.md"
      ]
    },
    "folderNote": {
      "enable": true,
      "rename": "index.md",
      "addTitle": {
        "enable": false,
        "key": "title"
      }
    },
    "metadataExtractorPath": ""
  },
  "conversion": {
    "hardbreak": false,
    "dataview": true,
    "censorText": [
      {
        "entry": ".excalidraw",
        "replace": ".svg",
        "flags": "",
        "after": false
      }
    ],
    "tags": {
      "inline": true,
      "exclude": [],
      "fields": []
    },
    "links": {
      "internal": false,
      "unshared": false,
      "wiki": false,
      "slugify": "disable",
      "unlink": false
    }
  },
  "embed": {
    "attachments": true,
    "overrideAttachments": [],
    "keySendFile": [],
    "notes": true,
    "folder": "docs/assets",
    "convertEmbedToLinks": "keep",
    "charConvert": "->",
    "unHandledObsidianExt": [],
    "sendSimpleLinks": true,
    "forcePush": true,
    "useObsidianFolder": true
  },
  "plugin": {
    "shareKey": "publish",
    "excludedFolder": [],
    "copyLink": {
      "enable": false,
      "links": "",
      "removePart": [],
      "transform": {
        "toUri": true,
        "slugify": "lower",
        "applyRegex": []
      }
    },
    "setFrontmatterKey": "Set"
  },
  "tabsId": "help"
}
```