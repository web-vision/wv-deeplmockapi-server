wv-deeplmockapi-server - DeepL API mock server container image
==============================================================

## Description

This repository contains the (automatic) docker image build workflow for
the [DeepL API mock server](https://github.com/DeepLcom/deepl-mock).

> This repository does not contain the DeepL API mock server nor claims
> rights on it. Container images are build purely and unmodified from
> the upstream repository: https://github.com/DeepLcom/deepl-mock

## Scheduler

The scheduler execution contains following steps: 

* check if last used release version and upstream latest release tag differs, if not do nothing
* Update release information with latest release tag in `version.json` and create a commit for it
* verify image(s) can be build based on the new release version, otherwise fail
* if image could be build, create a new tag for the change commit
* push the tagged commit and create a github release with that tag and push build images to the 
  github container registry.

> Note that schedule runs create only new patchlevel release tags if new image based on new version can be build.

## CI - pull_request/push

On each pull request or push a force build is done to verify that the image can still be build with
the changes - but no release or automatic change commits are created and pushed.

## Force rebuild

Maintainer of the repository can do a force rebuild even for the same version, but only for the main branch by
invoking the `image-build` workflow from the Github Action overview or by using the `github command line tool`.

```terminal
echo '{"forceRebuild": true, "tagMode": "patch"}' | gh workflow run image-build --repo webvision/wv-deeplmockapi 
```

**forceRebuild**

If true a rebuild is processed in any case, otherwise only if a new release of the DeepL Mock Api Server is available.

**tagModes**

> The tag mode describes how the new tag should be build based on the latest existing tag.

| value | Description                     | Example          |
|-------|---------------------------------|------------------|
| major | Rise version to next major      | `1.3.5 => 2.0.0` |
| minor | Rise version to next minor      | `1.3.5 => 1.4.0` |
| patch | Rise version to next patchlevel | `1.3.5 => 1.3.6` |
