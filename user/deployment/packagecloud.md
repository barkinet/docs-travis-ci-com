---
title: packagecloud Deployment
layout: en
permalink: /user/deployment/packagecloud/
---

Travis CI can automatically push your RPM, Deb, Deb source, or RubyGem package build
artifacts to [packagecloud.io](https://packagecloud.io/) after a successful build.

For a minimal configuration, all you need to do is add the following to your `.travis.yml`:

    deploy:
      provider: packagecloud
      username: "YOUR USERNAME"
      token: "YOUR TOKEN"
      dist: "YOUR DIST" # like 'ubuntu/precise', or 'centos/5', if pushing deb or rpms

You can retrieve your api key by logging in and visiting the [API Token](https://packagecloud.io/api_token) page under Account Settings.

This is the list of [supported distributions](https://packagecloud.io/docs#os_distro_version) for the 'dist' option.

It is recommended to encrypt your auth token. Assuming you have the Travis CI command line client installed, you can do it like this:

    $ travis encrypt THE-API-TOKEN --add deploy.token

You can also have the `travis` tool set up everything for you:

    $ travis setup packagecloud

Keep in mind that the above command has to run in your project directory, so it can modify the `.travis.yml` for you.

### Branch to release from

You can explicitly specify the branch to release from with the **on** option:

    deploy:
      provider: packagecloud
      username: ...
      token: ...
      on:
        branch: production

Alternatively, you can also configure Travis CI to release from all branches:

    deploy:
      provider: packagecloud
      username: ...
      token: ...
      on:
        all_branches: true

By default, Travis CI will only release from the **master** branch.

Builds triggered from Pull Requests will never trigger a release.

### Releasing build artifacts

After your tests ran and before the release, Travis CI will clean up any additional files and changes you made.

Maybe that is not what you want, as you might generate some artifacts that are supposed to be released, too. There is now an option to skip the clean up:

    deploy:
      provider: packagecloud
      username: ...
      token: ...
      skip_cleanup: true

### Specify package folder

By default, the packagecloud provider will scan the current directory and push all supported packages.
You can specify which directory to scan from with the `local-dir` option. This example scans from `build` directory of your project.

    deploy:
      provider: packagecloud
      username: ...
      token: ...
      local-dir: build


### A note about Debian source packages

If the packagecloud provider finds any ```.dsc``` files, it will scan it and try to locate it's contents within
the ```local-dir``` directory. Ensure the source package and it's contents are output to the same directory for it to work.

### Conditional releases

You can deploy only when certain conditions are met.
See [Conditional Releases with `on:`](/user/deployment#Conditional-Releases-with-on%3A).

### Running commands before and after release

Sometimes you want to run commands before or after releasing a package. You can use the `before_deploy` and `after_deploy` stages for this. These will only be triggered if Travis CI is actually pushing a release.

    before_deploy: "echo 'ready?'"
    deploy:
      ..
    after_deploy:
      - ./after_deploy_1.sh
      - ./after_deploy_2.sh
