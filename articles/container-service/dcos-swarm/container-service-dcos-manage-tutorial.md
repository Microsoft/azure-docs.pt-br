---
title: "Tutorial de Serviço de Contêiner do Azure – gerenciar DC/SO | Microsoft Docs"
description: "Tutorial de Serviço de Contêiner do Azure – gerenciar DC/SO"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Kubernetes, DC/SO, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e93f782c26c32f97749e817ec59ee3c2ecb7e119
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-service-tutorial---manage-dcos"></a>Tutorial de Serviço de Contêiner do Azure – gerenciar DC/SO

DC/SO fornece uma plataforma distribuída para executar aplicativos modernos e em contêineres. Com o Serviço de Contêiner do Azure, o provisionamento de um cluster de DC/SO pronto para produção é simples e rápido. Este início rápido detalha as etapas básicas necessárias para implantar um cluster do DC/SO e executar uma carga de trabalho básica.

> [!div class="checklist"]
> * Criar um cluster de DC/SO do ACS
> * Conectar-se ao cluster
> * Instalar a CLI de DC/SO
> * Implantar um aplicativo para o cluster
> * Dimensionar um aplicativo no cluster
> * Dimensionar os nós de cluster de DC/SO
> * Gerenciamento básico do DC/SO
> * Excluir o cluster de DC/SO

Este tutorial requer a CLI do Azure, versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar atualizar, confira [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-dcos-cluster"></a>Criar cluster de DC/SO

Primeiro, crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O seguinte exemplo cria um grupo de recursos chamado *myResourceGroup* no local *westeurope*.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Em seguida, crie um cluster de DC/SO com o comando [az acs create](/cli/azure/acs#create).

O exemplo a seguir cria um cluster de DC/SO chamado *myDCOSCluster* e cria as chaves de SSH se elas ainda não existirem. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

Após alguns minutos, o comando é concluído e retorna informações sobre a implantação.

## <a name="connect-to-dcos-cluster"></a>Conectar-se ao cluster de DC/SO

Quando um cluster de DC/SO tiver sido criado, ele poderá ser acessado por meio de um túnel SSH. Execute o seguinte comando para retornar o endereço IP público do mestre de DC/SO. Esse endereço IP é armazenado em uma variável e usado na próxima etapa.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Para criar o túnel SSH, execute o seguinte comando e siga as instruções na tela. Se a porta 80 já estiver em uso, o comando falhará. Atualize a porta em túnel para uma que não esteja em uso, como `85:localhost:80`. 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

## <a name="install-dcos-cli"></a>Instalar CLI de DC/SO

Instale a CLI de DC/SO usando o comando [az acs dcos install-cli](/azure/acs/dcos#install-cli). Se você estiver usando o Azure CloudShell, a CLI do DC/SO já estará instalada. Se você estiver executando a CLI do Azure em macOS ou Linux, talvez precise executar o comando com sudo.

```azurecli
az acs dcos install-cli
```

Antes de poder usar a CLI com o cluster, ela deve ser configurada para usar o túnel SSH. Para fazer isso, execute o comando a seguir, ajustando a porta, se necessário.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Executar um aplicativo

O mecanismo de agendamento padrão para um cluster de DC/SO do ACS é o Marathon. O Marathon é usado para iniciar um aplicativo e gerenciar o estado do aplicativo no cluster de DC/SO. Para agendar um aplicativo por meio do Marathon, crie um arquivo chamado **marathon-app.json** e copie o seguinte conteúdo para ele. 

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Execute o seguinte comando para agendar o aplicativo para ser executado no cluster do DC/SO.

```azurecli
dcos marathon app add marathon-app.json
```

Para ver o status da implantação para o aplicativo, execute o comando a seguir.

```azurecli
dcos marathon app list
```

Quando o valor da coluna **TAREFAS** mudar de *0/1* para *1/1*, a implantação do aplicativo terá sido concluída.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>Dimensionar aplicativo Marathon

No exemplo anterior, um aplicativo de instância única foi criado. Para atualizar essa implantação para que as três instâncias do aplicativo estejam disponíveis, abra o arquivo **marathon-app.json** e atualize a propriedade da instância para 3.

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 3,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Atualizar o aplicativo usando o comando `dcos marathon app update`.

```azurecli
dcos marathon app update demo-app-private < marathon-app.json
```

Para ver o status da implantação para o aplicativo, execute o comando a seguir.

```azurecli
dcos marathon app list
```

Quando o valor da coluna **TAREFAS** mudar de *1/3* para *3/1*, a implantação do aplicativo terá sido concluída.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>Execute o aplicativo acessível pela Internet

O cluster DC/SO do ACS consiste em dois conjuntos de nó, um público, que pode ser acessado pela Internet, e um privado, que não está acessível pela Internet. O conjunto padrão é o de nós privados, que foi usado no último exemplo.

Para disponibilizar um aplicativo pela Internet, implante-o no conjunto de nós públicos. Para fazer isso, dê ao objeto `acceptedResourceRoles` um valor de `slave_public`.

Crie um arquivo chamado **nginx-public.json** e copie o seguinte conteúdo para ele.

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

Execute o seguinte comando para agendar o aplicativo para ser executado no cluster do DC/SO.

```azurecli 
dcos marathon app add nginx-public.json
```

Obtenha o endereço IP público dos agentes de cluster público DC/SO.

```azurecli 
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Navegar para esse endereço retorna o site NGINX padrão.

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>Cluster de DC/SO de escala

Nos exemplos anteriores, um aplicativo foi dimensionado para várias instâncias. A infraestrutura do DC/SO também pode ser dimensionada para fornecer mais ou menos capacidade de computação. Isso é feito com o comando [az acs scale](). 

Para ver a contagem atual de agentes do DC/SO, use o comando [az acs show](/cli/azure/acs#show).

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

Para aumentar a contagem para 5, use o comando [az acs scale](/cli/azure/acs#scale). 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>Excluir cluster de DC/SO

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, o cluster DC/SO todos os recursos relacionados.

```azurecli 
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre a tarefa básica de gerenciamento de DC/SO, incluindo as seguintes. 

> [!div class="checklist"]
> * Criar um cluster de DC/SO do ACS
> * Conectar-se ao cluster
> * Instalar a CLI de DC/SO
> * Implantar um aplicativo para o cluster
> * Dimensionar um aplicativo no cluster
> * Dimensionar os nós de cluster de DC/SO
> * Excluir o cluster de DC/SO

Avance para o próximo tutorial para saber mais sobre o aplicativo de balanceamento de carga no DC/SO no Azure. 

> [!div class="nextstepaction"]
> [Aplicativos de balanceamento de carga](container-service-load-balancing.md)