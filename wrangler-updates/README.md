# Wrangler Updates Trick

## Setup

### Setting Node Version

All other repositories will use the version of node as specified in the `.node-version` file in this repository.

To prevent a repository from having it's `.node-version` overridden, put a blank `.node-lock` file into the target repository. The existence of that file will prevent the wrangler action from updating the node runtime version.

### Overriding

#### Adding

To add package overrides to every repo's `package.json`, simply just add the override json into `package-overrides.json`.

**Example**:

```json
{
  "overrides": {
    "wrangler": {
      "undici": "^7.29.0"
    }
  }
}
```

#### Removing

If something should be removed from every repo's `package.json`, add the json path to the key/object to `package-removals.txt`.

This supports multiple via simple csv format, and will traverse the object map to remove keys if they exist.

**Example**:

```text
overrides.wrangler,homepage
```

**NOTE**: If no overrides are needed to be removed, leave the file blank.

#### Skipping

If you want to skip adding/removing overrides, add the following to the target repo's `package.json`:

```json
{
  "custom": {
    "no_auto_override": true
  }
}
```
