---
title: Ingressar uma VM RedHat do Linux no Azure Active Directory DS | Microsoft Docs
description: Como ingressar uma VM RedHat Enterprise Linux 7 existente em um Azure Active Directory Domain Service.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2016
ms.author: v-livech
ms.openlocfilehash: 2e46a0f3c9bdbe267d121b4bf62e25d5d411fcc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="join-a-redhat-linux-vm-to-an-azure-active-directory-domain-service"></a>Ingressar uma VM RedHat do Linux em um Azure Active Directory Domain Service

Este artigo mostra como ingressar uma máquina virtual RHEL (Red Hat Enterprise Linux) 7 em um domínio gerenciado do AADDS (Azure Active Directory Domain Services).  Esses requisitos são:

- [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)

- [arquivos de chave SSH pública e privada](mac-create-ssh-keys.md)

- [Um controlador de domínio do Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Comandos rápidos

_Substitua quaisquer exemplos por suas próprias configurações._

### <a name="switch-the-azure-cli-to-classic-deployment-mode"></a>Mudar azure-cli para o modo de implantação clássico

```azurecli
azure config mode asm
```

### <a name="search-for-a-rhel-version-and-image"></a>Pesquise uma versão do RHEL e uma imagem

```azurecli
azure vm image list | grep "Red Hat"
```

### <a name="create-a-redhat-linux-vm"></a>Criar uma VM Red Hat do Linux

```azurecli
azure vm create myVM \
-o a879bbefc56a43abb0ce65052aac09f3__RHEL_7_2_Standard_Azure_RHUI-20161026220742 \
-g ahmet \
-p myPassword \
-e 22 \
-t "~/.ssh/id_rsa.pub" \
-z "Small" \
-l "West US"
```

### <a name="ssh-to-the-vm"></a>SSH para a VM

```bash
ssh -i ~/.ssh/id_rsa ahmet@myVM
```

### <a name="update-yum-packages"></a>Atualizar os pacotes YUM

```bash
sudo yum update
```

### <a name="install-packages-needed"></a>Instalar os pacotes necessários

```bash
sudo yum -y install realmd sssd krb5-workstation krb5-libs
```

Agora que os pacotes necessários são instalados na máquina virtual do Linux, a próxima tarefa é ingressar a máquina virtual no domínio gerenciado.

### <a name="discover-the-aad-domain-services-managed-domain"></a>Descobrir o domínio gerenciado do AAD Domain Services

```bash
sudo realm discover mydomain.com
```

### <a name="initialize-kerberos"></a>Inicializar o Kerberos

Certifique-se de especificar um usuário que pertence ao grupo 'Administradores do DC do AAD’. Apenas esses usuários podem ingressar computadores ao domínio gerenciado.

```bash
kinit ahmet@mydomain.com
```

### <a name="join-the-machine-to-the-domain"></a>Ingressar o computador no domínio

```bash
sudo realm join --verbose mydomain.com -U 'ahmet@mydomain.com'
```

### <a name="verify-the-machine-is-joined-to-the-domain"></a>Verificar se o computador está ingressado no domínio

```bash
ssh -l ahmet@mydomain.com mydomain.cloudapp.net
```

## <a name="next-steps"></a>Próximas etapas

* [RHUI (Infraestrutura de Atualização do Red Hat) para as VMs Red Hat Enterprise do Linux sob demanda no Azure](update-infrastructure-redhat.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Configurar o Cofre de Chaves para máquinas virtuais no Azure Resource Manager](key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Implantar e gerenciar máquinas virtuais usando modelos do Azure Resource Manager e a CLI do Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
