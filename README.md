[![cover][cover-src]][cover-href]
[![npm version][npm-version-src]][npm-version-href] 
[![npm downloads][npm-downloads-src]][npm-downloads-href] 
[![bundle][bundle-src]][bundle-href] 
[![License][license-src]][license-href]

# ✨ gitize

> 🌟 Easily fetch templates and Git repositories with joy!

## ✨ Features
🌟 Support for popular Git providers (GitHub, GitLab, Bitbucket, Sourcehut) out of the box.

📂 Built-in and custom template registry.

🚀 Fast cloning using tarball gzip without depending on local git and tar.

💻 Works online and offline with disk cache support.

🤖 Custom template provider support with programmatic usage.

🔍 Support for extracting with a subdir.

🔒 Authorization support to download private templates.

Enjoy exploring and trying out Gitize!

## 🚀 Usage (CLI)

```bash
npx gitize@latest <template> [<dir>] [...options]
```

### 🎯 Arguments

- 📝 **template**: Template name or a URI describing provider, repository, subdir, and branch/ref. (See [Examples](#examples))
- 📁 **dir**: A relative or absolute path where to extract the template.

### ⚙️ Options

- `--force`: Clone to existing directory even if exists.
- `--offline`: Do not attempt to download and use cached version.
- `--prefer-offline`: Use cache if exists otherwise try to download.
- `--force-clean`: 🔥 Remove any existing directory or file recursively before cloning. 
- `--shell`: 🐚 Open a new shell with current working directory in cloned dir. (Experimental).
- `--registry`: URL to a custom registry. (Can be overridden with `GITIZE_REGISTRY` environment variable).
- `--no-registry`: Disable registry lookup and functionality.
- `--verbose`: Show verbose debugging info.
- `--cwd`: Set current working directory to resolve dirs relative to it.
- `--auth`: Custom Authorization token to use for downloading template. (Can be overridden with `GITIZE_AUTH` environment variable).

### 💡 Examples

```sh
# Clone nuxt starter from gitize template registry
npx gitize@latest nuxt

# Clone the main branch of github.com/unjs/template to unjs-template directory
npx gitize@latest gh:unjs/template

# Clone to myProject directory
npx gitize@latest gh:unjs/template myProject

# Clone dev branch
npx gitize@latest gh:unjs/template#dev

# Clone /test directory from main branch
npx gitize@latest gh:unjs/template/test

# Clone from gitlab
npx gitize@latest gitlab:unjs/template

# Clone from bitbucket
npx gitize@latest bitbucket:unjs/template

# Clone from sourcehut
npx gitize@latest sourcehut:pi0/unjs-template
```

## 📂 Template Registry

Gitize has a built-in HTTP registry system for resolving templates. This way you can support template name shortcuts and meta-data. Default registry is served from [nyxlabs/gitize/templates](./templates/).

If you want to add your template to the built-in registry, just drop a PR to add it to the [./templates](./templates) directory. Slugs are added on first-come first-served basis but this might change in the future.

### ✨ Custom Registry

Creating a custom registry is like weaving a magical spell - it lets you take control of your templates and unleash their full potential. Your custom registry should provide an endpoint with a dynamic path `/:template.json` that returns a JSON response with keys same as [custom providers](#custom-providers).

- 📛 `name`: (required) Name of the template.
- 📦 `tar` (required) Link to the tar download link.
- 📁 `defaultDir`: (optional) Default cloning directory.
- 🔗 `url`: (optional) Webpage of the template.
- 🔍 `subdir`: (optional) Directory inside the tar file.
- 📝 `headers`: (optional) Custom headers to send while downloading template.

Because of the simplicity, you can even use a GitHub repository as template registry. But, with a custom registry, you can build something even more powerful by bringing your own API. So, let your magic flow and create your own custom registry with Gitize!

## 👨‍💻 Usage:

Install:

```sh
#nyxi
nyxi gitize

# pnpm
pnpm install gitize

# npm
npm install gitize

# yarn
yarn install gitize
```

📥 Import:

```js
// ESM
import { downloadTemplate } from 'gitize'

// CommonJS
const { downloadTemplate } = require('gitize')
```

### 📥 `downloadTemplate(source, options?)`

**💡 Example::**

```js
const { source, dir } = await downloadTemplate('github:nyxlabs/template')
```

**⚙️ Options:**

- `source`: (string) Input source in the format of `[provider]:repo[/subpath][#ref]`.
- `options`: (object) Options are usually inferred from the input string. You can customize them.
  - 📂 `dir`: (string) Destination directory to clone to. If not provided, `user-name` will be used relative to the current directory.
  - 🤖 `provider`: (string) Either `github`, `gitlab`, `bitbucket` or `sourcehut`. The default is `github`.
  - 📦 `repo`: (string) Name of the repository in the format of `{username}/{reponame}`.
  - 🔖 `ref`: (string) Git ref (branch or commit or tag). The default value is `main`.
  - 🔍 `subdir`: (string) Directory of the repo to clone from. The default value is none.
  - 🔥 `force`: (boolean) Extract to the existing directory even if it already exists.
  - 🧹 `forceClean`: (boolean) ⚠️ Clean up any existing directory or file before cloning.
  - 🌐 `offline`: (boolean) Do not attempt to download and use a cached version.
  - 💾 `preferOffline`: (boolean) Use the cache if it exists; otherwise, try to download.
  - 🗺️ `providers`: (object) A map from provider name to custom providers. Can be used to override built-ins too.
  - 📂 `registry`: (string or false) Set to `false` to disable the registry. Set to a URL string (without a trailing slash) for a custom registry. (Can be overridden with `GITIZE_REGISTRY` environment variable).
  - 🗂️ `cwd`: (string) Current working directory to resolve directories relative to it.
  - 🔑 `auth`: (string) Custom authorization token to use for downloading the template. (Can be overridden with `GITIZE_AUTH` environment variable).

**🎁 Return value:**

The return value is a promise that resolves to the resolved template.

- 📂 `dir`: (string) Path to the extracted directory.
- 🚀 `source`: (string) Normalized version of the input source without the provider.
- [other provider template keys]
  - 🔗 `url`: (string) URL of the repository that can be opened in a browser. Useful for logging.

## 🤖 Custom Providers

With Gitize's programmatic method, you can create your own custom template providers and unlock a world of possibilities!

```ts
import type { TemplateProvider } from 'gitize'

const rainbow: TemplateProvider = async (input, { auth }) => {
   return {
      name: 'rainbow',
      version: input,
      headers: { authorization: auth },
      url: `https://rainbow.template/?variant=${input}`,
      tar: `https://rainbow.template/dl/rainbow.${input}.tar.gz`,
   }
}

const { source, dir } = await downloadRepo('rainbow:one', {
   providers: { rainbow },
})
```

### 📦 Custom Registry Providers

You can define additional [custom registry](#custom-registry) providers using the `registryProvider` utility and register them with `providers`. With this feature, you can further customize your Gitize experience and make it work for you!

```ts
import { registryProvider } from 'gitize'

const themes = registryProvider(
   'https://raw.githubusercontent.com/nyxlabs/gitize/main/templates'
)

const { source, dir } = await downloadRepo('themes:test', {
   providers: { themes },
})
```

## 💻 Development

- 🐙 Clone this repository
- 🔌 Enable [Corepack](https://github.com/nodejs/corepack) using `corepack enable` (use `npm i -g corepack` for Node.js < 16.10)
- 📦 Install dependencies using `nyxi`
- 🧪 Run interactive tests using `nyxr dev`

## 📜 License

[MIT](./LICENSE) Made with 💞



<!-- Badges -->

[npm-version-src]: https://img.shields.io/npm/v/gitize?style=flat&colorA=18181B&colorB=14F195
[npm-version-href]: https://npmjs.com/package/gitize
[npm-downloads-src]: https://img.shields.io/npm/dm/gitize?style=flat&colorA=18181B&colorB=14F195
[npm-downloads-href]: https://npmjs.com/package/gitize
[bundle-src]: https://img.shields.io/bundlephobia/minzip/gitize?style=flat&colorA=18181B&colorB=14F195
[bundle-href]: https://bundlephobia.com/result?p=gitize
[license-src]: https://img.shields.io/github/license/nyxblabs/gitize.svg?style=flat&colorA=18181B&colorB=14F195
[license-href]: https://github.com/nyxblabs/gitize/blob/main/LICENSE

<!-- Cover -->
[cover-src]: https://raw.githubusercontent.com/nyxblabs/gitize/main/.github/assets/cover-github-gitize.png
[cover-href]: https://💻nyxb.ws
