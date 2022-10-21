# Packaging it all up

## Checkout our source code
```
    build-container:
      runs-on: ubuntu-latest
      needs: build

      steps:
      - name: checkout scm
        uses: actions/checkout@v2
```

## Get the short SHA associated with the current branch
```
      - name: track short sha
        id: vars
        run: echo "::set-output name=sha_short::$(git rev-parse --short HEAD)"
```
## Download our binary built in the previous step
```
      - name: download binary
        uses: actions/download-artifact@v3
        with:
          name: our-binary-${{ steps.vars.outputs.sha_short }}
```

## Configure docker
```
      - name: setup qemu
        uses: docker/setup-qemu-action@v2

      - name: setup docker buildx
        uses: docker/setup-buildx-action@v2
```

## Authenticate to docker
```
      - name: login to docker hub
        uses: docker/login-action@v2
        with:
          username: glennadjrussell
          password: dckr_pat_jUyM2RIc9Zvh7LKj3PT36a6l8gk
```

## Authenticate to docker registry
```
      - name: login to docker registry
        uses: docker/login-action@v2
        with:
          registry: ghcr.io
          username: glennadjrussell
          password: ${{ secrets.GITHUB_TOKEN }}
```

## Build our container and push
```
      - name: build and push
        uses: docker/build-push-action@v3
        with:
          context: .
          push: false
          tags: |
            glennadjrussell/our-binary:${{ steps.vars.outputs.sha_short }}
            glennadjrussell/our-binary:latest
            ghcr.io/glennadjrussell/our-binary:${{ steps.vars.outputs.sha_short }}
            ghcr.io/glennadjrussell/our-binary:latest
          build-args: |
            SHORT_SHA=${{ steps.vars.outputs.sha_short }}
            #outputs: type=docker,dest=/tmp/image.tar
          outputs: type=docker
```
