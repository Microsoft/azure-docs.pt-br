---
title: Introdução ao FreeBSD no Azure | Microsoft Docs
description: Saiba como usar máquinas virtuais FreeBSD no Azure
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: 248588ad43f3b3beefa35de468a21cecedc8913d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667801"
---
# <a name="introduction-to-freebsd-on-azure"></a>Introdução ao FreeBSD no Azure
Este artigo fornece uma visão geral da execução de uma máquina virtual FreeBSD no Azure.

## <a name="overview"></a>Visão geral
O FreeBSD para Microsoft Azure é um sistema operacional avançado usado para capacitar servidores modernos, desktops e plataformas incorporadas.

A Microsoft Corporation está disponibilizando imagens do FreeBSD no Azure com o [Agente convidado da VM Azure](https://github.com/Azure/WALinuxAgent/) pré-configurado. No momento, as seguintes versões do FreeBSD são oferecidas como imagens pela Microsoft:

- FreeBSD 10.3-RELEASE
- FreeBSD 10.4-RELEASE
- FreeBSD 11.1-RELEASE

O agente é responsável pela comunicação entre a VM do FreeBSD e a malha do Azure para operações como provisionamento da VM no primeiro uso (nome de usuário, senha, chave SSH, nome do host, etc.) e habilitação da funcionalidade para extensões de VM seletivas.

Para versões futuras do FreeBSD, a estratégia é manter-se atualizado e disponibilizar as versões mais recentes logo após a publicação pela equipe de engenharia de versão do FreeBSD.

## <a name="deploying-a-freebsd-virtual-machine"></a>Implantando uma máquina virtual FreeBSD
A implantação de uma máquina virtual do FreeBSD é um processo simples que usa uma imagem do Azure Marketplace do Portal do Azure:

- [FreeBSD 10.4 no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
- [FreeBSD 11.2 no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112)

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Criar uma VM do FreeBSD por meio da CLI do Azure no FreeBSD
Primeiro, você precisa instalar a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) seguindo os comandos em um computador FreeBSD.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Se o bash não estiver instalado no seu computador FreeBSD, execute o comando a seguir antes da instalação. 

```bash
sudo pkg install bash
```

Se o Python não estiver instalado no seu computador FreeBSD, execute o comando a seguir antes da instalação. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Durante a instalação, `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)` será solicitado. Se responder `y` e inserir `/etc/rc.conf` como `a path to an rc file to update`, você poderá encontrar o problema `ERROR: [Errno 13] Permission denied`. Para resolvê-lo, você deve conceder o direito de gravação ao usuário atual sobre o arquivo `etc/rc.conf`.

Agora você pode entrar no Azure e criar sua VM do FreeBSD. Abaixo está um exemplo de como criar uma VM do FreeBSD 11.0. Você também pode adicionar o parâmetro `--public-ip-address-dns-name` com um nome DNS exclusivo para um IP público recém-criado. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Em seguida, é possível entrar na VM do FreeBSD pelo endereço IP impresso na saída acima da implantação. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Extensões de VM para FreeBSD
Os itens a seguir têm suporte de extensões de VM no FreeBSD.

### <a name="vmaccess"></a>VMAccess
A extensão [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) pode:

* Redefinir a senha do usuário sudo original.
* Criar um novo usuário sudo com a senha especificada.
* Definir a chave pública do host com a chave fornecida.
* Redefinir a chave pública do host fornecida durante o provisionamento da VM se a chave do host não for fornecida.
* Abrir a porta (22) SSH e restaurar o sshd_config se reset_ssh estiver definido como true.
* Remover o usuário existente.
* Verificar os discos.
* Reparar o disco adicionado.

### <a name="customscript"></a>CustomScript
A extensão [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) pode:

* Se for fornecida, baixar os scripts personalizados do Armazenamento do Azure ou do armazenamento público externo (por exemplo, GitHub).
* Executar o script de ponto de entrada.
* Oferecer suporte ao comando embutido.
* Converter automaticamente o estilo newline do Windows em scripts de Shell e Python.
* Remover automaticamente BOM em scripts de Shell e Python.
* Proteger dados confidenciais em CommandToExecute.

> [!NOTE]
> A VM do FreeBSD só oferece suporte ao CustomScript versão 1. x até o momento.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autenticação: nomes de usuário, senhas e chaves SSH
Ao criar uma máquina virtual FreeBSD usando o Portal do Azure, você deve fornecer um nome de usuário, uma senha ou uma chave pública SSH.
Os nomes de usuário para implantação de uma máquina virtual do FreeBSD no Azure não devem corresponder aos nomes de contas do sistema (UID <100) já presentes na máquina virtual (“root”, por exemplo).
Atualmente, há suporte apenas para a chave RSA SSH. Uma chave SSH multilinha deve começar com `---- BEGIN SSH2 PUBLIC KEY ----` e terminar com `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Obtendo privilégios de superusuário
A conta de usuário especificada durante a implantação da instância de máquina virtual no Azure é uma conta privilegiada. O pacote do sudo foi instalado na imagem de FreeBSD publicada.
Depois de fazer logon usando essa conta de usuário, você poderá executar comandos como raiz usando a sintaxe de comando.

```
$ sudo <COMMAND>
```

Opcionalmente, é possível obter um shell root usando `sudo -s`.

## <a name="known-issues"></a>Problemas conhecidos
O [Agente convidado de VM do Azure](https://github.com/Azure/WALinuxAgent/) versão 2.2.2 tem um [problema conhecido](https://github.com/Azure/WALinuxAgent/pull/517) que causa a falha de provisionamento da VM do FreeBSD no Azure. A correção foi capturada pelo [Agente Convidado da VM do Azure](https://github.com/Azure/WALinuxAgent/) versão 2.2.3 e versões posteriores. 

## <a name="next-steps"></a>Próximas etapas
* Acesse o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112) para criar uma VM FreeBSD.
