## Bump Version

1. bump version of `package.json > version` that follow semantic versioning
2. commit

```
git commit -m "bump version to x.x.x"
```

3. create a git tag for this version

```
git tag x.x.x
```

4. push the tag (GitHub Action will publish automatically)

```
git push origin x.x.x
```

## Manual Publish to npm

```
npm login
npm publish --access public
```
