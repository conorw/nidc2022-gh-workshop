# Workshop 7

## Getting ready for deployment
```
pre-deploy:
      runs-on: ubuntu-latest
      needs: build-container
```
## Set the working directory
```
      defaults:
        run:
          working-directory: ./terraform
```
## Checkout the source code
```
      steps:
      - name: checkout scm
        uses: actions/checkout@v2

```
## Authenticate to Google cloud
```
      - name: 'Authenticate to Google Cloud'
        uses: 'google-github-actions/auth@v0'
        with:
          credentials_json: '${{ secrets.GOOGLE_CREDENTIALS }}'

```
## Setup and configure terraform
```
      - name: setup terraform
        uses: hashicorp/setup-terraform@v1

      - name: terraform init
        id: init
        run: terraform init

```
## Run Terraform checks on our code
```
      - name: terraform validate
        id: validate
        run: terraform validate -no-color
                
      - name: terraform fmt
        id: fmt
        run: terraform fmt -no-color

```
## Perform a dry run and post the plan to a PR comment
```
      - name: terraform plan
        id: plan
        run: terraform plan -no-color

      - name: update pull request comments
        if: github.event_name == 'pull_request'
        env:
          PLAN: "terraform\n${{ steps.plan.outputs.stdout }}"
        uses: actions/github-script@v4
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            const output = `#### Terraform Format and Style 🖌\`${{ steps.fmt.outcome }}\`
            #### Terraform Initialization ⚙️\`${{ steps.init.outcome }}\`
            #### Terraform Plan 📖\`${{ steps.plan.outcome }}\`
            #### Terraform Validation 🤖\`${{ steps.validate.outcome }}\`
            <details><summary>Show Plan</summary>
            \`\`\`\n
            ${process.env.PLAN}
            \`\`\`
            </details>
            *Pusher: @${{ github.actor }}, Action: \`${{ github.event_name }}\`*`;
            github.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: output
            })
```
# Configure Terraform for the real run
```
    deploy:
      # Only run when a merge happens
      runs-on: ubuntu-latest
      needs: pre-deploy
```
## Only run on PR merge!
```
      if: github.event_name == 'push' && github.ref == 'refs/heads/main'

      defaults:
        run:
          working-directory: ./terraform

```
## Same steps as before
```
      steps:
      - name: checkout scm
        uses: actions/checkout@v2

      - name: 'Authenticate to Google Cloud'
        uses: 'google-github-actions/auth@v0'
        with:
          credentials_json: '${{ secrets.GOOGLE_CREDENTIALS }}'

      - name: setup terraform
        uses: hashicorp/setup-terraform@v1

      - name: terraform init
        id: init
        run: terraform init

      - name: terraform validate
        id: validate
        run: terraform validate -no-color

      - name: terraform fmt
        id: fmt
        run: terraform fmt

      - name: terraform plan
        id: plan
        run: terraform plan -no-color
```
## Deploy our service
```
      - name: terraform apply
        id: apply
        run: terraform apply -auto-approve

```

