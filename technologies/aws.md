![AWS Logo](/uploads/logos/aws-logo.png "AWS Logo"){.pagelogo}
<!-- TITLE: AWS -->
<!-- SUBTITLE: A quick summary of AWS -->

# Overview
AWS is a public cloud provder, with an extensive set of resources available on a pay per use (subscription) price model. A public cloud makes it easy to get into new technology, because you can start small and grow as load on your application/service grows.


## VPC
Virtual Private Cloud (VPC) unique describes the hosted provision of a given customer environment; it's a virtual data centre. Each customer account can have upto five VPC (default; can be increased) and every customer's VPC is unique to that customer, thus providing the separation of each customer's environment.

Further information on [VPC](/technologies/aws/vpc) can be found [here](/technologies/aws/vpc).

## Lambda
### Lambda Layers

On the surface, layers look like being able to share code between lambda. For example, common logging and notification. But it also allows large complex deployments which might be close to breaching the 50MB package limit, to be broken down. A function (lambda) can have up to five layers; total deployment size for a function is up to 250MB.

A layer is referenced by ARN; in that way it can be thought of as a library. The function uses IAM roles to grant access to that layer. Layers are installed into the /opt directory (whereas the function remains in the /tmp directory).

Importantly, layers are versioned. The ARN includes the version. On pulishing a layer, the version is automatically increased. For a function to utilise the new version, it (the function) has to be updated to reference the new ARN.

When publishing a layer, you declare the platform runtime version (e.g. node v8, node v6); you can list multiple platform versions. A function can only consume those layers have the a compatible platform runtime version.

It sounds to me as though layers is an extension of lambda's existing capability to deploy using an S3 bucket. As layer is simply a bucket object. I get this feeling because when listing layers, there is a signed URL allowing download within ten minutes.