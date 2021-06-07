---
title: Criar um controlador de entrada com um gateway de aplicativo existente
description: Este artigo fornece informações sobre como implantar um controlador de entrada do gateway de aplicativo com um gateway de aplicativo existente.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 2d64766c754c0ea104ae83fde799a514e9da6d68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693732"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Instalar um controlador de entrada do gateway de aplicativo (AGIC) usando um gateway de aplicativo existente

O controlador de entrada do gateway de aplicativo (AGIC) é um pod dentro do cluster kubernetes.
O AGIC monitora os recursos de [entrada](https://kubernetes.io/docs/concepts/services-networking/ingress/) do kubernetes e cria e aplica a configuração do gateway de aplicativo com base no status do cluster kubernetes.

## <a name="outline"></a>Descreve
- [Pré-requisitos](#prerequisites)
- [Autenticação de Azure Resource Manager (ARM)](#azure-resource-manager-authentication)
    - Opção 1: [Configurar AAD-Pod-Identity](#set-up-aad-pod-identity) e criar identidade do Azure em braços
    - Opção 2: [usando uma entidade de serviço](#using-a-service-principal)
- [Instalar controlador de entrada usando Helm](#install-ingress-controller-as-a-helm-chart)
- [Gateway de aplicativo de vários clusters/compartilhados](#multi-cluster--shared-application-gateway): Instale o AGIC em um ambiente, em que o gateway de aplicativo é compartilhado entre um ou mais clusters do AKs e/ou outros componentes do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que você já tem as seguintes ferramentas e infraestrutura instaladas:
- [AKs](https://azure.microsoft.com/services/kubernetes-service/) com [rede avançada](../aks/configure-azure-cni.md) habilitada
- [Gateway de aplicativo v2](./tutorial-autoscale-ps.md) na mesma rede virtual que AKs
- [Identidade do Pod do AAD](https://github.com/Azure/aad-pod-identity) instalada no cluster AKs
- [Cloud Shell](https://shell.azure.com/) é o ambiente do shell do Azure, que tem a `az` CLI, o `kubectl` e o `helm` instalado. Essas ferramentas são necessárias para os comandos a seguir.

Faça __backup da configuração do gateway de aplicativo antes de__ instalar o AGIC:
  1. usando [portal do Azure](https://portal.azure.com/) navegar para sua `Application Gateway` instância
  2. do `Export template` clique `Download`

O arquivo zip que você baixou terá modelos JSON, bash e scripts do PowerShell que você pode usar para restaurar o gateway de aplicativo se ele se tornar necessário

## <a name="install-helm"></a>Instalar o Helm
[Helm](../aks/kubernetes-helm.md) é um Gerenciador de pacotes para kubernetes. Vamos aproveitá-lo para instalar o `application-gateway-kubernetes-ingress` pacote.
Use [Cloud Shell](https://shell.azure.com/) para instalar o Helm:

1. Instale o [Helm](../aks/kubernetes-helm.md) e execute o seguinte para adicionar o `application-gateway-kubernetes-ingress` pacote Helm:

    - *RBAC kubernetes habilitado* Cluster AKS

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - *RBAC kubernetes desabilitado* Cluster AKS

    ```bash
    helm init
    ```

1. Adicione o repositório AGIC do Helm:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Autenticação Azure Resource Manager

O AGIC se comunica com o servidor de API do kubernetes e o Azure Resource Manager. Ele requer uma identidade para acessar essas APIs.

## <a name="set-up-aad-pod-identity"></a>Configurar a identidade do Pod do AAD

A [identidade do Pod do AAD](https://github.com/Azure/aad-pod-identity) é um controlador, semelhante a AGIC, que também é executado em seu AKs. Ele associa Azure Active Directory identidades ao seu pods kubernetes. A identidade é necessária para que um aplicativo em um pod kubernetes seja capaz de se comunicar com outros componentes do Azure. No caso específico aqui, precisamos de autorização para que o Pod AGIC faça solicitações HTTP para o [ARM](../azure-resource-manager/management/overview.md).

Siga as [instruções de instalação de identidade do Pod do AAD](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) para adicionar esse componente ao seu AKs.

Em seguida, precisamos criar uma identidade do Azure e dar a ela permissões ARM.
Use [Cloud Shell](https://shell.azure.com/) para executar todos os comandos a seguir e criar uma identidade:

1. Crie uma identidade do Azure **no mesmo grupo de recursos que os nós AKs**. É importante escolher o grupo de recursos correto. O grupo de recursos necessário no comando abaixo *não* é aquele referenciado no painel do portal do AKS. Esse é o grupo de recursos das `aks-agentpool` máquinas virtuais. Normalmente, esse grupo de recursos começa com `MC_` e contém o nome do seu AKs. Por exemplo: `MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Para os comandos de atribuição de função abaixo, precisamos obter `principalId` a identidade recém-criada:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Conceda acesso de identidade `Contributor` ao seu gateway de aplicativo. Para isso, você precisa da ID do gateway de aplicativo, que terá uma aparência semelhante a esta: `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Obtenha a lista de IDs de gateway de aplicativo em sua assinatura com: `az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Conceda acesso de identidade `Reader` ao grupo de recursos do gateway de aplicativo. A ID do grupo de recursos teria a seguinte aparência: `/subscriptions/A/resourceGroups/B` . Você pode obter todos os grupos de recursos com: `az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Usando uma entidade de serviço
Também é possível fornecer acesso AGIC ao ARM por meio de um segredo kubernetes.

1. Crie uma entidade de serviço Active Directory e codifique com base64. A codificação Base64 é necessária para que o blob JSON seja salvo em kubernetes.

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. Adicione o blob JSON codificado em base64 ao `helm-config.yaml` arquivo. Mais informações sobre `helm-config.yaml` o estão na próxima seção.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Instalar controlador de entrada como um gráfico do Helm
Nas primeiras etapas, instalamos o gaveta do Helm no cluster do kubernetes. Use [Cloud Shell](https://shell.azure.com/) para instalar o pacote AGIC Helm:

1. Adicionar o `application-gateway-kubernetes-ingress` repositório Helm e executar uma atualização do Helm

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. Baixe Helm-config. YAML, que irá configurar o AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Ou copie o arquivo YAML abaixo: 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Edite Helm-config. YAML e preencha os valores de `appgw` e `armAuth` .
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > O `<identity-resource-id>` e `<identity-client-id>` são as propriedades da identidade do Azure AD que você configura na seção anterior. Você pode recuperar essas informações executando o seguinte comando: `az identity show -g <resourcegroup> -n <identity-name>` , em que `<resourcegroup>` é o grupo de recursos no qual o objeto de cluster AKs de nível superior, o gateway de aplicativo e a identificação gerenciada são implantados.

1. Instalar o gráfico do Helm `application-gateway-kubernetes-ingress` com a `helm-config.yaml` configuração da etapa anterior

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Como alternativa, você pode combinar o `helm-config.yaml` e o comando Helm em uma única etapa:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Verifique o log do pod recém-criado para verificar se ele foi iniciado corretamente

Consulte [este guia de instruções](ingress-controller-expose-service-over-http-https.md) para entender como você pode expor um serviço AKs por http ou HTTPS, para a Internet, usando um Gateway de aplicativo Azure.



## <a name="multi-cluster--shared-application-gateway"></a>Gateway de aplicativo de vários clusters/compartilhados
Por padrão, o AGIC assume a propriedade total do gateway de aplicativo ao qual está vinculado. O AGIC versão 0.8.0 e posterior pode compartilhar um único gateway de aplicativo com outros componentes do Azure. Por exemplo, poderíamos usar o mesmo gateway de aplicativo para um aplicativo hospedado no conjunto de dimensionamento de máquinas virtuais, bem como um cluster AKS.

Faça __backup da configuração do gateway de aplicativo antes de__ habilitar essa configuração:
  1. usando [portal do Azure](https://portal.azure.com/) navegar para sua `Application Gateway` instância
  2. do `Export template` clique `Download`

O arquivo zip que você baixou terá modelos JSON, bash e scripts do PowerShell que você pode usar para restaurar o gateway de aplicativo

### <a name="example-scenario"></a>Cenário de Exemplo
Vamos examinar um gateway de aplicativo imaginário, que gerencia o tráfego para dois sites da Web:
  - `dev.contoso.com` -hospedado em um novo AKS, usando o gateway de aplicativo e o AGIC
  - `prod.contoso.com` -hospedado em um [conjunto de dimensionamento de máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

Com as configurações padrão, AGIC assume 100% de Propriedade do gateway de aplicativo ao qual ele é apontado. AGIC substitui toda a configuração do gateway de aplicativo. Se tivéssemos de criar manualmente um ouvinte para `prod.contoso.com` (no gateway de aplicativo), sem defini-lo na entrada kubernetes, o AGIC excluirá a `prod.contoso.com` configuração em segundos.

Para instalar o AGIC e também para atender `prod.contoso.com` de nossos computadores de conjunto de dimensionamento de máquinas virtuais, é necessário restringir AGIC somente à configuração `dev.contoso.com` . Isso é facilitado com a instanciação do seguinte [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/):

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

O comando acima cria um `AzureIngressProhibitedTarget` objeto. Isso torna o AGIC (versão 0.8.0 e posterior) ciente da existência da configuração do gateway de aplicativo para o `prod.contoso.com` e o instrui explicitamente para evitar a alteração de qualquer configuração relacionada a esse nome de host.


### <a name="enable-with-new-agic-installation"></a>Habilitar com a instalação do New AGIC
Para limitar o AGIC (versão 0.8.0 e posterior) a um subconjunto da configuração do gateway de aplicativo, modifique o `helm-config.yaml` modelo.
Na `appgw:` seção, adicione a `shared` chave e defina-a como `true` .

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Aplique as alterações Helm:
  1. Verifique se o `AzureIngressProhibitedTarget` CRD está instalado com:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Atualizar Helm:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

Como resultado, seu AKS terá uma nova instância do `AzureIngressProhibitedTarget` chamada `prohibit-all-targets` :
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

O objeto `prohibit-all-targets` , como o nome sugere, proíbe AGIC de alterar a configuração de *qualquer* host e caminho.
O Helm instalar com o `appgw.shared=true` implantará o AGIC, mas não fará nenhuma alteração no gateway de aplicativo.


### <a name="broaden-permissions"></a>Ampliar permissões
Como Helm com `appgw.shared=true` e o padrão `prohibit-all-targets` bloqueia AGIC de aplicar qualquer configuração.

Amplie as permissões de AGIC com:
1. Crie um novo `AzureIngressProhibitedTarget` com sua configuração específica:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. Somente depois de criar sua própria proibição personalizada, você pode excluir o padrão, o que é muito amplo:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Habilitar para uma instalação existente do AGIC
Vamos supor que já temos um AKS de trabalho, um gateway de aplicativo e um AGIC configurado em nosso cluster. Temos uma entrada para `prod.contoso.com` e estão servindo com êxito o tráfego para ele do AKS. Queremos adicionar `staging.contoso.com` ao nosso gateway de aplicativo existente, mas é necessário hospedá-lo em uma [VM](https://azure.microsoft.com/services/virtual-machines/). Vamos reutilizar o gateway de aplicativo existente e configurar manualmente um ouvinte e pools de back-end para o `staging.contoso.com` . Mas ajustar manualmente a configuração do gateway de aplicativo (por meio do [portal](https://portal.azure.com), [APIs ARM](/rest/api/resources/) ou [Terraform](https://www.terraform.io/)) estaria em conflito com as suposições de AGIC de propriedade total. Logo após aplicarmos as alterações, AGIC irá substituí-las ou excluí-las.

Podemos proibir o AGIC de fazer alterações em um subconjunto da configuração.

1. Criar um `AzureIngressProhibitedTarget` objeto:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. Exiba o objeto recém-criado:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Modificar a configuração do gateway de aplicativo por meio do portal-adicionar ouvintes, regras de roteamento, back-ends etc. O novo objeto que criamos ( `manually-configured-staging-environment` ) proíbe AGIC de substituir a configuração do gateway de aplicativo relacionada ao `staging.contoso.com` .
