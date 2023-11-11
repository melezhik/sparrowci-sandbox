# sparrowci-sandbox

The only purpose of this module is to demonstrate 
usage of SparrowCI pipeline in Raku module automation.

# sparrow.yaml

This pipeline runs `zef test` and then 
if a commit message contains `release!` string
uploads a module via `fez upload`.

Pipeline requires fez token set as secret
in user's account in SparrowCI.

```yaml
image:
  - melezhik/sparrow:debian

secrets:
  - FEZ_TOKEN
tasks:
  - 
    name: test
    default: true
    followup:
      -
        name: release
    language: Bash
    code: |
      set -e
      env|grep SCM
      cd source
      zef test .
  -
    name: release
    if:
      language: Raku
      code: |
        update_state %( status => 'skip' )
          unless %*ENV<SCM_COMMIT_MESSAGE> ~~ /'release!'/;
    language: Bash
    code: |
      set -e
      cat << HERE > ~/.fez-config.json
       {"groups":[],"un":"melezhik","key":"$FEZ_TOKEN"}
      HERE

      cd source/
      zef install --/test fez

      tom --clean
      fez upload --unattended
```
# Author

Alexey Melezhik


