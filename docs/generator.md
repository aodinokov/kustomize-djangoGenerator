# Generator

[example]:(../overlays/crypt/)

generators are the essential part of kustomize. it's a code that creates new resources. Basically there are only 2 ways to add new documents to resource list - add them directly to resources, or use generators.

Moreover despite on the name Generators may be used to modify resources: please refer to [this page](https://github.com/kubernetes-sigs/kustomize/tree/master/docs/plugins#generator-options) and pay attention to 

**`kustomize.config.k8s.io/behavior`**

The values "merge" and "replace" can be used to partially or completely modify the previously existing resource.

It's very convenient to use Jinja in generator, because generating documents is the main purpose of template engines.

The exmpample has the [following](https://github.com/aodinokov/kustomize-jinjaPlugin/blob/master/overlays/crypt/testJinjaGenerator.yaml) generator

In the current version generator must have 2 fields:

**`jinjaCatalog`**

and

**`jinjaTemplate`**

Any arbitrary yaml file can be used as jinjaCatalog. There is aspecial case - when this file has kind==JinjaCatalog - in that case the file may be rendered and that adds a lot of flexibility.
Basically what is happening is that the resulting content of jinjaCatalog file is put to the jinja global variable catalog and this valiable can be used during resource rendering.

Kustomize allows generator to reture several resources as this example demonstrates.

What we can see in our exmpamle is that the first thing our generator is returning is the whole catalog:

**`{{ functions.yaml_dump(catalog) }}`**

[here](https://travis-ci.com/github/aodinokov/kustomize-jinjaPlugin/jobs/306891246#L221) you can see how our renderred catalog looks like.
We can avoid dumping the whole catalog by removing the line.

The line:

**`password: "{{ catalog.data.user }}"`**

renders password based on what we have in catalog. [Here](https://travis-ci.com/github/aodinokov/kustomize-jinjaPlugin/jobs/306891246#L193) is the resulting yaml with renderred data.

Please note that only one of the config maps in the output has hashed suffix in the output. That is the result of adding the annotation to that ConfigMap:

**`kustomize.config.k8s.io/needs-hash: "true"`**

This is a good way to control cases where we need hashed suffix.


# Crypted data

The catalog may have a fields with encrypted data: e.g.: [here](https://github.com/aodinokov/kustomize-jinjaPlugin/blob/master/overlays/crypt/jinjacatalog.yaml#L10)

Jinja template may decript it if ENV variable "JINJA_CRYPT_PASSWORD" is provided. There are 2 functions: [crypt_encrypt](https://github.com/aodinokov/kustomize-jinjaPlugin/blob/master/bin/jinjaPlugin#L82) and [crypt_decrypt](https://github.com/aodinokov/kustomize-jinjaPlugin/blob/master/bin/jinjaPlugin#L87) that use this value as a password. The generator will return error and kustomize build will not finish if the incorrect password is provided.
These functions can be used either during catalog renderring or during renderring of the resource. In the example above we used the first approach. [Here](https://travis-ci.com/github/aodinokov/kustomize-jinjaPlugin/jobs/306891246#L226) is the decrypted value.


There is one more potentially helpful function [vault_getClient](https://github.com/aodinokov/kustomize-jinjaPlugin/blob/master/bin/jinjaPlugin#L96) that requires 2 env variables "VAULT_ADDR" and "VAULT_TOKEN". This function returns the client that can be used to access vault parameters directly from jinja templates.
