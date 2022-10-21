# Workshop 4

## Check and lint our code
```
- name: run vet and lint
        run: |
          go vet .
          golint .
```
## Run our tests
```
      - name: run tests
        run: go test -v
```
## Get the hash for our branch
```
      - name: track short sha
        id: vars
        run: echo "::set-output name=sha_short::$(git rev-parse --short HEAD)"
```
## Upload our binary for later use
```
      - name: upload binary
        uses: actions/upload-artifact@v3
        with:
          name: our-binary-${{ steps.vars.outputs.sha_short }}
          path: our-binary-${{ steps.vars.outputs.sha_short }}
```

