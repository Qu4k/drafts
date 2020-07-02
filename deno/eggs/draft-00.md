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
https://nest.land/docs#configuration

## future implementation

fields will be covered individually, each one will contain a proposal to
make it context dependent and remove the need to explicitly declare it in
the configuration.

#### preconceptions

the CLI must be able to call the API and gather info about the module after
the first publish. keep in mind that a configuration file can be always
provided to ovverride any of the parameters found from context. NVC stands for
"no version control".

### `name`

will be retrieved from the root directory name, not required after the first
publish.

### `description`

will be retrieved from `README.md` subtitle / line after title but can be 
asked during first publish in the CLI.

### `version`

requires a version control system in the module directory. Can be retrieved
parsing output from the CLI of the version control system:

 *  SVN:  `$ svn log <repo>/tags --limit 1`
 *  GIT:  `$ git describe`
 *  NVC:  the `CHANGELOG.md` file or CLI input

this information can be also retrieved without using `Deno.run()` by simply
parsing version control files (eg `.git/refs/tags/`).
this information can be also retrieved in a `CHANGELOG.md` file.

if this information is not retrievable with any of the methods described above
it can be also retrieved with a direct prompt from the CLI in the first
publish:

```bash
version?: <input>
```

in the subsequent publishes the CLI could fetch the latest known version and
display it as input:

```bash
previous version was <api retrieved version>
version?: <input>
```

### `stable`

arbitrary, can be moved to cli input or as a `--unstable` flag.

### `fmt`

arbitrary, can be moved to cli input or as a `--fmt` flag. (eg: `would you like to run "deno fmt"
before publishing?`).

### `repository`

can be retrieved by listing remote repositories with version control:

 *  SVN:  `$ svn info`
 *  GIT:  `$ git remote -v`
 *  NVC:  CLI input

or as a cli input.

### `files`

files can be retrieved in an opt-out fashion. The CLI should be able to parse a `.ignore`-type file, if provided, and exclude files/directories from publishing:

 *  SVN:  `$ svn proplist -v -R dir/`
 *  GIT:  the `.gitignore` file
 *  NVC:  the `.eggsignore` file
