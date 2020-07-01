## Bump Version

1. bump version of `package.json > version` that follow semantic versioning
1. commit
1. create a git tag for this version
1. push the tag (GitHub Action will publish automatically)

## Manual Publish to npm

```
npm login
npm publish --access public
```
