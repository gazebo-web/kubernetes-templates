# Troubleshooting

## FAQs

### How do I define the version to deploy?

Change the `image` tag inside the `base/kustomization.yaml` file:

```yaml
images:
  - name: registry.gitlab.com/ignitionrobotics/web/cloudsim
    newTag: '2.8.0'
```