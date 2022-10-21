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
        run: go build -o our-binary .
```

## Upload our binary
```
- name: upload binary
        uses: actions/upload-artifact@v3
        with:
          name: our-binary
          path: our-binary
```
