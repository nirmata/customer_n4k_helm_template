# Nirmata Kyverno Operator Setup

This guide provides step-by-step instructions to set up the Nirmata Kyverno Operator in your Kubernetes environment.

## Prerequisites

- Kubernetes cluster up and running.
- `kubectl` installed and configured.
- Access to the Docker registry `docker-repo.nibr.novartis.net`.

## Steps

### 1. Encode Docker Registry Credentials

To securely store your Docker registry credentials, you need to encode them using `base64`.

1. Replace `registry_username` and `registry_password` with your actual Docker registry credentials.
   
   ```bash
   echo -n 'registry_username:registry_password' | base64
   ```

2. The output will be a base64-encoded string. Replace the encoded value `dmlrYXNoa2F1c2hpazAxOmdocF9RRTlCRGhGTEhOU2RkSFQ3UWpTZThnclB4bzRicw==` in the template below with the output from the previous step.

   ```json
   {"auths": {"docker-repo.nibr.novartis.net": {"auth": "dmlrYXNoa2F1c2hpazAxOmdocF9RRTlCRGhGTEhOU2RkSFQ3UWpTZThnclB4bzRicw=="}}}
   ```

3. Encode the JSON object again:

   ```bash
   echo -n '{"auths": {"docker-repo.nibr.novartis.net": {"auth": "your_encoded_credentials_here"}}}' | base64
   ```

4. Copy the output and save it in a text editor like Notepad++.

### 2. Clone the Repository

Clone the Nirmata customer Helm template repository:

```bash
git clone https://github.com/nirmata/customer_n4k_helm_template.git
cd customer_n4k_helm_template
```

### 3. Create the Nirmata Namespace

Create a namespace for Nirmata:

```bash
kubectl create ns nirmata-system
```

### 4. Deploy the CRD for the Nirmata Kyverno Operator

Navigate to the `crd-nirmata-kyverno-operator` folder and deploy the Custom Resource Definition (CRD):

```bash
cd crd-nirmata-kyverno-operator
kubectl create -f crd-n4k-template.yaml
```

### 5. Deploy the Nirmata Kyverno Operator

1. Go to the `nirmata-kyverno-operator` directory:

   ```bash
   cd ../nirmata-kyverno-operator/
   ```

2. Edit the `n4k-operator-main-template.yaml` file:

   - Find the line `105`.
   - Replace the `.dockerconfigjson` value with the base64-encoded value generated in step 1.

3. Save the file after making the changes.

4. Deploy the operator using:

   ```bash
   kubectl create -f n4k-operator-main-template.yaml
   ```

### 6. Verify the Deployment

1. Check the status of the pods in the `nirmata-system` namespace:

   ```bash
   kubectl get po -n nirmata-system
   ```

2. Once the operator pod is running in the `nirmata-system` namespace, verify the pods in the `kyverno` namespace:

   ```bash
   kubectl get po -n kyverno
   ```

## Troubleshooting

- Ensure that all the namespaces and deployments are correctly configured.
- Double-check the credentials and ensure that they are correctly encoded.


