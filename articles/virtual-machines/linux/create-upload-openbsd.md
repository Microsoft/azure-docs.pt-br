---
title: Criar e carregar uma imagem de VM OpenBSD no Azure | Microsoft Docs
description: Saiba como criar e carregar um disco rígido virtual (VHD) que contenha um sistema operacional OpenBSD para criar uma máquina virtual do Azure por meio da CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: huishao
ms.openlocfilehash: ea91b53b80b91e35ff7e2ffd36d02e6d478e8ebe
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667958"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Criar e carregar uma imagem de disco OpenBSD no Azure
Este artigo mostra como criar e carregar um disco rígido virtual (VHD) que contém o sistema operacional OpenBSD. Depois de carregá-lo, você pode usá-lo como sua própria imagem para criar uma máquina virtual (VM) no Azure por meio da CLI do Azure.


## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tenha os seguintes itens:

* **Uma assinatura do Azure** - Se não tiver uma conta, você poderá criar uma em apenas alguns minutos. Se você tiver uma assinatura do MSDN, confira [Crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Caso contrário, saiba como [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).  
* **CLI do Azure** – Verifique se você instalou a versão mais recente da [CLI do Azure](/cli/azure/install-azure-cli) e entrou em uma conta do Azure usando [az login](/cli/azure/reference-index).
* **Sistema operacional OpenBSD instalado em um arquivo. vhd** – um sistema operacional com suporte de OpenBSD ([versão 6.2 AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.2/amd64/)) deve ser instalado em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd. Por exemplo, você pode usar uma solução de virtualização, como o Hyper-V, para criar o arquivo .vhd e instalar o sistema operacional. Para obter instruções sobre como instalar e usar o Hyper-V, confira [Instalar o Hyper-V e criar uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Preparar imagem OpenBSD do Azure
Na VM em que você instalou o sistema de operacional OpenBSD 6.1, que adicionou suporte Hyper-V, conclua os procedimentos a seguir:

1. Se DHCP não está habilitado durante a instalação, habilite o serviço da seguinte maneira:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Instalar um console serial da seguinte maneira:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Configure a instalação do pacote da seguinte maneira:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Por padrão, o usuário `root` encontra-se desabilitado nas máquinas virtuais no Azure. Os usuários podem executar comandos com privilégios elevados usando o comando `doas` na VM OpenBSD. Doas é habilitado por padrão. Para obter mais informações, consulte [doas.conf](https://man.openbsd.org/doas.conf.5). 

5. Instalar e configurar os pré-requisitos para o agente do Azure da seguinte maneira:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. A versão mais recente do agente do Azure sempre pode ser encontrada no [GitHub](https://github.com/Azure/WALinuxAgent/releases). Instale o agente da seguinte maneira:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Depois de instalar o agente do Azure, convém verificar se ele está em execução da seguinte maneira:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Desprovisione o sistema para limpá-lo e torná-lo adequado para o reprovisionamento. O comando a seguir também exclui a última conta de usuário provisionada e os dados associados:

    ```sh
    waagent -deprovision+user -force
    ```

Agora você pode desligar sua VM.


## <a name="prepare-the-vhd"></a>Preparar o VHD
O formato VHDX não tem suporte no Azure, somente o **VHD fixo**. Você pode converter o disco no formato VHD fixo usando o Gerenciador do Hyper-V ou o cmdlet [convert-vhd](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) do Powershell. Um exemplo é como segue.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Criar recursos de armazenamento e carregar
Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Para carregar seu VHD, crie uma conta de armazenamento com [criar conta de armazenamento az](/cli/azure/storage/account). O nome da conta de armazenamento deve ser exclusivo; portanto, forneça seu próprio nome. O exemplo a seguir cria uma conta de armazenamento chamada *mystorageaccount*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Para controlar o acesso à conta de armazenamento, obter a chave de armazenamento com [az storage account keys list](/cli/azure/storage/account/keys) da seguinte maneira:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Para separar logicamente os VHDs que você carregue, crie um contêiner dentro da conta de armazenamento com [criar contêiner de armazenamento az](/cli/azure/storage/container):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Por fim, carregue o VHD com [carregamento de blob de armazenamento az](/cli/azure/storage/blob) da seguinte maneira:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Criar VM a partir de seu VHD
Você pode criar uma VM com um [script de exemplo](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) ou diretamente com [criar vm az](/cli/azure/vm). Para especificar o VHD OpenBSD carregado, use o parâmetro `--image` da seguinte maneira:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Obter o endereço IP para sua VM OpenBSD com [az vm lista--endereços ip](/cli/azure/vm) da seguinte maneira:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Agora você pode SSH para sua VM OpenBSD normal:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Próximas etapas
Se você quiser saber mais sobre o suporte de Hyper-V em OpenBSD6.1, leia [OpenBSD 6.1](https://www.openbsd.org/61.html) e [hyperv.4](https://man.openbsd.org/hyperv.4).

Se você quiser criar uma VM de disco gerenciado, leia [disco az](/cli/azure/disk). 
