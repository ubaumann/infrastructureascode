Title: CI/CD pipeline to update README.md with terraform-docs
Date: 2024-04-19 20:28
Modified: 2024-04-19 20:28
Tags: Terraform, GitLab, CI/CD
Authors: juntersander

In my journey to automate as much as possible, I stumbled upon the problem of keeping the terraform documentation in my README.md up to date with the terraform code.
I thought that should be easy, but as it turns out not as easy as I hoped. (At least for now)

Currently, the simplest way seems to be the following:
1. Create a ssh-key:
	`ssh-keygen -t ed25519 -f ./deploy-key`
2. Store the public key as a deploy key in the gitlab project settings (`Settings/Repository/Deploy keys`)
3. Create a CI/CD variable and call it DEPLOY_KEY.
   Make sure that it's of type "File" and use the private key as value. Be sure to also include the last newline otherwise you will get a `Load Key "path/to/privatekey" error in libcrypto
4. Update `.gitlab-ci.yml`
```yaml
update-docs:
  stage: validate
  image: alpine:3.19.1
  before_script:
    - apk update && apk add --no-cache git curl tar openssh-client-default
    - curl -sSLo /terraform-docs.tar.gz https://terraform-docs.io/dl/v0.17.0/terraform-docs-v0.17.0-$(uname)-amd64.tar.gz
    - tar -xzf /terraform-docs.tar.gz --directory=/
    - mv /terraform-docs ./terraform-docs
    - chmod +x terraform-docs
  script:
    - ./terraform-docs markdown table --output-file README.md --output-mode inject .
    - |
      # Check if README.md has been modified by terraform-doc
      if git diff --name-only | grep -q "README.md"
      then
          # Commit and push with ci.skip option
          git config --global user.email "tf-bot@ost.ch"
          git config --global user.name "tf-bot"
          git config --global core.sshCommand 'ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no'
          git remote set-url origin ssh://git@${CI_SERVER_SHELL_SSH_HOST}:${CI_SERVER_SHELL_SSH_PORT}/${CI_PROJECT_PATH}.git
          mkdir -p ~/.ssh
          cp ${DEPLOY_KEY} ~/.ssh/id_ed25519
          chmod 600 ~/.ssh/id_ed25519
          git add README.md
          git commit -m "Update README.md with terraform-doc" --no-verify
          git push origin HEAD:${CI_COMMIT_BRANCH} -o ci.skip
      fi
```

This could be improved and simplified in the future if the `CI_JOB_TOKEN` has the permissions to also push to the repository.
It's an open issue on gitlab https://gitlab.com/gitlab-org/gitlab/-/issues/389060