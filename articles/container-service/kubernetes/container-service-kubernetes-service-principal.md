---
title: "Entidade de serviço para cluster Kubernetes do Azure | Microsoft Docs"
description: "Criar e gerenciar uma entidade de serviço do Azure Active Directory para um cluster Kubernetes no Serviço de Contêiner do Azure"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 14975454cbc0afcfbdbd3aa6b52983be4d4b1785
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="set-up-an-azure-ad-service-principal-for-a-kubernetes-cluster-in-container-service"></a>Configurar uma entidade de serviço do Azure AD para um cluster Kubernetes no contêiner de serviço

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

No Serviço de Contêiner do Azure, um cluster Kubernetes requer uma [entidade de serviço do Azure Active Directory](../../active-directory/develop/active-directory-application-objects.md) para interagir com as APIs do Azure. A entidade de serviço é necessária para gerenciar dinamicamente recursos como [rotas definidas pelo usuário](../../virtual-network/virtual-networks-udr-overview.md) e o [Azure Load Balancer da Camada 4](../../load-balancer/load-balancer-overview.md).


Este artigo mostra diferentes opções para configurar uma entidade de serviço para o cluster Kubernetes. Por exemplo, se você instalou e configurou a [CLI do Azure 2.0](/cli/azure/install-az-cli2), poderá executar o comando [`az acs create`](/cli/azure/acs#create) para criar o cluster Kubernetes e a entidade de serviço ao mesmo tempo.


## <a name="requirements-for-the-service-principal"></a>Requisitos para a entidade de serviço

Você pode usar uma entidade de serviço existente do Azure AD que atende aos requisitos a seguir ou criar uma nova.

* **Escopo**: a assinatura usada para implantar o cluster.

* **Função**: **Colaborador**

* **Segredo do cliente**: deve ser uma senha. Atualmente, é possível usar uma entidade de serviço configurada para autenticação de certificado.

> [!IMPORTANT]
> Para criar uma entidade de serviço, você deve ter permissões para registrar um aplicativo com o locatário do Azure AD e para atribuir o aplicativo a uma função em sua assinatura. Se você tem as permissões necessárias, [verifique no Portal](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions).
>

## <a name="option-1-create-a-service-principal-in-azure-ad"></a>Opção 1: criar uma entidade de serviço no Azure AD

Se você quiser criar uma entidade de serviço do Azure AD antes de implantar o cluster Kubernetes, poderá usar vários métodos fornecidos pelo Azure.

Os comandos de exemplo a seguir mostram como fazer isso com a [CLI do Azure 2.0](../../azure-resource-manager/resource-group-authenticate-service-principal-cli.md). Você também pode criar uma entidade de serviço usando [Azure PowerShell](../../azure-resource-manager/resource-group-authenticate-service-principal.md), o [portal](../../azure-resource-manager/resource-group-create-service-principal-portal.md) ou outros métodos.

```azurecli
az login

az account set --subscription "mySubscriptionID"

az group create --name "myResourceGroup" --location "westus"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

A saída é semelhante à seguinte (mostrada aqui em uma versão editada):

![Criar uma entidade de serviço](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

Estão realçados a **ID do cliente** (`appId`) e o **segredo do cliente** (`password`) que você usa como parâmetros da entidade de serviço para a implantação de cluster.


### <a name="specify-service-principal-when-creating-the-kubernetes-cluster"></a>Especifique a entidade de serviço ao criar o cluster Kubernetes

Forneça a **ID do cliente** (também chamada de `appId`, para ID do Aplicativo) e o **segredo do cliente** (`password`) de uma entidade de serviço existente como parâmetros ao criar o cluster Kubernetes. Verifique se a entidade de serviço atende aos requisitos no início deste artigo.

Você pode especificar esses parâmetros ao implantar o cluster Kubernetes usando a [CLI (Interface de Linha de Comando) do Azure 2.0](container-service-kubernetes-walkthrough.md), o [portal do Azure](../dcos-swarm/container-service-deployment.md) ou outros métodos.

>[!TIP]
>Ao especificar a **ID do cliente**, use `appId` e não `ObjectId` da entidade de serviço.
>

O exemplo a seguir mostra uma maneira de passar os parâmetros com a CLI do Azure 2.0. Este exemplo usa o [modelo de início rápido do Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

1. [Baixe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json) o arquivo de parâmetros de modelo `azuredeploy.parameters.json` do GitHub.

2. Para especificar a entidade de serviço, insira valores para `servicePrincipalClientId` e `servicePrincipalClientSecret` no arquivo. (Você também precisa fornecer seus próprios valores para `dnsNamePrefix` e `sshRSAPublicKey`. O segundo é a chave pública SSH para acessar o cluster.) Salve o arquivo.

    ![Passar parâmetros de entidade de serviço](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. Execute o seguinte comando usando `--parameters` para definir o caminho para o arquivo azuredeploy.parameters.json. Esse comando implanta o cluster em um grupo de recursos que você criar chamado `myResourceGroup` na região Oeste dos EUA.

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus"

    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


## <a name="option-2-generate-a-service-principal-when-creating-the-cluster-with-az-acs-create"></a>Opção 2: gerar uma entidade de serviço ao criar o cluster com `az acs create`

Se você executar o comando [ `az acs create` ](/cli/azure/acs#create) para criar o cluster Kubernetes, terá a opção de gerar uma entidade de serviço automaticamente.

Como ocorre com outras opções de criação do cluster Kubernetes, você pode especificar parâmetros para uma entidade de serviço existente quando executa `az acs create`. No entanto, quando você omite esses parâmetros, a CLI do Azure cria uma automaticamente para uso com o serviço de contêiner. Isso ocorre de forma transparente durante a implantação.

O comando a seguir cria um cluster Kubernetes e gera chaves SSH e credenciais de entidade de serviço:

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

> [!IMPORTANT]
> Se sua conta não tiver o Azure AD e as permissões de assinatura para criar uma entidade de serviço, o comando gerará um erro semelhante a `Insufficient privileges to complete the operation.`
>

## <a name="additional-considerations"></a>Considerações adicionais

* Se você não tiver permissões para criar uma entidade de serviço em sua assinatura, talvez precise pedir ao administrador do Azure AD ou da assinatura para atribuir as permissões necessárias ou pedir uma entidade de serviço para usar com o Serviço de Contêiner do Azure.

* A entidade de serviço para o Kubernetes é parte da configuração do cluster. No entanto, não use a identidade para implantar o cluster.

* Cada entidade de serviço é associada a um aplicativo Azure AD. A entidade de serviço para um cluster Kubernetes pode ser associada a qualquer nome de aplicativo válido do Azure AD (por exemplo: `https://www.contoso.org/example`). A URL para o aplicativo não precisa ser um ponto de extremidade real.

* Ao especificar a **ID do cliente** da entidade de serviço, você pode usar o valor de `appId` (conforme mostrado neste artigo) ou `name` da entidade de serviço correspondente (por exemplo, `https://www.contoso.org/example`).

* Nas VMs mestre e agente no cluster Kubernetes, as credenciais de entidade de serviço são armazenadas no arquivo /etc/kubernetes/azure.json.

* Se você usar o comando `az acs create` para gerar a entidade de serviço automaticamente, as credenciais da entidade de serviço serão gravadas no arquivo ~/.azure/acsServicePrincipal.json no computador usado para executar o comando.

* Quando você usa o comando `az acs create` para gerar a entidade de serviço automaticamente, ela também pode autenticar com um [registro de contêiner do Azure](../../container-registry/container-registry-intro.md) criado na mesma assinatura.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Kubernetes](container-service-kubernetes-walkthrough.md) em seu cluster de serviço de contêiner.

* Para solucionar a entidade de serviço do Kubernetes, consulte a [documentação do mecanismo ACS](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes.md#troubleshooting).
