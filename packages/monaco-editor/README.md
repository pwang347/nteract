# @nteract/monaco-editor

This package implements a React component with a Monaco-based code editor. To see this package in action, you can view the source code for rendering text files in the [nteract-on-Jupyter application](https://github.com/nteract/nteract/blob/master/applications/jupyter-extension/nteract_on_jupyter/app/contents/file/text-file.js).

## Installation

```
$ yarn add @nteract/monaco-editor
```

```
$ npm install --save @nteract/monaco-editor
```

## Usage

The example below shows how we can use this package to render an editor for plain-text content.

```javascript
import MonacoEditor from "@nteract/monaco-editor";

export default () => {
  return (
    <MonacoEditor
      id="foo"
      contentRef="bar"
      theme="vscode"
      language="plaintext"
      value={"These are some words in an editor."}
    />
  );
};
```

## Documentation

### Editor
The `monaco-editor` package provides the core functionality to render [Monaco Editor](https://microsoft.github.io/monaco-editor/) as a React component. It also fetches code tab-completion items when running a notebook connected to a Jupyter kernel. To work well with the semantics of a notebook, the package requires the following props as laid down in the `IMonacoProps` interface:

* `id` - A unique identifier for the editor instance. In the notebook context, since every cell is tied to a single instance of the editor, `id` refers to the unique ID of the cell.
* `contentRef` - A unique identifier for the editor's host application. In the notebook context, `contentRef` provides a reference to the container element for the main notebook app component.
* `theme` - Theme to be used for rendering the component ([docs](https://microsoft.github.io/monaco-editor/api/interfaces/monaco.editor.idiffeditorconstructionoptions.html#theme))
* `language` - Valid language ID of a supported language (eg: `python`, `typescript`, `plaintext` etc.) Check out this Monaco Editor [playground](https://microsoft.github.io/monaco-editor/playground.html#extending-language-services-custom-languages) to add support for a language not yet supported out of the box.

Besides the minimum required props to instantiate the component, we also provide support for a host of optional properties and handlers. These optional properties are found:

`options` - Specify a list of supported [EditorOptions](https://microsoft.github.io/monaco-editor/api/interfaces/monaco.editor.ieditoroptions.html) as key-value pairs when instantiating the component

Important callbacks:
* `onChange: (value: string, event?: any) => void` - Contents of the editor are changed.
* `onFocusChange: (focus: boolean) => void` - The Editor Component loses or gains focus
* `onCursorPositionChange: (selection: monaco.ISelection | null) => void` - Cursor position changes


### Completions
The package also adds the capability to retrieve code-completion items from a connected Jupyter kernel. Completions are language-specific token recommendations when the user attempts to type or enumerate the attributes of a class/object (usually with a `dot` operator and the `tab` completion key). While we provide a default completion provider that works with the Jupyter kernel, we also support custom completion providers that users might want to register with their own language service.
![jupyter completions](https://i.stack.imgur.com/rcieN.png)

The completion behavior is controlled by the following props:

* `enableCompletion` - Boolean flag to enable/disable the behavior entirely
* `shouldRegisterDefaultCompletion` - Boolean flag to enable/disable the default completion provider
* `onRegisterCompletionProvider?: (languageId: string) => void` - Custom completion provider implementation for a Monaco Editor supported language.
* `onRegisterDocumentFormattingEditProvider?: (languageId: string) => void` - Custom document formatting provider implementation for a Monaco Editor supported language.

### Performance Tip

If you want to enable completions in your app when you use this package, it is ideal to do it on a [`Web worker`](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) which is separate from the UI thread. This provides a performance boost and ensures that the app doesn't stall UI updates when the editor is waiting for completions from the Jupyter Kernel. In Nteract, we use the [Monaco Editor Web pack plugin](https://github.com/microsoft/monaco-editor-webpack-plugin) to register and use the `editor` worker by Monaco. Check out the Monaco Editor [docs](https://github.com/microsoft/monaco-editor/blob/master/docs/integrate-esm.md) for more information on configuring the package and setting up web workers in a different set up.

Improving window resizing performance:
On resizing the browser window, the width of the container of this component is recalculated. Consider using these simple css overrides to further improve resizing performance:

```css
.monaco-container .monaco-editor {
  width: inherit !important;
}

.monaco-container .monaco-editor .overflow-guard {
  width: inherit !important;
}

/* 26px is the left margin for .monaco-scrollable-element  */
.monaco-container .monaco-editor .monaco-scrollable-element.editor-scrollable.vs {
  width: calc(100% - 26px) !important;
}
```
These style overrides for resize performance can also be found in the `@nteract/styles` package.
The CSS can be imported in a top level React component simply by doing:
```typescript
import "@nteract/styles/monaco/overrides.css";
```

## Support

If you experience an issue while using this package or have a feature request, please file an issue on the [issue board](https://github.com/nteract/nteract/issues/new/choose) and add the `pkg:monaco-editor` label.

## License

[BSD-3-Clause](https://choosealicense.com/licenses/bsd-3-clause/)
