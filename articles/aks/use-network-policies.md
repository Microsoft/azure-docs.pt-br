---
title: Proteger os pods com políticas de rede no Serviço de Kubernetes do Azure (AKS)
description: Saiba como proteger o tráfego que flui para dentro e fora de pods usando diretivas de rede do Kubernetes no serviço de Kubernetes do Azure (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/08/2019
ms.author: iainfou
ms.openlocfilehash: 29180d6c1bb5f0991a4f33c3b7c9418f84d8260c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61027959"
---
# <a name="preview---secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Visualização - proteger o tráfego entre os pods usando diretivas de rede no serviço de Kubernetes do Azure (AKS)

Ao executar aplicativos modernos baseados em microsserviços no Kubernetes, muitas vezes você deseja controlar quais componentes podem se comunicar uns com os outros. O princípio de privilégio mínimo deve ser aplicado a como o tráfego pode fluir entre os pods em um cluster do serviço de Kubernetes do Azure (AKS). Digamos que você provavelmente deseja bloquear o tráfego diretamente para os aplicativos de back-end. O *política de rede* recurso no Kubernetes permite que você defina regras para tráfego de entrada e saída entre os pods em um cluster.

Este artigo mostra como instalar o mecanismo de políticas de rede e criar políticas de rede do Kubernetes para controlar o fluxo de tráfego entre os pods no AKS. Esse recurso está atualmente na visualização.

> [!IMPORTANT]
> Recursos de visualização do AKS são Self-service e aceitação. As visualizações são fornecidas para reunir opiniões e bugs de nossa comunidade. No entanto, eles não são suportados pelo suporte técnico do Azure. Se você cria um cluster ou adicionar esses recursos para clusters existentes, há suporte para esse cluster até que o recurso não está mais em visualização e muda para GA (disponibilidade geral).
>
> Se você encontrar problemas com recursos de visualização [abra um problema no repositório GitHub do AKS] [ aks-github] com o nome do recurso de visualização no título do bug.

## <a name="before-you-begin"></a>Antes de começar

Você precisa da CLI do Azure versão 2.0.61 ou posterior instalado e configurado. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

Para criar um cluster do AKS que pode usar a diretiva de rede, primeiro habilite um sinalizador de recursos em sua assinatura. Para registrar o sinalizador de recurso *EnableNetworkPolicy*, use o comando [az feature register][az-feature-register] como mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

Demora alguns minutos para o status exibir *Registrado*. Você pode verificar o status de registro usando o [lista de recursos az] [ az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do *containerservice* provedor de recursos usando o [registro de provedor az] [ az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-network-policy"></a>Visão geral da política de rede

Todos os compartimentos em um cluster AKS podem enviar e receber tráfego sem limitações, por padrão. Para melhorar a segurança, você pode definir regras que controlam o fluxo de tráfego. Aplicativos de back-end geralmente só são expostos para os serviços front-end necessários, por exemplo. Ou, os componentes de banco de dados só são acessíveis para as camadas de aplicativo que se conectam a eles.

Política de rede é uma especificação de Kubernetes que define as políticas de acesso para a comunicação entre os Pods. Usando diretivas de rede, você definir um conjunto ordenado de regras para enviar e receber o tráfego e aplicá-las a uma coleção de pods que correspondem a um ou mais seletores de rótulo.

Essas regras de política de rede são definidas como se manifesta YAML. Políticas de rede podem ser incluídas como parte de um manifesto mais amplo que também cria uma implantação ou serviço.

### <a name="network-policy-options-in-aks"></a>Opções de política de rede no AKS

O Azure fornece duas maneiras de implementar a diretiva de rede. Você escolher uma opção de diretiva de rede quando você cria um cluster do AKS. A opção de política não pode ser alterada depois que o cluster é criado:

* Implementação do Azure, chamada *diretivas de rede do Azure*.
* *Políticas de rede Malhado*, uma rede de código-fonte aberto e uma solução de segurança de rede fundada por [Tigera][tigera].

Ambas as implementações de usam o Linux *IPTables* para impor as políticas especificadas. As políticas são convertidas em conjuntos de pares IP permitidos e não permitidos. Esses pares são programados, em seguida, como regras de filtro de IPTable.

Política de rede funciona somente com a opção CNI do Azure (Avançado). Implementação é diferente para as duas opções:

* *Políticas de rede do Azure* -o CNI do Azure configura uma ponte de host de VM para a rede de dentro do próprio nó. As regras de filtragem são aplicadas quando os pacotes passam por meio da ponte.
* *Políticas de rede Malhado* -o CNI do Azure configura as rotas de kernel local para o tráfego de dentro do próprio nó. As políticas são aplicadas na interface de rede do pod.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Diferenças entre as políticas do Azure e Malhado e seus recursos

| Recurso                               | Azure                      | Malhado                      |
|------------------------------------------|----------------------------|-----------------------------|
| Plataformas com suporte                      | Linux                      | Linux                       |
| Suporte para as opções de rede             | Azure CNI                  | Azure CNI                   |
| Conformidade com a especificação do Kubernetes | Todos os tipos de política com suporte |  Todos os tipos de política com suporte |
| Recursos adicionais                      | Nenhum                       | Estendido consistindo de ponto de extremidade do Host, rede Global definida e a política de rede Global de modelo de política. Para obter mais informações sobre como usar o `calicoctl` CLI para gerenciar esses recursos, consulte [referência de usuário calicoctl][calicoctl]. |
| Suporte                                  | Compatível com a equipe de engenharia e suporte do Azure | Suporte da comunidade de Malhado. Para obter mais informações sobre o suporte pago adicional, consulte [opções de suporte do projeto Malhado][calico-support]. |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Cria um cluster do AKS e habilita a política de rede

Para ver as políticas de rede em ação, vamos criar e, em seguida, expanda em uma política que define o fluxo de tráfego:

* Nega todo o tráfego ao pod.
* Permite o tráfego com base nos rótulos do pod.
* Permite o tráfego com base no namespace.

Primeiro, vamos criar um cluster do AKS que dá suporte à política de rede. O recurso de política de rede pode ser habilitado apenas quando o cluster é criado. Não é possível habilitar a política de rede em um cluster AKS existente.

Para usar a diretiva de rede com um cluster do AKS, você deve usar o [plug-in do Azure CNI] [ azure-cni] e definir sua própria rede virtual e sub-redes. Para saber mais sobre como planejar os intervalos de sub-rede necessários, consulte [Configurar a rede avançada][use-advanced-networking].

O exemplo de script a seguir:

* Cria uma rede virtual e uma sub-rede.
* Cria um Azure Active Directory (Azure AD) entidade de serviço para uso com o cluster do AKS.
* Atribui permissões de *Colaborador* para a entidade de serviço do cluster do AKS em uma rede virtual.
* Cria um cluster do AKS na rede virtual definida e habilita a política de rede.
    * O *azure* opção de política de rede é usada. Para usar Malhado como a opção de política de rede em vez disso, use o `--network-policy calico` parâmetro.

Forneça sua própria *SP_PASSWORD* segura. Você pode substituir a *nome_do_grupo_de_recursos* e *CLUSTER_NAME* variáveis:

```azurecli-interactive
SP_PASSWORD=mySecurePassword
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP_ID=$(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.6 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

São necessários alguns minutos para criar o cluster. Quando o cluster estiver pronto, configure `kubectl` para se conectar ao cluster Kubernetes usando o [az aks get-credentials] [ az-aks-get-credentials] comando. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Negar todo o tráfego de entrada para um pod

Antes de definir as regras para permitir o tráfego de rede específico, primeiro crie uma política para negar todo o tráfego de rede. Essa política fornece um ponto de partida para começar a lista de permissões somente o tráfego desejado. Você também pode ver claramente que o tráfego é removido quando a política de rede é aplicada.

Para o ambiente de aplicativo de exemplo e regras de tráfego, primeiro vamos criar um namespace chamado *desenvolvimento* para executar os pods de exemplo:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Crie um pod de back-end de exemplo que executa o NGINX. Este pod de back-end pode ser usado para simular um aplicativo baseado na web do exemplo back-end. Crie este pod no namespace *development* e abra a porta *80* para servir o tráfego da Web. Rotule o pod com *app=webapp,role=backend* para que possamos alcançá-lo com uma política de rede na próxima seção:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Crie o pod de outro e anexe uma sessão de terminal para testar o que você pode acessar com êxito a página da Web do NGINX padrão:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

No prompt de shell, use `wget` para confirmar que você pode acessar a página da Web do NGINX padrão:

```console
wget -qO- http://backend
```

A saída de exemplo a seguir mostra que a página da Web do NGINX padrão retornado:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão de terminal anexada. O pod de teste é excluído automaticamente.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Criar e aplicar uma política de rede

Agora que você confirmou que você pode usar a página da Web NGINX básica no pod de back-end de exemplo, crie uma política para negar todo o tráfego de rede. Crie um arquivo chamado `backend-policy.yaml` e cole o manifesto YAML a seguir. Esse manifesto usa um *podSelector* anexar a política para compartimentos que têm o *app:webapp, função: back-end* rótulo, como seu pod NGINX de exemplo. Nenhuma regra é definida em *ingress*, portanto, todo o tráfego de entrada para o pod é negado:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Aplicar a política de rede usando o [aplicar kubectl] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testar a política de rede


Vamos ver se você pode usar a página da Web do NGINX no pod de back-end novamente. Crie outro pod de teste e anexe uma sessão de terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

No prompt de shell, use `wget` para ver se você pode acessar a página da Web do NGINX padrão. Desta vez, defina um valor de tempo limite em *2* segundos. A política de rede agora bloqueia todo o tráfego de entrada, portanto, não é possível carregar a página, conforme mostrado no exemplo a seguir:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Saia da sessão de terminal anexada. O pod de teste é excluído automaticamente.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Permitir tráfego de entrada com base em um rótulo de pod

Na seção anterior, um pod NGINX de back-end foi agendado, e uma política de rede foi criada para negar todo o tráfego. Vamos criar um pod de front-end e atualizar a política de rede para permitir o tráfego dos pods de front-end.

Atualize a política de rede para permitir o tráfego dos pods com os rótulos *app:webapp,role:frontend* e em qualquer namespace. Editar anterior *back-end policy.yaml* arquivo e adicione *matchLabels* regras de entrada para que o manifesto se parece com o exemplo a seguir:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Esta política de rede usa *namespaceSelector* e um elemento *podSelector* para a regra de entrada. A sintaxe YAML é importante para as regras de entrada ser aditivos. Neste exemplo, os dois elementos devem corresponder à regra de entrada a ser aplicada. As versões do Kubernetes anteriores ao *1.12* não pode interpretar esses elementos corretamente e restringir o tráfego de rede, conforme o esperado. Para obter mais informações sobre esse comportamento, consulte [comportamento de e para seletores][policy-rules].

Aplicar a política de rede atualizado usando o [aplicar kubectl] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Agendar um pod que é rotulado como *app = webapp, função = front-end* e anexar a uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

No prompt de shell, use `wget` para ver se você pode acessar a página da Web do NGINX padrão:

```console
wget -qO- http://backend
```

Porque a regra de entrada permite que o tráfego com compartimentos que têm rótulos *app: aplicativo Web, função: front-end*, o tráfego do pod de front-end é permitido. A saída de exemplo a seguir mostra a página da Web NGINX padrão retornada:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão de terminal anexada. O pod é excluído automaticamente.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testar um pod sem um rótulo correspondente

A política de rede permite o tráfego dos pods rotulados com *app:webapp,role:frontend*, mas deve negar todos os outros tráfegos. Vamos testar para ver se outro vagem sem esses rótulos pode acessar o pod NGINX de back-end. Crie outro pod de teste e anexe uma sessão de terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

No prompt de shell, use `wget` para ver se você pode acessar a página da Web do NGINX padrão. A política de rede bloqueia o tráfego de entrada, portanto, não é possível carregar a página, conforme mostrado no exemplo a seguir:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Saia da sessão de terminal anexada. O pod de teste é excluído automaticamente.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Permitir o tráfego somente dentro de um namespace definido

Nos exemplos anteriores, você criou uma política de rede que negado todo o tráfego e, em seguida, a diretiva para permitir o tráfego dos pods com um rótulo específico foi atualizada. Outra necessidade comum é limitar o tráfego para dentro de um namespace específico. Se os exemplos anteriores foram para o tráfego em uma *desenvolvimento* namespace, crie uma política de rede que impede que o tráfego de outro namespace, tal como *produção*, atinjam os pods.

Primeiro crie um novo namespace para simular um namespace de produção:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Agende um pod de teste no namespace *production* que esteja rotulado como *app=webapp,role=frontend*. Anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

No prompt de shell, use `wget` para confirmar que você pode acessar a página da Web do NGINX padrão:

```console
wget -qO- http://backend.development
```

Como os rótulos para o pod corresponderem no momento, o que é permitido na política de rede, o tráfego é permitido. A política de rede não examina os namespaces, somente os rótulos de pod. A saída de exemplo a seguir mostra a página da Web NGINX padrão retornada:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão de terminal anexada. O pod de teste é excluído automaticamente.

```console
exit
```

### <a name="update-the-network-policy"></a>Atualizar a política de rede

Vamos atualizar a regra de ingresso *namespaceSelector* seção para permitir apenas o tráfego de dentro de *desenvolvimento* namespace. Edite o arquivo de manifesto *backend-policy.yaml*, como mostrado no exemplo a seguir:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

Nos exemplos mais complexos, você pode definir várias regras de entrada, como um *namespaceSelector* e, em seguida, um *podSelector*.

Aplicar a política de rede atualizado usando o [aplicar kubectl] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testar a política de rede atualizada

Agendar outra pod na *produção* namespace e anexar a uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

No prompt de shell, use `wget` para ver que a política de rede agora nega o tráfego:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Saia do pod de teste:

```console
exit
```

Com o tráfego negado da *produção* namespace, a agenda de um pod de teste de volta a *desenvolvimento* namespace e anexar a uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

No prompt de shell, use `wget` para ver a política de rede permite o tráfego:

```console
wget -qO- http://backend
```

O tráfego é permitido porque o pod está agendado no namespace que corresponde ao que é permitido na política de rede. A saída de exemplo a seguir mostra a página da Web NGINX padrão retornada:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão de terminal anexada. O pod de teste é excluído automaticamente.

```console
exit
```

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, criamos dois namespaces e aplicou uma política de rede. Para limpar esses recursos, use o [excluir kubectl] [ kubectl-delete] de comando e especifique os nomes de recursos:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre recursos de rede, consulte [conceitos para aplicativos no serviço de Kubernetes do Azure (AKS) de rede][concepts-network].

Para saber mais sobre as políticas, consulte [políticas de rede do Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues]
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.5/reference/calicoctl/
[calico-support]: https://www.projectcalico.org/support

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
