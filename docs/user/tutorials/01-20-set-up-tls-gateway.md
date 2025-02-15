# Set Up a TLS Gateway

This tutorial shows how to set up a TLS Gateway in both manual and simple modes. It also explains how to configure authentication for an mTLS Gateway based on certificate details.

## Prerequisites

* Deploy [a sample HTTPBin Service](./01-00-create-workload.md).
* Set up [your custom domain](./01-10-setup-custom-domain-for-workload.md) and export the following values as environment variables:

  ```bash
  export DOMAIN_TO_EXPOSE_WORKLOADS={DOMAIN_NAME}
  export GATEWAY=$NAMESPACE/httpbin-gateway
  ```
   

## Set Up a TLS Gateway in Simple Mode

To create a TLS Gateway in simple mode, run:

  ```bash
  cat <<EOF | kubectl apply -f -
  ---
  apiVersion: networking.istio.io/v1alpha3
  kind: Gateway
  metadata:
    name: httpbin-gateway
    namespace: $NAMESPACE
  spec:
    selector:
      istio: ingressgateway # Use Istio Ingress Gateway as default
    servers:
      - port:
          number: 443
          name: https
          protocol: HTTPS
        tls:
          mode: SIMPLE
          credentialName: $TLS_SECRET
        hosts:
          - "*.$DOMAIN_TO_EXPOSE_WORKLOADS"
  EOF        
  ```
    
## Set Up a TLS Gateway in Mutual Mode
  
1. Create a mutual TLS Gateway. Run:
    
    ```bash
    cat <<EOF | kubectl apply -f -
    ---
    apiVersion: networking.istio.io/v1beta1
    kind: Gateway
    metadata:
      name: ${MTLS_GATEWAY_NAME}
      namespace: ${NAMESPACE}
    spec:
      selector:
        istio: ingressgateway
        app: istio-ingressgateway
      servers:
        - port:
            number: 443
            name: https
            protocol: HTTPS
          tls:
            mode: MUTUAL
            credentialName: ${TLS_SECRET}
          hosts:
            - '*.${DOMAIN_TO_EXPOSE_WORKLOADS}'
        - port:
            number: 80
            name: http
            protocol: HTTP
          tls:
            httpsRedirect: true
          hosts:
            - '*.${DOMAIN_TO_EXPOSE_WORKLOADS}'
    EOF
    ```
2. Export the following value as an environment variable:

    ```bash
    export CLIENT_ROOT_CA_CRT_ENCODED=$(cat ${CLIENT_ROOT_CA_CRT_FILE}| base64)
    ```

3. Add client root CA to the CA cert bundle Secret for mTLS Gateway. Run:

    ```bash
    cat <<EOF | kubectl apply -f -
    ---
    apiVersion: v1
    kind: Secret
    metadata:
      name: ${TLS_SECRET}-cacert
      namespace: istio-system
    type: Opaque
    data:
      cacert: ${CLIENT_ROOT_CA_CRT_ENCODED}
    EOF
    ```