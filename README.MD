Continuous Deployment (CD) configuration template to deploy <span style="background:yellow; color:black">[PROJECT](https://gitlab.com/<PROJECT_PATH>) </span> to a Kubernetes cluster.

For Continous Integration (CI) configuration, go to the <span style="background:yellow; color:black">[project repository](https://gitlab.com/<PROJECT_PATH>) </span>.

# Configuration

⚠️ This template does not compile out of the box, there are a number of steps that need to be completed for this to work. ⚠️ 

## Setup checklist

* [ ] Edit this README to include links to the project it deploys. Look for <span style="background:yellow; color:black">highlighted fields</span>.
* [ ] Configure environments.
    * [ ] Base environment.
        * Search for `<REQUIRED>` in all files and replace with the necessary information.
        * Add project specific configurations.
    * [ ] Staging.
      * Search for `<REQUIRED>` in all files and replace with the necessary information.
      * Add project specific configurations.
    * [ ] Production.
      * Search for `<REQUIRED>` in all files and replace with the necessary information.
      * Add project specific configurations.
* [ ] Initialize target deployment clusters. Use [this script](https://gitlab.com/ignitionrobotics/continuous-deployment/kubernetes).
* [ ] Register clusters in repository.
  * [ ] Tag Gitlab runners.
    * Tag all Gitlab runners with `<PROJECT_NAME>`.
    * For each environment, tag at least one Gitlab runner with the environment name: `staging`, `production`, etc.

Once this checklist is complete, erase this section.

## Environments

Two environments are provided by default. These environments are called `staging` and `production`. The `staging` environment contains a configuration that mirrors the `production` configuration, but is deployed on isolated infrastructure to allow testing before deploying to `production` for public access.

[Kustomize](https://kustomize.io/) is used to define environment configuration declaratively.

Base configuration used by all environments can be found in the `base` directory. 

Configuration for each environment can be found in the `overlays` directory.

## Deployment workflow

Each deployment should be tied to a new version or configuration change.

**Steps required to start a deployment process:**

1. Create a new branch starting from `master`.
    * For new releases, use semantic versioning branch names `release/<major>.<minor>.<patch>`. Note the lack of a leading `v`.
2. Apply the changes you need for a certain environment:
- Bump version up by changing an image tag, for example: `project:1.0.0` to `project:1.1.0`.
- Change resource limits on deployments: CPU and Memory.
- Apply a new config: Update a config map, a cloudsim `config.yaml` file, or any specific config inside this repository.
3. Create a merge request for the new branch.
    * Title suggestion for releases `Release <major>.<minor>.<patch>`
4. Merge requests are able to deploy changes into the `staging` environment.
    * In order to deploy to `staging`, use the `Play` (▷) button in the Merge Request page.
5. Test your deployment.
6. Get at least one Merge Request approval.
7. Merge the MR once it has been approved.
8. Once merged, the new configuration will be deployed to the `production` environment immediately.
    * If the deployment fails, you can always redeploy by running the `production` job for the `main` branch pipeline.

# Building

Because configurations are split into base configurations and sets of patches, the Kustomize `build` command needs to disable load restrictions in order to be able to load files.

```bash
kustomize build --load-restrictor=LoadRestrictionsNone <DIR>
```

# Deploying

It is recommended to pipe Kustomize output directly into `kubectl apply -f -` instead of using `apply -k`, as `kubectl` CLIs tend to use older Kustomize versions that typically lead to errors.

```bash
kustomize build --load-restrictor=LoadRestrictionsNone <DIR> | kubectl apply -f -
```

Resources have been tested to work with Kustomize 4.4.0.

## Troubleshooting

Please consider referring to our [TROUBLESHOOTING](TROUBLESHOOTING.md) document for any questions and issues you might have during the deployment process.