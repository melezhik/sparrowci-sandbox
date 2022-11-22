# sparrowci-sandbox

The only purpose of this module is demonstrate SparrowCI pipeline

# sparrow.yaml

This pipeline runs zef test and
uploads module via fez if commit message contains
"ci: fez upload" string

```yaml
secrets:
  - FEZ_TOKEN
tasks:
  - name: fez-upload
    default: true
    language: Raku
    init: |
      run_task "test";
      if config()<tasks><git-commit><state><comment> ~~ /'ci: fez upload'/ {
        run_task "upload"
      }
    subtasks:
    - 
      name: test
      language: Bash
      code: |
        set -e
        cd source
        zef test .
    -
      name: upload
      language: Bash
      code: |
        set -e
        cat << HERE > ~/.fez-config.json
          {"groups":[],"un":"melezhik","key":"${FEZ_TOKEN}"}
        HERE
        cd source/
        fez checkbuild
        tom --clean
        fez upload
    depends:
      -
        name: git-commit
  - name: git-commit
    plugin: git-commit-data
    config:
      dir: source
```
# Author

Alexey Melezhik
