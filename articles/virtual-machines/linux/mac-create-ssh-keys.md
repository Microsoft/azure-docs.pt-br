---
title: Criar e usar um par de chaves SSH para VMs Linux no Azure | Microsoft Docs
description: Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure para melhorar a segurança do processo de autenticação.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/11/2018
ms.author: cynthn
ms.openlocfilehash: d442d09c8c8ded3aa50faf74e28c8d95ded24a5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614415"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Etapas rápidas: Criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure

Com um par de chaves SSH (secure shell), você pode criar VMs (máquinas virtuais) no Azure que usam chaves SSH para autenticação, eliminando a necessidade de senhas para entrar. Este artigo mostra como gerar e usar rapidamente um par de arquivos de chaves SSH pública e privada para VMs Linux. É possível concluir essas etapas com o Azure Cloud Shell, um host do macOS ou Linux, o Subsistema do Windows para Linux e outras ferramentas compatíveis com OpenSSH. 

> [!NOTE]
> As VMs criadas usando chaves SSH são por padrão configuradas com senhas desabilitadas, o que aumenta muito a dificuldade de ataques de adivinhação de força bruta. 

Para obter mais informações e exemplos, confira [Etapas detalhadas para criar pares de chaves SSH](create-ssh-keys-detailed.md).

Para ver outras maneiras de gerar e usar chaves SSH em um computador Windows, consulte [Como usar chaves SSH com o Windows no Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Use o comando `ssh-keygen` para gerar arquivos de chave SSH pública e privada. Por padrão, esses arquivos são criados no diretório ~/.ssh. Você pode especificar um local diferente e uma senha opcional (*frase secreta*) para acessar o arquivo de chave privada. Se um par de chaves SSH com o mesmo nome existir no local especificado, esses arquivos serão substituídos.

O comando a seguir cria um par de chaves SSH usando a criptografia RSA e o tamanho de bit 2048:

```bash
ssh-keygen -t rsa -b 2048
```

Ao usar a [CLI do Azure](/cli/azure) para criar a VM com o comando [az vm create](/cli/azure/vm#az-vm-create), opcionalmente, você pode gerar arquivos de chave SSH pública e privada executando a opção `--generate-ssh-keys`. Os arquivos de chave são armazenados no diretório ~/.ssh, a menos que você especifique outro local com a opção `--ssh-dest-key-path`. A opção `--generate-ssh-keys` não substituirá os arquivos de chave existentes e retornará um erro. No comando a seguir, substitua *VMname* e *RGname* pelos seus próprios valores:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Fornecer uma chave pública SSH ao implantar uma VM

Para criar uma VM do Linux que use chaves SSH para autenticação, especifique sua chave pública SSH ao criar a VM usando o portal do Azure, a CLI do Azure, os modelos do Azure Resource Manager ou outros métodos:

* [Criar uma máquina virtual Linux com o Portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma máquina virtual Linux com a CLI do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM Linux usando um modelo do Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Se você não estiver familiarizado com o formato de uma chave SSH pública, exiba sua chave pública com o seguinte comando `cat`, substituindo `~/.ssh/id_rsa.pub` pelo caminho e o nome de arquivo do seu próprio arquivo de chave pública, se necessário:

```bash
cat ~/.ssh/id_rsa.pub
```

Um valor de chave pública típico se parece com este exemplo:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Se você copiar e colar o conteúdo do arquivo de chave pública a ser usado no portal do Azure ou em um modelo do Resource Manager, não copie nenhum espaço em branco à direita. Para copiar uma chave pública no macOS, é possível redirecionar o arquivo de chave pública para **pbcopy**. Como no Linux, é possível redirecionar o arquivo de chave pública para programas como o **xclip**.

A chave pública colocada em sua VM do Linux no Azure é armazenada, por padrão, em ~/.ssh/id_rsa.pub, a menos que tenha especificado um local diferente ao criar o par de chaves. Para usar a [CLI do Azure 2.0](/cli/azure) para criar sua VM com uma chave pública existente, especifique o valor e, opcionalmente, o local dessa chave pública usando o comando [az vm create](/cli/azure/vm#az-vm-create) a opção `--ssh-key-value`. No comando a seguir, substitua *VMname*, *RGname* e *keyFile* pelos seus próprios valores:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-value @keyFile
```

## <a name="ssh-into-your-vm"></a>SSH em sua VM

Com a chave pública implantada em sua VM do Azure e a chave privada em seu sistema local, entre com SSH na VM usando o endereço IP ou o nome DNS da VM. No comando a seguir, substitua *azureuser* e *myvm.westus.cloudapp.azure.com* pelo nome de usuário administrador e o nome de domínio totalmente qualificado (ou o endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se você tiver especificado uma frase secreta quando criou o par de chaves, insira-a quando solicitado durante o processo de logon. A VM é adicionada ao arquivo ~/.ssh/known_hosts e você não precisará se conectar novamente até que a chave pública na VM do Azure seja alterada ou o nome do servidor seja removido de ~/.ssh/known_hosts.

Se a VM estiver usando a política de acesso Just-In-Time, você precisará solicitar acesso antes que possa se conectar à VM. Para obter mais informações sobre a política Just-In-Time, confira [Gerenciar o acesso à máquina virtual usando a política Just-In-Time](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações de como trabalhar com pares de chaves SSH, confira [Etapas detalhadas para criar e gerenciar pares de chaves SSH](create-ssh-keys-detailed.md).

* Se você tiver dificuldades com conexões SSH às VM no Azure, confira [Solucionar problemas de conexão SSH a uma VM do Linux no Azure](troubleshoot-ssh-connection.md).
