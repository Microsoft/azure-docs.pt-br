---
title: Redefinir o acesso a uma VM do Linux do Azure | Microsoft Docs
description: Como gerenciar usuários administrativos e redefinir o acesso em VMs Linux usando a extensão VMAccess e a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: roiyz
ms.openlocfilehash: 2841aedc1a4fe69e2604d3c6b95cc12dab4c7716
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706612"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Gerenciar usuários administrativos, SSH e verificar ou reparar discos em VMs Linux do usando a extensão VMAccess com a CLI do Azure
## <a name="overview"></a>Visão geral
O disco em sua VM do Linux está mostrando erros. De alguma forma, você redefiniu a senha raiz para sua VM do Linux ou excluiu acidentalmente sua chave privada SSH. Se isso tiver ocorrido na época do datacenter, você precisará ir até lá e abrir o KVM para acessar o console do servidor. Considere a Extensão VMAccess do Azure como o comutador KVM que permite que você acesse o console para redefinir o acesso para o Linux ou para realizar a manutenção no nível de disco.

Este artigo mostra como usar a extensão VMAccess do Azure para verificar ou reparar um disco, redefinir o acesso do usuário, gerenciar contas de usuário administrativo ou atualizar a configuração do SSH no Linux quando eles estão sendo executados como máquinas virtuais do Azure Resource Manager. Se precisar gerenciar máquinas virtuais Clássicas, você poderá seguir as instruções encontradas na [documentação de VM clássica](../linux/classic/reset-access-classic.md). 
 
> [!NOTE]
> Se você usar a extensão VMAccess para redefinir a senha da sua VM depois de instalar a extensão de logon do AAD, será preciso executar novamente a extensão de logon do AAD para habilitar novamente o logon do AAD para seu computador.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="operating-system"></a>Sistema operacional

A extensão de Acesso de VM pode ser executada nessas distribuições do Linux:

| Distribuição | Versão |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS e 12.04 LTS |
| Debian | Debian 7.9+, 8.2+ |
| Red Hat | RHEL 6.7+, 7.1+ |
| Oracle Linux | 6.4+, 7.0+ |
| Suse | 11 e 12 |
| OpenSuse | openSUSE Leap 42.2+ |
| CentOS | CentOS 6.3+, 7.0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Modos de usar a extensão VMAccess
Há duas maneiras de usar a extensão VMAccess em VMs Linux:

* Use a CLI do Azure e os parâmetros necessários.
* [Use os arquivos JSON brutos processados pela Extensão VMAccess](#use-json-files-and-the-vmaccess-extension) e depois tome atitudes em relação a eles.

Os exemplos seguintes usam comandos [az vm user](/cli/azure/vm/user). Para realizar essas etapas, é preciso ter a [CLI do Azure](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando [az login](/cli/azure/reference-index).

## <a name="update-ssh-key"></a>Atualizar chave SSH
O exemplo a seguir atualiza a chave SSH para o usuário `azureuser` na VM denominada `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **OBSERVAÇÃO:** O comando `az vm user update` acrescenta o novo texto de chave pública ao arquivo `~/.ssh/authorized_keys` para o usuário administrador na VM. Isso não substitui ou remove quaisquer chaves SSH existentes. Isso não removerá as chaves anteriores definidas no momento da implantação ou atualizações subsequentes através da extensão VMAccess.

## <a name="reset-password"></a>Redefinir senha
O exemplo a seguir redefine a senha para o usuário `azureuser` na VM denominada `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Reiniciar o SSH
O exemplo a seguir reiniciará o daemon SSH e redefine a configuração de SSH para valores padrão em uma VM denominada `myVM`:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Criar um usuário administrativo/sudo
O exemplo a seguir cria um usuário chamado `myNewUser` com permissões **sudo**. A conta usa uma chave SSH para autenticação na VM denominada `myVM`. Esse método foi projetado para ajudá-lo a recuperar o acesso a uma VM caso as credenciais atuais sejam perdidas ou esquecidas. Como melhor prática, contas com permissões **sudo** devem ser limitadas.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Excluir um usuário
O exemplo a seguir exclui um usuário chamado `myNewUser` na VM denominada `myVM`:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Usar arquivos JSON e a extensão VMAccess
Os exemplos a seguir usam arquivos JSON brutos. Use [az vm extension set](/cli/azure/vm/extension) para chamar seus arquivos JSON. Esses arquivos JSON também podem ser chamados dos modelos do Azure. 

### <a name="reset-user-access"></a>Redefinir o acesso do usuário
Se você tiver perdido o acesso à raiz em sua VM do Linux, poderá iniciar um script da VMAccess para atualizar a senha ou a chave SSH de um usuário.

Para atualizar a chave pública SSH de um usuário, crie um arquivo chamado `update_ssh_key.json` e adicione as configurações no seguinte formato. Substitua seus próprios valores para os parâmetros `username` e `ssh_key`:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Execute o script VMAccess com:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Para redefinir uma senha de usuário, crie um arquivo chamado `reset_user_password.json` e adicione configurações no formato a seguir. Substitua seus próprios valores para os parâmetros `username` e `password`:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Execute o script VMAccess com:

```azurecli-interactive
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

```azurecli-interactive
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

```azurecli-interactive
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

```azurecli-interactive
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

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solução de problemas

Dados sobre o estado das implantações de extensão podem ser recuperados do Portal do Azure usando a CLI do Azure. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir usando a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
