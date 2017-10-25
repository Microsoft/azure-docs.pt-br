---
title: "Usar a extensão de VM do Docker do Azure com a CLI do Azure 1.0 | Microsoft Docs"
description: "Saiba como usar a extensão de VM do Docker para implantar de maneira rápida e segura um ambiente Docker no Azure usando modelos do Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: a3cbcf63533f4042dcd695e141655c5814bd7068
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension-with-the-azure-cli-10"></a>Criar um ambiente de Docker no Azure usando a extensão de VM do Docker com a CLI do Azure 1.0
O Docker é uma plataforma popular de geração de imagens e gerenciamento de contêineres que permite que você trabalhe rapidamente com contêineres no Linux (e também no Windows). No Azure, há várias maneiras de que você pode implantar o Docker de acordo com suas necessidades. Este artigo se concentra no uso de modelos do Azure Resource Manager e da extensão de VM do Docker. 

Para obter mais informações sobre os diferentes métodos de implantação, incluindo o uso de Máquina do Docker e dos Serviços de Contêiner do Azure, consulte os seguintes artigos:

* Para fazer rapidamente o protótipo de um aplicativo, você pode criar um único host do Docker usando a [Máquina do Docker](docker-machine.md).
* Para ambientes maiores, mais estáveis, você pode usar a extensão de VM do Docker do Azure, que também dá suporte ao [Docker Compose](https://docs.docker.com/compose/overview/) para gerar implantações de contêiner consistentes. Este artigo detalha o uso da extensão de VM do Docker do Azure.
* Você também pode implantar um [cluster do Docker Swarm nos Serviços de Contêiner do Azure](../../container-service/dcos-swarm/container-service-deployment.md) para criar ambientes escalonáveis, prontos para produção que fornecem ferramentas de gerenciamento e agendamento adicionais.

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI 1.0 do Azure](#azure-docker-vm-extension-overview) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI 2.0 do Azure](dockerextension.md) – nossa última geração de CLI para o modelo de implantação de gerenciamento de recursos 

## <a name="azure-docker-vm-extension-overview"></a>Visão geral da extensão de VM do Docker do Azure
A Extensão de VM do Docker do Azure instala e configura o daemon do Docker, o cliente do Docker e o Docker Compose em sua VM (máquina virtual) Linux. Usando a extensão de VM do Docker do Azure, você tem mais controle e recursos do que simplesmente usando a Máquina do Docker ou criando o host do Docker você mesmo. Esses recursos adicionais, como [Docker Compose](https://docs.docker.com/compose/overview/), tornam a extensão de VM do Docker do Azure adequada para ambientes de produção ou de desenvolvedor mais robustos.

Os modelos do Azure Resource Manager definem a estrutura inteira do seu ambiente. Modelos permitem criar e configurar recursos, como o host Docker VMs, armazenamento, RBAC (controles de acesso baseados em função) e diagnóstico. Você pode reutilizar esses modelos para criar implantações adicionais de maneira consistente. Para obter mais informações sobre o Azure Resource Manager e modelos, veja a [Visão geral do Resource Manager](../../azure-resource-manager/resource-group-overview.md). 

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Implantar um modelo com a extensão de VM do Docker do Azure
Vamos usar um modelo existente de início rápido para criar uma VM do Ubuntu que use a extensão de VM do Docker do Azure para instalar e configurar o host do Docker. Você pode conferir o modelo aqui: [Simple deployment of an Ubuntu VM with Docker (Implantação simples de uma VM do Ubuntu com o Docker)](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

É necessário que a [CLI do Azure mais recente](../../cli-install-nodejs.md) esteja instalada e com logon realizado por meio do modo do Resource Manager, da seguinte maneira:

```azurecli
azure config mode arm
```

Implante o modelo usando a CLI do Azure, especificando o URI do modelo. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *westus*. Use seu próprio nome de grupo de recursos e local da seguinte maneira:

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Responda aos prompts para nomear sua conta de armazenamento, forneça um nome de usuário, uma senha e um nome DNS. A saída deverá ser semelhante ao seguinte exemplo:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: azureuser
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicidns
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

A CLI do Azure retorna você ao prompt após apenas alguns segundos, mas seu host Docker ainda está sendo criado e configurado pela extensão de VM do Docker do Azure. Leva alguns minutos para a conclusão da implantação. Você pode exibir detalhes sobre o status de host de Docker usando o comando `azure vm show`.

O exemplo a seguir verifica o status da VM chamada *myDockerVM* (o nome padrão do modelo – não altere esse nome) no grupo de recursos chamado *myResourceGroup*. Insira o nome do grupo de recursos que você criou na etapa anterior:

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

A saída do comando `azure vm show` é semelhante ao exemplo a seguir:

```azurecli
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicdns.westus.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

Próximo à parte superior da saída, você vê o **ProvisioningState** da VM. Quando for exibido *Succeeded*, a implantação terá sido concluída e você poderá SSH na VM.

No final da saída, *FQDN* exibe o nome de domínio totalmente qualificado do host do Docker. Esse FQDN é usado para acessar o seu host do Docker via SSH nas etapas restantes.

## <a name="deploy-your-first-nginx-container"></a>Implantar seu primeiro contêiner nginx
Depois que a implantação for concluída, acesse o seu novo host do Docker via SSH, do computador local. Insira seu próprio nome de usuário e o FQDN da seguinte maneira:

```bash
ssh ops@mypublicdns.westus.cloudapp.azure.com
```

Após fazer logon no host do Docker, vamos executar um contêiner nginx:

```bash
sudo docker run -d -p 80:80 nginx
```

A saída é semelhante ao exemplo a seguir, conforme a imagem de nginx é baixada um contêiner é iniciado:

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

A saída é semelhante ao exemplo a seguir, mostrando que o contêiner nginx está em execução nas portas TCP 80 e 443 e está sendo encaminhado:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Para ver seu contêiner em ação, abra um navegador da Web e digite o nome FQDN do seu host do Docker:

![Contêiner ngnix em execução](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Referência de modelo da extensão de VM do Docker do Azure
O exemplo anterior usa um modelo de início rápido existente. Você também pode implantar a extensão de VM do Docker do Azure com seus próprios modelos do Resource Manager. Para fazer isso, adicione o seguinte a seus modelos do Resource Manager, definindo o *vmName* da sua VM adequadamente:

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
    "typeHandlerVersion": "1.1",
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

