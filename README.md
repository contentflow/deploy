# Contentflow / deploy

`deploy` is a small pull-based deployment daemon following some of the principles AWS CodeDeploy is using. The deployment artifact is roughly compatible between the two systems while some attributes are ignored. Also there is no central API containing information about deployments: The latest deployment is derived from the update time of the artifacts found in the respective storage provider.

## Configuration

```console
$ deploy --help
Usage of deploy:
  -c, --fetch-cron string   When to query for new deployments (cron syntax) (default "* * * * *")
  -i, --identifier string   Software identifier to query deployments for (default "default")
      --log-level string    Log level (debug, info, warn, error, fatal) (default "info")
  -s, --storage string      URI for the storage provider to use
      --version             Prints current version and exits
```

Basically there are three important CLI parameters to be set:

- `fetch-cron` - Interval when to search for new deployment artifacts  
  ([cron syntax](https://linux.die.net/man/5/crontab) without named patterns like `@hourly`)
- `identifier` - Identifier to distinguish between different deployments published to the same location
- `storage` - The most important one to configure where to look for deployment artifacts

If not specified otherwise below the artifacts in the respective locations are expected to have the following format:

```
<identifier><deployment-id>.zip
```

So the software identifier `default` combined with the deployment-id `xyz123` would be named `defaultxyz123.zip`.

### Storage provider: Google Cloud Storage

Storage URI format: `gs://<bucket>/<prefix>` (Example: `gs://my-bucket/path/inside` which would load `path/inside/defaultxyz123.zip` file from bucket `my-bucket` in above mentioned example)

Authentication for GCS is done through the [Application Default Credentials (ADC)](https://cloud.google.com/docs/authentication/production) either through an account.json file specified in `GOOGLE_APPLICATION_CREDENTIALS` environment variable or through the instance serviceaccount when running on GCE.

### Storage provider: Local

_This provider mainly is meant for testing and debugging purposes!_

Storage URI format: `file://<path>` (Example: `file:///tmp/deploy` which would load `/tmp/deploy/defaultxyz123.zip` file in above mentioned example)
