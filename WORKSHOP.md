# Workshop 6

## Log into Google
```
      - name: gcloud setup
        uses: google-github-actions/auth@v0
        with:
          credentials_json: ${{ secrets.GOOGLE_CREDENTIALS }}
```
## Setup the Google SDK
```
      - name: gcloud setup
        uses: google-github-actions/setup-gcloud@v0
```
## Authenticate docker to gcloud
```
      - name: gcloud auth
        run: |
          gcloud auth configure-docker
```
## Push our image to Google COntainer Registry
```
      - name: publish to gcr
        run: |
          docker tag glennadjrussell/our-binary:${{ steps.vars.outputs.sha_short }} gcr.io/gh-pipeline/our-binary:${{ steps.vars.outputs.sha_short }}
          docker push gcr.io/gh-pipeline/our-binary:${{ steps.vars.outputs.sha_short }}
```

## Add a comment to the PR
```
      - name: add comment
        uses: actions/github-script@v4
        if: github.event_name == 'pull_request'
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            const output = `Container published to glennadjrussell/our-binary-${{ steps.vars.outputs.sha_short }}`;
            github.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: output
            })
```
