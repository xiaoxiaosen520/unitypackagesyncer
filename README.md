# unity3d-package-syncer

[![npm version](https://badge.fury.io/js/unity3d-package-syncer.svg)](https://badge.fury.io/js/unity3d-package-syncer)
[![Dependency Status](https://david-dm.org/rotorz/unity3d-package-syncer.svg)](https://david-dm.org/rotorz/unity3d-package-syncer)
[![devDependency Status](https://david-dm.org/rotorz/unity3d-package-syncer/dev-status.svg)](https://david-dm.org/rotorz/unity3d-package-syncer#info=devDependencies)

A command line utility that synchronizes asset files from yarn / npm packages into an
appropriate directory of a game project that is made using the Unity game engine.


## Usage

The package can be executed using the npx tool that ships with Node.js.

Use the following command at the root of your project to synchronize the Unity packages
into your project's "Assets" directory:

```sh
$ npx unity3d-package-syncer
```

For example, if you were to add a new package with yarn:
```bash
yarn add unity3d-package-example
npx unity3d-package-syncer
```

Or, with npm:
```bash
npm install --save unity3d-package-example
npx unity3d-package-syncer
```

If your package has an editor script that needs to generate data assets (such as presets,
project settings, etc) then it should store those in the `Assets/Plugins/PackageData/{package-name}`
path. You may find the [unity3d-package-utils](https://github.com/rotorz/unity3d-package-utils)
package useful in assiting with this.


## yarn vs npm

From my experience the [yarn](https://yarnpkg.com/en/) tool seems to work better with the
Unity development workflow since packages are installed deterministically. The minimum
required version of yarn is v1.9.0.


## Creating Unity packages

The **unity3d-package-syncer** only considers packages that have the keyword
`unity3d-package` declared in their `package.json` file when checking and copying files
from the package into a Unity project.

The following is an example of how the `package.json` of such a package might look:

```json
{
  "name": "some-cool-package",
  "version": "1.0.0",
  "description": "A cool package for Unity projects which ...",
  "keywords": [
    "unity3d",
    "unity3d-package"
  ]
}
```

For an example checkout the [unity3d-package-example](https://github.com/rotorz/unity3d-package-example) repository.


## Which files are copied from the package?

All files in the package's `assets/` directory are copied into the root of the package
inside the Unity project.

Some *extra files* are also copied from the package into the Unity project:

  - `package.json` (required)

  - `LICENSE`

  - `README.md`

For example, the file structure of the following package:

```
/some-cool-package/
  |-- assets/
  |    |-- Logo.png
  |    |-- Logo.png.meta
  |-- docs/
  |    |-- getting-started.md
  |-- index.js
  |-- LICENSE
  |-- package.json
  |-- README.md
```

Results in the following directory structure in a Unity project:

```
/MyUnityProject/
  |-- Assets/
  |    |-- Plugins/
  |    |    |-- Packages/
  |    |    |    |-- some-cool-package/
  |    |    |    |    |-- Logo.png
  |    |    |    |    |-- Logo.png.meta       (Copied from package)
  |    |    |    |    |-- LICENSE
  |    |    |    |    |-- LICENSE.meta        (Unity generates this automatically)
  |    |    |    |    |-- package.json
  |    |    |    |    |-- package.json.meta   (Unity generates this automatically)
  |    |    |    |    |-- README.md
  |    |    |    |    |-- README.md.meta      (Unity generates this automatically)
  |    |    |    |-- some-cool-package.meta   (Unity generates this automatically)
  |-- Library/
  |-- package.json
```

The `*.meta` files that are automatically generated by Unity should be included for each
file that is included in the `assets/` directory of your package.

Since nothing in the Unity project should be referencing the *extra files*; there
shouldn't be any issues with these being automatically generated by Unity each time the
package is copied into your project.


## How does the **unity3d-package-syncer** know when a package needs to be copied?

- If the package has not yet been copied into the Unity project.

- If the version of the package already in the Unity project differs from the version of
  the currently installed package.


## How does the **unity3d-package-syncer** know when a package needs to be removed?

- If the package exists in the Unity project but is not declared in `package.json`.


## What happens if I modify the package files that were copied into the Unity project?

**IMPORTANT: Any modifications will be lost when the package is next updated or uninstalled.**

If you need to modify the files of the package then you will need to update the package
with the modifications and adjust the version number of the package.

If you want to modify the files of a package that you don't currently maintain, then you
will need to create a fork of the original package so that you can maintain that.

Packages with editor scripts that create asset files **SHOULD NOT** be storing these
inside the `{unity-project}/Assets/Plugins/Packages/` directory structure because these
files will be lost if the package is updated or uninstalled.

If your package needs to generate configuration and/or data files then they should be
stored in a different location in the Unity project. I would suggest that packages adopt
the following directory structure for generated data files for consistency with one
another:

```
{unity-project}/Assets/PackageData/{name-of-package}/{generated-data-files}
```


## Contribution Agreement

This project is licensed under the MIT license (see LICENSE). To be in the best
position to enforce these licenses the copyright status of this project needs to
be as simple as possible. To achieve this the following terms and conditions
must be met:

- All contributed content (including but not limited to source code, text,
  image, videos, bug reports, suggestions, ideas, etc.) must be the
  contributors own work.

- The contributor disclaims all copyright and accepts that their contributed
  content will be released to the public domain.

- The act of submitting a contribution indicates that the contributor agrees
  with this agreement. This includes (but is not limited to) pull requests, issues,
  tickets, e-mails, newsgroups, blogs, forums, etc.
