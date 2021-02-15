---
title: Instalar Istio no AKS (Serviço de Kubernetes do Azure)
description: Saiba como instalar e usar o Istio para criar uma malha de serviço em um cluster do AKS (Serviço de Kubernetes do Azure)
author: paulbouwer
ms.topic: article
ms.date: 10/02/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: c42e9c31397e9313898d7029366bc8de169d368e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683821"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalar e usar o Istio no AKS (Serviço de Kubernetes do Azure)

[Istio][istio-github] é uma malha de serviço de software livre que fornece um conjunto de chaves de funcionalidade nos microsserviços em um cluster do Kubernetes. Esses recursos incluem gerenciamento de tráfego, segurança e identidade do serviço, imposição de política e observabilidade. Para obter mais informações sobre o Istio, veja a documentação oficial [O que é Istio?][istio-docs-concepts].

Este artigo mostra como instalar o Istio. O `istioctl` binário do cliente İSTİO é instalado no computador cliente e os componentes do İSTİO são instalados em um cluster kubernetes no AKs.

> [!NOTE]
> As instruções a seguir fazem referência à versão do İSTİO `1.7.3` .
>
> As `1.7.x` versões İSTİO foram testadas pela equipe do İSTİO em relação à versão kubernetes `1.16+` . Você pode encontrar versões adicionais do İSTİO em [versões do GitHub-İSTİO][istio-github-releases], informações sobre cada uma das versões em [İSTİO News][istio-release-notes] e versões de kubernetes com suporte em [perguntas frequentes][istio-faq]sobre o İSTİO.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Baixar e instalar o binário do cliente İSTİO istioctl
> * Instalar o İSTİO no AKS
> * Validar a instalação do İSTİO
> * Acessar os Complementos
> * Desinstalar o İSTİO do AKS

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste artigo pressupõem que você criou um cluster AKS (kubernetes `1.16` e superior, com o KUBERNETES RBAC habilitado) e estabeleceu uma `kubectl` conexão com o cluster. Se você precisar de ajuda com qualquer um desses itens, consulte o [Início rápido do AKS][aks-quickstart].

