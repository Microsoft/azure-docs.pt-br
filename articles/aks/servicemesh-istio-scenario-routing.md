---
title: Usar Istio para roteamento inteligente
titleSuffix: Azure Kubernetes Service
description: Saiba como usar o Istio para fornecer roteamento inteligente e implantar versões canário em um cluster do AKS (Serviço de Kubernetes do Azure)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d66f3099ba225fbdd2bfc3d54db56ffd8ed2c43f
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684025"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Usar roteamento inteligente e versões canário com Istio no AKS (Serviço de Kubernetes do Azure)

[Istio][istio-github] é uma malha de serviço de software livre que fornece um conjunto de chaves de funcionalidade nos microsserviços em um cluster do Kubernetes. Esses recursos incluem gerenciamento de tráfego, segurança e identidade do serviço, imposição de política e observabilidade. Para obter mais informações sobre o Istio, veja a documentação oficial [O que é Istio?][istio-docs-concepts].

Este artigo mostra como usar a funcionalidade de gerenciamento de tráfego do Istio. Um aplicativo de votação de exemplo do AKS é usado para explorar o roteamento inteligente e as versões canário.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Implantar o aplicativo
> * Atualizar o aplicativo
> * Distribuir uma versão canário do aplicativo
> * Finalizar a distribuição

## <a name="before-you-begin"></a>Antes de começar

> [!NOTE]
> Esse cenário foi testado em relação à versão `1.3.2` do Istio.

As etapas detalhadas neste artigo pressupõem que você criou um cluster AKS (kubernetes `1.13` e superior, com o KUBERNETES RBAC habilitado) e estabeleceu uma `kubectl` conexão com o cluster. Também é necessário ter o Istio instalado no seu cluster.

Se você precisar de ajuda com qualquer um desses itens, veja o [Início rápido do AKS][aks-quickstart] e [Instalar o Istio no AKS][istio-install].

## <a name="about-this-application-scenario"></a>Sobre esse cenário de aplicativo

O aplicativo de votação de exemplo do AKS fornece duas opções de votação (**Gatos** ou **Cachorros**) para os usuários. Há um componente de armazenamento que persiste o número de votos para cada opção. Além disso, há um componente de análise que fornece detalhes sobre os votos convertidos para cada opção.

Neste cenário de aplicativo, você começa implantando a versão `1.0` do aplicativo de votação e a versão `1.0` do componente de análise. O componente de análise fornece contagens simples para o número de votos. O aplicativo de votação e o componente de análise interagem com a versão `1.0` do componente de armazenamento, que tem suporte do Redis.

Atualize o componente de análise para a versão `1.1`, que fornece contagens e, agora, os totais e porcentagens.

Um subconjunto de usuários testa a versão `2.0` do aplicativo por meio de uma versão canário. Essa nova versão usa um componente de armazenamento que tem suporte de um banco de dados MySQL.

Quando você tiver certeza de que a versão `2.0` funciona conforme esperado no seu subconjunto de usuários, implante a versão `2.0` para todos os usuários.

## <a name="deploy-the-application"></a>Implantar o aplicativo

Vamos começar pela implantação do aplicativo no cluster do AKS (Serviço de Kubernetes do Azure). O diagrama a seguir mostra o que é executado no final desta seção: versão `1.0` de todos os componentes com solicitações de entrada atendidas por meio do gateway de entrada do Istio:

![Diagrama que mostra a versão 1,0 de todos os componentes com solicitações de entrada atendidas por meio do gateway de entrada do İSTİO.](media/servicemesh/istio/scenario-routing-components-01.png)

Os artefatos necessários para acompanhar este artigo estão disponíveis no repositório do GitHub [Azure-Samples/aks-voting-app][github-azure-sample]. É possível baixar os artefatos ou clonar o repositório da seguinte maneira:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Altere para a seguinte pasta no repositório clonado / baixado e execute todas as etapas subsequentes nesta pasta:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Primeiro, crie um namespace no cluster do AKS para o aplicativo de votação de exemplo do AKS nomeado `voting` como a seguir:

