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

You may test to create your own build with default settings
```bash
yarn build
```

You will get a custom build CKEditor version in `build` folder.

## 2. Install this plugin

```bash
yarn add -D ckeditor5-strapi-upload-plugin
```

Go to `src/ckeditor.js`, install this plugin.
```js
+import { StrapiUploadAdapter } from 'ckeditor5-strapi-upload-plugin';

export default class ClassicEditor extends ClassicEditorBase {}

// Plugins to include in the build.
ClassicEditor.builtinPlugins = [
	...,
+	StrapiUploadAdapter
];

// Editor configuration.
ClassicEditor.defaultConfig = {...};

```

After adding this plugin, build again.
```bash
yarn build
```

## 3. Integrate Custom CKEditor 5 with Strapi
Follow the [official Strapi tutorial](https://strapi.io/blog/how-to-change-the-wysiwyg-in-strapi).

After that, copy all the CKEditor build asset including 
- `ckeditor.js`
- `translations` folder
- `ckeditor.map.js`  
into `extensions/content-manager/admin/src/components/CKEditor`  

then modify `extensions/content-manager/admin/src/components/CKEditor` with 
the following code snippet.
```js
import React from 'react';
import PropTypes from 'prop-types';
import CKEditor from '@ckeditor/ckeditor5-react';
import styled from 'styled-components';
import { auth } from 'strapi-helper-plugin';

// import your custom build
import ClassicEditor from './ckeditor';

const Wrapper = styled.div`
  .ck-editor__main {
    min-height: 200px;
    > div {
      min-height: 200px;
    }
  }
`;

const Editor = ({ onChange, name, value }) => {
  const uploadUrl = `${strapi.backendURL}/upload`;
  const headers = {
    Authorization: "Bearer " + auth.getToken()
  };

  return (
      <Wrapper>
      <CKEditor
        editor={ClassicEditor}
        data={value}
        onChange={(event, editor) => {
          const data = editor.getData();
          onChange({ target: { name, value: data } });
        }}
        config={{
          strapiUpload: {
            uploadUrl,
            headers
          }
        }}
      />
    </Wrapper>
  );
};

Editor.propTypes = {
  onChange: PropTypes.func.isRequired,
  name: PropTypes.string.isRequired,
  value: PropTypes.string.isRequired,
};

export default Editor;
```
