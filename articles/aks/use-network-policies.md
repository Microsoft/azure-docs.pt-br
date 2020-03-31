---
title: Proteger os pods com políticas de rede no Serviço de Kubernetes do Azure (AKS)
description: Saiba como proteger o tráfego que flui dentro e fora dos pods usando as políticas de rede kubernetes no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 37b6ebd1c8b147db0a9cead4678a0b2bb4ed234d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473601"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Proteger o tráfego entre os pods usando as políticas de rede no Serviço de Kubernetes do Azure (AKS)

Ao executar aplicativos modernos baseados em microsserviços no Kubernetes, muitas vezes você deseja controlar quais componentes podem se comunicar uns com os outros. O princípio do menor privilégio deve ser aplicado à forma como o tráfego pode fluir entre pods em um cluster Azure Kubernetes Service (AKS). Digamos que você provavelmente queira bloquear o tráfego diretamente para aplicativos back-end. O recurso *Política de rede* no Kubernetes permite definir regras para o tráfego de ingestão e saída entre pods em um cluster.

Este artigo mostra como instalar o mecanismo de política de rede e criar políticas de rede Kubernetes para controlar o fluxo de tráfego entre pods em AKS. A política de rede só deve ser usada para nós e pods baseados em Linux em AKS.

## <a name="before-you-begin"></a>Antes de começar

Você precisa da versão 2.0.61 do Azure CLI ou posteriormente instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

> [!TIP]
> Se você usou o recurso de diretiva de rede durante a visualização, recomendamos que você [crie um novo cluster](#create-an-aks-cluster-and-enable-network-policy).
> 
> Se você deseja continuar usando clusters de teste existentes que usaram a política de rede durante a visualização, atualize seu cluster para uma nova versão do Kubernetes para a versão ga mais recente e, em seguida, implante o manifesto YAML a seguir para corrigir o servidor de métricas de falha e kubernetes Painel. Esta correção só é necessária para clusters que usaram o mecanismo de diretiva de rede Calico.
>
> Como uma prática recomendada de segurança, [revise o conteúdo deste manifesto YAML][calico-aks-cleanup] para entender o que é implantado no cluster AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Visão geral da política de rede

Todos os pods em um cluster AKS podem enviar e receber tráfego sem limitações, por padrão. Para melhorar a segurança, você pode definir regras que controlam o fluxo de tráfego. Os aplicativos back-end são frequentemente expostos apenas a serviços front-end obrigatórios, por exemplo. Ou, os componentes do banco de dados são acessíveis apenas aos níveis de aplicativos que se conectam a eles.

A Diretiva de Rede é uma especificação kubernetes que define políticas de acesso para comunicação entre Pods. Usando políticas de rede, você define um conjunto ordenado de regras para enviar e receber tráfego e aplicá-las a uma coleção de pods que correspondem a um ou mais seletores de rótulos.

Essas regras de diretiva de rede são definidas como manifestações YAML. As políticas de rede podem ser incluídas como parte de um manifesto mais amplo que também cria uma implantação ou serviço.

### <a name="network-policy-options-in-aks"></a>Opções de diretiva de rede em AKS

O Azure fornece duas maneiras de implementar a política de rede. Você escolhe uma opção de diretiva de rede ao criar um cluster AKS. A opção de diretiva não pode ser alterada após a criação do cluster:

* A própria implementação do Azure, chamada *Azure Network Policies*.
* *Calico Network Policies*, uma solução de segurança de rede e rede de código aberto fundada pela [Tigera][tigera].

Ambas as implementações usam *IPTables Linux* para aplicar as políticas especificadas. As políticas são traduzidas em conjuntos de pares IP permitidos e proibidos. Esses pares são então programados como regras de filtro IPTable.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Diferenças entre as políticas do Azure e da Calico e suas capacidades

| Recurso                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Plataformas compatíveis                      | Linux                      | Linux                       |
| Opções de rede suportadas             | Azure CNI                  | Azure CNI e kubenet       |
| Conformidade com a especificação kubernetes | Todos os tipos de políticas suportadas |  Todos os tipos de políticas suportadas |
| Recursos adicionais                      | Nenhum                       | Modelo de política estendida composto por Política de Rede Global, Conjunto de Rede Global e Ponto Final do Host. Para obter mais `calicoctl` informações sobre o uso da CLI para gerenciar esses recursos estendidos, consulte [a referência do usuário calicoctl][calicoctl]. |
| Suporte                                  | Apoiado pela equipe de suporte e Engenharia do Azure | Apoio da comunidade de Calico. Para obter mais informações sobre suporte pago adicional, consulte [as opções de suporte do Project Calico][calico-support]. |
| Registrando em log                                  | As regras adicionadas / excluídas no IPTables são registradas em cada host em */var/log/azure-npm.log* | Para obter mais informações, consulte [os registros de componentes da Calico][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Cria um cluster do AKS e habilita a política de rede

Para ver as políticas de rede em ação, vamos criar e expandir em uma política que define o fluxo de tráfego:

* Nega todo o tráfego ao pod.
* Permite o tráfego com base nos rótulos do pod.
* Permite o tráfego com base no namespace.

Primeiro, vamos criar um cluster AKS que suporte a política de rede. 

> [!IMPORTANT]
>
> O recurso de diretiva de rede só pode ser ativado quando o cluster é criado. Não é possível habilitar a política de rede em um cluster AKS existente.

Para usar a Política de Rede do Azure, você deve usar o [plug-in do Azure CNI][azure-cni] e definir sua própria rede virtual e sub-redes. Para saber mais sobre como planejar os intervalos de sub-rede necessários, consulte [Configurar a rede avançada][use-advanced-networking]. A Política de Rede Calico poderia ser usada com este mesmo plug-in da CNI azure ou com o plug-in da Kubenet CNI.

O exemplo de script a seguir:

* Cria uma rede virtual e uma sub-rede.
* Cria um diretor de serviço do Azure Active Directory (Azure AD) para uso com o cluster AKS.
* Atribui permissões de *Colaborador* para a entidade de serviço do cluster do AKS em uma rede virtual.
* Cria um cluster AKS na rede virtual definida e habilita a política de rede.
    * A opção de diretiva de rede *azure* é usada. Para usar o Calico como opção `--network-policy calico` de política de rede, use o parâmetro. Nota: Calico pode `--network-plugin azure` ser `--network-plugin kubenet`usado com qualquer um ou .

Forneça sua própria *SP_PASSWORD* segura. Você pode substituir as variáveis *RESOURCE_GROUP_NAME* e *CLUSTER_NAME:*

```azurecli-interactive
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
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

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

São necessários alguns minutos para criar o cluster. Quando o cluster estiver `kubectl` pronto, configure-se para conectar-se ao cluster Kubernetes usando o comando [az aks get-credentials.][az-aks-get-credentials] Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Negar todo o tráfego de entrada para um pod

Antes de definir as regras para permitir o tráfego de rede específico, primeiro crie uma política para negar todo o tráfego de rede. Esta política lhe dá um ponto de partida para começar a listar apenas o tráfego desejado. Você também pode ver claramente que o tráfego é removido quando a política de rede é aplicada.

Para o ambiente de aplicativos de exemplo e as regras de tráfego, vamos primeiro criar um namespace chamado *desenvolvimento* para executar os pods de exemplo:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Crie um exemplo de back-end pod que executa o NGINX. Este pod back-end pode ser usado para simular um aplicativo baseado na Web de back-end de amostra. Crie este pod no namespace *development* e abra a porta *80* para servir o tráfego da Web. Rotule o pod com *app=webapp,role=backend* para que possamos alcançá-lo com uma política de rede na próxima seção:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Crie outro pod e anexe uma sessão de terminal para testar que você pode alcançar com sucesso a página padrão do NGINX:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

No prompt shell, `wget` use para confirmar se você pode acessar a página web padrão do NGINX:

```console
wget -qO- http://backend
```

A seguinte saída de amostra mostra que a página padrão do NGINX retornou:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão de terminal anexada. A cápsula de teste é automaticamente excluída.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Criar e aplicar uma política de rede

Agora que você confirmou, você pode usar a página básica do NGINX no pod back-end da amostra, crie uma diretiva de rede para negar todo o tráfego. Crie um arquivo chamado `backend-policy.yaml` e cole o manifesto YAML a seguir. Este manifesto usa um *podSelector* para anexar a diretiva a pods que têm o *aplicativo:webapp,role:backend* label, como o pod NGINX de exemplo. Nenhuma regra é definida em *ingress*, portanto, todo o tráfego de entrada para o pod é negado:

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

Vá [https://shell.azure.com](https://shell.azure.com) abrir o Azure Cloud Shell em seu navegador.

Aplique a diretiva de rede usando o comando [kubectl apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testar a política de rede

Vamos ver se você pode usar a página da web NGINX no pod back-end novamente. Crie outro pod de teste e anexe uma sessão de terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

No prompt shell, `wget` use para ver se você pode acessar a página padrão nginx. Desta vez, defina um valor de tempo limite em *2* segundos. A diretiva de rede bloqueia agora todo o tráfego de entrada, de modo que a página não pode ser carregada, como mostrado no exemplo a seguir:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Saia da sessão de terminal anexada. A cápsula de teste é automaticamente excluída.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Permitir tráfego de entrada com base em um rótulo de pod

Na seção anterior, um pod NGINX back-end foi programado, e uma política de rede foi criada para negar todo o tráfego. Vamos criar um pod front-end e atualizar a diretiva de rede para permitir o tráfego a partir de pods front-end.

Atualize a política de rede para permitir o tráfego dos pods com os rótulos *app:webapp,role:frontend* e em qualquer namespace. Edite o arquivo *anterior backend-policy.yaml* e adicione as regras de entrada *de matchLabels* para que seu manifesto pareça o seguinte exemplo:

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
> Esta política de rede usa *namespaceSelector* e um elemento *podSelector* para a regra de entrada. A sintaxe YAML é importante para que as regras de ingestão sejam aditivas. Neste exemplo, os dois elementos devem corresponder à regra de entrada a ser aplicada. As versões do Kubernetes anteriores ao *1.12* podem não interpretar esses elementos corretamente e restringir o tráfego de rede como você espera. Para obter mais informações sobre esse comportamento, consulte [Comportamento de e para os seletores][policy-rules].

Aplique a diretiva de rede atualizada usando o comando [kubectl apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

Agende um pod que é rotulado como *app=webapp,role=frontend* e anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

No prompt shell, `wget` use para ver se você pode acessar a página web padrão nginx:

```console
wget -qO- http://backend
```

Como a regra de entrada permite o tráfego com pods que possuem o aplicativo de *etiquetas: webapp,role: frontend*, o tráfego do pod front-end é permitido. A saída de exemplo a seguir mostra a página padrão da WEB NGINX retornada:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão de terminal anexada. O pod é automaticamente excluído.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testar um pod sem um rótulo correspondente

A política de rede permite o tráfego dos pods rotulados com *app:webapp,role:frontend*, mas deve negar todos os outros tráfegos. Vamos testar para ver se outro pod sem essas etiquetas pode acessar o pod NGINX back-end. Crie outro pod de teste e anexe uma sessão de terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

No prompt shell, `wget` use para ver se você pode acessar a página padrão nginx. A diretiva de rede bloqueia o tráfego de entrada, de modo que a página não pode ser carregada, como mostrado no exemplo a seguir:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Saia da sessão de terminal anexada. A cápsula de teste é automaticamente excluída.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Permitir o tráfego somente dentro de um namespace definido

Nos exemplos anteriores, você criou uma diretiva de rede que negou todo o tráfego e, em seguida, atualizou a diretiva para permitir o tráfego de pods com um rótulo específico. Outra necessidade comum é limitar o tráfego apenas dentro de um determinado namespace. Se os exemplos anteriores foram para o tráfego em um namespace *de desenvolvimento,* crie uma política de rede que impeça o tráfego de outro namespace, como *a produção,* de chegar aos pods.

Primeiro crie um novo namespace para simular um namespace de produção:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Agende um pod de teste no namespace *production* que esteja rotulado como *app=webapp,role=frontend*. Anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

No prompt shell, `wget` use para confirmar se você pode acessar a página web padrão do NGINX:

```console
wget -qO- http://backend.development
```

Como os rótulos do pod correspondem ao que é permitido atualmente na política de rede, o tráfego é permitido. A política de rede não examina os namespaces, somente os rótulos de pod. A saída de exemplo a seguir mostra a página padrão da WEB NGINX retornada:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão de terminal anexada. A cápsula de teste é automaticamente excluída.

```console
exit
```

### <a name="update-the-network-policy"></a>Atualizar a política de rede

Vamos atualizar a regra de ingestão *namespaceSelector* seção para permitir apenas o tráfego dentro do namespace de *desenvolvimento.* Edite o arquivo de manifesto *backend-policy.yaml*, como mostrado no exemplo a seguir:

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

Em exemplos mais complexos, você pode definir várias regras de entrada, como um *namespaceSelector* e, em seguida, um *podSelector*.

Aplique a diretiva de rede atualizada usando o comando [kubectl apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testar a política de rede atualizada

Agende outro pod no namespace de *produção* e anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

No prompt shell, `wget` use para ver se a política de rede agora nega o tráfego:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Saia do pod de teste:

```console
exit
```

Com o tráfego negado no espaço de nome da *produção,* agende um pod de teste de volta no namespace de *desenvolvimento* e anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

No prompt shell, `wget` use para ver se a diretiva de rede permite o tráfego:

```console
wget -qO- http://backend
```

O tráfego é permitido porque o pod está programado no namespace que corresponde ao permitido na diretiva de rede. A seguinte saída de amostra mostra que a página padrão do NGINX retornou:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão de terminal anexada. A cápsula de teste é automaticamente excluída.

```console
exit
```

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, criamos dois namespaces e aplicamos uma política de rede. Para limpar esses recursos, use o comando [kubectl delete][kubectl-delete] e especifique os nomes dos recursos:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre recursos de rede, consulte [conceitos de rede para aplicações no Azure Kubernetes Service (AKS)][concepts-network].

Para saber mais sobre políticas, consulte [as políticas de rede da Kubernetes.][kubernetes-network-policies]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.9/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/v3.9/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
