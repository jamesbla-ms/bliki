# Overview

I prefer the following when laying out a new repository.

```text
/
├─ .github/workflows                # GitHub Actions
├─ .pipelines/                      # Azure DevOps Pipelines
│  ├─ templates/                    # Reusable templates for jobs etc.
│  |  ├─ template-purpose.yml
│  ├─ variables/                    # Variables, obviously. These should stack. env-name.yml inherits common.yml and overrides as necessary
│  |  ├─ common.yml
│  |  ├─ env-name.yml
│  ├─ ci-app-or-purpose.yml
│  ├─ pr-build.yml
├─ docs/                            # These are all recommmended topics.
│  ├─ architecture/
│  ├─ assets/
│  ├─ configuration/
│  ├─ deployment/
│  ├─ environments/
│  ├─ images/
│  ├─ observability/
│  ├─ testing/
│  ├─ README.md
├─ scripts/
├─ src/
├─ tools/
├─ .gitignore
├─ README.md
```
