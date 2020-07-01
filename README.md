# CKEditor 5 Strapi Upload Adapter Plugin

1. You need to create your custom build of CKEditor.
2. Register this plugin into your custom build
3. Integrate with Strapi

## 1. Create your own CKEditor

For more details, check this [official tutorial](https://ckeditor.com/docs/ckeditor5/latest/builds/guides/integration/installing-plugins.html).

```bash
git clone -b stable https://github.com/ckeditor/ckeditor5-build-classic.git
cd ckeditor5-build-classic
yarn install
```

You may test your own build with default settings before install this plugin.

```bash
yarn build
```

After the build, you will get a custom CKEditor in `build` folder.

## 2. Install this plugin

In your custom CKEditor build, install this plugin in package manager.

```bash
yarn add -D @gtomato/ckeditor5-strapi-upload-plugin
```

Go to `src/ckeditor.js`, make the following changes to load this plugin.

```diff
+import { StrapiUploadAdapter } from '@gtomato/ckeditor5-strapi-upload-plugin';

export default class ClassicEditor extends ClassicEditorBase {}

// Plugins to include in the build.
ClassicEditor.builtinPlugins = [
	...,
+	StrapiUploadAdapter
];

// Editor configuration.
ClassicEditor.defaultConfig = {...};

```

If you make any changes in `src/ckeditor.js`, you have to build again.

```bash
yarn build
```

## 3. Integrate Custom CKEditor 5 with Strapi (Without publish to NPM)

Follow the [official Strapi tutorial](https://strapi.io/blog/how-to-change-the-wysiwyg-in-strapi) to
replace draftjs with CKEditor 5.

After that, copy all the CKEditor build asset including

- `ckeditor.js`
- `translations` folder
- `ckeditor.map.js` (Optional)
  into `extensions/content-manager/admin/src/components/CKEditor`

Then, remove the original `@ckeditor/ckeditor5-build-classic` that you will manually
replace it with your custom build.

```bash
yarn remove @ckeditor/ckeditor5-build-classic
```

Last, modify `extensions/content-manager/admin/src/components/CKEditor` with
the following code snippet.

```js
import React from 'react'
import PropTypes from 'prop-types'
import CKEditor from '@ckeditor/ckeditor5-react'
import styled from 'styled-components'
import { auth } from 'strapi-helper-plugin'

// replace with your custom build
import ClassicEditor from './ckeditor'

const Wrapper = styled.div`
  .ck-editor__main {
    min-height: 200px;
    > div {
      min-height: 200px;
    }
  }
`

const Editor = ({ onChange, name, value }) => {
  const uploadUrl = `${strapi.backendURL}/upload`
  const headers = {
    Authorization: 'Bearer ' + auth.getToken(),
  }

  return (
    <Wrapper>
      <CKEditor
        editor={ClassicEditor}
        data={value}
        onChange={(event, editor) => {
          const data = editor.getData()
          onChange({ target: { name, value: data } })
        }}
        config={{
          strapiUpload: {
            uploadUrl,
            headers,
          },
        }}
      />
    </Wrapper>
  )
}

Editor.propTypes = {
  onChange: PropTypes.func.isRequired,
  name: PropTypes.string.isRequired,
  value: PropTypes.string.isRequired,
}

export default Editor
```

Finally, build and restart Strapi server.

```bash
npx strapi build && npx strapi develop
```

Done 👍!

## Trouble Shooting

### ckeditor-duplicated-modules

1. Please ensure your custom CKEditor 5 build dependencies version `>= 19.0.0`
1. After update/check the dependencies, try to have a clean build for you CKEditor 5

```bash
rm -rf node_modules && yarn install
yarn build
```

3. Use `npm ls`, search `@ckeditor` to see if there is duplicate modules.

If the problem still exist, please read [the official documentation](https://ckeditor.com/docs/ckeditor5/latest/framework/guides/support/error-codes.html#error-ckeditor-duplicated-modules),
