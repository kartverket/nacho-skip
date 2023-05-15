# Nacho SKIP

<p align="center">
<img src="https://i.imgur.com/6oIR0fz.png" alt="Nacho SKIP Logo"  width="20%">
<br/>
</p>

<hr/>

A composite GitHub Action for authenticating with GCP@Kartverket and SKIP while using cosign for image signing and verification. As of the current version cosign is used only to sign and verify an image, but in the future there will hopefully be ways of attesting and attaching SBOMs to the image using the SLSA framework.

### Inputs

| Key                                 | Required | Description                                                                                                                                                                                                                                                                                                           |
| ----------------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| auth_project_number                 | x        | The GCP Project Number used for authentication. A 12-digit number used as a unique identifier for the project. Used to find workload identity pool.                                                                                                                                                                   |
| service_account                     | x        | The GCP service account connected to the identity pool that will be used to authenticate with GCP.                                                                                                                                                                                                                    |
| image_name                          | x        | Image name of the image to sign. In the format \<FOLDER>/\<NAME>, where \<FOLDER> is optional. Example: `kartverket/skip`                                                                                                                                                                                             |
| image_digest                        | x        | Image digest of the image to sign. Looks like `sha256:a428de44a9059f31a59237a5881c2d2cffa93757d99026156e4ea544577ab7f3`.                                                                                                                                                                                              |
| image_registry                      |          | Registry in which the image is created. Defaults to `ghcr.io`, GitHubs registry.                                                                                                                                                                                                                                      |
| workload_identity_provider_override |          | The ID of the provider to use for authentication. Only used for overriding the default workload identity provider based on project number. It should be in the format of `projects/{{project}}/locations/global/workloadIdentityPools/{{workload_identity_pool_id}}/providers/{{workload_identity_pool_provider_id}}` |

### Example usage

Using the action is very simple, and may be added as a separate job in your workflow, or as a step in an existing job.

The job which runs the action must have the following permissions:
`

- `id-token: write`
- `packages: write`
- `contents: read`

```yaml
nacho-skip:
  name: Run Nacho-Skip for Provenance
  runs-on: ubuntu-latest
  needs: build
  permissions:
    id-token: write
    packages: write
    contents: read
  steps:
    - name: "Run Nacho SKIP"
      uses: kartverket/nacho-skip@main
      with:
        service_account: application-deploy@application-environment-1111.iam.gserviceaccount.com
        auth_project_number: 012345678987
        image_name: ${{ github.repository }}
        image_digest: ${{ $IMAGE_DIGEST }}
```

Here, the `$IMAGE_DIGEST` variable would typically come from the output of a previous build step or job.
