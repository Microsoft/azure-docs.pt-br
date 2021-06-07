---
title: Proteger o tráfego de pods com política de rede
titleSuffix: Azure Kubernetes Service
description: Saiba como proteger o tráfego de entrada e saída dos pods usando políticas de rede do Kubernetes no AKS (Kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 17e14859ecdfe11872d5b0526d755d01bc1b034a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577845"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Proteger o tráfego entre os pods usando as políticas de rede no Serviço de Kubernetes do Azure (AKS)

Ao executar aplicativos modernos baseados em microsserviços no Kubernetes, muitas vezes você deseja controlar quais componentes podem se comunicar uns com os outros. O princípio de privilégio mínimo deve ser aplicado à maneira como o tráfego pode fluir entre pods em um cluster AKS (Serviço de Kubernetes do Azure). Digamos que você provavelmente deseje bloquear o tráfego diretamente para os aplicativos de back-end. O recurso *Política de Rede* no Kubernetes permite definir regras para o tráfego de entrada e saída entre os pods em um cluster.

Este artigo mostra como instalar o mecanismo de política de rede e criar políticas de rede do Kubernetes para controlar o fluxo de tráfego entre pods no AKS. A política de rede só deve ser usada para nós e pods baseados em Linux no AKS.

## <a name="before-you-begin"></a>Antes de começar

Você precisará da CLI do Azure versão 2.0.61 ou posterior instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

> [!TIP]
> Se você usou o recurso de política de rede durante a versão prévia, recomendamos [criar um cluster](#create-an-aks-cluster-and-enable-network-policy).
> 
> Se você deseja continuar usando clusters de teste existentes que usaram a política de rede durante a versão prévia, atualize seu cluster para uma nova versão do Kubernetes da versão GA mais recente e implante o manifesto YAML a seguir para corrigir o servidor de métricas com falha e o painel do Kubernetes. Essa correção só é necessária para clusters que usavam o mecanismo de política de rede Calico.
>
> Como melhor prática de segurança, [examine o conteúdo deste manifesto YAML][calico-aks-cleanup] para entender o que é implantado no cluster do AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Visão geral da política de rede

Todos os pods em um cluster do AKS podem enviar e receber tráfego sem limitações, por padrão. Para melhorar a segurança, você pode definir regras que controlam o fluxo de tráfego. Os aplicativos de back-end geralmente são expostos apenas aos serviços front-end necessários, por exemplo. Ou os componentes de banco de dados só são acessíveis para as camadas de aplicativo que se conectam a eles.

A Política de Rede é uma especificação do Kubernetes que define políticas de acesso para comunicação entre pods. Usando as Políticas de Rede, você define um conjunto ordenado de regras para enviar e receber tráfego e aplicá-las a uma coleção de pods que correspondem a um ou mais seletores de rótulo.

Essas regras de política de rede são definidas como manifestos YAML. As políticas de rede podem ser incluídas como parte de um manifesto mais amplo que também cria uma implantação ou um serviço.

### <a name="network-policy-options-in-aks"></a>Opções de política de rede no AKS

O Azure fornece duas maneiras de implementar a política de rede. Escolha uma opção de política de rede ao criar um cluster do AKS. A opção de política não poderá ser alterada após a criação do cluster:

* A própria implementação do Azure, chamada de *Políticas de Rede do Azure*.
* As *Políticas de Rede Calico*, uma solução de segurança de rede e rede de software livre fundada por [Tigera][tigera].

Ambas as implementações usam o *IPTables* Linux para impor as políticas especificadas. As políticas são convertidas em conjuntos de pares de IP permitidos e não permitidos. Esses pares são programados como regras de filtro IPTable.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Diferenças entre as políticas do Azure e Calico e as funcionalidades delas

| Recurso                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Plataformas compatíveis                      | Linux                      | Linux, Windows Server 2019 (versão prévia)  |
| Opções de rede com suporte             | CNI do Azure                  | Azure CNI (Windows Server 2019 e Linux) e kubenet (Linux)  |
| Conformidade com a especificação do Kubernetes | Todos os tipos de política com suporte |  Todos os tipos de política com suporte |
| Recursos adicionais                      | Nenhum                       | Modelo de política estendida composto pela Política de Rede Global, Conjunto de Rede Global e Ponto de Extremidade do Host. Para obter mais informações sobre como usar a CLI `calicoctl` para gerenciar esses recursos estendidos, confira a [Referência de usuário do calicoctl][calicoctl]. |
| Suporte                                  | Com suporte da equipe de engenharia e suporte do Azure | Suporte da comunidade do Calico. Para obter mais informações sobre suporte pago adicional, confira [Opções de suporte do Projeto Calico][calico-support]. |
| Registro em log                                  | As regras adicionadas/excluídas em IPTables são registradas em todos os hosts em */var/log/azure-npm.log* | Para obter mais informações, confira [Logs de componente do Calico][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Cria um cluster do AKS e habilita a política de rede

Para ver as políticas de rede em ação, vamos criar e, em seguida, expandir uma política que define o fluxo de tráfego:

* Nega todo o tráfego ao pod.
* Permite o tráfego com base nos rótulos do pod.
* Permite o tráfego com base no namespace.

Primeiro, vamos criar um cluster do AKS que dê suporte à política de rede.

> [!IMPORTANT]
>
> O recurso de política de rede só pode ser habilitado ao criar o cluster. Não é possível habilitar a política de rede em um cluster AKS existente.

Para usar a Política de Rede do Azure, você deve usar o [plug-in da CNI do Azure][azure-cni] e definir sua própria rede virtual e sub-redes. Para saber mais sobre como planejar os intervalos de sub-rede necessários, confira [Configurar a rede avançada][use-advanced-networking]. A Política de Rede Calico pode ser usada com esse mesmo plug-in da CNI do Azure ou com o plug-in da CNI do Kubenet.

O exemplo de script a seguir:

* Cria uma rede virtual e uma sub-rede.
* Cria uma entidade de serviço do Azure AD (Azure Active Directory) para uso com o cluster do AKS.
* Atribui permissões de *Colaborador* para a entidade de serviço do cluster do AKS em uma rede virtual.
* Cria um cluster do AKS na rede virtual definida e habilita a política de rede.
    * A opção de política da _Rede do Azure_ é usada. Para usar o Calico como a opção de política de rede, use o parâmetro `--network-policy calico`. Observação: o Calico pode ser usado com `--network-plugin azure` ou `--network-plugin kubenet`.

Observe que, em vez de usar uma entidade de serviço, você pode usar uma identidade gerenciada para permissões. Para obter mais informações, confira [Usar identidades gerenciadas](use-managed-identity.md).

Forneça sua própria *SP_PASSWORD* segura. Você pode substituir as variáveis *RESOURCE_GROUP_NAME* e *CLUSTER_NAME*:

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
```

### <a name="create-an-aks-cluster-for-azure-network-policies"></a>Criar um cluster AKS para as políticas de rede do Azure

Crie o cluster AKS e especifique a rede virtual, as informações da entidade de serviço e o *Azure* para a política de rede e o plug-in de rede.

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-plugin azure \
    --network-policy azure
```

São necessários alguns minutos para criar o cluster. Quando o cluster estiver pronto, configure `kubectl` para se conectar ao cluster do Kubernetes usando o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

### <a name="create-an-aks-cluster-for-calico-network-policies"></a>Criar um cluster AKS para políticas de rede Calico

Crie o cluster AKS e especifique a rede virtual, as informações da entidade de serviço, o *Azure* para o plug-in de rede e *Calico* para a política de rede. Usar o *Calico* como a política de rede permite a rede Calico em pools de nós do Linux e do Windows.

Se você planeja adicionar pools de nós do Windows ao cluster, inclua `windows-admin-username` os `windows-admin-password` parâmetros e com que atendam aos [requisitos de senha do Windows Server][windows-server-password]. Para usar o Calico com pools de nós do Windows, você também precisa registrar o `Microsoft.ContainerService/EnableAKSWindowsCalico` .

Registre o `EnableAKSWindowsCalico` sinalizador de recurso usando o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAKSWindowsCalico"
```

 Você pode verificar o status de registro usando o comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAKSWindowsCalico')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> Neste momento, o uso de políticas de rede Calico com nós do Windows está disponível em novos clusters usando o kubernetes versão 1,20 ou posterior com Calico 3.17.2 e requer o uso da rede CNI do Azure. Os nós do Windows em clusters AKS com Calico habilitado também têm o [DSR (Direct Server Return)][dsr] habilitado por padrão.
>
> Para clusters com apenas pools de nós do Linux executando o kubernetes 1,20 com versões anteriores do Calico, a versão Calico será atualizada automaticamente para 3.17.2.

As políticas de rede Calico com nós do Windows estão atualmente em versão prévia.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Crie um nome de usuário para usar como credenciais de administrador para seus contêineres do Windows Server em seu cluster. Os comandos a seguir solicitam um nome de usuário e os definem WINDOWS_USERNAME para uso em um comando posterior (Lembre-se de que os comandos neste artigo são inseridos em um shell BASH).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --kubernetes-version 1.20.2 \
    --network-plugin azure \
    --network-policy calico
```

São necessários alguns minutos para criar o cluster. Por padrão, o cluster é criado apenas com um pool de nós do Linux. Se você quiser usar pools de nós do Windows, você pode adicionar um. Por exemplo:

```azurecli
az aks nodepool add \
    --resource-group $RESOURCE_GROUP_NAME \
    --cluster-name $CLUSTER_NAME \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Quando o cluster estiver pronto, configure `kubectl` para se conectar ao cluster do Kubernetes usando o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Negar todo o tráfego de entrada para um pod

Antes de definir as regras para permitir o tráfego de rede específico, primeiro crie uma política para negar todo o tráfego de rede. Essa política oferece um ponto de partida para começar a criar umalist de permissão apenas para o tráfego desejado. Você também pode ver claramente que o tráfego é removido quando a política de rede é aplicada.

Para o ambiente de aplicativo de exemplo e de regras de tráfego, primeiro vamos criar um namespace chamado *development* para executar os pods de exemplo:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Crie um exemplo de pod de back-end que executa o NGINX. Este pod de back-end pode ser usado para simular um exemplo de aplicativo baseado na Web de back-end. Crie este pod no namespace *development* e abra a porta *80* para servir o tráfego da Web. Rotule o pod com *app=webapp,role=backend* para que possamos alcançá-lo com uma política de rede na próxima seção:

```console
kubectl run backend --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --labels app=webapp,role=backend --namespace development --expose --port 80
```

Crie outro pod e anexe uma sessão de terminal para testar se você consegue acessar com êxito a página da Web padrão do NGINX:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

No prompt de shell, use `wget` para confirmar que você pode acessar a página da Web padrão do NGINX:

```console
wget -qO- http://backend
```

A seguinte saída de exemplo mostra que a página da Web padrão do NGINX retornou:

```output
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

Agora que você confirmou que pode usar a página da Web básica do NGINX no exemplo de pod de back-end, crie uma política de rede para negar todo o tráfego. Crie um arquivo chamado `backend-policy.yaml` e cole o manifesto YAML a seguir. Esse manifesto usa um *podSelector* para anexar a política a pods que têm o rótulo *app:webapp,role:backend*, como no pod NGINX de exemplo. Nenhuma regra é definida em *ingress*, portanto, todo o tráfego de entrada para o pod é negado:

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

Acesse [https://shell.azure.com](https://shell.azure.com) para abrir o Azure Cloud Shell em seu navegador.

Aplique a política de rede usando o comando [kubectl apply][kubectl-apply] e especifique o nome do manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testar a política de rede

Vamos ver se você pode usar a página da Web do NGINX no pod de back-end novamente. Crie outro pod de teste e anexe uma sessão de terminal:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

No prompt de shell, use `wget` para ver se você pode acessar a página Web padrão do NGINX. Desta vez, defina um valor de tempo limite em *2* segundos. A política de rede agora bloqueia todo o tráfego de entrada, portanto a página não pode ser carregada, conforme mostrado no exemplo a seguir:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Saia da sessão de terminal anexada. O pod de teste é excluído automaticamente.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Permitir tráfego de entrada com base em um rótulo de pod

Na seção anterior, um pod NGINX de back-end foi agendado e uma política de rede foi criada para negar todo o tráfego. Vamos criar um pod de front-end e atualizar a política de rede para permitir o tráfego dos pods de front-end.

Atualize a política de rede para permitir o tráfego dos pods com os rótulos *app:webapp,role:frontend* e em qualquer namespace. Edite o arquivo *backend-policy.yaml* anterior e adicione regras de entrada *matchLabels* para que o manifesto se pareça com o seguinte exemplo:

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
> Esta política de rede usa *namespaceSelector* e um elemento *podSelector* para a regra de entrada. A sintaxe YAML é importante para que as regras de entrada sejam aditivas. Neste exemplo, os dois elementos devem corresponder à regra de entrada a ser aplicada. As versões do Kubernetes anteriores à *1.12* podem não interpretar corretamente esses elementos e restringir o tráfego de rede, conforme o esperado. Para saber mais sobre esse comportamento, confira [Comportamento entre seletores][policy-rules].

Aplique a política de rede atualizada usando o comando [kubectl apply][kubectl-apply] e especifique o nome do manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

Agende um pod rotulado como *app=webapp,role=frontend* e anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

No prompt de shell, use `wget` para ver se você pode acessar a página Web padrão do NGINX:

```console
wget -qO- http://backend
```

Como a regra de entrada permite o tráfego com pods que têm rótulos *app:webapp,role:frontend*, o tráfego do pod de front-end é permitido. A seguinte saída de exemplo mostra a página Web padrão do NGINX retornada:

```output
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

A política de rede permite o tráfego dos pods rotulados com *app:webapp,role:frontend*, mas deve negar todos os outros tráfegos. Vamos testar para ver se outro pod sem esses rótulos pode acessar o pod NGINX de back-end. Crie outro pod de teste e anexe uma sessão de terminal:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

No prompt de shell, use `wget` para ver se você pode acessar a página Web padrão do NGINX. A política de rede bloqueia o tráfego de entrada, portanto a página não pode ser carregada, conforme mostrado no seguinte exemplo:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Saia da sessão de terminal anexada. O pod de teste é excluído automaticamente.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Permitir o tráfego somente dentro de um namespace definido

Nos exemplos anteriores, você criou uma política de rede que negou todo o tráfego e, em seguida, atualizou a política para permitir o tráfego dos pods com um rótulo específico. Outra necessidade comum é limitar o tráfego somente dentro de um namespace específico. Se os exemplos anteriores foram para o tráfego em um namespace *development*, crie uma política de rede que impeça o tráfego de outro namespace, tal como *production*, de alcançar os pods.

Primeiro crie um novo namespace para simular um namespace de produção:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Agende um pod de teste no namespace *production* que esteja rotulado como *app=webapp,role=frontend*. Anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

No prompt de shell, use `wget` para confirmar que você pode acessar a página da Web padrão do NGINX:

```console
wget -qO- http://backend.development
```

Como os rótulos para o pod correspondem ao que é permitido na política de rede no momento, o tráfego é permitido. A política de rede não examina os namespaces, somente os rótulos de pod. A seguinte saída de exemplo mostra a página Web padrão do NGINX retornada:

```output
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

Vamos atualizar a regra de entrada da seção *namespaceSelector* para permitir apenas o tráfego de dentro do namespace *development*. Edite o arquivo de manifesto *backend-policy.yaml*, como mostrado no exemplo a seguir:

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

Aplique a política de rede atualizada usando o comando [kubectl apply][kubectl-apply] e especifique o nome do manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testar a política de rede atualizada

Agende outro pod no namespace *production* e anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

No prompt do shell, use `wget` para ver que a política de rede agora nega o tráfego:

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

Com o tráfego negado do namespace *production*, agende um pod de teste novamente no namespace *development* e anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

No prompt do shell, use `wget` para ver que a política de rede permite o tráfego:

```console
wget -qO- http://backend
```

O tráfego é permitido porque o pod está agendado no namespace que corresponde ao que é permitido na política de rede. A saída de exemplo a seguir mostra a página padrão do NGINX na Web retornada:

```output
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

## <a name="clean-up-resources"></a>Limpar os recursos

Neste artigo, criamos dois namespaces e aplicamos uma política de rede. Para limpar esses recursos, use o comando [kubectl delete][kubectl-delete] e especifique os nomes de recurso:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre recursos de rede, confira [Conceitos de rede para aplicativos no AKS (Serviço de Kubernetes do Azure)][concepts-network].

Para saber mais sobre políticas, confira [Políticas de rede do Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/maintenance/troubleshoot/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip