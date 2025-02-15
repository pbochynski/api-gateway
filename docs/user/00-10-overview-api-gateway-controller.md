# APIGateway Controller

## Overview

APIGateway Controller is part of Kyma API Gateway Operator. Its role is to manage the installation of [Ory Oathkeeper](https://www.ory.sh/docs/oathkeeper) and handle the configuration of Kyma Gateway and the resources defined by the [APIGateway custom resource (CR)](./custom-resources/apigateway/04-00-apigateway-custom-resource.md). The controller is responsible for:
- Installing, upgrading, and uninstalling Ory Oathkeeper
- Configuring Kyma Gateway
- Managing Certificates and DNS entries
- Configuring Istio Gateways

## APIGateway CR

The `apigateways.operator.kyma-project.io` CustomResourceDefinition (CRD) describes the APIGateway CR that is used to manage the API Gateway resources. To learn more, read the [APIGateway CR documentation](./custom-resources/apigateway/04-00-apigateway-custom-resource.md).

## Status Codes

|     Code     | Description                              |
|:------------:|:-----------------------------------------|
|   `Ready`    | Controller finished reconciliation.      |
| `Processing` | Controller is reconciling resources.     |
|  `Deleting`  | Controller is deleting resources.        |
|   `Error`    | An error occurred during reconciliation. |
|  `Warning`   | Controller is misconfigured.             |

## Labeling Resources

In accordance with the decision [Consistent Labeling of Kyma Modules](https://github.com/kyma-project/community/issues/864), the APIGateway Operator resources use the standard Kubernetes labels:


```yaml
kyma-project.io/module: api-gateway
app.kubernetes.io/name: api-gateway-operator
app.kubernetes.io/instance: api-gateway-operator-default
app.kubernetes.io/version: "x.x.x"
app.kubernetes.io/component: operator
app.kubernetes.io/part-of: api-gateway
```

All other resources, such as the external `ory-oathkeeper` component and its respective resources, use only the Kyma module label:

```yaml
kyma-project.io/module: api-gateway
```

Run this command to get all resources created by the API Gateway module:

```bash
kubectl get all|<resources-kind> -A -l kyma-project.io/module=api-gateway
```
