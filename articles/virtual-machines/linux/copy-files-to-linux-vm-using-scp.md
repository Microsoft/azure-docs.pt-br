---
title: Mover arquivos de e para VMs Linux do Azure com o SCP | Microsoft Docs
description: Mova arquivos de e para uma VM Linux no Azure com segurança usando o SCP e um par de chaves SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: danlep
ms.subservice: disks
ms.openlocfilehash: 13a2c889ac648e2847d1cc58a60a7b1c0f1fc1e2
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671646"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Mover arquivos de e para uma VM Linux usando o SCP

Este artigo mostra como mover arquivos da estação de trabalho para uma VM Linux do Azure ou de uma VM Linux do Azure para a estação de trabalho, usando o SCP (Cópia Segura). Mover arquivos entre a estação de trabalho e uma VM Linux, de forma rápida e segura, é uma parte crítica do gerenciamento da infraestrutura do Azure. 

Para este artigo, você precisa de uma VM Linux implantada no Azure usando [arquivos de chave SSH pública e privada](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Você também precisa de um cliente SCP para o computador local. Ele é criado com base em SSH e incluído no shell de Bash padrão da maioria dos computadores Linux e Mac e alguns shells do Windows.

## <a name="quick-commands"></a>Comandos rápidos

Copiar um arquivo para a VM Linux

```bash
scp file azureuser@azurehost:directory/targetfile
```

Copiar um arquivo da VM Linux

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado

Como exemplo, movemos um arquivo de configuração do Azure para uma VM Linux e efetuamos pull de um diretório de arquivos de log, ambos usando o SCP e chaves SSH.   

## <a name="ssh-key-pair-authentication"></a>Autenticação de par de chaves SSH

O SCP usa o SSH para a camada de transporte. O SSH manipula a autenticação no host de destino e também move o arquivo em um túnel criptografado fornecido por padrão com o SSH. Para autenticação de SSH, nomes de usuário e senhas podem ser usados. No entanto, a autenticação de chaves SSH públicas e privadas é recomendada como uma melhor prática de segurança. Depois de o SSH autenticar a conexão, o SCP inicia a cópia do arquivo. Usando um `~/.ssh/config` configurado corretamente e chaves SSH pública e privada, a conexão do SCP pode ser estabelecida sem o uso de um nome de usuário, com apenas um nome do servidor (ou endereço IP). Se você tiver apenas uma chave SSH, o SCP a procurará no diretório `~/.ssh/` e a usará por padrão para fazer logon na VM.

Para obter mais informações sobre como configurar o `~/.ssh/config` e chaves SSH pública e privada, veja [Create SSH keys](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Criar chaves SSH).

## <a name="scp-a-file-to-a-linux-vm"></a>Usar o SCP para copiar um arquivo para uma VM Linux

Para o primeiro exemplo, copiamos um arquivo de configuração do Azure para uma VM Linux usada para implantar a automação. Já que esse arquivo contém credenciais de API do Azure, as quais incluem segredos, a segurança é importante. O túnel criptografado fornecido por SSH protege o conteúdo do arquivo.

O comando a seguir copia o arquivo local *.azure/config* para uma VM do Azure com o FQDN *myserver.eastus.cloudapp.azure.com*. O nome de usuário administrador na VM do Azure é *azureuser*. O arquivo é direcionado para o diretório */home/azureuser/* . Substitua seus próprios valores nesse comando.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Usar o SCP para copiar um diretório de uma VM Linux

Neste exemplo, copiamos um diretório de arquivos de log da VM Linux para a estação de trabalho. Um arquivo de log pode ou não conter dados confidenciais ou segredos. No entanto, usar o SCP garante que o conteúdo dos arquivos de log é criptografado. Usar o SCP para transferir os arquivos é a maneira mais fácil de enviar o diretório de log e os arquivos para a estação de trabalho e, ao mesmo tempo, permanecer seguro.

O comando a seguir copia os arquivos no diretório *inicial/azureuser/logs/* na VM do Azure para o diretório local /tmp:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

O sinalizador `-r` da CLI instrui o SCP a copiar os arquivos e diretórios recursivamente do ponto do diretório listado no comando.  Observe também que a sintaxe de linha de comando é semelhante a um comando de cópia `cp`.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar usuários, SSH e verificar ou reparar discos em VMs do Linux do Azure usando a extensão VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
