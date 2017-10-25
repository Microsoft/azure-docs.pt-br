---
title: "Início rápido do Serviço de Contêiner do Azure – implantar cluster de DC/SO | Microsoft Docs"
description: "Início rápido do Serviço de Contêiner do Azure – implantar cluster de DC/SO"
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
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 8070d224fe6281e61f67483d4f1dd905a2ab99eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-dcos-cluster"></a>Implantar um cluster de DC/SO

DC/SO fornece uma plataforma distribuída para executar aplicativos modernos e em contêineres. Com o Serviço de Contêiner do Azure, o provisionamento de um cluster de DC/SO pronto para produção é simples e rápido. Este início rápido detalha as etapas básicas necessárias para implantar um cluster do DC/SO e executar uma carga de trabalho básica.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este tutorial requer a CLI do Azure, versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar atualizar, confira [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>Criar cluster de DC/SO

Crie um cluster de DC/SO com o comando [az acs create](/cli/azure/acs#create).

O exemplo a seguir cria um cluster de DC/SO chamado *myDCOSCluster* e cria as chaves de SSH se elas ainda não existirem. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

Em alguns casos, como em uma avaliação limitada, uma assinatura do Azure terá acesso limitado aos recursos do Azure. Se a implantação falhar devido à limitação nos núcleos disponíveis, reduza a contagem de agentes padrão, adicionando `--agent-count 1` ao comando [az acs create](/cli/azure/acs#create). 

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

O túnel SSH pode ser testado navegando para `http://localhost`. Se uma porta que não a 80 for usada, ajuste o local de acordo. 

Se o túnel SSH tiver sido criado com êxito, o portal de DC/SO será retornado.

![INTERFACE DO USUÁRIO DE DC/SO](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>Instalar CLI de DC/SO

A interface de linha de comando do DC/SO é usada para gerenciar um cluster de DC/SO na linha de comando. Instale a CLI de DC/SO usando o comando [az acs dcos install-cli](/azure/acs/dcos#install-cli). Se você estiver usando o Azure CloudShell, a CLI do DC/SO já estará instalada. 

Se você estiver executando a CLI do Azure em macOS ou Linux, talvez precise executar o comando com sudo.

```azurecli
az acs dcos install-cli
```

Antes de poder usar a CLI com o cluster, ela deve ser configurada para usar o túnel SSH. Para fazer isso, execute o comando a seguir, ajustando a porta, se necessário.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Executar um aplicativo

O mecanismo de agendamento padrão para um cluster de DC/SO do ACS é o Marathon. O Marathon é usado para iniciar um aplicativo e gerenciar o estado do aplicativo no cluster de DC/SO. Para agendar um aplicativo por meio do Marathon, crie um arquivo chamado *marathon-app.json* e copie o seguinte conteúdo para ele. 

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
dcos marathon app add marathon-app.json
```

Para ver o status da implantação para o aplicativo, execute o comando a seguir.

```azurecli
dcos marathon app list
```

Quando o valor da coluna **ESPERANDO** mudar de *True* para *False*, a implantação do aplicativo terá sido concluída.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

Obtenha o endereço IP público dos agentes de cluster de DC/SO.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Navegar para esse endereço retorna o site NGINX padrão.

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>Excluir cluster de DC/SO

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, o cluster DC/SO todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um cluster de DC/SO e executou um contêiner simples do Docker no cluster. Para saber mais sobre o Serviço de Contêiner do Azure, prossiga para os tutoriais do ACS.

> [!div class="nextstepaction"]
> [Gerenciar um cluster de DC/SO do ACS](container-service-dcos-manage-tutorial.md)