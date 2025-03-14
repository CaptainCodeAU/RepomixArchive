This file is a merged representation of the entire codebase, combined into a single document by Repomix.

# File Summary

## Purpose
This file contains a packed representation of the entire repository's contents.
It is designed to be easily consumable by AI systems for analysis, code review,
or other automated processes.

## File Format
The content is organized as follows:
1. This summary section
2. Repository information
3. Directory structure
4. Multiple file entries, each consisting of:
  a. A header with the file path (## File: path/to/file)
  b. The full contents of the file in a code block

## Usage Guidelines
- This file should be treated as read-only. Any changes should be made to the
  original repository files, not this packed version.
- When processing this file, use the file path to distinguish
  between different files in the repository.
- Be aware that this file may contain sensitive information. Handle it with
  the same level of security as you would the original repository.

## Notes
- Some files may have been excluded based on .gitignore rules and Repomix's configuration
- Binary files are not included in this packed representation. Please refer to the Repository Structure section for a complete list of file paths, including binary files
- Files matching patterns in .gitignore are excluded
- Files matching default ignore patterns are excluded

## Additional Info

# Directory Structure
```
.github/
  workflows/
    ci.yml
    release-stable.yml
    stale.yml
    unstable.yml
  stale.yml
base/
  rootfs/
    etc/
      apt/
        apt.conf.d/
          99local
      cont-init.d/
        01-set-timezone
    usr/
      bin/
        apt-cleanup
        apt-dpkg-wrap
  Dockerfile
base-java/
  Dockerfile
examples/
  README.md
jibri/
  rootfs/
    defaults/
      autoscaler-sidecar.config
      jibri.conf
      logging.properties
      xmpp.conf
      xorg-video-dummy.conf
    etc/
      chromium/
        policies/
          managed/
            managed_policies.json
      cont-init.d/
        10-config
      fix-attrs.d/
        10-jibri
      opt/
        chrome/
          policies/
            managed/
              managed_policies.json
      pulse/
        default.pa
      services.d/
        10-xorg/
          run
        20-icewm/
          run
        30-pulse/
          run
        40-jibri/
          finish
          run
        50-autoscaler-sidecar/
          run
    home/
      jibri/
        .config/
          pulse/
            client.conf
            daemon.conf
            default.pa
    opt/
      jitsi/
        shutdown.sh
    usr/
      bin/
        install-chrome.sh
  Dockerfile
jicofo/
  rootfs/
    defaults/
      jicofo.conf
      logging.properties
    etc/
      cont-init.d/
        10-config
      services.d/
        jicofo/
          run
    usr/
      local/
        bin/
          healthcheck.sh
  Dockerfile
jigasi/
  rootfs/
    defaults/
      autoscaler-sidecar.config
      logging.properties
      sip-communicator.properties
      sipserver-sip-communicator.properties
      transcriber-sip-communicator.properties
      xmpp-sip-communicator.properties
    etc/
      cont-init.d/
        10-config
      services.d/
        50-autoscaler-sidecar/
          run
        jigasi/
          finish
          run
    opt/
      jitsi/
        shutdown.sh
    usr/
      local/
        bin/
          healthcheck.sh
  Dockerfile
jvb/
  rootfs/
    defaults/
      autoscaler-sidecar.config
      jvb.conf
      logging.properties
    etc/
      cont-init.d/
        10-config
      services.d/
        50-autoscaler-sidecar/
          run
        jvb/
          finish
          run
    opt/
      jitsi/
        shutdown.sh
    usr/
      local/
        bin/
          healthcheck.sh
  Dockerfile
log-analyser/
  grafana-provisioning/
    dashboards/
      dashboards.yml
      docker-statistics.json
      jicofo.json
      jitsi-all.json
      jitsi-web.json
      jvb.json
      Prosody-Dashboard.json
      prosody.json
    datasources/
      datasource_loki.yml
  loki/
    conf/
      loki-config.yaml
  otel-collector-config.yaml
  README.md
prometheus/
  prometheus.yml
  README.md
prosody/
  rootfs/
    defaults/
      conf.d/
        brewery.cfg.lua
        jitsi-meet.cfg.lua
        visitors.cfg.lua
      rules.d/
        jvb_muc_presence_filter.pfw
      prosody.cfg.lua
      saslauthd.conf
    etc/
      cont-init.d/
        10-config
      sasl/
        xmpp.conf
      services.d/
        10-saslauthd/
          run
        prosody/
          run
    usr/
      local/
        bin/
          healthcheck.sh
  Dockerfile
resources/
  docker-jitsi-meet.xml
web/
  rootfs/
    defaults/
      default
      ffdhe2048.txt
      meet.conf
      nginx.conf
      settings-config.js
      ssl.conf
      system-config.js
    etc/
      cont-init.d/
        10-config
      services.d/
        cron/
          run
        jaas-account/
          run
        nginx/
          run
  Dockerfile
.gitignore
CHANGELOG.md
docker-compose.yml
env.example
etherpad.yml
gen-passwords.sh
grafana.yml
jibri.yml
jigasi.yml
LICENSE
log-analyser.yml
Makefile
prometheus.yml
README.md
release.sh
transcriber.yml
whiteboard.yml
```

# Files

## File: .github/workflows/ci.yml
````yaml
name: CI Test Build

on:
  pull_request:
  push:
    branches:
    - master

jobs:
  lint:
    name: Lint
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-node@v4
    - name: Check Node / npm versions
      run: |
        node -v
        npm -v
    - name: setup tpl
      run: |
        dpkgArch="$(dpkg --print-architecture)"
        case "${dpkgArch##*-}" in \
          "amd64") TPL_ARCH=amd64; S6_ARCH=amd64 ;; \
          "arm64") TPL_ARCH=arm64; S6_ARCH=aarch64 ;; \
          *) echo "unsupported architecture"; exit 1 ;; \
        esac
        wget -qO /tmp/tpl https://github.com/jitsi/tpl/releases/latest/download/tpl-linux-${TPL_ARCH}
        chmod +x /tmp/tpl
    - name: setup duktape
      working-directory: /tmp
      run: |
        wget https://duktape.org/duktape-2.2.1.tar.xz
        tar xvf duktape-2.2.1.tar.xz
        cd duktape-2.2.1
        make -f Makefile.cmdline
        mv duk /usr/local/bin
    - name: Run tpl with default values for web
      env:
        P2P_STUN_SERVERS: stun.l.google.com:19302,stun1.l.google.com:19302,stun2.l.google.com:19302
      run:  |
        /tmp/tpl web/rootfs/defaults/system-config.js > /tmp/config.js
        /tmp/tpl web/rootfs/defaults/settings-config.js >> /tmp/config.js
        echo "console.log(JSON.stringify(config, undefined, 2));" >> /tmp/config.js
        cat /tmp/config.js
    - name: Check config.js syntax
      run: duk /tmp/config.js
    - name: Run tpl with default values for jvb
      run:  |
        /tmp/tpl jvb/rootfs/defaults/jvb.conf > /tmp/jvb.conf
        /tmp/tpl jvb/rootfs/defaults/logging.properties > /tmp/logging.properties
        /tmp/tpl jvb/rootfs/defaults/autoscaler-sidecar.config > /tmp/autoscaler-sidecar.config
    - name: Run tpl with default values for jicofo
      run:  |
        /tmp/tpl jicofo/rootfs/defaults/jicofo.conf > /tmp/jicofo.conf
        /tmp/tpl jicofo/rootfs/defaults/logging.properties > /tmp/logging.properties
    - name: Run tpl with default values for jibri
      run:  |
        /tmp/tpl jibri/rootfs/defaults/jibri.conf > /tmp/jibri.conf
        /tmp/tpl jibri/rootfs/defaults/logging.properties > /tmp/logging.properties
        /tmp/tpl jibri/rootfs/defaults/xmpp.conf > /tmp/xmpp.conf
        /tmp/tpl jibri/rootfs/defaults/xorg-video-dummy.conf > /tmp/xorg-video-dummy.conf
        /tmp/tpl jibri/rootfs/defaults/autoscaler-sidecar.config > /tmp/autoscaler-sidecar.config
    - name: Run tpl with default values for jigasi
      run:  |
        /tmp/tpl jigasi/rootfs/defaults/sip-communicator.properties > /tmp/sip-communicator.properties
        /tmp/tpl jigasi/rootfs/defaults/sipserver-sip-communicator.properties > /tmp/xmpp-sip-communicator.properties
        /tmp/tpl jigasi/rootfs/defaults/transcriber-sip-communicator.properties > /tmp/xmpp-sip-communicator.properties
        /tmp/tpl jigasi/rootfs/defaults/xmpp-sip-communicator.properties > /tmp/xmpp-sip-communicator.properties
        /tmp/tpl jigasi/rootfs/defaults/logging.properties > /tmp/logging.properties
        /tmp/tpl jigasi/rootfs/defaults/autoscaler-sidecar.config > /tmp/autoscaler-sidecar.config
    - name: Run tpl with default values for prosody
      run:  |
        /tmp/tpl prosody/rootfs/defaults/prosody.cfg.lua > /tmp/prosody.cfg.lua
        /tmp/tpl prosody/rootfs/defaults/saslauthd.conf > /tmp/saslauthd.conf
        /tmp/tpl prosody/rootfs/defaults/conf.d/jitsi-meet.cfg.lua > /tmp/jitsi-meet.cfg.lua
        /tmp/tpl prosody/rootfs/defaults/conf.d/brewery.cfg.lua > /tmp/brewery.cfg.lua
        /tmp/tpl prosody/rootfs/defaults/conf.d/visitors.cfg.lua > /tmp/visitors.cfg.lua
        /tmp/tpl prosody/rootfs/defaults/rules.d/jvb_muc_presence_filter.pfw > /tmp/jvb_muc_presence_filter.pfw
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
        with:
          driver: docker

      - name: Build base
        uses: docker/build-push-action@v6
        with:
          context: ./base
          load: true
          tags: |
            jitsi/base:latest
          build-args: |
            JITSI_RELEASE=unstable

      - name: Build base-java
        uses: docker/build-push-action@v6
        with:
          context: ./base-java
          load: true
          tags: |
            jitsi/base-java:latest

      - name: Build jibri
        uses: docker/build-push-action@v6
        with:
          context: ./jibri
          load: true
          tags: |
            jitsi/jibri:latest

      - name: Build jicofo
        uses: docker/build-push-action@v6
        with:
          context: ./jicofo
          load: true
          tags: |
            jitsi/jicofo:latest

      - name: Build jigasi
        uses: docker/build-push-action@v6
        with:
          context: ./jigasi
          load: true
          tags: |
            jitsi/jigasi:latest

      - name: Build jvb
        uses: docker/build-push-action@v6
        with:
          context: ./jvb
          load: true
          tags: |
            jitsi/jvb:latest

      - name: Build prosody
        uses: docker/build-push-action@v6
        with:
          context: ./prosody
          load: true
          tags: |
            jitsi/prosody:latest

      - name: Build web
        uses: docker/build-push-action@v6
        with:
          context: ./web
          load: true
          tags: |
            jitsi/web:latest
````

## File: .github/workflows/release-stable.yml
````yaml
name: "Release Stable"

on:
  workflow_dispatch:
    inputs:
      version:
        description: Version number
        required: true
        type: string
jobs:
  gh-release:
    runs-on: ubuntu-latest
    needs: [base, base-java, jibri, jicofo, jigasi, jvb, prosody, web]
    permissions:
      # Give the default GITHUB_TOKEN write permission to commit and push the changed files back to the repository.
      contents: write
    steps:
      - uses: actions/checkout@v4
      - run: |
          sed -i".bak" -e "s/unstable/stable-${{ github.event.inputs.version }}/" *.yml
      - uses: stefanzweifel/git-auto-commit-action@v5
        with:
            commit_message: "release: stable-${{ github.event.inputs.version }}"
      - name: release
        uses: softprops/action-gh-release@v2
        with:
            tag_name: stable-${{ github.event.inputs.version }}
            generate_release_notes: true
            make_latest: true
      - run: |
          sed -i".bak" -e "s/stable-${{ github.event.inputs.version }}/unstable/" *.yml
      - uses: stefanzweifel/git-auto-commit-action@v5
        with:
            commit_message: "misc: working on unstable"

  base:
    runs-on: ubuntu-latest
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./base
          tags: |
            ${{ secrets.JITSI_REPO }}/base:stable-${{ github.event.inputs.version }}
            ${{ secrets.JITSI_REPO }}/base:stable
          build-args: |
            JITSI_RELEASE=stable
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max

  base-java:
    runs-on: ubuntu-latest
    needs: base
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./base-java
          tags: |
            ${{ secrets.JITSI_REPO }}/base-java:stable-${{ github.event.inputs.version }}
            ${{ secrets.JITSI_REPO }}/base-java:stable
          build-args: |
            JITSI_REPO=${{ secrets.JITSI_REPO }}
            BASE_TAG=stable-${{ github.event.inputs.version }}
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max

  jibri:
    runs-on: ubuntu-latest
    needs: base-java
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./jibri
          tags: |
            ${{ secrets.JITSI_REPO }}/jibri:stable-${{ github.event.inputs.version }}
            ${{ secrets.JITSI_REPO }}/jibri:stable
          build-args: |
            JITSI_REPO=${{ secrets.JITSI_REPO }}
            BASE_TAG=stable-${{ github.event.inputs.version }}
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max

  jicofo:
    runs-on: ubuntu-latest
    needs: base-java
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./jicofo
          tags: |
            ${{ secrets.JITSI_REPO }}/jicofo:stable-${{ github.event.inputs.version }}
            ${{ secrets.JITSI_REPO }}/jicofo:stable
          build-args: |
            JITSI_REPO=${{ secrets.JITSI_REPO }}
            BASE_TAG=stable-${{ github.event.inputs.version }}
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max

  jigasi:
    runs-on: ubuntu-latest
    needs: base-java
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./jigasi
          tags: |
            ${{ secrets.JITSI_REPO }}/jigasi:stable-${{ github.event.inputs.version }}
            ${{ secrets.JITSI_REPO }}/jigasi:stable
          build-args: |
            JITSI_REPO=${{ secrets.JITSI_REPO }}
            BASE_TAG=stable-${{ github.event.inputs.version }}
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max

  jvb:
    runs-on: ubuntu-latest
    needs: base-java
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./jvb
          tags: |
            ${{ secrets.JITSI_REPO }}/jvb:stable-${{ github.event.inputs.version }}
            ${{ secrets.JITSI_REPO }}/jvb:stable
          build-args: |
            JITSI_REPO=${{ secrets.JITSI_REPO }}
            BASE_TAG=stable-${{ github.event.inputs.version }}
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max

  prosody:
    runs-on: ubuntu-latest
    needs: base
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./prosody
          tags: |
            ${{ secrets.JITSI_REPO }}/prosody:stable-${{ github.event.inputs.version }}
            ${{ secrets.JITSI_REPO }}/prosody:stable
          build-args: |
            JITSI_REPO=${{ secrets.JITSI_REPO }}
            BASE_TAG=stable-${{ github.event.inputs.version }}
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max

  web:
    runs-on: ubuntu-latest
    needs: base
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./web
          tags: |
            ${{ secrets.JITSI_REPO }}/web:stable-${{ github.event.inputs.version }}
            ${{ secrets.JITSI_REPO }}/web:stable
          build-args: |
            JITSI_REPO=${{ secrets.JITSI_REPO }}
            BASE_TAG=stable-${{ github.event.inputs.version }}
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max
````

## File: .github/workflows/stale.yml
````yaml
name: 'Close stale issues and PRs'
on:
  schedule:
    - cron: '30 1 * * *'

jobs:
  stale:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/stale@v8
        with:
          stale-issue-message: 'This issue has been automatically marked as stale because it has not had recent activity. It will be closed if no further activity occurs. Thank you for your contributions.'
          stale-pr-message: 'This PR has been automatically marked as stale because it has not had recent activity. It will be closed if no further activity occurs. Thank you for your contributions.'
          stale-issue-label: 'stale'
          stale-pr-label: 'stale'
          exempt-issue-labels: 'confirmed'
          exempt-pr-labels: 'confirmed'
          days-before-issue-stale: 60
          days-before-pr-stale: 90
          days-before-issue-close: 10
          days-before-pr-close: 10
````

## File: .github/workflows/unstable.yml
````yaml
name: Unstable Build

on:
  schedule:
    - cron: "0 5 * * *"
  workflow_dispatch:

jobs:
  version:
    runs-on: ubuntu-latest
    outputs:
      base: unstable
      date: unstable-${{ steps.date.outputs.date }}
      prosody_version: prosody-${{ steps.prosody_version.outputs.version }}
      jicofo_version: jicofo-${{ steps.jicofo_version.outputs.version }}
      web_version: web-${{ steps.web_version.outputs.version }}
      jvb_version: jvb-${{ steps.jvb_version.outputs.version }}
      jibri_version: jibri-${{ steps.jibri_version.outputs.version }}
      jigasi_version: jigasi-${{ steps.jigasi_version.outputs.version }}
    steps:
      - name: Get current date
        id: date
        run: echo "date=$(date +%F)">> $GITHUB_OUTPUT
      - name: Prosody gpg key
        id: prosody_gpg_key
        run: curl --location --silent --show-error https://prosody.im/files/prosody-debian-packages.key | sudo dd of=/etc/apt/trusted.gpg.d/prosody.gpg
      - name: Prosody repo
        uses: myci-actions/add-deb-repo@11
        with:
          repo: deb https://packages.prosody.im/debian bookworm main
          repo-name: prosody
          keys-asc: https://prosody.im/files/prosody-debian-packages.key
      - name: Jitsi repo
        uses: myci-actions/add-deb-repo@11
        with:
          repo: deb https://download.jitsi.org/ unstable/
          repo-name: jitsi
          keys-asc: https://download.jitsi.org/jitsi-key.gpg.key
      - name: Get current jicofo versions
        id: jicofo_version
        run: echo "version=$( apt-cache show jitsi-meet | head -10 | grep '^Depends:' | tr ',' '\n' | grep jicofo | cut -d'=' -f2 | tr -d ')' | awk '{print $1}' )" >> $GITHUB_OUTPUT
      - name: Get current jitsi-meet-web versions
        id: web_version
        run: echo "version=$( apt-cache show jitsi-meet | head -10 | grep '^Depends:' | tr ',' '\n' | grep 'jitsi-meet-web ' | cut -d'=' -f2 | tr -d ')' | awk '{print $1}' )" >> $GITHUB_OUTPUT
      - name: Get current jvb versions
        id: jvb_version
        run: echo "version=$( apt-cache show jitsi-meet | head -10 | grep '^Pre-Depends:'| tr ',' '\n' | grep 'jitsi-videobridge2' | cut -d'=' -f2 | tr -d ')' | awk '{print $1}' )" >> $GITHUB_OUTPUT
      - name: Get current prosody versions
        id: prosody_version
        run: echo "version=$( apt-cache madison prosody | awk '{print $3;}' | head -1 |  cut -d'-' -f1 )" >> $GITHUB_OUTPUT
      - name: Get current jibri version
        id: jibri_version
        run: echo "version=$( apt-cache show jibri | head -10 | grep Version | awk '{print $2}' )" >> $GITHUB_OUTPUT
      - name: Get current jigasi version
        id: jigasi_version
        run: echo "version=$( apt-cache show jigasi | head -10 | grep Version | awk '{print $2}' )" >> $GITHUB_OUTPUT

  base:
    runs-on: ubuntu-latest
    needs: version
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./base
          tags: |
            ${{ secrets.JITSI_REPO }}/base:${{ needs.version.outputs.base }}
            ${{ secrets.JITSI_REPO }}/base:${{ needs.version.outputs.date }}
          build-args: |
            JITSI_RELEASE=unstable
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max

  base-java:
    runs-on: ubuntu-latest
    needs: [version, base]
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./base-java
          tags: |
            ${{ secrets.JITSI_REPO }}/base-java:${{ needs.version.outputs.base }}
            ${{ secrets.JITSI_REPO }}/base-java:${{ needs.version.outputs.date }}
          build-args: |
            JITSI_REPO=${{ secrets.JITSI_REPO }}
            BASE_TAG=${{ needs.version.outputs.base }}
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max

  jibri:
    runs-on: ubuntu-latest
    needs: [version, base-java]
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./jibri
          tags: |
            ${{ secrets.JITSI_REPO }}/jibri:${{ needs.version.outputs.base }}
            ${{ secrets.JITSI_REPO }}/jibri:${{ needs.version.outputs.date }}
            ${{ secrets.JITSI_REPO }}/jibri:${{ needs.version.outputs.jibri_version }}
          build-args: |
            JITSI_REPO=${{ secrets.JITSI_REPO }}
            BASE_TAG=${{ needs.version.outputs.base }}
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max

  jicofo:
    runs-on: ubuntu-latest
    needs: [version, base-java]
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./jicofo
          tags: |
            ${{ secrets.JITSI_REPO }}/jicofo:${{ needs.version.outputs.base }}
            ${{ secrets.JITSI_REPO }}/jicofo:${{ needs.version.outputs.date }}
            ${{ secrets.JITSI_REPO }}/jicofo:${{ needs.version.outputs.jicofo_version }}
          build-args: |
            JITSI_REPO=${{ secrets.JITSI_REPO }}
            BASE_TAG=${{ needs.version.outputs.base }}
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max

  jigasi:
    runs-on: ubuntu-latest
    needs: [version, base-java]
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./jigasi
          tags: |
            ${{ secrets.JITSI_REPO }}/jigasi:${{ needs.version.outputs.base }}
            ${{ secrets.JITSI_REPO }}/jigasi:${{ needs.version.outputs.date }}
            ${{ secrets.JITSI_REPO }}/jigasi:${{ needs.version.outputs.jigasi_version }}
          build-args: |
            JITSI_REPO=${{ secrets.JITSI_REPO }}
            BASE_TAG=${{ needs.version.outputs.base }}
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max

  jvb:
    runs-on: ubuntu-latest
    needs: [version, base-java]
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./jvb
          tags: |
            ${{ secrets.JITSI_REPO }}/jvb:${{ needs.version.outputs.base }}
            ${{ secrets.JITSI_REPO }}/jvb:${{ needs.version.outputs.date }}
            ${{ secrets.JITSI_REPO }}/jvb:${{ needs.version.outputs.jvb_version }}
          build-args: |
            JITSI_REPO=${{ secrets.JITSI_REPO }}
            BASE_TAG=${{ needs.version.outputs.base }}
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max

  prosody:
    runs-on: ubuntu-latest
    needs: [version, base]
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./prosody
          tags: |
            ${{ secrets.JITSI_REPO }}/prosody:${{ needs.version.outputs.base }}
            ${{ secrets.JITSI_REPO }}/prosody:${{ needs.version.outputs.date }}
            ${{ secrets.JITSI_REPO }}/prosody:${{ needs.version.outputs.prosody_version }}
          build-args: |
            JITSI_REPO=${{ secrets.JITSI_REPO }}
            BASE_TAG=${{ needs.version.outputs.base }}
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max

  web:
    runs-on: ubuntu-latest
    needs: [version, base]
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
        with:
          platforms: linux/amd64,linux/arm64
      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          push: true
          context: ./web
          tags: |
            ${{ secrets.JITSI_REPO }}/web:${{ needs.version.outputs.base }}
            ${{ secrets.JITSI_REPO }}/web:${{ needs.version.outputs.date }}
            ${{ secrets.JITSI_REPO }}/web:${{ needs.version.outputs.web_version }}
          build-args: |
            JITSI_REPO=${{ secrets.JITSI_REPO }}
            BASE_TAG=${{ needs.version.outputs.base }}
          platforms: linux/amd64,linux/arm64
          cache-from: type=gha
          cache-to: type=gha,mode=max
````

## File: .github/stale.yml
````yaml
# Number of days of inactivity before an issue becomes stale
daysUntilStale: 60
# Number of days of inactivity before a stale issue is closed
daysUntilClose: 7
# Issues with these labels will never be considered stale
exemptLabels:
  - confirmed
staleLabel: wontfix
# Comment to post when marking an issue as stale. Set to `false` to disable
markComment: >
  This issue has been automatically marked as stale because it has not had
  recent activity. It will be closed if no further activity occurs. Thank you
  for your contributions.
# Comment to post when closing a stale issue. Set to `false` to disable
closeComment: false
````

## File: base/rootfs/etc/apt/apt.conf.d/99local
````
APT::Install-Recommends "false";
APT::Install-Suggests "false";
````

## File: base/rootfs/etc/cont-init.d/01-set-timezone
````
#!/usr/bin/with-contenv bash

if [[ ! -z "$TZ" ]]; then
    if [[ -f /usr/share/zoneinfo/$TZ ]]; then
         ln -sf /usr/share/zoneinfo/$TZ /etc/localtime
         echo "$TZ" >  /etc/timezone
    else
        echo "WARNING: $TZ is not a valid time zone."
    fi
fi
````

## File: base/rootfs/usr/bin/apt-cleanup
````
#!/bin/sh

rm -rf /var/lib/apt/lists/
````

## File: base/rootfs/usr/bin/apt-dpkg-wrap
````
#!/bin/sh

export LC_ALL=C
export DEBIAN_FRONTEND=noninteractive

bin=$1
shift
exec "$bin" "$@"
````

## File: base/Dockerfile
````
FROM docker.io/library/debian:bookworm-slim

ARG JITSI_RELEASE=stable
ENV S6_BEHAVIOUR_IF_STAGE2_FAILS=2

COPY rootfs /

RUN \
    dpkgArch="$(dpkg --print-architecture)" && \
    case "${dpkgArch##*-}" in \
        "amd64") TPL_ARCH=amd64; S6_ARCH=amd64 ;; \
        "arm64") TPL_ARCH=arm64; S6_ARCH=aarch64 ;; \
        *) echo "unsupported architecture"; exit 1 ;; \
    esac && \
    apt-dpkg-wrap apt-get update && \
    apt-dpkg-wrap apt-get install -y apt-transport-https apt-utils ca-certificates gnupg wget curl && \
    wget -qO /usr/bin/tpl https://github.com/jitsi/tpl/releases/download/v1.4.0/tpl-linux-${TPL_ARCH} && \
    # Workaround S6 bug when /bin is a symlink
    wget -qO /tmp/s6.tar.gz https://github.com/just-containers/s6-overlay/releases/download/v1.22.1.0/s6-overlay-${S6_ARCH}.tar.gz && \
    mkdir /tmp/s6 && \
    tar xfz /tmp/s6.tar.gz -C /tmp/s6 && \
    tar hxfz /tmp/s6.tar.gz -C / && \
    rm -f /usr/bin/execlineb && \
    cp /tmp/s6/bin/execlineb /usr/bin/ && \
    rm -rf /tmp/s6* && \
    wget -qO - https://download.jitsi.org/jitsi-key.gpg.key | gpg --dearmour > /etc/apt/trusted.gpg.d/jitsi.gpg && \
    echo "deb https://download.jitsi.org $JITSI_RELEASE/" > /etc/apt/sources.list.d/jitsi.list && \
    echo "deb http://ftp.debian.org/debian bookworm-backports main" > /etc/apt/sources.list.d/backports.list && \
    apt-dpkg-wrap apt-get update && \
    apt-dpkg-wrap apt-get dist-upgrade -y && \
    apt-cleanup && \
    chmod +x /usr/bin/tpl

RUN [ "$JITSI_RELEASE" = "unstable" ] && \
    apt-dpkg-wrap apt-get update && \
    apt-dpkg-wrap apt-get install -y jq procps curl vim iputils-ping net-tools && \
    apt-cleanup || \
    true

ENTRYPOINT [ "/init" ]
````

## File: base-java/Dockerfile
````
ARG JITSI_REPO=jitsi
ARG BASE_TAG=latest
FROM ${JITSI_REPO}/base:${BASE_TAG}

RUN mkdir -p /usr/share/man/man1 && \
    mkdir -p /etc/apt/keyrings/ && \
    apt-dpkg-wrap apt-get update && \
    apt-dpkg-wrap apt-get install -y unzip ca-certificates curl gnupg && \
    curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg && \
    echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_20.x nodistro main" | tee /etc/apt/sources.list.d/nodesource.list && \
    apt-dpkg-wrap apt-get update && \
    apt-dpkg-wrap apt-get install -y nodejs openjdk-17-jre-headless openjdk-17-jdk-headless && \
    apt-cleanup
````

## File: examples/README.md
````markdown
# Community Examples

This folder used to contain community maintained example configurations for
Kubernetes and Traefik. They have now been migrated to the [jitsi-contrib](https://github.com/jitsi-contrib)
organization.
````

## File: jibri/rootfs/defaults/autoscaler-sidecar.config
````
{{ $JIBRI_HTTP_API_EXTERNAL_PORT := .Env.JIBRI_HTTP_API_EXTERNAL_PORT | default "2222" -}}
{{ $SHUTDOWN_POLLING_INTERVAL := .Env.AUTOSCALER_SIDECAR_SHUTDOWN_POLLING_INTERVAL | default "60" -}}
{{ $STATS_POLLING_INTERVAL := .Env.AUTOSCALER_SIDECAR_STATS_POLLING_INTERVAL | default "30" -}}
export SHUTDOWN_POLLING_INTERVAL={{ $SHUTDOWN_POLLING_INTERVAL }}
export STATS_POLLING_INTERVAL={{ $STATS_POLLING_INTERVAL }}
export PORT={{ .Env.AUTOSCALER_SIDECAR_PORT }}
export GRACEFUL_SHUTDOWN_SCRIPT="/opt/jitsi/jibri/wait_graceful_shutdown.sh"
export TERMINATE_SCRIPT="/opt/jitsi/shutdown.sh"
export ENABLE_REPORT_STATS=true
export POLLING_URL="{{ .Env.AUTOSCALER_URL  }}/sidecar/poll"
export STATUS_URL="{{ .Env.AUTOSCALER_URL }}/sidecar/status"
export SHUTDOWN_URL="{{ .Env.AUTOSCALER_URL }}/sidecar/shutdown"
export STATS_RETRIEVE_URL="http://localhost:{{ $JIBRI_HTTP_API_EXTERNAL_PORT }}/jibri/api/v1.0/health"
export STATS_REPORT_URL="{{ .Env.AUTOSCALER_URL }}/sidecar/stats"
export ASAP_SIGNING_KEY_FILE="{{ .Env.AUTOSCALER_SIDECAR_KEY_FILE }}"
export ASAP_JWT_KID="{{ .Env.AUTOSCALER_SIDECAR_KEY_ID }}"
export INSTANCE_TYPE="jibri"
export INSTANCE_ID="{{ .Env.AUTOSCALER_SIDECAR_INSTANCE_ID }}"
export INSTANCE_METADATA='{"environment":"{{ .Env.XMPP_ENV_NAME }}","region":"{{ .Env.AUTOSCALER_SIDECAR_REGION }}","group":"{{ .Env.AUTOSCALER_SIDECAR_GROUP_NAME }}","name":"{{ .Env.JIBRI_INSTANCE_ID }}","version":"{{ .Env.JIBRI_VERSION }}","privateIp":"{{ .Env.LOCAL_ADDRESS }}","hostId":"{{ .Env.AUTOSCALER_SIDECAR_HOST_ID }}"}'
````

## File: jibri/rootfs/defaults/jibri.conf
````
{{ $IGNORE_CERTIFICATE_ERRORS := .Env.IGNORE_CERTIFICATE_ERRORS | default "false" | toBool -}}
{{ $ENABLE_PROMETHEUS := .Env.JIBRI_ENABLE_PROMETHEUS | default "false" | toBool -}}
{{ $JIBRI_RECORDING_RESOLUTION := .Env.JIBRI_RECORDING_RESOLUTION | default "1280x720" -}}
{{ $JIBRI_RECORDING_VIDEO_ENCODE_PRESET_RECORDING := .Env.JIBRI_RECORDING_VIDEO_ENCODE_PRESET_RECORDING | default "ultrafast" -}}
{{ $JIBRI_RECORDING_VIDEO_ENCODE_PRESET_STREAMING := .Env.JIBRI_RECORDING_VIDEO_ENCODE_PRESET_STREAMING | default "veryfast" -}}
{{ $JIBRI_RECORDING_CONSTANT_RATE_FACTOR := .Env.JIBRI_RECORDING_CONSTANT_RATE_FACTOR | default 25 -}}
{{ $JIBRI_RECORDING_FRAMERATE := .Env.JIBRI_RECORDING_FRAMERATE | default 30 -}}
{{ $JIBRI_RECORDING_QUEUE_SIZE := .Env.JIBRI_RECORDING_QUEUE_SIZE | default 4096 -}}
{{ $JIBRI_RECORDING_STREAMING_MAX_BITRATE := .Env.JIBRI_RECORDING_STREAMING_MAX_BITRATE | default "2976k" -}}
{{ $JIBRI_SINGLE_USE_MODE := .Env.JIBRI_SINGLE_USE_MODE | default "false" -}}
{{ $STATSD_HOST := .Env.JIBRI_STATSD_HOST | default "localhost" -}}
{{ $STATSD_PORT := .Env.JIBRI_STATSD_PORT | default "8125" -}}

jibri {
    // A unique identifier for this Jibri
    id = "{{ .Env.JIBRI_INSTANCE_ID }}"
    // Whether or not Jibri should return to idle state after handling
    // (successfully or unsuccessfully) a request.  A value of 'true'
    // here means that a Jibri will NOT return back to the IDLE state
    // and will need to be restarted in order to be used again.
    single-use-mode = {{ $JIBRI_SINGLE_USE_MODE }}

    api {
      {{ if or .Env.JIBRI_HTTP_API_EXTERNAL_PORT .Env.JIBRI_HTTP_API_INTERNAL_PORT -}}
      http {
        {{ if .Env.JIBRI_HTTP_API_EXTERNAL_PORT -}}
        external-api-port = {{ .Env.JIBRI_HTTP_API_EXTERNAL_PORT }}
        {{ end -}}
        {{ if .Env.JIBRI_HTTP_API_INTERNAL_PORT -}}
        internal-api-port = {{ .Env.JIBRI_HTTP_API_INTERNAL_PORT }}
        {{ end -}}
      }
      {{ end -}}
    }
    recording {
      recordings-directory = "{{ .Env.JIBRI_RECORDING_DIR | default "/config/recordings" }}"
      {{ if .Env.JIBRI_FINALIZE_RECORDING_SCRIPT_PATH -}}
      finalize-script = "{{ .Env.JIBRI_FINALIZE_RECORDING_SCRIPT_PATH }}"
      {{ end -}}
    }
{{ if .Env.JIBRI_WEBHOOK_SUBSCRIBERS -}}
    webhook {
      subscribers = [{{ range $index, $element := (splitList "," .Env.JIBRI_WEBHOOK_SUBSCRIBERS | compact) }}{{ if gt $index 0}},{{ end }}"{{ $element }}"{{ end }}]
    }{{ end }}
    ffmpeg {
      resolution =  "{{ $JIBRI_RECORDING_RESOLUTION }}"
      // The audio source that will be used to capture audio on Linux
      audio-source = "pulse"
      // The audio device that will be used to capture audio on Linux
      audio-device =  "default"
      framerate = {{ $JIBRI_RECORDING_FRAMERATE }}
      queue-size = {{ $JIBRI_RECORDING_QUEUE_SIZE }}
      streaming-max-bitrate = "{{ $JIBRI_RECORDING_STREAMING_MAX_BITRATE }}"
      // Available presets: ultrafast, superfast, veryfast, faster, fast, medium,
      // slow, slower, veryslow, placebo
      video-encode-preset-streaming = "{{ $JIBRI_RECORDING_VIDEO_ENCODE_PRESET_STREAMING }}"
      video-encode-preset-recording = "{{ $JIBRI_RECORDING_VIDEO_ENCODE_PRESET_RECORDING }}"

      // The range of the CRF scale is 0-51, where 0 is lossless,
      // 23 is the default, and 51 is worst quality possible.
      h264-constant-rate-factor = {{ $JIBRI_RECORDING_CONSTANT_RATE_FACTOR }}
    }

    {{ if .Env.CHROMIUM_FLAGS -}}
    chrome {
      // The flags which will be passed to chromium when launching
      flags = [
        "{{ join "\",\"" (splitList "," .Env.CHROMIUM_FLAGS | compact) }}"
      ]
    }
    {{ else if $IGNORE_CERTIFICATE_ERRORS -}}
    chrome {
      flags = [
        "--use-fake-ui-for-media-stream",
        "--start-maximized",
        "--kiosk",
        "--enabled",
        "--autoplay-policy=no-user-gesture-required",
        "--ignore-certificate-errors"
      ]
    }
    {{ end -}}

    stats {
    {{- if .Env.ENABLE_STATS_D }}
      enable-stats-d = {{ .Env.ENABLE_STATS_D }}
      host = "{{ $STATSD_HOST }}"
      port = {{ $STATSD_PORT }}
    {{- end }}
      prometheus.enabled = {{ $ENABLE_PROMETHEUS }}
    }
}

include "xmpp.conf"

include "custom-jibri.conf"
````

## File: jibri/rootfs/defaults/logging.properties
````
handlers = java.util.logging.FileHandler, java.util.logging.ConsoleHandler

org.jitsi.utils.logging2.JitsiLogFormatter.programname=Jibri

java.util.logging.FileHandler.level = FINE
java.util.logging.FileHandler.pattern   = /config/logs/log.%g.txt
java.util.logging.FileHandler.formatter = org.jitsi.utils.logging2.JitsiLogFormatter
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.limit = 10000000

org.jitsi.jibri.capture.ffmpeg.util.FfmpegFileHandler.level = FINE
org.jitsi.jibri.capture.ffmpeg.util.FfmpegFileHandler.pattern   = /config/logs/ffmpeg.%g.txt
org.jitsi.jibri.capture.ffmpeg.util.FfmpegFileHandler.formatter = org.jitsi.utils.logging2.JitsiLogFormatter
org.jitsi.jibri.capture.ffmpeg.util.FfmpegFileHandler.count = 10
org.jitsi.jibri.capture.ffmpeg.util.FfmpegFileHandler.limit = 10000000

org.jitsi.jibri.sipgateway.pjsua.util.PjsuaFileHandler.level = FINE
org.jitsi.jibri.sipgateway.pjsua.util.PjsuaFileHandler.pattern   = /config/logs/pjsua.%g.txt
org.jitsi.jibri.sipgateway.pjsua.util.PjsuaFileHandler.formatter = org.jitsi.utils.logging2.JitsiLogFormatter
org.jitsi.jibri.sipgateway.pjsua.util.PjsuaFileHandler.count = 10
org.jitsi.jibri.sipgateway.pjsua.util.PjsuaFileHandler.limit = 10000000

org.jitsi.jibri.selenium.util.BrowserFileHandler.level = FINE
org.jitsi.jibri.selenium.util.BrowserFileHandler.pattern   = /config/logs/browser.%g.txt
org.jitsi.jibri.selenium.util.BrowserFileHandler.formatter = org.jitsi.utils.logging2.JitsiLogFormatter
org.jitsi.jibri.selenium.util.BrowserFileHandler.count = 10
org.jitsi.jibri.selenium.util.BrowserFileHandler.limit = 10000000

java.util.logging.ConsoleHandler.level = FINE
java.util.logging.ConsoleHandler.formatter = org.jitsi.utils.logging2.JitsiLogFormatter

org.jitsi.level = FINE
org.glassfish.level = INFO
````

## File: jibri/rootfs/defaults/xmpp.conf
````
{{ $JIBRI_BREWERY_MUC := .Env.JIBRI_BREWERY_MUC | default "jibribrewery" -}}
{{ $XMPP_MUC_DOMAIN := .Env.XMPP_MUC_DOMAIN | default "muc.meet.jitsi" -}}
{{ $XMPP_MUC_DOMAIN_PREFIX := (split "." $XMPP_MUC_DOMAIN)._0  -}}
{{ $JIBRI_STRIP_DOMAIN_JID := .Env.JIBRI_STRIP_DOMAIN_JID | default $XMPP_MUC_DOMAIN_PREFIX -}}
{{ $JIBRI_RECORDER_USER := .Env.JIBRI_RECORDER_USER | default "recorder" -}}
{{ $JIBRI_USAGE_TIMEOUT := .Env.JIBRI_USAGE_TIMEOUT | default "0" -}}
{{ $JIBRI_XMPP_USER := .Env.JIBRI_XMPP_USER | default "jibri" -}}
{{ $XMPP_AUTH_DOMAIN := .Env.XMPP_AUTH_DOMAIN | default "auth.meet.jitsi" -}}
{{ $XMPP_DOMAIN := .Env.XMPP_DOMAIN | default "meet.jitsi" -}}
{{ $XMPP_INTERNAL_MUC_DOMAIN := .Env.XMPP_INTERNAL_MUC_DOMAIN | default "internal-muc.meet.jitsi" -}}
{{ $XMPP_HIDDEN_DOMAIN := .Env.XMPP_HIDDEN_DOMAIN | default "hidden.meet.jitsi" -}}
{{ $XMPP_TRUST_ALL_CERTS := .Env.XMPP_TRUST_ALL_CERTS | default "true" | toBool -}}
{{ $XMPP_PORT := .Env.XMPP_PORT | default "5222" -}}
{{ $XMPP_SERVER := .Env.XMPP_SERVER | default "xmpp.meet.jitsi" -}}
{{ $XMPP_SERVERS := splitList "," $XMPP_SERVER | compact -}}
{{/* assign env from context, preserve during range when . is re-assigned */}}
{{ $ENV := .Env -}}

jibri.api.xmpp.environments = [
    // See example_xmpp_envs.conf for an example of what is expected here
{{ range $index, $element := $XMPP_SERVERS -}}
{{ $SERVER := splitn ":" 2 $element }}
            {
                // A user-friendly name for this environment
                name = "{{ $ENV.XMPP_ENV_NAME }}-{{$index}}"

                // A list of XMPP server hosts to which we'll connect
                xmpp-server-hosts = [
                    "{{ $SERVER._0 }}"
                ]

                // The base XMPP domain
                xmpp-domain = "{{ $XMPP_DOMAIN }}"

                {{ if $ENV.PUBLIC_URL -}}
                // An (optional) base url the Jibri will join if it is set
                base-url = "{{ $ENV.PUBLIC_URL }}"
                {{ end -}}

                // The MUC we'll join to announce our presence for
                // recording and streaming services
                control-muc {
                    domain = "{{ $XMPP_INTERNAL_MUC_DOMAIN }}"
                    room-name = "{{ $JIBRI_BREWERY_MUC }}"
                    nickname = "{{ $ENV.JIBRI_INSTANCE_ID }}"
                }

                // The login information for the control MUC
                control-login {
                    domain = "{{ $XMPP_AUTH_DOMAIN }}"
                    port = "{{ $SERVER._1 | default $XMPP_PORT }}"
                    username = "{{ $JIBRI_XMPP_USER }}"
                    password = "{{ $ENV.JIBRI_XMPP_PASSWORD }}"
                }

                // The login information the selenium web client will use
                call-login {
                    domain = "{{ $XMPP_HIDDEN_DOMAIN }}"
                    username = "{{ $JIBRI_RECORDER_USER }}"
                    password = "{{ $ENV.JIBRI_RECORDER_PASSWORD }}"
                }

                // The value we'll strip from the room JID domain to derive
                // the call URL
                strip-from-room-domain = "{{ $JIBRI_STRIP_DOMAIN_JID }}."

                // How long Jibri sessions will be allowed to last before
                // they are stopped.  A value of 0 allows them to go on
                // indefinitely
                usage-timeout = "{{ $JIBRI_USAGE_TIMEOUT }}"

                // Whether or not we'll automatically trust any cert on
                // this XMPP domain
                trust-all-xmpp-certs = {{ $XMPP_TRUST_ALL_CERTS }}
            }
{{ end }}
]
````

## File: jibri/rootfs/defaults/xorg-video-dummy.conf
````
{{ $JIBRI_RECORDING_RESOLUTION := .Env.JIBRI_RECORDING_RESOLUTION | default "1280x720" -}}
{{ $JIBRI_HORIZ_SYNC := .Env.JIBRI_XORG_HORIZ_SYNC | default "5.0 - 1000.0" -}}
{{ $JIBRI_VERT_REFRESH := .Env.JIBRI_XORG_VERT_REFRESH | default "5.0 - 200.0" -}}

# This xorg configuration file is meant to be used by xpra
# to start a dummy X11 server.
# For details, please see:
# https://xpra.org/Xdummy.html

Section "ServerFlags"
  Option "DontVTSwitch" "true"
  Option "AllowMouseOpenFail" "true"
  Option "PciForceNone" "true"
  Option "AutoEnableDevices" "false"
  Option "AutoAddDevices" "false"
EndSection

Section "Device"
  Identifier "dummy_videocard"
  Driver "dummy"
  Option "ConstantDPI" "true"
  #VideoRam 4096000
  #VideoRam 256000
  VideoRam 192000
EndSection

Section "Monitor"
  Identifier "dummy_monitor"
  HorizSync   {{ $JIBRI_HORIZ_SYNC }}
  VertRefresh {{ $JIBRI_VERT_REFRESH }}
  #This can be used to get a specific DPI, but only for the default resolution:
  #DisplaySize 508 317
  #NOTE: the highest modes will not work without increasing the VideoRam
  # for the dummy video card.
  Modeline "32768x32768" 15226.50 32768 35800 39488 46208 32768 32771 32781 32953
  Modeline "32768x16384" 7516.25 32768 35544 39192 45616 16384 16387 16397 16478
  Modeline "16384x8192" 2101.93 16384 16416 24400 24432 8192 8390 8403 8602
  Modeline "8192x4096" 424.46 8192 8224 9832 9864 4096 4195 4202 4301
  Modeline "5496x1200" 199.13 5496 5528 6280 6312 1200 1228 1233 1261
  Modeline "5280x1080" 169.96 5280 5312 5952 5984 1080 1105 1110 1135
  Modeline "5280x1200" 191.40 5280 5312 6032 6064 1200 1228 1233 1261
  Modeline "5120x3200" 199.75 5120 5152 5904 5936 3200 3277 3283 3361
  Modeline "4800x1200" 64.42 4800 4832 5072 5104 1200 1229 1231 1261
  Modeline "3840x2880" 133.43 3840 3872 4376 4408 2880 2950 2955 3025
  Modeline "3840x2560" 116.93 3840 3872 4312 4344 2560 2622 2627 2689
  Modeline "3840x2048" 91.45 3840 3872 4216 4248 2048 2097 2101 2151
  Modeline "3840x1080" 100.38 3840 3848 4216 4592 1080 1081 1084 1093
  Modeline "3600x1200" 106.06 3600 3632 3984 4368 1200 1201 1204 1214
  Modeline "3288x1080" 39.76 3288 3320 3464 3496 1080 1106 1108 1135
  Modeline "2048x2048" 49.47 2048 2080 2264 2296 2048 2097 2101 2151
  Modeline "2048x1536" 80.06 2048 2104 2312 2576 1536 1537 1540 1554
  Modeline "2560x1600" 47.12 2560 2592 2768 2800 1600 1639 1642 1681
  Modeline "2560x1440" 42.12 2560 2592 2752 2784 1440 1475 1478 1513
  Modeline "1920x1440" 69.47 1920 1960 2152 2384 1440 1441 1444 1457
  Modeline "1920x1200" 26.28 1920 1952 2048 2080 1200 1229 1231 1261
  Modeline "1920x1080" 23.53 1920 1952 2040 2072 1080 1106 1108 1135
  Modeline "1680x1050" 20.08 1680 1712 1784 1816 1050 1075 1077 1103
  Modeline "1600x1200" 22.04 1600 1632 1712 1744 1200 1229 1231 1261
  Modeline "1600x900" 33.92 1600 1632 1760 1792 900 921 924 946
  Modeline "1440x900" 30.66 1440 1472 1584 1616 900 921 924 946
  ModeLine "1366x768" 72.00 1366 1414 1446 1494  768 771 777 803
  Modeline "1280x1024" 31.50 1280 1312 1424 1456 1024 1048 1052 1076
  Modeline "1280x800" 24.15 1280 1312 1400 1432 800 819 822 841
  Modeline "1280x768" 23.11 1280 1312 1392 1424 768 786 789 807
  Modeline "1360x768" 24.49 1360 1392 1480 1512 768 786 789 807
  Modeline "1024x768" 18.71 1024 1056 1120 1152 768 786 789 807
  Modeline "768x1024" 19.50 768 800 872 904 1024 1048 1052 1076


  #common resolutions for android devices (both orientations):
  Modeline "800x1280" 25.89 800 832 928 960 1280 1310 1315 1345
  Modeline "1280x800" 24.15 1280 1312 1400 1432 800 819 822 841
  Modeline "720x1280" 30.22 720 752 864 896 1280 1309 1315 1345
  Modeline "1280x720" 27.41 1280 1312 1416 1448 720 737 740 757
  Modeline "768x1024" 24.93 768 800 888 920 1024 1047 1052 1076
  Modeline "1024x768" 23.77 1024 1056 1144 1176 768 785 789 807
  Modeline "600x1024" 19.90 600 632 704 736 1024 1047 1052 1076
  Modeline "1024x600" 18.26 1024 1056 1120 1152 600 614 617 631
  Modeline "536x960" 16.74 536 568 624 656 960 982 986 1009
  Modeline "960x536" 15.23 960 992 1048 1080 536 548 551 563
  Modeline "600x800" 15.17 600 632 688 720 800 818 822 841
  Modeline "800x600" 14.50 800 832 880 912 600 614 617 631
  Modeline "480x854" 13.34 480 512 560 592 854 873 877 897
  Modeline "848x480" 12.09 848 880 920 952 480 491 493 505
  Modeline "480x800" 12.43 480 512 552 584 800 818 822 841
  Modeline "800x480" 11.46 800 832 872 904 480 491 493 505
  #resolutions for android devices (both orientations)
  #minus the status bar
  #38px status bar (and width rounded up)
  Modeline "800x1242" 25.03 800 832 920 952 1242 1271 1275 1305
  Modeline "1280x762" 22.93 1280 1312 1392 1424 762 780 783 801
  Modeline "720x1242" 29.20 720 752 856 888 1242 1271 1276 1305
  Modeline "1280x682" 25.85 1280 1312 1408 1440 682 698 701 717
  Modeline "768x986" 23.90 768 800 888 920 986 1009 1013 1036
  Modeline "1024x730" 22.50 1024 1056 1136 1168 730 747 750 767
  Modeline "600x986" 19.07 600 632 704 736 986 1009 1013 1036
  Modeline "1024x562" 17.03 1024 1056 1120 1152 562 575 578 591
  Modeline "536x922" 16.01 536 568 624 656 922 943 947 969
  Modeline "960x498" 14.09 960 992 1040 1072 498 509 511 523
  Modeline "600x762" 14.39 600 632 680 712 762 779 783 801
  Modeline "800x562" 13.52 800 832 880 912 562 575 578 591
  Modeline "480x810" 12.59 480 512 552 584 810 828 832 851
  Modeline "848x442" 11.09 848 880 920 952 442 452 454 465
  Modeline "480x762" 11.79 480 512 552 584 762 779 783 801
EndSection

Section "Screen"
  Identifier "dummy_screen"
  Device "dummy_videocard"
  Monitor "dummy_monitor"
  DefaultDepth 24
  SubSection "Display"
    Viewport 0 0
    Depth 24
    Modes {{ $JIBRI_RECORDING_RESOLUTION | quote }}
  EndSubSection
EndSection

Section "ServerLayout"
  Identifier   "dummy_layout"
  Screen       "dummy_screen"
EndSection
````

## File: jibri/rootfs/etc/chromium/policies/managed/managed_policies.json
````json
{
  "CommandLineFlagSecurityWarningsEnabled": false
}
````

## File: jibri/rootfs/etc/cont-init.d/10-config
````
#!/usr/bin/with-contenv bash

# Check if the SYS_ADMIN cap is set
if ! capsh --has-p=cap_sys_admin; then
    echo "Required capability SYS_ADMIN is missing"
    exit 1
fi

# Check if /dev/shm is large enough (2GB at least)
if ! shm-check; then
    echo "/dev/shm must be at least 2GB in size"
    exit 1
fi

if [[ -z $JIBRI_RECORDER_PASSWORD || -z $JIBRI_XMPP_PASSWORD ]]; then
    echo 'FATAL ERROR: Jibri recorder password and auth password must be set'
    exit 1
fi

OLD_JIBRI_RECORDER_PASSWORD=passw0rd
if [[ "$JIBRI_RECORDER_PASSWORD" == "$OLD_JIBRI_RECORDER_PASSWORD" ]]; then
    echo 'FATAL ERROR: Jibri recorder password must be changed, check the README'
    exit 1
fi

OLD_JIBRI_XMPP_PASSWORD=passw0rd
if [[ "$JIBRI_XMPP_PASSWORD" == "$OLD_JIBRI_XMPP_PASSWORD" ]]; then
    echo 'FATAL ERROR: Jibri auth password must be changed, check the README'
    exit 1
fi

# DISPLAY is necessary for start
[ -z "${DISPLAY}" ] \
&& ( echo -e "\e[31mERROR: Please set DISPLAY variable.\e[39m"; kill 1; exit 1 )

# script for finalizing must have executing bit.
[ ! -z "${JIBRI_FINALIZE_RECORDING_SCRIPT_PATH}" ] \
&& [ -f "${JIBRI_FINALIZE_RECORDING_SCRIPT_PATH}" ] \
&& [ ! -x "${JIBRI_FINALIZE_RECORDING_SCRIPT_PATH}" ] \
&& chmod +x ${JIBRI_FINALIZE_RECORDING_SCRIPT_PATH}

# set random jibri nickname for the instance if is not set
[ -z "${JIBRI_INSTANCE_ID}" ] && export JIBRI_INSTANCE_ID="jibri-$(date +%N)"

# check for AUTOSCALER_URL, AUTOSCALER_SIDECAR_KEY_FILE and AUTOSCALER_SIDECAR_KEY_ID as indicator that sidecar should be enabled
if [ -n "$AUTOSCALER_URL" ]; then
    if [ -z "$AUTOSCALER_SIDECAR_KEY_FILE" ]; then
        export AUTOSCALER_SIDECAR_KEY_FILE="/etc/jitsi/autoscaler-sidecar/asap.pem"
    fi
    if [ -z "$AUTOSCALER_SIDECAR_KEY_ID" ]; then
        # assume key id is equal to the base real path of the key file minus .pem
        export AUTOSCALER_SIDECAR_KEY_ID="$(basename "$(realpath "$AUTOSCALER_SIDECAR_KEY_FILE")" | tr -d '.pem')"
    fi

    if [ -f "$AUTOSCALER_SIDECAR_KEY_FILE" ]; then
        echo "AUTOSCALER_URL found, enabling autoscaler sidecar"

        export JIBRI_VERSION="$(dpkg -s jibri | grep Version | awk '{print $2}' | sed 's/..$//')"

        [ -z "$AUTOSCALER_SIDECAR_PORT" ] && export AUTOSCALER_SIDECAR_PORT="6000"
        [ -z "$JIBRI_WEBHOOK_SUBSCRIBERS" ] && export JIBRI_WEBHOOK_SUBSCRIBERS="http://localhost:$AUTOSCALER_SIDECAR_PORT/hook"
        [ -z "$AUTOSCALER_SIDECAR_INSTANCE_ID" ] && export AUTOSCALER_SIDECAR_INSTANCE_ID="$JIBRI_INSTANCE_ID"
        [ -z "$AUTOSCALER_SIDECAR_REGION" ] && export AUTOSCALER_SIDECAR_REGION="docker"
        [ -z "$AUTOSCALER_SIDECAR_GROUP_NAME" ] && export AUTOSCALER_SIDECAR_GROUP_NAME="docker-jibri"
        [ -z "$LOCAL_ADDRESS" ] && export LOCAL_ADDRESS="$(ip route get 1 | grep -oP '(?<=src ).*' | awk '{ print $1 '})"

        mkdir -p /etc/jitsi/autoscaler-sidecar
        tpl /defaults/autoscaler-sidecar.config > /etc/jitsi/autoscaler-sidecar/config
    else
        echo "No key file at $AUTOSCALER_SIDECAR_KEY_FILE, leaving autoscaler sidecar disabled"
    fi
else
    echo "No AUTOSCALER_URL defined, leaving autoscaler sidecar disabled"
fi

# maintain backward compatibility with older variable
[ -z "${XMPP_HIDDEN_DOMAIN}" ] && export XMPP_HIDDEN_DOMAIN="$XMPP_RECORDER_DOMAIN"

# always recreate configs
tpl /defaults/jibri.conf > /etc/jitsi/jibri/jibri.conf
tpl /defaults/xmpp.conf > /etc/jitsi/jibri/xmpp.conf
tpl /defaults/logging.properties > /etc/jitsi/jibri/logging.properties
tpl /defaults/xorg-video-dummy.conf > /etc/jitsi/jibri/xorg-video-dummy.conf

# make recording dir
[ -z "${JIBRI_RECORDING_DIR}" ] && export JIBRI_RECORDING_DIR=/config/recordings
mkdir -p ${JIBRI_RECORDING_DIR}
chown -R jibri ${JIBRI_RECORDING_DIR}

# make logs dir
JIBRI_LOGS_DIR=/config/logs
mkdir -p ${JIBRI_LOGS_DIR}
chown -R jibri ${JIBRI_LOGS_DIR}
````

## File: jibri/rootfs/etc/fix-attrs.d/10-jibri
````
/home/jibri/.config true jibri 0640 0750
/home/jibri false jibri 0640 0750
````

## File: jibri/rootfs/etc/opt/chrome/policies/managed/managed_policies.json
````json
{
  "CommandLineFlagSecurityWarningsEnabled": false
}
````

## File: jibri/rootfs/etc/pulse/default.pa
````
#!/usr/bin/pulseaudio -nF
#
# This file is part of PulseAudio.
#
# PulseAudio is free software; you can redistribute it and/or modify it
# under the terms of the GNU Lesser General Public License as published by
# the Free Software Foundation; either version 2 of the License, or
# (at your option) any later version.
#
# PulseAudio is distributed in the hope that it will be useful, but
# WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU
# General Public License for more details.
#
# You should have received a copy of the GNU Lesser General Public License
# along with PulseAudio; if not, see <http://www.gnu.org/licenses/>.

# This startup script is used only if PulseAudio is started per-user
# (i.e. not in system mode)

# Customized for Jibri

.fail

### Automatically restore the volume of streams and devices
load-module module-device-restore
load-module module-stream-restore
load-module module-card-restore

### Automatically augment property information from .desktop files
### stored in /usr/share/application
load-module module-augment-properties

### Should be after module-*-restore but before module-*-detect
load-module module-switch-on-port-available

### Load several protocols
load-module module-native-protocol-unix

### Automatically restore the default sink/source when changed by the user
### during runtime
### NOTE: This should be loaded as early as possible so that subsequent modules
### that look up the default sink/source get the right value
load-module module-default-device-restore

### Make sure we always have a sink around, even if it is a null sink.
load-module module-always-sink

### Honour intended role device property
load-module module-intended-roles

### Automatically suspend sinks/sources that become idle for too long
load-module module-suspend-on-idle

### If autoexit on idle is enabled we want to make sure we only quit
### when no local session needs us anymore.
#.ifexists module-console-kit.so
#load-module module-console-kit
#.endif
#.ifexists module-systemd-login.so
#load-module module-systemd-login
#.endif

### Enable positioned event sounds
load-module module-position-event-sounds
````

## File: jibri/rootfs/etc/services.d/10-xorg/run
````
#!/usr/bin/with-contenv bash

DAEMON="/usr/bin/Xorg -nocursor -noreset  +extension RANDR +extension RENDER -logfile /tmp/xorg.log  -config /etc/jitsi/jibri/xorg-video-dummy.conf ${DISPLAY}"
exec s6-setuidgid jibri /bin/bash -c "exec $DAEMON"
````

## File: jibri/rootfs/etc/services.d/20-icewm/run
````
#!/usr/bin/with-contenv bash

DAEMON="/usr/bin/icewm-session"
exec s6-setuidgid jibri /bin/bash -c "exec $DAEMON"
````

## File: jibri/rootfs/etc/services.d/30-pulse/run
````
#!/usr/bin/with-contenv bash

HOME=/home/jibri
exec s6-setuidgid jibri /bin/bash -c "exec /usr/bin/pulseaudio"
````

## File: jibri/rootfs/etc/services.d/40-jibri/finish
````
#!/usr/bin/with-contenv bash

# When jibri is shutdown (or gracefully shutdown), it exits with code 255.
# In this case, we don't want S6 to restart the service. We want to stop all
# services and shutdown the container.

if [[ $1 -eq 255 ]]; then
  s6-svscanctl -t /var/run/s6/services
fi
````

## File: jibri/rootfs/etc/services.d/40-jibri/run
````
#!/usr/bin/with-contenv bash

# we have to set it, otherwise chrome won't find ~/.asoundrc file
HOME=/home/jibri

DAEMON=/opt/jitsi/jibri/launch.sh
CHROME_BIN_PATH="$(which google-chrome)"
[ $? -ne 0 ] && CHROME_BIN_PATH="$(which chromium)"
# pre-warm google chrome before jibri launches to ensure fast chrome launch during recordings
[ -n "$CHROME_BIN_PATH" ] && s6-setuidgid jibri $CHROME_BIN_PATH --timeout=1000 --headless about:blank
exec s6-setuidgid jibri /bin/bash -c "exec $DAEMON"
````

## File: jibri/rootfs/etc/services.d/50-autoscaler-sidecar/run
````
#!/usr/bin/with-contenv bash

if [[ -n "$AUTOSCALER_URL" ]] && [[ -f "/etc/jitsi/autoscaler-sidecar/config" ]]; then
    DAEMON="/usr/bin/node /usr/share/jitsi-autoscaler-sidecar/app.js"
    exec s6-setuidgid autoscaler-sidecar /bin/bash -c ". /etc/jitsi/autoscaler-sidecar/config && exec $DAEMON"
else
    # if autoscaler-sidecar should not be started,
    # prevent s6 from restarting this script again and again
    s6-svc -O /var/run/s6/services/50-autoscaler-sidecar
fi
````

## File: jibri/rootfs/home/jibri/.config/pulse/client.conf
````
autospawn = no
````

## File: jibri/rootfs/home/jibri/.config/pulse/daemon.conf
````
daemonize = no
high-priority = no
realtime-scheduling = yes
realtime-priority = 5
exit-idle-time = -1
flat-volumes = no
deferred-volume-safety-margin-usec = 1
log-level = info
log-target = file:/config/logs/pulse.log
````

## File: jibri/rootfs/home/jibri/.config/pulse/default.pa
````
.include /etc/pulse/default.pa

# Load the virtual sink and set it as default
load-module module-virtual-sink sink_name=jibri-loop
set-default-sink jibri-loop

# set the monitor of the jibri-loop sink to be the default source
set-default-source jibri-loop.monitor
````

## File: jibri/rootfs/opt/jitsi/shutdown.sh
````bash
#!/usr/bin/with-contenv bash
# notify the sidecar of imminent shutdown
PORT=${AUTOSCALER_SIDECAR_PORT:-6000}
curl -d '{}' -v 0:$PORT/hook/v1/shutdown
sleep 10

# signal jibri to shutdown via rest api
/opt/jitsi/jibri/shutdown.sh

# shutdown everything else
s6-svscanctl -t /var/run/s6/services
````

## File: jibri/rootfs/usr/bin/install-chrome.sh
````bash
#!/bin/bash

set -o pipefail -xeu

dpkgArch="$(dpkg --print-architecture)"

if [ "${USE_CHROMIUM}" = 1 -o "${dpkgArch##*-}" = "arm64" ]; then
    echo "Using Debian's Chromium"
    apt-dpkg-wrap apt-get install -y chromium chromium-driver chromium-sandbox
    chromium --version
else
    if  [ "${CHROME_RELEASE}" = "latest" ]; then
        wget -qO - https://dl-ssl.google.com/linux/linux_signing_key.pub | gpg --dearmour > /etc/apt/trusted.gpg.d/google.gpg
        echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" > /etc/apt/sources.list.d/google-chrome.list
        apt-dpkg-wrap apt-get update
        apt-dpkg-wrap apt-get install -y google-chrome-stable
    else
        CHROME_DEB="/tmp/google-chrome-stable_${CHROME_RELEASE}-1_amd64.deb"
        curl -4so ${CHROME_DEB} "http://dl.google.com/linux/chrome/deb/pool/main/g/google-chrome-stable/google-chrome-stable_${CHROME_RELEASE}-1_amd64.deb"
        apt-dpkg-wrap apt-get install -y ${CHROME_DEB}
        rm -f ${CHROME_DEB}
    fi

    google-chrome --version

    BASE_URL=https://googlechromelabs.github.io/chrome-for-testing

    if [ "${CHROME_RELEASE}" = "latest" ]; then
        CHROMEDRIVER_RELEASE="$(curl -4Ls ${BASE_URL}/LATEST_RELEASE_STABLE)"
    else
        CHROMEDRIVER_MAJOR_RELEASE=$(echo $CHROME_RELEASE | cut -d. -f1)
        CHROMEDRIVER_RELEASE="$(curl -4Ls ${BASE_URL}/LATEST_RELEASE_${CHROMEDRIVER_MAJOR_RELEASE})"
    fi

    CHROMEDRIVER_ZIP="/tmp/chromedriver_linux64.zip"
    curl -4Lso ${CHROMEDRIVER_ZIP} "https://storage.googleapis.com/chrome-for-testing-public/${CHROMEDRIVER_RELEASE}/linux64/chromedriver-linux64.zip"
    unzip ${CHROMEDRIVER_ZIP} -d /tmp/
    mv /tmp/chromedriver-linux64/chromedriver /usr/bin/
    chmod +x /usr/bin/chromedriver
    rm -rf /tmp/chromedriver*
fi

chromedriver --version
````

## File: jibri/Dockerfile
````
ARG JITSI_REPO=jitsi
ARG BASE_TAG=latest
FROM ${JITSI_REPO}/base-java:${BASE_TAG}

LABEL org.opencontainers.image.title="Jitsi Broadcasting Infrastructure (jibri)"
LABEL org.opencontainers.image.description="Components for recording and/or streaming a conference."
LABEL org.opencontainers.image.url="https://github.com/jitsi/jibri"
LABEL org.opencontainers.image.source="https://github.com/jitsi/docker-jitsi-meet"
LABEL org.opencontainers.image.documentation="https://jitsi.github.io/handbook/"

ARG USE_CHROMIUM=0
#ARG CHROME_RELEASE=latest
# https://googlechromelabs.github.io/chrome-for-testing/
ARG CHROME_RELEASE=130.0.6723.116

COPY rootfs/ /

RUN apt-dpkg-wrap apt-get update && \
    apt-dpkg-wrap apt-get install -y -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" \
        jibri \
        libgl1-mesa-dri \
        procps \
        jitsi-upload-integrations \
        jitsi-autoscaler-sidecar \
        jq \
        pulseaudio \
        dbus \
        dbus-x11 \
        rtkit \
        unzip \
        fonts-noto \
        fonts-noto-cjk \
        libcap2-bin && \
    /usr/bin/install-chrome.sh && \
    apt-cleanup && \
    adduser jibri rtkit && \
    dpkgArch="$(dpkg --print-architecture)" && \
    case "${dpkgArch##*-}" in \
        "amd64") SC_ARCH=x86_64 ;; \
        "arm64") SC_ARCH=aarch64 ;; \
        *) echo "unsupported architecture"; exit 1 ;; \
    esac && \
    wget -qO /usr/bin/shm-check https://github.com/saghul/shm-check/releases/download/v1.0.0/shm-check-${SC_ARCH} && \
    chmod +x /usr/bin/shm-check

VOLUME /config
````

## File: jicofo/rootfs/defaults/jicofo.conf
````
{{ $ENABLE_AUTH := .Env.ENABLE_AUTH | default "0" -}}
{{ $ENABLE_VISITORS := .Env.ENABLE_VISITORS | default "0" | toBool -}}
{{ $JICOFO_ENABLE_AUTH := .Env.JICOFO_ENABLE_AUTH | default $ENABLE_AUTH | toBool -}}
{{ $AUTH_TYPE := .Env.AUTH_TYPE | default "internal" -}}
{{ $JICOFO_AUTH_TYPE := .Env.JICOFO_AUTH_TYPE | default $AUTH_TYPE -}}
{{ $JICOFO_AUTH_LIFETIME := .Env.JICOFO_AUTH_LIFETIME | default "24 hours" -}}
{{ $ENABLE_SCTP := .Env.ENABLE_SCTP | default "1" | toBool -}}
{{ $ENABLE_RECORDING := .Env.ENABLE_RECORDING | default "0" | toBool -}}
{{ $ENABLE_TRANSCRIPTIONS := .Env.ENABLE_TRANSCRIPTIONS | default "0" | toBool -}}
{{ $ENABLE_OCTO := .Env.ENABLE_OCTO | default "0" | toBool -}}
{{ $ENABLE_OCTO_SCTP := .Env.ENABLE_OCTO_SCTP | default $ENABLE_SCTP | toBool -}}
{{ $ENABLE_AUTO_LOGIN := .Env.ENABLE_AUTO_LOGIN | default "1" | toBool -}}
{{ $ENABLE_REST := .Env.JICOFO_ENABLE_REST | default "0" | toBool -}}
{{ $ENABLE_JVB_XMPP_SERVER := .Env.ENABLE_JVB_XMPP_SERVER | default "0" | toBool -}}
{{ $ENABLE_SHARED_DOCUMENT_RANDOM_NAME := .Env.ENABLE_SHARED_DOCUMENT_RANDOM_NAME | default "0" | toBool -}}
{{ $HEALTH_CHECKS_USE_PRESENCE := .Env.JICOFO_HEALTH_CHECKS_USE_PRESENCE | default "0" | toBool -}}
{{ $JIBRI_BREWERY_MUC := .Env.JIBRI_BREWERY_MUC | default "jibribrewery" -}}
{{ $JIGASI_BREWERY_MUC := .Env.JIGASI_BREWERY_MUC | default "jigasibrewery" -}}
{{ $JVB_BREWERY_MUC := .Env.JVB_BREWERY_MUC | default "jvbbrewery" -}}
{{ $JIBRI_PENDING_TIMEOUT := .Env.JIBRI_PENDING_TIMEOUT | default "90 seconds" -}}
{{ $JVB_XMPP_AUTH_DOMAIN := .Env.JVB_XMPP_AUTH_DOMAIN | default "auth.jvb.meet.jitsi" -}}
{{ $JVB_XMPP_INTERNAL_MUC_DOMAIN := .Env.JVB_XMPP_INTERNAL_MUC_DOMAIN | default "muc.jvb.meet.jitsi" -}}
{{ $JVB_XMPP_PORT := .Env.JVB_XMPP_PORT | default "6222" -}}
{{ $JVB_XMPP_SERVER := .Env.JVB_XMPP_SERVER | default "xmpp.jvb.meet.jitsi" -}}
{{ $VISITORS_MAX_VISITORS_PER_NODE := .Env.VISITORS_MAX_VISITORS_PER_NODE | default "250" }}
{{ $VISITORS_MUC_PREFIX := .Env.PROSODY_VISITORS_MUC_PREFIX | default "muc" -}}
{{ $VISITORS_REQUIRE_MUC_CONFIG := .Env.JICOFO_VISITORS_REQUIRE_MUC_CONFIG | default "0" | toBool }}
{{ $VISITORS_XMPP_AUTH_DOMAIN := .Env.VISITORS_XMPP_AUTH_DOMAIN | default "auth.meet.jitsi" -}}
{{ $VISITORS_XMPP_DOMAIN := .Env.VISITORS_XMPP_DOMAIN | default "meet.jitsi" -}}
{{ $VISITORS_XMPP_SERVER := .Env.VISITORS_XMPP_SERVER | default "" -}}
{{ $VISITORS_XMPP_SERVERS := splitList "," $VISITORS_XMPP_SERVER | compact -}}
{{ $VISITORS_XMPP_PORT := .Env.VISITORS_XMPP_PORT | default 52220 }}
{{ $XMPP_AUTH_DOMAIN := .Env.XMPP_AUTH_DOMAIN | default "auth.meet.jitsi" -}}
{{ $XMPP_MUC_DOMAIN := .Env.XMPP_MUC_DOMAIN | default "muc.meet.jitsi" -}}
{{ $XMPP_INTERNAL_MUC_DOMAIN := .Env.XMPP_INTERNAL_MUC_DOMAIN | default "internal-muc.meet.jitsi" -}}
{{ $XMPP_DOMAIN := .Env.XMPP_DOMAIN | default "meet.jitsi" -}}
{{ $XMPP_HIDDEN_DOMAIN := .Env.XMPP_HIDDEN_DOMAIN | default "hidden.meet.jitsi" -}}
{{ $XMPP_PORT := .Env.XMPP_PORT | default "5222" -}}
{{ $XMPP_SERVER := .Env.XMPP_SERVER | default "xmpp.meet.jitsi" -}}
{{ $MAX_SSRCS_PER_USER := .Env.MAX_SSRCS_PER_USER | default "20" -}}
{{ $MAX_SSRC_GROUPS_PER_USER := .Env.MAX_SSRC_GROUPS_PER_USER | default $MAX_SSRCS_PER_USER -}}
{{ $TRUSTED_DOMAIN_LIST := .Env.JICOFO_TRUSTED_DOMAINS | default (or $ENABLE_RECORDING $ENABLE_TRANSCRIPTIONS | ternary $XMPP_HIDDEN_DOMAIN "") -}}
{{ $TRUSTED_DOMAINS := splitList "," $TRUSTED_DOMAIN_LIST | compact -}}
{{ $ENV := .Env }}

jicofo {
    {{ if $JICOFO_ENABLE_AUTH }}
    authentication {
      enabled = true
      // The type of authentication. Supported values are XMPP or JWT.
      {{ if eq $JICOFO_AUTH_TYPE "jwt" }}
      type = JWT
      {{ else }}
      type = XMPP
      {{ end }}
      login-url = "{{ $XMPP_DOMAIN }}"
      enable-auto-login = {{ $ENABLE_AUTO_LOGIN }}
      authentication-lifetime = {{ $JICOFO_AUTH_LIFETIME }}
    }
    {{ end }}

    // Configuration related to jitsi-videobridge
    bridge {
      {{ if .Env.MAX_BRIDGE_PARTICIPANTS }}
      max-bridge-participants = "{{ .Env.MAX_BRIDGE_PARTICIPANTS }}"
      {{ end }}

      {{ if .Env.BRIDGE_AVG_PARTICIPANT_STRESS }}
      // The assumed average stress per participant. default is 0.01
      average-participant-stress = "{{ .Env.BRIDGE_AVG_PARTICIPANT_STRESS }}"
      {{ end }}

      {{ if .Env.BRIDGE_STRESS_THRESHOLD }}
      // The stress level above which a bridge is considered overstressed. 0.8 is the default value
      stress-threshold = "{{ .Env.BRIDGE_STRESS_THRESHOLD }}"
      {{ end }}

      {{ if $ENABLE_VISITORS }}
      selection-strategy = VisitorSelectionStrategy
      visitor-selection-strategy = RegionBasedBridgeSelectionStrategy
      participant-selection-strategy = RegionBasedBridgeSelectionStrategy
      topology-strategy = VisitorTopologyStrategy
      {{ else }}
      {{ if .Env.OCTO_BRIDGE_SELECTION_STRATEGY }}
      selection-strategy = "{{ .Env.OCTO_BRIDGE_SELECTION_STRATEGY }}"
      {{ end }}
      {{ end }}

      {{ if .Env.JICOFO_ENABLE_BRIDGE_HEALTH_CHECKS }}
      health-checks {
        enabled = {{ .Env.JICOFO_ENABLE_BRIDGE_HEALTH_CHECKS | toBool }}
        use-presence = {{ $HEALTH_CHECKS_USE_PRESENCE }}
      }
      {{ end }}

      {{ if .Env.JICOFO_ENABLE_ICE_FAILURE_DETECTION }}
      ice-failure-detection {
        enabled = {{ .Env.JICOFO_ENABLE_ICE_FAILURE_DETECTION | toBool }}
      }
      {{ end }}

      {{ if $ENABLE_JVB_XMPP_SERVER }}
      brewery-jid = "{{ $JVB_BREWERY_MUC }}@{{ $JVB_XMPP_INTERNAL_MUC_DOMAIN }}"
      {{ else }}
      brewery-jid = "{{ $JVB_BREWERY_MUC }}@{{ $XMPP_INTERNAL_MUC_DOMAIN }}"
      {{ end }}

      {{ if .Env.JICOFO_BRIDGE_REGION_GROUPS }}
      region-groups = [{{ .Env.JICOFO_BRIDGE_REGION_GROUPS }}]
      {{ end }}
    }
    // Configure the codecs and RTP extensions to be used in the offer sent to clients.
    codec {
      video {
        {{ if .Env.ENABLE_CODEC_VP8 }}
        vp8 {
          enabled = {{ .Env.ENABLE_CODEC_VP8 | toBool }}
        }
        {{ end }}
        {{ if .Env.ENABLE_CODEC_VP9 }}
        vp9 {
          enabled = {{ .Env.ENABLE_CODEC_VP9 | toBool }}
        }
        {{ end }}
        {{ if .Env.ENABLE_CODEC_AV1 }}
        av1 {
          enabled = {{ .Env.ENABLE_CODEC_AV1 | toBool }}
        }
        {{ end }}
        {{ if .Env.ENABLE_CODEC_H264 }}
        h264 {
          enabled = {{ .Env.ENABLE_CODEC_H264 | toBool }}
        }
        {{ end }}
      }
      audio {
        {{ if .Env.ENABLE_CODEC_OPUS_RED }}
        opus {
          red {
            enabled = {{ .Env.ENABLE_CODEC_OPUS_RED | toBool }}
          }
        }
        {{ end }}
      }
      rtp-extensions {
        video-layers-allocation {
          enabled = {{ .Env.ENABLE_VLA | default "0" | toBool }}
        }
      }
    }

    conference {
      {{ if .Env.ENABLE_AUTO_OWNER }}
      enable-auto-owner = {{ .Env.ENABLE_AUTO_OWNER | toBool }}
      {{ end }}

      {{ if .Env.ENABLE_MODERATOR_CHECKS }}
      enable-moderator-checks = {{ .Env.ENABLE_MODERATOR_CHECKS | toBool }}
      {{ end }}

      {{ if .Env.JICOFO_CONF_INITIAL_PARTICIPANT_WAIT_TIMEOUT }}
      initial-timeout = "{{ .Env.JICOFO_CONF_INITIAL_PARTICIPANT_WAIT_TIMEOUT }}"
      {{ end }}

      max-ssrcs-per-user = "{{ $MAX_SSRCS_PER_USER }}"

      max-ssrc-groups-per-user = "{{ $MAX_SSRC_GROUPS_PER_USER }}"

      {{ if .Env.JICOFO_CONF_SINGLE_PARTICIPANT_TIMEOUT }}
      single-participant-timeout = "{{ .Env.JICOFO_CONF_SINGLE_PARTICIPANT_TIMEOUT }}"
      {{ end }}

      {{ if .Env.JICOFO_CONF_SOURCE_SIGNALING_DELAYS }}
      source-signaling-delays = {{ .Env.JICOFO_CONF_SOURCE_SIGNALING_DELAYS }}
      {{ end }}

      {{ if .Env.JICOFO_CONF_MAX_AUDIO_SENDERS }}
      max-audio-senders = {{ .Env.JICOFO_CONF_MAX_AUDIO_SENDERS }}
      {{ end }}

      {{ if .Env.JICOFO_CONF_MAX_VIDEO_SENDERS }}
      max-video-senders = {{ .Env.JICOFO_CONF_MAX_VIDEO_SENDERS }}
      {{ end }}

      {{ if .Env.JICOFO_CONF_STRIP_SIMULCAST }}
      strip-simulcast = {{ .Env.JICOFO_CONF_STRIP_SIMULCAST | toBool }}
      {{ end }}

      {{ if .Env.JICOFO_CONF_SSRC_REWRITING }}
      use-ssrc-rewriting = {{ .Env.JICOFO_CONF_SSRC_REWRITING | toBool }}
      {{ end }}

      {{ if .Env.JICOFO_MULTI_STREAM_BACKWARD_COMPAT }}
      enable-multi-stream-backward-compat = {{ .Env.JICOFO_MULTI_STREAM_BACKWARD_COMPAT | toBool }}
      {{ end }}

      {{ if $ENABLE_SHARED_DOCUMENT_RANDOM_NAME }}
      shared-document {
        use-random-name = {{ $ENABLE_SHARED_DOCUMENT_RANDOM_NAME }}
      }
      {{ end }}

    }

    {{ if .Env.JICOFO_ENABLE_HEALTH_CHECKS }}
    // Configuration for the internal health checks performed by jicofo.
    health {
      // Whether to perform health checks.
      enabled = {{ .Env.JICOFO_ENABLE_HEALTH_CHECKS | toBool }}
    }
    {{ end }}

    {{ if $ENABLE_RECORDING }}
    jibri {
      brewery-jid = "{{ $JIBRI_BREWERY_MUC }}@{{ $XMPP_INTERNAL_MUC_DOMAIN }}"
      {{ if .Env.JIBRI_REQUEST_RETRIES }}
      num-retries = "{{ .Env.JIBRI_REQUEST_RETRIES }}"
      {{ end }}
      pending-timeout = "{{ $JIBRI_PENDING_TIMEOUT }}"
    }
    {{ end }}

    {{ if .Env.JIBRI_SIP_BREWERY_MUC }}
    jibri-sip {
      brewery-jid = "{{ .Env.JIBRI_SIP_BREWERY_MUC }}"
    }
    {{ end }}

    {{ if or .Env.JIGASI_SIP_URI $ENABLE_TRANSCRIPTIONS }}
    jigasi {
      brewery-jid = "{{ $JIGASI_BREWERY_MUC }}@{{ $XMPP_INTERNAL_MUC_DOMAIN }}"
    }
    {{ end }}

    {{ if .Env.JICOFO_OCTO_REGION }}
    local-region = "{{ .Env.JICOFO_OCTO_REGION }}"
    {{ end }}

    octo {
      // Whether or not to use Octo. Note that when enabled, its use will be determined by
      // $jicofo.bridge.selection-strategy. There's a corresponding flag in the JVB and these
      // two MUST be in sync (otherwise bridges will crash because they won't know how to
      // deal with octo channels).
      enabled = {{ $ENABLE_OCTO }}
      sctp-datachannels = {{ $ENABLE_OCTO_SCTP }}
    }

    {{ if $ENABLE_REST }}
    rest {
      host = "0.0.0.0"
    }
    {{ end }}

    sctp {
      enabled = {{ $ENABLE_SCTP }}
    }
{{ if $ENABLE_VISITORS }}
    visitors {
      enabled = true

      {{ if .Env.VISITORS_MAX_PARTICIPANTS }}
      max-participants = {{ .Env.VISITORS_MAX_PARTICIPANTS }}
      {{ end }}
      {{ if $VISITORS_MAX_VISITORS_PER_NODE }}
      max-visitors-per-node = {{ $VISITORS_MAX_VISITORS_PER_NODE }}
      {{ end }}
      require-muc-config-flag = {{ $VISITORS_REQUIRE_MUC_CONFIG }}
    }
{{ end }}
    xmpp {
      {{ if $ENABLE_VISITORS }}
      {{ if $.Env.VISITORS_XMPP_SERVER }}
      visitors {
        {{ range $index, $element := $VISITORS_XMPP_SERVERS -}}
        {{ $SERVER := splitn ":" 2 $element }}
            v{{ $index }} {
                enabled = true
                conference-service = {{ $VISITORS_MUC_PREFIX }}.v{{ $index }}.{{ $VISITORS_XMPP_DOMAIN }}
                hostname = {{ $SERVER._0 }}
                {{ $DEFAULT_PORT := add $VISITORS_XMPP_PORT $index }}
                port = {{ $SERVER._1 | default $DEFAULT_PORT }}
                domain = "{{ $VISITORS_XMPP_AUTH_DOMAIN }}"
                xmpp-domain = v{{ $index }}.{{ $VISITORS_XMPP_DOMAIN }}
                password = "{{ $ENV.JICOFO_AUTH_PASSWORD }}"
                disable-certificate-verification = true
            }
        {{ end }}
      }
      {{ end }}
      {{ end }}
      client {
        enabled = true
        hostname = "{{ $XMPP_SERVER }}"
        port = "{{ $XMPP_PORT }}"
        domain = "{{ $XMPP_AUTH_DOMAIN }}"
        xmpp-domain = "{{ $XMPP_DOMAIN }}"
        username = "focus"
        password = "{{ .Env.JICOFO_AUTH_PASSWORD }}"
        conference-muc-jid = "{{ $XMPP_MUC_DOMAIN }}"
        client-proxy = "focus.{{ $XMPP_DOMAIN }}"
        disable-certificate-verification = true
      }
      {{ if $ENABLE_JVB_XMPP_SERVER }}
      service {
        enabled = true
        hostname = "{{ $JVB_XMPP_SERVER }}"
        port = "{{ $JVB_XMPP_PORT }}"
        domain =  "{{ $JVB_XMPP_AUTH_DOMAIN }}"
        username = "focus"
        password = "{{ .Env.JICOFO_AUTH_PASSWORD }}"
        disable-certificate-verification = true
      }
      {{ end }}

      trusted-domains = [ {{ range $index, $element := $TRUSTED_DOMAINS }}{{ if gt $index 0 }},{{ end }}"{{ $element }}"{{ end}} ]

    }
}

include "custom-jicofo.conf"
````

## File: jicofo/rootfs/defaults/logging.properties
````
{{ if .Env.SENTRY_DSN | toBool }}
handlers=java.util.logging.ConsoleHandler,io.sentry.jul.SentryHandler
{{ else }}
handlers= java.util.logging.ConsoleHandler
{{ end }}

java.util.logging.ConsoleHandler.level = ALL
java.util.logging.ConsoleHandler.formatter = org.jitsi.utils.logging2.JitsiLogFormatter
org.jitsi.utils.logging2.JitsiLogFormatter.programname=Jicofo

.level=INFO
io.sentry.jul.SentryHandler.level=WARNING

# Enable debug packets logging
#org.jitsi.impl.protocol.xmpp.level=FINE
````

## File: jicofo/rootfs/etc/cont-init.d/10-config
````
#!/usr/bin/with-contenv bash

export SENTRY_RELEASE="${SENTRY_RELEASE:-$(apt-cache policy jicofo | sed -n '/Installed/p' | sed -e 's/[^:]*: //')}"

if [[ -z $JICOFO_AUTH_PASSWORD ]]; then
    echo 'FATAL ERROR: Jicofo auth password must be set'
    exit 1
fi

OLD_JICOFO_AUTH_PASSWORD=passw0rd
if [[ "$JICOFO_AUTH_PASSWORD" == "$OLD_JICOFO_AUTH_PASSWORD" ]]; then
    echo 'FATAL ERROR: Jicofo auth password must be changed, check the README'
    exit 1
fi

# maintain backward compatibility with older variable
[ -z "${XMPP_HIDDEN_DOMAIN}" ] && export XMPP_HIDDEN_DOMAIN="$XMPP_RECORDER_DOMAIN"

tpl /defaults/logging.properties > /config/logging.properties
tpl /defaults/jicofo.conf > /config/jicofo.conf

chown -R jicofo:jitsi /config
````

## File: jicofo/rootfs/etc/services.d/jicofo/run
````
#!/usr/bin/with-contenv bash

JAVA_SYS_PROPS="-Djava.util.logging.config.file=/config/logging.properties -Dconfig.file=/config/jicofo.conf"
DAEMON=/usr/share/jicofo/jicofo.sh
DAEMON_DIR=/usr/share/jicofo/

JICOFO_CMD="exec $DAEMON"

[ -n "$JICOFO_LOG_FILE" ] && JICOFO_CMD="$JICOFO_CMD 2>&1 | tee $JICOFO_LOG_FILE"

exec s6-setuidgid jicofo /bin/bash -c "cd $DAEMON_DIR; JAVA_SYS_PROPS=\"$JAVA_SYS_PROPS\" $JICOFO_CMD"
````

## File: jicofo/rootfs/usr/local/bin/healthcheck.sh
````bash
#!/bin/bash

curl --fail-with-body http://127.0.0.1:8888/about/health
````

## File: jicofo/Dockerfile
````
ARG JITSI_REPO=jitsi
ARG BASE_TAG=latest
FROM ${JITSI_REPO}/base-java:${BASE_TAG}

LABEL org.opencontainers.image.title="Jitsi Conference Focus (jicofo)"
LABEL org.opencontainers.image.description="Server-side focus component that manages media sessions and acts as load balancer."
LABEL org.opencontainers.image.url="https://github.com/jitsi/jicofo"
LABEL org.opencontainers.image.source="https://github.com/jitsi/docker-jitsi-meet"
LABEL org.opencontainers.image.documentation="https://jitsi.github.io/handbook/"

RUN apt-dpkg-wrap apt-get update && \
    apt-dpkg-wrap apt-get install -y jicofo && \
    apt-cleanup

COPY rootfs/ /

VOLUME /config
````

## File: jigasi/rootfs/defaults/autoscaler-sidecar.config
````
{{ $SHUTDOWN_POLLING_INTERVAL := .Env.AUTOSCALER_SIDECAR_SHUTDOWN_POLLING_INTERVAL | default "60" -}}
{{ $STATS_POLLING_INTERVAL := .Env.AUTOSCALER_SIDECAR_STATS_POLLING_INTERVAL | default "30" -}}
{{ $JIGASI_STATS_PORT := .Env.JIGASI_STATS_PORT | default "8788" -}}
export SHUTDOWN_POLLING_INTERVAL={{ $SHUTDOWN_POLLING_INTERVAL }}
export STATS_POLLING_INTERVAL={{ $STATS_POLLING_INTERVAL }}
export PORT={{ .Env.AUTOSCALER_SIDECAR_PORT }}
export GRACEFUL_SHUTDOWN_SCRIPT="/usr/share/jigasi/graceful_shutdown.sh"
export TERMINATE_SCRIPT="/opt/jitsi/shutdown.sh"
export ENABLE_REPORT_STATS=true
export POLLING_URL="{{ .Env.AUTOSCALER_URL  }}/sidecar/poll"
export STATUS_URL="{{ .Env.AUTOSCALER_URL }}/sidecar/status"
export SHUTDOWN_URL="{{ .Env.AUTOSCALER_URL }}/sidecar/shutdown"
export STATS_RETRIEVE_URL="http://localhost:{{ $JIGASI_STATS_PORT }}/about/stats"
export STATS_REPORT_URL="{{ .Env.AUTOSCALER_URL }}/sidecar/stats"
export ASAP_SIGNING_KEY_FILE="{{ .Env.AUTOSCALER_SIDECAR_KEY_FILE }}"
export ASAP_JWT_KID="{{ .Env.AUTOSCALER_SIDECAR_KEY_ID }}"
export INSTANCE_TYPE="jigasi"
export INSTANCE_ID="{{ .Env.AUTOSCALER_SIDECAR_INSTANCE_ID }}"
export INSTANCE_METADATA='{"environment":"{{ .Env.XMPP_ENV_NAME }}","region":"{{ .Env.AUTOSCALER_SIDECAR_REGION }}","group":"{{ .Env.AUTOSCALER_SIDECAR_GROUP_NAME }}","name":"{{ .Env.JIGASI_INSTANCE_ID }}","version":"{{ .Env.JIGASI_VERSION }}","privateIp":"{{ .Env.LOCAL_ADDRESS }}","hostId":"{{ .Env.AUTOSCALER_SIDECAR_HOST_ID }}"}'
````

## File: jigasi/rootfs/defaults/logging.properties
````
{{ if .Env.SENTRY_DSN | toBool }}
handlers=java.util.logging.ConsoleHandler,io.sentry.jul.SentryHandler
{{ else }}
handlers=java.util.logging.ConsoleHandler
{{ end }}

java.util.logging.ConsoleHandler.level = ALL
java.util.logging.ConsoleHandler.formatter = org.jitsi.utils.logging2.JitsiLogFormatter

.level=INFO
net.sf.level=SEVERE
net.java.sip.communicator.plugin.reconnectplugin.level=FINE
org.ice4j.level=SEVERE
org.jitsi.impl.neomedia.level=SEVERE
io.sentry.jul.SentryHandler.level=WARNING

# Do not worry about missing strings
net.java.sip.communicator.service.resources.AbstractResourcesService.level=SEVERE

#net.java.sip.communicator.service.protocol.level=ALL
````

## File: jigasi/rootfs/defaults/sip-communicator.properties
````
{{ $JIGASI_JVB_TIMEOUT := .Env.JIGASI_JVB_TIMEOUT | default "30000" -}}
{{ $JIGASI_LOCAL_REGION := .Env.JIGASI_LOCAL_REGION | default "" -}}
{{ $BOSH_URL_PATTERN := .Env.BOSH_URL_PATTERN | default "" -}}
{{ $XMPP_MUC_DOMAIN := .Env.XMPP_MUC_DOMAIN | default "muc.meet.jitsi" -}}
{{ $XMPP_SERVER := .Env.XMPP_SERVER | default "xmpp.meet.jitsi" -}}
{{ $XMPP_SERVERS := splitList "," $XMPP_SERVER | compact -}}
{{ $XMPP_PORT := .Env.XMPP_PORT | default "5222" -}}
{{ $XMPP_SERVER_ADDRESS := splitn ":" 3 ($XMPP_SERVERS | first) }}
{{ $SHUTDOWN_REST_ENABLED := .Env.SHUTDOWN_REST_ENABLED | default "false" | toBool -}}
{{ $TRUSTED_DOMAIN_LIST := .Env.JIGASI_TRUSTED_DOMAINS | default "" -}}
{{ $TRUSTED_DOMAINS := splitList "," $TRUSTED_DOMAIN_LIST | compact -}}

net.java.sip.communicator.impl.protocol.SingleCallInProgressPolicy.enabled=false

# Adjust opus encoder complexity
net.java.sip.communicator.impl.neomedia.codec.audio.opus.encoder.COMPLEXITY=10

# Disables packet logging
net.java.sip.communicator.packetlogging.PACKET_LOGGING_ENABLED=false

# Control REST Shutdown
org.jitsi.jigasi.ENABLE_REST_SHUTDOWN={{ $SHUTDOWN_REST_ENABLED }}

org.jitsi.jigasi.BREWERY_ENABLED=true
org.jitsi.jigasi.MUC_JOIN_TIMEOUT=10

org.jitsi.jigasi.HEALTH_CHECK_SIP_URI={{ .Env.JIGASI_HEALTH_CHECK_SIP_URI | default "" }}
org.jitsi.jigasi.HEALTH_CHECK_INTERVAL={{ .Env.JIGASI_HEALTH_CHECK_INTERVAL | default "300000" }}
org.jitsi.jigasi.HEALTH_CHECK_TIMEOUT=600000

org.jitsi.jigasi.xmpp.acc.IS_SERVER_OVERRIDDEN=true
org.jitsi.jigasi.xmpp.acc.SERVER_ADDRESS={{ $XMPP_SERVER_ADDRESS._0 }}
org.jitsi.jigasi.xmpp.acc.SERVER_PORT={{ $XMPP_SERVER_ADDRESS._1 | default $XMPP_PORT }}
org.jitsi.jigasi.xmpp.acc.VIDEO_CALLING_DISABLED=true
org.jitsi.jigasi.xmpp.acc.JINGLE_NODES_ENABLED=false
org.jitsi.jigasi.xmpp.acc.AUTO_DISCOVER_STUN=false
org.jitsi.jigasi.xmpp.acc.IM_DISABLED=true
org.jitsi.jigasi.xmpp.acc.SERVER_STORED_INFO_DISABLED=true
org.jitsi.jigasi.xmpp.acc.IS_FILE_TRANSFER_DISABLED=true

# Activate this property if you are using self-signed certificates or other
# type of non-trusted certicates. In this mode your service trust in the
# remote certificates always.
net.java.sip.communicator.service.gui.ALWAYS_TRUST_MODE_ENABLED=true


{{ if .Env.JIGASI_SIP_DEFAULT_ROOM }}
org.jitsi.jigasi.DEFAULT_JVB_ROOM_NAME={{ .Env.JIGASI_SIP_DEFAULT_ROOM }}
{{ end }}

org.jitsi.jigasi.MUC_SERVICE_ADDRESS={{ $XMPP_MUC_DOMAIN }}

# when checking other participants whether they are jibri/jigasi we can also check the the domain they use for connecting
{{ if $TRUSTED_DOMAIN_LIST }}
org.jitsi.jigasi.TRUSTED_DOMAINS=[ {{ range $index, $element := $TRUSTED_DOMAINS }}{{ if gt $index 0 }},{{ end }}"{{ $element }}"{{ end}} ]
{{ end }}

org.jitsi.jigasi.JVB_INVITE_TIMEOUT={{ $JIGASI_JVB_TIMEOUT }}

{{ if $JIGASI_LOCAL_REGION }}
org.jitsi.jigasi.LOCAL_REGION={{ $JIGASI_LOCAL_REGION }}
{{ end }}

{{ if $BOSH_URL_PATTERN }}
org.jitsi.jigasi.xmpp.acc.BOSH_URL_PATTERN={{ $BOSH_URL_PATTERN }}
{{ end }}

{{ if .Env.JIGASI_CONFIGURATION -}}
{{ join "\n" (splitList "," .Env.JIGASI_CONFIGURATION | compact) }}
{{ end -}}
````

## File: jigasi/rootfs/defaults/sipserver-sip-communicator.properties
````
{{ $ENABLE_VISITORS := .Env.ENABLE_VISITORS | default "0" | toBool -}}
{{ $JIGASI_SIP_PASSWORD := .Env.JIGASI_SIP_PASSWORD | default "replaceme" -}}
{{ $JIGASI_XMPP_USER := .Env.JIGASI_XMPP_USER | default "jigasi" -}}
{{ $XMPP_AUTH_DOMAIN := .Env.XMPP_AUTH_DOMAIN | default "auth.meet.jitsi" -}}
{{ $XMPP_DOMAIN := .Env.XMPP_DOMAIN | default "meet.jitsi" -}}
{{ $XMPP_GUEST_DOMAIN := .Env.XMPP_GUEST_DOMAIN | default "guest.meet.jitsi" -}}
{{ $USE_TRANSLATOR_IN_CONFERENCE := .Env.USE_TRANSLATOR_IN_CONFERENCE | default "0" | toBool -}}

org.jitsi.jigasi.ENABLE_SIP=true

# SIP account
net.java.sip.communicator.impl.protocol.sip.acc1=acc1
{{ if .Env.JIGASI_SIP_SERVER }}
net.java.sip.communicator.impl.protocol.sip.acc1.PROXY_ADDRESS={{ .Env.JIGASI_SIP_SERVER }}
net.java.sip.communicator.impl.protocol.sip.acc1.PROXY_AUTO_CONFIG=false
net.java.sip.communicator.impl.protocol.sip.acc1.PROXY_PORT={{ .Env.JIGASI_SIP_PORT | default "5060" }}
net.java.sip.communicator.impl.protocol.sip.acc1.PREFERRED_TRANSPORT={{ .Env.JIGASI_SIP_TRANSPORT | default "UDP" }}
{{ end }}
{{ if .Env.JIGASI_ENABLE_SDES_SRTP | default "0" | toBool }}
net.java.sip.communicator.impl.protocol.sip.acc1.SAVP_OPTION=1
net.java.sip.communicator.impl.protocol.sip.acc1.DEFAULT_ENCRYPTION=true
net.java.sip.communicator.impl.protocol.sip.acc1.DEFAULT_SIPZRTP_ATTRIBUTE=false
net.java.sip.communicator.impl.protocol.sip.acc1.ENCRYPTION_PROTOCOL.ZRTP=0
net.java.sip.communicator.impl.protocol.sip.acc1.ENCRYPTION_PROTOCOL.SDES=1
net.java.sip.communicator.impl.protocol.sip.acc1.ENCRYPTION_PROTOCOL.DTLS-SRTP=0
net.java.sip.communicator.impl.protocol.sip.acc1.ENCRYPTION_PROTOCOL_STATUS.ZRTP=false
net.java.sip.communicator.impl.protocol.sip.acc1.ENCRYPTION_PROTOCOL_STATUS.SDES=true
net.java.sip.communicator.impl.protocol.sip.acc1.ENCRYPTION_PROTOCOL_STATUS.DTLS-SRTP=false
net.java.sip.communicator.impl.protocol.sip.acc1.SDES_CIPHER_SUITES=AES_CM_128_HMAC_SHA1_80,AES_CM_128_HMAC_SHA1_32
{{ end }}
net.java.sip.communicator.impl.protocol.sip.acc1.ACCOUNT_UID=SIP\:{{ .Env.JIGASI_SIP_URI }}
net.java.sip.communicator.impl.protocol.sip.acc1.PASSWORD={{ $JIGASI_SIP_PASSWORD | b64enc }}
net.java.sip.communicator.impl.protocol.sip.acc1.PROTOCOL_NAME=SIP
net.java.sip.communicator.impl.protocol.sip.acc1.SERVER_ADDRESS={{ .Env.JIGASI_SIP_SERVER }}
net.java.sip.communicator.impl.protocol.sip.acc1.USER_ID={{ .Env.JIGASI_SIP_URI }}
net.java.sip.communicator.impl.protocol.sip.acc1.KEEP_ALIVE_INTERVAL=25
net.java.sip.communicator.impl.protocol.sip.acc1.KEEP_ALIVE_METHOD={{ .Env.JIGASI_SIP_KEEP_ALIVE_METHOD | default "OPTIONS" }}
net.java.sip.communicator.impl.protocol.sip.acc1.VOICEMAIL_ENABLED=false
net.java.sip.communicator.impl.protocol.sip.acc1.JITSI_MEET_ROOM_HEADER_NAME=X-Room-Name
net.java.sip.communicator.impl.protocol.sip.acc1.JITSI_MEET_DOMAIN_BASE_HEADER_NAME=X-Domain-Base
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.AMR-WB/16000=750
# Temporarily disable G722, see: https://github.com/jitsi/docker-jitsi-meet/issues/1357
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.G722/8000=0
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.GSM/8000=0
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.H263-1998/90000=0
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.H264/90000=0
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.PCMA/8000=600
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.PCMU/8000=650
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.SILK/12000=0
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.SILK/16000=0
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.SILK/24000=0
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.SILK/8000=0
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.VP8/90000=0
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.iLBC/8000=10
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.opus/48000=1000
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.red/90000=0
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.speex/16000=0
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.speex/32000=0
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.speex/8000=0
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.telephone-event/8000=1
net.java.sip.communicator.impl.protocol.sip.acc1.Encodings.ulpfec/90000=0
net.java.sip.communicator.impl.protocol.sip.acc1.OVERRIDE_ENCODINGS=true
net.java.sip.communicator.impl.protocol.sip.acc1.DOMAIN_BASE={{ $XMPP_DOMAIN }}

{{ if .Env.ENABLE_AUTH | default "0" | toBool }}
{{ if .Env.ENABLE_GUESTS | default "0" | toBool }}
org.jitsi.jigasi.xmpp.acc.USER_ID={{ $JIGASI_XMPP_USER }}@{{ $XMPP_GUEST_DOMAIN }}
org.jitsi.jigasi.xmpp.acc.ANONYMOUS_AUTH=true
{{ else }}
org.jitsi.jigasi.xmpp.acc.USER_ID={{ $JIGASI_XMPP_USER }}@{{ $XMPP_AUTH_DOMAIN }}
org.jitsi.jigasi.xmpp.acc.ANONYMOUS_AUTH=false
{{ end }}
org.jitsi.jigasi.xmpp.acc.PASS={{ .Env.JIGASI_XMPP_PASSWORD }}
org.jitsi.jigasi.xmpp.acc.ALLOW_NON_SECURE=true
{{ end }}

# forces sending presence on sip call hangup
net.java.sip.communicator.impl.protocol.jabber.FORCE_PRESENCE_ON_LEAVE=true

{{ if $USE_TRANSLATOR_IN_CONFERENCE }}
org.jitsi.jigasi.xmpp.acc.USE_TRANSLATOR_IN_CONFERENCE=true
net.java.sip.communicator.impl.protocol.sip.acc1.USE_TRANSLATOR_IN_CONFERENCE=true
# Should be enabled when using translator mode
net.java.sip.communicator.impl.neomedia.audioSystem.audiosilence.captureDevice_list=["AudioSilenceCaptureDevice:noTransferData"]
{{ end }}

{{ if $ENABLE_VISITORS -}}
org.jitsi.jigasi.ENABLE_SIP_VISITORS=true
{{ if .Env.JIGASI_VISITORS_QUEUE_SERVICE_URL -}}
org.jitsi.jigasi.VISITOR_QUEUE_SERVICE={{ .Env.JIGASI_VISITORS_QUEUE_SERVICE_URL }}
{{ end -}}
{{ if .Env.JIGASI_VISITORS_QUEUE_SERVICE_PRIVATE_KEY_PATH -}}
org.jitsi.jigasi.VISITOR_QUEUE_SERVICE_PRIVATE_KEY_PATH={{ .Env.JIGASI_VISITORS_QUEUE_SERVICE_PRIVATE_KEY_PATH }}
{{ end -}}
{{ if .Env.JIGASI_VISITORS_QUEUE_SERVICE_PRIVATE_KEY_ID -}}
org.jitsi.jigasi.VISITOR_QUEUE_SERVICE_PRIVATE_KEY_ID={{ .Env.JIGASI_VISITORS_QUEUE_SERVICE_PRIVATE_KEY_ID }}
{{ end -}}
{{ end -}}
````

## File: jigasi/rootfs/defaults/transcriber-sip-communicator.properties
````
{{ $JIGASI_TRANSCRIBER_USER := .Env.JIGASI_TRANSCRIBER_USER | default "transcriber" -}}
{{ $JIGASI_TRANSCRIBER_FILTER_SILENCE := .Env.JIGASI_TRANSCRIBER_FILTER_SILENCE | default "0" | toBool -}}
{{ $XMPP_HIDDEN_DOMAIN := .Env.XMPP_HIDDEN_DOMAIN | default "hidden.meet.jitsi" -}}
{{ $JIGASI_TRANSCRIBER_ENABLE_SAVING := .Env.JIGASI_TRANSCRIBER_ENABLE_SAVING | default "1" | toBool -}}
{{ $JIGASI_TRANSCRIBER_ENABLE_TRANSLATION := .Env.JIGASI_TRANSCRIBER_ENABLE_TRANSLATION | default "0" | toBool -}}

org.jitsi.jigasi.ENABLE_SIP=false
org.jitsi.jigasi.xmpp.acc.USER_ID={{ $JIGASI_TRANSCRIBER_USER }}@{{ $XMPP_HIDDEN_DOMAIN }}
org.jitsi.jigasi.xmpp.acc.PASS={{ .Env.JIGASI_TRANSCRIBER_PASSWORD }}
org.jitsi.jigasi.xmpp.acc.ANONYMOUS_AUTH=false
org.jitsi.jigasi.xmpp.acc.ALLOW_NON_SECURE=true

# Transcription config
org.jitsi.jigasi.ENABLE_TRANSCRIPTION=true
{{ if $JIGASI_TRANSCRIBER_ENABLE_TRANSLATION -}}
org.jitsi.jigasi.transcription.ENABLE_TRANSLATION=true
{{ end -}}
{{ if $JIGASI_TRANSCRIBER_ENABLE_SAVING -}}
org.jitsi.jigasi.transcription.DIRECTORY=/tmp/transcripts
org.jitsi.jigasi.transcription.BASE_URL={{ .Env.PUBLIC_URL }}/transcripts
org.jitsi.jigasi.transcription.jetty.port=-1
org.jitsi.jigasi.transcription.ADVERTISE_URL={{ .Env.JIGASI_TRANSCRIBER_ADVERTISE_URL | default "false"}}
org.jitsi.jigasi.transcription.SAVE_JSON=false
org.jitsi.jigasi.transcription.SAVE_TXT=true
{{ end -}}
org.jitsi.jigasi.transcription.SEND_JSON=true
org.jitsi.jigasi.transcription.SEND_TXT={{ .Env.JIGASI_TRANSCRIBER_SEND_TXT | default "false"}}
org.jitsi.jigasi.transcription.RECORD_AUDIO={{ .Env.JIGASI_TRANSCRIBER_RECORD_AUDIO | default "false"}}
org.jitsi.jigasi.transcription.RECORD_AUDIO_FORMAT=wav
{{ if .Env.JIGASI_TRANSCRIBER_CUSTOM_SERVICE -}}
org.jitsi.jigasi.transcription.customService={{ .Env.JIGASI_TRANSCRIBER_CUSTOM_SERVICE }}
{{ end -}}
{{ if .Env.JIGASI_TRANSCRIBER_CUSTOM_TRANSLATION_SERVICE -}}
org.jitsi.jigasi.transcription.translationService={{ .Env.JIGASI_TRANSCRIBER_CUSTOM_TRANSLATION_SERVICE }}
{{ end -}}
{{ if .Env.JIGASI_TRANSCRIBER_LIBRETRANSLATE_URL -}}
org.jitsi.jigasi.transcription.libreTranslate.api_url={{ .Env.JIGASI_TRANSCRIBER_LIBRETRANSLATE_URL }}
{{ end -}}
{{ if $JIGASI_TRANSCRIBER_FILTER_SILENCE -}}
org.jitsi.jigasi.transcription.FILTER_SILENCE = true
{{ end -}}
{{ if .Env.JIGASI_TRANSCRIBER_REMOTE_CONFIG_URL -}}
org.jitsi.jigasi.transcription.remoteTranscriptionConfigUrl={{ .Env.JIGASI_TRANSCRIBER_REMOTE_CONFIG_URL }}
{{ if .Env.JIGASI_TRANSCRIBER_REMOTE_CONFIG_URL_KEY_PATH -}}
org.jitsi.jigasi.transcription.remoteTranscriptionConfigUrl.keyPath={{ .Env.JIGASI_TRANSCRIBER_REMOTE_CONFIG_URL_KEY_PATH }}
{{ end -}}
{{ if .Env.JIGASI_TRANSCRIBER_REMOTE_CONFIG_URL_KEY_ID -}}
org.jitsi.jigasi.transcription.remoteTranscriptionConfigUrl.kid={{ .Env.JIGASI_TRANSCRIBER_REMOTE_CONFIG_URL_KEY_ID }}
{{ end -}}
{{ if .Env.JIGASI_TRANSCRIBER_REMOTE_CONFIG_URL_AUD -}}
org.jitsi.jigasi.transcription.remoteTranscriptionConfigUrl.aud={{ .Env.JIGASI_TRANSCRIBER_REMOTE_CONFIG_URL_AUD }}
{{ end -}}
{{ end -}}
{{ if .Env.JIGASI_TRANSCRIBER_OCI_REGION -}}
org.jitsi.jigasi.transcription.oci.websocketUrl=wss://realtime.aiservice.{{ .Env.JIGASI_TRANSCRIBER_OCI_REGION }}.oci.oraclecloud.com
{{ end -}}
{{ if .Env.JIGASI_TRANSCRIBER_OCI_COMPARTMENT -}}
org.jitsi.jigasi.transcription.oci.compartmentId={{ .Env.JIGASI_TRANSCRIBER_OCI_COMPARTMENT }}
{{ end -}}
{{ if .Env.JIGASI_TRANSCRIBER_WHISPER_URL -}}
org.jitsi.jigasi.transcription.whisper.websocket_url={{ .Env.JIGASI_TRANSCRIBER_WHISPER_URL }}
{{ end -}}
{{ if .Env.JIGASI_TRANSCRIBER_WHISPER_PRIVATE_KEY_NAME -}}
org.jitsi.jigasi.transcription.whisper.private_key_name={{ .Env.JIGASI_TRANSCRIBER_WHISPER_PRIVATE_KEY_NAME }}
{{ end -}}
{{ if .Env.JIGASI_TRANSCRIBER_WHISPER_PRIVATE_KEY -}}
org.jitsi.jigasi.transcription.whisper.private_key={{ .Env.JIGASI_TRANSCRIBER_WHISPER_PRIVATE_KEY }}
{{ end -}}
{{ if .Env.JIGASI_TRANSCRIBER_VOSK_URL -}}
org.jitsi.jigasi.transcription.vosk.websocket_url={{ .Env.JIGASI_TRANSCRIBER_VOSK_URL }}
{{ end -}}
````

## File: jigasi/rootfs/defaults/xmpp-sip-communicator.properties
````
{{ $JIGASI_BREWERY_MUC := .Env.JIGASI_BREWERY_MUC | default "jigasibrewery" -}}
{{ $JIGASI_XMPP_USER := .Env.JIGASI_XMPP_USER | default "jigasi" -}}
{{ $JIGASI_XMPP_PASSWORD := .Env.JIGASI_XMPP_PASSWORD | default "replaceme" -}}
{{ $PUBLIC_URL_DOMAIN := .Env.PUBLIC_URL | default "https://localhost:8443" | trimPrefix "https://" | trimSuffix "/" -}}
{{ $XMPP_AUTH_DOMAIN := .Env.XMPP_AUTH_DOMAIN | default "auth.meet.jitsi" -}}
{{ $XMPP_DOMAIN := .Env.XMPP_DOMAIN | default "meet.jitsi" -}}
{{ $XMPP_INTERNAL_MUC_DOMAIN := .Env.XMPP_INTERNAL_MUC_DOMAIN | default "internal-muc.meet.jitsi" -}}
{{ $XMPP_PORT := .Env.XMPP_PORT | default "5222" -}}
{{ $XMPP_SERVER := .Env.XMPP_SERVER | default "xmpp.meet.jitsi" -}}
{{ $XMPP_SERVERS := splitList "," $XMPP_SERVER | compact -}}

# XMPP account used for control
{{ range $index, $element := $XMPP_SERVERS -}}
{{ $SERVER := splitn ":" 3 $element }}
{{ $ID := $SERVER._2 | default $index }}
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}=acc-{{ $ID }}
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.ACCOUNT_UID=Jabber:{{ $JIGASI_XMPP_USER }}@{{ $XMPP_AUTH_DOMAIN }}@{{ $ID }}
net.java.sip.communicator.plugin.reconnectplugin.ATLEAST_ONE_SUCCESSFUL_CONNECTION.Jabber\:{{ $JIGASI_XMPP_USER }}@{{ $XMPP_AUTH_DOMAIN }}=true
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.USER_ID={{ $JIGASI_XMPP_USER }}@{{ $XMPP_AUTH_DOMAIN }}
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.IS_SERVER_OVERRIDDEN=true
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.SERVER_ADDRESS={{ $SERVER._0 }}
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.SERVER_PORT={{ $SERVER._1 | default $XMPP_PORT }}
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.ALLOW_NON_SECURE=true
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.PASSWORD={{ $JIGASI_XMPP_PASSWORD | b64enc }}
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.AUTO_GENERATE_RESOURCE=true
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.RESOURCE_PRIORITY=30
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.KEEP_ALIVE_METHOD=XEP-0199
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.KEEP_ALIVE_INTERVAL=20
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.CALLING_DISABLED=true
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.JINGLE_NODES_ENABLED=false
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.IS_CARBON_DISABLED=true
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.DEFAULT_ENCRYPTION=true
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.IS_USE_ICE=true
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.IS_ACCOUNT_DISABLED=false
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.IS_PREFERRED_PROTOCOL=false
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.AUTO_DISCOVER_JINGLE_NODES=false
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.PROTOCOL=Jabber
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.IS_USE_UPNP=false
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.IM_DISABLED=true
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.SERVER_STORED_INFO_DISABLED=true
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.IS_FILE_TRANSFER_DISABLED=true
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.USE_DEFAULT_STUN_SERVER=true
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.ENCRYPTION_PROTOCOL.DTLS-SRTP=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.ENCRYPTION_PROTOCOL_STATUS.DTLS-SRTP=true
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.VIDEO_CALLING_DISABLED=true
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.OVERRIDE_ENCODINGS=true
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.G722/8000=705
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.GSM/8000=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.H263-1998/90000=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.H264/90000=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.PCMA/8000=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.PCMU/8000=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.SILK/12000=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.SILK/16000=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.SILK/24000=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.SILK/8000=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.VP8/90000=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.iLBC/8000=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.opus/48000=750
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.speex/16000=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.speex/32000=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.speex/8000=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.Encodings.telephone-event/8000=0
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.BREWERY={{ $JIGASI_BREWERY_MUC }}@{{ $XMPP_INTERNAL_MUC_DOMAIN }}
net.java.sip.communicator.impl.protocol.jabber.acc-{{ $ID }}.DOMAIN_BASE={{ $PUBLIC_URL_DOMAIN }}
{{ end -}}

org.jitsi.jigasi.ALLOWED_JID={{ $JIGASI_BREWERY_MUC }}@{{ $XMPP_INTERNAL_MUC_DOMAIN }}
````

## File: jigasi/rootfs/etc/cont-init.d/10-config
````
#!/usr/bin/with-contenv bash

export SENTRY_RELEASE="${SENTRY_RELEASE:-$(apt-cache policy jigasi | sed -n '/Installed/p' | sed -e 's/[^:]*: //')}"

if [[ -z $JIGASI_XMPP_PASSWORD ]]; then
    echo 'FATAL ERROR: Jigasi auth password must be set'
    exit 1
fi

OLD_JIGASI_XMPP_PASSWORD=passw0rd
if [[ "$JIGASI_XMPP_PASSWORD" == "$OLD_JIGASI_XMPP_PASSWORD" ]]; then
    echo 'FATAL ERROR: Jigasi auth password must be changed, check the README'
    exit 1
fi

[ -z "$JIGASI_MODE" ] && JIGASI_MODE="sip"
JIGASI_MODE="$(echo $JIGASI_MODE | tr '[:upper:]' '[:lower:]')"

if [[ "$JIGASI_MODE" == "transcriber" ]]; then
    # set random jigasi nickname for the instance if is not set
    [ -z "${JIGASI_INSTANCE_ID}" ] && export JIGASI_INSTANCE_ID="transcriber-$(date +%N)"
fi

# set random jigasi nickname for the instance if is not set
[ -z "${JIGASI_INSTANCE_ID}" ] && export JIGASI_INSTANCE_ID="jigasi-$(date +%N)"

# check for AUTOSCALER_URL, AUTOSCALER_SIDECAR_KEY_FILE and AUTOSCALER_SIDECAR_KEY_ID as indicator that sidecar should be enabled
if [ -n "$AUTOSCALER_URL" ]; then
    if [ -z "$AUTOSCALER_SIDECAR_KEY_FILE" ]; then
        export AUTOSCALER_SIDECAR_KEY_FILE="/etc/jitsi/autoscaler-sidecar/asap.pem"
    fi
    if [ -z "$AUTOSCALER_SIDECAR_KEY_ID" ]; then
        # assume key id is equal to the base real path of the key file minus .pem
        export AUTOSCALER_SIDECAR_KEY_ID="$(basename "$(realpath "$AUTOSCALER_SIDECAR_KEY_FILE")" | tr -d '.pem')"
    fi

    if [ -f "$AUTOSCALER_SIDECAR_KEY_FILE" ]; then
        echo "AUTOSCALER_URL found, enabling autoscaler sidecar"

        export JIGASI_VERSION="$(dpkg -s jigasi | grep Version | awk '{print $2}' | sed 's/..$//')"

        [ -z "$AUTOSCALER_SIDECAR_PORT" ] && export AUTOSCALER_SIDECAR_PORT="6000"
        [ -z "$AUTOSCALER_SIDECAR_INSTANCE_ID" ] && export AUTOSCALER_SIDECAR_INSTANCE_ID="$JIGASI_INSTANCE_ID"
        [ -z "$AUTOSCALER_SIDECAR_REGION" ] && export AUTOSCALER_SIDECAR_REGION="docker"
        [ -z "$AUTOSCALER_SIDECAR_GROUP_NAME" ] && export AUTOSCALER_SIDECAR_GROUP_NAME="docker-jigasi"

        mkdir -p /etc/jitsi/autoscaler-sidecar
        tpl /defaults/autoscaler-sidecar.config > /etc/jitsi/autoscaler-sidecar/config
    else
        echo "No key file at $AUTOSCALER_SIDECAR_KEY_FILE, leaving autoscaler sidecar disabled"
    fi
else
    echo "No AUTOSCALER_URL defined, leaving autoscaler sidecar disabled"
fi

# maintain backward compatibility with older variable
[ -z "${XMPP_HIDDEN_DOMAIN}" ] && export XMPP_HIDDEN_DOMAIN="$XMPP_RECORDER_DOMAIN"

tpl /defaults/logging.properties > /config/logging.properties
tpl /defaults/sip-communicator.properties > /config/sip-communicator.properties
tpl /defaults/xmpp-sip-communicator.properties >> /config/sip-communicator.properties

if [[ "$JIGASI_MODE" == "sip" ]]; then
    tpl /defaults/sipserver-sip-communicator.properties >> /config/sip-communicator.properties
elif [[ "$JIGASI_MODE" == "transcriber" ]]; then
    tpl /defaults/transcriber-sip-communicator.properties >> /config/sip-communicator.properties
    mkdir -pm777 /tmp/transcripts
    chown jigasi:jitsi /tmp/transcripts

    # Create Google Cloud Credentials
    if [[ -z $GC_PROJECT_ID || -z $GC_PRIVATE_KEY_ID || -z $GC_PRIVATE_KEY || -z $GC_CLIENT_EMAIL || -z $GC_CLIENT_ID || -z $GC_CLIENT_CERT_URL ]]; then
        echo 'Transcriptions: One or more gcloud environment variables are undefined, skipping gcloud credentials file /config/key.json'
    else
        jq -n \
            --arg GC_PROJECT_ID "$GC_PROJECT_ID" \
            --arg GC_PRIVATE_KEY_ID "$GC_PRIVATE_KEY_ID" \
            --arg GC_PRIVATE_KEY "$GC_PRIVATE_KEY" \
            --arg GC_CLIENT_EMAIL "$GC_CLIENT_EMAIL" \
            --arg GC_CLIENT_ID "$GC_CLIENT_ID" \
            --arg GC_CLIENT_CERT_URL "$GC_CLIENT_CERT_URL" \
            '{
                type: "service_account",
                project_id: $GC_PROJECT_ID,
                private_key_id: $GC_PRIVATE_KEY_ID,
                private_key: $GC_PRIVATE_KEY,
                client_email: $GC_CLIENT_EMAIL,
                client_id: $GC_CLIENT_ID,
                auth_uri: "https://accounts.google.com/o/oauth2/auth",
                token_uri: "https://oauth2.googleapis.com/token",
                auth_provider_x509_cert_url: "https://www.googleapis.com/oauth2/v1/certs",
                client_x509_cert_url: $GC_CLIENT_CERT_URL
            }' \
            > /config/key.json
    fi
fi

if [[ -f /config/custom-sip-communicator.properties ]]; then
    cat /config/custom-sip-communicator.properties >> /config/sip-communicator.properties
fi
if [[ -f /config/custom-logging.properties ]]; then
    cat /config/custom-logging.properties >> /config/logging.properties
fi
````

## File: jigasi/rootfs/etc/services.d/50-autoscaler-sidecar/run
````
#!/usr/bin/with-contenv bash

if [[ -n "$AUTOSCALER_URL" ]] && [[ -f "/etc/jitsi/autoscaler-sidecar/config" ]]; then
    DAEMON="/usr/bin/node /usr/share/jitsi-autoscaler-sidecar/app.js"
    exec s6-setuidgid autoscaler-sidecar /bin/bash -c ". /etc/jitsi/autoscaler-sidecar/config && exec $DAEMON"
else
    # if autoscaler-sidecar should not be started,
    # prevent s6 from restarting this script again and again
    s6-svc -O /var/run/s6/services/50-autoscaler-sidecar
fi
````

## File: jigasi/rootfs/etc/services.d/jigasi/finish
````
#!/usr/bin/with-contenv bash

# When the jigasi is shutdown (or gracefully shutdown), it exits with code 0.
# In this case, we don't want S6 to restart the service. We want to stop all
# services and shutdown the container.

if [[ $1 -eq 0 ]]; then
  /opt/jitsi/shutdown.sh
fi
````

## File: jigasi/rootfs/etc/services.d/jigasi/run
````
#!/usr/bin/with-contenv bash

JAVA_SYS_PROPS="-Djava.util.logging.config.file=/config/logging.properties"

DAEMON=/usr/share/jigasi/jigasi.sh
DAEMON_OPTS="--nocomponent=true --configdir=/ --configdirname=config --min-port=${JIGASI_PORT_MIN:-20000} --max-port=${JIGASI_PORT_MAX:-20050}"

JIGASI_CMD="JAVA_SYS_PROPS=\"$JAVA_SYS_PROPS\" exec $DAEMON $DAEMON_OPTS"
[ -n "$JIGASI_LOG_FILE" ] && JIGASI_CMD="$JIGASI_CMD 2>&1 | tee $JIGASI_LOG_FILE"

exec s6-setuidgid jigasi /bin/bash -c "$JIGASI_CMD"
````

## File: jigasi/rootfs/opt/jitsi/shutdown.sh
````bash
#!/usr/bin/with-contenv bash

if [ -n "$AUTOSCALER_URL" ]; then
    # notify the sidecar of imminent shutdown
    PORT=${AUTOSCALER_SIDECAR_PORT:-6000}
    curl -d '{}' -v 0:$PORT/hook/v1/shutdown
    sleep 10
fi

# shutdown everything
s6-svscanctl -t /var/run/s6/services
````

## File: jigasi/rootfs/usr/local/bin/healthcheck.sh
````bash
#!/bin/bash

curl --fail-with-body http://127.0.0.1:8788/about/health
````

## File: jigasi/Dockerfile
````
ARG JITSI_REPO=jitsi
ARG BASE_TAG=latest
FROM ${JITSI_REPO}/base-java:${BASE_TAG}

LABEL org.opencontainers.image.title="Jitsi Gateway to SIP (jigasi)"
LABEL org.opencontainers.image.description="Server-side application that allows regular SIP clients to join conferences."
LABEL org.opencontainers.image.url="https://github.com/jitsi/jigasi"
LABEL org.opencontainers.image.source="https://github.com/jitsi/docker-jitsi-meet"
LABEL org.opencontainers.image.documentation="https://jitsi.github.io/handbook/"

ENV GOOGLE_APPLICATION_CREDENTIALS /config/key.json

RUN apt-dpkg-wrap apt-get update && \
    apt-dpkg-wrap apt-get install -y jigasi jq jitsi-autoscaler-sidecar && \
    apt-cleanup

COPY rootfs/ /

VOLUME ["/config", "/tmp/transcripts"]
````

## File: jvb/rootfs/defaults/autoscaler-sidecar.config
````
{{ $JVB_COLIBRI_PORT := .Env.JVB_COLIBRI_PORT | default "8080" -}}
{{ $SHUTDOWN_POLLING_INTERVAL := .Env.AUTOSCALER_SIDECAR_SHUTDOWN_POLLING_INTERVAL | default "60" -}}
{{ $STATS_POLLING_INTERVAL := .Env.AUTOSCALER_SIDECAR_STATS_POLLING_INTERVAL | default "30" -}}
export SHUTDOWN_POLLING_INTERVAL={{ $SHUTDOWN_POLLING_INTERVAL }}
export STATS_POLLING_INTERVAL={{ $STATS_POLLING_INTERVAL }}
export PORT={{ .Env.AUTOSCALER_SIDECAR_PORT }}
export GRACEFUL_SHUTDOWN_SCRIPT="/usr/share/jitsi-videobridge/graceful_shutdown.sh"
export TERMINATE_SCRIPT="/opt/jitsi/shutdown.sh"
export ENABLE_REPORT_STATS=true
export POLLING_URL="{{ .Env.AUTOSCALER_URL  }}/sidecar/poll"
export STATUS_URL="{{ .Env.AUTOSCALER_URL }}/sidecar/status"
export SHUTDOWN_URL="{{ .Env.AUTOSCALER_URL }}/sidecar/shutdown"
export STATS_RETRIEVE_URL="http://localhost:{{ $JVB_COLIBRI_PORT }}/colibri/stats"
export STATS_REPORT_URL="{{ .Env.AUTOSCALER_URL }}/sidecar/stats"
export ASAP_SIGNING_KEY_FILE="{{ .Env.AUTOSCALER_SIDECAR_KEY_FILE }}"
export ASAP_JWT_KID="{{ .Env.AUTOSCALER_SIDECAR_KEY_ID }}"
export INSTANCE_TYPE="JVB"
export INSTANCE_ID="{{ .Env.AUTOSCALER_SIDECAR_INSTANCE_ID }}"
export INSTANCE_METADATA='{"environment":"{{ .Env.XMPP_ENV_NAME }}","region":"{{ .Env.AUTOSCALER_SIDECAR_REGION }}","group":"{{ .Env.AUTOSCALER_SIDECAR_GROUP_NAME }}","name":"{{ .Env.JVB_INSTANCE_ID }}","version":"{{ .Env.JVB_VERSION }}","privateIp":"{{ .Env.LOCAL_ADDRESS }}","publicIp":"{{ .Env.JVB_ADVERTISE_IPS }}","hostId":"{{ .Env.AUTOSCALER_SIDECAR_HOST_ID }}"}'
````

## File: jvb/rootfs/defaults/jvb.conf
````
{{ $COLIBRI_REST_ENABLED := .Env.COLIBRI_REST_ENABLED | default "false" | toBool -}}
{{ $DISABLE_XMPP := .Env.JVB_DISABLE_XMPP | default "0" | toBool -}}
{{ $ENABLE_COLIBRI_WEBSOCKET := .Env.ENABLE_COLIBRI_WEBSOCKET | default "0" | toBool -}}
{{ $ENABLE_OCTO := .Env.ENABLE_OCTO | default "0" | toBool -}}
{{ $ENABLE_SCTP := .Env.ENABLE_SCTP | default "1" | toBool -}}
{{ $ENABLE_JVB_XMPP_SERVER := .Env.ENABLE_JVB_XMPP_SERVER | default "0" | toBool }}
{{ $JVB_DISABLE_STUN := .Env.JVB_DISABLE_STUN | default "0" | toBool -}}
{{ $JVB_STUN_SERVERS := .Env.JVB_STUN_SERVERS | default "meet-jit-si-turnrelay.jitsi.net:443" -}}
{{ $JVB_AUTH_USER := .Env.JVB_AUTH_USER | default "jvb" -}}
{{ $JVB_BREWERY_MUC := .Env.JVB_BREWERY_MUC | default "jvbbrewery" -}}
{{ $JVB_CC_TRUST_BWE := .Env.JVB_CC_TRUST_BWE | default "true" | toBool -}}
{{ $JVB_MUC_NICKNAME := .Env.JVB_MUC_NICKNAME | default .Env.HOSTNAME -}}
{{ $JVB_ADVERTISE_PRIVATE_CANDIDATES := .Env.JVB_ADVERTISE_PRIVATE_CANDIDATES | default "true" | toBool -}}
{{ $JVB_ADVERTISE_IPS := .Env.JVB_ADVERTISE_IPS | default "" -}}
{{ $JVB_IPS := splitList "," $JVB_ADVERTISE_IPS | compact -}}
{{ $JVB_REQUIRE_VALID_ADDRESS := .Env.JVB_REQUIRE_VALID_ADDRESS | default "0" | toBool -}}
{{ $JVB_XMPP_AUTH_DOMAIN := .Env.JVB_XMPP_AUTH_DOMAIN | default "auth.jvb.meet.jitsi" -}}
{{ $JVB_XMPP_INTERNAL_MUC_DOMAIN := .Env.JVB_XMPP_INTERNAL_MUC_DOMAIN | default "muc.jvb.meet.jitsi" -}}
{{ $JVB_XMPP_PORT := .Env.JVB_XMPP_PORT | default "6222" -}}
{{ $JVB_XMPP_SERVER := .Env.JVB_XMPP_SERVER | default "xmpp.jvb.meet.jitsi" -}}
{{ $JVB_XMPP_SERVERS := splitList "," $JVB_XMPP_SERVER | compact -}}
{{ $PUBLIC_URL_DOMAIN := .Env.PUBLIC_URL | default "https://localhost:8443" | trimPrefix "https://" | trimSuffix "/" -}}
{{ $SHUTDOWN_REST_ENABLED := .Env.SHUTDOWN_REST_ENABLED | default "false" | toBool -}}
{{ $USE_USRSCTP := .Env.JVB_USE_USRSCTP | default "false" | toBool -}}
{{ $WS_DOMAIN := .Env.JVB_WS_DOMAIN | default $PUBLIC_URL_DOMAIN -}}
{{ $WS_SERVER_ID := .Env.JVB_WS_SERVER_ID | default .Env.JVB_WS_SERVER_ID_FALLBACK | default "default" -}}
{{ $WS_TLS := .Env.JVB_WS_TLS | default "1" | toBool -}}
{{ $XMPP_AUTH_DOMAIN := .Env.XMPP_AUTH_DOMAIN | default "auth.meet.jitsi" -}}
{{ $XMPP_INTERNAL_MUC_DOMAIN := .Env.XMPP_INTERNAL_MUC_DOMAIN | default "internal-muc.meet.jitsi" -}}
{{ $XMPP_PORT := .Env.XMPP_PORT | default "5222" -}}
{{ $XMPP_SERVER := .Env.XMPP_SERVER | default "xmpp.meet.jitsi" -}}
{{ $XMPP_SERVERS := splitList "," $XMPP_SERVER | compact -}}
{{/* assign env from context, preserve during range when . is re-assigned */}}
{{ $ENV := .Env -}}

videobridge {
    cc {
        use-vla-target-bitrate = {{ .Env.ENABLE_VLA | default "0" | toBool }}
        trust-bwe = {{ $JVB_CC_TRUST_BWE }}
    }
    ice {
        udp {
            port = {{ .Env.JVB_PORT | default 10000 }}
        }
        advertise-private-candidates = {{ $JVB_ADVERTISE_PRIVATE_CANDIDATES }}
    }
    apis {
{{ if not $DISABLE_XMPP -}}
        xmpp-client {
            configs {
{{ if $ENABLE_JVB_XMPP_SERVER }}
{{ range $index, $element := $JVB_XMPP_SERVERS -}}
{{ $SERVER := splitn ":" 2 $element }}
                shard{{ $index }} {
                    HOSTNAME = "{{ $SERVER._0 }}"
                    PORT = "{{ $SERVER._1 | default $JVB_XMPP_PORT }}"
                    DOMAIN = "{{ $JVB_XMPP_AUTH_DOMAIN }}"
                    USERNAME = "{{ $JVB_AUTH_USER }}"
                    PASSWORD = "{{ $ENV.JVB_AUTH_PASSWORD }}"
                    MUC_JIDS = "{{ $JVB_BREWERY_MUC }}@{{ $JVB_XMPP_INTERNAL_MUC_DOMAIN }}"
                    MUC_NICKNAME = "{{ $JVB_MUC_NICKNAME }}"
                    DISABLE_CERTIFICATE_VERIFICATION = true
                }
{{ end -}}
{{ else }}
{{ range $index, $element := $XMPP_SERVERS -}}
{{ $SERVER := splitn ":" 2 $element }}
                shard{{ $index }} {
                    HOSTNAME = "{{ $SERVER._0 }}"
                    PORT = "{{ $SERVER._1 | default $XMPP_PORT }}"
                    DOMAIN = "{{ $XMPP_AUTH_DOMAIN }}"
                    USERNAME = "{{ $JVB_AUTH_USER }}"
                    PASSWORD = "{{ $ENV.JVB_AUTH_PASSWORD }}"
                    MUC_JIDS = "{{ $JVB_BREWERY_MUC }}@{{ $XMPP_INTERNAL_MUC_DOMAIN }}"
                    MUC_NICKNAME = "{{ $JVB_MUC_NICKNAME }}"
                    DISABLE_CERTIFICATE_VERIFICATION = true
                }
{{ end -}}
{{ end -}}
            }
        }
{{ end -}}
        rest {
            enabled = {{ $COLIBRI_REST_ENABLED }}
        }
    }
    rest {
        shutdown {
            enabled = {{ $SHUTDOWN_REST_ENABLED }}
        }
    }
    sctp {
      enabled = {{ $ENABLE_SCTP }}
      use-usrsctp = {{ $USE_USRSCTP }}
    }
    stats {
        enabled = true
    }
    websockets {
        enabled = {{ $ENABLE_COLIBRI_WEBSOCKET }}
        domain = "{{ $WS_DOMAIN }}"
        tls = {{ $WS_TLS }}
        server-id = "{{ $WS_SERVER_ID }}"
    }
    http-servers {
        private {
          host = 0.0.0.0
          send-server-version = false
        }
        public {
            host = 0.0.0.0
            port = 9090
            send-server-version = false
        }
    }
    health {
        require-valid-address = {{ $JVB_REQUIRE_VALID_ADDRESS }}
    }

    {{ if $ENABLE_OCTO -}}
    relay {
        enabled = true
        region = "{{ .Env.JVB_OCTO_REGION | default "europe" }}"
        relay-id = "{{ .Env.JVB_OCTO_RELAY_ID | default .Env.JVB_OCTO_BIND_ADDRESS }}"
    }
    {{ end -}}
}

ice4j {
    harvest {
        mapping {
            stun {
{{ if not $JVB_DISABLE_STUN -}}
                addresses = [ "{{ join "\",\"" (splitList "," $JVB_STUN_SERVERS) }}" ]
{{ else -}}
                enabled = false
{{ end -}}
            }
            static-mappings = [
{{ range $index, $element := $JVB_IPS -}}
                {
                    local-address = "{{ $ENV.LOCAL_ADDRESS }}"
                    public-address = "{{ $element }}"
                    name = "ip-{{ $index }}"
                },
{{ end -}}
            ]
        }
    }
}

include "custom-jvb.conf"
````

## File: jvb/rootfs/defaults/logging.properties
````
{{ if .Env.SENTRY_DSN | toBool }}
handlers=java.util.logging.ConsoleHandler,io.sentry.jul.SentryHandler
{{ else }}
handlers= java.util.logging.ConsoleHandler
{{ end }}

java.util.logging.ConsoleHandler.level = ALL
java.util.logging.ConsoleHandler.formatter = org.jitsi.utils.logging2.JitsiLogFormatter
org.jitsi.utils.logging2.JitsiLogFormatter.programname=JVB

.level=INFO
io.sentry.jul.SentryHandler.level=WARNING
````

## File: jvb/rootfs/etc/cont-init.d/10-config
````
#!/usr/bin/with-contenv bash

if [[ -z $JVB_DISABLE_XMPP ]]; then
    if [[ -z $JVB_AUTH_PASSWORD ]]; then
        echo 'FATAL ERROR: JVB auth password must be set'
        exit 1
    fi

    OLD_JVB_AUTH_PASSWORD=passw0rd
    if [[ "$JVB_AUTH_PASSWORD" == "$OLD_JVB_AUTH_PASSWORD" ]]; then
        echo 'FATAL ERROR: JVB auth password must be changed, check the README'
        exit 1
    fi

    [ -z "${XMPP_SERVER}" ] && export XMPP_SERVER=xmpp.meet.jitsi

    # On environments like Swarm the IP address used by the default gateway need not be
    # the one used for inter-container traffic. Use that one for our fallback ID.
    XMPP_SERVER_IP=$(dig +short +search ${XMPP_SERVER})
    export JVB_WS_SERVER_ID_FALLBACK=$(ip route get ${XMPP_SERVER_IP} | grep -oP '(?<=src ).*' | awk '{ print $1 '})
fi

# Migration from DOCKER_HOST_ADDRESS to JVB_ADVERTISE_IPS
if [[ -z "${JVB_ADVERTISE_IPS}" ]]; then
    if [[ ! -z "${DOCKER_HOST_ADDRESS}" ]]; then
        echo "WARNING: DOCKER_HOST_ADDRESS is deprecated, migrate to JVB_ADVERTISE_IPS"
        export JVB_ADVERTISE_IPS=${DOCKER_HOST_ADDRESS}
    fi
fi

# Local IP for the ice4j mapping harvester.
export LOCAL_ADDRESS=$(ip route get 1 | grep -oP '(?<=src ).*' | awk '{ print $1 '})

export SENTRY_RELEASE="${SENTRY_RELEASE:-$(apt-cache policy jitsi-videobridge2 | sed -n '/Installed/p' | sed -e 's/[^:]*: //')}"

if [[ -f /config/custom-sip-communicator.properties ]]; then
    cat /config/custom-sip-communicator.properties > /config/sip-communicator.properties
fi

# set random jvb nickname for the instance if is not set
[ -z "${JVB_INSTANCE_ID}" ] && export JVB_INSTANCE_ID="jvb-$(date +%N)"

# check for AUTOSCALER_URL, AUTOSCALER_SIDECAR_KEY_FILE and AUTOSCALER_SIDECAR_KEY_ID as indicator that sidecar should be enabled
if [ -n "$AUTOSCALER_URL" ]; then
    if [ -z "$AUTOSCALER_SIDECAR_KEY_FILE" ]; then
        export AUTOSCALER_SIDECAR_KEY_FILE="/etc/jitsi/autoscaler-sidecar/asap.pem"
    fi
    if [ -z "$AUTOSCALER_SIDECAR_KEY_ID" ]; then
        # assume key id is equal to the base real path of the key file minus .pem
        export AUTOSCALER_SIDECAR_KEY_ID="$(basename "$(realpath "$AUTOSCALER_SIDECAR_KEY_FILE")" | tr -d '.pem')"
    fi

    if [ -f "$AUTOSCALER_SIDECAR_KEY_FILE" ]; then
        echo "AUTOSCALER_URL found, enabling autoscaler sidecar"

        export JVB_VERSION="$(dpkg -s jitsi-videobridge2 | grep Version | awk '{print $2}' | sed 's/..$//')"

        [ -z "$AUTOSCALER_SIDECAR_PORT" ] && export AUTOSCALER_SIDECAR_PORT="6000"
        [ -z "$JIBRI_WEBHOOK_SUBSCRIBERS" ] && export JIBRI_WEBHOOK_SUBSCRIBERS="http://localhost:$AUTOSCALER_SIDECAR_PORT/hook"
        [ -z "$AUTOSCALER_SIDECAR_INSTANCE_ID" ] && export AUTOSCALER_SIDECAR_INSTANCE_ID="$JVB_INSTANCE_ID"
        [ -z "$AUTOSCALER_SIDECAR_REGION" ] && export AUTOSCALER_SIDECAR_REGION="docker"
        [ -z "$AUTOSCALER_SIDECAR_GROUP_NAME" ] && export AUTOSCALER_SIDECAR_GROUP_NAME="docker-jvb"

        mkdir -p /etc/jitsi/autoscaler-sidecar
        tpl /defaults/autoscaler-sidecar.config > /etc/jitsi/autoscaler-sidecar/config
    else
        echo "No key file at $AUTOSCALER_SIDECAR_KEY_FILE, leaving autoscaler sidecar disabled"
    fi
else
    echo "No AUTOSCALER_URL defined, leaving autoscaler sidecar disabled"
fi

tpl /defaults/logging.properties > /config/logging.properties
tpl /defaults/jvb.conf > /config/jvb.conf

chown -R jvb:jitsi /config
````

## File: jvb/rootfs/etc/services.d/50-autoscaler-sidecar/run
````
#!/usr/bin/with-contenv bash

if [[ -n "$AUTOSCALER_URL" ]] && [[ -f "/etc/jitsi/autoscaler-sidecar/config" ]]; then
    DAEMON="/usr/bin/node /usr/share/jitsi-autoscaler-sidecar/app.js"
    exec s6-setuidgid autoscaler-sidecar /bin/bash -c ". /etc/jitsi/autoscaler-sidecar/config && exec $DAEMON"
else
    # if autoscaler-sidecar should not be started,
    # prevent s6 from restarting this script again and again
    s6-svc -O /var/run/s6/services/50-autoscaler-sidecar
fi
````

## File: jvb/rootfs/etc/services.d/jvb/finish
````
#!/usr/bin/with-contenv bash

# When the jvb is shutdown (or gracefully shutdown), it exits with code 0.
# In this case, we don't want S6 to restart the service. We want to stop all
# services and shutdown the container.

if [[ $1 -eq 0 ]]; then
  /opt/jitsi/shutdown.sh
fi
````

## File: jvb/rootfs/etc/services.d/jvb/run
````
#!/usr/bin/with-contenv bash

export JAVA_SYS_PROPS="-Dnet.java.sip.communicator.SC_HOME_DIR_LOCATION=/ -Dnet.java.sip.communicator.SC_HOME_DIR_NAME=config -Djava.util.logging.config.file=/config/logging.properties -Dconfig.file=/config/jvb.conf"

DAEMON=/usr/share/jitsi-videobridge/jvb.sh

JVB_CMD="exec $DAEMON"
[ -n "$JVB_LOG_FILE" ] && JVB_CMD="$JVB_CMD 2>&1 | tee $JVB_LOG_FILE"

exec s6-setuidgid jvb /bin/bash -c "$JVB_CMD"
````

## File: jvb/rootfs/opt/jitsi/shutdown.sh
````bash
#!/usr/bin/with-contenv bash

if [ -n "$AUTOSCALER_URL" ]; then
    # notify the sidecar of imminent shutdown
    PORT=${AUTOSCALER_SIDECAR_PORT:-6000}
    curl -d '{}' -v 0:$PORT/hook/v1/shutdown
    sleep 10
fi

# shutdown everything
s6-svscanctl -t /var/run/s6/services
````

## File: jvb/rootfs/usr/local/bin/healthcheck.sh
````bash
#!/bin/bash

curl --fail-with-body http://127.0.0.1:8080/about/health
````

## File: jvb/Dockerfile
````
ARG JITSI_REPO=jitsi
ARG BASE_TAG=latest
FROM ${JITSI_REPO}/base-java:${BASE_TAG}

LABEL org.opencontainers.image.title="Jitsi Videobridge (jvb)"
LABEL org.opencontainers.image.description="WebRTC compatible server designed to route video streams amongst participants in a conference."
LABEL org.opencontainers.image.url="https://jitsi.org/jitsi-videobridge/"
LABEL org.opencontainers.image.source="https://github.com/jitsi/docker-jitsi-meet"
LABEL org.opencontainers.image.documentation="https://jitsi.github.io/handbook/"

RUN apt-dpkg-wrap apt-get update && \
    apt-dpkg-wrap apt-get install -y jitsi-videobridge2 jitsi-autoscaler-sidecar jq curl iproute2 dnsutils libpcap0.8 && \
    apt-cleanup

COPY rootfs/ /

VOLUME /config
````

## File: log-analyser/grafana-provisioning/dashboards/dashboards.yml
````yaml
apiVersion: 1

providers:
- name: 'default'
  orgId: 1
  folder: ''
  type: file
  disableDeletion: false
  editable: true
  options:
    path: /etc/grafana/provisioning/dashboards
````

## File: log-analyser/grafana-provisioning/dashboards/docker-statistics.json
````json
{
    "annotations": {
      "list": [
        {
          "builtIn": 1,
          "datasource": {
            "type": "grafana",
            "uid": "-- Grafana --"
          },
          "enable": true,
          "hide": true,
          "iconColor": "rgba(0, 211, 255, 1)",
          "name": "Annotations & Alerts",
          "type": "dashboard"
        }
      ]
    },
    "editable": true,
    "fiscalYearStartMonth": 0,
    "graphTooltip": 0,
    "links": [],
    "liveNow": false,
    "panels": [
      {
        "datasource": {
          "type": "prometheus",
          "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
        },
        "fieldConfig": {
          "defaults": {
            "color": {
              "mode": "thresholds"
            },
            "mappings": [],
            "thresholds": {
              "mode": "absolute",
              "steps": [
                {
                  "color": "green",
                  "value": null
                },
                {
                  "color": "red",
                  "value": 80
                }
              ]
            }
          },
          "overrides": []
        },
        "gridPos": {
          "h": 8,
          "w": 7,
          "x": 0,
          "y": 0
        },
        "id": 4,
        "options": {
          "minVizHeight": 75,
          "minVizWidth": 75,
          "orientation": "auto",
          "reduceOptions": {
            "calcs": [
              "lastNotNull"
            ],
            "fields": "",
            "values": false
          },
          "showThresholdLabels": false,
          "showThresholdMarkers": true
        },
        "pluginVersion": "10.2.0",
        "targets": [
          {
            "datasource": {
              "type": "prometheus",
              "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
            },
            "editorMode": "code",
            "expr": "container_memory_percent_ratio * 100",
            "instant": false,
            "legendFormat": "Memory Usage (in %)",
            "range": true,
            "refId": "A"
          }
        ],
        "title": "Memory Usage",
        "type": "gauge"
      },
      {
        "datasource": {
          "type": "prometheus",
          "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
        },
        "fieldConfig": {
          "defaults": {
            "color": {
              "mode": "thresholds"
            },
            "mappings": [],
            "thresholds": {
              "mode": "absolute",
              "steps": [
                {
                  "color": "green",
                  "value": null
                },
                {
                  "color": "red",
                  "value": 80
                }
              ]
            }
          },
          "overrides": []
        },
        "gridPos": {
          "h": 8,
          "w": 7,
          "x": 7,
          "y": 0
        },
        "id": 2,
        "options": {
          "minVizHeight": 75,
          "minVizWidth": 75,
          "orientation": "auto",
          "reduceOptions": {
            "calcs": [
              "lastNotNull"
            ],
            "fields": "",
            "values": false
          },
          "showThresholdLabels": false,
          "showThresholdMarkers": true
        },
        "pluginVersion": "10.2.0",
        "targets": [
          {
            "datasource": {
              "type": "prometheus",
              "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
            },
            "editorMode": "code",
            "exemplar": false,
            "expr": "container_cpu_utilization_ratio * 100",
            "format": "time_series",
            "instant": false,
            "legendFormat": "CPU Utilization (in %)",
            "range": true,
            "refId": "A"
          }
        ],
        "title": "CPU Utilization",
        "type": "gauge"
      },
      {
        "datasource": {
          "type": "prometheus",
          "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
        },
        "fieldConfig": {
          "defaults": {
            "color": {
              "mode": "palette-classic"
            },
            "custom": {
              "axisBorderShow": false,
              "axisCenteredZero": false,
              "axisColorMode": "text",
              "axisLabel": "",
              "axisPlacement": "auto",
              "barAlignment": 0,
              "drawStyle": "line",
              "fillOpacity": 0,
              "gradientMode": "none",
              "hideFrom": {
                "legend": false,
                "tooltip": false,
                "viz": false
              },
              "insertNulls": false,
              "lineInterpolation": "linear",
              "lineWidth": 1,
              "pointSize": 5,
              "scaleDistribution": {
                "type": "linear"
              },
              "showPoints": "auto",
              "spanNulls": false,
              "stacking": {
                "group": "A",
                "mode": "none"
              },
              "thresholdsStyle": {
                "mode": "off"
              }
            },
            "mappings": [],
            "thresholds": {
              "mode": "absolute",
              "steps": [
                {
                  "color": "green",
                  "value": null
                },
                {
                  "color": "red",
                  "value": 80
                }
              ]
            }
          },
          "overrides": []
        },
        "gridPos": {
          "h": 8,
          "w": 10,
          "x": 14,
          "y": 0
        },
        "id": 1,
        "options": {
          "legend": {
            "calcs": [],
            "displayMode": "list",
            "placement": "bottom",
            "showLegend": true
          },
          "tooltip": {
            "mode": "single",
            "sort": "none"
          }
        },
        "targets": [
          {
            "datasource": {
              "type": "prometheus",
              "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
            },
            "editorMode": "code",
            "expr": "sum(rate(container_blockio_io_service_bytes_recursive_total{operation=\"read\"}[5m])) ",
            "hide": false,
            "instant": false,
            "legendFormat": "Read Operation",
            "range": true,
            "refId": "B"
          },
          {
            "datasource": {
              "type": "prometheus",
              "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
            },
            "editorMode": "code",
            "expr": "sum(rate(container_blockio_io_service_bytes_recursive_total{operation=\"write\"}[5m])) ",
            "hide": false,
            "instant": false,
            "legendFormat": "Write Operation",
            "range": true,
            "refId": "C"
          }
        ],
        "title": "Block IO in bytes",
        "type": "timeseries"
      },
      {
        "datasource": {
          "type": "prometheus",
          "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
        },
        "fieldConfig": {
          "defaults": {
            "color": {
              "mode": "palette-classic"
            },
            "custom": {
              "axisBorderShow": false,
              "axisCenteredZero": false,
              "axisColorMode": "text",
              "axisLabel": "",
              "axisPlacement": "auto",
              "barAlignment": 0,
              "drawStyle": "line",
              "fillOpacity": 0,
              "gradientMode": "none",
              "hideFrom": {
                "legend": false,
                "tooltip": false,
                "viz": false
              },
              "insertNulls": false,
              "lineInterpolation": "linear",
              "lineWidth": 1,
              "pointSize": 5,
              "scaleDistribution": {
                "type": "linear"
              },
              "showPoints": "auto",
              "spanNulls": false,
              "stacking": {
                "group": "A",
                "mode": "none"
              },
              "thresholdsStyle": {
                "mode": "off"
              }
            },
            "mappings": [],
            "thresholds": {
              "mode": "absolute",
              "steps": [
                {
                  "color": "green",
                  "value": null
                },
                {
                  "color": "red",
                  "value": 80
                }
              ]
            }
          },
          "overrides": []
        },
        "gridPos": {
          "h": 8,
          "w": 12,
          "x": 0,
          "y": 8
        },
        "id": 3,
        "options": {
          "legend": {
            "calcs": [],
            "displayMode": "table",
            "placement": "bottom",
            "showLegend": true
          },
          "tooltip": {
            "mode": "single",
            "sort": "none"
          }
        },
        "pluginVersion": "10.2.0",
        "targets": [
          {
            "datasource": {
              "type": "prometheus",
              "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
            },
            "editorMode": "code",
            "expr": "rate(container_cpu_usage_kernelmode_nanoseconds_total[5m])",
            "instant": false,
            "legendFormat": "Kernel Mode",
            "range": true,
            "refId": "A"
          },
          {
            "datasource": {
              "type": "prometheus",
              "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
            },
            "editorMode": "code",
            "expr": "rate(container_cpu_usage_usermode_nanoseconds_total[5m])",
            "hide": false,
            "instant": false,
            "legendFormat": "User Mode",
            "range": true,
            "refId": "B"
          }
        ],
        "title": "CPU usage in different modes",
        "type": "timeseries"
      },
      {
        "datasource": {
          "type": "prometheus",
          "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
        },
        "fieldConfig": {
          "defaults": {
            "color": {
              "mode": "palette-classic"
            },
            "custom": {
              "axisBorderShow": false,
              "axisCenteredZero": false,
              "axisColorMode": "text",
              "axisLabel": "",
              "axisPlacement": "auto",
              "barAlignment": 0,
              "drawStyle": "line",
              "fillOpacity": 0,
              "gradientMode": "none",
              "hideFrom": {
                "legend": false,
                "tooltip": false,
                "viz": false
              },
              "insertNulls": false,
              "lineInterpolation": "linear",
              "lineWidth": 1,
              "pointSize": 5,
              "scaleDistribution": {
                "type": "linear"
              },
              "showPoints": "auto",
              "spanNulls": false,
              "stacking": {
                "group": "A",
                "mode": "none"
              },
              "thresholdsStyle": {
                "mode": "off"
              }
            },
            "mappings": [],
            "thresholds": {
              "mode": "absolute",
              "steps": [
                {
                  "color": "green",
                  "value": null
                },
                {
                  "color": "red",
                  "value": 80
                }
              ]
            }
          },
          "overrides": []
        },
        "gridPos": {
          "h": 8,
          "w": 12,
          "x": 12,
          "y": 8
        },
        "id": 5,
        "options": {
          "legend": {
            "calcs": [],
            "displayMode": "list",
            "placement": "bottom",
            "showLegend": true
          },
          "tooltip": {
            "mode": "single",
            "sort": "none"
          }
        },
        "pluginVersion": "10.2.0",
        "targets": [
          {
            "datasource": {
              "type": "prometheus",
              "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
            },
            "editorMode": "code",
            "expr": "(container_memory_usage_total_bytes / container_memory_usage_limit_bytes) * 100",
            "instant": false,
            "legendFormat": "Total Bytes Read/Written",
            "range": true,
            "refId": "A"
          }
        ],
        "title": "Container memory usage vs limit",
        "type": "timeseries"
      },
      {
        "datasource": {
          "type": "prometheus",
          "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
        },
        "fieldConfig": {
          "defaults": {
            "color": {
              "mode": "palette-classic"
            },
            "custom": {
              "axisBorderShow": false,
              "axisCenteredZero": false,
              "axisColorMode": "text",
              "axisLabel": "",
              "axisPlacement": "auto",
              "barAlignment": 0,
              "drawStyle": "line",
              "fillOpacity": 0,
              "gradientMode": "none",
              "hideFrom": {
                "legend": false,
                "tooltip": false,
                "viz": false
              },
              "insertNulls": false,
              "lineInterpolation": "linear",
              "lineWidth": 1,
              "pointSize": 5,
              "scaleDistribution": {
                "type": "linear"
              },
              "showPoints": "auto",
              "spanNulls": false,
              "stacking": {
                "group": "A",
                "mode": "none"
              },
              "thresholdsStyle": {
                "mode": "off"
              }
            },
            "mappings": [],
            "thresholds": {
              "mode": "absolute",
              "steps": [
                {
                  "color": "green",
                  "value": null
                },
                {
                  "color": "red",
                  "value": 80
                }
              ]
            }
          },
          "overrides": []
        },
        "gridPos": {
          "h": 8,
          "w": 12,
          "x": 0,
          "y": 16
        },
        "id": 6,
        "options": {
          "legend": {
            "calcs": [],
            "displayMode": "list",
            "placement": "bottom",
            "showLegend": true
          },
          "tooltip": {
            "mode": "single",
            "sort": "none"
          }
        },
        "targets": [
          {
            "datasource": {
              "type": "prometheus",
              "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
            },
            "editorMode": "code",
            "exemplar": false,
            "expr": "rate(container_network_io_usage_rx_bytes_total{interface=\"eth0\"}[5m])",
            "instant": false,
            "legendFormat": "Network bytes received",
            "range": true,
            "refId": "A"
          },
          {
            "datasource": {
              "type": "prometheus",
              "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
            },
            "editorMode": "code",
            "expr": "rate(container_network_io_usage_tx_bytes_total{interface=\"eth0\"}[5m])",
            "hide": false,
            "instant": false,
            "legendFormat": "Network bytes Sent",
            "range": true,
            "refId": "B"
          }
        ],
        "title": "Network bytes sent and received",
        "type": "timeseries"
      },
      {
        "datasource": {
          "type": "prometheus",
          "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
        },
        "fieldConfig": {
          "defaults": {
            "color": {
              "mode": "palette-classic"
            },
            "custom": {
              "axisBorderShow": false,
              "axisCenteredZero": false,
              "axisColorMode": "text",
              "axisLabel": "",
              "axisPlacement": "auto",
              "barAlignment": 0,
              "drawStyle": "line",
              "fillOpacity": 0,
              "gradientMode": "none",
              "hideFrom": {
                "legend": false,
                "tooltip": false,
                "viz": false
              },
              "insertNulls": false,
              "lineInterpolation": "linear",
              "lineWidth": 1,
              "pointSize": 5,
              "scaleDistribution": {
                "type": "linear"
              },
              "showPoints": "auto",
              "spanNulls": false,
              "stacking": {
                "group": "A",
                "mode": "none"
              },
              "thresholdsStyle": {
                "mode": "off"
              }
            },
            "mappings": [],
            "thresholds": {
              "mode": "absolute",
              "steps": [
                {
                  "color": "green",
                  "value": null
                },
                {
                  "color": "red",
                  "value": 80
                }
              ]
            }
          },
          "overrides": []
        },
        "gridPos": {
          "h": 8,
          "w": 12,
          "x": 12,
          "y": 16
        },
        "id": 7,
        "options": {
          "legend": {
            "calcs": [],
            "displayMode": "list",
            "placement": "bottom",
            "showLegend": true
          },
          "tooltip": {
            "mode": "single",
            "sort": "none"
          }
        },
        "targets": [
          {
            "datasource": {
              "type": "prometheus",
              "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
            },
            "editorMode": "code",
            "expr": "rate(container_network_io_usage_rx_dropped_total{interface=\"eth0\"}[5m])",
            "instant": false,
            "legendFormat": "Network Bytes Dropped in Receiving",
            "range": true,
            "refId": "A"
          },
          {
            "datasource": {
              "type": "prometheus",
              "uid": "d301145e-8c4e-4027-bf6e-43e81f095020"
            },
            "editorMode": "code",
            "expr": "rate(container_network_io_usage_tx_dropped_total{interface=\"eth0\"}[5m])",
            "hide": false,
            "instant": false,
            "legendFormat": "Network Bytes dropped in Sending",
            "range": true,
            "refId": "B"
          }
        ],
        "title": "Network Packets dropped",
        "type": "timeseries"
      }
    ],
    "refresh": "5s",
    "schemaVersion": 38,
    "tags": [],
    "templating": {
      "list": []
    },
    "time": {
      "from": "now-24h",
      "to": "now"
    },
    "timepicker": {},
    "timezone": "",
    "title": "Docker Statistics",
    "uid": "c67742f2-7db9-489b-90fa-a13f4655806a",
    "version": 3,
    "weekStart": ""
  }
````

## File: log-analyser/grafana-provisioning/dashboards/jicofo.json
````json
{
  "annotations": {
    "list": [
      {
        "builtIn": 1,
        "datasource": {
          "type": "grafana",
          "uid": "-- Grafana --"
        },
        "enable": true,
        "hide": true,
        "iconColor": "rgba(0, 211, 255, 1)",
        "name": "Annotations & Alerts",
        "type": "dashboard"
      }
    ]
  },
  "editable": true,
  "fiscalYearStartMonth": 0,
  "graphTooltip": 0,
  "links": [],
  "liveNow": false,
  "panels": [
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 0
      },
      "id": 6,
      "options": {
        "dedupStrategy": "none",
        "enableLogDetails": true,
        "prettifyLogMessage": false,
        "showCommonLabels": false,
        "showLabels": false,
        "showTime": false,
        "sortOrder": "Descending",
        "wrapLogMessage": false
      },
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
          },
          "editorMode": "code",
          "expr": "{exporter=\"OTLP\"} | json | attributes_attrs_service=\"jitsi-jicofo\"",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Jicofo Logs",
      "type": "logs"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            }
          },
          "mappings": []
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 0
      },
      "id": 2,
      "options": {
        "displayLabels": [
          "percent"
        ],
        "legend": {
          "calcs": [],
          "displayMode": "hidden",
          "placement": "right",
          "showLegend": false,
          "values": []
        },
        "pieType": "pie",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "b8130a28-4867-4668-917d-539c93852857"
          },
          "editorMode": "code",
          "expr": "sum by (attributes_level) (\n  rate({exporter=\"OTLP\"} | json|attributes_attrs_service=\"jitsi-jicofo\"| line_format \"{{.log}}\" | logfmt | pattern \"[<_>] <_level>: <_>\"[5m])\n)",
          "legendFormat": "Level: {{attributes_level}}",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Jicofo Log Levels Pie Chart",
      "type": "piechart"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "continuous-GrYlRd"
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 8
      },
      "id": 1,
      "options": {
        "displayMode": "lcd",
        "minVizHeight": 10,
        "minVizWidth": 0,
        "namePlacement": "auto",
        "orientation": "horizontal",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "showUnfilled": true,
        "valueMode": "color"
      },
      "pluginVersion": "10.2.0",
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "b8130a28-4867-4668-917d-539c93852857"
          },
          "editorMode": "code",
          "expr": "sum by (attributes_level, attributes_attrs_service) (\n  rate({exporter=\"OTLP\"} | json|attributes_attrs_service=\"jitsi-jicofo\"| line_format \"{{.attributes_message}}\" | logfmt | pattern \"[<_>] <attributes_level>#<attributes_attrs_service>: <_>\"[5m]))",
          "legendFormat": "Level: {{attributes_level}}",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Jicofo Log Levels Bar Chart",
      "transformations": [],
      "type": "bargauge"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "bars",
            "fillOpacity": 100,
            "gradientMode": "hue",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "normal"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 8
      },
      "id": 3,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "hidden",
          "placement": "right",
          "showLegend": false
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "pluginVersion": "10.2.0",
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "P8E80F9AEF21F6940"
          },
          "editorMode": "code",
          "expr": "sum by (attributes_codefile) (\n    rate({exporter=\"OTLP\"} | json|attributes_attrs_service=\"jitsi-jicofo\"| attributes_level=\"ERROR\" | line_format \"{{.attributes_message}}\" | logfmt | pattern \"[<_>] <attributes_level>#<attributes_attrs_service>: <_>\"[5m]))",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Jicofo Total Rate of ERROR Logs Aggregated by Code File",
      "type": "timeseries"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "description": "This panel shows the number of conference requests over time.",
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "bars",
            "fillOpacity": 100,
            "gradientMode": "hue",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "normal"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 16
      },
      "id": 4,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "hidden",
          "placement": "right",
          "showLegend": false
        },
        "tooltip": {
          "mode": "multi",
          "sort": "none"
        }
      },
      "pluginVersion": "10.2.0",
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
          },
          "editorMode": "code",
          "expr": "count_over_time({exporter=\"OTLP\"} | json | attributes_attrs_service=\"jitsi-jicofo\" |= \"Conference request\" [1m])",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Jicofo Number of Conference Requests",
      "type": "timeseries"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "fieldConfig": {
        "defaults": {
          "mappings": [],
          "thresholds": {
            "mode": "percentage",
            "steps": [
              {
                "color": "green",
                "value": null
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 16
      },
      "id": 5,
      "options": {
        "minVizHeight": 75,
        "minVizWidth": 75,
        "orientation": "auto",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "showThresholdLabels": false,
        "showThresholdMarkers": true
      },
      "pluginVersion": "10.2.0",
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
          },
          "editorMode": "code",
          "expr": "sum(count_over_time({exporter=\"OTLP\"} | json | attributes_attrs_service=\"jitsi-jicofo\" |~ \"Member left|Terminating|Removed participant\" [1m]))",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Jicofo Total Counts of Member Left, Terminating, Removed Participant",
      "type": "gauge"
    }
  ],
  "refresh": "",
  "schemaVersion": 38,
  "tags": [],
  "templating": {
    "list": []
  },
  "time": {
    "from": "now-6h",
    "to": "now"
  },
  "timepicker": {},
  "timezone": "",
  "title": "Jicofo Dashboard",
  "uid": "f2dcfe84-3c27-4b1d-8583-bc2c97a8d22d",
  "version": 20,
  "weekStart": ""
}
````

## File: log-analyser/grafana-provisioning/dashboards/jitsi-all.json
````json
{
  "annotations": {
    "list": [
      {
        "builtIn": 1,
        "datasource": {
          "type": "grafana",
          "uid": "-- Grafana --"
        },
        "enable": true,
        "hide": true,
        "iconColor": "rgba(0, 211, 255, 1)",
        "name": "Annotations & Alerts",
        "type": "dashboard"
      }
    ]
  },
  "editable": true,
  "fiscalYearStartMonth": 0,
  "graphTooltip": 0,
  "links": [],
  "liveNow": false,
  "panels": [
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "description": "",
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "thresholds"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "fillOpacity": 80,
            "gradientMode": "opacity",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "lineWidth": 1,
            "scaleDistribution": {
              "type": "linear"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          },
          "unit": "percent"
        },
        "overrides": []
      },
      "gridPos": {
        "h": 13,
        "w": 24,
        "x": 0,
        "y": 0
      },
      "id": 1,
      "options": {
        "barRadius": 0,
        "barWidth": 1,
        "fullHighlight": false,
        "groupWidth": 0.7,
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "orientation": "auto",
        "showValue": "auto",
        "stacking": "none",
        "tooltip": {
          "mode": "single",
          "sort": "none"
        },
        "xTickLabelRotation": 0,
        "xTickLabelSpacing": 0
      },
      "pluginVersion": "10.2.0",
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "b8130a28-4867-4668-917d-539c93852857"
          },
          "editorMode": "code",
          "expr": "sum by (attributes_attrs_service) (\n  rate({exporter=\"OTLP\"} | json | line_format \"{{.attributes_message}}\" | logfmt | pattern \"[<_>] <_level>: <_>\"[5m]))",
          "legendFormat": "{{attributes_attrs_service}}",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Jicofo, Prosody, Jitsi Web and JVB Log Counts",
      "type": "barchart"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "gridPos": {
        "h": 8,
        "w": 24,
        "x": 0,
        "y": 13
      },
      "id": 2,
      "options": {
        "dedupStrategy": "none",
        "enableLogDetails": true,
        "prettifyLogMessage": false,
        "showCommonLabels": false,
        "showLabels": false,
        "showTime": false,
        "sortOrder": "Descending",
        "wrapLogMessage": false
      },
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
          },
          "editorMode": "code",
          "expr": "{exporter=\"OTLP\"} ",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Jitsi All Logs (Jicofo, Prosody, JVB, Web)",
      "type": "logs"
    }
  ],
  "refresh": "",
  "schemaVersion": 38,
  "tags": [],
  "templating": {
    "list": []
  },
  "time": {
    "from": "now-6h",
    "to": "now"
  },
  "timepicker": {},
  "timezone": "",
  "title": "Jitsi All Components Dashboard",
  "uid": "b75d666d-4537-45e2-94a1-2783f9362b65",
  "version": 14,
  "weekStart": ""
}
````

## File: log-analyser/grafana-provisioning/dashboards/jitsi-web.json
````json
{
  "annotations": {
    "list": [
      {
        "builtIn": 1,
        "datasource": {
          "type": "grafana",
          "uid": "-- Grafana --"
        },
        "enable": true,
        "hide": true,
        "iconColor": "rgba(0, 211, 255, 1)",
        "name": "Annotations & Alerts",
        "type": "dashboard"
      }
    ]
  },
  "editable": true,
  "fiscalYearStartMonth": 0,
  "graphTooltip": 0,
  "links": [],
  "liveNow": false,
  "panels": [
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 0
      },
      "id": 5,
      "options": {
        "dedupStrategy": "none",
        "enableLogDetails": true,
        "prettifyLogMessage": false,
        "showCommonLabels": false,
        "showLabels": false,
        "showTime": false,
        "sortOrder": "Descending",
        "wrapLogMessage": false
      },
      "pluginVersion": "10.2.0",
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
          },
          "editorMode": "code",
          "expr": "{exporter=\"OTLP\"} | json | attributes_attrs_service=\"jitsi-web\"",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Jitsi Web Logs",
      "type": "logs"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            }
          },
          "mappings": []
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 0
      },
      "id": 4,
      "options": {
        "displayLabels": [
          "percent"
        ],
        "legend": {
          "calcs": [],
          "displayMode": "hidden",
          "placement": "right",
          "showLegend": false,
          "values": []
        },
        "pieType": "pie",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
          },
          "editorMode": "code",
          "expr": "sum by (attributes_level) (\n  rate({exporter=\"OTLP\"} | json|attributes_attrs_service=\"jitsi-web\"| line_format \"{{.log}}\" | logfmt | pattern \"[<_>] <_level>: <_>\"[5m])\n)",
          "legendFormat": "Level: {{attributes_level}}",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Jitsi Web Log Levels Pie Chart",
      "type": "piechart"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "continuous-GrYlRd"
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 8
      },
      "id": 3,
      "options": {
        "displayMode": "lcd",
        "minVizHeight": 10,
        "minVizWidth": 0,
        "namePlacement": "auto",
        "orientation": "horizontal",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "showUnfilled": true,
        "valueMode": "color"
      },
      "pluginVersion": "10.2.0",
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
          },
          "editorMode": "code",
          "expr": "sum by (attributes_level, attributes_attrs_service) (\n  rate({exporter=\"OTLP\"} | json|attributes_attrs_service=\"jitsi-web\"| line_format \"{{.attributes_message}}\" | logfmt | pattern \"[<_>] <attributes_level>#<attributes_attrs_service>: <_>\"[5m]))",
          "legendFormat": "Level: {{attributes_level}}",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Jitsi Web Log Levels Bar Chart",
      "type": "bargauge"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "fieldConfig": {
        "defaults": {
          "mappings": [],
          "thresholds": {
            "mode": "percentage",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "semi-dark-red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 8
      },
      "id": 2,
      "options": {
        "minVizHeight": 75,
        "minVizWidth": 75,
        "orientation": "auto",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "showThresholdLabels": false,
        "showThresholdMarkers": true
      },
      "pluginVersion": "10.2.0",
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
          },
          "editorMode": "code",
          "expr": "sum(count_over_time({exporter=\"OTLP\"} | json | attributes_attrs_service=\"jitsi-web\" |= \"GET\" [5m])) by (instance)",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Jitsi Web Sum of 'GET' Log Counts",
      "type": "gauge"
    }
  ],
  "refresh": "",
  "schemaVersion": 38,
  "tags": [],
  "templating": {
    "list": []
  },
  "time": {
    "from": "now-6h",
    "to": "now"
  },
  "timepicker": {},
  "timezone": "",
  "title": "Jitsi Web Dashboard",
  "uid": "d1f8ba02-9b8d-42c7-8934-d30ea3559a49",
  "version": 4,
  "weekStart": ""
}
````

## File: log-analyser/grafana-provisioning/dashboards/jvb.json
````json
{
  "annotations": {
    "list": [
      {
        "builtIn": 1,
        "datasource": {
          "type": "grafana",
          "uid": "-- Grafana --"
        },
        "enable": true,
        "hide": true,
        "iconColor": "rgba(0, 211, 255, 1)",
        "name": "Annotations & Alerts",
        "type": "dashboard"
      }
    ]
  },
  "editable": true,
  "fiscalYearStartMonth": 0,
  "graphTooltip": 0,
  "links": [],
  "liveNow": false,
  "panels": [
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 0
      },
      "id": 3,
      "options": {
        "dedupStrategy": "none",
        "enableLogDetails": true,
        "prettifyLogMessage": false,
        "showCommonLabels": false,
        "showLabels": false,
        "showTime": false,
        "sortOrder": "Descending",
        "wrapLogMessage": false
      },
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
          },
          "editorMode": "code",
          "expr": "{exporter=\"OTLP\"} | json | attributes_attrs_service=\"jitsi-jvb\"",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "JVB Logs",
      "type": "logs"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            }
          },
          "mappings": []
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 0
      },
      "id": 2,
      "options": {
        "displayLabels": [
          "percent",
          "name"
        ],
        "legend": {
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "pieType": "pie",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "b8130a28-4867-4668-917d-539c93852857"
          },
          "editorMode": "code",
          "expr": "sum by (attributes_level) (\n  rate({exporter=\"OTLP\"} | json|attributes_attrs_service=\"jitsi-jvb\"| line_format \"{{.log}}\" | logfmt | pattern \"[<_>] <_level>: <_>\"[5m])\n)",
          "legendFormat": "Level: {{attributes_level}}",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "JVB Log Levels Pie Chart",
      "type": "piechart"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "continuous-GrYlRd"
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          },
          "unit": "percent"
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 8
      },
      "id": 1,
      "options": {
        "displayMode": "lcd",
        "minVizHeight": 10,
        "minVizWidth": 0,
        "namePlacement": "auto",
        "orientation": "horizontal",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "showUnfilled": true,
        "valueMode": "color"
      },
      "pluginVersion": "10.2.0",
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "b8130a28-4867-4668-917d-539c93852857"
          },
          "editorMode": "code",
          "expr": "sum by (attributes_level, attributes_attrs_service) (\n  rate({exporter=\"OTLP\"} | json|attributes_attrs_service=\"jitsi-jvb\" | line_format \"{{.attributes_message}}\" | logfmt | pattern \"[<_>] <attributes_level>#<attributes_attrs_service>: <_>\"[5m]))",
          "legendFormat": "Level: {{attributes_level}}",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "JVB Log Levels Bar Chart",
      "type": "bargauge"
    }
  ],
  "refresh": "",
  "schemaVersion": 38,
  "tags": [],
  "templating": {
    "list": []
  },
  "time": {
    "from": "now-6h",
    "to": "now"
  },
  "timepicker": {},
  "timezone": "",
  "title": "JVB Dashboard",
  "uid": "d53a9efb-ca3b-4f47-af3a-9638de8a35fa",
  "version": 10,
  "weekStart": ""
}
````

## File: log-analyser/grafana-provisioning/dashboards/Prosody-Dashboard.json
````json
{
  "annotations": {
    "list": [
      {
        "builtIn": 1,
        "datasource": {
          "type": "grafana",
          "uid": "-- Grafana --"
        },
        "enable": true,
        "hide": true,
        "iconColor": "rgba(0, 211, 255, 1)",
        "name": "Annotations & Alerts",
        "type": "dashboard"
      }
    ]
  },
  "editable": true,
  "fiscalYearStartMonth": 0,
  "graphTooltip": 0,
  "id": 20,
  "links": [],
  "liveNow": false,
  "panels": [
    {
      "collapsed": false,
      "gridPos": {
        "h": 1,
        "w": 24,
        "x": 0,
        "y": 0
      },
      "id": 4,
      "panels": [],
      "title": "Session Activity",
      "type": "row"
    },
    {
      "datasource": {
        "type": "prometheus",
        "uid": "bbb38190-2464-44ea-8080-f9ca9e3dcd61"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 1
      },
      "id": 1,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          },
          "editorMode": "code",
          "expr": "sum(rate(prosody_mod_smacks__sessions_started_total[5m]))",
          "instant": false,
          "legendFormat": "Smacks Sessions",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "Smacks Sessions started",
      "type": "timeseries"
    },
    {
      "datasource": {},
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 1
      },
      "id": 2,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          },
          "editorMode": "code",
          "expr": "sum(prosody_mod_smacks__session_resumption_expired_total)",
          "instant": false,
          "legendFormat": "Session Resumption Expired",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "Session Resumption Expired",
      "type": "timeseries"
    },
    {
      "datasource": {},
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 9
      },
      "id": 3,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          },
          "editorMode": "code",
          "expr": "sum(prosody_mod_bosh__active_sessions)",
          "instant": false,
          "legendFormat": "Bosh active sessions",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "Bosh Active Sessions",
      "type": "timeseries"
    },
    {
      "datasource": {},
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 9
      },
      "id": 6,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "hidden",
          "placement": "right",
          "showLegend": false
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          },
          "editorMode": "code",
          "expr": "sum(prosody_mod_c2s__connections) by (ip_family)",
          "instant": false,
          "legendFormat": "{{label_name}}",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "C2S Connections by IP Family",
      "type": "timeseries"
    },
    {
      "collapsed": false,
      "gridPos": {
        "h": 1,
        "w": 24,
        "x": 0,
        "y": 17
      },
      "id": 10,
      "panels": [],
      "title": "Stanza Handling",
      "type": "row"
    },
    {
      "datasource": {},
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 18
      },
      "id": 8,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          },
          "editorMode": "code",
          "expr": "sum(prosody_mod_smacks__tx_queued_stanzas_total)",
          "instant": false,
          "legendFormat": "TX Queued Stanzas",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "TX Queued Stanzas",
      "type": "timeseries"
    },
    {
      "datasource": {
        "type": "prometheus",
        "uid": "bbb38190-2464-44ea-8080-f9ca9e3dcd61"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 18
      },
      "id": 21,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "bbb38190-2464-44ea-8080-f9ca9e3dcd61"
          },
          "editorMode": "code",
          "expr": "sum(prosody_mod_measure_stanza_counts__received_total)by(stanza_kind)",
          "instant": false,
          "legendFormat": "__auto",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "Stanza Counts",
      "type": "timeseries"
    },
    {
      "datasource": {},
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 26
      },
      "id": 9,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          },
          "editorMode": "code",
          "expr": "sum(rate(prosody_mod_smacks__tx_dropped_stanzas_count[5m]))",
          "instant": false,
          "legendFormat": "Rate of Dropped Stanzas",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "Dropped Stanzas Rate",
      "type": "timeseries"
    },
    {
      "collapsed": false,
      "gridPos": {
        "h": 1,
        "w": 24,
        "x": 0,
        "y": 34
      },
      "id": 13,
      "panels": [],
      "title": "Processing Performance",
      "type": "row"
    },
    {
      "datasource": {},
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 35
      },
      "id": 12,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "hidden",
          "placement": "right",
          "showLegend": false
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          },
          "editorMode": "code",
          "expr": "sum(rate(prosody_stats_processing_seconds_sum[5m]))*1000000",
          "instant": false,
          "legendFormat": "Processing in microsecond",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "Processing time Sum",
      "type": "timeseries"
    },
    {
      "datasource": {},
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "thresholds"
          },
          "custom": {
            "fillOpacity": 80,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "lineWidth": 1
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": [
          {
            "__systemRef": "hideSeriesFrom",
            "matcher": {
              "id": "byNames",
              "options": {
                "mode": "exclude",
                "names": [
                  "Processing"
                ],
                "prefix": "All except:",
                "readOnly": true
              }
            },
            "properties": [
              {
                "id": "custom.hideFrom",
                "value": {
                  "legend": false,
                  "tooltip": false,
                  "viz": true
                }
              }
            ]
          },
          {
            "matcher": {
              "id": "byName",
              "options": "Processing"
            },
            "properties": [
              {
                "id": "color",
                "value": {
                  "fixedColor": "green",
                  "mode": "fixed"
                }
              }
            ]
          }
        ]
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 35
      },
      "id": 11,
      "options": {
        "bucketOffset": 0,
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        }
      },
      "pluginVersion": "10.2.0",
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          },
          "editorMode": "code",
          "expr": "histogram_quantile(0.90, sum(rate(prosody_stats_processing_seconds_bucket[5m])) by (le))",
          "instant": false,
          "legendFormat": "Processing",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "Processing time Histogram",
      "type": "histogram"
    },
    {
      "collapsed": false,
      "gridPos": {
        "h": 1,
        "w": 24,
        "x": 0,
        "y": 43
      },
      "id": 16,
      "panels": [],
      "title": "Room Metrics",
      "type": "row"
    },
    {
      "datasource": {
        "type": "prometheus",
        "uid": "bbb38190-2464-44ea-8080-f9ca9e3dcd61"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green"
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 44
      },
      "id": 15,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          },
          "editorMode": "code",
          "expr": "sum(prosody_mod_muc__room_hit_total{host!~\"internal-muc.*\"}) by (host)",
          "instant": false,
          "legendFormat": "__auto",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "Room Hits",
      "type": "timeseries"
    },
    {
      "datasource": {
        "type": "prometheus",
        "uid": "bbb38190-2464-44ea-8080-f9ca9e3dcd61"
      },
      "fieldConfig": {
        "defaults": {
          "mappings": [],
          "thresholds": {
            "mode": "percentage",
            "steps": [
              {
                "color": "green"
              },
              {
                "color": "orange",
                "value": 70
              },
              {
                "color": "red",
                "value": 85
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 44
      },
      "id": 14,
      "options": {
        "minVizHeight": 75,
        "minVizWidth": 75,
        "orientation": "auto",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "showThresholdLabels": false,
        "showThresholdMarkers": true
      },
      "pluginVersion": "10.2.0",
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          },
          "editorMode": "code",
          "expr": "sum(prosody_mod_muc__live_room{host!~\"internal-muc.*\"})by(host)\n",
          "instant": false,
          "legendFormat": "__auto",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "Live MUC Rooms",
      "type": "gauge"
    },
    {
      "datasource": {
        "type": "prometheus",
        "uid": "bbb38190-2464-44ea-8080-f9ca9e3dcd61"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green"
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 52
      },
      "id": 20,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          },
          "editorMode": "code",
          "expr": "sum(prosody_mod_muc__room_miss_total{host!~\"internal-muc.*\"})by(host)",
          "instant": false,
          "legendFormat": "__auto",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "MUC Room Miss",
      "type": "timeseries"
    },
    {
      "collapsed": false,
      "gridPos": {
        "h": 1,
        "w": 24,
        "x": 0,
        "y": 60
      },
      "id": 19,
      "panels": [],
      "title": "Error Monitoring",
      "type": "row"
    },
    {
      "datasource": {},
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green"
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 61
      },
      "id": 17,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          },
          "editorMode": "code",
          "expr": "sum(prosody_mod_bosh__bad_sid_total)",
          "instant": false,
          "legendFormat": "Bad Bosh Sessions",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "Bad Bosh Session IDs",
      "type": "timeseries"
    },
    {
      "datasource": {},
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green"
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 61
      },
      "id": 18,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          },
          "editorMode": "code",
          "expr": "sum(prosody_mod_bosh__new_sid_total)",
          "instant": false,
          "legendFormat": "New Bosh",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "New Bosh SIDs",
      "type": "timeseries"
    }
  ],
  "refresh": "5s",
  "schemaVersion": 38,
  "tags": [],
  "templating": {
    "list": []
  },
  "time": {
    "from": "now-1h",
    "to": "now"
  },
  "timepicker": {},
  "timezone": "",
  "title": "Prosody Dashboard",
  "uid": "c8b1b5f8-6b45-4fa7-92b2-01cc164e2f94asdf",
  "version": 1,
  "weekStart": ""
}
````

## File: log-analyser/grafana-provisioning/dashboards/prosody.json
````json
{
  "annotations": {
    "list": [
      {
        "builtIn": 1,
        "datasource": {
          "type": "grafana",
          "uid": "-- Grafana --"
        },
        "enable": true,
        "hide": true,
        "iconColor": "rgba(0, 211, 255, 1)",
        "name": "Annotations & Alerts",
        "type": "dashboard"
      }
    ]
  },
  "editable": true,
  "fiscalYearStartMonth": 0,
  "graphTooltip": 0,
  "links": [],
  "liveNow": false,
  "panels": [
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 0
      },
      "id": 6,
      "options": {
        "dedupStrategy": "none",
        "enableLogDetails": true,
        "prettifyLogMessage": false,
        "showCommonLabels": false,
        "showLabels": false,
        "showTime": false,
        "sortOrder": "Descending",
        "wrapLogMessage": false
      },
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
          },
          "editorMode": "code",
          "expr": "{exporter=\"OTLP\"} | json | attributes_attrs_service=\"jitsi-prosody\"",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Prosody Logs",
      "type": "logs"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            }
          },
          "mappings": []
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 0
      },
      "id": 1,
      "options": {
        "displayLabels": [
          "percent"
        ],
        "legend": {
          "calcs": [],
          "displayMode": "hidden",
          "placement": "right",
          "showLegend": false,
          "values": []
        },
        "pieType": "pie",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
          },
          "editorMode": "code",
          "expr": "sum by (attributes_level) (\n  rate({exporter=\"OTLP\"} | json|attributes_attrs_service=\"jitsi-prosody\"| line_format \"{{.log}}\" | logfmt | pattern \"[<_>] <_level>: <_>\"[5m])\n)",
          "legendFormat": "Level: {{attributes_level}}",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Prosody Log Levels Pie Chart",
      "type": "piechart"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "continuous-GrYlRd"
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 8
      },
      "id": 7,
      "options": {
        "displayMode": "lcd",
        "minVizHeight": 10,
        "minVizWidth": 0,
        "namePlacement": "auto",
        "orientation": "horizontal",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "showUnfilled": true,
        "valueMode": "color"
      },
      "pluginVersion": "10.2.0",
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
          },
          "editorMode": "code",
          "expr": "sum by (attributes_level, attributes_attrs_service) (\n  rate({exporter=\"OTLP\"} | json|attributes_attrs_service=\"jitsi-prosody\"| line_format \"{{.attributes_message}}\" | logfmt | pattern \"[<_>] <attributes_level>#<attributes_attrs_service>: <_>\"[5m]))",
          "legendFormat": "Level: {{attributes_level}}",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Prosody Log Levels Bar Chart",
      "type": "bargauge"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "fieldConfig": {
        "defaults": {
          "mappings": [],
          "thresholds": {
            "mode": "percentage",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "orange",
                "value": 70
              },
              {
                "color": "red",
                "value": 85
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 8
      },
      "id": 5,
      "options": {
        "minVizHeight": 75,
        "minVizWidth": 75,
        "orientation": "auto",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "showThresholdLabels": false,
        "showThresholdMarkers": true
      },
      "pluginVersion": "10.2.0",
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
          },
          "editorMode": "code",
          "expr": "sum(count_over_time({exporter=\"OTLP\"} | json | attributes_attrs_service=\"jitsi-prosody\" |~ \"Starting room\" [1m]))",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Prosody Total Number of Rooms Started",
      "type": "gauge"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "fieldConfig": {
        "defaults": {
          "mappings": [],
          "thresholds": {
            "mode": "percentage",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "orange",
                "value": 70
              },
              {
                "color": "red",
                "value": 85
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 16
      },
      "id": 3,
      "options": {
        "minVizHeight": 75,
        "minVizWidth": 75,
        "orientation": "auto",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "showThresholdLabels": false,
        "showThresholdMarkers": true
      },
      "pluginVersion": "10.2.0",
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
          },
          "editorMode": "code",
          "expr": "sum(count_over_time({exporter=\"OTLP\"} | json | attributes_attrs_service=\"jitsi-prosody\" |~ \"Client disconnected\" [1m]))",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Prosody Total Number of Clients Disconnected",
      "type": "gauge"
    },
    {
      "datasource": {
        "type": "loki",
        "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
      },
      "fieldConfig": {
        "defaults": {
          "mappings": [],
          "thresholds": {
            "mode": "percentage",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "orange",
                "value": 70
              },
              {
                "color": "red",
                "value": 85
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 16
      },
      "id": 2,
      "options": {
        "minVizHeight": 75,
        "minVizWidth": 75,
        "orientation": "auto",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "showThresholdLabels": false,
        "showThresholdMarkers": true
      },
      "pluginVersion": "10.2.0",
      "targets": [
        {
          "datasource": {
            "type": "loki",
            "uid": "a4bdfb3e-762a-46e5-a79f-2e7bbe88d444"
          },
          "editorMode": "code",
          "expr": "sum(count_over_time({exporter=\"OTLP\"} | json |attributes_attrs_service=\"jitsi-prosody\" |~ \"Client connected\" [1m]))",
          "queryType": "range",
          "refId": "A"
        }
      ],
      "title": "Prosody Total Number of Clients Connected",
      "type": "gauge"
    }
  ],
  "refresh": "",
  "schemaVersion": 38,
  "tags": [],
  "templating": {
    "list": []
  },
  "time": {
    "from": "now-6h",
    "to": "now"
  },
  "timepicker": {},
  "timezone": "",
  "title": "Prosody Dashboard",
  "uid": "fe2d57bc-b09b-4688-8037-f642047b0cfc",
  "version": 1,
  "weekStart": ""
}
````

## File: log-analyser/grafana-provisioning/datasources/datasource_loki.yml
````yaml
apiVersion: 1

datasources:
  - name: Loki
    isDefault: true
    type: loki
    access: proxy
    url: http://loki:3100
    editable: true
    apiVersion: 1

  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://prometheus:9090
    isDefault: true
    editable: true
    jsonData:
      timeInterval: "5s"
````

## File: log-analyser/loki/conf/loki-config.yaml
````yaml
limits_config:
  allow_structured_metadata: true

auth_enabled: false

server:
  http_listen_port: 3100
  grpc_listen_port: 9096

common:
  instance_addr: 127.0.0.1
  path_prefix: /tmp/loki
  storage:
    filesystem:
      chunks_directory: /tmp/loki/chunks
      rules_directory: /tmp/loki/rules
  replication_factor: 1
  ring:
    kvstore:
      store: inmemory

query_range:
  results_cache:
    cache:
      embedded_cache:
        enabled: true
        max_size_mb: 100

schema_config:
  configs:
    - from: 2020-10-24
      store: tsdb
      object_store: filesystem
      schema: v13
      index:
        prefix: index_
        period: 24h


# By default, Loki will send anonymous, but uniquely-identifiable usage and configuration
# analytics to Grafana Labs. These statistics are sent to https://stats.grafana.org/
#
# Statistics help us better understand how Loki is used, and they show us performance
# levels for most users. This helps us prioritize features and documentation.
# For more information on what's sent, look at
# https://github.com/grafana/loki/blob/main/pkg/analytics/stats.go
# Refer to the buildReport method to see what goes into a report.
#
# If you would like to disable reporting, uncomment the following lines:
#analytics:
#  reporting_enabled: false
````

## File: log-analyser/otel-collector-config.yaml
````yaml
receivers:
  otlp:
    protocols:
      http:
      grpc:
        endpoint: 0.0.0.0:4317
  filelog/jitsi-containers:
    include: ['/var/lib/docker/containers/*/*.log']
    encoding: utf-8
    operators:
      - type: json_parser
        id: parser-docker
        output: filter_non_tagged_containers
        timestamp:
          parse_from: attributes.time
          layout: '%Y-%m-%dT%H:%M:%S.%LZ'
      - type: filter
        id: filter_non_tagged_containers
        expr: |
          (attributes?.attrs?.service != "jitsi-web" and 
          attributes?.attrs?.service != "jitsi-jicofo" and 
          attributes?.attrs?.service != "jitsi-jvb" and
          attributes?.attrs?.service != "jitsi-prosody")
        output: regex_parser_choice
      - type: router
        id: regex_parser_choice
        routes:
          - expr: 'attributes.attrs.service == "jitsi-web"'
            output: jitsi_web_parser
          - expr: 'attributes.attrs.service == "jitsi-jicofo"'
            output: jitsi_jicofo_parser
          - expr: 'attributes.attrs.service == "jitsi-jvb"'
            output: jitsi_jvb_parser
          - expr: 'attributes.attrs.service == "jitsi-prosody"'
            output: jitsi_prosody_parser
      - type: regex_parser
        id: jitsi_web_parser
        parse_from: attributes.log
        regex: "^(?P<message>[\\s\\S]*)$|^(\\[(?P<temp_meta>[\\w\\W]+)\\] (?P<temp_message>[\\S\\s]*)$)"
      - type: regex_parser
        id: jitsi_jicofo_parser
        parse_from: attributes.log
        regex: "(^(?P<app>\\w+) (?P<timestamp>\\d{4}-\\d{2}-\\d{2} \\d{2}:\\d{2}:\\d{2}\\.\\d{3}) (?P<level>\\w+): \\[(?P<pid>\\d+)\\]( \\[(?P<meta>[\\w\\W]+)\\])? (?P<codefile>[\\w\\W]+)(#(?P<codeline>\\d+))?: (?P<message>[\\s\\S]*)$)|^(\\[(?P<temp_meta>[\\w\\W]+)\\] (?P<temp_message>[\\S\\s]*)$)"
        output: move_meta_key
      - type: regex_parser
        id: jitsi_jvb_parser
        parse_from: attributes.log
        regex: "^(?P<app>JVB)  \\[(?P<pid>\\d+)\\] (?P<codefile>[\\w\\.]+)#(?P<codeline>\\d+): (?P<message>[\\S\\s]*)$"
      - type: regex_parser
        id: jitsi_prosody_parser
        parse_from: attributes.log
        regex: (^(?<timestamp>\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2})\s+(?<service>\S+)\s+(?<level>\w{0,10})(\t)(?P<message>[\s\S]*)$)|^(\[(?P<temp_meta>[\w\W]+)\] (?P<temp_message>[\S\s]*)$)
        output: move_meta_key
      - type: move
        id: move_meta_key
        if: "attributes.temp_meta != nil and attributes.temp_meta != ''"
        from: attributes.temp_meta
        to: attributes.meta
        output: move_message_key
      - type: move
        id: move_message_key
        if: "attributes.temp_message != nil and attributes.temp_message != ''"
        from: attributes.temp_message
        to: attributes.message
  docker_stats:
    endpoint: "unix:///var/run/docker.sock"
processors:
  batch:

exporters:
  logging:
    loglevel: debug
  loki:
    endpoint: "http://loki:3100/loki/api/v1/push"
  prometheus:
    endpoint: "0.0.0.0:9464"

service:
  pipelines:
    logs:
      receivers: [otlp, filelog/jitsi-containers]
      processors: [batch]
      exporters: [loki]
    metrics:
      receivers: [docker_stats]
      processors: [batch]
      exporters: [prometheus]
````

## File: log-analyser/README.md
````markdown
# JITSI MEET LOG ANALYSER - Grafana Loki and OpenTelemetry Integration

Welcome to the Grafana Loki and OpenTelemetry integration project! This repository provides a simple and effective setup for log management and analysis using Docker, Grafana Loki, and OpenTelemetry.
Currently this is an in-progress GSoC Summer of Code project and so the instructions may change before being finalized.  Please treat all this as alpha code.

## Overview

This project demonstrates how to configure and use Grafana Loki with OpenTelemetry to collect, parse, and visualize log data from Jitsi Meet components. It includes:

- A Docker Compose setup (`log-analyser.yml`) for Loki and OpenTelemetry Collector.
- A Docker Compose setup (`grafana.yml`) for Grafana.
- A unified Docker Compose command to start all services.
- Instructions to set up and access Grafana with Loki as a data source.

## Getting Started

### Prerequisites

- Docker
- Docker Compose

### Setup

1. **Clone the repository:**

```bash
git clone https://github.com/jitsi/docker-jitsi-meet.git
```

2. **Update Jitsi Meet Docker Compose Configuration:**

To enable log collection and analysis, you need to modify the `docker-compose.yml` file for Jitsi Meet components. Add the following configuration to each Jitsi service within the `docker-compose.yml` file:

```yaml
    logging:
      driver: "json-file"
      options:
        labels: "service"
```

This configuration ensures that logs are collected in JSON format and tagged with service labels, which is essential for Loki to properly ingest and index the logs.

3. **Start the Docker containers:**

   To start all necessary services, including Jitsi Meet components, Grafana, Loki, and OpenTelemetry, run:

    ```bash
    docker-compose -f docker-compose.yml -f log-analyser.yml -f grafana.yml up -d
    ```

   - This command will start the Jitsi Meet components from `docker-compose.yml`, the log analysis tools from `log-analyser.yml`, and Grafana from `grafana.yml`. The logs from Jitsi Meet components will automatically be sent to Grafana through Loki.
   - **Note:** To use only Grafana for visualization without log analysis, you can use just `grafana.yml` alone. However, for the complete log analysis project, you need both `log-analyser.yml` and `grafana.yml`.

### Access Grafana

1. **Open your web browser and navigate to [http://localhost:3000](http://localhost:3000).**

2. **Log in to Grafana:**

   Use the default credentials:

    ```
    Username: admin
    Password: admin
    ```

3. **Dashboard Setup:**

   The dashboards for Jitsi Meet components are pre-configured and will be automatically available in Grafana. You can explore these dashboards to view and analyze logs.

## Usage

- **Log Parsing and Visualization:** After setting up, use Grafana to explore and visualize your logs. Check the pre-configured dashboards and panels to monitor and analyze log data from Jitsi Meet components effectively.

## Acknowledgements

Thank you for exploring this project!
For detailed documentation, follow the [Jitsi Handbook](https://jitsi.github.io/handbook/docs/intro), you can follow the Docker and Log-Analyser guides under Self-Hosting Guide > Deployment guide.

If you have any questions or need further assistance, please feel free to reach out.
````

## File: prometheus/prometheus.yml
````yaml
scrape_configs:
  - job_name: "prometheus"
    scrape_interval: 5s
    static_configs:
      - targets: ["prosody:5280","jvb:8080","jicofo:8888","otel:9464"]
````

## File: prometheus/README.md
````markdown
# Prometheus Scraping & Grafana Dashboard for Jitsi

## Overview

This project aims to integrate **Prometheus** and **Grafana** with Jitsi to monitor and visualize performance metrics.

## Features

- **Prometheus Integration**: Collects metrics from Jitsi containers.
- **Grafana Dashboards**: Visualizes the metrics for easy analysis.

## Installation

### Prerequisites

- Docker
- Docker Compose

### Steps

1. **Setup Jitsi with Docker Compose**

   Follow the [Jitsi Docker](https://github.com/jitsi/docker-jitsi-meet) setup instructions. <br>
   Also, you could follow Self - Hosting guide of Jitsi Meet: [Jitsi handbook](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/)

2. **Configure Prometheus**

   Edit `/prometheus/prometheus.yml` with any **Port / Container name** changes are there to scrape metrics from Jitsi containers:

   ```yaml
   scrape_configs:
     - job_name: "jitsi"
       static_configs:
         - targets: ["prosody:5280", "jvb:8080", "jicofo:8888", "otel:9464"]
   ```

3. **Run Docker Compose**

   The following command turns up the Jitsi Meet:

   ```bash
   docker-compose up -d
   ```

   If you want to add the Prometheus and Grafana for monitoring purpose. Use the following command:

   ```bash
   docker-compose -f docker-compose.yml -f prometheus.yml -f grafana.yml up -d
   ```

   To monitor Docker Engine we need to enable **Open Telemetry** service, which can be turned up from `log-analyser.yml`. Use the following command:

   ```bash
   docker-compose -f docker-compose.yml -f prometheus.yml -f grafana.yml -f log-analyser.yml up -d
   ```

## Usage

1. **View the Prometheus Targets**

   Open [http://localhost:9090](http://localhost:9090) in your browser.

2. **Access Grafana Dashboard**

   Open [http://localhost:3000](http://localhost:3000) in your browser.

3. **Import Dashboard**

   Import the provided JSON file in Grafana to visualize Jitsi metrics.

## Contributer

[@24kushang](https://github.com/24kushang).
````

## File: prosody/rootfs/defaults/conf.d/brewery.cfg.lua
````lua
{{ $REGION_NAME := .Env.PROSODY_REGION_NAME | default "default" -}}
{{ $RELEASE_NUMBER := .Env.RELEASE_NUMBER | default "" -}}
{{ $SHARD_NAME := .Env.SHARD | default "default" -}}
{{ $JVB_XMPP_AUTH_DOMAIN := .Env.JVB_XMPP_AUTH_DOMAIN | default "auth.jvb.meet.jitsi" -}}
{{ $JVB_XMPP_INTERNAL_MUC_DOMAIN := .Env.JVB_XMPP_INTERNAL_MUC_DOMAIN | default "muc.jvb.meet.jitsi" -}}
{{ $JVB_AUTH_USER := .Env.JVB_AUTH_USER | default "jvb" -}}

admins = {
    "focus@{{ $JVB_XMPP_AUTH_DOMAIN }}",
    "{{ $JVB_AUTH_USER }}@{{ $JVB_XMPP_AUTH_DOMAIN }}"
}

plugin_paths = { "/prosody-plugins/", "/prosody-plugins-custom", "/prosody-plugins-contrib" }

VirtualHost "{{ $JVB_XMPP_AUTH_DOMAIN }}"
    modules_enabled = {
        "smacks";
    }
    authentication = "internal_hashed"
    ssl = {
        key = "/config/certs/{{ $JVB_XMPP_AUTH_DOMAIN }}.key";
        certificate = "/config/certs/{{ $JVB_XMPP_AUTH_DOMAIN }}.crt";
    }
    smacks_hibernation_time = 15;

Component "{{ $JVB_XMPP_INTERNAL_MUC_DOMAIN }}" "muc"
    modules_enabled = {
      "muc_hide_all";
      "muc_filter_access";
    }
    storage = "memory"
    muc_room_cache_size = 10000
    muc_filter_whitelist="{{ $JVB_XMPP_AUTH_DOMAIN }}"
    muc_room_locking = false
    muc_room_default_public_jids = true
````

## File: prosody/rootfs/defaults/conf.d/jitsi-meet.cfg.lua
````lua
{{ $AUTH_TYPE := .Env.AUTH_TYPE | default "internal" -}}
{{ $C2S_REQUIRE_ENCRYPTION := .Env.PROSODY_C2S_REQUIRE_ENCRYPTION | default "1" | toBool -}}
{{ $DISABLE_POLLS := .Env.DISABLE_POLLS | default "false" | toBool -}}
{{ $ENABLE_APP_SECRET := .Env.JWT_APP_SECRET | default "false" | toBool -}}
{{ $ENABLE_AUTH := .Env.ENABLE_AUTH | default "0" | toBool -}}
{{ $ENABLE_AV_MODERATION := .Env.ENABLE_AV_MODERATION | default "true" | toBool -}}
{{ $ENABLE_BREAKOUT_ROOMS := .Env.ENABLE_BREAKOUT_ROOMS | default "true" | toBool -}}
{{ $ENABLE_END_CONFERENCE := .Env.ENABLE_END_CONFERENCE | default "true" | toBool -}}
{{ $ENABLE_GUEST_DOMAIN := and $ENABLE_AUTH (.Env.ENABLE_GUESTS | default "0" | toBool) -}}
{{ $ENABLE_JAAS_COMPONENTS := .Env.ENABLE_JAAS_COMPONENTS | default "0" | toBool -}}
{{ $ENABLE_LOBBY := .Env.ENABLE_LOBBY | default "true" | toBool -}}
{{ $ENABLE_RATE_LIMITS := .Env.PROSODY_ENABLE_RATE_LIMITS | default "0" | toBool -}}
{{ $ENABLE_RECORDING := .Env.ENABLE_RECORDING | default "0" | toBool -}}
{{ $ENABLE_RECORDING_METADATA := .Env.PROSODY_ENABLE_RECORDING_METADATA | default "1" | toBool -}}
{{ $ENABLE_SUBDOMAINS := .Env.ENABLE_SUBDOMAINS | default "true" | toBool -}}
{{ $ENABLE_TRANSCRIPTIONS := .Env.ENABLE_TRANSCRIPTIONS | default "0" | toBool -}}
{{ $ENABLE_VISITORS := .Env.ENABLE_VISITORS | default "0" | toBool -}}
{{ $ENABLE_XMPP_WEBSOCKET := .Env.ENABLE_XMPP_WEBSOCKET | default "1" | toBool -}}
{{ $ENV := .Env -}}
{{ $GUEST_AUTH_TYPE := .Env.PROSODY_GUEST_AUTH_TYPE | default "jitsi-anonymous" -}}
{{ $JIBRI_RECORDER_USER := .Env.JIBRI_RECORDER_USER | default "recorder" -}}
{{ $JIBRI_XMPP_USER := .Env.JIBRI_XMPP_USER | default "jibri" -}}
{{ $JIGASI_TRANSCRIBER_USER := .Env.JIGASI_TRANSCRIBER_USER | default "transcriber" -}}
{{ $JIGASI_XMPP_USER := .Env.JIGASI_XMPP_USER | default "jigasi" -}}
{{ $JVB_AUTH_USER := .Env.JVB_AUTH_USER | default "jvb" -}}
{{ $JWT_ALLOW_EMPTY := .Env.JWT_ALLOW_EMPTY | default "0" | toBool -}}
{{ $JWT_ASAP_KEYSERVER := .Env.JWT_ASAP_KEYSERVER | default "" -}}
{{ $JWT_AUTH_TYPE := .Env.JWT_AUTH_TYPE | default "token" -}}
{{ $JWT_ENABLE_DOMAIN_VERIFICATION := .Env.JWT_ENABLE_DOMAIN_VERIFICATION | default "false" | toBool -}}
{{ $JWT_TOKEN_AUTH_MODULE := .Env.JWT_TOKEN_AUTH_MODULE | default "token_verification" -}}
{{ $MATRIX_LOBBY_BYPASS := .Env.MATRIX_LOBBY_BYPASS | default "0" | toBool -}}
{{ $MATRIX_UVS_ISSUER := .Env.MATRIX_UVS_ISSUER | default "issuer" -}}
{{ $MATRIX_UVS_SYNC_POWER_LEVELS := .Env.MATRIX_UVS_SYNC_POWER_LEVELS | default "0" | toBool -}}
{{ $PROSODY_AUTH_TYPE := .Env.PROSODY_AUTH_TYPE | default $AUTH_TYPE -}}
{{ $PROSODY_RESERVATION_ENABLED := .Env.PROSODY_RESERVATION_ENABLED | default "false" | toBool -}}
{{ $PROSODY_RESERVATION_REST_BASE_URL := .Env.PROSODY_RESERVATION_REST_BASE_URL | default "" -}}
{{ $PUBLIC_URL := .Env.PUBLIC_URL | default "https://localhost:8443" -}}
{{ $PUBLIC_URL_DOMAIN := $PUBLIC_URL | trimPrefix "https://" | trimSuffix "/" -}}
{{ $RATE_LIMIT_ALLOW_RANGES := .Env.PROSODY_RATE_LIMIT_ALLOW_RANGES | default "10.0.0.0/8" -}}
{{ $RATE_LIMIT_CACHE_SIZE := .Env.PROSODY_RATE_LIMIT_CACHE_SIZE | default "10000" -}}
{{ $RATE_LIMIT_LOGIN_RATE := .Env.PROSODY_RATE_LIMIT_LOGIN_RATE | default "3" -}}
{{ $RATE_LIMIT_SESSION_RATE := .Env.PROSODY_RATE_LIMIT_SESSION_RATE | default "200" -}}
{{ $RATE_LIMIT_TIMEOUT := .Env.PROSODY_RATE_LIMIT_TIMEOUT | default "60" -}}
{{ $XMPP_AUTH_DOMAIN := .Env.XMPP_AUTH_DOMAIN | default "auth.meet.jitsi" -}}
{{ $XMPP_DOMAIN := .Env.XMPP_DOMAIN | default "meet.jitsi" -}}
{{ $XMPP_GUEST_DOMAIN := .Env.XMPP_GUEST_DOMAIN | default "guest.meet.jitsi" -}}
{{ $XMPP_INTERNAL_MUC_DOMAIN := .Env.XMPP_INTERNAL_MUC_DOMAIN | default "internal-muc.meet.jitsi" -}}
{{ $XMPP_MUC_DOMAIN := .Env.XMPP_MUC_DOMAIN | default "muc.meet.jitsi" -}}
{{ $XMPP_MUC_DOMAIN_PREFIX := (split "." $XMPP_MUC_DOMAIN)._0 -}}
{{ $XMPP_HIDDEN_DOMAIN := .Env.XMPP_HIDDEN_DOMAIN | default "hidden.meet.jitsi" -}}

admins = {
    {{ if .Env.JIGASI_XMPP_PASSWORD }}
    "{{ $JIGASI_XMPP_USER }}@{{ $XMPP_AUTH_DOMAIN }}",
    {{ end }}

    {{ if .Env.JIBRI_XMPP_PASSWORD }}
    "{{ $JIBRI_XMPP_USER }}@{{ $XMPP_AUTH_DOMAIN }}",
    {{ end }}

    "focus@{{ $XMPP_AUTH_DOMAIN }}",
    "{{ $JVB_AUTH_USER }}@{{ $XMPP_AUTH_DOMAIN }}"
}

unlimited_jids = {
    "focus@{{ $XMPP_AUTH_DOMAIN }}",
    "{{ $JVB_AUTH_USER }}@{{ $XMPP_AUTH_DOMAIN }}"
}

plugin_paths = { "/prosody-plugins/", "/prosody-plugins-custom", "/prosody-plugins-contrib" }

muc_mapper_domain_base = "{{ $XMPP_DOMAIN }}";
muc_mapper_domain_prefix = "{{ $XMPP_MUC_DOMAIN_PREFIX }}";

recorder_prefixes = { "{{ $JIBRI_RECORDER_USER }}@{{ $XMPP_HIDDEN_DOMAIN }}" };

http_default_host = "{{ $XMPP_DOMAIN }}"

{{ if and $ENABLE_AUTH (or (eq $PROSODY_AUTH_TYPE "jwt") (eq $PROSODY_AUTH_TYPE "hybrid_matrix_token")) .Env.JWT_ACCEPTED_ISSUERS }}
asap_accepted_issuers = { "{{ join "\",\"" (splitList "," .Env.JWT_ACCEPTED_ISSUERS | compact) }}" }
{{ end }}

{{ if and $ENABLE_AUTH (or (eq $PROSODY_AUTH_TYPE "jwt") (eq $PROSODY_AUTH_TYPE "hybrid_matrix_token")) .Env.JWT_ACCEPTED_AUDIENCES }}
asap_accepted_audiences = { "{{ join "\",\"" (splitList "," .Env.JWT_ACCEPTED_AUDIENCES | compact) }}" }
{{ end }}

consider_bosh_secure = true;
consider_websocket_secure = true;

{{ if $ENABLE_XMPP_WEBSOCKET }}
smacks_max_unacked_stanzas = 5;
smacks_hibernation_time = 60;
smacks_max_old_sessions = 1;
{{ end }}

{{ if $ENABLE_JAAS_COMPONENTS }}
VirtualHost "jigasi.meet.jitsi"
    modules_enabled = {
      "bosh";
      "muc_password_check";
    }
    authentication = "token"
    app_id = "jitsi";
    asap_key_server = "https://jaas-public-keys.jitsi.net/jitsi-components/prod-8x8"
    asap_accepted_issuers = { "jaas-components" }
    asap_accepted_audiences = { "jigasi.{{ $PUBLIC_URL_DOMAIN }}" }
{{ end }}

VirtualHost "{{ $XMPP_DOMAIN }}"
{{ if $ENABLE_AUTH }}
  {{ if eq $PROSODY_AUTH_TYPE "jwt" }}
  {{ if .Env.JWT_SIGN_TYPE }}
       signature_algorithm = "{{ .Env.JWT_SIGN_TYPE }}"
    {{ end -}}
    authentication = "{{ $JWT_AUTH_TYPE }}"
    app_id = "{{ .Env.JWT_APP_ID }}"
    {{ if $ENABLE_APP_SECRET }}
    app_secret = "{{ .Env.JWT_APP_SECRET }}"
    {{ end }}
    allow_empty_token = {{ $JWT_ALLOW_EMPTY }}
    {{ if $JWT_ASAP_KEYSERVER }}
    asap_key_server = "{{ .Env.JWT_ASAP_KEYSERVER }}"
    {{ end }}
    enable_domain_verification = {{ $JWT_ENABLE_DOMAIN_VERIFICATION }}
  {{ else if eq $PROSODY_AUTH_TYPE "ldap" }}
    authentication = "cyrus"
    cyrus_application_name = "xmpp"
    allow_unencrypted_plain_auth = true
  {{ else if eq $PROSODY_AUTH_TYPE "matrix" }}
    authentication = "matrix_user_verification"
    app_id = "{{ $MATRIX_UVS_ISSUER }}"
    uvs_base_url = "{{ .Env.MATRIX_UVS_URL }}"
    {{ if .Env.MATRIX_UVS_AUTH_TOKEN }}
    uvs_auth_token = "{{ .Env.MATRIX_UVS_AUTH_TOKEN }}"
    {{ end }}
    {{ if $MATRIX_UVS_SYNC_POWER_LEVELS }}
    uvs_sync_power_levels = true
    {{ end }}
  {{ else if eq $PROSODY_AUTH_TYPE "hybrid_matrix_token" }}
    authentication = "hybrid_matrix_token"
    app_id = "{{ .Env.JWT_APP_ID }}"
    {{ if $ENABLE_APP_SECRET }}
    app_secret = "{{ .Env.JWT_APP_SECRET }}"
    {{ end }}
    allow_empty_token = {{ $JWT_ALLOW_EMPTY }}
    enable_domain_verification = {{ $JWT_ENABLE_DOMAIN_VERIFICATION }}

    uvs_base_url = "{{ .Env.MATRIX_UVS_URL }}"
    {{ if .Env.MATRIX_UVS_ISSUER }}
    uvs_issuer = "{{ .Env.MATRIX_UVS_ISSUER }}"
    {{ end }}
    {{ if .Env.MATRIX_UVS_AUTH_TOKEN }}
    uvs_auth_token = "{{ .Env.MATRIX_UVS_AUTH_TOKEN }}"
    {{ end }}
  {{ else if eq $PROSODY_AUTH_TYPE "internal" }}
    authentication = "internal_hashed"
  {{ end }}
{{ else }}
    authentication = "jitsi-anonymous"
{{ end }}
    ssl = {
        key = "/config/certs/{{ $XMPP_DOMAIN }}.key";
        certificate = "/config/certs/{{ $XMPP_DOMAIN }}.crt";
    }
    modules_enabled = {
        "bosh";
        {{ if $ENABLE_XMPP_WEBSOCKET }}
        "websocket";
        "smacks"; -- XEP-0198: Stream Management
        {{ end }}
        "speakerstats";
        "conference_duration";
        "room_metadata";
        {{ if $ENABLE_END_CONFERENCE }}
        "end_conference";
        {{ end }}
        {{ if $ENABLE_LOBBY }}
        "muc_lobby_rooms";
        {{ end }}
        {{ if $ENABLE_BREAKOUT_ROOMS }}
        "muc_breakout_rooms";
        {{ end }}
        {{ if $ENABLE_AV_MODERATION }}
        "av_moderation";
        {{ end }}
        {{ if .Env.XMPP_MODULES }}
        "{{ join "\";\n        \"" (splitList "," .Env.XMPP_MODULES | compact) }}";
        {{ end }}
        {{ if and $ENABLE_AUTH (eq $PROSODY_AUTH_TYPE "ldap") }}
        "auth_cyrus";
        {{end}}
        {{ if $PROSODY_RESERVATION_ENABLED }}
        "reservations";
        {{ end }}
        {{ if $ENABLE_VISITORS }}
        "visitors";
        {{ end }}
        {{- if and $ENABLE_RECORDING_METADATA $ENABLE_AUTH (eq $PROSODY_AUTH_TYPE "jwt") $ENABLE_RECORDING }}
        "jibri_session";
        {{- end }}

    }

    main_muc = "{{ $XMPP_MUC_DOMAIN }}"
    room_metadata_component = "metadata.{{ $XMPP_DOMAIN }}"
    {{ if $ENABLE_LOBBY }}
    lobby_muc = "lobby.{{ $XMPP_DOMAIN }}"
    {{ if or $ENABLE_RECORDING $ENABLE_TRANSCRIPTIONS }}
    muc_lobby_whitelist = { "{{ $XMPP_HIDDEN_DOMAIN }}" }
    {{ end }}
    {{ end }}

    {{ if $PROSODY_RESERVATION_ENABLED }}
    reservations_api_prefix = "{{ $PROSODY_RESERVATION_REST_BASE_URL }}"
    {{ end }}

    {{ if $ENABLE_BREAKOUT_ROOMS }}
    breakout_rooms_muc = "breakout.{{ $XMPP_DOMAIN }}"
    {{ end }}

    speakerstats_component = "speakerstats.{{ $XMPP_DOMAIN }}"
    conference_duration_component = "conferenceduration.{{ $XMPP_DOMAIN }}"

    {{ if $ENABLE_END_CONFERENCE }}
    end_conference_component = "endconference.{{ $XMPP_DOMAIN }}"
    {{ end }}

    {{ if $ENABLE_AV_MODERATION }}
    av_moderation_component = "avmoderation.{{ $XMPP_DOMAIN }}"
    {{ end }}

    c2s_require_encryption = {{ $C2S_REQUIRE_ENCRYPTION }}

    {{ if $ENABLE_VISITORS -}}
    visitors_ignore_list = { "{{ $XMPP_HIDDEN_DOMAIN }}" }
    {{ end }}

    {{ if .Env.XMPP_CONFIGURATION -}}
    {{ join "\n    " (splitList "," .Env.XMPP_CONFIGURATION | compact) }}
    {{ end -}}

{{ if $ENABLE_GUEST_DOMAIN }}
VirtualHost "{{ $XMPP_GUEST_DOMAIN }}"
    authentication = "{{ $GUEST_AUTH_TYPE }}"
    modules_enabled = {
        {{ if $ENABLE_XMPP_WEBSOCKET }}
        "smacks"; -- XEP-0198: Stream Management
        {{ end }}
    }

    c2s_require_encryption = {{ $C2S_REQUIRE_ENCRYPTION }}
    {{ if $ENABLE_VISITORS }}
    allow_anonymous_s2s = true
    {{ end }}

{{ end }}

VirtualHost "{{ $XMPP_AUTH_DOMAIN }}"
    ssl = {
        key = "/config/certs/{{ $XMPP_AUTH_DOMAIN }}.key";
        certificate = "/config/certs/{{ $XMPP_AUTH_DOMAIN }}.crt";
    }
    modules_enabled = {
        "limits_exception";
        {{- if and $ENABLE_RECORDING_METADATA $ENABLE_AUTH (eq $PROSODY_AUTH_TYPE "jwt") $ENABLE_RECORDING }}
        "jibri_session";
        {{- end }}
        "smacks";
    }
    authentication = "internal_hashed"
    smacks_hibernation_time = 15;

{{ if or $ENABLE_RECORDING $ENABLE_TRANSCRIPTIONS }}
VirtualHost "{{ $XMPP_HIDDEN_DOMAIN }}"
    modules_enabled = {
      "smacks";
    }
    authentication = "internal_hashed"
{{ end }}

Component "{{ $XMPP_INTERNAL_MUC_DOMAIN }}" "muc"
    storage = "memory"
    modules_enabled = {
        "muc_hide_all";
        "muc_filter_access";
        {{ if .Env.XMPP_INTERNAL_MUC_MODULES -}}
        "{{ join "\";\n\"" (splitList "," .Env.XMPP_INTERNAL_MUC_MODULES | compact) }}";
        {{ end -}}
    }
    restrict_room_creation = true
    muc_filter_whitelist="{{ $XMPP_AUTH_DOMAIN }}"
    muc_room_locking = false
    muc_room_default_public_jids = true
    muc_room_cache_size = 1000
    muc_tombstones = false
    muc_room_allow_persistent = false

Component "{{ $XMPP_MUC_DOMAIN }}" "muc"
    restrict_room_creation = true
    storage = "memory"
    modules_enabled = {
        "muc_meeting_id";
        {{ if .Env.XMPP_MUC_MODULES -}}
        "{{ join "\";\n        \"" (splitList "," .Env.XMPP_MUC_MODULES | compact) }}";
        {{ end -}}
        {{ if and $ENABLE_AUTH (or (eq $PROSODY_AUTH_TYPE "jwt") (eq $PROSODY_AUTH_TYPE "hybrid_matrix_token")) -}}
        "{{ $JWT_TOKEN_AUTH_MODULE }}";
        {{ end }}
        {{ if and $ENABLE_AUTH (eq $PROSODY_AUTH_TYPE "matrix") $MATRIX_UVS_SYNC_POWER_LEVELS -}}
        "matrix_power_sync";
        {{ end -}}
        {{ if and $ENABLE_AUTH (eq $PROSODY_AUTH_TYPE "hybrid_matrix_token") $MATRIX_UVS_SYNC_POWER_LEVELS -}}
        "matrix_affiliation";
        {{ end -}}
        {{ if and $ENABLE_AUTH (eq $PROSODY_AUTH_TYPE "hybrid_matrix_token") $MATRIX_LOBBY_BYPASS -}}
        "matrix_lobby_bypass";
        {{ end -}}
        {{ if not $DISABLE_POLLS -}}
        "polls";
        {{ end -}}
        {{ if $ENABLE_SUBDOMAINS -}}
        "muc_domain_mapper";
        {{ end -}}
        {{ if $ENABLE_RATE_LIMITS -}}
        "muc_rate_limit";
        "rate_limit";
        {{ end -}}
        {{ if .Env.MAX_PARTICIPANTS }}
        "muc_max_occupants";
        {{ end }}
        "muc_password_whitelist";
    }

    {{ if $ENABLE_RATE_LIMITS -}}
    -- Max allowed join/login rate in events per second.
    rate_limit_login_rate = {{ $RATE_LIMIT_LOGIN_RATE }};
    -- The rate to which sessions from IPs exceeding the join rate will be limited, in bytes per second.
    rate_limit_session_rate = {{ $RATE_LIMIT_SESSION_RATE }};
    -- The time in seconds, after which the limit for an IP address is lifted.
    rate_limit_timeout = {{ $RATE_LIMIT_TIMEOUT }};
    -- List of regular expressions for IP addresses that are not limited by this module.
    rate_limit_whitelist = {
        "127.0.0.1";
{{ range $index, $cidr := (splitList "," $RATE_LIMIT_ALLOW_RANGES | compact) }}
        "{{ $cidr }}";
{{ end }}
    };

    rate_limit_whitelist_hosts = {
        "{{ $XMPP_HIDDEN_DOMAIN }}";
    }
    {{ end -}}

	-- The size of the cache that saves state for IP addresses
    rate_limit_cache_size = {{ $RATE_LIMIT_CACHE_SIZE }};

    muc_room_cache_size = 10000
    muc_room_locking = false
    muc_room_default_public_jids = true
    {{ if .Env.XMPP_MUC_CONFIGURATION -}}
    {{ join "\n    " (splitList "," .Env.XMPP_MUC_CONFIGURATION | compact) }}
    {{ end -}}
    {{ if .Env.MAX_PARTICIPANTS }}
    muc_access_whitelist = {
        "focus@{{ $XMPP_AUTH_DOMAIN }}";
        {{- if $ENABLE_RECORDING }}
        "{{ $JIBRI_RECORDER_USER }}@{{ $XMPP_HIDDEN_DOMAIN }}";
        {{- end }}
        {{- if $ENABLE_TRANSCRIPTIONS }}
        "{{ $JIGASI_TRANSCRIBER_USER }}@{{ $XMPP_HIDDEN_DOMAIN }}";
        {{- end }}
    }
    muc_max_occupants = "{{ .Env.MAX_PARTICIPANTS }}"
    {{ end }}
    muc_password_whitelist = {
        "focus@{{ $XMPP_AUTH_DOMAIN }}";
{{- if $ENABLE_RECORDING }}
        "{{ $JIBRI_RECORDER_USER }}@{{ $XMPP_HIDDEN_DOMAIN }}";
{{- end }}
{{- if $ENABLE_TRANSCRIPTIONS }}
        "{{ $JIGASI_TRANSCRIBER_USER }}@{{ $XMPP_HIDDEN_DOMAIN }}";
{{- end }}
    }
    muc_tombstones = false
    muc_room_allow_persistent = false

Component "focus.{{ $XMPP_DOMAIN }}" "client_proxy"
    target_address = "focus@{{ $XMPP_AUTH_DOMAIN }}"

Component "speakerstats.{{ $XMPP_DOMAIN }}" "speakerstats_component"
    muc_component = "{{ $XMPP_MUC_DOMAIN }}"
    {{- if .Env.XMPP_SPEAKERSTATS_MODULES }}
    modules_enabled = {
        "{{ join "\";\n        \"" (splitList "," .Env.XMPP_SPEAKERSTATS_MODULES | compact) }}";
    }
    {{- end }}

Component "conferenceduration.{{ $XMPP_DOMAIN }}" "conference_duration_component"
    muc_component = "{{ $XMPP_MUC_DOMAIN }}"

{{ if $ENABLE_END_CONFERENCE }}
Component "endconference.{{ $XMPP_DOMAIN }}" "end_conference"
    muc_component = "{{ $XMPP_MUC_DOMAIN }}"
{{ end }}

{{ if $ENABLE_AV_MODERATION }}
Component "avmoderation.{{ $XMPP_DOMAIN }}" "av_moderation_component"
    muc_component = "{{ $XMPP_MUC_DOMAIN }}"
{{ end }}

{{ if $ENABLE_LOBBY }}
Component "lobby.{{ $XMPP_DOMAIN }}" "muc"
    storage = "memory"
    restrict_room_creation = true
    muc_tombstones = false
    muc_room_allow_persistent = false
    muc_room_cache_size = 10000
    muc_room_locking = false
    muc_room_default_public_jids = true
    {{- if .Env.MAX_PARTICIPANTS }}
    muc_max_occupants = "{{ .Env.MAX_PARTICIPANTS }}"
    {{- end }}
    modules_enabled = {
        {{- if $ENABLE_RATE_LIMITS }}
        "muc_rate_limit";
        {{- end }}
        {{- if .Env.MAX_PARTICIPANTS }}
        "muc_max_occupants";
        {{- end }}
        {{- if .Env.XMPP_LOBBY_MUC_MODULES }}
        "{{ join "\";\n        \"" (splitList "," .Env.XMPP_LOBBY_MUC_MODULES | compact) }}";
        {{- end }}
    }

    {{ end }}

{{ if $ENABLE_BREAKOUT_ROOMS }}
Component "breakout.{{ $XMPP_DOMAIN }}" "muc"
    storage = "memory"
    restrict_room_creation = true
    muc_room_cache_size = 10000
    muc_room_locking = false
    muc_room_default_public_jids = true
    muc_tombstones = false
    muc_room_allow_persistent = false
    modules_enabled = {
        "muc_meeting_id";
        {{ if not $DISABLE_POLLS -}}
        "polls";
        {{ end -}}
        {{ if $ENABLE_RATE_LIMITS -}}
        "muc_rate_limit";
        {{ end -}}
        {{ if .Env.XMPP_BREAKOUT_MUC_MODULES -}}
        "{{ join "\";\n        \"" (splitList "," .Env.XMPP_BREAKOUT_MUC_MODULES | compact) }}";
        {{ end -}}
    }
{{ end }}

Component "metadata.{{ $XMPP_DOMAIN }}" "room_metadata_component"
    muc_component = "{{ $XMPP_MUC_DOMAIN }}"
    breakout_rooms_component = "breakout.{{ $XMPP_DOMAIN }}"


{{ if $ENABLE_VISITORS }}
Component "visitors.{{ $XMPP_DOMAIN }}" "visitors_component"
    auto_allow_visitor_promotion = true
    always_visitors_enabled = true
{{ end }}
````

## File: prosody/rootfs/defaults/conf.d/visitors.cfg.lua
````lua
{{ $ENABLE_AUTH := .Env.ENABLE_AUTH | default "0" | toBool -}}
{{ $ENABLE_GUEST_DOMAIN := and $ENABLE_AUTH (.Env.ENABLE_GUESTS | default "0" | toBool) -}}
{{ $ENABLE_RATE_LIMITS := .Env.PROSODY_ENABLE_RATE_LIMITS | default "0" | toBool -}}
{{ $ENABLE_RECORDING := .Env.ENABLE_RECORDING | default "0" | toBool -}}
{{ $ENABLE_SUBDOMAINS := .Env.ENABLE_SUBDOMAINS | default "true" | toBool -}}
{{ $ENABLE_TRANSCRIPTIONS := .Env.ENABLE_TRANSCRIPTIONS | default "0" | toBool -}}
{{ $ENABLE_XMPP_WEBSOCKET := .Env.ENABLE_XMPP_WEBSOCKET | default "1" | toBool -}}
{{ $JIBRI_RECORDER_USER := .Env.JIBRI_RECORDER_USER | default "recorder" -}}
{{ $JIGASI_TRANSCRIBER_USER := .Env.JIGASI_TRANSCRIBER_USER | default "transcriber" -}}
{{ $LIMIT_MESSAGES_CHECK_TOKEN := .Env.PROSODY_LIMIT_MESSAGES_CHECK_TOKEN | default "0" | toBool -}}
{{ $RATE_LIMIT_LOGIN_RATE := .Env.PROSODY_RATE_LIMIT_LOGIN_RATE | default "3" -}}
{{ $RATE_LIMIT_SESSION_RATE := .Env.PROSODY_RATE_LIMIT_SESSION_RATE | default "200" -}}
{{ $RATE_LIMIT_TIMEOUT := .Env.PROSODY_RATE_LIMIT_TIMEOUT | default "60" -}}
{{ $RATE_LIMIT_ALLOW_RANGES := .Env.PROSODY_RATE_LIMIT_ALLOW_RANGES | default "10.0.0.0/8" -}}
{{ $RATE_LIMIT_CACHE_SIZE := .Env.PROSODY_RATE_LIMIT_CACHE_SIZE | default "10000" -}}
{{ $REGION_NAME := .Env.PROSODY_REGION_NAME | default "default" -}}
{{ $RELEASE_NUMBER := .Env.RELEASE_NUMBER | default "" -}}
{{ $SHARD_NAME := .Env.SHARD | default "default" -}}
{{ $S2S_PORT := .Env.PROSODY_S2S_PORT | default "5269" -}}
{{ $VISITOR_INDEX := .Env.PROSODY_VISITOR_INDEX | default "0" -}}
{{ $VISITORS_MUC_PREFIX := .Env.PROSODY_VISITORS_MUC_PREFIX | default "muc" -}}
{{ $VISITORS_MAX_VISITORS_PER_NODE := .Env.VISITORS_MAX_VISITORS_PER_NODE | default "250" }}
{{ $VISITORS_XMPP_DOMAIN := .Env.VISITORS_XMPP_DOMAIN | default "meet.jitsi" -}}
{{ $XMPP_AUTH_DOMAIN := .Env.XMPP_AUTH_DOMAIN | default "auth.meet.jitsi" -}}
{{ $XMPP_DOMAIN := .Env.XMPP_DOMAIN | default "meet.jitsi" -}}
{{ $XMPP_GUEST_DOMAIN := .Env.XMPP_GUEST_DOMAIN | default "guest.meet.jitsi" -}}
{{ $XMPP_MUC_DOMAIN := .Env.XMPP_MUC_DOMAIN | default "muc.meet.jitsi" -}}
{{ $XMPP_MUC_DOMAIN_PREFIX := (split "." $XMPP_MUC_DOMAIN)._0 -}}
{{ $XMPP_SERVER := .Env.XMPP_SERVER | default "xmpp.meet.jitsi" -}}
{{ $XMPP_SERVER_S2S_PORT := .Env.XMPP_SERVER_S2S_PORT | default $S2S_PORT -}}
{{ $XMPP_HIDDEN_DOMAIN := .Env.XMPP_HIDDEN_DOMAIN | default "hidden.meet.jitsi" -}}

plugin_paths = { "/prosody-plugins/", "/prosody-plugins-custom", "/prosody-plugins-contrib" }

muc_mapper_domain_base = "v{{ $VISITOR_INDEX }}.{{ $VISITORS_XMPP_DOMAIN }}";
muc_mapper_domain_prefix = "{{ $XMPP_MUC_DOMAIN_PREFIX }}";

http_default_host = "v{{ $VISITOR_INDEX }}.{{ $VISITORS_XMPP_DOMAIN }}"

main_domain = '{{ $XMPP_DOMAIN }}';

-- https://prosody.im/doc/modules/mod_smacks
smacks_max_unacked_stanzas = 5;
smacks_hibernation_time = 60;
-- this is dropped in 0.12
smacks_max_hibernated_sessions = 1;
smacks_max_old_sessions = 1;

unlimited_jids = { "focus@{{ $XMPP_AUTH_DOMAIN }}" }
limits = {
    c2s = {
        rate = "512kb/s";
    };
    s2sin = {
        rate = "512kb/s";
    };
}

authentication = 'internal_hashed'
storage = 'internal'

consider_websocket_secure = true;
consider_bosh_secure = true;
bosh_max_inactivity = 60;

-- this is added to make certs_s2soutinjection work
s2sout_override = {
    ["{{ $XMPP_MUC_DOMAIN }}"] = "tcp://{{ $XMPP_SERVER }}:{{ $XMPP_SERVER_S2S_PORT }}"; -- needed for visitors to send messages to main room
    ["{{ $XMPP_DOMAIN }}"] = "tcp://{{ $XMPP_SERVER }}:{{ $XMPP_SERVER_S2S_PORT }}";
    ["visitors.{{ $XMPP_DOMAIN }}"] = "tcp://{{ $XMPP_SERVER }}:{{ $XMPP_SERVER_S2S_PORT }}";
{{ if $ENABLE_GUEST_DOMAIN -}}
    ["{{ $XMPP_GUEST_DOMAIN }}"] = "tcp://{{ $XMPP_SERVER }}:{{ $XMPP_SERVER_S2S_PORT }}";
{{ end -}}
{{ if or $ENABLE_RECORDING $ENABLE_TRANSCRIPTIONS -}}
    ["{{ $XMPP_HIDDEN_DOMAIN }}"] = "tcp://{{ $XMPP_SERVER }}:{{ $XMPP_SERVER_S2S_PORT }}";
{{ end -}}
{{ if .Env.PROSODY_VISITORS_S2S_VHOSTS -}}
  {{- range $index, $vhost := (splitList "," .Env.PROSODY_VISITORS_S2S_VHOSTS | compact) }}
    ["{{ $vhost }}"] = "tcp://{{ $XMPP_SERVER }}:{{ $XMPP_SERVER_S2S_PORT }}";
  {{ end -}}
{{ end -}}

}

muc_limit_messages_count = 10;
muc_limit_messages_check_token = {{ $LIMIT_MESSAGES_CHECK_TOKEN }};

----------- Virtual hosts -----------
VirtualHost 'v{{ $VISITOR_INDEX }}.{{ $VISITORS_XMPP_DOMAIN }}'
    authentication = 'jitsi-anonymous'
    ssl = {
        key = "/config/certs/v{{ $VISITOR_INDEX }}.{{ $VISITORS_XMPP_DOMAIN }}.key";
        certificate = "/config/certs/v{{ $VISITOR_INDEX }}.{{ $VISITORS_XMPP_DOMAIN }}.crt";
    }
    modules_enabled = {
      'bosh';
      "external_services";
      {{ if $ENABLE_XMPP_WEBSOCKET -}}
      "websocket";
      "smacks"; -- XEP-0198: Stream Management
      {{ end -}}
      {{ if .Env.XMPP_MODULES }}
      "{{ join "\";\n\"" (splitList "," .Env.XMPP_MODULES | compact) }}";
      {{ end }}
    }
    main_muc = '{{ $VISITORS_MUC_PREFIX }}.v{{ $VISITOR_INDEX }}.{{ $VISITORS_XMPP_DOMAIN }}';
    shard_name = "{{ $SHARD_NAME }}"
    region_name = "{{ $REGION_NAME }}"
    release_number = "{{ $RELEASE_NUMBER }}"

    {{ if .Env.XMPP_CONFIGURATION -}}
    {{ join "\n    " (splitList "," .Env.XMPP_CONFIGURATION | compact) }}
    {{- end }}

VirtualHost '{{ $XMPP_AUTH_DOMAIN }}'
    modules_enabled = {
      'limits_exception';
      'smacks';
    }
    authentication = 'internal_hashed'
    smacks_hibernation_time = 15;

Component '{{ $VISITORS_MUC_PREFIX }}.v{{ $VISITOR_INDEX }}.{{ $VISITORS_XMPP_DOMAIN }}' 'muc'
    storage = 'memory'
    muc_room_cache_size = 10000
    restrict_room_creation = true
    modules_enabled = {
        "muc_hide_all";
        "muc_meeting_id";
        'fmuc';
        's2s_bidi';
        's2s_whitelist';
        's2sout_override';
        'muc_max_occupants';
        {{ if $ENABLE_SUBDOMAINS -}}
        "muc_domain_mapper";
        {{ end -}}
        {{ if $ENABLE_RATE_LIMITS -}}
        "muc_rate_limit";
        "rate_limit";
        {{ end -}}
        {{ if .Env.XMPP_MUC_MODULES -}}
        "{{ join "\";\n\"" (splitList "," .Env.XMPP_MUC_MODULES | compact) }}";
        {{ end -}}
      }
    muc_room_default_presence_broadcast = {
        visitor = false;
        participant = true;
        moderator = true;
    };
    muc_room_locking = false
    muc_room_default_public_jids = true
    muc_max_occupants = {{ $VISITORS_MAX_VISITORS_PER_NODE}}
    muc_access_whitelist = {
        "{{ $XMPP_DOMAIN }}";
    }
    muc_tombstones = false
    muc_room_allow_persistent = false

    {{ if $ENABLE_RATE_LIMITS -}}
    -- Max allowed join/login rate in events per second.
    rate_limit_login_rate = {{ $RATE_LIMIT_LOGIN_RATE }};
    -- The rate to which sessions from IPs exceeding the join rate will be limited, in bytes per second.
    rate_limit_session_rate = {{ $RATE_LIMIT_SESSION_RATE }};
    -- The time in seconds, after which the limit for an IP address is lifted.
    rate_limit_timeout = {{ $RATE_LIMIT_TIMEOUT }};
    -- List of regular expressions for IP addresses that are not limited by this module.
    rate_limit_whitelist = {
        "127.0.0.1";
        {{ range $index, $cidr := (splitList "," $RATE_LIMIT_ALLOW_RANGES) -}}
        "{{ $cidr }}";
        {{ end -}}
      };

    rate_limit_whitelist_jids = {
        "{{ $JIBRI_RECORDER_USER }}@{{ $XMPP_HIDDEN_DOMAIN }}",
        "{{ $JIGASI_TRANSCRIBER_USER }}@{{ $XMPP_HIDDEN_DOMAIN }}"
    }

    {{ end -}}

	-- The size of the cache that saves state for IP addresses
    rate_limit_cache_size = {{ $RATE_LIMIT_CACHE_SIZE }};

    muc_rate_joins = 30;
    {{ if .Env.XMPP_MUC_CONFIGURATION -}}
    {{ join "\n" (splitList "," .Env.XMPP_MUC_CONFIGURATION | compact) }}
    {{ end -}}
````

## File: prosody/rootfs/defaults/rules.d/jvb_muc_presence_filter.pfw
````
{{ $JVB_XMPP_AUTH_DOMAIN := .Env.JVB_XMPP_AUTH_DOMAIN | default "auth.jvb.meet.jitsi" -}}
{{ $JVB_XMPP_INTERNAL_MUC_DOMAIN := .Env.JVB_XMPP_INTERNAL_MUC_DOMAIN | default "muc.jvb.meet.jitsi" -}}
{{ $JVB_AUTH_USER := .Env.JVB_AUTH_USER | default "jvb" -}}
{{ $JVB_BREWERY_MUC := .Env.JVB_BREWERY_MUC | default "jvbbrewery" -}}
# Drop all presence from a jvb in a MUC to a jvb
FROM: {{ $JVB_BREWERY_MUC }}@{{ $JVB_XMPP_INTERNAL_MUC_DOMAIN }}
TO: {{ $JVB_AUTH_USER }}@{{ $JVB_XMPP_AUTH_DOMAIN }}
KIND: presence
# Seems safer to allow all "unavailable" to pass
TYPE: available
# Allow self-presence (code=110)
NOT INSPECT: {http://jabber.org/protocol/muc#user}x/status@code=110
DROP.
````

## File: prosody/rootfs/defaults/prosody.cfg.lua
````lua
{{ $C2S_REQUIRE_ENCRYPTION := .Env.PROSODY_C2S_REQUIRE_ENCRYPTION | default "1" | toBool -}}
{{ $DISABLE_C2S_LIMIT := .Env.PROSODY_DISABLE_C2S_LIMIT | default "0" | toBool -}}
{{ $DISABLE_S2S_LIMIT := .Env.PROSODY_DISABLE_S2S_LIMIT | default "0" | toBool -}}
{{ $ENABLE_AUTH := .Env.ENABLE_AUTH | default "0" | toBool -}}
{{ $ENABLE_GUEST_DOMAIN := and $ENABLE_AUTH (.Env.ENABLE_GUESTS | default "0" | toBool) -}}
{{ $ENABLE_IPV6 := .Env.ENABLE_IPV6 | default "true" | toBool -}}
{{ $ENABLE_RECORDING := .Env.ENABLE_RECORDING | default "0" | toBool -}}
{{ $ENABLE_TRANSCRIPTIONS := .Env.ENABLE_TRANSCRIPTIONS | default "0" | toBool -}}
{{ $ENABLE_VISITORS := .Env.ENABLE_VISITORS | default "0" | toBool -}}
{{ $ENABLE_S2S := or $ENABLE_VISITORS ( .Env.PROSODY_ENABLE_S2S | default "0" | toBool ) }}
{{ $GC_TYPE := .Env.GC_TYPE | default "incremental" -}}
{{ $GC_INC_TH := .Env.GC_INC_TH | default 400 -}}
{{ $GC_INC_SPEED := .Env.GC_INC_SPEED | default 250 -}}
{{ $GC_INC_STEP_SIZE := .Env.GC_INC_STEP_SIZE | default 13 -}}
{{ $GC_GEN_MIN_TH := .Env.GC_GEN_MIN_TH | default 20 -}}
{{ $GC_GEN_MAX_TH := .Env.GC_GEN_MAX_TH | default 100 -}}
{{ $LOG_LEVEL := .Env.LOG_LEVEL | default "info" }}
{{ $PROSODY_C2S_LIMIT := .Env.PROSODY_C2S_LIMIT | default "10kb/s" -}}
{{ $PROSODY_METRICS_ALLOWED_CIDR := .Env.PROSODY_METRICS_ALLOWED_CIDR | default "172.16.0.0/12" -}}
{{ $PROSODY_HTTP_PORT := .Env.PROSODY_HTTP_PORT | default "5280" -}}
{{ $PROSODY_ENABLE_METRICS := .Env.PROSODY_ENABLE_METRICS | default "false" | toBool -}}
{{ $PROSODY_ENABLE_STANZA_COUNTS := .Env.PROSODY_ENABLE_STANZA_COUNTS | default "false" | toBool -}}
{{ $PROSODY_ADMINS := .Env.PROSODY_ADMINS | default "" -}}
{{ $PROSODY_ADMIN_LIST := splitList "," $PROSODY_ADMINS | compact -}}
{{ $PROSODY_MODE := .Env.PROSODY_MODE | default "client" -}}
{{ $TRUSTED_PROXIES := .Env.PROSODY_TRUSTED_PROXIES | default "127.0.0.1,::1" -}}
{{ $TRUSTED_PROXY_LIST := splitList "," $TRUSTED_PROXIES | compact -}}
{{ $PROSODY_S2S_LIMIT := .Env.PROSODY_S2S_LIMIT | default "30kb/s" -}}
{{ $S2S_PORT := .Env.PROSODY_S2S_PORT | default "5269" }}
{{ $STUN_HOST := .Env.STUN_HOST | default "" -}}
{{ $STUN_PORT := .Env.STUN_PORT | default "443" -}}
{{ $TURNS_HOST := .Env.TURNS_HOST | default "" -}}
{{ $TURNS_HOSTS := splitList "," $TURNS_HOST | compact -}}
{{ $TURNS_PORT := .Env.TURNS_PORT | default "443" -}}
{{ $TURN_HOST := .Env.TURN_HOST | default "" -}}
{{ $TURN_HOSTS := splitList "," $TURN_HOST | compact -}}
{{ $TURN_PORT := .Env.TURN_PORT | default "443" -}}
{{ $TURN_TRANSPORT := .Env.TURN_TRANSPORT | default "tcp" -}}
{{ $TURN_TRANSPORTS := splitList "," $TURN_TRANSPORT | compact -}}
{{ $TURN_TTL := .Env.TURN_TTL | default "86400" -}}
{{ $VISITORS_MUC_PREFIX := .Env.PROSODY_VISITORS_MUC_PREFIX | default "muc" -}}
{{ $VISITORS_XMPP_DOMAIN := .Env.VISITORS_XMPP_DOMAIN | default "meet.jitsi" -}}
{{ $VISITORS_XMPP_SERVER := .Env.VISITORS_XMPP_SERVER | default "" -}}
{{ $VISITORS_XMPP_SERVERS := splitList "," $VISITORS_XMPP_SERVER | compact -}}
{{ $VISITORS_XMPP_PORT := .Env.VISITORS_XMPP_PORT | default 52220 }}
{{ $XMPP_DOMAIN := .Env.XMPP_DOMAIN | default "meet.jitsi" -}}
{{ $XMPP_GUEST_DOMAIN := .Env.XMPP_GUEST_DOMAIN | default "guest.meet.jitsi" -}}
{{ $XMPP_MUC_DOMAIN := .Env.XMPP_MUC_DOMAIN | default "muc.meet.jitsi" -}}
{{ $XMPP_PORT := .Env.XMPP_PORT | default "5222" -}}
{{ $XMPP_HIDDEN_DOMAIN := .Env.XMPP_HIDDEN_DOMAIN | default "hidden.meet.jitsi" -}}

-- Prosody Example Configuration File
--
-- Information on configuring Prosody can be found on our
-- website at http://prosody.im/doc/configure
--
-- Tip: You can check that the syntax of this file is correct
-- when you have finished by running: luac -p prosody.cfg.lua
-- If there are any errors, it will let you know what and where
-- they are, otherwise it will keep quiet.
--
-- The only thing left to do is rename this file to remove the .dist ending, and fill in the
-- blanks. Good luck, and happy Jabbering!


---------- Server-wide settings ----------
-- Settings in this section apply to the whole server and are the default settings
-- for any virtual hosts

-- This is a (by default, empty) list of accounts that are admins
-- for the server. Note that you must create the accounts separately
-- (see http://prosody.im/doc/creating_accounts for info)
-- Example: admins = { "user1@example.com", "user2@example.net" }
admins = { {{ if .Env.PROSODY_ADMINS }}{{ range $index, $element := $PROSODY_ADMIN_LIST -}}{{ if $index }}, {{ end }}"{{ $element }}"{{ end }}{{ end }} }
-- Enable use of libevent for better performance under high load
-- For more information see: http://prosody.im/doc/libevent
--use_libevent = true;

-- This is the list of modules Prosody will load on startup.
-- It looks for mod_modulename.lua in the plugins folder, so make sure that exists too.
-- Documentation on modules can be found at: http://prosody.im/doc/modules
modules_enabled = {

	-- Generally required
		"roster"; -- Allow users to have a roster. Recommended ;)
		"saslauth"; -- Authentication for clients and servers. Recommended if you want to log in.
		"tls"; -- Add support for secure TLS on c2s/s2s connections
		"disco"; -- Service discovery
{{- if eq $PROSODY_MODE "client" }}
	-- Not essential, but recommended
		"private"; -- Private XML storage (for room bookmarks, etc.)
		"limits"; -- Enable bandwidth limiting for XMPP connections

	-- These are commented by default as they have a performance impact
		--"privacy"; -- Support privacy lists
		--"compression"; -- Stream compression (Debian: requires lua-zlib module to work)

	-- Admin interfaces
		-- "admin_adhoc"; -- Allows administration via an XMPP client that supports ad-hoc commands
		--"admin_telnet"; -- Opens telnet console interface on localhost port 5582

	-- Nice to have
		"version"; -- Replies to server version requests
{{- end }}
		"ping"; -- Replies to XMPP pings with pongs
{{- if eq $PROSODY_MODE "visitors" }}
		"limits"; -- Enable bandwidth limiting for XMPP connections
{{- end }}
	-- HTTP modules
		--"bosh"; -- Enable BOSH clients, aka "Jabber over HTTP"
		--"http_files"; -- Serve static files from a directory over HTTP

	-- Other specific functionality
		"posix"; -- POSIX functionality, sends server to background, enables syslog, etc.
		--"groups"; -- Shared roster support
		--"announce"; -- Send announcement to all online users
		--"welcome"; -- Welcome users who register accounts
		--"watchregistrations"; -- Alert admins of registrations
		--"motd"; -- Send a message to users when they log in
		--"legacyauth"; -- Legacy authentication. Only used by some old clients and bots.
		"http_health";
		{{ if eq $PROSODY_MODE "brewery" -}}
		"firewall"; -- Enable firewalling
		"secure_interfaces";
		{{ end -}}
		{{ if $ENABLE_S2S -}}
		"s2s_bidi";
		"certs_s2soutinjection";
		"s2sout_override";
		"s2s_whitelist";
		{{ end -}}
        {{- if or .Env.TURN_HOST .Env.TURNS_HOST }}
        "external_services";
        {{- end }}

		{{ if $PROSODY_ENABLE_METRICS }}
		-- metrics collection functionality
		"http_openmetrics";
		{{ end -}}

		{{ if $PROSODY_ENABLE_STANZA_COUNTS }}
		-- Stanza count metrics for monitoring
		"measure_stanza_counts";
		{{ end -}}

		{{ if .Env.GLOBAL_MODULES }}
        "{{ join "\";\n\"" (splitList "," .Env.GLOBAL_MODULES | compact) }}";
        {{ end }}
};

component_ports = { }
https_ports = { }

trusted_proxies = {
{{ range $index, $proxy := $TRUSTED_PROXY_LIST }}
  "{{ $proxy }}";
{{ end }}
}

{{ if eq $PROSODY_MODE "brewery" -}}
firewall_scripts = {
    "/config/rules.d/jvb_muc_presence_filter.pfw";
};
{{ end -}}

-- These modules are auto-loaded, but should you want
-- to disable them then uncomment them here:
modules_disabled = {
    "offline"; -- Store offline messages
    "register";
	-- "c2s"; -- Handle client connections

	{{ if not $ENABLE_S2S -}}
	"s2s"; -- Handle server-to-server connections
	{{ end -}}
};

-- Disable account creation by default, for security
-- For more information see http://prosody.im/doc/creating_accounts
allow_registration = false;

{{ if and (ne $PROSODY_MODE "brewery") (or (not $DISABLE_C2S_LIMIT) (not $DISABLE_S2S_LIMIT)) -}}
-- Enable rate limits for incoming connections
limits = {
{{ if not $DISABLE_C2S_LIMIT }}
-- Limit incoming client connections
  c2s = {
    rate = "{{ $PROSODY_C2S_LIMIT }}";
  };
{{ end }}
{{ if not $DISABLE_S2S_LIMIT }}
-- Limit incoming server connections
  s2sin = {
    rate = "{{ $PROSODY_S2S_LIMIT }}";
  };
{{ end }}
}
{{ end -}}

--Prosody garbage collector settings
--For more information see https://prosody.im/doc/advanced_gc
{{ if eq $GC_TYPE "generational" }}
gc = {
    mode = "generational";
    minor_threshold = {{ $GC_GEN_MIN_TH }};
    major_threshold = {{ $GC_GEN_MAX_TH }};
}
{{ else }}
gc = {
	mode = "incremental";
	threshold = {{ $GC_INC_TH }};
	speed = {{ $GC_INC_SPEED }};
	step_size = {{ $GC_INC_STEP_SIZE }};
}
{{ end }}

pidfile = "/config/data/prosody.pid";

-- Force clients to use encrypted connections? This option will
-- prevent clients from authenticating unless they are using encryption.

c2s_require_encryption = {{ $C2S_REQUIRE_ENCRYPTION }};

-- set c2s port
c2s_ports = { {{ $XMPP_PORT }} } -- Listen on specific c2s port
{{ if $ENABLE_IPV6 }}
c2s_interfaces = { "*", "::" }
{{ else }}
c2s_interfaces = { "*" }
{{ end }}

{{ if $ENABLE_S2S -}}
-- set s2s port
s2s_ports = { {{ $S2S_PORT }} } -- Listen on specific s2s port

{{ if eq $PROSODY_MODE "visitors" -}}
s2s_whitelist = {
	{{- if $ENABLE_VISITORS }}
    '{{ $XMPP_MUC_DOMAIN }}'; -- needed for visitors to send messages to main room
    'visitors.{{ $XMPP_DOMAIN }}'; -- needed for sending promotion request to visitors.{{ $XMPP_DOMAIN }} component
    '{{ $XMPP_DOMAIN }}'; -- unavailable presences back to main room
	{{- end }}

    {{- if $ENABLE_GUEST_DOMAIN }}
    '{{ $XMPP_GUEST_DOMAIN }}';
    {{- end }}
    {{ if or $ENABLE_RECORDING $ENABLE_TRANSCRIPTIONS -}}
    '{{ $XMPP_HIDDEN_DOMAIN }}';
	{{- end }}

    {{- if .Env.PROSODY_VISITORS_S2S_VHOSTS }}
    '{{ join "';\n    '" (splitList "," .Env.PROSODY_VISITORS_S2S_VHOSTS | compact) }}';
    {{- end }}
}
{{ end -}}

{{ end -}}

{{ if $ENABLE_VISITORS -}}
{{ if $.Env.VISITORS_XMPP_SERVER -}}
s2sout_override = {
{{ range $index, $element := $VISITORS_XMPP_SERVERS -}}
{{ $SERVER := splitn ":" 2 $element }}
{{ $DEFAULT_PORT := add $VISITORS_XMPP_PORT $index }}
        ["{{ $VISITORS_MUC_PREFIX }}.v{{ $index }}.{{ $VISITORS_XMPP_DOMAIN }}"] = "tcp://{{ $SERVER._0 }}:{{ $SERVER._1 | default $DEFAULT_PORT }}";
        ["v{{ $index }}.{{ $VISITORS_XMPP_DOMAIN }}"] = "tcp://{{ $SERVER._0 }}:{{ $SERVER._1 | default $DEFAULT_PORT }}";
{{ end -}}
};
{{ if ne $PROSODY_MODE "visitors" -}}
s2s_whitelist = {
{{ range $index, $element := $VISITORS_XMPP_SERVERS -}}
	"{{ $VISITORS_MUC_PREFIX }}.v{{ $index }}.{{ $VISITORS_XMPP_DOMAIN }}";
{{ end -}}
};
{{ end -}}
{{ end -}}
{{ end -}}


-- Force certificate authentication for server-to-server connections?
-- This provides ideal security, but requires servers you communicate
-- with to support encryption AND present valid, trusted certificates.
-- NOTE: Your version of LuaSec must support certificate verification!
-- For more information see http://prosody.im/doc/s2s#security

s2s_secure_auth = false

-- Many servers don't support encryption or have invalid or self-signed
-- certificates. You can list domains here that will not be required to
-- authenticate using certificates. They will be authenticated using DNS.

--s2s_insecure_domains = { "gmail.com" }

-- Even if you leave s2s_secure_auth disabled, you can still require valid
-- certificates for some domains by specifying a list here.

--s2s_secure_domains = { "jabber.org" }

-- Select the authentication backend to use. The 'internal' providers
-- use Prosody's configured data storage to store the authentication data.
-- To allow Prosody to offer secure authentication mechanisms to clients, the
-- default provider stores passwords in plaintext. If you do not trust your
-- server please see http://prosody.im/doc/modules/mod_auth_internal_hashed
-- for information about using the hashed backend.

authentication = "internal_hashed"

-- Select the storage backend to use. By default Prosody uses flat files
-- in its configured data directory, but it also supports more backends
-- through modules. An "sql" backend is included by default, but requires
-- additional dependencies. See http://prosody.im/doc/storage for more info.

--storage = "sql" -- Default is "internal" (Debian: "sql" requires one of the
-- lua-dbi-sqlite3, lua-dbi-mysql or lua-dbi-postgresql packages to work)

-- For the "sql" backend, you can uncomment *one* of the below to configure:
--sql = { driver = "SQLite3", database = "prosody.sqlite" } -- Default. 'database' is the filename.
--sql = { driver = "MySQL", database = "prosody", username = "prosody", password = "secret", host = "localhost" }
--sql = { driver = "PostgreSQL", database = "prosody", username = "prosody", password = "secret", host = "localhost" }

-- Logging configuration
-- For advanced logging see http://prosody.im/doc/logging
--
-- Debian:
--  Logs info and higher to /var/log
--  Logs errors to syslog also
log = {
	{ levels = {min = "{{ $LOG_LEVEL }}"}, timestamps = "%Y-%m-%d %X", to = "console"};
{{ if .Env.PROSODY_LOG_CONFIG }}
	{{ join "\n" (splitList "\\n" .Env.PROSODY_LOG_CONFIG | compact) }}
{{ end }}
}

{{ if $PROSODY_ENABLE_METRICS }}
-- Statistics Provider configuration
statistics = "internal"
statistics_interval = "manual"
openmetrics_allow_cidr = "{{ $PROSODY_METRICS_ALLOWED_CIDR }}"
{{ end }}

{{ if .Env.TURN_CREDENTIALS -}}
external_service_secret = "{{.Env.TURN_CREDENTIALS}}";
{{- end }}

{{ if or .Env.STUN_HOST .Env.TURN_HOST .Env.TURNS_HOST -}}
external_services = {
  {{- if $STUN_HOST }}
        { type = "stun", host = "{{ $STUN_HOST }}", port = {{ $STUN_PORT }}, transport = "udp" }
  {{- end }}
  {{- if $TURN_HOST -}}
    {{- range $idx1, $host := $TURN_HOSTS -}}
      {{- range $idx2, $transport := $TURN_TRANSPORTS -}}
        {{- if or $STUN_HOST $idx1 $idx2 -}},{{- end }}
        {
            type = "turn",
            host = "{{ $host }}",
            port = {{ $TURN_PORT }},
            transport = "{{ $transport }}",
            ttl = {{ $TURN_TTL }},

            {{ if $.Env.TURN_CREDENTIALS -}}
            secret = true,
            algorithm = "turn",
            {{- end }}

            {{ if $.Env.TURN_USERNAME -}}
            username = "{{$.Env.TURN_USERNAME}}",
            {{- end }}

            {{ if $.Env.TURN_PASSWORD -}}
            password = "{{$.Env.TURN_PASSWORD}}",
            {{- end }}
        }
      {{- end -}}
    {{- end -}}
  {{- end -}}

  {{- if $TURNS_HOST -}}
    {{- range $idx, $host := $TURNS_HOSTS -}}
        {{- if or $STUN_HOST $TURN_HOST $idx -}},{{- end }}
        {
            type = "turns",
            host = "{{ $host }}",
            port = {{ $TURNS_PORT }},
            transport = "tcp",
            ttl = {{ $TURN_TTL }},

            {{ if $.Env.TURN_CREDENTIALS -}}
            secret = true,
            algorithm = "turn",
            {{- end }}

            {{ if $.Env.TURN_USERNAME -}}
            username = "{{$.Env.TURN_USERNAME}}",
            {{- end }}

            {{ if $.Env.TURN_PASSWORD -}}
            password = "{{$.Env.TURN_PASSWORD}}",
            {{- end }}
        }
    {{- end }}
  {{- end }}
};
{{- end }}


{{ if .Env.GLOBAL_CONFIG }}
{{ join "\n" (splitList "\\n" .Env.GLOBAL_CONFIG | compact) }}
{{ end }}

-- Enable use of native prosody 0.11 support for epoll over select
network_backend = "epoll";
-- Set the TCP backlog to 511 since the kernel rounds it up to the next power of 2: 512.
network_settings = {
  tcp_backlog = 511;
}
unbound = {
    resolvconf = true
}

http_ports = { {{ $PROSODY_HTTP_PORT }} }
{{ if $ENABLE_IPV6 }}
http_interfaces = { "*", "::" }
{{ else }}
http_interfaces = { "*" }
{{ end }}

data_path = "/config/data"

Include "conf.d/*.cfg.lua"
````

## File: prosody/rootfs/defaults/saslauthd.conf
````
{{ $AUTH_TYPE := .Env.AUTH_TYPE | default "internal" -}}
{{ $PROSODY_AUTH_TYPE := .Env.PROSODY_AUTH_TYPE | default $AUTH_TYPE }}
{{ $XMPP_DOMAIN := .Env.XMPP_DOMAIN | default "meet.jitsi" -}}

{{ if eq $PROSODY_AUTH_TYPE "ldap" }}
ldap_servers: {{ .Env.LDAP_URL }}
ldap_search_base: {{ .Env.LDAP_BASE }}
{{ if .Env.LDAP_BINDDN | default "" }}
ldap_bind_dn: {{ .Env.LDAP_BINDDN }}
ldap_bind_pw: {{ .Env.LDAP_BINDPW }}
{{ end }}
ldap_filter: {{ .Env.LDAP_FILTER | default "uid=%u" }}
ldap_version: {{ .Env.LDAP_VERSION | default "3" }}
ldap_auth_method: {{ .Env.LDAP_AUTH_METHOD | default "bind" }}
  {{ if .Env.LDAP_USE_TLS | default "0" | toBool }}
ldap_tls_key: /config/certs/{{ $XMPP_DOMAIN }}.key
ldap_tls_cert: /config/certs/{{ $XMPP_DOMAIN }}.crt
    {{ if .Env.LDAP_TLS_CHECK_PEER | default "0" | toBool }}
ldap_tls_check_peer: yes
ldap_tls_cacert_file: {{ .Env.LDAP_TLS_CACERT_FILE | default "/etc/ssl/certs/ca-certificates.crt" }}
ldap_tls_cacert_dir: {{ .Env.LDAP_TLS_CACERT_DIR | default "/etc/ssl/certs" }}
    {{ end }}
    {{ if .Env.LDAP_TLS_CIPHERS }}
ldap_tls_ciphers: {{ .Env.LDAP_TLS_CIPHERS }}
    {{ end }}
  {{ end }}
{{ end }}
{{ if .Env.LDAP_START_TLS | default "0" | toBool }}
ldap_start_tls: yes
{{ end }}
````

## File: prosody/rootfs/etc/cont-init.d/10-config
````
#!/usr/bin/with-contenv bash

if [[ ! -f /etc/saslauthd.conf ]] && [[ "$AUTH_TYPE" == "ldap" ]]; then
  tpl /defaults/saslauthd.conf > /etc/saslauthd.conf
  mkdir -pm777 /var/run/saslauthd
  adduser prosody sasl
  echo >> /etc/ldap/ldap.conf "TLS_REQCERT allow"
fi

PROSODY_CFG="/config/prosody.cfg.lua"

if [[ ! -d /config/data ]]; then
    mkdir -pm 750 /config/data
fi

if [[ "$(stat -c %U /config)" != "prosody" ]]; then
    chown -R prosody /config
fi

if [[ "$(stat -c %U /prosody-plugins)" != "prosody" ]]; then
    chown -R prosody /prosody-plugins
fi

if [[ "$(stat -c %U /prosody-plugins-custom)" != "prosody" ]]; then
    chown -R prosody /prosody-plugins-custom
fi

if [[ "$(stat -c %U /prosody-plugins-contrib)" != "prosody" ]]; then
    chown -R prosody /prosody-plugins-contrib
fi

mkdir /config/certs
cp -r /defaults/* /config

[ -z "$PROSODY_MODE" ] && export PROSODY_MODE="client"

if [[ "$PROSODY_MODE" == "visitors" ]]; then
    echo "Prosody visitor mode, using alternate config"
    PROSODY_SITE_CFG="visitors.cfg.lua"
    rm /config/conf.d/jitsi-meet.cfg.lua
    rm /config/conf.d/brewery.cfg.lua
    # force jicofo into auth domain for visitor-mode prosody
    [ -z "$XMPP_AUTH_DOMAIN" ] && XMPP_AUTH_DOMAIN="auth.meet.jitsi"
    export PROSODY_ADMINS="focus@$XMPP_AUTH_DOMAIN"
elif [[ "$PROSODY_MODE" == "brewery" ]]; then
    echo "Prosody brewery mode, using alternate config"
    PROSODY_SITE_CFG="brewery.cfg.lua"
    rm /config/conf.d/jitsi-meet.cfg.lua
    rm /config/conf.d/visitors.cfg.lua
    # force jicofo into auth domain for brewer prosody
    [ -z "$JVB_XMPP_AUTH_DOMAIN" ] && JVB_XMPP_AUTH_DOMAIN="auth.meet.jitsi"
    # ensure proper certs are generated
    export XMPP_AUTH_DOMAIN="$JVB_XMPP_AUTH_DOMAIN"
    # brewery mode requires C2S encryption
    export PROSODY_C2S_REQUIRE_ENCRYPTION="true"

    mkdir -p /config/rules.d
    tpl /defaults/rules.d/jvb_muc_presence_filter.pfw > /config/rules.d/jvb_muc_presence_filter.pfw
else
    echo "Prosody normal mode, using default config"
    PROSODY_SITE_CFG="jitsi-meet.cfg.lua"
    rm /config/conf.d/visitors.cfg.lua
    rm /config/conf.d/brewery.cfg.lua
fi
tpl /defaults/prosody.cfg.lua > $PROSODY_CFG
tpl /defaults/conf.d/$PROSODY_SITE_CFG > /config/conf.d/$PROSODY_SITE_CFG

if [[ -z $JICOFO_AUTH_PASSWORD ]]; then
    echo 'FATAL ERROR: Jicofo auth password must be set'
    exit 1
fi

# Defaults
[ -z "${JIBRI_RECORDER_USER}" ] && export JIBRI_RECORDER_USER=recorder
[ -z "${JIBRI_XMPP_USER}" ] && export JIBRI_XMPP_USER=jibri
[ -z "${JIGASI_XMPP_USER}" ] && export JIGASI_XMPP_USER=jigasi
[ -z "${JVB_AUTH_USER}" ] && export JVB_AUTH_USER=jvb
[ -z "${XMPP_DOMAIN}" ] && export XMPP_DOMAIN=meet.jitsi
[ -z "${XMPP_AUTH_DOMAIN}" ] && export XMPP_AUTH_DOMAIN=auth.meet.jitsi
# maintain backward compatibility with older variable
[ -z "${XMPP_HIDDEN_DOMAIN}" ] && export XMPP_HIDDEN_DOMAIN="$XMPP_RECORDER_DOMAIN"
[ -z "${XMPP_HIDDEN_DOMAIN}" ] && export XMPP_HIDDEN_DOMAIN=hidden.meet.jitsi

prosodyctl --config $PROSODY_CFG register focus $XMPP_AUTH_DOMAIN $JICOFO_AUTH_PASSWORD

# if we are in client mode, we need to subscribe the focus user to the focus component proxy
if [[ "$PROSODY_MODE" == "client" ]]; then
    prosodyctl --config $PROSODY_CFG mod_roster_command subscribe focus.$XMPP_DOMAIN focus@$XMPP_AUTH_DOMAIN
fi

if [[ -z $JVB_AUTH_PASSWORD ]]; then
    echo 'FATAL ERROR: JVB auth password must be set'
    exit 1
fi

OLD_JVB_AUTH_PASSWORD=passw0rd
if [[ "$JVB_AUTH_PASSWORD" == "$OLD_JVB_AUTH_PASSWORD" ]]; then
    echo 'FATAL ERROR: JVB auth password must be changed, check the README'
    exit 1
fi

prosodyctl --config $PROSODY_CFG register $JVB_AUTH_USER $XMPP_AUTH_DOMAIN $JVB_AUTH_PASSWORD

if [[ ! -z $JIBRI_XMPP_PASSWORD ]]; then
    OLD_JIBRI_XMPP_PASSWORD=passw0rd
    if [[ "$JIBRI_XMPP_PASSWORD" == "$OLD_JIBRI_XMPP_PASSWORD" ]]; then
        echo 'FATAL ERROR: Jibri auth password must be changed, check the README'
        exit 1
    fi
    prosodyctl --config $PROSODY_CFG register $JIBRI_XMPP_USER $XMPP_AUTH_DOMAIN $JIBRI_XMPP_PASSWORD
fi

if [[ "$PROSODY_MODE" == "client" ]]; then
    if [[ ! -z $JIBRI_RECORDER_PASSWORD ]]; then
        OLD_JIBRI_RECORDER_PASSWORD=passw0rd
        if [[ "$JIBRI_RECORDER_PASSWORD" == "$OLD_JIBRI_RECORDER_PASSWORD" ]]; then
            echo 'FATAL ERROR: Jibri recorder password must be changed, check the README'
            exit 1
        fi
        prosodyctl --config $PROSODY_CFG register $JIBRI_RECORDER_USER $XMPP_HIDDEN_DOMAIN $JIBRI_RECORDER_PASSWORD
    fi
    if [[ "$(echo "$ENABLE_TRANSCRIPTIONS" | tr '[:upper:]' '[:lower:]')" == "true" ]] || [[ "$ENABLE_TRANSCRIPTIONS" == "1" ]]; then
        if [[ ! -z $JIGASI_TRANSCRIBER_PASSWORD ]]; then
            [ -z "$JIGASI_TRANSCRIBER_USER" ] && JIGASI_TRANSCRIBER_USER="transcriber"
            prosodyctl --config $PROSODY_CFG register $JIGASI_TRANSCRIBER_USER $XMPP_HIDDEN_DOMAIN $JIGASI_TRANSCRIBER_PASSWORD
        fi
    fi
fi

if [[ ! -z $JIGASI_XMPP_PASSWORD ]]; then
    OLD_JIGASI_XMPP_PASSWORD=passw0rd
    if [[ "$JIGASI_XMPP_PASSWORD" == "$OLD_JIGASI_XMPP_PASSWORD" ]]; then
        echo 'FATAL ERROR: Jigasi auth password must be changed, check the README'
        exit 1
    fi
    prosodyctl --config $PROSODY_CFG register $JIGASI_XMPP_USER $XMPP_AUTH_DOMAIN $JIGASI_XMPP_PASSWORD
fi

if [[ "$PROSODY_MODE" == "visitors" ]]; then
    [ -z "$VISITORS_XMPP_DOMAIN" ] && VISITORS_XMPP_DOMAIN="meet.jitsi"
    [ -z "$PROSODY_VISITOR_INDEX" ] && PROSODY_VISITOR_INDEX=0
    FULL_VISITORS_XMPP_DOMAIN="v$PROSODY_VISITOR_INDEX.$VISITORS_XMPP_DOMAIN"
    if [[ ! -f /config/certs/$FULL_VISITORS_XMPP_DOMAIN.crt ]]; then
        # echo for using all default values
        echo | prosodyctl --config $PROSODY_CFG cert generate $FULL_VISITORS_XMPP_DOMAIN
    fi
elif [[ "$PROSODY_MODE" == "brewery" ]]; then
    echo "No need to generate certs for main XMPP domain in brewery mode"
else
    if [[ ! -f /config/certs/$XMPP_DOMAIN.crt ]]; then
        # echo for using all default values
        echo | prosodyctl --config $PROSODY_CFG cert generate $XMPP_DOMAIN
    fi
fi

if [[ ! -f /config/certs/$XMPP_AUTH_DOMAIN.crt ]]; then
    # echo for using all default values
    echo | prosodyctl --config $PROSODY_CFG cert generate $XMPP_AUTH_DOMAIN
fi

# certs will be created in /config/data
mv /config/data/*.{crt,key} /config/certs/ || true
rm -f /config/data/*.cnf
````

## File: prosody/rootfs/etc/sasl/xmpp.conf
````
pwcheck_method: saslauthd
mech_list: PLAIN
````

## File: prosody/rootfs/etc/services.d/10-saslauthd/run
````
#!/usr/bin/with-contenv bash
if [[ -f /etc/saslauthd.conf ]]; then
  exec s6-setuidgid root saslauthd -a ldap -O /etc/saslauthd.conf -c -m /var/run/saslauthd -n 5 -d
else
    # if saslauthd should not be started,
    # prevent s6 from restarting this script again and again
    s6-svc -O /var/run/s6/services/10-saslauthd
fi
````

## File: prosody/rootfs/etc/services.d/prosody/run
````
#!/usr/bin/with-contenv bash
exec s6-setuidgid prosody prosody --config /config/prosody.cfg.lua -F
````

## File: prosody/rootfs/usr/local/bin/healthcheck.sh
````bash
#!/bin/bash

curl --fail-with-body http://127.0.0.1:5280/health
````

## File: prosody/Dockerfile
````
ARG JITSI_REPO=jitsi
ARG BASE_TAG=latest

FROM ${JITSI_REPO}/base:${BASE_TAG} AS builder

RUN apt-dpkg-wrap apt-get update && \
    apt-dpkg-wrap apt-get install -y \
      build-essential \
      lua5.4 \
      liblua5.4-dev \
      libreadline-dev \
      git \
      unzip \
      wget && \
    mkdir /tmp/luarocks && \
    wget -qO - https://luarocks.github.io/luarocks/releases/luarocks-3.8.0.tar.gz | tar xfz - --strip-components 1 -C /tmp/luarocks && \
    cd /tmp/luarocks && ./configure && make && make install && cd - && \
    luarocks install basexx 0.4.1-1 && \
    luarocks install lua-cjson 2.1.0-1 && \
    luarocks install net-url 0.9-1

FROM ${JITSI_REPO}/base:${BASE_TAG}

LABEL org.opencontainers.image.title="Prosody IM"
LABEL org.opencontainers.image.description="XMPP server used for signalling."
LABEL org.opencontainers.image.url="https://prosody.im/"
LABEL org.opencontainers.image.source="https://github.com/jitsi/docker-jitsi-meet"
LABEL org.opencontainers.image.documentation="https://jitsi.github.io/handbook/"

ARG VERSION_JITSI_CONTRIB_PROSODY_PLUGINS="20241017"
ARG VERSION_MATRIX_USER_VERIFICATION_SERVICE_PLUGIN="1.8.0"

RUN set -x && \
    wget -qO /etc/apt/trusted.gpg.d/prosody.gpg https://prosody.im/files/prosody-debian-packages.key && \
    echo "deb http://packages.prosody.im/debian bookworm main" > /etc/apt/sources.list.d/prosody.list && \
    apt-dpkg-wrap apt-get update && \
    apt-dpkg-wrap apt-get install -y \
      lua5.4 \
      prosody \
      libldap-common \
      sasl2-bin \
      libsasl2-modules-ldap \
      lua-cyrussasl \
      lua-inspect \
      lua-ldap \
      lua-luaossl \
      lua-sec \
      lua-unbound && \
    apt-dpkg-wrap apt-get -d install -y jitsi-meet-prosody && \
    dpkg -x /var/cache/apt/archives/jitsi-meet-prosody*.deb /tmp/pkg && \
    rm /tmp/pkg/usr/share/jitsi-meet/prosody-plugins/mod_smacks.lua && \
    mv /tmp/pkg/usr/share/jitsi-meet/prosody-plugins /prosody-plugins && \
    rm -rf /tmp/pkg /var/cache/apt && \
    apt-cleanup && \
    rm -rf /etc/prosody && \
    mv /usr/share/lua/5.3/inspect.lua /usr/share/lua/5.4/ && \
    rm -rf /usr/lib/lua/{5.1,5.2,5.3} && \
    rm -rf /usr/share/lua/{5.1,5.2,5.3} && \
    wget -qO /prosody-plugins/mod_auth_cyrus.lua https://hg.prosody.im/prosody-modules/raw-file/65438e4ba563/mod_auth_cyrus/mod_auth_cyrus.lua && \
    wget -qO /prosody-plugins/sasl_cyrus.lua https://hg.prosody.im/prosody-modules/raw-file/65438e4ba563/mod_auth_cyrus/sasl_cyrus.lua  && \
    wget -qO /prosody-plugins/mod_http_health.lua https://hg.prosody.im/prosody-modules/raw-file/2b80188448d1/mod_http_health/mod_http_health.lua && \
    wget https://github.com/matrix-org/prosody-mod-auth-matrix-user-verification/archive/refs/tags/v$VERSION_MATRIX_USER_VERIFICATION_SERVICE_PLUGIN.tar.gz && \
    tar -xf v$VERSION_MATRIX_USER_VERIFICATION_SERVICE_PLUGIN.tar.gz && \
    mv prosody-mod-auth-matrix-user-verification-$VERSION_MATRIX_USER_VERIFICATION_SERVICE_PLUGIN/mod_auth_matrix_user_verification.lua /prosody-plugins && \
    mv prosody-mod-auth-matrix-user-verification-$VERSION_MATRIX_USER_VERIFICATION_SERVICE_PLUGIN/mod_matrix_power_sync.lua /prosody-plugins && \
    rm -rf prosody-mod-auth-matrix-user-verification-$VERSION_MATRIX_USER_VERIFICATION_SERVICE_PLUGIN v$VERSION_MATRIX_USER_VERIFICATION_SERVICE_PLUGIN.tar.gz && \
    wget -q https://github.com/jitsi-contrib/prosody-plugins/archive/refs/tags/v$VERSION_JITSI_CONTRIB_PROSODY_PLUGINS.tar.gz && \
    tar -xf v$VERSION_JITSI_CONTRIB_PROSODY_PLUGINS.tar.gz && \
    mkdir /prosody-plugins-contrib && \
    cp -a prosody-plugins-$VERSION_JITSI_CONTRIB_PROSODY_PLUGINS/*  /prosody-plugins-contrib && \
    rm -rf prosody-plugins-$VERSION_JITSI_CONTRIB_PROSODY_PLUGINS v$VERSION_JITSI_CONTRIB_PROSODY_PLUGINS.tar.gz

COPY rootfs/ /

COPY --from=builder /usr/local/lib/lua/5.4 /usr/local/lib/lua/5.4
COPY --from=builder /usr/local/share/lua/5.4 /usr/local/share/lua/5.4

EXPOSE 5222 5280

VOLUME ["/config", "/prosody-plugins-custom"]
````

## File: resources/docker-jitsi-meet.xml
````xml
<mxfile host="app.diagrams.net" modified="2020-03-24T10:51:12.906Z" agent="Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:75.0) Gecko/20100101 Firefox/75.0" version="12.9.1" etag="dVRm2Ai7Pk1qfq7lOKyD" type="device"><diagram name="Page-1" id="7a6c530e-6e37-e111-ec74-82921da8cc10">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</diagram></mxfile>
````

## File: web/rootfs/defaults/default
````
server {
	listen 80 default_server;
	
	{{ if .Env.ENABLE_IPV6 | default "1" | toBool }}
	listen [::]:80 default_server;
	{{ end }}

	{{ if .Env.ENABLE_HTTP_REDIRECT | default "0" | toBool }}
	return 301 https://$host$request_uri;
	{{ else }}
	include /config/nginx/meet.conf;
	{{ end }}
}

{{ if not (.Env.DISABLE_HTTPS | default "0" | toBool) }}
server {
	listen 443 ssl http2;
	
	{{ if .Env.ENABLE_IPV6 | default "1" | toBool }}
	listen [::]:443 ssl http2;
	{{ end }}

	include /config/nginx/ssl.conf;
	include /config/nginx/meet.conf;
}
{{ end }}
````

## File: web/rootfs/defaults/ffdhe2048.txt
````
-----BEGIN DH PARAMETERS-----
MIIBCAKCAQEA//////////+t+FRYortKmq/cViAnPTzx2LnFg84tNpWp4TZBFGQz
+8yTnc4kmz75fS/jY2MMddj2gbICrsRhetPfHtXV/WVhJDP1H18GbtCFY2VVPe0a
87VXE15/V8k1mE8McODmi3fipona8+/och3xWKE2rec1MKzKT0g6eXq8CrGCsyT7
YdEIqUuyyOP7uWrat2DX9GgdT0Kj3jlN9K5W7edjcrsZCwenyO4KbXCeAvzhzffi
7MA0BM0oNC9hkXL+nOmFg/+OTxIy7vKBg8P+OxtMb61zO7X8vC7CIAXFjvGDfRaD
ssbzSibBsu/6iGtCOGEoXJf//////////wIBAg==
-----END DH PARAMETERS-----
````

## File: web/rootfs/defaults/meet.conf
````
{{ $ENABLE_COLIBRI_WEBSOCKET := .Env.ENABLE_COLIBRI_WEBSOCKET | default "0" | toBool }}
{{ $COLIBRI_WEBSOCKET_PORT := .Env.COLIBRI_WEBSOCKET_PORT | default "9090" }}
{{ $COLIBRI_WEBSOCKET_REGEX := .Env.COLIBRI_WEBSOCKET_REGEX | default "jvb" }}
{{ $ENABLE_JAAS_COMPONENTS := .Env.ENABLE_JAAS_COMPONENTS | default "0" | toBool }}
{{ $ENABLE_LOAD_TEST_CLIENT := .Env.ENABLE_LOAD_TEST_CLIENT | default "0" | toBool }}
{{ $ENABLE_OCTO := .Env.ENABLE_OCTO | default "0" | toBool -}}
{{ $ENABLE_XMPP_WEBSOCKET := .Env.ENABLE_XMPP_WEBSOCKET | default "1" | toBool }}
{{ $ENABLE_SUBDOMAINS := .Env.ENABLE_SUBDOMAINS | default "true" | toBool -}}
{{ $XMPP_DOMAIN := .Env.XMPP_DOMAIN | default "meet.jitsi" -}}
{{ $XMPP_BOSH_URL_BASE := .Env.XMPP_BOSH_URL_BASE | default "http://xmpp.meet.jitsi:5280" -}}
{{ $CORS_HEADER_ACCESS_CONTROL_ALLOW_ORIGIN := .Env.CORS_HEADER_ACCESS_CONTROL_ALLOW_ORIGIN | default "*" }}

server_name _;

charset utf8;

client_max_body_size 0;

root /usr/share/jitsi-meet;

# ssi on with javascript for multidomain variables in config.js
ssi on;
ssi_types application/x-javascript application/javascript;

index index.html index.htm;
error_page 404 /static/404.html;

# Security headers
add_header X-Content-Type-Options nosniff;
add_header X-XSS-Protection "1; mode=block";

set $prefix "";

{{ if .Env.DEPLOYMENTINFO_SHARD }}
add_header X-Jitsi-Shard {{ .Env.DEPLOYMENTINFO_SHARD }};
{{ end }}

# Opt out of FLoC (deprecated)
add_header Permissions-Policy "interest-cohort=()";

include /config/nginx-custom/*.conf;

location = /config.js {
    alias /config/config.js;
}

location = /interface_config.js {
    alias /config/interface_config.js;
}

location = /external_api.js {
    alias /usr/share/jitsi-meet/libs/external_api.min.js;
}

{{ if $ENABLE_JAAS_COMPONENTS }}
location = /_api/room-info {
    proxy_pass {{ $XMPP_BOSH_URL_BASE }}/room-info?prefix=$prefix&$args;
    proxy_http_version 1.1;
    proxy_set_header X-Forwarded-For $remote_addr;
    proxy_set_header Host $http_host;
}
{{ end }}

# ensure all static content can always be found first
location ~ ^/(libs|css|static|images|fonts|lang|sounds|connection_optimization|.well-known|transcripts)/(.*)$ {
    add_header 'Access-Control-Allow-Origin' '{{ $CORS_HEADER_ACCESS_CONTROL_ALLOW_ORIGIN }}';
    alias /usr/share/jitsi-meet/$1/$2;

    # cache all versioned files
    if ($arg_v) {
        expires 1y;
    }
}

{{ if $ENABLE_COLIBRI_WEBSOCKET }}
# colibri (JVB) websockets
location ~ ^/colibri-ws/({{ $COLIBRI_WEBSOCKET_REGEX }})/(.*) {
    tcp_nodelay on;

    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;

    proxy_pass http://$1:{{ $COLIBRI_WEBSOCKET_PORT }}/colibri-ws/$1/$2$is_args$args;
}

{{ if $ENABLE_OCTO }}
# colibri (JVB) Relay to Relay websockets
location ~ ^/colibri-relay-ws/([a-zA-Z0-9-\._]+)/(.*) {
    tcp_nodelay on;

    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;

    proxy_pass http://$1:{{ $COLIBRI_WEBSOCKET_PORT }}/colibri-relay-ws/$1/$2$is_args$args;
}
{{ end }}
{{ end }}

# BOSH
location = /http-bind {
    proxy_set_header X-Forwarded-For $remote_addr;
    proxy_set_header Host {{ $XMPP_DOMAIN }};

    proxy_pass {{ $XMPP_BOSH_URL_BASE }}/http-bind?prefix=$prefix&$args;
}

{{ if $ENABLE_XMPP_WEBSOCKET }}
# xmpp websockets
location = /xmpp-websocket {
    tcp_nodelay on;

    proxy_http_version 1.1;
    proxy_set_header Connection $connection_upgrade;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Host {{ $XMPP_DOMAIN }};
    proxy_set_header X-Forwarded-For $remote_addr;

    proxy_pass {{ $XMPP_BOSH_URL_BASE }}/xmpp-websocket?prefix=$prefix&$args;
}
{{ end }}

{{ if .Env.ETHERPAD_URL_BASE }}
# Etherpad-lite
location ^~ /etherpad/ {
    proxy_buffering off;
    proxy_cache_bypass $http_upgrade;

    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header X-Forwarded-For $remote_addr;

    proxy_pass {{ .Env.ETHERPAD_URL_BASE }}/;
}
{{ end }}

{{ if .Env.WHITEBOARD_COLLAB_SERVER_URL_BASE }}
# whiteboard (excalidraw-backend)
location = /socket.io/ {
    proxy_buffering off;
    proxy_cache_bypass $http_upgrade;

    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header X-Forwarded-For $remote_addr;

    proxy_pass {{ .Env.WHITEBOARD_COLLAB_SERVER_URL_BASE }}/socket.io/?$args;
}
{{ end }}

location ~ ^/([^/?&:'"]+)$ {
    try_files $uri @root_path;
}

location @root_path {
    rewrite ^/(.*)$ / break;
}

{{ if $ENABLE_SUBDOMAINS }}
    # Matches /(TENANT)/pwa-worker.js or /(TENANT)/manifest.json to rewrite to / and look for file
    location ~ ^/([^/?&:'"]+)/(pwa-worker.js|manifest.json)$ {
        set $subdomain "$1.";
        set $subdir "$1/";
        rewrite ^/([^/?&:'"]+)/(pwa-worker.js|manifest.json)$ /$2;
    }

    location ~ ^/([^/?&:'"]+)/config.js$ {
        set $subdomain "$1.";
        set $subdir "$1/";

        alias /config/config.js;
    }

    # BOSH for subdomains
    location ~ ^/([^/?&:'"]+)/http-bind {
        set $subdomain "$1.";
        set $subdir "$1/";
        set $prefix "$1";

        rewrite ^/(.*)$ /http-bind;
    }

    {{ if $ENABLE_XMPP_WEBSOCKET }}
    # websockets for subdomains
    location ~ ^/([^/?&:'"]+)/xmpp-websocket {
        set $subdomain "$1.";
        set $subdir "$1/";
        set $prefix "$1";

        rewrite ^/(.*)$ /xmpp-websocket;
    }
    {{ end }}

    {{ if $ENABLE_JAAS_COMPONENTS }}
    location ~ ^/([^/?&:'"]+)/_api/room-info {
        set $subdomain "$1.";
        set $subdir "$1/";
        set $prefix "$1";

        rewrite ^/(.*)$ /_api/room-info;
    }
    {{ end }}

    {{- if $ENABLE_LOAD_TEST_CLIENT }}
    # load test minimal client, uncomment when used
    location ~ ^/_load-test/([^/?&:'"]+)$ {
        rewrite ^/_load-test/(.*)$ /load-test/index.html break;
    }
    location ~ ^/_load-test/libs/(.*)$ {
        add_header 'Access-Control-Allow-Origin' '{{ $CORS_HEADER_ACCESS_CONTROL_ALLOW_ORIGIN }}';
        alias /usr/share/jitsi-meet/load-test/libs/$1;
    }

    # load-test for subdomains
    location ~ ^/([^/?&:'"]+)/_load-test/([^/?&:'"]+)$ {
        set $subdomain "$1.";
        set $subdir "$1/";
        set $prefix "$1";

        rewrite ^/(.*)$ /load-test/index.html break;
    }

    # load-test for subdomains
    location ~ ^/([^/?&:'"]+)/_load-test/libs/(.*)$ {
        set $subdomain "$1.";
        set $subdir "$1/";
        set $prefix "$1";

        alias /usr/share/jitsi-meet/load-test/libs/$2;
    }

    {{- end }}
    # Anything that didn't match above, and isn't a real file, assume it's a room name and redirect to /
    location ~ ^/([^/?&:'"]+)/(.*)$ {
        set $subdomain "$1.";
        set $subdir "$1/";
        rewrite ^/([^/?&:'"]+)/(.*)$ /$2;
    }
{{ end }}
````

## File: web/rootfs/defaults/nginx.conf
````
user www-data;
worker_processes {{ .Env.NGINX_WORKER_PROCESSES | default "4" }};
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections {{ .Env.NGINX_WORKER_CONNECTIONS | default "768" }};
	# multi_accept on;
}

http {

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout {{ .Env.NGINX_KEEPALIVE_TIMEOUT | default "65" }};
	types_hash_max_size 2048;
	server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	client_max_body_size 0;

	{{ if .Env.NGINX_RESOLVER }}
	resolver {{ .Env.NGINX_RESOLVER }};
	{{ end -}}

 	include /etc/nginx/mime.types;
	types {
		# add support for the wav MIME type that is requried to playback wav files in Firefox.
		audio/wav        wav;
	}
	default_type application/octet-stream;

	##
	# Logging Settings
	##

	access_log /dev/stdout;
	error_log /dev/stderr;

	##
	# Gzip Settings
	##

	gzip on;
	gzip_types text/plain text/css application/javascript application/json;
	gzip_vary on;
	gzip_min_length 860;

	##
	# Connection header for WebSocket reverse proxy
	##
	map $http_upgrade $connection_upgrade {
		default upgrade;
		''      close;
	}

	##
	# Virtual Host Configs
	##
	include /config/nginx/site-confs/*;
}


daemon off;
````

## File: web/rootfs/defaults/settings-config.js
````javascript
{{ $ENABLE_ADAPTIVE_MODE := .Env.ENABLE_ADAPTIVE_MODE | default "true" | toBool -}}
{{ $ENABLE_AUDIO_PROCESSING := .Env.ENABLE_AUDIO_PROCESSING | default "true" | toBool -}}
{{ $ENABLE_AUTOMATIC_GAIN_CONTROL := .Env.ENABLE_AUTOMATIC_GAIN_CONTROL | default "true" | toBool -}}
{{ $ENABLE_BREAKOUT_ROOMS := .Env.ENABLE_BREAKOUT_ROOMS | default "true" | toBool -}}
{{ $ENABLE_CALENDAR := .Env.ENABLE_CALENDAR | default "false" | toBool -}}
{{ $ENABLE_FILE_RECORDING_SHARING := .Env.ENABLE_FILE_RECORDING_SHARING | default "false" | toBool -}}
{{ $ENABLE_NO_AUDIO_DETECTION := .Env.ENABLE_NO_AUDIO_DETECTION | default "true" | toBool -}}
{{ $ENABLE_P2P := .Env.ENABLE_P2P | default "true" | toBool -}}
{{ $ENABLE_PREJOIN_PAGE := .Env.ENABLE_PREJOIN_PAGE | default "true" | toBool -}}
{{ $ENABLE_WELCOME_PAGE := .Env.ENABLE_WELCOME_PAGE | default "true" | toBool -}}
{{ $ENABLE_CLOSE_PAGE := .Env.ENABLE_CLOSE_PAGE | default "false" | toBool -}}
{{ $ENABLE_RECORDING := .Env.ENABLE_RECORDING | default "false" | toBool -}}
{{ $ENABLE_SERVICE_RECORDING := .Env.ENABLE_SERVICE_RECORDING | default ($ENABLE_RECORDING | printf "%t") | toBool -}}
{{ $ENABLE_LIVESTREAMING := .Env.ENABLE_LIVESTREAMING | default "false" | toBool -}}
{{ $ENABLE_LIVESTREAMING_DATA_PRIVACY_LINK := .Env.ENABLE_LIVESTREAMING_DATA_PRIVACY_LINK | default "https://policies.google.com/privacy" -}}
{{ $ENABLE_LIVESTREAMING_HELP_LINK := .Env.ENABLE_LIVESTREAMING_HELP_LINK | default "https://jitsi.org/live" -}}
{{ $ENABLE_LIVESTREAMING_TERMS_LINK := .Env.ENABLE_LIVESTREAMING_TERMS_LINK | default "https://www.youtube.com/t/terms" -}}
{{ $ENABLE_LIVESTREAMING_VALIDATOR_REGEXP_STRING := .Env.ENABLE_LIVESTREAMING_VALIDATOR_REGEXP_STRING | default "^(?:[a-zA-Z0-9]{4}(?:-(?!$)|$)){4}" -}}
{{ $ENABLE_REMB := .Env.ENABLE_REMB | default "true" | toBool -}}
{{ $ENABLE_REQUIRE_DISPLAY_NAME := .Env.ENABLE_REQUIRE_DISPLAY_NAME | default "false" | toBool -}}
{{ $ENABLE_SIMULCAST := .Env.ENABLE_SIMULCAST | default "true" | toBool -}}
{{ $ENABLE_STEREO := .Env.ENABLE_STEREO | default "false" | toBool -}}
{{ $ENABLE_OPUS_RED := .Env.ENABLE_OPUS_RED | default "false" | toBool -}}
{{ $ENABLE_TALK_WHILE_MUTED := .Env.ENABLE_TALK_WHILE_MUTED | default "false" | toBool -}}
{{ $ENABLE_TCC := .Env.ENABLE_TCC | default "true" | toBool -}}
{{ $ENABLE_TRANSCRIPTIONS := .Env.ENABLE_TRANSCRIPTIONS | default "false" | toBool -}}
{{ $TRANSLATION_LANGUAGES := .Env.TRANSLATION_LANGUAGES | default "[]" -}}
{{ $TRANSLATION_LANGUAGES_HEAD := .Env.TRANSLATION_LANGUAGES_HEAD | default "['en']" -}}
{{ $USE_APP_LANGUAGE := .Env.USE_APP_LANGUAGE | default "true" | toBool -}}
{{ $PREFERRED_LANGUAGE := .Env.PREFERRED_LANGUAGE | default "en-US" -}}
{{ $DISABLE_START_FOR_ALL := .Env.DISABLE_START_FOR_ALL | default "false" | toBool -}}
{{ $AUTO_CAPTION_ON_RECORD := .Env.AUTO_CAPTION_ON_RECORD | default "false" | toBool -}}
{{ $ENABLE_JAAS_COMPONENTS := .Env.ENABLE_JAAS_COMPONENTS | default "0" | toBool }}
{{ $HIDE_PREJOIN_DISPLAY_NAME := .Env.HIDE_PREJOIN_DISPLAY_NAME | default "false" | toBool -}}
{{ $PUBLIC_URL := .Env.PUBLIC_URL | default "https://localhost:8443" -}}
{{ $RESOLUTION := .Env.RESOLUTION | default "720" -}}
{{ $RESOLUTION_MIN := .Env.RESOLUTION_MIN | default "180" -}}
{{ $RESOLUTION_WIDTH := .Env.RESOLUTION_WIDTH | default "1280" -}}
{{ $RESOLUTION_WIDTH_MIN := .Env.RESOLUTION_WIDTH_MIN | default "320" -}}
{{ $START_AUDIO_ONLY := .Env.START_AUDIO_ONLY | default "false" | toBool -}}
{{ $START_AUDIO_MUTED := .Env.START_AUDIO_MUTED | default 10 -}}
{{ $START_WITH_AUDIO_MUTED := .Env.START_WITH_AUDIO_MUTED | default "false" | toBool -}}
{{ $START_SILENT := .Env.START_SILENT | default "false" | toBool -}}
{{ $DISABLE_AUDIO_LEVELS := .Env.DISABLE_AUDIO_LEVELS | default "false" | toBool -}}
{{ $ENABLE_NOISY_MIC_DETECTION := .Env.ENABLE_NOISY_MIC_DETECTION | default "true" | toBool -}}
{{ $START_VIDEO_MUTED := .Env.START_VIDEO_MUTED | default 10 -}}
{{ $START_WITH_VIDEO_MUTED := .Env.START_WITH_VIDEO_MUTED | default "false" | toBool -}}
{{ $DESKTOP_SHARING_FRAMERATE_AUTO := .Env.DESKTOP_SHARING_FRAMERATE_AUTO | default "true" | toBool -}}
{{ $DESKTOP_SHARING_FRAMERATE_MIN := .Env.DESKTOP_SHARING_FRAMERATE_MIN | default 5 -}}
{{ $DESKTOP_SHARING_FRAMERATE_MAX := .Env.DESKTOP_SHARING_FRAMERATE_MAX | default 5 -}}
{{ $XMPP_DOMAIN := .Env.XMPP_DOMAIN | default "meet.jitsi" -}}
{{ $XMPP_HIDDEN_DOMAIN := .Env.XMPP_HIDDEN_DOMAIN | default "hidden.meet.jitsi" -}}
{{ $DISABLE_DEEP_LINKING  := .Env.DISABLE_DEEP_LINKING | default "false" | toBool -}}
{{ $DISABLE_POLLS := .Env.DISABLE_POLLS | default "false" | toBool -}}
{{ $DISABLE_REACTIONS := .Env.DISABLE_REACTIONS | default "false" | toBool -}}
{{ $DISABLE_REMOTE_VIDEO_MENU := .Env.DISABLE_REMOTE_VIDEO_MENU | default "false" | toBool -}}
{{ $DISABLE_PRIVATE_CHAT:= .Env.DISABLE_PRIVATE_CHAT | default "false" | toBool -}}
{{ $DISABLE_KICKOUT := .Env.DISABLE_KICKOUT | default "false" | toBool -}}
{{ $DISABLE_GRANT_MODERATOR := .Env.DISABLE_GRANT_MODERATOR | default "false" | toBool -}}
{{ $ENABLE_E2EPING := .Env.ENABLE_E2EPING | default "false" | toBool -}}
{{ $DISABLE_LOCAL_RECORDING := .Env.DISABLE_LOCAL_RECORDING | default "false" | toBool -}}
{{ $ENABLE_LOCAL_RECORDING_NOTIFY_ALL_PARTICIPANT := .Env.ENABLE_LOCAL_RECORDING_NOTIFY_ALL_PARTICIPANT | default "false" | toBool -}}
{{ $ENABLE_LOCAL_RECORDING_SELF_START := .Env.ENABLE_LOCAL_RECORDING_SELF_START | default "false" | toBool -}}
{{ $DISABLE_PROFILE := .Env.DISABLE_PROFILE | default "false" | toBool -}}
{{ $ROOM_PASSWORD_DIGITS := .Env.ROOM_PASSWORD_DIGITS | default "false" -}}
{{ $WHITEBOARD_ENABLED := or (.Env.WHITEBOARD_COLLAB_SERVER_PUBLIC_URL | default "" | toBool) (.Env.WHITEBOARD_COLLAB_SERVER_URL_BASE | default "" | toBool) }}
{{ $CODEC_ORDER_JVB := .Env.CODEC_ORDER_JVB | default "[\"AV1\", \"VP9\", \"VP8\", \"H264\"]" -}}
{{ $CODEC_ORDER_JVB_MOBILE := .Env.CODEC_ORDER_JVB_MOBILE | default "[\"VP8\", \"VP9\", \"H264\", \"AV1\"]" -}}
{{ $CODEC_ORDER_P2P := .Env.CODEC_ORDER_JVB | default "[\"AV1\", \"VP9\", \"VP8\", \"H264\"]" -}}
{{ $CODEC_ORDER_P2P_MOBILE := .Env.CODEC_ORDER_JVB_MOBILE | default "[\"VP8\", \"VP9\", \"H264\", \"AV1\"]" -}}

// Video configuration.
//

config.resolution = {{ $RESOLUTION }};
config.constraints = {
    video: {
        height: { ideal: {{ $RESOLUTION }}, max: {{ $RESOLUTION }}, min: {{ $RESOLUTION_MIN }} },
        width: { ideal: {{ $RESOLUTION_WIDTH }}, max: {{ $RESOLUTION_WIDTH }}, min: {{ $RESOLUTION_WIDTH_MIN }}},
    }
};

{{ if not $ENABLE_SIMULCAST -}}
config.disableSimulcast = true;
{{ end -}}
config.startVideoMuted = {{ $START_VIDEO_MUTED }};
config.startWithVideoMuted = {{ $START_WITH_VIDEO_MUTED }};

config.flags = {
    sourceNameSignaling: true,
    sendMultipleVideoStreams: true,
    receiveMultipleVideoStreams: true
};

// ScreenShare Configuration.
//

{{ if not $DESKTOP_SHARING_FRAMERATE_AUTO -}}
config.desktopSharingFrameRate = {
    min: {{ $DESKTOP_SHARING_FRAMERATE_MIN }},
    max: {{ $DESKTOP_SHARING_FRAMERATE_MAX }}
};
{{ end -}}

// Audio configuration.
//

config.enableNoAudioDetection = {{ $ENABLE_NO_AUDIO_DETECTION }};
config.enableTalkWhileMuted = {{ $ENABLE_TALK_WHILE_MUTED }};
config.disableAP = {{ not $ENABLE_AUDIO_PROCESSING }};
config.disableAGC = {{ not $ENABLE_AUTOMATIC_GAIN_CONTROL }};

config.audioQuality = {
    stereo: {{ $ENABLE_STEREO }}
};

{{ if .Env.AUDIO_QUALITY_OPUS_BITRATE -}}
config.audioQuality.opusMaxAverageBitrate = '{{ .Env.AUDIO_QUALITY_OPUS_BITRATE }}';
{{ end -}}

config.startAudioOnly = {{ $START_AUDIO_ONLY }};
config.startAudioMuted = {{ $START_AUDIO_MUTED }};
config.startWithAudioMuted = {{ $START_WITH_AUDIO_MUTED }};
config.startSilent = {{ $START_SILENT }};
config.enableOpusRed = {{ $ENABLE_OPUS_RED }};
config.disableAudioLevels = {{ $DISABLE_AUDIO_LEVELS }};
config.enableNoisyMicDetection = {{ $ENABLE_NOISY_MIC_DETECTION }};


// Peer-to-Peer options.
//

config.p2p = {
    enabled: {{ $ENABLE_P2P }},
    codecPreferenceOrder: {{ $CODEC_ORDER_P2P }},
    mobileCodecPreferenceOrder: {{ $CODEC_ORDER_P2P_MOBILE }}
};

{{ if .Env.P2P_STUN_SERVERS -}}
config.p2p.stunServers = '{{ .Env.P2P_STUN_SERVERS }}'.split(',').map(function (url) { return { urls: 'stun:' + url }; } );

{{ end -}}

// Breakout Rooms
//

config.hideAddRoomButton = {{ $ENABLE_BREAKOUT_ROOMS | not }};


// Etherpad
//

{{ if .Env.ETHERPAD_PUBLIC_URL -}}
config.etherpad_base = '{{ .Env.ETHERPAD_PUBLIC_URL }}';
{{ else if .Env.ETHERPAD_URL_BASE -}}
config.etherpad_base = '{{ $PUBLIC_URL }}/etherpad/p/';
{{ end -}}


// Recording.
//

{{ if or $ENABLE_RECORDING $ENABLE_TRANSCRIPTIONS  -}}

config.hiddenDomain = '{{ $XMPP_HIDDEN_DOMAIN }}';
{{ end -}}

{{ if $ENABLE_RECORDING -}}

config.recordingService = {
    // Whether to enable file recording or not using the "service" defined by the finalizer in Jibri
    enabled: {{ $ENABLE_SERVICE_RECORDING }},

    // Whether to show the possibility to share file recording with other people
    // (e.g. meeting participants), based on the actual implementation
    // on the backend.
    sharingEnabled: {{ $ENABLE_FILE_RECORDING_SHARING }}
};

// Live streaming configuration.
config.liveStreaming = {
    enabled: {{ $ENABLE_LIVESTREAMING }},
    dataPrivacyLink: '{{ $ENABLE_LIVESTREAMING_DATA_PRIVACY_LINK }}',
    helpLink: '{{ $ENABLE_LIVESTREAMING_HELP_LINK }}',
    termsLink: '{{ $ENABLE_LIVESTREAMING_TERMS_LINK }}',
    validatorRegExpString: '{{ $ENABLE_LIVESTREAMING_VALIDATOR_REGEXP_STRING }}'
};


{{ if .Env.DROPBOX_APPKEY -}}
// Enable the dropbox integration.
config.dropbox = {
    appKey: '{{ .Env.DROPBOX_APPKEY }}'
};

{{ if .Env.DROPBOX_REDIRECT_URI -}}
// A URL to redirect the user to, after authenticating
// by default uses:
// 'https://jitsi-meet.example.com/static/oauth.html'
config.dropbox.redirectURI = '{{ .Env.DROPBOX_REDIRECT_URI }}';
{{ end -}}
{{ end -}}

{{ end -}}

// Local recording configuration.
config.localRecording = {
    disable: {{ $DISABLE_LOCAL_RECORDING }},
    notifyAllParticipants: {{ $ENABLE_LOCAL_RECORDING_NOTIFY_ALL_PARTICIPANT }},
    disableSelfRecording: {{ $ENABLE_LOCAL_RECORDING_SELF_START }}
};


// Analytics.
//

config.analytics = {};

{{ if .Env.AMPLITUDE_ID -}}
// The Amplitude APP Key:
config.analytics.amplitudeAPPKey = '{{ .Env.AMPLITUDE_ID }}';
{{ end -}}

{{ if .Env.GOOGLE_ANALYTICS_ID -}}
// The Google Analytics Tracking ID:
config.analytics.googleAnalyticsTrackingId = '{{ .Env.GOOGLE_ANALYTICS_ID }}';
{{ end -}}

{{ if .Env.MATOMO_ENDPOINT -}}
// Matomo endpoint:
config.analytics.matomoEndpoint = '{{ .Env.MATOMO_ENDPOINT }}';
{{ end -}}

{{ if .Env.MATOMO_SITE_ID -}}
// Matomo site ID:
config.analytics.matomoSiteID = '{{ .Env.MATOMO_SITE_ID }}';
{{ end -}}

{{ if .Env.ANALYTICS_SCRIPT_URLS -}}
// Array of script URLs to load as lib-jitsi-meet "analytics handlers".
config.analytics.scriptURLs = [ '{{ join "','" (splitList "," .Env.ANALYTICS_SCRIPT_URLS | compact) }}' ];
{{ end -}}

{{ if .Env.ANALYTICS_WHITELISTED_EVENTS -}}
config.analytics.whiteListedEvents = [ '{{ join "','" (splitList "," .Env.ANALYTICS_WHITELISTED_EVENTS | compact) }}' ];
{{ end -}}


// Dial in/out services.
//

{{ if $ENABLE_JAAS_COMPONENTS }}
config.dialInConfCodeUrl = 'https://conference-mapper.jitsi.net/v1/access';
config.dialInNumbersUrl = 'https://conference-mapper.jitsi.net/v1/access/dids';
{{ else }}
{{ if .Env.CONFCODE_URL -}}
config.dialInConfCodeUrl = '{{ .Env.CONFCODE_URL }}';
{{ end -}}
{{ if .Env.DIALIN_NUMBERS_URL -}}
config.dialInNumbersUrl = '{{ .Env.DIALIN_NUMBERS_URL }}';
{{ end -}}
{{ end -}}

{{ if .Env.DIALOUT_AUTH_URL -}}
config.dialOutAuthUrl = '{{ .Env.DIALOUT_AUTH_URL }}';
{{ end -}}

{{ if .Env.DIALOUT_CODES_URL -}}
config.dialOutCodesUrl = '{{ .Env.DIALOUT_CODES_URL }}';
{{ end -}}


// Calendar service integration.
//

config.enableCalendarIntegration = {{ $ENABLE_CALENDAR }};

{{ if .Env.GOOGLE_API_APP_CLIENT_ID -}}
config.googleApiApplicationClientID = '{{ .Env.GOOGLE_API_APP_CLIENT_ID }}';
{{ end -}}

{{ if .Env.MICROSOFT_API_APP_CLIENT_ID -}}
config.microsoftApiApplicationClientID = '{{ .Env.MICROSOFT_API_APP_CLIENT_ID }}';
{{ end -}}


// Invitation service.
//

{{ if .Env.INVITE_SERVICE_URL -}}
config.inviteServiceUrl = '{{ .Env.INVITE_SERVICE_URL }}';
{{ end -}}

{{ if .Env.PEOPLE_SEARCH_URL -}}
config.peopleSearchUrl = '{{ .Env.PEOPLE_SEARCH_URL }}';
config.peopleSearchQueryTypes = ['user','conferenceRooms'];
{{ end -}}


// Miscellaneous.
//

// Prejoin page.
config.prejoinConfig = {
    enabled: {{ $ENABLE_PREJOIN_PAGE }},

    // Hides the participant name editing field in the prejoin screen.
    hideDisplayName: {{ $HIDE_PREJOIN_DISPLAY_NAME }}
};

// List of buttons to hide from the extra join options dropdown on prejoin screen.
{{ if .Env.HIDE_PREJOIN_EXTRA_BUTTONS -}}
config.prejoinConfig.hideExtraJoinButtons = [ '{{ join "','" (splitList "," .Env.HIDE_PREJOIN_EXTRA_BUTTONS | compact) }}' ];
{{ end -}}

// Welcome page.
config.welcomePage = {
    disabled: {{ not $ENABLE_WELCOME_PAGE }}
};

// Close page.
config.enableClosePage = {{ $ENABLE_CLOSE_PAGE }};

// Default language.
{{ if .Env.DEFAULT_LANGUAGE -}}
config.defaultLanguage = '{{ .Env.DEFAULT_LANGUAGE }}';
{{ end -}}

// Require users to always specify a display name.
config.requireDisplayName = {{ $ENABLE_REQUIRE_DISPLAY_NAME }};

// Chrome extension banner.
{{ if .Env.CHROME_EXTENSION_BANNER_JSON -}}
config.chromeExtensionBanner = {{ .Env.CHROME_EXTENSION_BANNER_JSON }};
{{ end -}}

// Disables profile and the edit of all fields from the profile settings (display name and email)
config.disableProfile = {{ $DISABLE_PROFILE }};

// Room password (false for anything, number for max digits)
{{ if $ENABLE_JAAS_COMPONENTS -}}
config.roomPasswordNumberOfDigits = 10;
{{ else -}}
config.roomPasswordNumberOfDigits = {{ $ROOM_PASSWORD_DIGITS }};
{{ end -}}

// Advanced.
//

{{ if not $ENABLE_REMB -}}
config.enableRemb = false;
{{ end -}}
{{ if not $ENABLE_TCC -}}
config.enableTcc = false;
{{ end -}}


// Transcriptions (subtitles and buttons can be configured in interface_config)
config.transcription = {
    enabled: {{ $ENABLE_TRANSCRIPTIONS }},
    translationLanguages: {{ $TRANSLATION_LANGUAGES }},
    translationLanguagesHead: {{ $TRANSLATION_LANGUAGES_HEAD }},
    useAppLanguage: {{ $USE_APP_LANGUAGE }},
    preferredLanguage: '{{ $PREFERRED_LANGUAGE }}',
    disableStartForAll: {{ $DISABLE_START_FOR_ALL }},
    autoCaptionOnRecord: {{ $AUTO_CAPTION_ON_RECORD }},
};

// Dynamic branding
{{ if .Env.DYNAMIC_BRANDING_URL -}}
// External API url used to receive branding specific information.
config.dynamicBrandingUrl = '{{ .Env.DYNAMIC_BRANDING_URL }}';
{{ else if .Env.BRANDING_DATA_URL  -}}
config.brandingDataUrl = '{{ .Env.BRANDING_DATA_URL }}';
{{ end -}}

{{ if .Env.TOKEN_AUTH_URL -}}
// Authenticate using external service
config.tokenAuthUrl = '{{ .Env.TOKEN_AUTH_URL }}';
{{ end -}}


// Deployment information.
//

config.deploymentInfo = {};

{{ if .Env.DEPLOYMENTINFO_ENVIRONMENT -}}
config.deploymentInfo.environment = '{{ .Env.DEPLOYMENTINFO_ENVIRONMENT }}';
{{ end -}}

{{ if .Env.DEPLOYMENTINFO_SHARD -}}
config.deploymentInfo.shard = '{{ .Env.DEPLOYMENTINFO_SHARD }}';
{{ end -}}

{{ if .Env.DEPLOYMENTINFO_ENVIRONMENT_TYPE -}}
config.deploymentInfo.envType = '{{ .Env.DEPLOYMENTINFO_ENVIRONMENT_TYPE }}';
{{ end -}}

{{ if .Env.DEPLOYMENTINFO_REGION -}}
config.deploymentInfo.region = '{{ .Env.DEPLOYMENTINFO_REGION }}';
{{ end -}}

// Deep Linking
config.disableDeepLinking = {{ $DISABLE_DEEP_LINKING }};

// P2P preferred codec
{{ if .Env.P2P_PREFERRED_CODEC -}}
config.p2p.preferredCodec = '{{ .Env.P2P_PREFERRED_CODEC }}';
{{ end -}}

// Video quality settings.
//

config.videoQuality = {};
config.videoQuality.codecPreferenceOrder = {{ $CODEC_ORDER_JVB }};
config.videoQuality.mobileCodecPreferenceOrder = {{ $CODEC_ORDER_JVB_MOBILE }};
config.videoQuality.enableAdaptiveMode = {{ $ENABLE_ADAPTIVE_MODE }};

{{ if .Env.VIDEOQUALITY_PREFERRED_CODEC -}}
config.videoQuality.preferredCodec = '{{ .Env.VIDEOQUALITY_PREFERRED_CODEC }}';
{{ end -}}

config.videoQuality.av1 = {};

{{ if .Env.VIDEOQUALITY_BITRATE_AV1_LOW }}
config.videoQuality.av1.low = {{ .Env.VIDEOQUALITY_BITRATE_AV1_LOW }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_AV1_STANDARD }}
config.videoQuality.av1.standard = {{ .Env.VIDEOQUALITY_BITRATE_AV1_STANDARD }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_AV1_HIGH }}
config.videoQuality.av1.high = {{ .Env.VIDEOQUALITY_BITRATE_AV1_HIGH }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_AV1_FULL }}
config.videoQuality.av1.fullHd = {{ .Env.VIDEOQUALITY_BITRATE_AV1_FULL }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_AV1_ULTRA }}
config.videoQuality.av1.ultraHd = {{ .Env.VIDEOQUALITY_BITRATE_AV1_ULTRA }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_AV1_SS_HIGH }}
config.videoQuality.av1.ssHigh = {{ .Env.VIDEOQUALITY_BITRATE_AV1_SS_HIGH }};
{{ end -}}

config.videoQuality.h264 = {};

{{ if .Env.VIDEOQUALITY_BITRATE_H264_LOW }}
config.videoQuality.h264.low = {{ .Env.VIDEOQUALITY_BITRATE_H264_LOW }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_H264_STANDARD }}
config.videoQuality.h264.standard = {{ .Env.VIDEOQUALITY_BITRATE_H264_STANDARD }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_H264_HIGH }}
config.videoQuality.h264.high = {{ .Env.VIDEOQUALITY_BITRATE_H264_HIGH }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_H264_FULL }}
config.videoQuality.h264.fullHd = {{ .Env.VIDEOQUALITY_BITRATE_H264_FULL }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_H264_ULTRA }}
config.videoQuality.h264.ultraHd = {{ .Env.VIDEOQUALITY_BITRATE_H264_ULTRA }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_H264_SS_HIGH }}
config.videoQuality.h264.ssHigh = {{ .Env.VIDEOQUALITY_BITRATE_H264_SS_HIGH }};
{{ end -}}

config.videoQuality.vp8 = {};

{{ if .Env.VIDEOQUALITY_BITRATE_VP8_LOW }}
config.videoQuality.vp8.low = {{ .Env.VIDEOQUALITY_BITRATE_VP8_LOW }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_VP8_STANDARD }}
config.videoQuality.vp8.standard = {{ .Env.VIDEOQUALITY_BITRATE_VP8_STANDARD }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_VP8_HIGH }}
config.videoQuality.vp8.high = {{ .Env.VIDEOQUALITY_BITRATE_VP8_HIGH }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_VP8_FULL }}
config.videoQuality.vp8.fullHd = {{ .Env.VIDEOQUALITY_BITRATE_VP8_FULL }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_VP8_ULTRA }}
config.videoQuality.vp8.ultraHd = {{ .Env.VIDEOQUALITY_BITRATE_VP8_ULTRA }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_VP8_SS_HIGH }}
config.videoQuality.vp8.ssHigh = {{ .Env.VIDEOQUALITY_BITRATE_VP8_SS_HIGH }};
{{ end -}}

config.videoQuality.vp9 = {};

{{ if .Env.VIDEOQUALITY_BITRATE_VP9_LOW }}
config.videoQuality.vp9.low = {{ .Env.VIDEOQUALITY_BITRATE_VP9_LOW }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_VP9_STANDARD }}
config.videoQuality.vp9.standard = {{ .Env.VIDEOQUALITY_BITRATE_VP9_STANDARD }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_VP9_HIGH }}
config.videoQuality.vp9.high = {{ .Env.VIDEOQUALITY_BITRATE_VP9_HIGH }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_VP9_FULL }}
config.videoQuality.vp9.fullHd = {{ .Env.VIDEOQUALITY_BITRATE_VP9_FULL }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_VP9_ULTRA }}
config.videoQuality.vp9.ultraHd = {{ .Env.VIDEOQUALITY_BITRATE_VP9_ULTRA }};
{{ end -}}

{{ if .Env.VIDEOQUALITY_BITRATE_VP9_SS_HIGH }}
config.videoQuality.vp9.ssHigh = {{ .Env.VIDEOQUALITY_BITRATE_VP9_SS_HIGH }};
{{ end -}}

 // Reactions
config.disableReactions = {{ $DISABLE_REACTIONS }};

// Polls
config.disablePolls = {{ $DISABLE_POLLS }};

// Configure toolbar buttons
{{ if .Env.TOOLBAR_BUTTONS -}}
config.toolbarButtons = [ '{{ join "','" (splitList "," .Env.TOOLBAR_BUTTONS | compact) }}' ];
{{ end -}}

// Hides the buttons at pre-join screen
{{ if .Env.HIDE_PREMEETING_BUTTONS -}}
config.hiddenPremeetingButtons = [ '{{ join "','" (splitList "," .Env.HIDE_PREMEETING_BUTTONS | compact) }}' ];
{{ end -}}

// Configure remote participant video menu
config.remoteVideoMenu = {
    disabled: {{ $DISABLE_REMOTE_VIDEO_MENU }},
    disableKick: {{ $DISABLE_KICKOUT }},
    disableGrantModerator: {{ $DISABLE_GRANT_MODERATOR }},
    disablePrivateChat: {{ $DISABLE_PRIVATE_CHAT }}
};

// Configure e2eping
config.e2eping = {
    enabled: {{ $ENABLE_E2EPING }}
};

{{ if .Env.E2EPING_NUM_REQUESTS -}}
config.e2eping.numRequests = {{ .Env.E2EPING_NUM_REQUESTS }};
{{ end -}}
{{ if .Env.E2EPING_MAX_CONFERENCE_SIZE -}}
config.e2eping.maxConferenceSize = {{ .Env.E2EPING_MAX_CONFERENCE_SIZE }};
{{ end -}}
{{ if .Env.E2EPING_MAX_MESSAGE_PER_SECOND -}}
config.e2eping.maxMessagePerSecond = {{ .Env.E2EPING_MAX_MESSAGE_PER_SECOND }};
{{ end }}

// Settings for the Excalidraw whiteboard integration.
config.whiteboard = {
    enabled: {{ $WHITEBOARD_ENABLED }},
{{ if .Env.WHITEBOARD_COLLAB_SERVER_PUBLIC_URL -}}
    collabServerBaseUrl: '{{ .Env.WHITEBOARD_COLLAB_SERVER_PUBLIC_URL }}'
{{ else if .Env.WHITEBOARD_COLLAB_SERVER_URL_BASE -}}
    collabServerBaseUrl: '{{ $PUBLIC_URL }}'
{{ end -}}
};

// JaaS support: pre-configure image if JAAS_APP_ID was set.
{{ if .Env.JAAS_APP_ID -}}
{{ $JAAS_USE_STAGING := .Env.JAAS_USE_STAGING | default "false" | toBool -}}
{{ $JAAS_DOMAIN := $JAAS_USE_STAGING | ternary "stage.8x8.vc" "8x8.vc" -}}

config.hosts.domain = '{{ $JAAS_DOMAIN }}';
config.hosts.muc = 'conference.{{ .Env.JAAS_APP_ID }}.{{ $JAAS_DOMAIN }}';
config.hosts.focus = 'focus.{{ $JAAS_DOMAIN }}';

config.analytics.rtcstatsEnabled = true;
config.analytics.rtcstatsStoreLogs = true;
config.analytics.rtcstatsUseLegacy = false;
config.analytics.rtcstatsEndpoint = 'wss://rtcstats-server-8x8.jitsi.net/';
config.analytics.rtcstatsPollInterval = 10000;
config.analytics.rtcstatsSendSdp = true;

config.bosh = 'https://{{ $JAAS_DOMAIN }}/{{ .Env.JAAS_APP_ID }}/http-bind';
config.websocket = 'wss://{{ $JAAS_DOMAIN }}/{{ .Env.JAAS_APP_ID }}/xmpp-websocket';
config.websocketKeepAliveUrl = 'https://{{ $JAAS_DOMAIN }}/{{ .Env.JAAS_APP_ID }}/_unlock';
config.conferenceRequestUrl = 'https://{{ $JAAS_DOMAIN }}/{{ .Env.JAAS_APP_ID }}/conference-request/v1';

config.hiddenDomain = 'recorder.{{ $JAAS_DOMAIN }}';
config.hiddenFromRecorderFeatureEnabled = true;
config.enableEmailInStats = true;

config.jaasActuatorUrl = 'https://{{ $JAAS_DOMAIN }}/v1/_jaas/jaas-actuator';
config.jaasTokenUrl = 'https://{{ $JAAS_DOMAIN }}/v1/_jaas/token-mapping';
config.jaasConferenceCreatorUrl = 'https://{{ $JAAS_DOMAIN }}/v1/_jaas/vmms-conference-mapper/v1/access/conference-creator';
config.webhookProxyUrl = 'https://{{ $JAAS_DOMAIN }}/v1/_jaas/webhook-proxy';
config.billingCounterUrl = 'https://{{ $JAAS_DOMAIN }}/v1/_jaas/billing-counter/v1/connection';
config.brandingDataUrl = 'https://{{ $JAAS_DOMAIN }}/v1/_jaas/branding/public/v1/conferences';
config.dialInNumbersUrl = 'https://{{ $JAAS_DOMAIN }}/v1/_jaas/vmms-conference-mapper/access/v1/dids';
config.dialInConfCodeUrl = 'https://{{ $JAAS_DOMAIN }}/v1/_jaas/vmms-conference-mapper/v1/access';
config.dialOutAuthUrl = 'https://{{ $JAAS_DOMAIN }}/v1/_jaas/phone-authorize';
config.dialOutRegionUrl = 'https://{{ $JAAS_DOMAIN }}/v1/_jaas/customer-configs/v1/outbound-destination';
config.peopleSearchUrl = 'https://{{ $JAAS_DOMAIN }}/v1/_jaas/v1/directory/search';
config.inviteServiceUrl = 'https://{{ $JAAS_DOMAIN }}/v1/_jaas/v1/meeting/invite';
config.recordingSharingUrl = 'https://{{ $JAAS_DOMAIN }}/v1/_jaas/jaas-recordings/link';
config.peopleSearchQueryTypes = ['user','conferenceRooms'];
config.sipInviteUrl = 'https://{{ $JAAS_DOMAIN }}/v1/_jaas/sip-jibri-gateway/jibris/invite';
config.jaasFeedbackMetadataURL = 'https://{{ $JAAS_DOMAIN }}/v1/_jaas/webhook-proxy/feedback';

{{ if $JAAS_USE_STAGING -}}
config.whiteboard.collabServerBaseUrl = 'https://eght-excalidraw-backend-pilot.cloudflare.jitsi.net';
{{ else -}}
config.whiteboard.collabServerBaseUrl = 'https://eght-excalidraw-backend.cloudflare.jitsi.net';
{{ end -}}
config.whiteboard.userLimit = 25;
{{ end -}}

// Testing
config.testing = {
    enableCodecSelectionAPI: true
};
````

## File: web/rootfs/defaults/ssl.conf
````
# session settings
ssl_session_timeout 1d;
ssl_session_cache shared:MozSSL:10m;  # about 40000 sessions
ssl_session_tickets off;

# ssl certs
{{ if .Env.ENABLE_LETSENCRYPT | default "0" | toBool }}
ssl_certificate /config/acme-certs/{{ .Env.LETSENCRYPT_DOMAIN }}/fullchain.pem;
ssl_certificate_key /config/acme-certs/{{ .Env.LETSENCRYPT_DOMAIN }}/key.pem;
{{ else }}
ssl_certificate /config/keys/cert.crt;
ssl_certificate_key /config/keys/cert.key;
{{ end }}

# protocols
# Mozilla Guideline v5.6, nginx 1.14.2, OpenSSL 1.1.1d, intermediate configuration, no OCSP
# https://ssl-config.mozilla.org/#server=nginx&version=1.14.2&config=intermediate&openssl=1.1.1d&ocsp=false&guideline=5.6
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
ssl_prefer_server_ciphers off;

# Diffie-Hellman parameter for DHE cipher suites
ssl_dhparam /defaults/ffdhe2048.txt;

# HSTS (ngx_http_headers_module is required) (63072000 seconds)
{{ if .Env.ENABLE_HSTS | default "1" | toBool }}
add_header Strict-Transport-Security "max-age=63072000" always;
{{ end }}
````

## File: web/rootfs/defaults/system-config.js
````javascript
{{ $CONFIG_PREFIX := .Env.WEB_CONFIG_PREFIX | default "// Jitsi Meet configuration.\n" -}}
{{ $BOSH_RELATIVE := .Env.BOSH_RELATIVE | default "false" | toBool -}}
{{ $ENABLE_AUTH := .Env.ENABLE_AUTH | default "false" | toBool -}}
{{ $ENABLE_AUTH_DOMAIN := .Env.ENABLE_AUTH_DOMAIN | default "true" | toBool -}}
{{ $ENABLE_GUESTS := .Env.ENABLE_GUESTS | default "false" | toBool -}}
{{ $ENABLE_SUBDOMAINS := .Env.ENABLE_SUBDOMAINS | default "true" | toBool -}}
{{ $ENABLE_XMPP_WEBSOCKET := .Env.ENABLE_XMPP_WEBSOCKET | default "1" | toBool -}}
{{ $PUBLIC_URL_DOMAIN := .Env.PUBLIC_URL | default "https://localhost:8443" | trimPrefix "https://" | trimSuffix "/" -}}
{{ $XMPP_AUTH_DOMAIN := .Env.XMPP_AUTH_DOMAIN | default "auth.meet.jitsi" -}}
{{ $XMPP_DOMAIN := .Env.XMPP_DOMAIN | default "meet.jitsi" -}}
{{ $XMPP_GUEST_DOMAIN := .Env.XMPP_GUEST_DOMAIN | default "guest.meet.jitsi" -}}
{{ $XMPP_MUC_DOMAIN := .Env.XMPP_MUC_DOMAIN | default "muc.meet.jitsi" -}}
{{ $XMPP_MUC_DOMAIN_PREFIX := (split "." $XMPP_MUC_DOMAIN)._0  -}}
{{ $JVB_PREFER_SCTP := .Env.JVB_PREFER_SCTP | default "1" | toBool -}}

{{ join "\n" (splitList "\\n" $CONFIG_PREFIX) }}
var config = {};

config.hosts = {};
config.hosts.domain = '{{ $XMPP_DOMAIN }}';

{{ if $ENABLE_SUBDOMAINS -}}
var subdir = '<!--# echo var="subdir" default="" -->';
var subdomain = '<!--# echo var="subdomain" default="" -->';
if (subdir.startsWith('<!--')) {
    subdir = '';
}
if (subdomain) {
    subdomain = subdomain.substring(0,subdomain.length-1).split('.').join('_').toLowerCase() + '.';
}
config.hosts.muc = '{{ $XMPP_MUC_DOMAIN_PREFIX }}.' + subdomain + '{{ $XMPP_DOMAIN }}';
{{ else -}}
config.hosts.muc = '{{ $XMPP_MUC_DOMAIN }}';
{{ end -}}

{{ if $ENABLE_AUTH -}}
{{ if $ENABLE_GUESTS -}}
// When using authentication, domain for guest users.
config.hosts.anonymousdomain = '{{ $XMPP_GUEST_DOMAIN }}';
{{ end -}}
{{ if $ENABLE_AUTH_DOMAIN -}}
// Domain for authenticated users. Defaults to <domain>.
config.hosts.authdomain = '{{ $XMPP_DOMAIN }}';
{{ end -}}
{{ end -}}

{{ if $BOSH_RELATIVE -}}
{{ if $ENABLE_SUBDOMAINS -}}
config.bosh = '/'+ subdir + 'http-bind';
{{ else -}}
config.bosh = '/http-bind';
{{ end -}}
{{ else -}}
{{ if $ENABLE_SUBDOMAINS -}}
config.bosh = 'https://{{ $PUBLIC_URL_DOMAIN}}/' + subdir + 'http-bind';
{{ else -}}
config.bosh = 'https://{{ $PUBLIC_URL_DOMAIN}}/http-bind';
{{ end -}}
{{ end -}}

{{ if $ENABLE_XMPP_WEBSOCKET -}}
{{ if $ENABLE_SUBDOMAINS -}}
config.websocket = 'wss://{{ $PUBLIC_URL_DOMAIN }}/' + subdir + 'xmpp-websocket';
{{ else -}}
config.websocket = 'wss://{{ $PUBLIC_URL_DOMAIN }}/xmpp-websocket';
{{ end -}}
{{ end -}}

config.bridgeChannel = {
    preferSctp: {{ $JVB_PREFER_SCTP }}
};
````

## File: web/rootfs/etc/cont-init.d/10-config
````
#!/usr/bin/with-contenv bash

# make our folders
mkdir -p \
    /config/{nginx/site-confs,keys} \
    /run \
    /var/lib/nginx/tmp/client_body \
    /var/tmp/nginx

# generate keys (maybe)
if [[ $DISABLE_HTTPS -ne 1 ]]; then
    if [[ $ENABLE_LETSENCRYPT -eq 1 ]]; then
        mkdir -p /config/acme.sh
        pushd /opt
        sh ./acme.sh --install --home /config/acme.sh --accountemail $LETSENCRYPT_EMAIL
        popd

        STAGING=""
        if [[ $LETSENCRYPT_USE_STAGING -eq 1 ]]; then
            STAGING="--staging"
        fi

        ACME_SERVER=""
        if [[ ! -z $LETSENCRYPT_ACME_SERVER ]]; then
            ACME_SERVER="--set-default-ca --server $LETSENCRYPT_ACME_SERVER"
            echo "Using custom ACME server: $LETSENCRYPT_ACME_SERVER"
        fi

        export LE_WORKING_DIR="/config/acme.sh"
        # TODO: move away from standalone mode to webroot mode.
        /config/acme.sh/acme.sh \
            $STAGING \
            $ACME_SERVER \
            --issue \
            --standalone \
            --pre-hook "if [[ -d /var/run/s6/services/nginx ]]; then s6-svc -d /var/run/s6/services/nginx; fi" \
            --post-hook "if [[ -d /var/run/s6/services/nginx ]]; then s6-svc -u /var/run/s6/services/nginx; fi" \
            -d $LETSENCRYPT_DOMAIN
        rc=$?
        if [[ $rc -eq 1 ]]; then
            echo "Failed to obtain a certificate from the Let's Encrypt CA."
            # this tries to get the user's attention and to spare the
            # authority's rate limit:
            sleep 15
            echo "Exiting."
            exit 1
        fi
        if [[ $rc -eq 0 ]]; then
            mkdir -p /config/acme-certs/$LETSENCRYPT_DOMAIN
            if ! /config/acme.sh/acme.sh \
                    --install-cert -d $LETSENCRYPT_DOMAIN \
                    --key-file /config/acme-certs/$LETSENCRYPT_DOMAIN/key.pem  \
                    --fullchain-file /config/acme-certs/$LETSENCRYPT_DOMAIN/fullchain.pem ; then
                echo "Failed to install certificate."
                # this tries to get the user's attention and to spare the
                # authority's rate limit:
                sleep 15
                echo "Exiting."
                exit 1
            fi
        fi
    else
        # use self-signed certs
        if [[ -f /config/keys/cert.key && -f /config/keys/cert.crt ]]; then
            echo "using keys found in /config/keys"
        else
            echo "generating self-signed keys in /config/keys, you can replace these with your own keys if required"
            SUBJECT="/C=US/ST=TX/L=Austin/O=jitsi.org/OU=Jitsi Server/CN=*"
            openssl req -new -x509 -days 3650 -nodes -out /config/keys/cert.crt -keyout /config/keys/cert.key -subj "$SUBJECT"
        fi
    fi
fi

# Detect nameserver for Nginx, if not specified.
if [[ -z "$NGINX_RESOLVER" ]]; then
    IP_LIST=""

    # Parse IPs in /etc/resolv.conf, taking into account IPv6 addresses need to be
    # enclosed in square brackets for the Nginx config file.
    while read -r line; do
        if [[ $line =~ ^nameserver.* ]]; then
            IP=$(echo $line | cut -d" " -f2)
            COLONS=$(echo $IP | tr -dc ":" | awk '{ print length '})
            if [[ $COLONS -ge 2 ]]; then
                IP="[$IP]"
            fi
            if [[ ! "$IP_LIST" = "" ]]; then
                IP_LIST+=" "
            fi
            IP_LIST+="$IP"
        fi
    done < <(cat /etc/resolv.conf)

    export NGINX_RESOLVER=$IP_LIST
fi

echo "Using Nginx resolver: =$NGINX_RESOLVER="

# colibri-ws settings
COLIBRI_WEBSOCKET_UNSAFE_REGEX="[a-zA-Z0-9-\._]+"
# use custom websocket regex if provided
if [ -z "$COLIBRI_WEBSOCKET_REGEX" ]; then
    # default to the previous unsafe behavior only if flag is set
    if [[ "$ENABLE_COLIBRI_WEBSOCKET_UNSAFE_REGEX" == "1" ]]; then
        export COLIBRI_WEBSOCKET_REGEX="$COLIBRI_WEBSOCKET_UNSAFE_REGEX"
    else
        # default value to the JVB IP, works in compose and anywhere a dns lookup of the JVB reveals the correct IP for proxying
        [ -z "$COLIBRI_WEBSOCKET_JVB_LOOKUP_NAME" ] && export COLIBRI_WEBSOCKET_JVB_LOOKUP_NAME="jvb"
        if [[ "$DISABLE_COLIBRI_WEBSOCKET_JVB_LOOKUP" == "1" ]]; then
            # otherwise value default to the static value in the template 'jvb'
            echo "WARNING: DISABLE_COLIBRI_WEBSOCKET_JVB_LOOKUP is set and no value for COLIBRI_WEBSOCKET_REGEX was provided, using static value 'jvb' for COLIBRI_WEBSOCKET_REGEX"
        else
            export COLIBRI_WEBSOCKET_REGEX="$(dig +short +search $COLIBRI_WEBSOCKET_JVB_LOOKUP_NAME)"
        fi
    fi
fi

# maintain backward compatibility with older variable
[ -z "${XMPP_HIDDEN_DOMAIN}" ] && export XMPP_HIDDEN_DOMAIN="$XMPP_RECORDER_DOMAIN"

# copy config files
tpl /defaults/nginx.conf > /config/nginx/nginx.conf

tpl /defaults/meet.conf > /config/nginx/meet.conf
if [[ -f /config/nginx/custom-meet.conf ]]; then
    cat /config/nginx/custom-meet.conf >> /config/nginx/meet.conf
fi

tpl /defaults/ssl.conf > /config/nginx/ssl.conf

tpl /defaults/default > /config/nginx/site-confs/default

tpl /defaults/system-config.js > /config/config.js
tpl /defaults/settings-config.js >> /config/config.js
if [[ -f /config/custom-config.js ]]; then
    cat /config/custom-config.js >> /config/config.js
fi

cp /defaults/interface_config.js /config/interface_config.js
if [[ -f /config/custom-interface_config.js ]]; then
    cat /config/custom-interface_config.js >> /config/interface_config.js
fi
````

## File: web/rootfs/etc/services.d/cron/run
````
#!/usr/bin/with-contenv bash

if [[ $DISABLE_HTTPS -ne 1 ]] && [[ $ENABLE_LETSENCRYPT -eq 1 ]]; then
    exec cron -f
else
    # if cron should not be started,
    # prevent s6 from restarting this script again and again
    s6-svc -O /var/run/s6/services/cron
fi
````

## File: web/rootfs/etc/services.d/jaas-account/run
````
#!/usr/bin/with-contenv bash

set -e

EMAIL=$LETSENCRYPT_EMAIL
DOMAIN=$LETSENCRYPT_DOMAIN

JITSI_INSTALLATION="DOCKER"
JAAS_ENDPOINT="https://account-provisioning.cloudflare.jitsi.net/operations"
CHALLENGE_DIR="/usr/share/jitsi-meet/.well-known"
CHALLENGE_FILE="$CHALLENGE_DIR/jitsi-challenge.txt"
SUPPORT_MSG="Reach out to JaaS support at https://jaas.8x8.vc/#components"
JAAS_ACCOUNT_FILE="/config/jaas-account-created.txt"

function stop_service() {
  s6-svc -O /var/run/s6/services/jaas-account
  exit 0
}

if [[ $DISABLE_HTTPS -ne 1 ]] && [[ $ENABLE_LETSENCRYPT -eq 1 ]] && [[ $ENABLE_JAAS_COMPONENTS -eq 1 ]]  && [[ ! -z $EMAIL ]] && [[ ! -z $DOMAIN ]]; then

if [ -f $JAAS_ACCOUNT_FILE ]; then
    echo "JaaS account already exists"
    stop_service
fi

KEEP_WAITING=true
RETRIES=0
MAX_TRIES=5
SLEEP_INTERVAL=10
# Waiting for nginx to start before creating the JaaS account
while $KEEP_WAITING; do
    s6-svwait -u /var/run/s6/services/nginx
    NGINX_RESPONSE=$?
    if [ $NGINX_RESPONSE -eq 0 ]; then
        echo "Nginx started"
        KEEP_WAITING=false
    else
        RETRIES=$((RETRIES + 1))
        if [ $RETRIES -ge $MAX_TRIES ]; then
            echo "Nginx did not start, exiting..."
            KEEP_WAITING=false
        else
            echo "Waiting for nginx to start, retrying in $SLEEP_INTERVAL seconds... $RETRIES/$MAX_TRIES"
            sleep $SLEEP_INTERVAL
        fi
    fi
done

create_error=0
create_data=$(curl -s -f -X 'POST' "${JAAS_ENDPOINT}" -H 'Content-Type: application/json' -H 'accept: */*' -d "{ \"domain\": \"${DOMAIN}\", \"email\": \"${EMAIL}\", \"jitsiInstallation\": \"${JITSI_INSTALLATION}\" }") || create_error=$?
if [ ${create_error} -ne 0 ]; then
    echo "JaaS account creation failed. Status: ${create_error}, response: ${create_data}"
    stop_service
fi

echo "${create_data}"

# Creating the challenge dir
mkdir -p ${CHALLENGE_DIR}
# Creating the challenge file
echo "${create_data}" | jq -r .challenge > ${CHALLENGE_FILE}

op_id=$(echo "${create_data}" | jq -r .operationId)
ready_error=0
ready_data=$(curl -s -f -X 'PUT' "${JAAS_ENDPOINT}/${op_id}/ready") || ready_error=$?
if [ ${ready_error} -ne 0 ]; then
    echo "Jitsi domain validation failed. Status: ${ready_error}"
    echo "Response: "
    echo "${ready_data}" | jq -r
    echo "${SUPPORT_MSG}"
    echo
    stop_service
fi

SLEEP_TIME=0
WAIT_BEFORE_CHECK=5
TIMEOUT=60
echo -n "Creating the JaaS account..."
(while true; do
    provisioned_data=$(curl -s -f "${JAAS_ENDPOINT}/${op_id}")

    status=$(echo "${provisioned_data}" | jq -r .status)

    if [ "${status}" == "PROVISIONED" ]; then
        echo ""
        echo "=================="
        echo ""
        echo "A JaaS account was created. Please check your email for more details."
        echo ""
        echo "=================="
        # Creating the jaas-account file
        touch ${JAAS_ACCOUNT_FILE}
        stop_service
    elif  [ "${status}" == "FAILED" ]; then
        echo ""
        echo "=================="
        echo ""
        echo "JaaS account creation failed:${provisioned_data}"
        echo ""
        echo "=================="
        stop_service
    fi

    if [ ${SLEEP_TIME} -ge ${TIMEOUT} ]; then
        echo ""
        echo "=================="
        echo ""
        echo "Timeout creating the JaaS account. ${SUPPORT_MSG}"
        echo ""
        echo "=================="
        stop_service
    fi

    echo -n "Waiting for the JaaS account to be created..."
    sleep ${WAIT_BEFORE_CHECK}
    SLEEP_TIME=$((SLEEP_TIME+WAIT_BEFORE_CHECK))
done)
rm ${CHALLENGE_FILE} || true

fi
stop_service
````

## File: web/rootfs/etc/services.d/nginx/run
````
#!/usr/bin/with-contenv bash

exec nginx -c /config/nginx/nginx.conf
````

## File: web/Dockerfile
````
ARG JITSI_REPO=jitsi
ARG BASE_TAG=latest
FROM ${JITSI_REPO}/base:${BASE_TAG}

LABEL org.opencontainers.image.title="Jitsi Meet"
LABEL org.opencontainers.image.description="WebRTC compatible JavaScript application that uses Jitsi Videobridge to provide high quality, scalable video conferences."
LABEL org.opencontainers.image.url="https://jitsi.org/jitsi-meet/"
LABEL org.opencontainers.image.source="https://github.com/jitsi/docker-jitsi-meet"
LABEL org.opencontainers.image.documentation="https://jitsi.github.io/handbook/"

ADD https://raw.githubusercontent.com/acmesh-official/acme.sh/3.0.7/acme.sh /opt
COPY rootfs/ /

RUN apt-dpkg-wrap apt-get update && \
    apt-dpkg-wrap apt-get install -y dnsutils cron nginx-extras jitsi-meet-web socat curl jq && \
    mv /usr/share/jitsi-meet/interface_config.js /defaults && \
    rm -f /etc/nginx/conf.d/default.conf && \
    apt-cleanup

EXPOSE 80 443

VOLUME ["/config", "/usr/share/jitsi-meet/transcripts"]
````

## File: .gitignore
````
*.bak
*.swp
.env*
docker-compose.override.yml
log-analyser/grafana
**/.DS_Store
**/.idea
````

## File: CHANGELOG.md
````markdown
# ChangeLog

Look at the GH releases from now on.

## stable-10008

Based on stable release 10008.

* 1f7544c fix(build) fix local build
* 2a329fa feat(web): include custom nginx conf files
* 862986a feat(web): Drops userRegion comes from backend now.
* 2b5c291 misc: working on unstable

## stable-9955

Based on stable release 9955.

* 3ba77e1 feat(prosody) - Added TURN_USERNAME and TURN_PASSWORD (#1989)
* 82e98d2 web: Added P2P_STUN_SERVERS environment variable (#1990)
* 02499f8 feat(grafana): prosody monitoring dashboard created (#1879)
* 3af0725 web: add option param for Automatic Gain Control
* 9bc57e2 misc: working on unstable

## stable-9909

Based on stable release 9909.

* 9098202 feat(lint): add tpl lint step for each project (#1983)
* 70b75f5 feat(build): Checks config.js syntax. (#1981)
* f18b182 fix(web): config.js syntax errors (#1980)
* eea18b1 feat: Add an option to enable VLA. (#1979)
* 9527545 feat(web) Add defaults and env variables for codec preference. (#1978)
* 6ae3414 feat(prosody): Sets recorder prefixes.
* 07ea79a feat(prosody): Enables connection resumption for jicofo and jvb.
* 5341065 Update docker-compose.yml (#1968)
* 5e8b702 fix(prosody-visitors): proper variable for transcriber jid in rate limit (#1967)
* d3db3a5 fix(jibri,jicofo,jigasi,prosody,web): respect previous XMPP_RECORDER_DOMAIN value if set (#1966)
* 2d9c3cc feat(web): allow comment at the top of config.js to be overridden (#1964)
* 9fe5b8b fix(web) Remove no longer used config setting. Av1 has already been enabled by default.
* a53775c misc: working on unstable

## stable-9823

Based on stable release 9823.

* 51a1214 feat(jibri) bump Chrome to version 130
* 345f8c2 fix(prosody,jicofo,jigasi,jibri) rename recorder domain to hidden domain
* 47d974d feat(jibri,jicofo,jvb): custom hocon support via include for components that support it (#1960)
* 6443c6f feat(transcriber): fix exposing transcript path in web
* 74d913f feat(transcriber): flag to enable translations (#1953)
* 8909050 feat(transcriber): remote config auth parameters (#1951)
* bb17a29 feat(prosody): enable muc_max_participants in lobby component (#1950)
* 26b6f27 fix(prosody): remove admin_adhoc module from defaults (#1948)
* 4ae7438 feat(prosody): move external_services module and config to global prosody.cfg.lua (#1949)
* 74a536e misc: working on unstable

## stable-9779

Based on stable release 9779.

* a5b719c :sparkles: allow configuration of a custom translation service (such as libretranslate) (#1946)
* d6251b7 feat(jaas): updated jaas URLs (#1944)
* 2a87a50 fix(jigasi): ensure tpl doesn't fail even if no JIGASI_SIP_PASSWORD is set (#1943)
* 5164db7 feat(jigasi): config for jigasi in visitor mode (#1942)
* a88c6e4 feat(prosody): allow s2s whitelist additions (#1940)
* b89c328 fix(prosody): install the new version of jitsi-contrib/prosody-plugins
* 0b75cd1 fix(transcriber): fix bosh and xmpp connections from jigasi to prosody (#1934)
* 4369907 feat(jibri,jicofo,jigasi,jvb,prosody,web): trim empty list entries when splitting with splitList to allow trailing comma (#1932)
* c58a9e5 feat(prosody): allow components access even after max participants is reached (#1930)
* 5e1fcd8 fix(prosody): default value for PROSODY_MODE (#1929)
* 7b7f101 feat(prosody): new flags to disable incoming client and server conection limits (#1928)
* 02aa4f1 fix: Only set app_secret when defined. (#1925)
* df0bb25 fix(prosody): install the new version of jitsi-contrib/prosody-plugins (#1926)
* ff0cdc7 fix(prosody): tpl errors on container startup due to missing variables (#1924)
* 905943d feat(prosody) update prosody contrib plugins from 20240117 to 20240817
* 1f2b14d feat(prosody): enable recorder domain for s2s to visitor nodes to allow transcription support (#1905)
* c9b6679 feat(web): mount load-test volume to expose load-test client from host (#1910)
* b726312 feat(jicofo) add config parameter for ENABLE_MODERATOR_CHECKS
* 2d39624 misc: working on unstable

## stable-9753

Based on stable release 9753.

* 9e17f95 feat(jigasi): allow unique account UID for each entry even if the same auth is used (#1917)
* ad2a577 fix(jigasi): transcriber VOSK URL variable into compose file (#1916)
* 6c08391 feat(jigasi): transcriber configuration supports vosk URL (#1915)
* 1db83ed feat(ldap): Start and configure only if enabled.
* ceb4a51 feat(prosody): Skip some modules for prosody-jvb.
* e7786d5 feat(jigasi): transcriber configuration for OCI and whisper and to skip saving (#1909)
* c5afcde feat(jigasi): support graceful shutdown via sidecar (#1908)
* 5d40297 feat(jigasi): allow optional label for xmpp servers (#1907)
* e7cb0f4 feat(jvb): autoscaler sidecar shutdown hook on finish (#1906)
* b0675c0 prosody: recording metadata module (#1734)
* 36b2e16 feat(web) - Added NGINX_KEEPALIVE_TIMEOUT environment variable
* 24bf3e4 fix(web) add HTTPS port to URL in env.example
* 2280f6b feat(web) add ability to run the web container targetting JaaS
* ff49cd9 Revert "feat(jigasi): install openjdk nonheadless, currently required for dep…" (#1899)
* 66c6ad8 feat(jigasi): install openjdk nonheadless, currently required for dependencies (#1895)
* 3f67408 feat(jigasi,transcriber): transcriber component and jigasi configuration updates (#1881)
* 3e93212 fix(jicofo): pass compose variable for jicofo transcription support (#1893)
* 084266c feat(transcriber): transcriber password in env.example and gen-passwords.sh (#1894)
* e1b14f2 feat(prosody): enable brewery muc modules in internal muc component (#1892)
* 1889c5d fix(web): enable hidden domain when transcriptions or recordings are enabled (#1891)
* 46377d4 feat(prosody): transcriber account support and flag configuration fix (#1890)
* 9f2489d feat(log-analyser): updated grafana dashboards (#1885)
* 1e589f3 feat(log-analyser): transfered docker logs from docker containers and updated readme (#1884)
* 1f056b4 web: remove unnecessary option
* f893e49 base: update tpl
* 7617503 fix(log-analyser): added Jitsi network to loki service
* 27150a2 feat(prometheus): Docker Daemon scraping for monitoring (#1865)
* d6b64a2 misc: working on unstable

## stable-9646

Based on stable release 9646.

* 0dad31d fix(jicofo) enable jigasi brewery with ENABLE_TRANSCRIPTIONS
* fae66ad fix(jibri): IGNORE_CERTIFICATE_ERRORS as boolean
* 54baee2 feat(prosody): custom metrics of stanza counts for prosody service implemented (#1862)
* a88dd8b feat(jvb) default to using dcSCTP
* d2c26aa fix(jigasi) add missing variables to jigasi.yml
* 41559d6 feat(log-analyser): added grafana dashboard provisioning (#1855)
* 2e9e6ca misc: working on unstable

## stable-9584-1

Based on stable release 9584-1.

* 29a4523 feat(jvb) add JVB_CC_TRUST_BWE environment variable
* 0f7be54 feat(prometheus): Prometheus container and basic scraping configuration  (#1838)
* 9c87bba feat(jitsi-meet): added grafana loki and otel integration for log analysis (#1844)
* fcb90ba fix(web): whiteboard config.js syntax error (#1851)
* 811518b misc: working on unstable

## stable-9584

Based on stable release 9584.

* b4604f3 feat(whiteboard) add builtin whiteboard integration
* 49bd165 feat(jibri) update Chrome to 126
* 980703b fix(jibri): display modes in xorg config
* c65d2bd feat(jigasi,jicofo,jvb,jibri,prosody): add healthcheck.sh scripts and prosody http_health mod
* d2ac43d feat(jigasi): Add new environment variables for jigasi configuration (#1841)
* fbc250e feat(prosody): openmetrics module support (#1832)
* 2adac72 feat(prosody): Drop non existing config.
* e22b4f3 feat(jibri): Adds more fonts.
* 982e577 feat(jicofo,jvb,web) default to using SCTP based datachannels
* 55c975f fix(jicofo): use integer for port value to fix addition (#1826)
* 7c8763f base: update tpl to version 1.3.0
* 1d3c0f1 jibri: fail to start if the SYS_ADMIN cap is missing (#1816)
* cacee4e jvb: adds autoscaler sidecar hook to signal final shutdown (#1809)
* 726b0f0 jibri: adds autoscaler sidecar hook to signal final shutdown (#1810)
* f2b2cc4 etherpad: update image version and settings
* 731d6d1 base: update tpl to make toBool more resilient
* 59c4eb2 jvb: add fallback WS server ID
* 9a54843 jvb: add ability to configure the TLS option for colibri WS
* 79a4635 jvb: add ability to disable XMPP
* 77ce86a jvb: add ability to enable SCTP datachannels
* 76ffaa7 jicofo, jvb: fix OCTO + SCTP behaviour
* 0298a30 misc: working on unstable

## stable-9457-2

Based on stable release 9457-2.

* fed87fb web: update acme.sh version to 3.0.7
* 11285cd prosody: Fixed the wrong position of the JWT_SIGN_TYPE setting in the file (#1796)
* f9ff2a4 misc: working on unstable

## stable-9457-1

Based on stable release 9457-1.

* 09bbfaf misc: working on unstable

## stable-9457

Based on stable release 9457.

* 6f98bbe prosody: add ability to customize TURN TTL value
* 8fe3139 jvb: fix autoscaler sidecar config (#1785)
* 48d803c jigasi: fix autoscaler sidecar config for shutdown url (#1784)
* c951116 jibri: fix autoscaler sidecar config (#1783)
* cae1e40 feat(jigasi): Adds trusted domains option.
* e939230 prosody: add ability to customize JWT signature type
* b0f9eb2 prosody: integrate prosody-plugins-contrib
* 604ba22 prosody: Switches lua gc back to incremental. (#1777)
* 68d4b1a jibri: fix xorg modeline for 1280x720 (#1778)
* 8094a17 web: add ability to change CORS header value
* 03b6ca2 web: remove no longer needed mime type definition
* 70ba4cb base: update to Debian Bookworm
* dc5cbae misc: working on unstable

## stable-9364-1

Based on stable release 9364-1.

* a5ebb99 jibri: flag to support prometheus-style metrics (#1768)
* 80fc10e feat(prosody): Cleans up gc settings.
* 69bba17 feat(prosody): Adjusts lua to use generational GC for brewery.cfg.lua.
* 2798426 feat(prosody): Adjusts lua to use generational GC.
* 28d2b32 feat(prosody): Make sure muc tumbstones is disabled.
* d5df19d jigasi: autoscaler sidecar support (#1738)
* 0953ca0 jigasi: skip SIP configuration when configured as a transcriber
* ca1d670 fix: :bug: wrong quote in default config (#1761)
* 360361e jibri: move xmpp config for easier override (#1748)
* 452f5ba misc: working on unstable

## stable-9364

Based on stable release 9364.

* ca61ea2 jibri: add ability to ignore certificate errors
* 615396e java: use Java 17
* 140db22 jibri: check for chrome bin before pre-warm (#1757)
* 648612f feat(prosody): Always report visitors support when enabled.
* 28f3cf8 jvb: lipcap from apt to allow for pcap dumps (#1747)
* 33350c3 prosody: fix env var default value
* d6dc1e3 base-java: install nodejs 20 from nodesource
* 8f84f2f jibri: use storage.googleapis.com location for chromedriver
* 1bbb4ab fix(prosody): Updates a rate whitelist config that changed in jitsi-meet. (#1741)
* eccf68e misc: working on unstable

## stable-9258

Based on stable release 9258.

* a198d56 compose: add jvb dependency to web container (#1739)
* 5c5742b prosody: var for speakerstats modules
* a1d1f2c web: Add env vars for setting 1080p and 4k bitrates.
* bb69407 Remove "ping" in ViarualHosts (#1728)
* 14efd9c prosody: disable "offline" and "register" modules. (#1727)
* 8e28256 misc: working on unstable

## stable-9220-1

Based on stable release 9220-1.

* 1e97277 jibri: update Chrome to version 121
* 310b80d jvb: add env var to control require-valid-address
* b0092bc jicofo,jigasi,jvb: fix accidentally enabling Sentry
* 102281e base: update tpl
* bf237da etherpad: update etherpad image to etherpad/etherpad:1.9.6 (#1725)
* 7381471 misc: working on unstable

## stable-9220

Based on stable release 9220.

* 4eb5575 prosody: c2s encryption flag from environment
* cc34823 prosody: tune internal muc settings
* f512116 prosody: jibri and jigasi skip muc passwords (#1718)
* 6c1f384 Add flag for require-muc-config-flag. (#1717)
* 6e6dd96 web: nginx config for load-test client if present (#1715)
* 11d7d75 fix: Do not load domain_mapper under breakout component (#1714)
* 4ca5763 prosody: the new version of contrib-prosody-plugins (#1713)
* d31e164 build: simplify detection of the target platform
* 21f04ea base: update tpl
* 9b686c6 prosody: Drops unused module.
* 1365d7f base-java: include jdk package
* 4584779 jicofo: fix enable sctp octo defaults (#1708)
* 2a3f8fd web: fix config.js syntax
* df88c7c Fix/web settings prop syntax (#1706)
* d64ae1c web: remove 'startBitrate' that is no longer supported.
* 1cba0b5 web: Fix the maxbitrates config setting. Also, remove the 'enforcePreferredCodec' setting which is no longer supported.
* 1dbe5cd web: cleanup config
* 3a888fd jicofo: add support for max-ssrcs-per-user config
* 5093efb feat: Add testing flag for Av1
* 7f86006 prosody: remove muc_meeting_id from lobby
* c21a598 prosody: visitors config whitespace fix (#1694)
* 8845606 Move trusted_proxies from visitors to main prosody config file. (#1699)
* e41e4f4 Require tls by default. (#1698)
* 80e4ee6 Remove unnecessary modules and properties (#1697)
* 2915176 prosody: skip recorder account unless in client mode
* 46b98b5 prosody: brewery mode for jvb/jicofo discovery muc
* 52eef2e feat: Add an option to set jibri-sip brewery jid. (#1689)
* ab30f56 prosody: enable muc_meeting_id in lobby component
* 5cc0ef0 jicofo: fix duplicate line
* 2a67885 jicofo: trusted domain list
* 78fb030 fix: Fix typo in var name. (#1690)
* 1ad045e feat: Add an option to enable sctp for relays. (#1688)
* aa898e4 jicofo: fix visitors auth domain (#1687)
* ec8615a fix: Fix default JIBRI_PENDING_TIMEOUT. (#1686)
* 483bbde web: trim deprecated options and defaults
* 11cbfcc web: provide bosh URL as relative only if flagged (#1682)
* 60100d6 jvb: expose public IP in autoscaler sidecar config (#1679)
* b8a9080 task: fix shutdown script
* 2e3c272 jvb: fix terminate script for autoscaler sidecar
* 3519113 jvb: finish script for graceful shutdown (#1676)
* 4fba8b5 task: support logging to file in JVB container (#1675)
* 431cdcc web: allow desktop framerate to be automatically determined by the browser
* 4c009ad jvb: fix version for autoscaler config
* 4531615 prosody: add smacks to guest vhost
* b4428bf prosody: use mod_smacks.lua from prosody since 0.12.4
* ee2f3c9 docker-compose.yml: expose jicofo and jvb env vars for defining max memory.
* 940cd97 env.example: add jicofo and jvb env vars for defining max memory.
* 71b3892 jicofo,jvb,jigasi: fix SENTRY_DSN cannot be defined
* e6a0c04 jibri: add check for /dev/shm size
* dbffba1 Revert "jicofo, jigasi, jvb: fix SENTRY_DSN not being read"
* 073f85d Revert "logging: fix reference to old variable"
* 8415c84 logging: fix reference to old variable
* fa2b4db jicofo, jigasi, jvb: fix SENTRY_DSN not being read
* 42a2153 misc: working on unstable

## stable-9111

Based on stable release 9111.

* 825730d web: nginx ws-colibri proxy regex updates (#1645)
* 54d3aca jicofo: add AV1 options
* 0827186 prosody: stun in external services (#1644)
* d67938c misc: working on unstable

## stable-9078

Based on stable release 9078.

* 3b9afe4 release: build images before comitting the changelog
* 54d422b jvb: autoscaler sidecar support
* 9f0658d sample: escape/encapsulate string
* 5d05ba2 jicofo: support jicofo log file for tailing (#1632)
* 8555fe1 web: param to control config.hosts.authDomain (#1627)
* cd1c9fb prosody: remove muc limit messages from visitors (#1626)
* af50dde prosody: s2s whitelist duplicate param fix (#1625)
* eb91893 prosody: add ping module to auth domain (#1624)
* 261caa3 prosody: guest ping module, var for auth type (#1623)
* 7fb1026 prosody: params for limits (#1622)
* cf894ce prosody: variables for lobby and breakout modules
* a827437 prosody: param to link room metadata to main vhost (#1616)
* 5120595 prosody: var for config in main vhost (#1615)
* bebd748 web: flag to control sctp bridge channel choice (#1613)
* 6bfa830 prosody: visitor mode support (#1611)
* 7bfc5c1 prosody: update version of prosody-plugings package
* 3a77aac jicofo: support visitors in jicofo configuration (#1610)
* f860c5d jvb: don’t send Jetty server version
* 63380fa misc: working on unstable

## stable-8960-1

Based on stable release 8960-1.

* 9bd3258 jibri: fix variable names
* 807b7bf misc: working on unstable

## stable-8960

Based on stable release 8960.

* 5c5575c jibri: make some ffmpeg arguments configurable via env variables
* 09b0df4 misc: working on unstable

## stable-8922-1

Based on stable release 8922-1.

* b3abfc0 ci: split unstable build and test workflows (#1601)
* 603d461 jibri: adapt to new ChromeDriver zip file structure
* a76b1f4 jibri: simplify ChromeDriver download
* fd1c308 jibri: fix new ChromeDriver API endpoints
* 2c0a793 jibri: update Chrome to 116
* 916bccb jibri: autoscaler sidecar tuning parameters
* 6e59319 misc: working on unstable

## stable-8922

Based on stable release 8922.

* 1cbb8f8 prosody: add hybrid_matrix_token as a new authentication method
* b5b8ea2 jigasi: add toggle to enable REST shutdown
* 6bb2455 jibri: add extra fonts
* 497015b jicofo: add JICOFO_AUTH_LIFETIME as environment variable
* f18acb4 jicofo, prosody: allow to set different AUTH_TYPE
* dc1994a jicofo: remove shibboleth authentication options
* 58d4736 misc: working on unstable

## stable-8719

Based on stable release 8719.

* 6f72293 jibri: bump Chrome version
* 94c7060 jibri: params to override statsd host and port
* 0569bce jibri: add autoscaler-sidecar service support (#1562)
* 57a7c22 jicofo: add ability to disable auth
* a41578c prosody: update the comma logic while listing TURNs
* 9776714 prosody: allow multiple TURN (#1559)
* ce59d1e jibri: upgrade chrome to 113 (#1555)
* c2f01f3 web: update livestreaming settings according to new config.js
* e4c8c2a jibri: pre-warm chrome first before starting jibri (#1549)
* 809f63c compose: add ability to configure the Jicofo REST port in the host
* b81bd5c misc: working on unstable

## stable-8615

Based on stable release 8615.

* 57e3bb3 jibri: bump Chrome version
* a0a3410 fix: Allows jicofo entering rooms without requiring a password.
* 1c27da8 prosody: fix ranges redux (#1538)
* 9fc8ffa prosody: fix ranges definition (#1537)
* 39de818 prosody: enable rate limits (#1536)
* 3568542 prosody: add timestamps to the log
* edb5e76 workflow: tag jibri and jigasi images with version (#1522)
* 5d1d80d jvb: fix missing dot in env (#1521)
* 0b1b45d base: use FQIN in base/Dockerfile FROM statement
* 29f0cb2 actions: tag images with detected versions (#1518)
* 7c50cb7 web: support loading pwa-worker.js from subdir (#1517)
* 3af59c6 web: fix bosh for subdomains
* 2fdc643 jicofo: use bool instead of string for codec flags (#1507)
* bdcae29 jicofo: fix syntax error on opus end brace (#1506)
* e5d4213 jicofo: additional conference options (#1504)
* 6034e09 jicofo: support opus red audio codec (#1503)
* ef7ef9c jicofo: disable cert verification for jvb xmpp (#1501)
* a8fe1aa jicofo: fix template error in jvb xmpp server (#1500)
* 7d1bf8e jicofo: flag to use presence for bridge health checks (#1499)
* 1a4f9bd jicofo: fix broken rest template (#1498)
* 76f886f jicofo: add bridge region support and local region (#1497)
* 9c2f742 jicofo: fix rest bind in container (#1496)
* ed095bc misc: support alternate xmpp server for jvb (#1495)
* e1c1f1b jicofo: enable rest interface (#1494)
* ca0b92e web: allow custom colibri websocket port (#1491)
* fe5dea3 misc: working on unstable

## stable-8319

Based on stable release 8319.

* 57e852f build: fix native build on M1 macs
* 1cdf970 prosody: fix "<no value>" issue in prosody config
* 4a0b48c etherpad: suppress errors in pad
* 15335c0 misc: working on unstable

## stable-8252

Based on stable release 8252.

* 076dbf7 jibri: fix downloading new (>= 109) ChromeDriver
* 8f40804 jibri: update Chrome to M109
* 1cf8638 compose: fix whiteboard collab server variable name
* 9e0305b prosody: set JWT_ENABLE_DOMAIN_VERIFICATION to false by default
* ac551c3 base: update tpl
* ec972ee base: update tpl
* 8684b0b misc: working on unstable

## stable-8218

Based on stable release 8218.

* 8d7728b jibri: update Chrome to M108
* 9cfbaf2 misc: drop JICOFO_AUTH_USER
* 68751c2 prosody: add metadata component
* 07f7054 jaas: pass the jitsi installation type at provisioning (#1456)
* e219bcf web: add ability to configure whiteboard
* ac12313 misc: working on unstable

## stable-8138-1

Based on stable release 8138-1.

* 8923b72 web: fix missing quotes on config.js string
* 6b11a89 misc: working on unstable

## stable-8138

Based on stable release 8138.

* 1e49d65 web: simplify build
* dd399fe web,jvb: remove ENABLE_MULTISTREAM
* 723d661 jibri: add single-use-mode config option
* ca14c52 web: add more transcription config env vars
* ccc5746 prosody: add ability to configure TURN server transports
* 17d047a misc: working on unstable

## stable-8044-1

Based on stable release 8044-1.

* fd70f04 env: add note about JaaS account creation
* 046bb79 jaas: register JaaS account automatically
* c44c59e misc: working on unstable

## stable-8044

Based on stable release 8044.

* b212dca web: fix parsing IPv6 reolver addresses
* 53b2654 web: auto-detect nginx resolver
* 9fbb5bd jicofo: fix XMPP config (all moved to jicofo.conf)
* a2333b3 jicofo: remove JICOFO_SHORT_ID (removed upstream)
* d764db9 doc: update README
* c694a9e web: set charset as utf-8
* 8660089 misc: working on unstable

## stable-7882

Based on stable release 7882.

* 4fcba2c jibri: update Chrome to M106
* 957a225 misc: working on unstable

## stable-7830

Based on stable release 7830.

* dd95b3d prosody: fix arm64 build
* acb2f4e misc: update stale.yml
* 02e32e5 jibri: update Chrome to M105
* c53de72 jvb: add JVB_ADVERTISE_IPS, deprecating DOCKER_HOST_ADDRESS
* 723acc2 web: add ability to configure the room password digit size
* a1e82ea jvb: migrate config to secure octo
* 91043c5 prosody: upgrade UVS module to be compatible with Prosody 0.12 and luajwtjitsi 3.0
* dc5b6a1 fix: multi tenant setup (#1401)
* 47804d0 prosody: add JWT_ENABLE_DOMAIN_VERIFICATION to compose file
* 832b178 prosody: make GC options configurable
* bf6a68b web: fix setting prefix for subdomains
* 5fabec9 prosody: add end conference
* 7f7a9b4 misc: working on unstable

## stable-7648-4

Based on stable release 7648-4.

* 6449c60 prosody: fix installation of lua inspect module
* 6664c89 prosody: add missing lua-inspect dependency
* 755bd3f prosody: add jigasi and jibri users as admins
* 8c5fba1 jigasi: add ability to disable SIP
* 4fa0a2f jvb: allow configuration of videobridge.ice.advertise-private-candidates
* 74e5942 misc: working on unstable

## stable-7648-3

Based on stable release 7648-3.

* 7890183 jibri: fix ENABLE_RECORDING issue
* a2b86a0 fix: Fixes undefined variable $ENABLE_JAAS_COMPONENTS. Fixes #1377.
* 9f3c81f misc: working on unstable

## stable-7648-2

Based on stable release 7648-2.

* 24b6adb feat: Adds room info http endpoint for jaas components.
* 475be2a misc: working on unstable

## stable-7648-1

Based on stable release 7648-1.

* d9921a0 prosody: fix syntax error
* bc6ce20 jibri: update Chrome to M104
* 7c7a43a prosody: add ability to configure max occupants
* 85a38d9 jibri: add ability to enable Dropbox recording without enabling "service recording"
* f8b7037 jvb: enable multi-stream by default
* eb0dd6b web: fix receiveMultipleVideoStreams flag
* 674f134 misc: working on unstable

## stable-7648

Based on stable release 7648.

* 12941f5 web: turn on multi-stream by default
* 1d4b265 web: add new flag for multi-stream
* 4264f25 prosody: make enable_domain_verification configurable
* 2a7db7c jigasi: fix Sentry test
* 2d106d8 jigasi: adjust log formatter
* 6c9e305 jigasi: temporarily disable G722
* 9edecf2 misc: working on unstable

## stable-7577-2

Based on stable release 7577-2.

* 55e0eed prosody: remove explicit dependency
* e0bc4e4 prosody: add missing net-url dependency
* e811d7b misc: working on unstable

## stable-7577-1

Based on stable release 7577-1.

* 20eb991 prosody: clean build
* 6fb422c prosody: fix not finding the basexx and cjsson modules
* 1768164 misc: working on unstable

## stable-7577

Based on stable release 7577.

* b670959 prosody: simplify container build
* e05a9c2 fixup: template syntax for newly added variables (#1355)
* 164d28b web: migrate deprecated recordings options, add some more
* f126f7a web: set config.videoQuality.maxBitratesVideo to null if no bitrates are specified
* 0364d94 jibri: remove deprecated PulseAudio module
* 1c93e1b web: add support for brandingDataUrl (#1346)
* 4372717 web: add support for wav files to nginx default
* d804ba4 misc: make ignore rule more generic
* b224131 prosody: use ENABLE_IPV6 environment variable
* be8c41f etherpad,jigasi: fix compose file version
* d7cee00 misc: working on unstable

## stable-7439-2

Based on stable release 7439-2.

* b2f704a misc: working on unstable
* 62655d8 release: stable-7439-1
* 22dc822 prosody: fix XMPP_MUC_CONFIGURATION
* 11de38f fix: properly use default SIP config
* 82a5382 fix: add missing $ to JIGASI_XMPP_USER
* 76ff646 misc: working on unstable

## stable-7439-1

Based on stable release 7439-1.

* 76ff646 misc: working on unstable

## stable-7439

Based on stable release 7439.

* ea37859 prosody: add ability to configure MUC modules through ENV variables
* 5ff69fd jvb: fix jvb.conf parsing error
* 8f38fe6 web,jvb: add option to enable multi-stream
* 701dadf jvb: add ability to disable STUN
* 264a3d8 web: add prejoin config options
* fec78e4 jigasi: build on arm
* c04f658 web,jvb: allow underscore in JVB_WS_SERVER_ID
* 55a4591 prosody: configure unbound resolver
* 576e5a9 web: start with clean config.js
* a7f260e web,etherpad: fix default public URL
* 0cbfbfd web: don't proxy HTTP traffic to WS endpoints
* 7ed5063 jibri,compose: avoid mounting /dev/shm/
* fbb8a2d jibri: switch to PulseAudio
* e7533f8 jibri: simplify Dockerfile
* 7e74308 jigasi: switch to Java 11
* a9d1ed6 misc: working on unstable
* b227b73 build: fix multiarch build

## stable-7287-2

Based on stable release 7287-2.

* ab08247 build: make sure JITSI_RELEASE is passed when invoking make
* 5109874 prosody: update to latest stable
* 343ef56 doc: update README
* 829841e jibri: add support for arm64
* 8d5a9cf jvb: fix not setting WS server ID
* cffab8f jibri: fix log location template
* 94833b5 doc: update README
* d3901ba build: also release a "stable" tag
* 9217b0a misc: working on unstable

## stable-7287-1

Based on stable release 7287-1.

* 22e727c build: adapt release process to multiarch builds
* be422c7 jibri: update Chrome to M102
* 1463df4 compose: add ability to override image versions
* 7c29b57 prosody: fix reservations API
* 8337c0b jicofo,prosody: migrate to new reservations system
* 70c5cbf misc: update dialin numbers url setup (#1298)
* 7790012 misc: define ENABLE_JAAS_COMPONENTS variable (#1297)
* 62ad172 misc: update env.example
* 74ef7de web,prosody: add support for JaaS components
* 902a673 misc: move security options in sample file
* 2a23095 misc: use the "unstable" tag between releases
* 741ec4a build: add native arch building support
* 68d97c8 ci: add GH action docker build caching
* 8b02b8a build,ci: add initial arm64 support
* 3b86df0 compose: add ability to change the JVB colibri REST API exposed port
* 458515c env: add link to handbook
* 261577c web: stop using the default config file
* ed6ef89 web: remove config option to control FLoC
* cb5a753 config: simplify configuration
* b505d58 misc: add stalebot
* 5ff2735 web: add e2eping support using env variables
* 6284167 web: add more audio quality options
* 515bd19 misc: working on latest

## stable-7287

Based on stable release 7287.

* 41d6a9a jibri: bump Chrome to version 101
* 88bb1bc feature: support multiple XMPP servers via list (#1276)
* 95af778 jicofo: add optional XMPP_PORT value (#1275)
* da0a43a misc: working on latest

## stable-7210-2

Based on stable release 7210-2.

* 2634e96 misc: working on latest

## stable-7210-1

Based on stable release 7210-1.

* a8e6a34 prosody: completely disable external components
* 8587d29 prosody: add mod_auth_cyrus from community libraries
* 3a070e6 misc: working on latest

## stable-7210

Based on stable release 7210.

* 1afa278 prosody: add temporary workaround for JWT auth
* 6fe240a prosody: update to 0.12
* 097558b ci: dry run Docker builds on PRs
* eca5d16 web: fix matching etherpad location
* 3afc1e3 prosody: update package version
* b0617c0 web: fix Etherpad when using multi-domain
* 0ce0f09 prosody: update version
* 201a1b4 prosody: pin to version 0.11 for now
* 29b4c23 prosody: use a more recent version of luarocks
* c5b049a jvb: forward port 8080 to docker host
* 6af7cd8 doc: update CHANGELOG
* dd7b70b misc: working on latest

## stable-7001

**IMPORTANT:** Starting with this release TCP has support has been removed from the JVB.

Based on stable release 7001.

* 6e0dd04 base: replace frep with tpl
* 1b51c77 feat: Enables polls for breakout rooms.
* 0b019ee feat: Enables tenants/subdomains by default.
* d50df67 fix: Fixes missing variable for prosody plugins.
* 88997f5 prosody: authentication by matrix user authentication service
* 7a93978 jvb: remove TCP support
* c37706c misc: fix label order in  dockerfiles
* 0de062b misc: add missing quotes to labels in dockerfiles
* 76424fd chore: add opencontainers labels to Dockerfiles
* 3b8ed7e misc: working on latest

## stable-6865

Based on stable release 6865.

* 8004ffe Use the new log formatters, clean up stale logging config.
* a862e84 web: cache versioned static files
* 48d499a web: configure remote participant video menu
* 78791ad env.example : ETHERPAD_PUBLIC_URL : incl. /p/ path
* a504b59 misc: working on latest

## stable-6826

Based on stable release 6826.

* 238a636 jibri: correct chromedriver mismatch
* 555a40e doc: update CHANGELOG
* 825b4cb misc: working on latest

## stable-6726-2

**IMPORTANT:** This version updates Prosody to version 0.11.12 to fix CVE-2022-0217: https://prosody.im/security/advisory_20220113/

Based on stable release 6726-2.

* ae3e7e7 jvb: make MUC_NICKNAME configurable
* 0be9c8f web: allow configuring buttons in toolbar and pre-join screen
* d9d12f0 jvb: fix resolving XMPP server aliases
* 81dc384 jigasi: allow jigasi guest participants
* a8a596b jicofo: configure trusted-domains for Jibri if ENABLE_RECORDING is set
* d250ad7 misc: working on latest

## stable-6726-1

Based on stable release 6726-1.

* 9ac7b59 jibri: update Chrome to version 96
* fb2326e prosody: add missing package libldap-common
* 0600ece sample: add  ENABLE_BREAKOUT_ROOMS to env.example
* 6cf0176 misc: working on latest

## stable-6726

Based on stable release 6726.

* e9275d5 jvb: remove deprecated option
* f40a8d5 jicofo: Handle special characters in password
* 6f56e5b web,prosody: add breakout rooms support
* 3208296 base: update base images to Debian Bullseye
* b02a689 env:  fix unexpected character bug with recent docker desktop
* b5dbfa0 misc: working on latest

## stable-6433

Based on stable release 6433.

* 487bcca jvb: try to use the correct IP as the default server ID
* 9e982fe examples: move to jitsi-contrib
* 192a623 jvb: add ability to configure the shutdown API
* 5dcf7b4 compose: changed REACTIONS env variable name in docker-compose file
* d94f4b6 jvb: remove unneeded alias
* 7cd71a2 jibri: default to recording in 720p
* bee4b6a jibri: use new configuration file
* 76a16a8 jvb: use modern config for ice4j
* 18ac85b jibri: allow graceful shutdown of the container
* 3c19ed6 jibri: uppdate Chrome to version 94
* b858b37 base-java: update to Java 11
* 2061b86 misc: enable features by default
* 343062b misc: fix/ improve shebang compatibility
* ff8c1c2 web: regenerate interface_config.js on every boot
* bda1502 prosody: simplify code
* cfd8d3c web: add config options for polls and reactions
* 537fcd5 misc: add support for sentry logging
* be1da0e misc: cleanup Dockerfiles
* 09cf0a8 web: add env variables for configuring vp9
* 3df32d9 web: persist crontabs for letsencrypt
* f748484 jicofo: add enable-auto-login config option
* 96419ba web: remove no longer needed code
* 1835d65 web: recreate interface_config.js on container restart
* b555d41 jicofo: fix boolean values in configuration file
* 6be198c misc: remove quotation marks from TURN configuration (#1111)
* 407a98d misc: working on latest

## stable-6173

Based on stable release 6173.

* c95f0d6 prosody: add support for A/V Moderation
* 856e414 prosody: add ability to configure external TURN server
* bcae3b1 prosody: add domain mapper options to default configuration
* cf90461 web: fix acme.sh pre and post hooks
* 65563d9 misc: working on latest

## stable-5963

Based on stable release 5963.

* 6f6fe77 prosody: enable limits
* db3d790 prosody: fix: restrict room creation to jicofo (#1059)
* 281db36 misc: working on latest

## stable-5870

Based on stable release 5870.

* d9b84cf jibri: set base URL for joining meetings
* a77a43e jibri: update default Chrome version to 90
* a90e4ce doc: drop confusing port number from PUBLIC_URL
* 8620caa doc: clarify env variable
* 6f52f71 web: add FLoC environment variable
* 014aa59 web: add default language variable
* ce25bf6 doc: update CHANGELOG
* 6bf1336 misc: working on latest

## stable-5765-1

Based on stable release 5765.

* 7a47202 jicofo: make sure client-proxy is properly configured
* 5c32833 web: add start environment variables START_WITH_AUDIO_MUTED; START_SILENT; START_WITH_VIDEO_MUTED
* 3d93f2b misc: working on latest

## stable-5765

Based on stable release 5765.

* 9bc262a prosody: fix building unstable images
* 0cbe0d9 web: add a env variable to enable/disable deep linking
* b22421b misc: publish nightly unstable images
* 78699fe web: allow to configure shard name using env variable
* a6853ef jvb: add octo configuration options
* d6fac8e jicofo: disable octo by default
* 1fa5048 doc: add link to Kubernetes setup
* e1cebcc web,jvb: add ability to disable web sockets for colibri
* 6c4dce1 jicofo: fix ENABLE_SCTP type
* 953a4d2 jicofo: use a client proxy connection
* d27336b web: always try to renew cert on container boot
* 73acbad web: remove deprecated config option
* cb4d941 web: removed duplicate host headers
* ec570ba k8s: fix PodSecurityPolicy
* c4fc3d3 web: make a custom interface config possible
* b45b505 web: always install acme.sh when container starts
* 94ca16d etherpad: remove quotes from all env vars
* c89ccc9 jicofo: reintroduce shibboleth auth
* a6486b4 examples: update traefik v2 example
* f4ec023 misc: working on latest

## stable-5390-3

Based on stable release 5390-3.

* a698da5 misc: add jicofo reservation env variables to compose
* 86c3022 web: brandingDataUrl -> dynamicBrandingUrl
* 88e950d jicofo: fix healthcheck
* 493cbdd misc: fix typo
* e12d7f2 web : Add DESKTOP_SHARING_FRAMERATE_MIN and MAX env vars
* fa98a31 examples: fix k8s example
* 88d1034 doc: add port to PUBLIC_URL
* c876b40 doc: update CHANGELOG
* 5cf14b0 misc: working on latest

## stable-5390-2

Based on stable release 5390-2.

* 3e04fb4 prosody: fix lobby when authentication is enabled
* 24781e3 misc: working on latest

## stable-5390-1

Based on stable release 5390-1.

* 3ac5397 misc: working on latest

## stable-5390

Based on stable release 5390.

* 0f541c8 jicofo: migrate to new config
* 12823cb prosody: fix jibri recording websocket error
* 7594ea2 jigasi: add ability to control SIP default room for incoming calls
* b0e653a jigasi: fix when using authentication
* 4564170 misc: working on latest

## stable-5142-4

Based on stable release 5142-4.

* 6f7b2b4 prosody: add internal domain name to default cross-domains list
* ada7b95 jvb: fix check for JVB_TCP_HARVESTER_DISABLED
* a7fb101 jibri: don't provide a non-existing finalizer path
* d013053 jibri: add missing dependency for `kill` command
* 0b25141 web: Add ENABLE_HSTS flag to disable strict-transport-security header
* f856037 web: add more config options
* eedac14 web: add ability to disable IPv6
* af6f3ac doc: update CHANGELOG
* e3bb5c1 misc: working on latest

## stable-5142-3

**Important:** This release should fix some update problems users found in -1 and -2 versions. The main problem observed is the introduction of XMPP WebSockets, which requires extra configuration for the /xmpp-wesocket route if a reverse proxy is used in front of this setup. Pure docker-compose installations don't need any changes.

Based on stable release 5142-3.

* c2c6460 prosody: fix cross-domain WS default value
* 8261f72 jicofo,jigase: add ability to extend the config file
* 6a4887d web: use env variables to set worker processes and connections
* 5679578 prosody: add env var to config cross domain settings
* effb30b prosody: always rebuild configs on start
* 905d431 jicofo,jigasi: always rebuild configs on start
* c52b64a misc: working on latest

## stable-5142-2

Based on stable release 5142-2.

* 700c04a web: properly handle acme.sh return codes
* 4cb181c web: install acme certs to persistent storage
* 1d2c68a web: fix running acme.sh on the right home directory
* 5c44a84 misc: stop using apt-key, it's deprecated
* 5f06c3a doc: update CHANGELOG
* 0f780b4 misc: working on latest

## stable-5142-1

**Important:** This release includes 2 major changes: migrating the base image to Debian Buster and replacing certbot with acme.sh for getting Letś Encrypt certificates. Please report any problems you find!

Based on stable release 5142-1.

* b0cb4a1 web: update TLS config to Mozilla security guidelines
* 0601212 web: replace certbot with acme.sh
* 43f678d build: refactor Makefile
* b00f92a web: use Python 3 only for certbot
* 880b9b0 core: update base image to Debian Buster
* ba01190 web: prevent s6 from restarting cron if it shouldn't be run
* 42a4346 etherpad: use official image and making skin full width
* c36c4d0 web: always rebuild nginx configs on start
* aea4411 Adds private server.
* 6b69576 web: add ability to configure tokenAuthUrl
* ff6d9bc Fix websocket
* e5746ae misc: add ENABLE_PREJOIN_PAGE to .env
* 465816b web,prosody: turn on XMPP WebSocket by default
* d747bfb web,prosody: add XMPP WebSocket / Stream Management support
* 130eb55 jvb: migrate to new config file
* 5290499 doc: updated link for running behind NAT
* 7cb470c misc: support/encourage usage of ShellCheck
* 04a210f misc: working on latest

## stable-5142

Based on stable release 5142.

* 7ab45bb web: add ability to configure prejoin page
* 0c95794 jvb: regenerate config on every boot
* 3ef2221 jvb: add ability to set the WS domain with an env var
* 79d2601 jvb: add ability to specify set the WS_SERVER_ID with an env var
* b277926 jvb: make colibri websocket endpoints dynamic for multiple jvbs
* 991f695 web: remove no longer needed settings
* 8b7cbc3 revert "jicofo: no auth URL in JWT auth mode"
* 33b386b jvb: add missing variable to docker-compose
* 087f024 web: configure brandingDataUrl with env variables
* a404653 web: configure startAudioOnly using environment variable
* e195cbf jvb: make jvb apis available from outside the container
* 409cade web: configure Matomo using environment variables
* b731c60 doc: update CHANGELOG
* 0fbf3b7 misc: working on latest

## stable-5076

**Important:** Starting with this release config.js is autogenerated with every container boot.
In addition, bridge channels now using WebSocket. Some setups may break on upgrade.

Based on stable release 5076.

* 5ceaf5f web: add IPv6 support
* aff3775 xmpp: allow recorders to bypass lobby
* ad5625b jvb: switch to WebSocket based bridge channels
* 8110336 web: add ability to configure the nginx resolver
* 2f47518 jicofo: no auth URL in JWT auth mode
* c149463 web: build config.js on each boot
* c792bbc base: update frep
* bec928c prosody: configure lobby on the guest domain is necessary
* bcbd977 jicofo: pass XMPP_MUC_DOMAIN through docker-compose.yml
* 8f9caa4 jicofo: set XMPP_MUC_COMPONENT_PREFIX
* 2a0120d web: set security headers also for non HTTPS
* e6586f2 jvb: set LOCAL_ADDRESS to the correct local IP (#630)
* 97f5e75 base: optimize size
* b78c89e misc: minor Dockerfile Improvements
* a754519 misc: working on latest

## stable-4857

Based on stable release 4857.

* a81ad73 prosody: add support for lobby
* baed605 web: fix removing closed captions button if transcription is enabled
* edecacd etherpad: add ability to use a external server
* a7563d4 jvb: use JVB_TCP_PORT for exposing the port
* b235ea1 prosody: disable s2s module
* 1d428a8 prosody: use a 2-stage build
* 613c26c misc: working on latest
* 4d72ee3 release: stable-4627-1
* 22b7063 examples: update Traefik v1 example
* 1381b08 prosody: fix installing dependdencies
* 2900c11 misc: add extra line to tag message
* c57a84b misc: working on latest

## stable-4627-1

Based on stable release 4627-1.

* 1381b08 prosody: fix installing dependdencies
* 2900c11 misc: add extra line to tag message
* c57a84b misc: working on latest

## stable-4627

Based on stable release 4627.

* fdf5030 prosody: update configuration
* afafe23 prosody: shrink container size
* 8e7ea34 base: fix setting timezone
* 58441ae doc: update README
* 3c12526 etherpad: update to version 1.8.4
* 0038e71 jibri: install extra dependency
* 0615ed6 doc: add missing volumes to quick start
* 2781865 doc: clarify usage of gen-passwords.sh
* a8d0b6c build: add PHONY target for "release"
* d4a35a6 misc: working on latest

## stable-4548-1

Based on stable release 4548-1.

* abf2f73 jicofo: fix setting incorrect auth URL scheme for JWT
* 3472ab0 jicofo: add ability to configure health checks
* ec3622b jibri: install jitsi-upload-integrations by default
* 0e7bc91 etherpad: pin image version
* 4fa50b9 jwt: do not load token_verification module with disabled authentication
* b0d76a2 jibri: add jq dep for upload integrations
* 53b58fd jvb: add jq, curl deps for graceful_shutdown.sh
* 2d063ad doc: update installation instructions
* e73df5f misc: working on latest

## stable-4548

Based on stable release 4548.

* a79fc0c misc: add release script
* 0f0adc8 compose: add image tag to compose files
* 0177765 misc: fix config volumes to work with SELinux
* eae3f5c jibri: chrome/driver 78 as a stopgap
* 78df6a4 doc: delete unnecessary dot
* 4426ed8 jibri: fix case when /dev/snd is not bound (https://github.com/jitsi/docker-jitsi-meet/issues/240#issuecomment-610422404)
* 125775a web: fix WASM MIME type
* e70975e web: enable GZIP compression for more file types
* 774aba5 misc: set ddefault timezone to UTC
* 3c3fc19 prosody: enable speaker stats and conferene duration modules
* f911df2 jvb: set JVB_TCP_MAPPED_PORT default value
* 1205170 jvb: allow `TCP_HARVESTER_MAPPED_PORT` to be configured
* f7796a1 prosody: add volume  /prosody-plugins-custom to docker-compose
* d44230e prosody: use hashed xmpp auth

## stable-4416

Based on stable release 4416.

* b039b29 web: use certbot-auto
* b95c95d web: improve nginx configuration
* 2dd6b99 k8s: specify namespace for secret
* 7aa2d81 ldap: avoid unnecessary copy
* e1b47db exampless: update Traefik v2 example with UDP
* 0940605 doc: fix typos and minor grammar issues in README
* 1c4b11c doc: correct minor mistake
* c06867b doc: added steps for updating kernel manually in AWS installation
* dc46215 web: remove DHE suites support
* 367621f prosody: remove no longer needed patch
* 34e6601 doc: clarify acronym
* 2c95ab7 web: revert using PUBLIC_URL for BOSH URL
* 7fd7e2b Add docker-compose.override.yml to .gitignore (#438)
* 67a941b misc: update gen-passwords.sh shell code
* 4e2cec6 misc: add configurable service restart policy
* 729f9d2 doc: fix typo in env.example

## stable-4384(-1)

**Important security note:** Previous releases included default passwords for
system accounts, and users who didn't change them are at risk of getting
the authentication system circumvented by an attacker using a system account
with the default password. Please update and use the provided script
(instructions on the README) to generate a strong password for each system
account.

Thanks joernchen for the security report.

<hr/>

Based on stable release 4384.

* 768b6c4 security: fail to start if using the old default password
* 1ffd472 security: add script to generate strong passwords
* a015710 security: don't provide default passwords
* aaec22d jigasi: fix typo in config
* ebfa142 docs: fix grammar and typos
* bab77e0 doc: update env.example
* 7652807 examples: traefik v2
* 10983b4 prosody: prevent item-not-found error in certain cases
* 3524a52 base: fail to start the container if the init script fails
* 7c0c795 jicofo: only configure Jigasi brewery if Jigasi is configured
* 40c2920 build: add prepare command
* 93ba770 prosody: fix installing prosody from the right repository
* 3c07d76 doc: improve wording of README
* ed410d9 doc: fix typo
* fabfb2a doc: fix typo
* 5e6face web: use PUBLIC_URL for etherpaad base and BOSH URLs
* 264df04 jvb: switch to using Jitsi's STUN server by default
* 655cf6b web,prosody,jvb: prepare for new stable release
* ebb4536 doc: update CHANGELOG
* 06c3a83 doc: fix references to running behind NAT in the README

## stable-4101-2

Based on stable release 4101.

* b15bb28 prosody: update to latest stable version
* 75cb31b doc: add build instructions to README
* 25dbde9 doc: fix typo
* badc2d4 doc: add examples/README
* f6f6ca6 Merge branch 'dev'
* 52a1449 doc: clarify DOCKER_HOST_ADDRESS
* f26c9e6 prosody: fix ldap config template
* cd4a071 web: check for certbot's success and exit in case of a failure
* dea8d6c doc: fix typo
* 573c6fa doc: update diagrams
* 29125fd examples: add minimal example to run jitsi behind traefik

## stable-4101-1

Based on stable release 4101.

* b0def9a prosody: use epoll backend
* 8fa9f94 web: update nginx config from upstream
* 2f17380 doc: clarify account registration command
* edfd8f2 ldap: actually fix anonymous binds (Fixes #234)
* f4ac7cc misc: remove bogus quotation marks
* 0a68be1 jibri: start once jicofo has started
* 76acc65 doc: add tip re. ports to open on firewall to README
* e92a00c ldap: fix anonymous binds
* df40447 ldap: add option for ldap starttls support
* 1ebc535 doc: make localhost link in README clickable
* 33abdf3 doc: add mkdir -p ~/.jitsi-meet-cfg/... to README
* 2c93dce doc: fix typo in README
* d7bb2e6 doc: clarify HTTP vs HTTPS in README
* a1df1e0 Revert "prosody: fix restart loop on rolling deployment"
* 986071b jigasi: add missing transcription volumes to dockerfile
* 01eca74 jigasi: generate google cloud credentials from env vars
* cc2c042 prosody: fix restart loop on rolling deployment
* 5423a8a examples: adding simple kubernetes example
* 6eebabd jicofo: set owner jicofo rights for /config directory
* 69ba9ff jigasi: Updates jigasi client default options.
* 2b9a13b jicofo: add support of reservation REST API
* 8bfe7fb jicofo: add support of reservation REST API
* 9b17c05 web: fix letsencrypt renewal
* 6234a18 web: fix letsencrypt renewal
````

## File: docker-compose.yml
````yaml
version: '3.5'

services:
    # Frontend
    web:
        image: jitsi/web:${JITSI_IMAGE_VERSION:-unstable}
        restart: ${RESTART_POLICY:-unless-stopped}
        ports:
            - '${HTTP_PORT}:80'
            - '${HTTPS_PORT}:443'
        volumes:
            - ${CONFIG}/web:/config:Z
            - ${CONFIG}/web/crontabs:/var/spool/cron/crontabs:Z
            - ${CONFIG}/transcripts:/usr/share/jitsi-meet/transcripts:Z
            - ${CONFIG}/web/load-test:/usr/share/jitsi-meet/load-test:Z
        labels:
            service: "jitsi-web"
        environment:
            - AMPLITUDE_ID
            - ANALYTICS_SCRIPT_URLS
            - ANALYTICS_WHITELISTED_EVENTS
            - AUDIO_QUALITY_OPUS_BITRATE
            - AUTO_CAPTION_ON_RECORD
            - BRANDING_DATA_URL
            - BOSH_RELATIVE
            - CHROME_EXTENSION_BANNER_JSON
            - CODEC_ORDER_JVB
            - CODEC_ORDER_JVB_MOBILE
            - CODEC_ORDER_P2P
            - CODEC_ORDER_P2P_MOBILE
            - COLIBRI_WEBSOCKET_PORT
            - COLIBRI_WEBSOCKET_JVB_LOOKUP_NAME
            - COLIBRI_WEBSOCKET_REGEX
            - CONFCODE_URL
            - CORS_HEADER_ACCESS_CONTROL_ALLOW_ORIGIN
            - DEFAULT_LANGUAGE
            - DEPLOYMENTINFO_ENVIRONMENT
            - DEPLOYMENTINFO_ENVIRONMENT_TYPE
            - DEPLOYMENTINFO_REGION
            - DEPLOYMENTINFO_SHARD
            - DESKTOP_SHARING_FRAMERATE_AUTO
            - DESKTOP_SHARING_FRAMERATE_MIN
            - DESKTOP_SHARING_FRAMERATE_MAX
            - DIALIN_NUMBERS_URL
            - DIALOUT_AUTH_URL
            - DIALOUT_CODES_URL
            - DISABLE_AUDIO_LEVELS
            - DISABLE_COLIBRI_WEBSOCKET_JVB_LOOKUP
            - DISABLE_DEEP_LINKING
            - DISABLE_GRANT_MODERATOR
            - DISABLE_HTTPS
            - DISABLE_KICKOUT
            - DISABLE_LOCAL_RECORDING
            - DISABLE_POLLS
            - DISABLE_PRIVATE_CHAT
            - DISABLE_PROFILE
            - DISABLE_REACTIONS
            - DISABLE_REMOTE_VIDEO_MENU
            - DISABLE_START_FOR_ALL
            - DROPBOX_APPKEY
            - DROPBOX_REDIRECT_URI
            - DYNAMIC_BRANDING_URL
            - ENABLE_ADAPTIVE_MODE
            - ENABLE_AUDIO_PROCESSING
            - ENABLE_AUTOMATIC_GAIN_CONTROL
            - ENABLE_AUTH
            - ENABLE_AUTH_DOMAIN
            - ENABLE_BREAKOUT_ROOMS
            - ENABLE_CALENDAR
            - ENABLE_COLIBRI_WEBSOCKET
            - ENABLE_COLIBRI_WEBSOCKET_UNSAFE_REGEX
            - ENABLE_E2EPING
            - ENABLE_FILE_RECORDING_SHARING
            - ENABLE_GUESTS
            - ENABLE_HSTS
            - ENABLE_HTTP_REDIRECT
            - ENABLE_IPV6
            - ENABLE_LETSENCRYPT
            - ENABLE_NO_AUDIO_DETECTION
            - ENABLE_NOISY_MIC_DETECTION
            - ENABLE_OCTO
            - ENABLE_OPUS_RED
            - ENABLE_PREJOIN_PAGE
            - ENABLE_P2P
            - ENABLE_WELCOME_PAGE
            - ENABLE_CLOSE_PAGE
            - ENABLE_LIVESTREAMING
            - ENABLE_LIVESTREAMING_DATA_PRIVACY_LINK
            - ENABLE_LIVESTREAMING_HELP_LINK
            - ENABLE_LIVESTREAMING_TERMS_LINK
            - ENABLE_LIVESTREAMING_VALIDATOR_REGEXP_STRING
            - ENABLE_LOAD_TEST_CLIENT
            - ENABLE_LOCAL_RECORDING_NOTIFY_ALL_PARTICIPANT
            - ENABLE_LOCAL_RECORDING_SELF_START
            - ENABLE_RECORDING
            - ENABLE_REMB
            - ENABLE_REQUIRE_DISPLAY_NAME
            - ENABLE_SERVICE_RECORDING
            - ENABLE_SIMULCAST
            - ENABLE_STATS_ID
            - ENABLE_STEREO
            - ENABLE_SUBDOMAINS
            - ENABLE_TALK_WHILE_MUTED
            - ENABLE_TCC
            - ENABLE_TRANSCRIPTIONS
            - ENABLE_VLA
            - ENABLE_XMPP_WEBSOCKET
            - ENABLE_JAAS_COMPONENTS
            - ETHERPAD_PUBLIC_URL
            - ETHERPAD_URL_BASE
            - E2EPING_NUM_REQUESTS
            - E2EPING_MAX_CONFERENCE_SIZE
            - E2EPING_MAX_MESSAGE_PER_SECOND
            - GOOGLE_ANALYTICS_ID
            - GOOGLE_API_APP_CLIENT_ID
            - HIDE_PREMEETING_BUTTONS
            - HIDE_PREJOIN_DISPLAY_NAME
            - HIDE_PREJOIN_EXTRA_BUTTONS
            - INVITE_SERVICE_URL
            - JVB_PREFER_SCTP
            - LETSENCRYPT_DOMAIN
            - LETSENCRYPT_EMAIL
            - LETSENCRYPT_USE_STAGING
            - LETSENCRYPT_ACME_SERVER
            - MATOMO_ENDPOINT
            - MATOMO_SITE_ID
            - MICROSOFT_API_APP_CLIENT_ID
            - NGINX_KEEPALIVE_TIMEOUT
            - NGINX_RESOLVER
            - NGINX_WORKER_PROCESSES
            - NGINX_WORKER_CONNECTIONS
            - PEOPLE_SEARCH_URL
            - PREFERRED_LANGUAGE
            - PUBLIC_URL
            - P2P_PREFERRED_CODEC
            - P2P_STUN_SERVERS
            - RESOLUTION
            - RESOLUTION_MIN
            - RESOLUTION_WIDTH
            - RESOLUTION_WIDTH_MIN
            - START_AUDIO_MUTED
            - START_AUDIO_ONLY
            - START_SILENT
            - START_WITH_AUDIO_MUTED
            - START_VIDEO_MUTED
            - START_WITH_VIDEO_MUTED
            - TOKEN_AUTH_URL
            - TOOLBAR_BUTTONS
            - TRANSLATION_LANGUAGES
            - TRANSLATION_LANGUAGES_HEAD
            - TZ
            - USE_APP_LANGUAGE
            - VIDEOQUALITY_BITRATE_H264_LOW
            - VIDEOQUALITY_BITRATE_H264_STANDARD
            - VIDEOQUALITY_BITRATE_H264_HIGH
            - VIDEOQUALITY_BITRATE_H264_FULL
            - VIDEOQUALITY_BITRATE_H264_ULTRA
            - VIDEOQUALITY_BITRATE_H264_SS_HIGH
            - VIDEOQUALITY_BITRATE_VP8_LOW
            - VIDEOQUALITY_BITRATE_VP8_STANDARD
            - VIDEOQUALITY_BITRATE_VP8_HIGH
            - VIDEOQUALITY_BITRATE_VP8_FULL
            - VIDEOQUALITY_BITRATE_VP8_ULTRA
            - VIDEOQUALITY_BITRATE_VP8_SS_HIGH
            - VIDEOQUALITY_BITRATE_VP9_LOW
            - VIDEOQUALITY_BITRATE_VP9_STANDARD
            - VIDEOQUALITY_BITRATE_VP9_HIGH
            - VIDEOQUALITY_BITRATE_VP9_FULL
            - VIDEOQUALITY_BITRATE_VP9_ULTRA
            - VIDEOQUALITY_BITRATE_VP9_SS_HIGH
            - VIDEOQUALITY_BITRATE_AV1_LOW
            - VIDEOQUALITY_BITRATE_AV1_STANDARD
            - VIDEOQUALITY_BITRATE_AV1_HIGH
            - VIDEOQUALITY_BITRATE_AV1_FULL
            - VIDEOQUALITY_BITRATE_AV1_ULTRA
            - VIDEOQUALITY_BITRATE_AV1_SS_HIGH
            - VIDEOQUALITY_PREFERRED_CODEC
            - XMPP_AUTH_DOMAIN
            - XMPP_BOSH_URL_BASE
            - XMPP_DOMAIN
            - XMPP_GUEST_DOMAIN
            - XMPP_MUC_DOMAIN
            - XMPP_HIDDEN_DOMAIN
            - XMPP_PORT
            - XMPP_RECORDER_DOMAIN
            - WHITEBOARD_COLLAB_SERVER_PUBLIC_URL
            - WHITEBOARD_COLLAB_SERVER_URL_BASE
        networks:
            meet.jitsi:
        depends_on:
            - jvb

    # XMPP server
    prosody:
        image: jitsi/prosody:${JITSI_IMAGE_VERSION:-unstable}
        restart: ${RESTART_POLICY:-unless-stopped}
        expose:
            - '${XMPP_PORT:-5222}'
            - '${PROSODY_S2S_PORT:-5269}'
            - '5347'
            - '${PROSODY_HTTP_PORT:-5280}'
        labels:
            service: "jitsi-prosody"
        volumes:
            - ${CONFIG}/prosody/config:/config:Z
            - ${CONFIG}/prosody/prosody-plugins-custom:/prosody-plugins-custom:Z
        environment:
            - AUTH_TYPE
            - DISABLE_POLLS
            - ENABLE_AUTH
            - ENABLE_AV_MODERATION
            - ENABLE_BREAKOUT_ROOMS
            - ENABLE_END_CONFERENCE
            - ENABLE_GUESTS
            - ENABLE_IPV6
            - ENABLE_LOBBY
            - ENABLE_RECORDING
            - ENABLE_S2S
            - ENABLE_TRANSCRIPTIONS
            - ENABLE_VISITORS
            - ENABLE_XMPP_WEBSOCKET
            - ENABLE_JAAS_COMPONENTS
            - GC_TYPE
            - GC_INC_TH
            - GC_INC_SPEED
            - GC_INC_STEP_SIZE
            - GC_GEN_MIN_TH
            - GC_GEN_MAX_TH
            - GLOBAL_CONFIG
            - GLOBAL_MODULES
            - JIBRI_RECORDER_USER
            - JIBRI_RECORDER_PASSWORD
            - JIBRI_SIP_BREWERY_MUC
            - JIBRI_XMPP_USER
            - JIBRI_XMPP_PASSWORD
            - JICOFO_AUTH_PASSWORD
            - JICOFO_COMPONENT_SECRET
            - JIGASI_TRANSCRIBER_PASSWORD
            - JIGASI_TRANSCRIBER_USER
            - JIGASI_XMPP_USER
            - JIGASI_XMPP_PASSWORD
            - JVB_AUTH_USER
            - JVB_AUTH_PASSWORD
            - JWT_APP_ID
            - JWT_APP_SECRET
            - JWT_ACCEPTED_ISSUERS
            - JWT_ACCEPTED_AUDIENCES
            - JWT_ASAP_KEYSERVER
            - JWT_ALLOW_EMPTY
            - JWT_AUTH_TYPE
            - JWT_ENABLE_DOMAIN_VERIFICATION
            - JWT_SIGN_TYPE
            - JWT_TOKEN_AUTH_MODULE
            - MATRIX_UVS_URL
            - MATRIX_UVS_ISSUER
            - MATRIX_UVS_AUTH_TOKEN
            - MATRIX_UVS_SYNC_POWER_LEVELS
            - MATRIX_LOBBY_BYPASS
            - LOG_LEVEL
            - LDAP_AUTH_METHOD
            - LDAP_BASE
            - LDAP_BINDDN
            - LDAP_BINDPW
            - LDAP_FILTER
            - LDAP_VERSION
            - LDAP_TLS_CIPHERS
            - LDAP_TLS_CHECK_PEER
            - LDAP_TLS_CACERT_FILE
            - LDAP_TLS_CACERT_DIR
            - LDAP_START_TLS
            - LDAP_URL
            - LDAP_USE_TLS
            - MAX_PARTICIPANTS
            - PROSODY_ADMINS
            - PROSODY_AUTH_TYPE
            - PROSODY_C2S_LIMIT
            - PROSODY_C2S_REQUIRE_ENCRYPTION
            - PROSODY_RESERVATION_ENABLED
            - PROSODY_RESERVATION_REST_BASE_URL
            - PROSODY_DISABLE_C2S_LIMIT
            - PROSODY_DISABLE_S2S_LIMIT
            - PROSODY_ENABLE_RATE_LIMITS
            - PROSODY_ENABLE_RECORDING_METADATA
            - PROSODY_ENABLE_STANZA_COUNTS
            - PROSODY_ENABLE_S2S
            - PROSODY_ENABLE_METRICS
            - PROSODY_GUEST_AUTH_TYPE
            - PROSODY_HTTP_PORT
            - PROSODY_LOG_CONFIG
            - PROSODY_METRICS_ALLOWED_CIDR
            - PROSODY_MODE
            - PROSODY_RATE_LIMIT_LOGIN_RATE
            - PROSODY_RATE_LIMIT_SESSION_RATE
            - PROSODY_RATE_LIMIT_TIMEOUT
            - PROSODY_RATE_LIMIT_ALLOW_RANGES
            - PROSODY_RATE_LIMIT_CACHE_SIZE
            - PROSODY_S2S_LIMIT
            - PROSODY_S2S_PORT
            - PROSODY_TRUSTED_PROXIES
            - PROSODY_VISITOR_INDEX
            - PROSODY_VISITORS_MUC_PREFIX
            - PROSODY_VISITORS_S2S_VHOSTS
            - PUBLIC_URL
            - STUN_HOST
            - STUN_PORT
            - TURN_CREDENTIALS
            - TURN_USERNAME
            - TURN_PASSWORD
            - TURN_HOST
            - TURNS_HOST
            - TURN_PORT
            - TURNS_PORT
            - TURN_TRANSPORT
            - TURN_TTL
            - TZ
            - VISITORS_MAX_VISITORS_PER_NODE
            - VISITORS_XMPP_DOMAIN
            - VISITORS_XMPP_SERVER
            - VISITORS_XMPP_PORT
            - XMPP_BREAKOUT_MUC_MODULES
            - XMPP_CONFIGURATION
            - XMPP_DOMAIN
            - XMPP_AUTH_DOMAIN
            - XMPP_GUEST_DOMAIN
            - XMPP_MUC_DOMAIN
            - XMPP_INTERNAL_MUC_DOMAIN
            - XMPP_LOBBY_MUC_MODULES
            - XMPP_MODULES
            - XMPP_MUC_MODULES
            - XMPP_MUC_CONFIGURATION
            - XMPP_INTERNAL_MUC_MODULES
            - XMPP_HIDDEN_DOMAIN
            - XMPP_PORT
            - XMPP_RECORDER_DOMAIN
            - XMPP_SERVER_S2S_PORT
            - XMPP_SPEAKERSTATS_MODULES
        networks:
            meet.jitsi:
                aliases:
                    - ${XMPP_SERVER:-xmpp.meet.jitsi}

    # Focus component
    jicofo:
        image: jitsi/jicofo:${JITSI_IMAGE_VERSION:-unstable}
        restart: ${RESTART_POLICY:-unless-stopped}
        ports:
            - '127.0.0.1:${JICOFO_REST_PORT:-8888}:8888'
        volumes:
            - ${CONFIG}/jicofo:/config:Z
        labels:
            service: "jitsi-jicofo"
        environment:
            - AUTH_TYPE
            - BRIDGE_AVG_PARTICIPANT_STRESS
            - BRIDGE_STRESS_THRESHOLD
            - ENABLE_AUTH
            - ENABLE_AUTO_OWNER
            - ENABLE_MODERATOR_CHECKS
            - ENABLE_CODEC_VP8
            - ENABLE_CODEC_VP9
            - ENABLE_CODEC_AV1
            - ENABLE_CODEC_H264
            - ENABLE_CODEC_OPUS_RED
            - ENABLE_JVB_XMPP_SERVER
            - ENABLE_OCTO
            - ENABLE_OCTO_SCTP
            - ENABLE_RECORDING
            - ENABLE_SCTP
            - ENABLE_SHARED_DOCUMENT_RANDOM_NAME
            - ENABLE_TRANSCRIPTIONS
            - ENABLE_VISITORS
            - ENABLE_AUTO_LOGIN
            - JICOFO_AUTH_LIFETIME
            - JICOFO_AUTH_PASSWORD
            - JICOFO_AUTH_TYPE
            - JICOFO_BRIDGE_REGION_GROUPS
            - JICOFO_ENABLE_AUTH
            - JICOFO_ENABLE_BRIDGE_HEALTH_CHECKS
            - JICOFO_CONF_INITIAL_PARTICIPANT_WAIT_TIMEOUT
            - JICOFO_CONF_SINGLE_PARTICIPANT_TIMEOUT
            - JICOFO_CONF_SOURCE_SIGNALING_DELAYS
            - JICOFO_CONF_MAX_AUDIO_SENDERS
            - JICOFO_CONF_MAX_VIDEO_SENDERS
            - JICOFO_CONF_STRIP_SIMULCAST
            - JICOFO_CONF_SSRC_REWRITING
            - JICOFO_ENABLE_HEALTH_CHECKS
            - JICOFO_ENABLE_ICE_FAILURE_DETECTION
            - JICOFO_ENABLE_REST
            - JICOFO_HEALTH_CHECKS_USE_PRESENCE
            - JICOFO_MAX_MEMORY
            - JICOFO_MULTI_STREAM_BACKWARD_COMPAT
            - JICOFO_OCTO_REGION
            - JICOFO_TRUSTED_DOMAINS
            - JIBRI_BREWERY_MUC
            - JIBRI_REQUEST_RETRIES
            - JIBRI_PENDING_TIMEOUT
            - JIGASI_BREWERY_MUC
            - JIGASI_SIP_URI
            - JIGASI_TRUSTED_DOMAINS
            - JVB_BREWERY_MUC
            - JVB_XMPP_AUTH_DOMAIN
            - JVB_XMPP_INTERNAL_MUC_DOMAIN
            - JVB_XMPP_PORT
            - JVB_XMPP_SERVER
            - MAX_BRIDGE_PARTICIPANTS
            - OCTO_BRIDGE_SELECTION_STRATEGY
            - PROSODY_VISITORS_MUC_PREFIX
            - SENTRY_DSN="${JICOFO_SENTRY_DSN:-0}"
            - SENTRY_ENVIRONMENT
            - SENTRY_RELEASE
            - TZ
            - VISITORS_MAX_PARTICIPANTS
            - VISITORS_MAX_VISITORS_PER_NODE
            - VISITORS_XMPP_AUTH_DOMAIN
            - VISITORS_XMPP_SERVER
            - VISITORS_XMPP_DOMAIN
            - XMPP_DOMAIN
            - XMPP_AUTH_DOMAIN
            - XMPP_INTERNAL_MUC_DOMAIN
            - XMPP_MUC_DOMAIN
            - XMPP_HIDDEN_DOMAIN
            - XMPP_SERVER
            - XMPP_PORT
            - XMPP_RECORDER_DOMAIN
            - MAX_SSRCS_PER_USER
            - MAX_SSRC_GROUPS_PER_USER
        depends_on:
            - prosody
        networks:
            meet.jitsi:

    # Video bridge
    jvb:
        image: jitsi/jvb:${JITSI_IMAGE_VERSION:-unstable}
        restart: ${RESTART_POLICY:-unless-stopped}
        ports:
            - '${JVB_PORT:-10000}:${JVB_PORT:-10000}/udp'
            - '127.0.0.1:${JVB_COLIBRI_PORT:-8080}:8080'
        volumes:
            - ${CONFIG}/jvb:/config:Z
        labels:
            service: "jitsi-jvb"
        environment:
            - AUTOSCALER_SIDECAR_KEY_FILE
            - AUTOSCALER_SIDECAR_KEY_ID
            - AUTOSCALER_SIDECAR_GROUP_NAME
            - AUTOSCALER_SIDECAR_HOST_ID
            - AUTOSCALER_SIDECAR_INSTANCE_ID
            - AUTOSCALER_SIDECAR_PORT
            - AUTOSCALER_SIDECAR_REGION
            - AUTOSCALER_SIDECAR_SHUTDOWN_POLLING_INTERVAL
            - AUTOSCALER_SIDECAR_STATS_POLLING_INTERVAL
            - DOCKER_HOST_ADDRESS
            - ENABLE_COLIBRI_WEBSOCKET
            - ENABLE_JVB_XMPP_SERVER
            - ENABLE_OCTO
            - ENABLE_SCTP
            - JVB_ADVERTISE_IPS
            - JVB_ADVERTISE_PRIVATE_CANDIDATES
            - JVB_AUTH_USER
            - JVB_AUTH_PASSWORD
            - JVB_BREWERY_MUC
            - JVB_CC_TRUST_BWE
            - JVB_DISABLE_STUN
            - JVB_DISABLE_XMPP
            - JVB_INSTANCE_ID
            - JVB_PORT
            - JVB_MUC_NICKNAME
            - JVB_STUN_SERVERS
            - JVB_LOG_FILE
            - JVB_OCTO_BIND_ADDRESS
            - JVB_OCTO_REGION
            - JVB_OCTO_RELAY_ID
            - JVB_REQUIRE_VALID_ADDRESS
            - JVB_USE_USRSCTP
            - JVB_WS_DOMAIN
            - JVB_WS_SERVER_ID
            - JVB_WS_TLS
            - JVB_XMPP_AUTH_DOMAIN
            - JVB_XMPP_INTERNAL_MUC_DOMAIN
            - JVB_XMPP_PORT
            - JVB_XMPP_SERVER
            - PUBLIC_URL
            - SENTRY_DSN="${JVB_SENTRY_DSN:-0}"
            - SENTRY_ENVIRONMENT
            - SENTRY_RELEASE
            - COLIBRI_REST_ENABLED
            - SHUTDOWN_REST_ENABLED
            - TZ
            - VIDEOBRIDGE_MAX_MEMORY
            - XMPP_AUTH_DOMAIN
            - XMPP_INTERNAL_MUC_DOMAIN
            - XMPP_SERVER
            - XMPP_PORT
        depends_on:
            - prosody
        networks:
            meet.jitsi:

# Custom network so all services can communicate using a FQDN
networks:
    meet.jitsi:
````

## File: env.example
````
# shellcheck disable=SC2034

################################################################################
################################################################################
# Welcome to the Jitsi Meet Docker setup!
#
# This sample .env file contains some basic options to get you started.
# The full options reference can be found here:
# https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker
################################################################################
################################################################################


#
# Basic configuration options
#

# Directory where all configuration will be stored
CONFIG=~/.jitsi-meet-cfg

# Exposed HTTP port (will redirect to HTTPS port)
HTTP_PORT=8000

# Exposed HTTPS port
HTTPS_PORT=8443

# System time zone
TZ=UTC

# Public URL for the web service (required)
# Keep in mind that if you use a non-standard HTTPS port, it has to appear in the public URL
#PUBLIC_URL=https://meet.example.com:${HTTPS_PORT}

# Media IP addresses to advertise by the JVB
# This setting deprecates DOCKER_HOST_ADDRESS, and supports a comma separated list of IPs
# See the "Running behind NAT or on a LAN environment" section in the Handbook:
# https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker#running-behind-nat-or-on-a-lan-environment
#JVB_ADVERTISE_IPS=192.168.1.1,1.2.3.4

#
# Memory limits for Java components
#

#JICOFO_MAX_MEMORY=3072m
#VIDEOBRIDGE_MAX_MEMORY=3072m

#
# JaaS Components (beta)
# https://jaas.8x8.vc
#

# Enable JaaS Components (hosted Jigasi)
# NOTE: if Let's Encrypt is enabled a JaaS account will be automatically created, using the provided email in LETSENCRYPT_EMAIL
#ENABLE_JAAS_COMPONENTS=0

#
# Let's Encrypt configuration
#

# Enable Let's Encrypt certificate generation
#ENABLE_LETSENCRYPT=1

# Domain for which to generate the certificate
#LETSENCRYPT_DOMAIN=meet.example.com

# E-Mail for receiving important account notifications (mandatory)
#LETSENCRYPT_EMAIL=alice@atlanta.net

# Use the staging server (for avoiding rate limits while testing)
#LETSENCRYPT_USE_STAGING=1

# Set ACME server. Default is zerossl, you can peek one at https://github.com/acmesh-official/acme.sh/wiki/Server
#LETSENCRYPT_ACME_SERVER="letsencrypt"

#
# Etherpad integration (for document sharing)
#

# Set the etherpad-lite URL in the docker local network (uncomment to enable)
#ETHERPAD_URL_BASE=http://etherpad.meet.jitsi:9001

# Set etherpad-lite public URL, including /p/ pad path fragment (uncomment to enable)
#ETHERPAD_PUBLIC_URL=https://etherpad.my.domain/p/


#
# Whiteboard integration
#

# Set the excalidraw-backend URL in the docker local network (uncomment to enable)
#WHITEBOARD_COLLAB_SERVER_URL_BASE=http://whiteboard.meet.jitsi

# Set the excalidraw-backend public URL (uncomment to enable)
#WHITEBOARD_COLLAB_SERVER_PUBLIC_URL=https://whiteboard.meet.my.domain


#
# Basic Jigasi configuration options (needed for SIP gateway support)
#

# SIP URI for incoming / outgoing calls
#JIGASI_SIP_URI=test@sip2sip.info

# Password for the specified SIP account as a clear text
#JIGASI_SIP_PASSWORD=passw0rd

# SIP server (use the SIP account domain if in doubt)
#JIGASI_SIP_SERVER=sip2sip.info

# SIP server port
#JIGASI_SIP_PORT=5060

# SIP server transport
#JIGASI_SIP_TRANSPORT=UDP


#
# Authentication configuration (see handbook for details)
#

# Enable authentication (will ask for login and password to join the meeting)
#ENABLE_AUTH=1

# Enable guest access (if authentication is enabled, this allows for users to be held in lobby until registered user lets them in)
#ENABLE_GUESTS=1

# Select authentication type: internal, jwt, ldap or matrix
#AUTH_TYPE=internal

# JWT authentication
#

# Application identifier
#JWT_APP_ID=my_jitsi_app_id

# Application secret known only to your token generator
#JWT_APP_SECRET=my_jitsi_app_secret

# (Optional) Set asap_accepted_issuers as a comma separated list
#JWT_ACCEPTED_ISSUERS=my_web_client,my_app_client

# (Optional) Set asap_accepted_audiences as a comma separated list
#JWT_ACCEPTED_AUDIENCES=my_server1,my_server2

# LDAP authentication (for more information see the Cyrus SASL saslauthd.conf man page)
#

# LDAP url for connection
#LDAP_URL=ldaps://ldap.domain.com/

# LDAP base DN. Can be empty
#LDAP_BASE=DC=example,DC=domain,DC=com

# LDAP user DN. Do not specify this parameter for the anonymous bind
#LDAP_BINDDN=CN=binduser,OU=users,DC=example,DC=domain,DC=com

# LDAP user password. Do not specify this parameter for the anonymous bind
#LDAP_BINDPW=LdapUserPassw0rd

# LDAP filter. Tokens example:
# %1-9 - if the input key is user@mail.domain.com, then %1 is com, %2 is domain and %3 is mail
# %s - %s is replaced by the complete service string
# %r - %r is replaced by the complete realm string
#LDAP_FILTER=(sAMAccountName=%u)

# LDAP authentication method
#LDAP_AUTH_METHOD=bind

# LDAP version
#LDAP_VERSION=3

# LDAP TLS using
#LDAP_USE_TLS=1

# List of SSL/TLS ciphers to allow
#LDAP_TLS_CIPHERS=SECURE256:SECURE128:!AES-128-CBC:!ARCFOUR-128:!CAMELLIA-128-CBC:!3DES-CBC:!CAMELLIA-128-CBC

# Require and verify server certificate
#LDAP_TLS_CHECK_PEER=1

# Path to CA cert file. Used when server certificate verify is enabled
#LDAP_TLS_CACERT_FILE=/etc/ssl/certs/ca-certificates.crt

# Path to CA certs directory. Used when server certificate verify is enabled
#LDAP_TLS_CACERT_DIR=/etc/ssl/certs

# Wether to use starttls, implies LDAPv3 and requires ldap:// instead of ldaps://
# LDAP_START_TLS=1


#
# Security
#
# Set these to strong passwords to avoid intruders from impersonating a service account
# The service(s) won't start unless these are specified
# Running ./gen-passwords.sh will update .env with strong passwords
# You may skip the Jigasi and Jibri passwords if you are not using those
# DO NOT reuse passwords
#

# XMPP password for Jicofo client connections
JICOFO_AUTH_PASSWORD=

# XMPP password for JVB client connections
JVB_AUTH_PASSWORD=

# XMPP password for Jigasi MUC client connections
JIGASI_XMPP_PASSWORD=

# XMPP password for Jigasi transcriber client connections
JIGASI_TRANSCRIBER_PASSWORD=

# XMPP recorder password for Jibri client connections
JIBRI_RECORDER_PASSWORD=

# XMPP password for Jibri client connections
JIBRI_XMPP_PASSWORD=

#
# Docker Compose options
#

# Container restart policy
#RESTART_POLICY=unless-stopped

# Jitsi image version (useful for local development)
#JITSI_IMAGE_VERSION=latest
````

## File: etherpad.yml
````yaml
version: '3.5'

services:
    # Etherpad: real-time collaborative document editing
    etherpad:
        image: etherpad/etherpad:2.0.3
        restart: ${RESTART_POLICY:-unless-stopped}
        environment:
            - TITLE=${ETHERPAD_TITLE:-""}
            - DEFAULT_PAD_TEXT=${ETHERPAD_DEFAULT_PAD_TEXT:-""}
            - SKIN_NAME=${ETHERPAD_SKIN_NAME:-colibris}
            - SKIN_VARIANTS=${ETHERPAD_SKIN_VARIANTS:-"super-light-toolbar super-light-editor light-background full-width-editor"}
            - SUPPRESS_ERRORS_IN_PAD_TEXT=true
        networks:
            meet.jitsi:
                aliases:
                    - etherpad.meet.jitsi
````

## File: gen-passwords.sh
````bash
#!/usr/bin/env bash

function generatePassword() {
    openssl rand -hex 16
}

JICOFO_AUTH_PASSWORD=$(generatePassword)
JVB_AUTH_PASSWORD=$(generatePassword)
JIGASI_XMPP_PASSWORD=$(generatePassword)
JIBRI_RECORDER_PASSWORD=$(generatePassword)
JIBRI_XMPP_PASSWORD=$(generatePassword)
JIGASI_TRANSCRIBER_PASSWORD=$(generatePassword)

sed -i.bak \
    -e "s#JICOFO_AUTH_PASSWORD=.*#JICOFO_AUTH_PASSWORD=${JICOFO_AUTH_PASSWORD}#g" \
    -e "s#JVB_AUTH_PASSWORD=.*#JVB_AUTH_PASSWORD=${JVB_AUTH_PASSWORD}#g" \
    -e "s#JIGASI_XMPP_PASSWORD=.*#JIGASI_XMPP_PASSWORD=${JIGASI_XMPP_PASSWORD}#g" \
    -e "s#JIBRI_RECORDER_PASSWORD=.*#JIBRI_RECORDER_PASSWORD=${JIBRI_RECORDER_PASSWORD}#g" \
    -e "s#JIBRI_XMPP_PASSWORD=.*#JIBRI_XMPP_PASSWORD=${JIBRI_XMPP_PASSWORD}#g" \
    -e "s#JIGASI_TRANSCRIBER_PASSWORD=.*#JIGASI_TRANSCRIBER_PASSWORD=${JIGASI_TRANSCRIBER_PASSWORD}#g" \
    "$(dirname "$0")/.env"
````

## File: grafana.yml
````yaml
version: '3.5'

services:
  # Grafana: used for visualization of metrics and log data through customizable dashboards.
  grafana:
    image: grafana/grafana:10.2.0
    environment:
      - GF_ANALYTICS_REPORTING_ENABLED=false
    volumes:
      - ./log-analyser/grafana:/var/lib/grafana
      - ./log-analyser/grafana-provisioning/dashboards/:/etc/grafana/provisioning/dashboards/
    ports:
      - "3000:3000"
    networks:
      meet.jitsi:
````

## File: jibri.yml
````yaml
version: '3.5'

services:
    jibri:
        image: jitsi/jibri:${JITSI_IMAGE_VERSION:-unstable}
        restart: ${RESTART_POLICY:-unless-stopped}
        volumes:
            - ${CONFIG}/jibri:/config:Z
        shm_size: '2gb'
        cap_add:
            - SYS_ADMIN
        environment:
            - AUTOSCALER_SIDECAR_KEY_FILE
            - AUTOSCALER_SIDECAR_KEY_ID
            - AUTOSCALER_SIDECAR_GROUP_NAME
            - AUTOSCALER_SIDECAR_HOST_ID
            - AUTOSCALER_SIDECAR_INSTANCE_ID
            - AUTOSCALER_SIDECAR_PORT
            - AUTOSCALER_SIDECAR_REGION
            - AUTOSCALER_SIDECAR_SHUTDOWN_POLLING_INTERVAL
            - AUTOSCALER_SIDECAR_STATS_POLLING_INTERVAL
            - AUTOSCALER_URL
            - CHROMIUM_FLAGS
            - DISPLAY=:0
            - ENABLE_STATS_D
            - IGNORE_CERTIFICATE_ERRORS
            - JIBRI_WEBHOOK_SUBSCRIBERS
            - JIBRI_INSTANCE_ID
            - JIBRI_ENABLE_PROMETHEUS
            - JIBRI_HTTP_API_EXTERNAL_PORT
            - JIBRI_HTTP_API_INTERNAL_PORT
            - JIBRI_RECORDING_RESOLUTION
            - JIBRI_RECORDING_VIDEO_ENCODE_PRESET_RECORDING
            - JIBRI_RECORDING_VIDEO_ENCODE_PRESET_STREAMING
            - JIBRI_RECORDING_CONSTANT_RATE_FACTOR
            - JIBRI_RECORDING_FRAMERATE
            - JIBRI_RECORDING_QUEUE_SIZE
            - JIBRI_RECORDING_STREAMING_MAX_BITRATE
            - JIBRI_USAGE_TIMEOUT
            - JIBRI_XMPP_USER
            - JIBRI_XMPP_PASSWORD
            - JIBRI_XORG_HORIZ_SYNC
            - JIBRI_XORG_VERT_REFRESH
            - JIBRI_BREWERY_MUC
            - JIBRI_RECORDER_USER
            - JIBRI_RECORDER_PASSWORD
            - JIBRI_RECORDING_DIR
            - JIBRI_FINALIZE_RECORDING_SCRIPT_PATH
            - JIBRI_STRIP_DOMAIN_JID
            - JIBRI_STATSD_HOST
            - JIBRI_STATSD_PORT
            - LOCAL_ADDRESS
            - PUBLIC_URL
            - TZ
            - XMPP_AUTH_DOMAIN
            - XMPP_DOMAIN
            - XMPP_INTERNAL_MUC_DOMAIN
            - XMPP_MUC_DOMAIN
            - XMPP_HIDDEN_DOMAIN
            - XMPP_SERVER
            - XMPP_PORT
            - XMPP_RECORDER_DOMAIN
            - XMPP_TRUST_ALL_CERTS
        depends_on:
            - jicofo
        networks:
            meet.jitsi:
````

## File: jigasi.yml
````yaml
version: '3.5'

services:
    # SIP gateway (audio)
    jigasi:
        image: jitsi/jigasi:${JITSI_IMAGE_VERSION:-unstable}
        restart: ${RESTART_POLICY:-unless-stopped}
        ports:
            - '${JIGASI_PORT_MIN:-20000}-${JIGASI_PORT_MAX:-20050}:${JIGASI_PORT_MIN:-20000}-${JIGASI_PORT_MAX:-20050}/udp'
        volumes:
            - ${CONFIG}/jigasi:/config:Z
        environment:
            - AUTOSCALER_SIDECAR_KEY_FILE
            - AUTOSCALER_SIDECAR_KEY_ID
            - AUTOSCALER_SIDECAR_GROUP_NAME
            - AUTOSCALER_SIDECAR_HOST_ID
            - AUTOSCALER_SIDECAR_INSTANCE_ID
            - AUTOSCALER_SIDECAR_PORT
            - AUTOSCALER_SIDECAR_REGION
            - AUTOSCALER_SIDECAR_SHUTDOWN_POLLING_INTERVAL
            - AUTOSCALER_SIDECAR_STATS_POLLING_INTERVAL
            - AUTOSCALER_URL
            - BOSH_URL_PATTERN
            - ENABLE_AUTH
            - ENABLE_GUESTS
            - ENABLE_VISITORS
            - XMPP_AUTH_DOMAIN
            - XMPP_GUEST_DOMAIN
            - XMPP_MUC_DOMAIN
            - XMPP_INTERNAL_MUC_DOMAIN
            - XMPP_SERVER
            - XMPP_PORT
            - XMPP_DOMAIN
            - PUBLIC_URL
            - JIGASI_CONFIGURATION
            - JIGASI_DISABLE_SIP
            - JIGASI_JVB_TIMEOUT
            - JIGASI_LOCAL_REGION
            - JIGASI_LOG_FILE
            - JIGASI_MODE=sip
            - JIGASI_SIP_URI
            - JIGASI_SIP_PASSWORD
            - JIGASI_SIP_SERVER
            - JIGASI_SIP_PORT
            - JIGASI_SIP_TRANSPORT
            - JIGASI_SIP_DEFAULT_ROOM
            - JIGASI_XMPP_USER
            - JIGASI_XMPP_PASSWORD
            - JIGASI_BREWERY_MUC
            - JIGASI_PORT_MIN
            - JIGASI_PORT_MAX
            - JIGASI_HEALTH_CHECK_SIP_URI
            - JIGASI_HEALTH_CHECK_INTERVAL
            - JIGASI_SIP_KEEP_ALIVE_METHOD
            - JIGASI_ENABLE_SDES_SRTP
            - JIGASI_VISITORS_QUEUE_SERVICE_URL
            - JIGASI_VISITORS_QUEUE_SERVICE_PRIVATE_KEY_PATH
            - JIGASI_VISITORS_QUEUE_SERVICE_PRIVATE_KEY_ID
            - SHUTDOWN_REST_ENABLED
            - SENTRY_DSN="${JIGASI_SENTRY_DSN:-0}"
            - SENTRY_ENVIRONMENT
            - SENTRY_RELEASE
            - TZ
            - USE_TRANSLATOR_IN_CONFERENCE
        depends_on:
            - prosody
        networks:
            meet.jitsi:
````

## File: LICENSE
````
Apache License
                           Version 2.0, January 2004
                        http://www.apache.org/licenses/


   TERMS AND CONDITIONS FOR USE, REPRODUCTION, AND DISTRIBUTION

   1. Definitions.

      "License" shall mean the terms and conditions for use, reproduction,
      and distribution as defined by Sections 1 through 9 of this document.

      "Licensor" shall mean the copyright owner or entity authorized by
      the copyright owner that is granting the License.

      "Legal Entity" shall mean the union of the acting entity and all
      other entities that control, are controlled by, or are under common
      control with that entity. For the purposes of this definition,
      "control" means (i) the power, direct or indirect, to cause the
      direction or management of such entity, whether by contract or
      otherwise, or (ii) ownership of fifty percent (50%) or more of the
      outstanding shares, or (iii) beneficial ownership of such entity.

      "You" (or "Your") shall mean an individual or Legal Entity
      exercising permissions granted by this License.

      "Source" form shall mean the preferred form for making modifications,
      including but not limited to software source code, documentation
      source, and configuration files.

      "Object" form shall mean any form resulting from mechanical
      transformation or translation of a Source form, including but
      not limited to compiled object code, generated documentation,
      and conversions to other media types.

      "Work" shall mean the work of authorship, whether in Source or
      Object form, made available under the License, as indicated by a
      copyright notice that is included in or attached to the work
      (an example is provided in the Appendix below).

      "Derivative Works" shall mean any work, whether in Source or Object
      form, that is based on (or derived from) the Work and for which the
      editorial revisions, annotations, elaborations, or other modifications
      represent, as a whole, an original work of authorship. For the purposes
      of this License, Derivative Works shall not include works that remain
      separable from, or merely link (or bind by name) to the interfaces of,
      the Work and Derivative Works thereof.

      "Contribution" shall mean any work of authorship, including
      the original version of the Work and any modifications or additions
      to that Work or Derivative Works thereof, that is intentionally
      submitted to Licensor for inclusion in the Work by the copyright owner
      or by an individual or Legal Entity authorized to submit on behalf of
      the copyright owner. For the purposes of this definition, "submitted"
      means any form of electronic, verbal, or written communication sent
      to the Licensor or its representatives, including but not limited to
      communication on electronic mailing lists, source code control systems,
      and issue tracking systems that are managed by, or on behalf of, the
      Licensor for the purpose of discussing and improving the Work, but
      excluding communication that is conspicuously marked or otherwise
      designated in writing by the copyright owner as "Not a Contribution."

      "Contributor" shall mean Licensor and any individual or Legal Entity
      on behalf of whom a Contribution has been received by Licensor and
      subsequently incorporated within the Work.

   2. Grant of Copyright License. Subject to the terms and conditions of
      this License, each Contributor hereby grants to You a perpetual,
      worldwide, non-exclusive, no-charge, royalty-free, irrevocable
      copyright license to reproduce, prepare Derivative Works of,
      publicly display, publicly perform, sublicense, and distribute the
      Work and such Derivative Works in Source or Object form.

   3. Grant of Patent License. Subject to the terms and conditions of
      this License, each Contributor hereby grants to You a perpetual,
      worldwide, non-exclusive, no-charge, royalty-free, irrevocable
      (except as stated in this section) patent license to make, have made,
      use, offer to sell, sell, import, and otherwise transfer the Work,
      where such license applies only to those patent claims licensable
      by such Contributor that are necessarily infringed by their
      Contribution(s) alone or by combination of their Contribution(s)
      with the Work to which such Contribution(s) was submitted. If You
      institute patent litigation against any entity (including a
      cross-claim or counterclaim in a lawsuit) alleging that the Work
      or a Contribution incorporated within the Work constitutes direct
      or contributory patent infringement, then any patent licenses
      granted to You under this License for that Work shall terminate
      as of the date such litigation is filed.

   4. Redistribution. You may reproduce and distribute copies of the
      Work or Derivative Works thereof in any medium, with or without
      modifications, and in Source or Object form, provided that You
      meet the following conditions:

      (a) You must give any other recipients of the Work or
          Derivative Works a copy of this License; and

      (b) You must cause any modified files to carry prominent notices
          stating that You changed the files; and

      (c) You must retain, in the Source form of any Derivative Works
          that You distribute, all copyright, patent, trademark, and
          attribution notices from the Source form of the Work,
          excluding those notices that do not pertain to any part of
          the Derivative Works; and

      (d) If the Work includes a "NOTICE" text file as part of its
          distribution, then any Derivative Works that You distribute must
          include a readable copy of the attribution notices contained
          within such NOTICE file, excluding those notices that do not
          pertain to any part of the Derivative Works, in at least one
          of the following places: within a NOTICE text file distributed
          as part of the Derivative Works; within the Source form or
          documentation, if provided along with the Derivative Works; or,
          within a display generated by the Derivative Works, if and
          wherever such third-party notices normally appear. The contents
          of the NOTICE file are for informational purposes only and
          do not modify the License. You may add Your own attribution
          notices within Derivative Works that You distribute, alongside
          or as an addendum to the NOTICE text from the Work, provided
          that such additional attribution notices cannot be construed
          as modifying the License.

      You may add Your own copyright statement to Your modifications and
      may provide additional or different license terms and conditions
      for use, reproduction, or distribution of Your modifications, or
      for any such Derivative Works as a whole, provided Your use,
      reproduction, and distribution of the Work otherwise complies with
      the conditions stated in this License.

   5. Submission of Contributions. Unless You explicitly state otherwise,
      any Contribution intentionally submitted for inclusion in the Work
      by You to the Licensor shall be under the terms and conditions of
      this License, without any additional terms or conditions.
      Notwithstanding the above, nothing herein shall supersede or modify
      the terms of any separate license agreement you may have executed
      with Licensor regarding such Contributions.

   6. Trademarks. This License does not grant permission to use the trade
      names, trademarks, service marks, or product names of the Licensor,
      except as required for reasonable and customary use in describing the
      origin of the Work and reproducing the content of the NOTICE file.

   7. Disclaimer of Warranty. Unless required by applicable law or
      agreed to in writing, Licensor provides the Work (and each
      Contributor provides its Contributions) on an "AS IS" BASIS,
      WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or
      implied, including, without limitation, any warranties or conditions
      of TITLE, NON-INFRINGEMENT, MERCHANTABILITY, or FITNESS FOR A
      PARTICULAR PURPOSE. You are solely responsible for determining the
      appropriateness of using or redistributing the Work and assume any
      risks associated with Your exercise of permissions under this License.

   8. Limitation of Liability. In no event and under no legal theory,
      whether in tort (including negligence), contract, or otherwise,
      unless required by applicable law (such as deliberate and grossly
      negligent acts) or agreed to in writing, shall any Contributor be
      liable to You for damages, including any direct, indirect, special,
      incidental, or consequential damages of any character arising as a
      result of this License or out of the use or inability to use the
      Work (including but not limited to damages for loss of goodwill,
      work stoppage, computer failure or malfunction, or any and all
      other commercial damages or losses), even if such Contributor
      has been advised of the possibility of such damages.

   9. Accepting Warranty or Additional Liability. While redistributing
      the Work or Derivative Works thereof, You may choose to offer,
      and charge a fee for, acceptance of support, warranty, indemnity,
      or other liability obligations and/or rights consistent with this
      License. However, in accepting such obligations, You may act only
      on Your own behalf and on Your sole responsibility, not on behalf
      of any other Contributor, and only if You agree to indemnify,
      defend, and hold each Contributor harmless for any liability
      incurred by, or claims asserted against, such Contributor by reason
      of your accepting any such warranty or additional liability.

   END OF TERMS AND CONDITIONS

   APPENDIX: How to apply the Apache License to your work.

      To apply the Apache License to your work, attach the following
      boilerplate notice, with the fields enclosed by brackets "[]"
      replaced with your own identifying information. (Don't include
      the brackets!)  The text should be enclosed in the appropriate
      comment syntax for the file format. We also recommend that a
      file or class name and description of purpose be included on the
      same "printed page" as the copyright notice for easier
      identification within third-party archives.

   Copyright [yyyy] [name of copyright owner]

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
````

## File: log-analyser.yml
````yaml
version: '3.5'

services:
  # Log Analyser: used for setting up a log analysis system for visualization, log collection and log processing.

  loki:
    container_name: loki
    image: grafana/loki:3.0.0
    command: -config.file=/conf/loki-config.yaml
    volumes:
      - ./log-analyser/loki/data:/data
      - ./log-analyser/loki/conf:/conf
    ports:
      - "3100:3100"
    networks:
      meet.jitsi:

  otel-collector:
    container_name: otel
    image: otel/opentelemetry-collector-contrib
    user: "0" # required for reading docker container logs
    volumes:
      - ./log-analyser/otel-collector-config.yaml:/etc/otelcol-contrib/config.yaml
      - ./log-analyser/jitsi-logs/:/tmp/jitsi-logs/
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      meet.jitsi:
````

## File: Makefile
````
FORCE_REBUILD ?= 0
JITSI_RELEASE ?= stable
JITSI_BUILD ?= unstable
JITSI_REPO ?= jitsi

JITSI_SERVICES := base base-java web prosody jicofo jvb jigasi jibri

BUILD_ARGS := \
	--build-arg JITSI_REPO=$(JITSI_REPO) \
	--build-arg JITSI_RELEASE=$(JITSI_RELEASE)

ifeq ($(FORCE_REBUILD), 1)
  BUILD_ARGS := $(BUILD_ARGS) --no-cache
endif


all: build-all

release:
	@$(foreach SERVICE, $(JITSI_SERVICES), $(MAKE) --no-print-directory JITSI_SERVICE=$(SERVICE) buildx;)

buildx:
	docker buildx build \
		--platform linux/amd64,linux/arm64 \
		--progress=plain \
		$(BUILD_ARGS) --build-arg BASE_TAG=$(JITSI_BUILD) \
		--pull --push \
		--tag $(JITSI_REPO)/$(JITSI_SERVICE):$(JITSI_BUILD) \
		--tag $(JITSI_REPO)/$(JITSI_SERVICE):$(JITSI_RELEASE) \
		$(JITSI_SERVICE)

$(addprefix buildx_,$(JITSI_SERVICES)):
	$(MAKE) --no-print-directory JITSI_SERVICE=$(patsubst buildx_%,%,$@) buildx

build:
	docker buildx build \
		$(BUILD_ARGS) \
		--load \
		--progress plain \
		--tag $(JITSI_REPO)/$(JITSI_SERVICE) \
		$(JITSI_SERVICE)

$(addprefix build_,$(JITSI_SERVICES)):
	$(MAKE) --no-print-directory JITSI_SERVICE=$(patsubst build_%,%,$@) build

tag:
	docker tag $(JITSI_REPO)/$(JITSI_SERVICE) $(JITSI_REPO)/$(JITSI_SERVICE):$(JITSI_BUILD)

push:
	docker push $(JITSI_REPO)/$(JITSI_SERVICE):$(JITSI_BUILD)

%-all:
	@$(foreach SERVICE, $(JITSI_SERVICES), $(MAKE) --no-print-directory JITSI_SERVICE=$(SERVICE) $(subst -all,;,$@))

clean:
	docker-compose stop
	docker-compose rm
	docker network prune

prepare:
	FORCE_REBUILD=1 $(MAKE)

.PHONY: all build tag push clean prepare release $(addprefix build_,$(JITSI_SERVICES))
````

## File: prometheus.yml
````yaml
services:
  prometheus:
    image: prom/prometheus
    container_name: prometheus
    command:
      - "--config.file=/etc/prometheus/prometheus.yml"
    ports:
      - 9090:9090
    restart: ${RESTART_POLICY:-unless-stopped}
    volumes:
      - ./prometheus:/etc/prometheus
    networks:
      meet.jitsi:
````

## File: README.md
````markdown
# Jitsi Meet on Docker

![](resources/jitsi-docker.png)

[Jitsi](https://jitsi.org/) is a set of Open Source projects that allows you to easily build and deploy secure videoconferencing solutions.

[Jitsi Meet](https://jitsi.org/jitsi-meet/) is a fully encrypted, 100% Open Source video conferencing solution that you can use all day, every day, for free — with no account needed.

This repository contains the necessary tools to run a Jitsi Meet stack on [Docker](https://www.docker.com) using [Docker Compose](https://docs.docker.com/compose/).

All our images are published on [DockerHub](https://hub.docker.com/u/jitsi/).

## Supported architectures

Starting with `stable-7439` the published images are available for `amd64` and `arm64`.

## Tags

These are the currently published tags for all our images:

Tag | Description
-- | --
`stable` | Points to the latest stable release
`stable-NNNN-X` | A stable release
`unstable` | Points to the latest unstable release
`unstable-YYYY-MM-DD` | Daily unstable release
`latest` | Deprecated, no longer updated (will be removed)

## Installation

The installation manual is available [here](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker).

### Kubernetes

If you plan to install the jitsi-meet stack on a Kubernetes cluster you can find tools and tutorials in the project [Jitsi on Kubernetes](https://github.com/jitsi-contrib/jitsi-kubernetes).

## TODO

* Builtin TURN server.
````

## File: release.sh
````bash
#!/bin/bash

set -e

# **** NB ****
# This iscript is no longer used, but it is kept here for reference.
#


# Don't start a release if the tree is dirty
#

if [[ ! -z $(git status -s) ]]; then
    echo "Git tree is not clean, aborting release!"
    exit 1
fi

# Get version and branch (we only do stable for now)
#

V="$1"
RELEASE="${2:-stable}"

if [[ -z $V ]]; then
    echo "A version must be specified!"
    exit 1
fi

VERSION="${RELEASE}-${V}"
echo "Releasing ${VERSION}"

if git rev-parse "${VERSION}" >/dev/null 2>&1; then
    echo "Tag for such version already exists!"
    exit 1
fi

# Prepare changelog
#

LAST_VERSION=$(git describe --tags --abbrev=0)
CHANGES=$(git log --oneline --no-decorate --no-merges ${LAST_VERSION}..HEAD --pretty=format:"%x2a%x20%h%x20%s")

echo "Changelog:"
echo "$CHANGES"

# Tag Docker images and push them to DockerHub
#

JITSI_BUILD=${VERSION} JITSI_RELEASE=${RELEASE} make release

# Changelog
#

echo -e "## ${VERSION}\n\nBased on ${RELEASE} release ${V}.\n\n${CHANGES}\n" > tmp
cat CHANGELOG.md >> tmp
mv tmp CHANGELOG.md

# Set specific image tags in compose files
#

sed -i".bak" -e "s/unstable/${VERSION}/" *.yml

# Commit all changes and tag the repo
#

git commit -a -m "release: ${VERSION}" -m "${CHANGES}"
git tag -a "${VERSION}" -m "release" -m "${CHANGES}"

# Revert back to "unstable" for development
#

sed -i".bak" -e "s/${VERSION}/unstable/" *.yml

git commit -a -m "misc: working on unstable"

# Push all changes and tags
#

git push
git push --tags
````

## File: transcriber.yml
````yaml
version: '3.5'

services:
    transcriber:
        image: jitsi/jigasi:${JITSI_IMAGE_VERSION:-unstable}
        restart: ${RESTART_POLICY:-unless-stopped}
        volumes:
            - ${CONFIG}/transcriber:/config:Z
            - ${CONFIG}/transcripts:/tmp/transcripts:Z
        environment:
            - AUTOSCALER_SIDECAR_KEY_FILE
            - AUTOSCALER_SIDECAR_KEY_ID
            - AUTOSCALER_SIDECAR_GROUP_NAME
            - AUTOSCALER_SIDECAR_HOST_ID
            - AUTOSCALER_SIDECAR_INSTANCE_ID
            - AUTOSCALER_SIDECAR_PORT
            - AUTOSCALER_SIDECAR_REGION
            - AUTOSCALER_SIDECAR_SHUTDOWN_POLLING_INTERVAL
            - AUTOSCALER_SIDECAR_STATS_POLLING_INTERVAL
            - AUTOSCALER_URL
            - BOSH_URL_PATTERN
            - XMPP_AUTH_DOMAIN
            - XMPP_GUEST_DOMAIN
            - XMPP_MUC_DOMAIN
            - XMPP_INTERNAL_MUC_DOMAIN
            - XMPP_SERVER
            - XMPP_PORT
            - XMPP_HIDDEN_DOMAIN
            - XMPP_RECORDER_DOMAIN
            - XMPP_DOMAIN
            - PUBLIC_URL
            - JIGASI_CONFIGURATION
            - JIGASI_JVB_TIMEOUT
            - JIGASI_LOCAL_REGION
            - JIGASI_LOG_FILE
            - JIGASI_MODE=transcriber
            - JIGASI_XMPP_USER
            - JIGASI_XMPP_PASSWORD
            - JIGASI_BREWERY_MUC
            - JIGASI_TRANSCRIBER_ADVERTISE_URL
            - JIGASI_TRANSCRIBER_CUSTOM_SERVICE
            - JIGASI_TRANSCRIBER_CUSTOM_TRANSLATION_SERVICE
            - JIGASI_TRANSCRIBER_LIBRETRANSLATE_URL
            - JIGASI_TRANSCRIBER_ENABLE_SAVING
            - JIGASI_TRANSCRIBER_ENABLE_TRANSLATION
            - JIGASI_TRANSCRIBER_FILTER_SILENCE
            - JIGASI_TRANSCRIBER_OCI_COMPARTMENT
            - JIGASI_TRANSCRIBER_OCI_REGION
            - JIGASI_TRANSCRIBER_PASSWORD
            - JIGASI_TRANSCRIBER_RECORD_AUDIO
            - JIGASI_TRANSCRIBER_REMOTE_CONFIG_URL
            - JIGASI_TRANSCRIBER_REMOTE_CONFIG_URL_KEY_PATH
            - JIGASI_TRANSCRIBER_REMOTE_CONFIG_URL_KEY_ID
            - JIGASI_TRANSCRIBER_REMOTE_CONFIG_URL_AUD
            - JIGASI_TRANSCRIBER_SEND_TXT
            - JIGASI_TRANSCRIBER_USER
            - JIGASI_TRANSCRIBER_VOSK_URL
            - JIGASI_TRANSCRIBER_WHISPER_URL
            - JIGASI_TRANSCRIBER_WHISPER_PRIVATE_KEY
            - JIGASI_TRANSCRIBER_WHISPER_PRIVATE_KEY_NAME
            - GC_PROJECT_ID
            - GC_PRIVATE_KEY_ID
            - GC_PRIVATE_KEY
            - GC_CLIENT_EMAIL
            - GC_CLIENT_ID
            - GC_CLIENT_CERT_URL
            - SHUTDOWN_REST_ENABLED
            - SENTRY_DSN="${JIGASI_SENTRY_DSN:-0}"
            - SENTRY_ENVIRONMENT
            - SENTRY_RELEASE
            - TZ
        depends_on:
            - prosody
        networks:
            meet.jitsi:
````

## File: whiteboard.yml
````yaml
version: '3.5'

services:
    whiteboard:
        image: jitsi/excalidraw-backend:21
        restart: ${RESTART_POLICY:-unless-stopped}
        depends_on:
            - web
        networks:
            meet.jitsi:
              aliases:
                  - whiteboard.meet.jitsi
````
