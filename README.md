# K8s manifests for the Grott Growatt proxy

Kubernetest manifests for the [Grott](https://github.com/johanmeijer/grott) proxy for the [Growatt solar panel inverters](https://www.ginverter.com/products/sph-3000-6000tl-bl-up) by [johnmeijer](https://github.com/johanmeijer/grott)

```text
┌────────────────────────────────────────────┐
│                                            │
│          Solar Panels                      │
│                                            │
└────────────────────────────────────────────┘






                                                                                                  ┌────────────────────────┐
         ┌─────────────────────┐                                                                  │                        │
         │                     │                                                                  │                        │
         │                     │                                                                  │                        │
         │                     │                                                                  │                        │
         │                     │                                                                  │                        │
         │                     │                                                                  │                        │
         │     Growatt Inverter│                                                                  │    Growatt servers     │
         │                     │                                                                  │                        │
         │                     │                                                                  │                        │
         │                     │                                                                  │                        │
         │                     │                                                                  │                        │
         │                     │                                                                  │                        │
         │                     │                                                                  │                        │
         │                     │                                                                  │                        │
         │                     │                                                                  │                        │
         │                     │                                                                  │                        │
         │                     │                                                                  │                        │
         └───────────┬─────────┘                                                                  └────────────────────────┘
                     │                              ┌─────────────────────────┐                                 ▲
                     │                              │                         │                                 │
                     │                              │                         │                                 │
                     │                              │                         │                                 │
                     │                              │                         │                                 │
                     │                              │                         │                                 │
                     │                              │     grott running on k8s│                                 │
                     │                              │                         │                                 │
                     │                              │                         │                                 │
                     │                              │                         │                                 │
                     └─────────────────────────────►│                         ├─────────────────────────────────┘
                                                    │                         │
                                                    │                         │
                                                    │                         │
                                                    │                         │
                                                    │                         │
                                                    │                         │
                                                    │                         │
                                                    │                         │
                                                    └─────────────────────────┘
```

## Building and deploying

```bash
# deploy application stack
(ENVIRONMENT="production"
    kubectl kustomize "deploy/kubernetes/${ENVIRONMENT}" | \
    kubectl apply -f -)

```

### Generate your own Grott.ini

`grott.ini` contains various things you might want to keep secret. I've embedded my own in here but used [`mozilla sops`](https://github.com/getsops/sops) to keep it secret.

```bash
# decrypting
sops --decrypt deploy/kubernetes/base/grott-configmap.enc.yaml --output > deploy/kubernetes/base/grott-configmap.yaml

## encrypting
sops --encrypt deploy/kubernetes/base/grott-configmap.yaml > deploy/kubernetes/base/grott-configmap.enc.yaml

```

To generate your own configmap.ini from your `grott.ini` then simply run this command:

`kubectl create configmap grott-ini --from-file=grott.ini --output="yaml" --dry-run=client >deploy/kubernetes/base/grott-configmap.yaml`
