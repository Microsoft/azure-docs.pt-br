---
title: Redefinir o acesso a uma VM do Linux do Azure | Microsoft Docs
description: "Como gerenciar usuários administrativos e redefinir o acesso em VMs Linux usando a extensão VMAccess e a CLI do Azure 2.0"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 08/04/2017
ms.author: danlep
ms.openlocfilehash: 3596b50b68cabf212218825566c0f8313f054f65
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>Gerenciar usuários administrativos, SSH e verificar ou reparar discos em VMs Linux do usando a extensão VMAccess com a CLI do Azure 2.0
O disco em sua VM do Linux está mostrando erros. De alguma forma, você redefiniu a senha raiz para sua VM do Linux ou excluiu acidentalmente sua chave privada SSH. Se isso tiver ocorrido na época do datacenter, você precisará ir até lá e abrir o KVM para acessar o console do servidor. Considere a Extensão VMAccess do Azure como o comutador KVM que permite que você acesse o console para redefinir o acesso para o Linux ou para realizar a manutenção no nível de disco.

Este artigo mostra como usar a extensão VMAccess do Azure para verificar ou reparar um disco, redefinir o acesso do usuário, gerenciar contas de usuário administrativo ou redefinir a configuração do SSH no Linux. Você também pode executar essas etapas com a [CLI do Azure 1.0](using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="ways-to-use-the-vmaccess-extension"></a>Modos de usar a extensão VMAccess
Há duas maneiras de usar a extensão VMAccess em VMs Linux:

* Use a CLI 2.0 do Azure e os parâmetros necessários.
* [Use os arquivos JSON brutos processados pela Extensão VMAccess](#use-json-files-and-the-vmaccess-extension) e depois tome atitudes em relação a eles.

Os exemplos seguintes usam comandos [az vm user](/cli/azure/vm/user). Para realizar essas etapas, é preciso ter a [CLI 2.0 do Azure](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando o [logon az](/cli/azure/#login).

## <a name="reset-ssh-key"></a>Redefinir chave SSH
O exemplo a seguir redefine a chave SSH para o usuário `azureuser` na VM denominada `myVM`:

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="reset-password"></a>Redefinir senha
O exemplo a seguir redefine a senha para o usuário `azureuser` na VM denominada `myVM`:

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Reiniciar o SSH
O exemplo a seguir reiniciará o daemon SSH e redefine a configuração de SSH para valores padrão em uma VM denominada `myVM`:

```azurecli
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Criar um usuário administrativo/sudo
O exemplo a seguir cria um usuário chamado `myNewUser` com permissões **sudo**. A conta usa uma chave SSH para autenticação na VM denominada `myVM`. Esse método foi projetado para ajudá-lo a recuperar o acesso a uma VM caso as credenciais atuais sejam perdidas ou esquecidas. Como melhor prática, contas com permissões **sudo** devem ser limitadas.

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```



## <a name="delete-a-user"></a>Excluir um usuário
O exemplo a seguir exclui um usuário chamado `myNewUser` na VM denominada `myVM`:

```azurecli
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>Usar arquivos JSON e a extensão VMAccess
Os exemplos a seguir usam arquivos JSON brutos. Use [az vm extension set](/cli/azure/vm/extension#set) para chamar seus arquivos JSON. Esses arquivos JSON também podem ser chamados dos modelos do Azure. 

### <a name="reset-user-access"></a>Redefinir o acesso do usuário
Se você tiver perdido o acesso à raiz em sua VM do Linux, poderá iniciar um script da VMAccess para redefinir a senha ou a chave SSH de um usuário.

Para redefinir a chave pública SSH de um usuário, crie um arquivo chamado `reset_ssh_key.json` e adicione as configurações no seguinte formato. Substitua seus próprios valores para os parâmetros `username` e `ssh_key`:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Execute o script VMAccess com:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_ssh_key.json
```

Para redefinir uma senha de usuário, crie um arquivo chamado `reset_user_password.json` e adicione configurações no formato a seguir. Substitua seus próprios valores para os parâmetros `username` e `password`:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Execute o script VMAccess com:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Reiniciar o SSH
Para reiniciar o daemon SSH e redefinir a configuração de SSH para valores padrão, crie um arquivo chamado `reset_sshd.json`. Adicione o seguinte conteúdo:

```json
{
  "reset_ssh": true
}
```

Execute o script VMAccess com:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Gerenciar usuários administrativos

Para criar um usuário com permissões **sudo** que usa uma chave SSH para autenticação, crie um arquivo chamado `create_new_user.json` e adicione as configurações no formato a seguir. Substitua os valores dos parâmetros `username` e `ssh_key` pelos seus próprios. Esse método foi projetado para ajudá-lo a recuperar o acesso a uma VM caso as credenciais atuais sejam perdidas ou esquecidas. Como melhor prática, contas com permissões **sudo** devem ser limitadas.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Execute o script VMAccess com:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Para excluir um usuário, crie um arquivo chamado `delete_user.json` e adicione o conteúdo a seguir. Substitua seu próprio valor para o parâmetro `remove_user`:

```json
{
  "remove_user":"myNewUser"
}
```

Execute o script VMAccess com:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Verificar ou reparar o disco
Usar o VMAccess você pode verificar e reparar um disco que você adicionou à VM Linux.

Para verificar e reparar o disco, crie um arquivo chamado `disk_check_repair.json` e adicione as configurações no seguinte formato. Substitua seu próprio valor para o nome do `repair_disk`:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Execute o script VMAccess com:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```

## <a name="next-steps"></a>Próximas etapas
A atualização do Linux com a Extensão VMAccess do Azure é um método para fazer alterações em uma VM Linux em execução. Você também pode usar ferramentas como inicialização de nuvem e os modelos do Azure Resource Manager para modificar a VM Linux na inicialização.

[Recursos e extensões da máquina virtual para Linux](extensions-features.md)

[Criando modelos do Azure Resource Manager com extensões de VM do Linux](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Uso de cloud-init para personalizar uma VM do Linux durante a criação](using-cloud-init.md)

