---
title: "Redefinir o acesso em VMs Linux do Azure usando a extensão VMAccess | Microsoft Docs"
description: "Redefina o acesso em VMs do Linux do Azure usando a extensão VMAccess"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: v-livech
ms.openlocfilehash: 278bf1785aac71068ab94cf9916af69a204c44be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-10"></a>Gerenciar usuários, SSH e verificar ou reparar discos em VMs Linux do Azure usando a extensão VMAccess com a CLI 1.0 do Azure
Este artigo mostra como usar a extensão VMAcesss do Azure para verificar ou reparar um disco, redefinir o acesso do usuário, gerenciar contas de usuário ou redefinir a configuração do SSHD no Linux. O artigo exige:

* uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).
* a [CLI do Azure](../../cli-install-nodejs.md) conectada com o `azure login`.
* A CLI do Azure *deve estar no* modo Azure Resource Manager `azure config mode arm`.


## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI 1.0 do Azure](#quick-commands) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI 2.0 do Azure](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa última geração de CLI para o modelo de implantação de gerenciamento de recursos


## <a name="quick-commands"></a>Comandos rápidos
Há duas maneiras de usar VMAccess em suas VMs do Linux:

* Usando a CLI 1.0 do Azure e os parâmetros necessários.
* Usando os arquivos JSON brutos que a VMAccess processa e usa.

Para a seção de comando rápido, vamos usar o método `azure vm reset-access` da CLI 1.0 do Azure. Nos exemplos de comandos a seguir, substitua os valores que contêm “exemplo” pelos valores de seu próprio ambiente.

## <a name="create-a-resource-group-and-linux-vm"></a>Criar um Grupo de Recursos e uma VM do Linux
```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Criar uma VM Debian
```azurecli
azure vm quick-create \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -g myResourceGroup \
  -l westus \
  -y Linux \
  -n myVM \
  -Q Debian
```

## <a name="reset-root-password"></a>Redefinir a senha raiz
Para redefinir a senha raiz:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u root \
  -p myNewPassword
```

## <a name="ssh-key-reset"></a>Redefinição de chave SSH
Para redefinir a chave SSH de um usuário não raiz:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Criar um usuário
Para criar um usuário:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -p myAdminUserPassword
```

## <a name="remove-a-user"></a>Remover um usuário
```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -R myRemovedUser
```

## <a name="reset-sshd"></a>Redefinir SSHD
Para redefinir a configuração de SSH:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM
  -r
```


## <a name="detailed-walkthrough"></a>Passo a passo detalhado
### <a name="vmaccess-defined"></a>VMAccess definido:
O disco em sua VM do Linux está mostrando erros. De alguma forma, você redefiniu a senha raiz para sua VM do Linux ou excluiu acidentalmente sua chave privada SSH. Se isso tiver ocorrido na época do datacenter, você precisará ir até lá e abrir o KVM para acessar o console do servidor. Considere a Extensão VMAccess do Azure como o comutador KVM que permite que você acesse o console para redefinir o acesso para o Linux ou para realizar a manutenção no nível de disco.

Para obter o passo a passo detalhado, vamos usar a forma longa da VMAccess, que usa arquivos JSON brutos.  Esses arquivos json da VMAccess também podem ser chamados desde os Modelos do Azure.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Usar a VMAccess para verificar ou reparar o disco de uma VM do Linux
Usando a VMAccess, você pode executar o fsck no disco em sua VM do Linux.  Você também pode fazer uma verificação e um reparo de disco usando uma VMAccess.

Para verificar e reparar o disco, use este script da VMAccess:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Execute o script VMAccess com:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>Usar a VMAccess para redefinir o acesso do usuário para Linux
Se você tiver perdido o acesso à raiz em sua VM do Linux, poderá iniciar um script da VMAccess para redefinir a senha raiz.

Para redefinir a senha raiz, use este script da VMAccess:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword"
}
```

Execute o script VMAccess com:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_root_password.json
```

Para redefinir a chave SSH de um usuário não raiz, use este script da VMAccess:

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM" 
}
```

Execute o script VMAccess com:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>Usar a VMAccess para gerenciar contas de usuário no Linux
A VMAccess é um script Python que pode ser usado para gerenciar usuários em sua VM do Linux sem logon, usando sudo ou a conta raiz.

Para criar um novo usuário, use este script da VMAccess:

`create_new_user.json`

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Execute o script VMAccess com:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path create_new_user.json
```

Para excluir um novo usuário, use este script da VMAccess:

`remove_user.json`

```json
{
  "remove_user":"myDeletedUser"
}
```

Execute o script VMAccess com:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>Usar a VMAccess para redefinir a configuração do SSHD
Se você alterar a configuração do SSHD de VMs do Linux e fechar a conexão SSH antes de verificar as alterações, talvez não consiga usar o SSH novamente.  A VMAccess pode ser usada para redefinir a configuração do SSHD para uma configuração válida conhecida sem estar conectado por SSH.

Para redefinir a configuração do SSHD use esse script VMAccess:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Execute o script VMAccess com:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_sshd.json
```

## <a name="next-steps"></a>Próximas etapas
A atualização do Linux com as Extensões VMAccess do Azure é um método para fazer alterações em uma VM do Linux em execução.  Você também pode usar ferramentas como inicialização de nuvem e os Modelos do Azure para modificar a VM do Linux na inicialização.

[Sobre os recursos e extensões de máquina virtual](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Criando modelos do Azure Resource Manager com extensões de VM do Linux](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Uso de cloud-init para personalizar uma VM do Linux durante a criação](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