```console
kubectl create namespace voting
```

Rotule o namespace com `istio-injection=enabled`. Esse rótulo instrui o Istio para injetar automaticamente os istio-proxies como sidecars em todos os pods nesse namespace.

```console
kubectl label namespace voting istio-injection=enabled
```

Agora vamos criar os componentes para o aplicativo de votação do AKS. Crie esses componentes no namespace `voting` criado em uma etapa anterior.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

A saída de exemplo a seguir mostra os recursos que estão sendo criados:

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> O Istio tem alguns requisitos específicos acerca de pods e serviços. Para obter mais informações, veja os [Requisitos dos Istio para a documentação de Pods e Serviços][istio-requirements-pods-and-services].

Para ver os pods que foram criados, use o comando [kubectl get pods][kubectl-get] como a seguir:

```console
kubectl get pods -n voting --show-labels
```

A saída de exemplo a seguir mostra que há três instâncias do pod `voting-app` e uma única instância de pods `voting-analytics` e `voting-storage`. Cada um dos pods tem dois contêineres. Um desses contêineres é o componente, sendo que o outro é o `istio-proxy`:

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Para ver as informações sobre o pod, usaremos o comando [kubectl describe pod][kubectl-describe] com seletores de rótulo para selecionar o pod `voting-analytics`. Filtraremos a saída para mostrar os detalhes dos dois contêineres presentes no pod:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Não é possível se conectar ao aplicativo de votação até que você crie o [Gateway][istio-reference-gateway] e o [Serviço Virtual][istio-reference-virtualservice] do Istio. Esses recursos do Istio roteiam o tráfego do gateway de entrada padrão do Istio para nosso aplicativo.

> [!NOTE]
> Um **Gateway** é um componente na borda da malha de serviço que recebe o tráfego HTTP e TCP de entrada ou saída.
> 
> Um **Serviço Virtual** define um conjunto de regras de roteamento para um ou mais serviços de destino.

Use o comando `kubectl apply` para implantar o gateway e o yaml do Serviço Virtual. Lembre-se de especificar o namespace no qual esses recursos são implantados.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

A saída de exemplo a seguir mostra o novo Gateway e o Serviço Virtual que está sendo criado:

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Obtenha o endereço IP do gateway de entrada do Istio usando o seguinte comando:

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

A saída de exemplo a seguir mostra os endereços IP do gateway de entrada:

```output
20.188.211.19
```

Abra um navegador e cole o endereço IP. O aplicativo de votação de exemplo do AKS é exibido.

