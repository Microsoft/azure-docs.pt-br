---
title: Capturar uma VM do Linux do Azure para usar como modelo | Microsoft Docs
description: "Saiba como capturar e generalizar uma imagem de uma VM (máquina virtual) do Azure baseada no Linux criada com o modelo de implantação do Azure Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: b1164fbd816eea5189786850f096438e32f8f802
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Capturar uma máquina virtual do Linux em execução no Azure
Siga as etapas neste artigo para generalizar e capturar sua VM (máquina virtual) do Linux do Azure no modelo de implantação do Gerenciador de Recursos. Ao generalizar a VM, você remove informações de conta pessoal e prepara a VM a ser usada como uma imagem. Em seguida, você captura a imagem de um VHD (disco rígido virtual) generalizado para o sistema operacional, VHDs para discos de dados anexados e um [modelo do Resource Manager](../../azure-resource-manager/resource-group-overview.md) para novas implantações de VM. Este artigo fornece detalhes sobre como capturar uma imagem de VM com a CLI do Azure 1.0 para uma VM usando discos não gerenciados. Você também pode [capturar uma VM usando o Azure Managed Disks com a CLI 2.0 do Azure](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Os Managed Disks são tratados pela plataforma do Azure e não exigem nenhuma preparação ou local para armazenamento. Para saber mais, veja [Visão geral dos Azure Managed Disks](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Para criar VMs usando a imagem, configure recursos de rede para cada nova VM e use o modelo (um arquivo JavaScript Object Notation ou JSON) para implantá-lo por meio de imagens VHD capturadas. Dessa forma, você pode replicar uma VM com sua configuração atual de software, da mesma forma como usa imagens no Azure Marketplace.

> [!TIP]
> Se quiser criar uma cópia de sua VM Linux existente com seu estado especializado para depuração ou backup, confira [Criar uma cópia de uma máquina virtual do Linux em execução no Azure](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se você quiser carregar um VHD do Linux na VM local, confira [Carregar e criar uma VM do Linux por meio da imagem de disco personalizada](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI 1.0 do Azure](#before-you-begin) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI 2.0 do Azure](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa última geração de CLI para o modelo de implantação de gerenciamento de recursos

## <a name="before-you-begin"></a>Antes de começar
Verifique se os seguintes pré-requisitos foram atendidos:

* **VM do Azure criada no modelo de implantação do Gerenciador de Recursos** - se você ainda não criou uma VM do Linux, pode usar o [portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), a [CLI do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [modelos do Resource Manager](create-ssh-secured-vm-from-template.md). 
  
    Configure a VM conforme necessário. Por exemplo, [adicionar discos de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), aplicar atualizações e instalar aplicativos. 
* **CLI do Azure** - instalar a [CLI do Azure](../../cli-install-nodejs.md) em um computador local.

## <a name="step-1-remove-the-azure-linux-agent"></a>Etapa 1: remover o agente Linux do Azure
Primeiro, execute o comando **waagent** com o parâmetro **deprovision** na VM Linux. Esse comando exclui arquivos e dados para preparar a VM para generalização. Para obter detalhes, confira o [Guia do usuário do Azure Linux Agent](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

1. Conecte-se à VM do Linux usando um cliente SSH.
2. Na janela SSH, digite o seguinte comando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Execute o comando apenas em uma VM que você pretende capturar como uma imagem. Ele não garante que a imagem esteja sem nenhuma informação confidencial ou seja adequada para redistribuição.
 
3. Digite **y** para continuar. Você pode adicionar o parâmetro **-force** para evitar essa etapa de confirmação.
4. Após a conclusão do comando, digite **sair**. Esta etapa fecha o cliente SSH.

## <a name="step-2-capture-the-vm"></a>Etapa 2: Capturar a VM
Use a CLI do Azure para generalizar e capturar a VM. Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem **myResourceGroup**, **myVnet** e **myVM**.

1. No computador local, abra a CLI do Azure e [faça logon com sua assinatura do Azure](../../xplat-cli-connect.md). 
2. Certifique-se de estar no modo Resource Manager.
   
    ```azurecli
    azure config mode arm
    ```
3. Desligue a VM que você já desprovisionou usando o seguinte comando:
   
    ```azurecli
    azure vm deallocate -g myResourceGroup -n myVM
    ```
4. Generalize a VM com o seguinte comando:
   
    ```azurecli
    azure vm generalize -g myResourceGroup -n myVM
    ```
5. Agora, execute o comando **azure vm capture**, que captura a VM. No exemplo a seguir, os VHDs de imagem são capturados com nomes que começam com **MyVHDNamePrefix**, e a opção **-t** especifica um caminho para o modelo **MyTemplate.json**. 
   
    ```azurecli
    azure vm capture -g myResourceGroup -n myVM -p myVHDNamePrefix -t myTemplate.json
    ```
   
   > [!IMPORTANT]
   > Os arquivos de imagem VHD são criados por padrão na mesma conta de armazenamento da VM original usada. Use a *mesma conta de armazenamento* para armazenar os VHDs de novas VMs que você criar com base na imagem. 

6. Para encontrar o local de uma imagem capturada, abra o modelo JSON em um editor de texto. Em **storageProfile**, encontre o **uri** da **imagem** localizado no contêiner do **sistema**. Por exemplo, o URI da imagem de disco do sistema operacional é semelhante a `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Etapa 3: criar uma VM com base na imagem capturada
Agora, use a imagem com um modelo para criar uma VM do Linux. Essas etapas mostram como usar a CLI do Azure e o modelo de arquivo JSON capturado para criar a VM em uma nova rede virtual.

### <a name="create-network-resources"></a>Criar recursos da rede
Para usar o modelo, primeiro é necessário configurar uma rede virtual e uma NIC para sua nova VM. Recomendamos criar um grupo de recursos para esses recursos na localização em que a imagem de VM está armazenada. Execute comandos semelhantes aos seguintes, substituindo os nomes dos seus recursos e um local apropriado do Azure ("centralus" nesses comandos):

```azurecli
azure group create myResourceGroup1 -l "centralus"

azure network vnet create myResourceGroup1 myVnet -l "centralus"

azure network vnet subnet create myResourceGroup1 myVnet mySubnet

azure network public-ip create myResourceGroup1 myPublicIP -l "centralus"

azure network nic create myResourceGroup1 myNIC -k mySubnet -m myVnet -p myPublicIP -l "centralus"
```

### <a name="get-the-id-of-the-nic"></a>Obter a Id da NIC
Para implantar uma VM a partir da imagem usando o JSON salvo durante a captura, é necessária a ID do NIC. Obtenha-a executando o seguinte comando:

```azurecli
azure network nic show myResourceGroup1 myNIC
```

A **Id** na saída é semelhante a `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`

### <a name="create-a-vm"></a>Criar uma máquina virtual
Agora, execute o comando a seguir para criar a VM com base na imagem de VM capturada. Use o parâmetro **-f** para especificar o caminho para o arquivo JSON de modelo salvo.

```azurecli
azure group deployment create myResourceGroup1 MyDeployment -f MyTemplate.json
```

Na saída do comando, você precisará fornecer um novo nome da VM, nome de usuário administrador, senha e a ID da NIC criada anteriormente.

```bash
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName: myNewVM
adminUserName: myAdminuser
adminPassword: ********
networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/myResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
```

O seguinte exemplo mostra o que você vê para uma implantação bem-sucedida:

```bash
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment xxxxxxx
+ Waiting for deployment to complete
data:    DeploymentName     : MyDeployment
data:    ResourceGroupName  : MyResourceGroup1
data:    ProvisioningState  : Succeeded
data:    Timestamp          : xxxxxxx
data:    Mode               : Incremental
data:    Name                Type          Value

data:    ------------------  ------------  -------------------------------------

data:    vmName              String        myNewVM

data:    vmSize              String        Standard_D1

data:    adminUserName       String        myAdminuser

data:    adminPassword       SecureString  undefined

data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
info:    group deployment create command OK
```

### <a name="verify-the-deployment"></a>Verificar a implantação
Agora, use o SSH na máquina virtual que você criou para verificar a implantação e começar a usar a nova VM. Para se conectar via SSH, localize o endereço IP da VM criada executando o seguinte comando:

```azurecli
azure network public-ip show myResourceGroup1 myPublicIP
```

O endereço IP público é listado na saída do comando. Por padrão, você conecta a VM Linux por SSH na porta 22.

## <a name="create-additional-vms"></a>Criar VMs adicionais
Use a imagem capturada e o modelo para implantar VMs adicionais usando as etapas da seção anterior. Outras opções para criar VMs com base na imagem incluem usar um modelo de início rápido ou executar o comando **azure vm create**.

### <a name="use-the-captured-template"></a>Usar o modelo capturado
Para usar a imagem capturada e o modelo, siga estas etapas (detalhadas na seção anterior):

* Verifique se a imagem da VM está na mesma conta de armazenamento que hospeda o VHD da VM.
* Copie o arquivo de modelo JSON e especifique um nome exclusivo para o disco do sistema operacional do VHD (ou VHDs) da nova VM. Por exemplo, em **storageProfile**, em **vhd**, em **uri**, especifique um nome exclusivo para o VHD **osDisk**, semelhante a `https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* Crie uma NIC na mesma rede virtual ou em uma diferente.
* Usando o arquivo JSON de modelo modificado, crie uma implantação no grupo de recursos em que você configura a rede virtual.

### <a name="use-a-quickstart-template"></a>Usar um modelo de início rápido
Se desejar que a rede seja configurada automaticamente quando você criar uma VM por meio da imagem, você poderá especificar esses recursos em um modelo. Por exemplo, confira [101-vm-from-user-image template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) do GitHub. Esse modelo cria uma VM de sua imagem personalizada e da rede virtual necessária, endereço IP público e recursos da NIC. Para obter uma explicação sobre como usar o modelo no Portal do Azure, confira [How to create a virtual machine from a custom image using a Resource Manager template](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/)(Como criar uma máquina virtual de uma imagem personalizada usando um modelo do Resource Manager).

### <a name="use-the-azure-vm-create-command"></a>Usar o comando azure vm create
Geralmente, é mais fácil de usar um modelo do Resource Manager para criar uma VM com base na imagem. No entanto, você pode criar a VM *de modo forçado* usando o comando **azure vm create** com o parâmetro **-Q** (**--image-urn**). Se usar este método, você também passará o parâmetro **-d** (**--os-disk-vhd**) para especificar a localização do arquivo.vhd do sistema operacional para a nova VM. O arquivo deve estar no contêiner de vhds da conta de armazenamento na qual o arquivo VHD da imagem está armazenado. O comando copia o VHD para a nova VM automaticamente para o contêiner de **vhds**.

Antes de executar **azure vm create** com a imagem, conclua as seguintes etapas:

1. Crie um grupo de recursos ou identifique um grupo de recursos existente para a implantação.
2. Crie um recurso do endereço IP público e um recurso NIC para a nova VM. Para obter as etapas para criar uma rede virtual, endereço IP público e NIC usando a CLI, confira o que foi escrito anteriormente neste artigo. (o**azure vm create** também pode criar uma NIC, mas você precisa passar parâmetros adicionais para uma rede virtual e uma sub-rede.)

Em seguida, execute um comando que passa URIs para o novo arquivo de VHD do sistema operacional e a imagem existente. Neste exemplo, um tamanho de VM Standard_A1 é criado na região Leste dos EUA.

```azurecli
azure vm create -g myResourceGroup1 -n myNewVM -l eastus -y Linux \
-z Standard_A1 -u myAdminname -p myPassword -f myNIC \
-d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" \
-Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.vhd"
```

Para obter opções adicionais de comando, execute `azure help vm create`.

## <a name="next-steps"></a>Próximas etapas
Para gerenciar suas VMs com a CLI, consulte as tarefas em [Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e a CLI do Azure](create-ssh-secured-vm-from-template.md).

