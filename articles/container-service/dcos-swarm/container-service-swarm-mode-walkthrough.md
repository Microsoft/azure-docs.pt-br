---
title: '(PRETERIDO) Início rápido: cluster do Azure Docker CE para Linux'
description: Aprenda rapidamente a criar um cluster do Docker CE para os contêineres do Linux no Serviço de Contêiner do Azure com a CLI do Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/16/2018
ms.author: iainfou
ms.custom: ''
ms.openlocfilehash: a7a7455ce9167a9c480d317d50fdce49e2ef06a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60721770"
---
# <a name="deprecated-deploy-docker-ce-cluster"></a>(PRETERIDO) Implantar um cluster do Docker CE

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste guia de início rápido, um cluster do Docker CE é implantado usando a CLI do Azure. Em seguida, um aplicativo com vários contêineres, composto por um front-end na Web e uma instância Redis, é executado no cluster. Depois de concluído, o aplicativo pode ser acessado pela internet.

O Docker CE no Serviço de Contêiner do Azure está na versão prévia e **não deve ser usado para cargas de trabalho de produção**.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um grupo lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *westus2*.

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Saída:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westus2",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Criar um cluster do Docker Swarm

Crie um cluster do Docker CE no Serviço de Contêiner do Azure com o comando [az acs create](/cli/azure/acs#az-acs-create). Para obter informações sobre a disponibilidade de região do Docker CE, consulte [Regiões do ACS para Docker CE](https://github.com/Azure/ACS/blob/master/announcements/2017-08-04_additional_regions.md)

O exemplo a seguir cria um cluster chamado *mySwarmCluster* com um nó mestre do Linux e três nós de agente do Linux.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

Em alguns casos, como em uma avaliação limitada, uma assinatura do Azure terá acesso limitado aos recursos do Azure. Se a implantação falhar devido à limitação nos núcleos disponíveis, reduza a contagem de agentes padrão, adicionando `--agent-count 1` ao comando [az acs create](/cli/azure/acs#az-acs-create). 

Após alguns minutos, o comando é concluído e retorna informações formatadas em JSON sobre o cluster.

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Em todo este guia rápido, é necessário o FQDN do mestre do Docker Swarm e do pool de agentes do Docker. Execute o seguinte comando para retornar os FQDNs do mestre e dos agentes.


```bash
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

Saída:

```bash
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

Crie um túnel SSH para o mestre Swarm. Substitua `MasterFQDN` pelo endereço FQDN do mestre Swarm.

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

Defina a variável de ambiente `DOCKER_HOST`. Isso permite que você execute comandos do docker no Docker Swarm sem precisar especificar o nome do host.

```bash
export DOCKER_HOST=localhost:2374
```

Agora, você está pronto para executar os serviços do Docker no Docker Swarm.


## <a name="run-the-application"></a>Executar o aplicativo

Crie um arquivo chamado `azure-vote.yaml` e copie o seguinte conteúdo para ele.


```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Execute o comando [docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/) para criar o serviço de Voto do Azure.

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

Saída:

```bash
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

Use o comando [docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/) para retornar o status de implantação do aplicativo.

```bash
docker stack ps azure-vote
```

Assim que o `CURRENT STATE` de cada serviço for `Running`, o aplicativo estará pronto.

```bash
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>Testar o aplicativo

Navegue até o FQDN do pool de agentes do Swarm para testar o aplicativo de Voto do Azure.

![Imagem de navegação para o Voto do Azure](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Excluir cluster
Quando o cluster não for mais necessário, você poderá usar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Obter o código

Neste guia de início rápido, foram usadas imagens de contêiner criadas previamente para criar um serviço do Docker. O código do aplicativo relacionado, o Dockerfile e o arquivo Compose estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você implantou um cluster do Docker Swarm e um aplicativo de com vários contêineres nele.

Para saber mais sobre como integrar o Docker Swarm ao Azure DevOps, continue na CI/CD com o Docker Swarm e o Azure DevOps.

> [!div class="nextstepaction"]
> [CI/CD com o Docker Swarm e o Azure DevOps](./container-service-docker-swarm-setup-ci-cd.md)
