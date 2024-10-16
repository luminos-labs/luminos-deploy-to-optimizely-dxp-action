# Deploy to Optimizely DXP Action

This GitHub Action allows you to easily deploy your site to the Optimizely Digital Experience Platform (DXP). With just a few simple steps, you can automate the deployment process and ensure that your changes are quickly and accurately reflected on your Optimizely DXP instance.

## Usage

See [action.yml](action.yml)

To use this action, simply add the following step to your GitHub Actions workflow:

```yaml
- name: Deploy to Optimizely DXP
    uses: luminoslabs/luminos-deploy-to-optimizely-dxp-action@v1
    with:
        # The uploaded artifact name. 
        artifact-name: $YOUR_ARTIFACT_NAME
        # Naming accepted by opti <app name>.<package type>.app.<version>.nupkg.
        artifact-file-name: $YOUR_ARTIFACT_FILE_NAME
        #  Optimizely Project Id.
        optimizely-project-id: $YOUR_OPTIMIZELY_PROJECT_ID
        # Optimizely API Key.
        optimizely-api-key: $YOUR_OPTIMIZELY_API_KEY
        # Optimizely API Secret.
        optimizely-api-secret: $YOUR_OPTIMIZELY_API_SECRET
        # Environment to deploy (optional, default: 'Integration').
        optimizely-environment: $YOUR_OPTIMIZELY_ENVIRONMENT
        # Enable direct deploy in DXP (optional, default: 'true').
        optimizely-direct-deploy: $YOUR_OPTIMIZELY_DIRECT_DEPLOY
```

Make sure to replace the following variables with the appropriate values for your deployment. For getting the Optimizely credentials consult [this](https://docs.developers.optimizely.com/digital-experience-platform/docs/authentication) page.

The artifact name should respect naming convention. The package type can be `cms` or `commerce`.

## Example

Here's an example workflow that demonstrates how to use this action:

```yaml
name: Deploy to Optimizely DXP

on:
    push:
        branches:
            - main

jobs:
    deploy:
        runs-on: windows-latest

        steps:
            - name: Checkout code
              uses: actions/checkout@v2
            - name: Build
              run: msbuild ...
            - name: Archive wwwroot folder
              run: |
                Compress-Archive -Path "${{ github.workspace }}\publish\wwwroot" -DestinationPath "${{ github.workspace }}\publish\appname.cms.app.1.${{ env.   run_number }}.nupkg" -Force
              shell: pwsh
            - name: Upload artifact for deployment job
              uses: actions/upload-artifact@v4
              with:
                name: app-artifact
                path: ${{ github.workspace }}\publish\appname.cms.app.1.${{ env.run_number }}.nupkg

            - name: Deploy to Optimizely DXP
                uses: luminoslabs/luminos-deploy-to-optimizely-dxp-action@v1
                with:
                    artifact-name: app-artifact
                    artifact-file-name: appname.cms.app.1.${{ env.run_number }}.nupkg
                    optimizely-project-id: ${{ secrets.OPTI_PROJECT_ID }}
                    optimizely-api-key: ${{ secrets.OPTI_API_KEY }}
                    optimizely-api-secret: ${{ secrets.OPTI_API_SECRET }}
                    optimizely-environment: ${{ secrets.OPTI_TARGET_ENVIRONMENT }}
                    optimizely-direct-deploy: 'false'

```

## Documentation

- [Deploy DXP](https://docs.developers.optimizely.com/digital-experience-platform/docs/deploying)
- [Authenticate with Deployment API](https://docs.developers.optimizely.com/digital-experience-platform/docs/authentication)
