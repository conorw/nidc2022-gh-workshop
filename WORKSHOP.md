# Workshop 3

## Check the code out
```
      - name: checkout scm
        uses: actions/checkout@v2
```
## Configure go
```
      - name: setup go
        uses: actions/setup-go@v2
        with:
          go-version: '1.14.0'
```
## Install some dependencies
```
      - name: install some deps
        run: |
          go version
          go get -u golang.org/x/lint/golint
```
## Build our executable
```
      - name: build the app
        run: go build -o our-binary-${{ steps.vars.outputs.sha_short }} .
```
