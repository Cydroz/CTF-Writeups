We're given a `credentials.json` file for this task. But first, we need to install Google Cloud CLI (google install instructions for this; on windows it's a simple installer file).

After this is some additional setup with the gcloud CLI:
- First we run a CLI shell and `cd` into where our credentials file is.
- Then we use the `credentials.json` file to authenticate like so: `gcloud auth login --cred-file .\credentials.json`
- Then define the default project (this is given in the json file as well but gcloud CLI informs us to do this anyway): `gcloud config set project needle-in-iam`

Now true to form, the problem mentions that running `gcloud iam roles describe ComputeOperator --project=needle-in-iam` returns an error, and it does. But we can instead list all roles and then `grep` (or `Select-string` in pwsh) to locate our flag

`gcloud iam roles list --project=needle-in-iam | sls DUCTF -Context 3`

Result:
```
  stage: GA
  title: Comprehend Operator
  ---
> description: DUCTF{D3scr1be_L1ST_Wh4ts_th3_d1fference_FDyIMbnDmX}
  etag: BwYDv1xX9Y4=
  name: projects/needle-in-iam/roles/ComputeOperator
  stage: GA

```

Flag: `DUCTF{D3scr1be_L1ST_Wh4ts_th3_d1fference_FDyIMbnDmX}`
