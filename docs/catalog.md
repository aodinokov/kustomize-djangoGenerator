# Catalog

[example]:(overlays/catalogs)

Catalogs is the feature, incroduced in JinjaPlugin to solve one simple issue: Generators can't get access to existing resources. If we don't have catalog feature it leads us to duplication of code where we repeat the way how we access data. 

Catalog yamls are used in jinjaCatalog fields along with arbitrary yamls. If the document kind is JinjaCatalog, then the certain yaml structure is expected.

JinjaCatalog document may have the following fields: "parents", "jinjaTemplate" and "data".

[Parents](https://github.com/aodinokov/kustomize-jinjaPlugin/blob/master/overlays/catalogs/jinjacatalog.yaml#L5) and [jinjaTemplate](https://github.com/aodinokov/kustomize-jinjaPlugin/blob/master/overlays/catalogs/jinjacatalog.yaml#L13) fields are used if it's necessaary to render the catalog, e.g. like in this [example](https://github.com/aodinokov/kustomize-jinjaPlugin/blob/master/overlays/catalogs/jinjacatalog.yaml) we're building catalog from several sources by merging them: ../crypt/jinjacatalog.yaml and arbitrary.yaml

[Here](https://travis-ci.com/github/aodinokov/kustomize-jinjaPlugin/jobs/306891246#L243)
it's possible to find the final values that appeared in the catalog.

Note: you will probably want to keep Catalogs in kustomize and don't feed it to kubectl. Please consider annotation

**`config.kubernetes.io/local-config: "true"`**

for that cases. For more info please refer [this](https://github.com/kubernetes-sigs/kustomize/blob/master/cmd/config/docs/api-conventions/config-io.md) document.
