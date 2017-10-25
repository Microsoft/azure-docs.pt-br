---
title: "Usar a extensão de VM do Docker do Azure | Microsoft Docs"
description: "Saiba como usar a extensão de VM do Docker para implantar de maneira rápida e segura um ambiente Docker no Azure usando modelos do Resource Manager e a CLI 2.0 do Azure"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 0cef78edaeec9d45aa733b1912d82d5a058ba289
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Criar um ambiente de Docker no Azure usando a extensão de VM do Docker
O Docker é uma plataforma popular de geração de imagens e gerenciamento de contêineres que permite que você trabalhe rapidamente com contêineres no Linux. No Azure, há várias maneiras de que você pode implantar o Docker de acordo com suas necessidades. Este artigo se concentra no uso de modelos do Azure Resource Manager e da extensão de VM do Docker com a CLI 2.0 do Azure. Você também pode executar essas etapas com a [CLI do Azure 1.0](dockerextension-nodejs.md).

## <a name="azure-docker-vm-extension-overview"></a>Visão geral da extensão de VM do Docker do Azure
A Extensão de VM do Docker do Azure instala e configura o daemon do Docker, o cliente do Docker e o Docker Compose em sua VM (máquina virtual) Linux. Usando a extensão de VM do Docker do Azure, você tem mais controle e recursos do que simplesmente usando a Máquina do Docker ou criando o host do Docker você mesmo. Esses recursos adicionais, como [Docker Compose](https://docs.docker.com/compose/overview/), tornam a extensão de VM do Docker do Azure adequada para ambientes de produção ou de desenvolvedor mais robustos.

Para obter mais informações sobre os diferentes métodos de implantação, incluindo o uso de Máquina do Docker e dos Serviços de Contêiner do Azure, consulte os seguintes artigos:

* Para fazer rapidamente o protótipo de um aplicativo, você pode criar um único host do Docker usando a [Máquina do Docker](docker-machine.md).
* Você também pode implantar um [cluster do Docker Swarm nos Serviços de Contêiner do Azure](../../container-service/dcos-swarm/container-service-deployment.md) para criar ambientes escalonáveis, prontos para produção que fornecem ferramentas de gerenciamento e agendamento adicionais.

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Implantar um modelo com a extensão de VM do Docker do Azure
Vamos usar um modelo existente de início rápido para criar uma VM do Ubuntu que use a extensão de VM do Docker do Azure para instalar e configurar o host do Docker. Você pode conferir o modelo aqui: [Simple deployment of an Ubuntu VM with Docker (Implantação simples de uma VM do Ubuntu com o Docker)](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). É preciso ter a [CLI 2.0 do Azure](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando [az login](/cli/azure/#login).

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Em seguida, implante uma VM com [az group deployment create](/cli/azure/group/deployment#create), o que inclui a extensão da VM do Docker do Azure [deste modelo do Azure Resource Manager no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Forneça seus próprios valores exclusivos para *newStorageAccountName*, *adminUsername*, *adminPassword* e *dnsNameForPublicIP* da seguinte maneira:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Leva alguns minutos para a conclusão da implantação. Após a conclusão da implantação, [vá para a próxima etapa](#deploy-your-first-nginx-container) a fim de usar SSH em sua VM. 

Como opção, para, em vez disso, retornar o controle ao prompt e permitir que a implantação continue em segundo plano, adicione o sinalizador `--no-wait` ao comando anterior. Esse processo permite que você execute outro trabalho na CLI enquanto a implantação continua por alguns minutos. 

Você pode exibir detalhes sobre o status de host do Docker usando o comando [az vm show](/cli/azure/vm#show). O exemplo a seguir verifica o status da VM chamada *myDockerVM* (o nome padrão do modelo – não altere esse nome) no grupo de recursos chamado *myResourceGroup*:

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --query [provisioningState] \
    --output tsv
```

Quando esse comando retornar *Succeeded*, a implantação terá sido concluída e você poderá usar SSH na VM na etapa a seguir.

## <a name="deploy-your-first-nginx-container"></a>Implantar seu primeiro contêiner NGINX
Para exibir detalhes de sua VM, incluindo o nome DNS, use [az vm show](/cli/azure/vm#show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

SSH para seu novo host do Docker. Fornece seu próprio nome DNS da seguinte maneira:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Após fazer logon no host do Docker, executaremos um contêiner NGINX:

```bash
sudo docker run -d -p 80:80 nginx
```

A saída é semelhante ao exemplo a seguir, conforme a imagem de NGINX é baixada e um contêiner é iniciado:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Verifique o status dos contêineres em execução no host do Docker da seguinte maneira:

```bash
sudo docker ps
```

A saída é semelhante ao exemplo a seguir, mostrando que o contêiner NGINX está em execução nas portas TCP 80 e 443 e está sendo encaminhado:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Para ver seu contêiner em ação, abra um navegador da Web e digite o nome DNS do host do Docker:

![Contêiner ngnix em execução](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Referência de modelo da extensão de VM do Docker do Azure
O exemplo anterior usa um modelo de início rápido existente. Você também pode implantar a extensão de VM do Docker do Azure com seus próprios modelos do Resource Manager. Para fazer isso, adicione o seguinte a seus modelos do Resource Manager, definindo o `vmName` da sua VM adequadamente:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.*",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Você pode encontrar um passo a passo mais detalhado de como usar modelos do Gerenciador de Recursos lendo a [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>Próximas etapas
Você poderá [configurar a porta TCP do daemon do Docker](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), compreender a [segurança do Docker](https://docs.docker.com/engine/security/security/) ou implantar contêineres usando o [Docker Compose](https://docs.docker.com/compose/overview/). Para obter mais informações sobre a extensão de VM do Docker do Azure em si, consulte o [projeto GitHub](https://github.com/Azure/azure-docker-extension/).

Leia mais informações sobre as opções de implantação adicionais do Docker no Azure:

* [Usar o computador Docker com o driver do Azure](docker-machine.md)  
* [Introdução ao Docker e Compose para definir e executar um aplicativo de vários contêineres em uma máquina virtual do Azure](docker-compose-quickstart.md).
* [Implantar um cluster do Serviço de Contêiner do Azure](../../container-service/dcos-swarm/container-service-deployment.md)

