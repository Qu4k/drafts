# eggs cli v-00, contextual awareness

## abstract

remove the need of a configuration file for nest.land modules, gathering much
of the information from the module directory itself.

## current implementation

as it stands the basic - but complete - implementation of a nest.land eggs.json
file is:

```json
{
    "name": "module-name",
    "description": "Your brief module description",
    "version": "0.0.1",
    "entry": "./src/main.ts",
    "stable": true,
    "unlisted": false,
    "fmt": true,
    "repository": "https://github.com/your_name/your_project",
    "files": [
        "./mod.ts",
        "./src/**/*",
        "./README.md"
    ]
}
```

information for all the fields can be retrieved from the documentation:
<https://nest.land/docs#configuration>

## future implementation

the methods proposed are considered a direct substitute for the current
`eggs.json` file. this does not mean that `eggs.json` will be deprecated
though, since the user can always provide one. our focus should be ease
of use without the *need* of a file. if the user chooses to make use of
a file for fine control he is free to do so.

if no `eggs.json` is provided, the `publish` command will now consist of
a series of questions with default answers gathered from the methods
below. not all fields are required in every publish event.

### ... first of all

* the CLI must be able to call the API and gather info about the module
  after the first publish.
* "only first publish" indicates that this field is no longer needed after
  the first publish, but (with the exception of name) can be changed later with
  cli flag (eg. `--override-description`)
* NVC stands for "no version control".

### `name`

* only first publish
* suggestion can be retrieved from root directory name or `README.md`

### `description`

* only fist publish
* suggestion can be retrieved from `README.md` subtitle / line after title

### `version`

* every publish
* SVN:  `$ svn log <repo>/tags --limit 1`
* GIT:  `$ git describe`
* NVC:  the `CHANGELOG.md` file or CLI input

this information can be also retrieved without using `Deno.run()` by simply
parsing version control files (eg `.git/refs/tags/`).
this information can be also retrieved in a `CHANGELOG.md` file.

### `stable`

* arbitrary
* can be moved to cli input or as a `--unstable` flag.

### `fmt`

* arbitrary
* can be moved to cli input or as a `--fmt` flag.
  (eg: `would you like to run "deno fmt" before publishing?`).

### `repository`

* only first publish
* SVN:  `$ svn info`
* GIT:  `$ git remote -v`
* NVC:  CLI input

### `files`

files can be retrieved in an opt-out fashion. The CLI should be able to parse
a `.ignore`-type file, if provided, and exclude files/directories
from publishing:

* SVN:  `$ svn proplist -v -R dir/`
* GIT:  the `.gitignore` file
* NVC:  the `.eggsignore` file
