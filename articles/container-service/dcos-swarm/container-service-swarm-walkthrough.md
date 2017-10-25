---
title: "Guia de início rápido - cluster do Docker Swarm do Azure para Linux | Microsoft Docs"
description: "Aprenda rapidamente a criar um cluster do Docker Swarm para os contêineres do Linux no Serviço de Contêiner do Azure com a CLI do Azure."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service, Docker, Swarm
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/14/2017
ms.author: nepeters
ms.custom: 
ms.openlocfilehash: 876135d62d548e155f4ebefd8bbd9d9cca8b87d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-docker-swarm-cluster"></a>Implantar cluster do Docker Swarm

Neste guia de início rápido, um cluster do Docker Swarm é implantado usando a CLI do Azure. Em seguida, um aplicativo com vários contêineres, composto por um front-end na Web e uma instância Redis, é executado no cluster. Depois de concluído, o aplicativo pode ser acessado pela internet.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este guia de início rápido exige que você esteja executando a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um grupo lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Saída:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westcentralus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Criar um cluster do Docker Swarm

Crie um cluster do Docker Swarm no Serviço de Contêiner do Azure com o comando [az acs create](/cli/azure/acs#create). 

O exemplo a seguir cria um cluster chamado *mySwarmCluster* com um nó mestre do Linux e três nós de agente do Linux.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type Swarm --resource-group myResourceGroup --generate-ssh-keys
```

Em alguns casos, como em uma avaliação limitada, uma assinatura do Azure terá acesso limitado aos recursos do Azure. Se a implantação falhar devido à limitação nos núcleos disponíveis, reduza a contagem de agentes padrão, adicionando `--agent-count 1` ao comando [az acs create](/cli/azure/acs#create). 

Após alguns minutos, o comando é concluído e retorna informações formatadas em json sobre o cluster.

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Em todo este guia rápido, é necessário o endereço IP do mestre do Docker Swarm e do pool de agentes do Docker. Execute o comando a seguir para retornar os dois endereços IP.


```bash
az network public-ip list --resource-group myResourceGroup --query "[*].{Name:name,IPAddress:ipAddress}" -o table
```

Saída:

```bash
Name                                                                 IPAddress
-------------------------------------------------------------------  -------------
swarmm-agent-ip-myswarmcluster-myresourcegroup-d5b9d4agent-66066781  52.179.23.131
swarmm-master-ip-myswarmcluster-myresourcegroup-d5b9d4mgmt-66066781  52.141.37.199
```

Crie um túnel SSH para o mestre Swarm. Substitua `IPAddress` pelo endereço IP do mestre Swarm.

```bash
ssh -p 2200 -fNL 2375:localhost:2375 azureuser@IPAddress
```

Defina a variável de ambiente `DOCKER_HOST`. Isso permite que você execute comandos do docker no Docker Swarm sem precisar especificar o nome do host.

```bash
export DOCKER_HOST=:2375
```

Agora, você está pronto para executar os serviços do Docker no Docker Swarm.


## <a name="run-the-application"></a>Executar o aplicativo

Crie um arquivo chamado `docker-compose.yaml` e copie o seguinte conteúdo para ele.

```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:redis-v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Execute o comando a seguir para criar o serviço Voto do Azure.

```bash
docker-compose up -d
```

Saída:

```bash
Creating network "user_default" with the default driver
Pulling azure-vote-front (microsoft/azure-vote-front:redis-v1)...
swarm-agent-EE873B23000005: Pulling microsoft/azure-vote-front:redis-v1...
swarm-agent-EE873B23000004: Pulling microsoft/azure-vote-front:redis-v1... : downloaded
Pulling azure-vote-back (redis:latest)...
swarm-agent-EE873B23000004: Pulling redis:latest... : downloaded
Creating azure-vote-front ... 
Creating azure-vote-back ... 
Creating azure-vote-front
Creating azure-vote-back ...
```

## <a name="test-the-application"></a>Testar o aplicativo

Navegue até o endereço IP do pool de agentes do Swarm para testar o aplicativo Voto do Azure.

![Imagem de navegação para o Voto do Azure](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Excluir cluster
Quando o cluster não for mais necessário, você poderá usar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Obter o código

Neste guia de início rápido, foram usadas imagens de contêiner criadas previamente para criar um serviço do Docker. O código do aplicativo relacionado, o Dockerfile e o arquivo Compose estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você implantou um cluster do Docker Swarm e um aplicativo de com vários contêineres nele.

Para aprender mais sobre como integrar o Docker Swarm no Visual Studio Team Services, continue no CI/CD com Docker Swarm e VSTS.

> [!div class="nextstepaction"]
> [CI/CD com Docker Swarm e VSTS](./container-service-docker-swarm-setup-ci-cd.md)