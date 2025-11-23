-----
### 구축한 CI / CD 모듈화 하기 1
-----
1. github-action-module Repository 생성 후, 생성한 Repository를 git clone
2. /common/test/action.yaml (test module)
```yaml
name: test
authore: SooYoung
description: 'Use test module in github action'
inputs: 
  NODE_VERSION:
    description: 'set node version'
    requried: true
    default: '18'
  WORKING_DIRECTORY:
    description: 'set working directory'
    requried: true
    default: 'my-app'

  runs:
    using: "composite"
    steps:
    - name: setup-node
      uses: actions/setup-node@v3
      with:
        node-version: ${{ inputs.NODE_VERSION }}
    - name: Cache Node.js modules
      uses: actions/cache@v3
      with:
        path: ~/.npm
        key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
        restore-keys: |
          ${{ runner.os }}-node-
    - name: Install dependencies
      shell: bash
      run: |
        cd ${{ inputs.WORKING_DIRECTORY }}
        npm ci
    - name: npm build
      shell: bash
      run: |
        cd ${{ inputs.WORKING_DIRECTORY }}
        npm run build
```

3. /common/set-environment/action.yaml (set-environment Module)
```yaml
name: tset-environment
authore: SooYoung
description: 'Use set-environment module int github action
inputs: 
  REF_TYPE:
    description: 'branch or tag'
    required: true
    default: 'branch'
  BASE_REF:
    description: 'dev or master'
    required: true
    default: 'dev'

outputs:
  environment:
    description: 'set env'
    value: ${{ steps.set-env.outputs.environment }}

runs:
  using: "composite"
  stpes:
  - name: set env
    id: set-env
    shell: bash
    run: |
      if [[ ${{ inputs.REF_TYPE }} == "tag" ]]; then
        echo "environment=qa" >> $GITHUB_OUTPUT 
        exit 0
      fi

      if [[ ${{ inputs.REF_TYPE }} == "branch" ]]; then
        echo "environment=dev" >> $GITHUB_OUTPUT
        if [[ ${{ inputs.BASE_REF }} == "master" ]]; then
          echo "environment=staging" >> $GITHUB_OUTPUT 
        fi
      fi

  - name: check env
    shell: bash
    run: echo ${{ steps.set-env.outputs.environment }}
```

4. /common/aws/action.yaml
```yaml
name: aws
authore: SooYoung
description: 'Use aws module int github action
inputs: 
  AWS_REGION:
    description: 'set aws region'
    required: true
    default: 'ap-northeast-2'
  AWS_ROLE_TO_ASSUME:
    description: 'set aws role to assume'
    required: true
    default: 'GithubActions'

runs:
  using: "composite"
  steps:
  - name: Configure AWS Credentials
    id: credentials
    uses: aws-actions/configure-aws-credentials@v4
    with:
      aws-region: ${{ inputs.AWS_REGION }}
      role-to-assume: ${{ intputs.AWS_ROLE_TO_ASSUME }}
```

5. /common/image-build/action.yaml
```yaml
name: image build
authore: SooYoung
description: 'Use image build module in github action'
inputs: 
  REPOSITORY:
    description: 'set aws ecr repository'
    required: true
    default: 'my-app'
  REGISTRY:
    description: 'set aws ecr registry'
    required: true
    default: ''
  DOCKERFILE_PATH:
    description: 'set dockerfile path'
    required: false
    default: 'Dokcerfile'

runs:
  using: "composite"
  steps:
  - name: Login to Amazon ECR
    id: login-ecr
    uses: aws-actions/amazon-ecr-login@v2
    with:
      mask-password: "true"
  - name: docker build & push
    shell: bash
    run: |
      docker build -f ${{ inputs.DOCKERFILE_PATH }} --tag ${{ inputs.REGISTRY }}/${{ inputs.REPOSITORY }}:${{ github.sha }} .
      docker push ${{ inputs.REGISTRY }}/${{ inputs.REPOSITORY }}:${{ github.sha }}
```

6. /common/deploy/action.yaml
```yaml
name: deploy
authore: SooYoung
description: "Use deploy module in github action"
inputs:
  KUBECTL_VERSION:
    description: "set kubectl version"
    required: false
    default: "latest"
  HELM_VERSION:
    description: "set helm version"
    required: false
    default: v3.11.1
  CLUSTER_NAME:
    description: "set cluster name"
    required: true
    default: "github-actions"
  RELEASE_NAME:
    description: "set release_name"
    required: true
    default: "my-app"
  HELM_CHART_PATH:
    description: "set helm chart path"
    required: true
    default: "kubernetes/my-app"
  NAMESPACE:
    description: "set namespace"
    required: true
    default: "my-app-dev"
  REPOSITORY:
    description: "set ecr registry/repository"
    required: true
    default: ""

runs:
  using: "composite"
  steps:
  - name: setup kubectl
    uses: azure/setup-kubectl@v3
    with:
      version: ${{ inputs.KUBECTL_VERSION }}
  - name: setup helm
    uses: azure/setup-helm@v3
    with:
      version: ${{ inputs.HELM_VERSION }}
  - name: access Kubernetes
    shell: bash
    run: |
      aws eks update-kubeconfig --name ${{ inputs.CLUSTER_NAME }}
  - name: deploy
    shell: bash
    run: |
      helm upgrade --install ${{ inputs.RELEASE_NAME }} ${{ inputs.HELM_CHART_PATH }} --create-namespace --namespace ${{ inputs.NAMESPACE }} \
      --set image.tag=${{ github.sha }} \
      --set image.repository=${{ inputs.REPOSITORY }}
```

7. /common/slack/action.yaml
```yaml
name: slack
authore: SooYoung
description: "Use slack module in github action"
inputs:
  DEPLOY_STEP_STATUS:
    description: "deploy step status"
    required: true
    default: "success"
  ENVIRONMENT:
    description: "set environment"
    required: true
    default: 'dev'
  SLACK_WEBHOOK_URL:
    description: "set slack webhook url"
    required: true
    default: ''

runs:
  using: "composite"
  steps:
  - name: notify
    uses: slackapi/slack-github-action@v1.24.0
    with:
      payload: |
        {
          "text": "message",
          "blocks": [
            {
              "type": "section",
              "text": {
                "type": "mrkdwn",
                "text": "Environment : ${{ inputs.ENVIRONMENT }}, Deploy Result : ${{ inputs.DEPLOY_STEP_STATUS }}, Repository : ${{ github.repository }}."
              }
            }
          ]
        }
    env:
      SLACK_WEBHOOK_URL: ${{ inputs.SLACK_WEBHOOK_URL }}
      SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK
```

8. /common/create-pr/action.yaml
```yaml
name: create-pr
authore: SooYoung
description: "Use create-pr module in github action"
inputs:
  PERSONAL_ACCESS_TOKEN:
    description: "set personal access token"
    required: true
    default: ""
  HEAD:
    description: "set head branch"
    required: true
    default: "release/v0.0.0"
  BASE:
    description: "set base branch"
    required: true
    default: "master"

runs:
  using: "composite"
  steps:
    - name: gh auth login
      run: |
        echo ${{ inputs.PERSONAL_ACCESS_TOKEN }} | gh auth login --with-token
    - name: create branch
      run: |
        git checkout -b release/${{ inputs.HEAD }}
        git push origin release/${{ inputs.head }}
    - name: create pr
      run: |
        gh pr create --base ${{ inputs.BASE }} --head ${{ inputs.HEAD }} --title ${{ inputs.HEAD }} -> ${{ inptus.BASE }}" --body "release pr"
```

9. Github github-actions-module에 Push
