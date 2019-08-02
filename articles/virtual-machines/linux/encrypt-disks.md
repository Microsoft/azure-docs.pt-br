---
title: Criptografar discos em uma VM do Linux no Azure | Microsoft Docs
description: Como criptografar discos virtuais em uma VM Linux para aumentar a segurança usando a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: f38dab21d44e5ed4a01d841a515e8c50e1c713a6
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667888"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Como criptografar uma máquina virtual Linux no Azure

Para conformidade e segurança aprimoradas da VM (máquina virtual), os discos virtuais e a própria VM podem ser criptografados. VMs são criptografadas usando chaves criptográficas que são protegidas em um Azure Key Vault. Você controla essas chaves criptográficas e pode auditar seu uso. Este artigo detalha como criptografar discos virtuais em uma VM Linux usando a CLI do Azure. 

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que esteja em execução a CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Visão geral da criptografia de disco
Discos virtuais em VMs do Linux são criptografados em repouso usando [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Não há nenhuma taxa para criptografar discos virtuais no Azure. Chaves e criptográficas são armazenadas no Cofre de chaves do Azure usando a proteção de software ou você pode importar ou gerar as chaves em Módulos de segurança de Hardware (HSMs) certificados para padrões de nível 2 de FIPS 140-2. Você mantém o controle dessas chaves criptográficas e pode auditar seu uso. Essas chaves criptográficas são usadas para criptografar e descriptografar os discos virtuais conectados à sua VM. 

O processo de criptografia de uma VM é o seguinte:

1. Crie uma chave de criptografia em um Cofre de chaves do Azure.
1. Configure a chave de criptografia a ser usada para criptografar discos.
1. Ative a criptografia de disco para seus discos virtuais.
1. A Azure Key Vault criptográficas necessárias são solicitadas do Cofre de Chaves do Azure.
1. Os discos virtuais são criptografados usando a chave de criptografia fornecida.


## <a name="requirements-and-limitations"></a>Requisitos e limitações
Requisitos e cenários com suporte para criptografia de disco:

* Os seguintes SKUs de servidor Linux – Ubuntu, CentOS, SUSE e SLES (SUSE Linux Enterprise Server) e Red Hat Enterprise Linux.
* Todos os recursos (por exemplo, Cofre de chaves, conta de Armazenamento e VM) devem pertencer à mesma assinatura e região do Azure.
* VMs Standard das séries A, D, DS, G, GS etc.
* Atualização das chaves de criptografia em uma VM Linux já está criptografado.

A criptografia de disco não tem suporte atualmente nos seguintes cenários:

* VMs de camada básica.
* VMs criadas com o modelo de implantação Clássico.
* Desabilitação da criptografia de disco do OS em VMs do Linux.
* Uso de imagens personalizadas do Linux.

Para saber mais sobre cenários com suporte e limitações, consulte [Azure Disk Encryption para VMs de IaaS](../../security/azure-security-disk-encryption.md)


## <a name="create-an-azure-key-vault-and-keys"></a>Criar um Azure Key Vault e as chaves
Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup*, *myKey* e *myVM*.

A primeira etapa é criar um Cofre de Chaves do Azure para armazenar as chaves criptográficas. O Cofre de Chaves do Azure pode armazenar chaves, segredos ou senhas que permitem implementá-los de forma segura em seus aplicativos e serviços. Para criptografia de disco virtual, use o Cofre de chaves para armazenar uma chave de criptografia que é usada para criptografar ou descriptografar seus discos virtuais.

Habilite o provedor do Azure Key Vault dentro de sua assinatura do Azure com [az provider register](/cli/azure/provider#az-provider-register) e crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local `eastus`:

```azurecli-interactive
az provider register -n Microsoft.KeyVault
resourcegroup="myResourceGroup"
az group create --name $resourcegroup --location eastus
```

O Cofre de chaves do Azure que contém as chaves criptográficas e recursos de computação associados, como armazenamento e a própria VM deve residir na mesma região. Crie um Azure Key Vault com [az keyvault create](/cli/azure/keyvault#az-keyvault-create) e habilite o Key Vault para uso com criptografia de disco. Especifique um nome exclusivo de Key Vault para *keyvault_name* da seguinte maneira:

```azurecli-interactive
keyvault_name=myvaultname$RANDOM
az keyvault create \
    --name $keyvault_name \
    --resource-group $resourcegroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

É possível armazenar chaves de criptografia usando a proteção do Modelo de segurança de Hardware (HSM) ou software. Usar um HSM requer um Cofre de Chaves premium. Há um custo adicional para a criação de um Cofre de Chaves premium em vez do Cofre de Chaves padrão que armazena as chaves protegidas por software. Para criar um Cofre de Chaves premium, na etapa anterior, adicione `--sku Premium` ao comando. O exemplo a seguir usa chaves protegidas por software, desde a criação de um Key Vault padrão.

Para ambos os modelos de proteção, a plataforma do Azure deve ter acesso para solicitar as chaves criptográficas quando a VM é inicializada para descriptografar os discos virtuais. Criar uma chave de criptografia em seu Key Vault com [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create). O exemplo a seguir cria uma chave chamada *myKey*:

```azurecli-interactive
az keyvault key create \
    --vault-name $keyvault_name \
    --name myKey \
    --protection software
```


## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Crie uma VM com [az vm create](/cli/azure/vm#az-vm-create) e anexe um disco de dados de 5 GB. Somente determinadas imagens do marketplace dão suporte à criptografia de disco. O exemplo a seguir cria uma VM chamada *myVM* usando uma imagem do *Ubuntu 16.04 LTS*:

```azurecli-interactive
az vm create \
    --resource-group $resourcegroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

SSH para a VM usando o *publicIpAddress* mostrado na saída do comando anterior. Crie uma partição e o sistema de arquivos e, em seguida, monte o disco de dados. Para obter mais informações, consulte [Conectar-se a uma VM Linux para montar o novo disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Feche a sessão SSH.


## <a name="encrypt-the-virtual-machine"></a>Criptografar a máquina virtual


Criptografe sua VM com [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable):

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Leva algum tempo para o processo de criptografia de disco ser concluído. Monitorar o status do processo com [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group $resourcegroup --name myVM --query 'status'
```

Quando concluída, a saída será semelhante ao exemplo a seguir:

```json
[
  {
    "code": "ProvisioningState/succeeded",
    "displayStatus": "Provisioning succeeded",
    "level": "Info",
    "message": "Encryption succeeded for all volumes",
    "time": null
  }
]
```


## <a name="add-additional-data-disks"></a>Adicionar discos de dados adicionais
Depois de criptografar seus discos de dados, você pode adicionar outros discos virtuais à sua VM e criptografá-los. 

Depois que o disco de dados tiver sido adicionado à VM, execute novamente o comando para criptografar os discos virtuais da seguinte maneira:

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type data
```


## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre como gerenciar o Cofre de Chaves do Azure, incluindo a exclusão de chaves criptográficas e cofres, consulte [Gerenciar Cofres de Chaves usando a CLI](../../key-vault/key-vault-manage-with-cli2.md).
* Para obter mais informações sobre criptografia de disco, como preparar uma VM personalizada criptografada para carregar no Azure, consulte [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
