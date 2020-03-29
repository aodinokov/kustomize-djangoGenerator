# Transformer

Transformes are the either the essential part of Kustomize: their main goal is to change the exising documents. Transformers are applied in the sequence given in the kustomize.yaml.

[JinjaTransformer](../overlays/transformer2/transform-dev-node1-bmc-secret-v1-2.yaml) gives a flexibility to modify documents. 

JinjaTransformer document has just 2 fields [jinjaCatalog](../overlays/transformer2/transform-dev-node1-bmc-secret-v1-2.yaml#L6) and [jinjaTemplate](../overlays/transformer2/transform-dev-node1-bmc-secret-v1-2.yaml#L6).

"jinjaCatalog" is used for to access external data: see [Catalog doc](catalog.md) for more info. The renderred catalog is present as yaml in the global variable "catalog".

"jinjaTemplate" contains the template code. 

The standard approach to modify the input resources. The resources passed by kustomize are storead as an array of yaml documents in the global variable "resources" and can be accessed from template like [that](../overlays/transformer2/transform-dev-node1-bmc-secret-v1-2.yaml#L7).

Globals contain a set of functions that allow to simplify transformations

functions.filter_getMatchingIndexes(resource_array, filter) - finds all resources that match the filter and return array of their indexes.

functions.dict_getByPath(resource, path) - returns the value of the resource field. Patch has 2 types of separators: '/' - is used to separate fields name within the same yaml. '|' is used to access yaml values, stored as a string within another yaml, e.g. [here](https://github.com/aodinokov/kustomize-jinjaPlugin/blob/master/overlays/transformer2/secret.yaml#L9) to access the field ssh_authorized_keys it's necessary to use path "stringData/userData|ssh_authorized_keys"

functions.dict_modifyByPath(resource, path, operation) - creates a deepcopy of the resource provided, with modified value by path, depending on operation (can be functions.operation_set, functions.operation_del, functions.operation_merge). it accepts the same path format as functions.dict_getByPath. E.g. [here](https://github.com/aodinokov/kustomize-jinjaPlugin/blob/master/overlays/transformer2/transform-dev-node1-bmc-secret-v1-2.yaml#L14) we're modifying the value of the yaml inside yaml. [Here](https://travis-ci.com/github/aodinokov/kustomize-jinjaPlugin/builds/156182646#L432) is the output of the updated value.

functions.yaml_dump(resource, _all=False) - is a function that outputs the resours in yaml format. If _all is True resource is treated as a resource array.

The full list of functions that are available in jinja templates (for all types of documents: JinjaGenerator, JinjaTransformer and JinjaCatalog) can be found [here](../bin/jinjaPlugin#L198)