Verifique se você leu a documentação de [desempenho e escalabilidade do İSTİO](https://istio.io/docs/concepts/performance-and-scalability/) para entender os requisitos de recursos adicionais para executar o İSTİO em seu cluster AKs. Os requisitos de núcleo e memória variam de acordo com sua carga de trabalho específica. Escolha um número apropriado de nós e o tamanho da VM para atender à sua configuração.

Este artigo separa as diretrizes de instalação do Istio em várias etapas discretas. O resultado final é o mesmo na estrutura das [diretrizes][istio-install-istioctl] de instalação do Istio oficial.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-operator-on-aks"></a>Instalar o operador İSTİO em AKS

O İSTİO fornece um [operador][istio-install-operator] para gerenciar a instalação e as atualizações dos componentes do İSTİO no cluster do AKS. Instalaremos o operador İSTİO usando o `istioctl` binário do cliente.

```bash
istioctl operator init
```

Você deve ver algo como a saída a seguir para confirmar se o operador İSTİO foi instalado.

```console
Using operator Deployment image: docker.io/istio/operator:1.7.3
✔ Istio operator installed
✔ Installation complete
```

O operador İSTİO é instalado no `istio-operator` namespace. Consulte o namespace.

```bash
kubectl get all -n istio-operator
```

Você deve ver os seguintes componentes implantados.

```console
NAME                                  READY   STATUS    RESTARTS   AGE
pod/istio-operator-6d7958b7bf-wxgdc   1/1     Running   0          2m43s

NAME                     TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/istio-operator   ClusterIP   10.0.8.57    <none>        8383/TCP   2m43s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-operator   1/1     1            1           2m43s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-operator-6d7958b7bf   1         1         1       2m43s
```

Você pode aprender mais sobre o padrão de operador e como ele pode ajudar a automatizar tarefas complexas por meio do [kubernetes.Io][kubernetes-operator].


### <a name="install-istio-components"></a>Instalar componentes do İSTİO

Agora que instalamos com êxito o operador İSTİO em nosso cluster AKS, é hora de instalar os componentes do İSTİO. 

Aproveitaremos o `default` [perfil de configuração do İSTİO][istio-configuration-profiles] para criar a especificação do [operador İSTİO][istio-control-plane].

Você pode executar o comando a seguir `istioctl` para exibir a configuração do `default` perfil de configuração do İSTİO.

```bash
istioctl profile dump default
```

> [!NOTE]
> No momento, o İSTİO deve ser agendado para ser executado em nós do Linux. Se você tiver nós do Windows Server em seu cluster, deverá garantir que os pods İSTİO sejam agendados apenas para execução em nós do Linux. Usaremos [seletores de nó][kubernetes-node-selectors] para garantir que os pods estejam agendados para os nós corretos.

> [!CAUTION]
> Os recursos do [ISTIO CNI][istio-feature-cni] İSTİO estão atualmente em [Alpha][istio-feature-stages], portanto, é preciso considerar o que deve ser dado antes de habilitá-los. 

Crie um arquivo chamado `istio.aks.yaml` com o conteúdo a seguir. Esse arquivo manterá a [especificação do operador İSTİO][istio-control-plane] para configurar o İSTİO.

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: istio-control-plane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  # Enable the addons that we will want to use
  addonComponents:
    grafana:
      enabled: true
    prometheus:
      enabled: true
    tracing:
      enabled: true
    kiali:
      enabled: true
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
    kiali:
      dashboard:
        auth:
          strategy: anonymous 
```

Crie o `istio-system` namespace e implante a especificação do operador İSTİO para esse namespace. O operador İSTİO inspecionará a especificação do operador İSTİO e a usará para instalar e configurar o İSTİO no cluster do AKS.

```bash
kubectl create ns istio-system

kubectl apply -f istio.aks.yaml 
```

Neste ponto, você implantou o İSTİO em seu cluster AKS. Para garantir que tenhamos uma implantação bem-sucedida do İSTİO, vamos passar para a próxima seção para [validar a instalação do İSTİO](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Validar a instalação do İSTİO

Consulte o `istio-system` namespace, onde os componentes İSTİO e complemento foram instalados pelo operador İSTİO:

```bash
kubectl get all -n istio-system
```

Você deve ver os seguintes componentes:

- `istio*` -os componentes do İSTİO
- `jaeger-*``tracing` `zipkin` complemento, e-Tracing
- `prometheus` -complemento de métricas
- `grafana` -complemento de painel de análise e monitoramento
- `kiali` -complemento de painel de malha de serviço

```console
NAME                                        READY   STATUS    RESTARTS   AGE
pod/grafana-7cf9794c74-mpfbp                1/1     Running   0          5m53s
pod/istio-ingressgateway-86b5dbdcb9-ndrp5   1/1     Running   0          5m57s
pod/istio-tracing-c98f4b8fc-zqklg           1/1     Running   0          82s
pod/istiod-6965c56995-4ph9h                 1/1     Running   0          6m15s
pod/kiali-7b44985d68-p87zh                  1/1     Running   0          81s
pod/prometheus-6868989549-5ghzz             1/1     Running   0          81s

NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                                      AGE
service/grafana                     ClusterIP      10.0.226.39    <none>         3000/TCP                                                     5m54s
service/istio-ingressgateway        LoadBalancer   10.0.143.56    20.53.72.254   15021:32166/TCP,80:31684/TCP,443:31302/TCP,15443:30863/TCP   5m57s
service/istiod                      ClusterIP      10.0.211.228   <none>         15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                6m16s
service/jaeger-agent                ClusterIP      None           <none>         5775/UDP,6831/UDP,6832/UDP                                   82s
service/jaeger-collector            ClusterIP      10.0.7.62      <none>         14267/TCP,14268/TCP,14250/TCP                                82s
service/jaeger-collector-headless   ClusterIP      None           <none>         14250/TCP                                                    82s
service/jaeger-query                ClusterIP      10.0.52.172    <none>         16686/TCP                                                    82s
service/kiali                       ClusterIP      10.0.71.179    <none>         20001/TCP                                                    82s
service/prometheus                  ClusterIP      10.0.171.151   <none>         9090/TCP                                                     82s
service/tracing                     ClusterIP      10.0.195.137   <none>         80/TCP                                                       82s
service/zipkin                      ClusterIP      10.0.136.111   <none>         9411/TCP                                                     82s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                1/1     1            1           5m54s
deployment.apps/istio-ingressgateway   1/1     1            1           5m58s
deployment.apps/istio-tracing          1/1     1            1           83s
deployment.apps/istiod                 1/1     1            1           6m16s
deployment.apps/kiali                  1/1     1            1           83s
deployment.apps/prometheus             1/1     1            1           82s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7cf9794c74                1         1         1       5m54s
replicaset.apps/istio-ingressgateway-86b5dbdcb9   1         1         1       5m58s
replicaset.apps/istio-tracing-c98f4b8fc           1         1         1       83s
replicaset.apps/istiod-6965c56995                 1         1         1       6m16s
replicaset.apps/kiali-7b44985d68                  1         1         1       82s
replicaset.apps/prometheus-6868989549             1         1         1       82s

NAME                                                       REFERENCE                         TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   7%/80%    1         5         1          5m57s
horizontalpodautoscaler.autoscaling/istiod                 Deployment/istiod                 1%/80%    1         5         1          6m16s
```

Você também pode obter informações adicionais sobre a instalação observando os logs do operador İSTİO.

```bash
kubectl logs -n istio-operator -l name=istio-operator -f
```

Se `istio-ingressgateway` mostrar um ip externo de `<pending>`, aguarde alguns minutos até que um endereço IP seja atribuído pela rede do Azure.

Todos os pods devem mostrar um status de `Running` . Se os pods não tiverem esses status, aguarde um minuto ou dois até que tenham. Se algum pods reportar um problema, use o comando [kubectl describe pod][kubectl-describe] para revisar a saída e o status.

## <a name="accessing-the-add-ons"></a>Acessar os complementos

Um número de Complementos foi instalado pelo operador İSTİO que fornece funcionalidade adicional. Os aplicativos Web para os complementos **não** são expostos publicamente por meio de um endereço IP externo. 

Para acessar as interfaces de usuário do complemento, use o `istioctl dashboard` comando. Esse comando usa [kubectl Port-Forward][kubectl-port-forward] e uma porta aleatória para criar uma conexão segura entre o computador cliente e o Pod relevante em seu cluster AKs. Em seguida, ele abrirá automaticamente o aplicativo Web complementar no navegador padrão.

### <a name="grafana"></a>Grafana

Os painéis de análise e monitoramento para İSTİO são fornecidos pelo [Grafana][grafana]. Lembre-se de usar as credenciais criadas por meio do segredo Grafana anteriormente quando solicitado. Abra o painel do Grafana com segurança da seguinte maneira:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

As métricas para İSTİO são fornecidas pelo [Prometheus][prometheus]. Abra o painel do Prometheus com segurança da seguinte maneira:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

O rastreamento dentro de İSTİO é fornecido pelo [Jaeger][jaeger]. Abra o painel do Jaeger com segurança da seguinte maneira:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

Um painel de observabilidade de malha de serviço é fornecido por [Kiali][kiali]. Lembre-se de usar as credenciais criadas por meio do segredo Kiali anteriormente quando solicitado. Abra o painel do Kiali com segurança da seguinte maneira:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Uma interface simples para os proxies [Envoy][envoy] está disponível. Ele fornece informações de configuração e métricas para um proxy Envoy em execução em um pod especificado. Abra a interface Envoy com segurança da seguinte maneira:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Desinstalar o İSTİO do AKS

> [!WARNING]
> Excluir İSTİO de um sistema em execução pode resultar em problemas relacionados ao tráfego entre seus serviços. Verifique se você fez provisões para que o seu sistema ainda opere corretamente sem İSTİO antes de continuar.

### <a name="remove-istio"></a>Remover İSTİO

Para remover o İSTİO do cluster do AKS, exclua o `IstioOperator` recurso nomeado `istio-control-plane` que adicionamos anteriormente. O operador İSTİO reconhecerá que a especificação do operador İSTİO foi removida e, em seguida, excluirá todos os componentes İSTİO associados.

```bash
kubectl delete istiooperator istio-control-plane -n istio-system
```

Você pode executar o seguinte para verificar quando todos os componentes do İSTİO foram excluídos.

```bash
kubectl get all -n istio-system
```

### <a name="remove-istio-operator"></a>Remover operador İSTİO

Depois que o İSTİO tiver sido desinstalado com êxito, você também poderá remover o operador İSTİO.

```bash
istioctl operator remove
```

E, finalmente, remova os `istio-` namespaces.

```bash
kubectl delete ns istio-system
kubectl delete ns istio-operator
```

## <a name="next-steps"></a>Próximas etapas

Para explorar mais opções de instalação e configuração para o İSTİO, consulte as seguintes diretrizes oficiais de İSTİO:

- [İSTİO-guias de instalação][istio-installation-guides]

Você também pode seguir cenários adicionais usando:

- [Exemplo de aplicativo İSTİO BookInfo][istio-bookinfo-example]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-install-operator]: https://istio.io/latest/docs/setup/install/operator/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[kubernetes-operator]: https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
