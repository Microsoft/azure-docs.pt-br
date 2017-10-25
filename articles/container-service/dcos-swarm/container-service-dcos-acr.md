---
title: Usando ACR com um cluster DC/OS do Azure | Microsoft Docs
description: "Usar um Registro de Contêiner do Azure com um cluster DC/OS no Serviço de Contêiner do Azure"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service, acr, azure-container-registry
keywords: "Docker, Contêineres, Microsserviços, Mesos, Azure, FileShare, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 7fdcff5ed93e56e4ac70dd9dcee0056a4cbef0bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>Usar ACR com um cluster DC/OS para implantar seu aplicativo

Neste artigo, exploraremos como usar o Registro de Contêiner do Azure com um cluster de DC/SO. Usar o ACR permite armazenar e gerenciar imagens de contêiner de forma privada. Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Implantar o Registro de Contêiner do Azure (se necessário)
> * Configurar a autenticação do ACR em um cluster de DC/SO
> * Uma imagem carregada no Registro de Contêiner do Azure
> * Executar uma imagem de contêiner do Registro de Contêiner do Azure

É necessário um cluster de DC/SO do ACS para concluir as etapas neste tutorial. Se necessário, [este exemplo de script](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) pode criar um para você.

Este tutorial requer a CLI do Azure, versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar atualizar, confira [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Implantar o Registro de Contêiner do Azure

Se necessário, crie um Registro de Contêiner do Azure com o comando [az acr create](/cli/azure/acr#create). 

O exemplo a seguir cria um registro com um nome gerado aleatoriamente. O registro também é configurado com uma conta do administrador usando o argumento `--admin-enabled`.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic --admin-enabled true
```

Quando o registro tiver sido criado, a CLI do Azure gerará dados semelhantes aos seguintes. Anote `name` e `loginServer`, eles serão usados em etapas posteriores.

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Obtenha as credenciais de registro de contêiner usando o comando [az acr credential show](/cli/azure/acr/credential). Substitua o `--name` pelo anotado na última etapa. Anote uma senha, ela será necessária em uma etapa posterior.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Para obter mais informações sobre o Registro de Contêiner do Azure, consulte [Introdução aos registros de contêiner do Docker privado](../../container-registry/container-registry-intro.md). 

## <a name="manage-acr-authentication"></a>Gerenciar a autenticação do ACR

A maneira convencional de enviar uma imagem por push e de efetuar pull dela de um registro privado é primeiro autenticar no registro. Para fazer isso, use o comando `docker login` em qualquer cliente que precise acessar o registro privado. Como um cluster de DC/SO pode conter muitos nós que precisam ser autenticados com o ACR, é útil automatizar esse processo em cada nó. 

### <a name="create-shared-storage"></a>Criar um armazenamento compartilhado

Esse processo usa um compartilhamento de arquivos do Azure que foi montado em cada nó no cluster. Se você ainda não definiu o armazenamento compartilhado, consulte [Criar e montar um compartilhamento de arquivos em um cluster de controlador de domínio/sistema operacional](container-service-dcos-fileshare.md).

### <a name="configure-acr-authentication"></a>Configurar a autenticação do ACR

Primeiro, obtenha o FQDN do mestre de DC/SO e armazene-o em uma variável.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Crie uma conexão SSH com o mestre (ou o primeiro mestre) do cluster baseado no DC/SO. Atualize o nome de usuário se um valor não padrão tiver sido usado ao criar o cluster.

```azurecli-interactive
ssh azureuser@$FQDN
```

Execute o seguinte comando para fazer logon no Registro de Contêiner do Azure. Substitua `--username` pelo o nome do registro de contêiner e `--password` por uma das senhas fornecidas. Substitua o último argumento *mycontainerregistry.azurecr.io* no exemplo pelo nome de loginServer de registro de contêiner. 

Este comando armazena os valores de autenticação localmente no caminho `~/.docker`.

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

Crie um arquivo compactado que contenha os valores de autenticação do registro de contêiner.

```azurecli-interactive
tar czf docker.tar.gz .docker
```

Copie esse arquivo no armazenamento de cluster compartilhado. Esta etapa disponibiliza o arquivo em todos os nós de cluster de DC/SO.

```azurecli-interactive
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>Carregar imagem no ACR

Agora de um computador de desenvolvimento ou de qualquer outro sistema com o Docker instalado, crie uma imagem e carregue-a no Registro de Contêiner do Azure.

Crie um contêiner da imagem do Ubuntu.

```azurecli-interactive
docker run ubuntu --name base-image
```

Agora, capture o contêiner em uma nova imagem. O nome da imagem precisa incluir o nome do `loginServer` do registro de contêiner com o formato `loginServer/imageName`.

```azurecli-interactive
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
````

Faça logon no Registro de Contêiner do Azure. Substitua o nome pelo nome de loginServer, --username pelo o nome do registro de contêiner e --password por uma das senhas fornecidas.

```azurecli-interactive
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Por fim, carregue a imagem no registro do ACR. Este exemplo carrega uma imagem chamada dcos-demo.

```azurecli-interactive
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>Execute uma imagem do ACR

Para usar uma imagem do registro ACR, crie um arquivo chamado *acrDemo.json* e copie o seguinte texto nele. Substitua o nome da imagem pelo nome de loginServer do registro de contêiner e pelo nome da imagem, por exemplo `loginServer/imageName`. Anote a propriedade `uris`. Esta propriedade contém o local do arquivo de autenticação do registro de contêiner, que nesse caso é o compartilhamento de arquivos do Azure que está montado em cada nó do cluster de DC/SO.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

Implante o aplicativo com a CLI do DC/OC.

```azurecli-interactive
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou o DC/SO para usar o Registro de Contêiner do Azure, incluindo as seguintes tarefas:

> [!div class="checklist"]
> * Implantar o Registro de Contêiner do Azure (se necessário)
> * Configurar a autenticação do ACR em um cluster de DC/SO
> * Uma imagem carregada no Registro de Contêiner do Azure
> * Executar uma imagem de contêiner do Registro de Contêiner do Azure
