---
title: "Implantar um cluster do contêiner Docker - CLI do Azure | Microsoft Docs"
description: "Implante uma solução Kubernetes, CD/SO ou Docker Swarm no Serviço de Contêiner do Azure usando a CLI 2.0 do Azure"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: saudas
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: ecac5c255735b588ebb512b183e8a8bbbdcc905f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-docker-container-hosting-solution-using-the-azure-cli-20"></a>Implante uma solução de hospedagem de contêiner do Docker usando a CLI 2.0 do Azure

Use o comando `az acs` na CLI do Azure 2.0 para criar e gerenciar clusters no Serviço de Contêiner do Azure. Você também pode implantar um cluster do serviço de contêiner do Azure usando o [portal do Azure](container-service-deployment.md) ou as APIs de serviço do contêiner do Azure.

Para obter ajuda sobre os comandos `az acs`, passe o parâmetro `-h` para qualquer comando. Por exemplo: `az acs create -h`.



## <a name="prerequisites"></a>Pré-requisitos
Para criar um cluster do Serviço de Contêiner do Azure usando a CLI do Azure 2.0, você deve:
* ter uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/))
* instalar e configurar [CLI do Azure 2.0](/cli/azure/install-az-cli2)

## <a name="get-started"></a>Introdução 
### <a name="log-in-to-your-account"></a>Faça logon na sua conta
```azurecli
az login 
```

Siga os prompts para fazer logon interativamente. Para obter outros métodos para fazer logon, consulte [Introdução à CLI do Azure 2.0](/cli/azure/get-started-with-az-cli2).

### <a name="set-your-azure-subscription"></a>Definir sua assinatura do Azure

Se você tiver mais de uma assinatura do Azure, defina a assinatura padrão. Por exemplo:

```
az account set --subscription "f66xxxxx-xxxx-xxxx-xxx-zgxxxx33cha5"
```


### <a name="create-a-resource-group"></a>Criar um grupos de recursos
Recomendamos que você crie um grupo de recursos para cada cluster. Especifique uma região do Azure no qual o serviço de contêiner do Azure é [disponível](https://azure.microsoft.com/en-us/regions/services/). Por exemplo:

```azurecli
az group create -n acsrg1 -l "westus"
```
A saída deverá ser semelhante a esta:

![Criar um grupos de recursos](./media/container-service-create-acs-cluster-cli/rg-create.png)


## <a name="create-an-azure-container-service-cluster"></a>Criar um cluster do Serviço de Contêiner do Azure

Para criar um cluster, use `az acs create`.
Um nome para o cluster e o nome do grupo de recursos criado na etapa anterior são parâmetros obrigatórios. 

Outras entradas são definidas com valores padrão (veja a tela a seguir), a menos que substituídas usando seus respectivos comutadores. Por exemplo, o orquestrador é definido por padrão para o controlador de domínio/sistema operacional. E se não for especificado, um prefixo de nome DNS será criado com base no nome do cluster.

![az acs create usage](./media/container-service-create-acs-cluster-cli/create-help.png)


### <a name="quick-acs-create-using-defaults"></a>`acs create` rápido usando padrões
Se você tiver um arquivo de chave pública SSH RSA `id_rsa.pub` no local padrão (ou criou um para [OS X e Linux](../../virtual-machines/linux/mac-create-ssh-keys.md) ou [Windows](../../virtual-machines/linux/ssh-from-windows.md)), use um comando semelhante ao seguinte:

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```
Se você não tiver uma chave pública SSH, use este segundo comando. Esse comando com a opção `--generate-ssh-keys` criará um para você.

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

Depois de inserir o comando, aguarde cerca de 10 minutos para o cluster ser criado. A saída do comando inclui nomes de domínio totalmente qualificados (FQDNs) de nós mestre e o agente e um comando SSH para conectar-se ao mestre primeiro. Aqui está a saída abreviada:

![Criação ACS de imagem](./media/container-service-create-acs-cluster-cli/cluster-create.png)

> [!TIP]
> O [passo a passo do Kubernetes](../kubernetes/container-service-kubernetes-walkthrough.md) mostra como usar `az acs create` com valores padrão para criar um cluster Kubernetes.
>

## <a name="manage-acs-clusters"></a>Gerenciar clusters do ACS

Use os comandos `az acs` adicionais para gerenciar o cluster. Veja alguns exemplos.

### <a name="list-clusters-under-a-subscription"></a>Listar os clusters em uma assinatura

```azurecli
az acs list --output table
```

### <a name="list-clusters-in-a-resource-group"></a>Listar os clusters em um grupo de recursos

```azurecli
az acs list -g acsrg1 --output table
```

![acs list](./media/container-service-create-acs-cluster-cli/acs-list.png)


### <a name="display-details-of-a-container-service-cluster"></a>Exibir detalhes de um cluster do serviço de contêiner

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![acs show](./media/container-service-create-acs-cluster-cli/acs-show.png)


### <a name="scale-the-cluster"></a>Dimensionar o cluster
A ampliação e a redução dos nós de agente são permitidas. O parâmetro `new-agent-count` é o número de agentes no cluster do ACS.

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![acs scale](./media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>Exclui um cluster do serviço de contêiner
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
Esse comando não exclui todos os recursos criados (rede e armazenamento) ao criar o contêiner de serviço. Para excluir todos os recursos facilmente, é recomendável que implantar cada cluster em um grupo de recursos distintos. Em seguida, exclua o grupo de recursos quando o cluster não for mais necessário.

## <a name="next-steps"></a>Próximas etapas
Agora que você tem um cluster em funcionamento, confira estes documentos para obter detalhes sobre conexão e gerenciamento:

* [Conectar a um cluster do Serviço de Contêiner do Azure](../container-service-connect.md)
* [Trabalhar com o Serviço de Contêiner do Azure e o DC/SO](container-service-mesos-marathon-rest.md)
* [Trabalhar com o Serviço de Contêiner do Azure e o Docker Swarm](container-service-docker-swarm.md)
* [Trabalhar com o Serviço de Contêiner do Azure e o Kubernetes](../kubernetes/container-service-kubernetes-walkthrough.md)