---
title: "Compartilhamento de arquivos para o cluster do sistema operacional e do controlador de domínio do Azure | Microsoft Docs"
description: "Criar e montar um compartilhamento de arquivos em um cluster de controlador de domínio/sistema operacional no Serviço de Contêiner do Azure"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Mesos, Azure, FileShare, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: a5905cac12f52f94a5722cc01495d5c1168634f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>Criar e montar um compartilhamento de arquivos em um cluster de controlador de domínio/sistema operacional
Este tutorial fornece detalhes sobre como criar um compartilhamento de arquivos no Azure e montá-lo em cada agente e o mestre do cluster de DC/SO. A configuração de um compartilhamento de arquivos facilita o compartilhamento de arquivos no cluster, como configuração, acesso, logs e muito mais. As tarefas a seguir são concluídas neste tutorial:

> [!div class="checklist"]
> * Criar uma conta de armazenamento do Azure
> * Criar um compartilhamento de arquivos
> * Montar o compartilhamento no cluster DC/SO

É necessário um cluster de DC/SO do ACS para concluir as etapas neste tutorial. Se necessário, [este exemplo de script](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) pode criar um para você.

Este tutorial requer a CLI do Azure, versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar atualizar, confira [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Criar um compartilhamento de arquivos no Microsoft Azure

Antes de usar um compartilhamento de arquivos do Azure com um cluster ACS DC/SO, a conta de armazenamento e o compartilhamento de arquivo devem ser criados. Execute o script a seguir para criar o compartilhamento de arquivo e armazenamento. Atualize os parâmetros com aqueles do seu ambiente.

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Montar o compartilhamento no cluster

Em seguida, o compartilhamento de arquivos precisa ser montado em cada máquina virtual dentro de seu cluster. Essa tarefa é concluída usando o protocolo/ferramenta cifs. A operação de montagem pode ser concluída manualmente em cada nó do cluster ou executando um script em cada nó no cluster.

Neste exemplo, dois scripts são executados, um para montar o compartilhamento de arquivos do Azure e outro para executar esse script em cada nó do cluster de DC/SO.

Primeiro, o nome da conta de armazenamento do Azure e a chave de acesso são necessários. Execute os seguintes comandos para obter essas informações. Tome nota de cada um, esses valores são usados em uma etapa posterior.

Nome da conta de armazenamento:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Chave de acesso da conta de armazenamento:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

Em seguida, obtenha o FQDN do mestre de DC/SO e armazene-o em uma variável.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Copie sua chave privada para o nó principal. Essa chave é necessária para criar uma conexão ssh com todos os nós no cluster. Atualize o nome de usuário se um valor não padrão tiver sido usado ao criar o cluster. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Crie uma conexão SSH com o mestre (ou o primeiro mestre) do cluster baseado no DC/SO. Atualize o nome de usuário se um valor não padrão tiver sido usado ao criar o cluster.

```azurecli-interactive
ssh azureuser@$FQDN
```

Crie um arquivo chamado **cifsMount.sh** e copie o seguinte conteúdo para ele. 

Esse script é usado para montar o compartilhamento de arquivos do Azure. Atualize as variáveis `STORAGE_ACCT_NAME` e `ACCESS_KEY` com as informações coletadas anteriormente.

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
Crie um segundo arquivo denominado **getNodesRunScript.sh** e copie o seguinte conteúdo para ele. 

Esse script descobre todos os nós de cluster e, em seguida, executa o script **cifsMount.sh** para montar o compartilhamento de arquivos em cada um.

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

Execute o script para montar o compartilhamento de arquivos do Azure em todos os nós do cluster.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

O compartilhamento de arquivos agora está acessível no `/mnt/share/dcosshare` em cada nó do cluster.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, um compartilhamento de arquivos do Azure foi disponibilizado a um cluster do DC/SO usando as etapas:

> [!div class="checklist"]
> * Criar uma conta de armazenamento do Azure
> * Criar um compartilhamento de arquivos
> * Montar o compartilhamento no cluster DC/SO

Avance para o próximo tutorial para saber mais sobre como integrar um Registro de Contêiner do Azure ao DC/SO no Azure.  

> [!div class="nextstepaction"]
> [Aplicativos de balanceamento de carga](container-service-dcos-acr.md)
