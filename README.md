# My CRD example for metadata problem

To reproduce the issue just do the following:

```bash
kubectl apply -f config/crd/bases/demo.my.crd_mycrds.yaml
kubectl apply -f config/samples/demo_v1_mycrd.yaml
kubectl describe mycrd mycrd-sample
```

The output should be this:

```
Name:         mycrd-sample
Namespace:    everest-workloads
Labels:       <none>
Annotations:  <none>
API Version:  demo.my.crd/v1
Kind:         MyCRD
Metadata:
  Creation Timestamp:  2022-01-25T14:09:48Z
  Generation:          1
  Managed Fields:
    API Version:  demo.my.crd/v1
    Fields Type:  FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          .:
          f:kubectl.kubernetes.io/last-applied-configuration:
      f:spec:
        .:
        f:metadata:
    Manager:         kubectl-client-side-apply
    Operation:       Update
    Time:            2022-01-25T14:09:48Z
  Resource Version:  68926
  UID:               fbb77a14-b4b2-4aa3-b27b-c1a290da13a4
Spec:
  Metadata:
Events:  <none>
```

As we can see, the Spec's metadata is empty.

If we want to investigate what the fields are defined within the API, we can execute the following:

```bash
kubectl explain mycrd --recursive=true
```

The output should be:

```log
KIND:     MyCRD
VERSION:  demo.my.crd/v1

DESCRIPTION:
     MyCRD is the Schema for the mycrds API

FIELDS:
   apiVersion	<string>
   kind	<string>
   metadata	<Object>
      annotations	<map[string]string>
      clusterName	<string>
      creationTimestamp	<string>
      deletionGracePeriodSeconds	<integer>
      deletionTimestamp	<string>
      finalizers	<[]string>
      generateName	<string>
      generation	<integer>
      labels	<map[string]string>
      managedFields	<[]Object>
         apiVersion	<string>
         fieldsType	<string>
         fieldsV1	<map[string]>
         manager	<string>
         operation	<string>
         subresource	<string>
         time	<string>
      name	<string>
      namespace	<string>
      ownerReferences	<[]Object>
         apiVersion	<string>
         blockOwnerDeletion	<boolean>
         controller	<boolean>
         kind	<string>
         name	<string>
         uid	<string>
      resourceVersion	<string>
      selfLink	<string>
      uid	<string>
   spec	<Object>
      metadata	<map[string]>
   status	<map[string]>
```

As we can see, in the root, metadata is defined as `<Object>` and within Spec it's defined as `map[string]` which is an invalid type, since we would need a type for the value of the key-value pair.
