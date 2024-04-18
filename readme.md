
# GenAlpha Schematics

[![codespace](https://github.com/codespaces/badge.svg "Open in GitHub Codespaces")](https://codespaces.new/gen-alpha-xtor/GenAlphaSchematicsDemo?quickstart=1)

<picture style="max-width:600px;width:100%">
  <source 
    media="(prefers-color-scheme: dark)" 
    srcset="https://raw.githubusercontent.com/gen-alpha-xtor/GenAlphaSchematics/main/files/genalpha-demo-chrome-dark.jpg"
  >
  <source 
    media="(prefers-color-scheme: light)" 
    srcset="https://raw.githubusercontent.com/gen-alpha-xtor/GenAlphaSchematics/main/files/genalpha-demo-chrome-light.jpg"
  >
  <a href=https://codespaces.new/gen-alpha-xtor/GenAlphaSchematicsDemo?quickstart=1>
  <img 
    alt="demo" 
    width="1200"
    src="https://raw.githubusercontent.com/gen-alpha-xtor/GenAlphaSchematics/main/files/genalpha-demo-chrome-light.jpg"
  >
  </a>
</picture>

---

[GenAlpha Schematics](https://marketplace.visualstudio.com/items?itemName=gen-alpha-xtor.schematics-vscode) enables editing real, production-quality schematics, live in the popular [VsCode](https://code.visualstudio.com) programming environment.

GenAlpha operates on the same [OpenAccess](https://si2.org/openaccess) databases and schematics supported by leading silicon foundry PDKs.

## Access and Authentication

GenAlpha Schematics is available on a private preview basis. Contact [dan@fritch.mn](mailto:dan@fritch.mn) if you are interested in joining the trial! 

Access is managed through [GitHub login, and its seamless VsCode integration](https://code.visualstudio.com/docs/sourcecontrol/github). Open a schematic or run the `GenAlpha: Log In With GitHub` command to log in.

![](https://raw.githubusercontent.com/gen-alpha-xtor/GenAlphaSchematics/main/files/github-popup.jpg)
![](https://raw.githubusercontent.com/gen-alpha-xtor/GenAlphaSchematics/main/files/github-web.jpg)

**Note:** __GenAlpha Schematics will never, for any reason, no matter what, send your design data to GitHub, or anywhere else outside your own servers.__ 
GitHub is used solely for user authentication and access validation. 

## Opening a Schematic or Symbol

With a database configured, just open the `sch.oa` or `symbol.oa` file in VsCode's file explorer. 
Or run `code {path}/{to}/sch.oa` from the command line.

## Setting up a Database

Configuring a database requires two primary pieces of information: 

- The library definitions. Each OpenAccess library has an associated directory on disk. Mappings from library-names to directories are dictated in a text format file defined by OpenAccess. By convention they have the filename `lib.defs`, although some popular programs will call them `cds.lib`. 
- Any environment variables required by the library definitions file. (They usually do depend on environment variables.)

Environment variables can be defined in either of two formats: 

- The popular [dotenv](https://www.dotenv.org/docs/security/env) format, supported by libraries such as [python-dotenv](https://pypi.org/project/python-dotenv/) and the original (JS) [dotenv](https://github.com/motdotla/dotenv) itself. 
- JSON, in the format `{ "VAR_NAME": "VAR_VALUE" }`.

GenAlpha will attempt to auto-configure a database from your VsCode workspace directory. It will search for both library-definitions and environment variables starting in the workspace root, and then in each parent directory until it reaches the filesystem root. 

- Library definition files named either `lib.defs` or `cds.lib` will be auto-loaded. 
  - If your library definitions are in any other path, run the `GenAlpha: Set Library Definitions` VsCode command and select their file.  
- Environment variable files named either `.env` (in dotenv format) or `env.json` (in JSON format) will be auto-loaded.
  - If your environment file is at any other path, run the `GenAlpha: Set Environment File` VsCode command to select it.

After configuring a schematic database, GenAlpha will write a resolution-file name `db.resolved.json` in the workspace root. This file contains the absolute paths to the library definitions and environment variables files that were loaded. It will also often be the best source of debug information if you're having trouble getting a database to load. The resolution-file attempts to catalog any information (typically variable-values) that it needed, but couldn't find. 

Example successful resolution-file:

```json
{
  "schemaVersion": 1,
  "env": {
    "vars": {
      "DESIGNDATA": "/designdata/"
    },
    "file": {
      "path": "/designdata/env.json",
      "fmt": "json"
    }
  },
  "libdefs": {
    "path": "/designdata/cds.lib",
    "warnings": [],
    "libs": {
      "mylib": "/designdata/mylib"
    },
    "unresolved": {
      "libs": [],
      "includes": [],
      "vars": []
    }
  }
}
```

Note running the popular Unix `env` utility *can*, but often doesn't, produce output compatible with the `dotenv` file format. Particularly `env` often includes special characters (and whitespace) which is not escaped or quoted. If running `env` to produce a `dotenv` file, expect to have to do some manual editing. JSON encoding, particularly when written by popular libraries, should avoid this problem. For example the (two-line) Python script [env.py](https://raw.githubusercontent.com/gen-alpha-xtor/GenAlphaSchematics/main/files/env.py) will produce a JSON file compatible with GenAlpha: 

```python
import os, json
open("env.json", "w").write(json.dumps(dict(os.environ), indent=2))
```

## Issues and Feature Requests

Please report any issues or feature requests to [gen-alpha-xtor/GenAlphaSchematics](https://github.com/gen-alpha-xtor/GenAlphaSchematics). Note this is not the source code repository.
