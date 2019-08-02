---
title: Montar o armazenamento de Arquivos do Azure em VMs Linux usando SMB | Microsoft Docs
description: Como montar o armazenamento de Arquivos do Azure em VMs Linux usando SMB com a CLI do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 9ca58bbc6f7b983ff545eb2dca6240359637e214
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671219"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Montar o Armazenamento de Arquivos do Azure em VMs Linux usando SMB

Este artigo mostra como utilizar o serviço armazenamento de Arquivos do Azure em uma VM Linux usando uma montagem SMB com a CLI do Azure. O armazenamento de arquivos do Azure oferece compartilhamentos de arquivos na nuvem usando o protocolo SMB padrão. 

O armazenamento de arquivos oferece compartilhamentos de arquivos na nuvem que usam o protocolo SMB padrão. Você pode montar um compartilhamento de arquivos em qualquer sistema operacional que dá suporte a SMB 3.0. Ao usar uma montagem SMB no Linux, você obtém backups fáceis em uma localização robusta e permanente de armazenamento de arquivamento com suporte em um SLA.

Mover arquivos de uma VM para uma montagem SMB hospedada no Armazenamento de arquivos é uma ótima maneira de depurar logs. O mesmo compartilhamento SMB pode ser montado localmente em sua estação de trabalho Mac, Linux ou Windows. O SMB não é a melhor solução para transmitir logs do Linux ou de aplicativo em tempo real, pois o protocolo SMB não foi desenvolvido para lidar com tarefas de log tão grandes. Uma ferramenta de camada de log unificada dedicada, como o Fluentd, poderá ser uma escolha melhor em relação ao SMB para coletar a saída de log do Linux e de aplicativo.

Este guia exige que você esteja executando a CLI do Azure versão 2.0.4 ou posterior. Execute **az --version** para descobrir a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos denominado *myResourceGroup* no local *Leste dos EUA*.

```bash
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma nova conta de armazenamento no grupo de recursos que você criou ao utilizar o [az storage account create](/cli/azure/storage/account). Este exemplo cria uma conta de armazenamento denominada *mySTORAGEACCT\<número aleatório >* e coloca o nome da conta de armazenamento na variável **STORAGEACCT**. Os nomes de conta de armazenamento devem ser exclusivo e, portanto, use `$RANDOM` para acrescentar um número ao final e torná-lo exclusivo.

```bash
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Obter a chave de Armazenamento

Ao criar uma conta de armazenamento, as chaves da conta são criadas em pares, para que possam ser giradas sem nenhuma interrupção de serviço. Depois de mudar as chaves para a segunda chave do par, você cria um novo par de chaves. Novas chaves de conta de armazenamento são sempre criadas em pares, garantindo que você sempre tenha, pelo menos, uma chave de conta de armazenamento não utilizada pronta para ser mudada.

Exibir as chaves da conta de armazenamento, usando [az storage account keys list](/cli/azure/storage/account/keys). Este exemplo armazena o valor da chave 1 na variável **STORAGEKEY**.

```bash
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Criar um compartilhamento de arquivos

Criar o compartilhamento de armazenamento de arquivos usando [criar compartilhamento de armazenamento az](/cli/azure/storage/share). 

Os nomes de compartilhamento precisam ter somente letras em minúsculas, números e hifens, mas não podem começar com um hífen. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Este exemplo cria um compartilhamento chamado *myshare* com uma cota de 10-GiB. 

```bash
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Criar um ponto de montagem

Para montar o compartilhamento de arquivos do Azure no seu computador Linux, você precisa certificar-se de que você tem o pacote **cifs-utils** instalado. Para instruções sobre instalação, consulte [Instalar o pacote cifs-utils para sua distribuição Linux](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Os arquivos do Azure usa o protocolo SMB, que se comunica pela porta TCP 445.  Se você estiver tendo problemas para montar o compartilhamento de arquivos do Azure, verifique se que o firewall não está bloqueando a porta TCP 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Montar o compartilhamento

Monte o compartilhamento de arquivos do Azure para o diretório. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

O comando acima usa o [montar](https://linux.die.net/man/8/mount) comando para montar o compartilhamento de arquivos do Azure e as opções específicas ao [cifs](https://linux.die.net/man/8/mount.cifs). Especificamente, o dir_mode e file_mode opções de conjunto de arquivos e diretórios para a permissão `0777`. O `0777` dá a permissão de leitura, gravação e execução permissões para todos os usuários. Você pode alterar essas permissões, substituindo os valores com os outros [permissões chmod](https://en.wikipedia.org/wiki/Chmod). Você também pode usar outras [cifs](https://linux.die.net/man/8/mount.cifs) opções como gid ou uid. 


## <a name="persist-the-mount"></a>Persista a montagem

Ao reinicializar a VM Linux, o compartilhamento SMB montado é desmontado durante o desligamento. Para montar novamente o compartilhamento SMB durante a inicialização, adicione uma linha ao /etc/fstab do Linux. O Linux usa o arquivo fstab para listar os sistemas de arquivos que precisa montar durante o processo de inicialização. A adição do compartilhamento SMB garante que o compartilhamento do Armazenamento de arquivos seja um sistema de arquivos montado permanentemente para a VM Linux. É possível adicionar o compartilhamento SMB do Armazenamento de arquivos a uma nova VM ao usar a inicialização de nuvem.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```
Para aumentar a segurança em ambientes de produção, você deve armazenar suas credenciais fora fstab.

## <a name="next-steps"></a>Próximas etapas

- [Uso de cloud-init para personalizar uma VM do Linux durante a criação](using-cloud-init.md)
- [Adicionar um disco a uma VM do Linux](add-disk.md)
- [Criptografar discos em uma VM do Linux usando a CLI do Azure](encrypt-disks.md)