![O aplicativo de votação do AKS em execução no Istio habilitou o cluster do AKS.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

As informações na parte inferior da tela mostram que o aplicativo usa a versão `1.0` do `voting-app` e a versão `1.0` do `voting-storage` (Redis).

## <a name="update-the-application"></a>Atualizar o aplicativo

Vamos implantar uma nova versão do componente de análise. Essa nova versão `1.1` exibe totais e porcentagens, além da contagem para cada categoria.

O diagrama a seguir mostra o que será executado ao final desta seção: somente a versão `1.1` do nosso componente `voting-analytics` tem tráfego roteado do componente `voting-app`. Embora a versão `1.0` do nosso componente `voting-analytics` continue a ser executada e seja referenciada pelo serviço `voting-analytics`, os proxies Istio não permitem o tráfego de e para eles.

![O diagrama que mostra apenas a versão 1,1 do componente de análise de votação tem o tráfego roteado do componente de aplicativo de votação.](media/servicemesh/istio/scenario-routing-components-02.png)

Vamos implantar a versão `1.1` do componente `voting-analytics`. Crie este componente no namespace `voting`:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

A saída de exemplo a seguir mostra os recursos que estão sendo criados:

```output
deployment.apps/voting-analytics-1-1 created
```

Abra novamente o aplicativo de votação de exemplo do AKS em um navegador, usando o endereço IP do gateway de entrada do Istio obtido na etapa anterior.

Seu navegador alterna entre os dois modos de exibição mostrados abaixo. Como você está usando um [Serviço][kubernetes-service] do Kubernetes para o componente `voting-analytics` com apenas um único seletor de rótulo (`app: voting-analytics`), o Kubernetes usa o comportamento padrão de round-robin entre os pods que correspondem ao seletor. Nesse caso, ele é tanto a versão `1.0` quanto a versão `1.1` dos seus pods `voting-analytics`.

![A versão 1.0 do componente de análise em execução no nosso aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![A versão 1.1 do componente de análise em execução no nosso aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Você pode ver a alternância entre as duas versões do componente `voting-analytics` como a seguir. Lembre-se de usar o endereço IP do seu próprio gateway de entrada do Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

A saída de exemplo a seguir mostra a parte relevante do site retornada como as alternâncias de site entre as versões:

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Bloquear o tráfego para a versão 1.1 do aplicativo

Agora, vamos bloquear o tráfego somente para a versão `1.1` do componente `voting-analytics` e para a versão `1.0` do componente `voting-storage`. Em seguida, você define regras de roteamento para todos os outros componentes.

> * Um **Serviço Virtual** define um conjunto de regras de roteamento para um ou mais serviços de destino.
> * Uma **Regra de Destino** define políticas de tráfego e políticas específicas de versão.
> * Uma **Política** define quais métodos de autenticação podem ser aceitos em cargas de trabalho.

Use o comando `kubectl apply` para substituir a definição de Serviço Virtual em seu `voting-app` e adicionar [Regras de Destino][istio-reference-destinationrule] e [Serviços Virtuais][istio-reference-virtualservice] aos outros componentes. Você adicionará uma [Política][istio-reference-policy] ao namespace `voting` para garantir que toda a comunicação entre serviços seja protegida usando TLS mútua e certificados do cliente.

* A política tem `peers.mtls.mode` definido como `STRICT` para garantir que a TLS mútua seja imposta entre os serviços dentro do namespace `voting`.
* Também definimos `trafficPolicy.tls.mode` como `ISTIO_MUTUAL` em todas as nossas regras de destino. O Istio fornece serviços com identidades fortes e protege as comunicações entre serviços usando a TLS mútua e certificados do cliente que o Istio gerencia de forma transparente.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

A saída de exemplo a seguir mostra que a nova política, as regras de destino e os serviços virtuais atualizados/criados:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Se você abrir o aplicativo de votação do AKS em um navegador novamente, somente a nova versão `1.1` do componente `voting-analytics` será usada pelo componente `voting-app`.

![A versão 1.1 do componente de análise em execução no nosso aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

É possível ver que agora você só é encaminhado para a versão `1.1` do seu componente `voting-analytics` da maneira a seguir. Lembre-se de usar o endereço IP do seu próprio gateway de entrada do Istio:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

A saída de exemplo a seguir mostra a parte relevante do site retornada:

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Vamos confirmar se o Istio usa TLS mútua para proteger as comunicações entre cada um dos nossos serviços. Para isso, usaremos o comando [authn tls-check][istioctl-authn-tls-check] no binário de cliente `istioctl`, que usa o formulário a seguir.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Esse conjunto de comandos dá informações sobre o acesso aos serviços especificados, de todos os pods que estão em um namespace e correspondem a um conjunto de rótulos:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

Este exemplo de saída a seguir mostra que o TLS mútuo é imposto para cada uma de nossas consultas acima. A saída também mostra as Regras de Política e Destino que impõem o TLS mútuo:

```output
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Distribuir uma versão canário do aplicativo

Agora, vamos implantar uma nova versão `2.0` dos componentes `voting-app`, `voting-analytics` e `voting-storage`. O novo componente `voting-storage` usa o MySQL em vez do Redis, e os componentes `voting-app` e `voting-analytics` são atualizados para permitir que eles usem esse novo componente `voting-storage`.

O componente `voting-app` agora é compatível com a funcionalidade de sinalizador de recurso. Esse sinalizador de recursos permite que você teste a capacidade da versão canário do Istio para um subconjunto de usuários.

O diagrama a seguir mostra o que você terá em execução no final desta seção.

* Versão `1.0` do componente `voting-app`, a versão `1.1` do componente `voting-analytics` e a versão `1.0` do componente `voting-storage` são capazes de se comunicar entre si.
* Versão `2.0` do componente `voting-app`, a versão `2.0` do componente `voting-analytics` e a versão `2.0` do componente `voting-storage` são capazes de se comunicar entre si.
* A versão `2.0` do componente `voting-app` só é acessível aos usuários que tenham um conjunto de sinalizadores de recurso específico. Essa alteração é gerenciada usando um sinalizador de recurso por meio de um cookie.

![Diagrama que mostra o que você terá em execução no final desta seção.](media/servicemesh/istio/scenario-routing-components-03.png)

Primeiro, atualize as regras de destino do Istio e os serviços virtuais para atender a esses novos componentes. Essas atualizações garantem que você não roteie tráfego incorretamente para os novos componentes e os usuários não obtenham acesso inesperado:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

A saída de exemplo a seguir mostra as regras de destino e os serviços virtuais sendo atualizados:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Em seguida, vamos adicionar os objetos do Kubernetes aos componentes da nova versão `2.0`. Também é possível atualizar o serviço `voting-storage` para incluir a porta `3306` para MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

A saída de exemplo a seguir mostra que os objetos do Kubernetes foram criados ou atualizados com êxito:

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Aguarde até que todos os pods da versão `2.0` estejam em execução. Use o comando[kubectl get pods][kubectl-get] com a opção `-w` trocada para inspeção para ver as alterações em todos os pods no namespace `voting`:

```console
kubectl get pods --namespace voting -w
```

Agora você deve conseguir alternar entre a versão `1.0` e a versão `2.0` (canário) do aplicativo de votação. A alternância de sinalizador de recurso na parte inferior da tela define um cookie. Esse cookie é usado pelo serviço virtual `voting-app` para rotear usuários para a nova versão `2.0`.

![A versão 1.0 do aplicativo de votação do AKS - sinalizador de recurso NÃO ESTÁ definida.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![A versão 2.0 do aplicativo de votação do AKS - sinalizador de recurso ESTÁ definida.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

As contagens de voto são diferentes entre as versões do aplicativo. Essa diferença destaca que você está usando dois back-ends de armazenamento diferentes.

## <a name="finalize-the-rollout"></a>Finalizar a distribuição

Após testar com êxito a versão canário, atualize o serviço virtual `voting-app` para rotear todo o tráfego para a versão `2.0` do componente `voting-app`. Em seguida, todos os usuários podem ver a versão `2.0` do aplicativo, independentemente de o sinalizador de recurso estar definido ou não:

![Diagrama que mostra que os usuários veem a versão 2,0 do aplicativo, independentemente de o sinalizador de recurso estar ou não definido.](media/servicemesh/istio/scenario-routing-components-04.png)

Atualize todas as regras de destino para remover as versões dos componentes que não quer mais ativas. Em seguida, atualize todos os serviços virtuais para parar de fazer referência a essas versões.

Como não há mais nenhum tráfego para qualquer uma das versões mais antigas dos componentes, agora é possível excluir com segurança todas as implantações para esses componentes.

![Os componentes e roteamento do aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-components-05.png)

Você agora distribuiu com êxito uma nova versão do aplicativo de votação do AKS.

## <a name="clean-up"></a>Limpar 

É possível remover o aplicativo de votação do AKS que usamos neste cenário do cluster do AKS excluindo o namespace `voting` da seguinte maneira:

```console
kubectl delete namespace voting
```

A saída de exemplo a seguir mostra que todos os componentes do aplicativo de votação do AKS foram removidos do cluster do AKS.

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>Próximas etapas

Você pode explorar cenários adicionais usando o [Exemplo do aplicativo Bookinfo do Istio][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.mesh.v1alpha1/#AuthenticationPolicy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./servicemesh-istio-install.md
