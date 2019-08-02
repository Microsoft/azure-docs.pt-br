---
title: Como criar imagens de VM Linux do Azure com o Packer | Microsoft Docs
description: Saiba como usar o Packer para criar imagens de máquinas virtuais Linux no Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.openlocfilehash: 4dcf6f2e26a2cc589e350ee2b40c10b85786d4be
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671776"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Como usar o Packer para criar imagens de máquina virtual Linux no Azure
Cada VM (máquina virtual) no Azure é criada com base em uma imagem que define a distribuição do Linux e a versão do sistema operacional. As imagens podem incluir configurações e aplicativos pré-instalados. O Azure Marketplace fornece várias imagens internas e de terceiros para as distribuições e os ambientes de aplicativo mais comuns ou você pode criar suas próprias imagens personalizadas adequadas às suas necessidades. Este artigo fornece detalhes sobre como usar a ferramenta de software livre [Packer](https://www.packer.io/) para definir e criar imagens personalizadas no Azure.

> [!NOTE]
> Agora, o Azure tem um serviço, o construtor de imagens do Azure (visualização), para definir e criar suas próprias imagens personalizadas. Construtor de imagens do Azure é criado em Packer, portanto, você pode até usar seus scripts existentes do Packer shell provisionador com ele. Para se familiarizar com o construtor de imagens do Azure, consulte [criar uma VM do Linux com o construtor de imagens do Azure](image-builder.md).


## <a name="create-azure-resource-group"></a>Criar um grupo de recursos do Azure
Durante o processo de build, o Packer cria recursos temporários do Azure conforme cria a VM de origem. Para capturar essa VM de origem para uso como uma imagem, você precisa definir um grupo de recursos. A saída do processo de build do Packer é armazenada nesse grupo de recursos.

Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Criar as credenciais do Azure
O Packer se autentica no Azure usando uma entidade de serviço. Uma entidade de serviço do Azure é uma identidade de segurança que você pode usar com aplicativos, serviços e ferramentas de automação como o Packer. Você controla e define as permissões referentes a quais operações a entidade de serviço pode executar no Azure.

Crie uma entidade de serviço com [az ad sp create-for-rbac](/cli/azure/ad/sp) e gere as credenciais necessárias para o Packer:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Um exemplo da saída dos comandos anteriores é o seguinte:

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Para se autenticar no Azure, você também precisa obter sua ID de assinatura do Azure com [az account show](/cli/azure/account):

```azurecli
az account show --query "{ subscription_id: id }"
```

Você pode usar a saída desses dois comandos na próxima etapa.


## <a name="define-packer-template"></a>Definir modelo do Packer
Para criar imagens, você cria um modelo como um arquivo JSON. No modelo, você define construtores e provisionadores que executam o processo de build real. O Packer tem um [provisionador do Azure](https://www.packer.io/docs/builders/azure.html) que permite definir recursos do Azure, como as credenciais da entidade de serviço criadas na etapa anterior.

Crie um arquivo chamado *ubuntu.json* e cole o conteúdo a seguir. Insira seus próprios valores para o seguinte:

| Parâmetro                           | Onde obter |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Primeira linha da saída do comando create `az ad sp` – *appId* |
| *client_secret*                     | Segunda linha da saída do comando create `az ad sp` – *password* |
| *tenant_id*                         | Terceira linha da saída do comando create `az ad sp` – *tenant* |
| *subscription_id*                   | Saída do comando `az account show` |
| *managed_image_resource_group_name* | Nome do grupo de recursos que você criou na primeira etapa |
| *managed_image_name*                | Nome da imagem de disco gerenciado que é criada |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Este modelo cria uma imagem do Ubuntu 16.04 LTS, instala o NGINX e, em seguida, desprovisiona a VM.

> [!NOTE]
> Se você expandir este modelo para provisionar as credenciais do usuário, ajuste o comando do provisionador que desprovisiona o agente do Azure para que ele indique `-deprovision`, em vez de `deprovision+user`.
> O sinalizador `+user` remove todas as contas de usuário da VM de origem.


## <a name="build-packer-image"></a>Criar uma imagem do Packer
Se você ainda não tiver o Packer instalado no computador local, [siga as instruções de instalação do Packer](https://www.packer.io/docs/install/index.html).

Crie a imagem especificando o arquivo de modelo do Packer da seguinte maneira:

```bash
./packer build ubuntu.json
```

Um exemplo da saída dos comandos anteriores é o seguinte:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

São necessários alguns minutos para que o Packer crie a VM, execute os provisionadores e limpe a implantação.


## <a name="create-vm-from-azure-image"></a>Criar uma VM com base em uma Imagem do Azure
Agora você pode criar uma VM com base na Imagem com [az vm create](/cli/azure/vm). Especifique a Imagem criada com o parâmetro `--image`. O exemplo a seguir cria uma VM chamada *myVM* com base em *myPackerImage* e gera as chaves SSH, caso ainda não existam:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Se você quiser criar VMs em um grupo de recursos diferente ou a região da imagem Packer, especifique a ID da imagem em vez do nome da imagem. Você pode obter a ID de imagem com [Mostrar de imagem az](/cli/azure/image#az-image-show).

São necessários alguns minutos para criar a VM. Depois que a VM for criada, amote o `publicIpAddress` exibido pela CLI do Azure. Esse endereço é usado para acessar o site do NGINX em um navegador da Web.

Para permitir o tráfego da web para acessar sua VM, abra a porta 80 da Internet com [az vm open-port](/cli/azure/vm):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testar a VM e o NGINX
Agora, abra um navegador da Web e digite `http://publicIpAddress` na barra de endereços. Forneça seu próprio endereço de IP público do processo de criação da máquina virtual. A página padrão do NGINX é exibida como no seguinte exemplo:

![Site padrão NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Próximas etapas
Você também pode usar scripts existentes de provisionador do Packer com [Azure Image Builder](image-builder.md).
