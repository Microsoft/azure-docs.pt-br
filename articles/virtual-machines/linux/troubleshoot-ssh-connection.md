---
title: "Solucionar problemas de conexão SSH com uma VM do Azure | Microsoft Docs"
description: "Como solucionar problemas como 'conexão SSH com falha' ou 'conexão SSH recusada' para uma VM do Azure executando Linux."
keywords: "conexão ssh recusada, erro de ssh, ssh do azure, falha de conexão SSH"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 3a282c8b2c2ba2749de6a2d3688bd57d75703b22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Solucionar problemas em conexões SSH com uma VM Linux do Azure que falha, apresenta erro ou é recusada
Há vários motivos pelos quais você pode encontrar erros de SSH (Secure Shell), falhas na conexão SSH ou então o SSH é recusado ao tentar se conectar a uma VM (máquina virtual) Linux. Este artigo ajuda você a localizar e corrigir os problemas. Você pode usar o portal do Azure, a CLI do Azure ou a Extensão de Acesso da VM para Linux para solucionar problemas de conexão.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Caso precise de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](http://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](http://azure.microsoft.com/support/options/) e selecione **Obter suporte**. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](http://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Etapas rápidas para solucionar problemas
Após cada etapa de solução de problemas, tente se reconectar à VM.

1. Redefinir a configuração de SSH.
2. Redefinir as credenciais para o usuário.
3. Verifique se as regras do [Grupo de Segurança de Rede](../../virtual-network/virtual-networks-nsg.md) permitem o tráfego SSH.
   * Certifique-se de que existe uma regra de Grupo de Segurança de Rede para permitir o tráfego SSH (por padrão, a porta TCP 22).
   * Você não pode usar o mapeamento/redirecionamento de porta sem usar um Azure Load Balancer.
4. Verifique a [Integridade do Recurso de VM](../../resource-health/resource-health-overview.md). 
   * Certifique-se de que a VM é relatada como íntegra.
   * Se você tiver habilitado o diagnóstico de inicialização, verifique se a VM não está relatando erros de inicialização nos logs.
5. Reinicie a VM.
6. Reimplante a VM.

Caso você precise de etapas e explicações mais detalhadas para solução de problemas, continue lendo.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Métodos disponíveis para solucionar problemas de conexão SSH
Você pode redefinir as credenciais ou configuração de SSH usando um dos seguintes métodos:

* [Portal do Azure](#use-the-azure-portal) – excelente se você precisar redefinir rapidamente as credenciais de usuário ou configurações de SSH ou chave SSH e não tiver as Ferramentas do Azure instaladas.
* [CLI 2.0 do Azure](#use-the-azure-cli-20) – se você já estiver na linha de comando, redefina rapidamente as credenciais ou a configuração de SSH. Também é possível usar a [CLI do Azure 1.0](#use-the-azure-cli-10)
* [Extensão VMAccessForLinux do Azure](#use-the-vmaccess-extension) – criar e reutilizar os arquivos de definição json para redefinir as credenciais de usuário ou configuração do SSH.

Após cada etapa de solução de problemas, tente se conectar à VM novamente. Caso você ainda não consiga conectar, tente a próxima etapa.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure
O portal do Azure fornece uma maneira rápida para redefinir as credenciais de usuário ou configuração do SSH sem instalar as ferramentas no computador local.

No Portal do Azure, selecione sua VM. Role para baixo até a seção **Suporte + Solução de problemas** e selecione **Redefinir senha** como no exemplo a seguir:

![Redefinir a configuração de SSH ou credenciais no Portal do Azure](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Redefinir a configuração de SSH
Como uma primeira etapa, selecione `Reset configuration only` do menu suspenso **Modo** como na captura de tela anterior, depois clique no botão **Redefinir**. Quando essa ação for concluída, tente acessar sua VM novamente.

### <a name="reset-ssh-credentials-for-a-user"></a>Redefinir credenciais SSH para um usuário
Para redefinir as credenciais de um usuário existente, selecione `Reset SSH public key` ou `Reset password` do menu suspenso **Modo** como na captura de tela anterior. Especifique o nome de usuário e uma chave SSH ou a nova senha, depois clique no botão **Redefinir**.

Você também pode criar um usuário com privilégios sudo na VM nesse menu. Insira um novo nome de usuário e senha associada ou chave SSH e, em seguida, clique no botão **Redefinir**.

## <a name="use-the-azure-cli-20"></a>Usar a CLI 2.0 do Azure
Se você ainda não fez isso, instale a versão mais recente da [CLI 2.0 do Azure](/cli/azure/install-az-cli2) e faça logon na conta do Azure usando [az login](/cli/azure/#login).

Se você criar e carregar uma imagem de disco Linux personalizada, verifique se o [Agente Linux do Microsoft Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) versão 2.0.5 ou posterior está instalado. Para VMs criadas usando imagens da galeria, é possível que essa extensão de acesso já esteja instalada e configurada para você.

### <a name="reset-ssh-configuration"></a>Redefinir a configuração de SSH
Inicialmente, você pode tentar redefinir a configuração de SSH para valores padrão e a reinicialização do servidor SSH na VM. Observe que isso não muda o nome, a senha ou as chaves SSH da conta de usuário.
O exemplo a seguir usa [az vm user reset-ssh](/cli/azure/vm/user#reset-ssh) para redefinir a configuração de SSH na VM denominada `myVM` em `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Redefinir credenciais SSH para um usuário
O exemplo a seguir usa [az vm user update](/cli/azure/vm/user#update) para redefinir as credenciais de `myUsername` para o valor especificado em `myPassword`, na VM chamada `myVM` em `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Se usar autenticação de chave SSH, você poderá redefinir a chave SSH para um determinado usuário. O exemplo a seguir usa **az vm access set-linux-user** para atualizar a chave SSH armazenada em `~/.ssh/id_rsa.pub` para o usuário chamado `myUsername`, na VM chamada `myVM` em `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Usar a extensão VMAccess
A Extensão de Acesso de VM para Linux lê um arquivo json que define as ações a executar. Essas ações incluem redefinir o SSHD, redefinir uma chave SSH ou adicionar um usuário. Você ainda usa a CLI do Azure para chamar a extensão VMAccess, mas você pode reutilizar os arquivos json em várias VMs, se desejado. Essa abordagem permite que você crie um repositório de arquivos json que podem então ser chamados para determinado cenários.

### <a name="reset-sshd"></a>Redefinir SSHD
Crie um arquivo chamado `settings.json` com o conteúdo a seguir:

```json
{  
    "reset_ssh":"True"
}
```

Usando a CLI do Azure, você chama em seguida a extensão `VMAccessForLinux` para redefinir sua conexão SSHD ao especificar o arquivo json. O exemplo a seguir usa [az vm extension set](/cli/azure/vm/extension#set) para redefinir SSHD na VM denominada `myVM` em `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Redefinir credenciais SSH para um usuário
Se o SSHD parecer funcionar corretamente, você poderá redefinir as credenciais para um determinado usuário. Para redefinir a senha de um usuário, crie um arquivo chamado `settings.json`. O exemplo a seguir redefine as credenciais para `myUsername` para o valor especificado em `myPassword`. Digite as seguintes linhas no seu arquivo de `settings.json`, usando seus próprios valores:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Ou, para redefinir a chave SSH para um usuário, primeiro crie um arquivo chamado `settings.json`. O exemplo a seguir redefine as credenciais para `myUsername` para o valor especificado em `myPassword`, na VM denominada `myVM` em `myResourceGroup`. Digite as seguintes linhas no seu arquivo de `settings.json`, usando seus próprios valores:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Depois de criar o arquivo json, use a CLI do Azure para chamar a extensão `VMAccessForLinux` para redefinir suas credenciais de usuário SSH, especificando seu arquivo json. O exemplo a seguir redefine as credenciais na VM denominada `myVM` em `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-cli-10"></a>Usar a CLI 1.0 do Azure
Se você ainda não fez isso, [instale a CLI 1.0 do Azure e conecte-se à sua assinatura do Azure](../../cli-install-nodejs.md). Certifique-se de que você esteja usando o modo Resource Manager da seguinte forma:

```azurecli
azure config mode arm
```

Se você criar e carregar uma imagem de disco Linux personalizada, verifique se o [Agente Linux do Microsoft Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) versão 2.0.5 ou posterior está instalado. Para VMs criadas usando imagens da galeria, é possível que essa extensão de acesso já esteja instalada e configurada para você.

### <a name="reset-ssh-configuration"></a>Redefinir a configuração de SSH
A configuração SSHD em si pode estar definida incorretamente ou o serviço encontrou um erro. Você pode redefinir o SSHD para garantir que a configuração de SSH em si é válida. Redefinir SSHD deve ser a primeira etapa de solução de problemas para você realizar.

O exemplo a seguir redefine o SSHD na VM denominada `myVM` no grupo de recursos chamado `myResourceGroup`. Use seus próprios nomes de grupo de recursos e de VM, da seguinte maneira:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Redefinir credenciais SSH para um usuário
Se o SSHD parecer funcionar corretamente, você poderá redefinir a senha para um determinado usuário. O exemplo a seguir redefine as credenciais para `myUsername` para o valor especificado em `myPassword`, na VM denominada `myVM` em `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Se usar autenticação de chave SSH, você poderá redefinir a chave SSH para um determinado usuário. O exemplo a seguir atualiza a chave SSH armazenada em `~/.ssh/id_rsa.pub` para o usuário chamado `myUsername`, na VM denominada `myVM` em `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```


## <a name="restart-a-vm"></a>Reiniciar uma VM
Se você tiver redefinido as credenciais de usuário e a configuração do SSH ou encontrado um erro ao fazer isso, você poderá tentar reiniciar a VM para solucionar problemas de computação subjacentes.

### <a name="azure-portal"></a>Portal do Azure
Para reiniciar uma VM usando o Portal do Azure, selecione sua VM e clique no botão **Reiniciar** como no exemplo a seguir:

![Reiniciar uma VM no Portal do Azure](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli-10"></a>CLI 1.0 do Azure
O exemplo a seguir reinicia a VM denominada `myVM` no grupo de recursos denominado `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
O exemplo a seguir usa [az vm restart](/cli/azure/vm#restart) para reiniciar a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Reimplantar uma VM
Você pode reimplantar uma VM para outro nó no Azure, o que pode corrigir possíveis problemas de rede subjacentes. Para obter informações sobre como reimplantar uma VM, consulte [Reimplantar Máquina Virtual em um novo nó do Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Após a conclusão dessa operação, os dados de disco efêmeros serão perdidos e os endereços IP dinâmicos associados à máquina virtual serão atualizados.
> 
> 

### <a name="azure-portal"></a>Portal do Azure
Para reimplantar uma VM usando o Portal do Azure, selecione sua VM e role para baixo até a seção **Suporte + Solução de Problemas**. Clique no botão **Reimplantar** como no exemplo a seguir:

![Reimplantar a VM no Portal do Azure](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli-10"></a>CLI 1.0 do Azure
O exemplo a seguir reimplanta a VM denominada `myVM` no grupo de recursos denominado `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
O exemplo a seguir usa [az vm redeploy](/cli/azure/vm#redeploy) para reimplantar a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>VMs criadas com o modelo de implantação clássico
Experimente essas etapas para resolver as falhas de conexão SSH mais comuns em VMs criadas usando o modelo de implantação clássico. Após cada etapa, tente se reconectar à VM.

* Redefina o acesso remoto no [Portal do Azure](https://portal.azure.com). No Portal do Azure, selecione sua VM e clique no botão **Redefinir Remoto...**.
* Reinicie a VM. No [Portal do Azure](https://portal.azure.com), selecione sua VM e clique no botão **Reiniciar**.
    
* Reimplante a VM em um novo nó do Azure. Para obter informações sobre como reimplantar uma VM, veja [Reimplantar Máquina Virtual em um novo nó do Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
    Após a conclusão dessa operação, os dados de disco efêmeros serão perdidos e os endereços IP dinâmicos associados à máquina virtual serão atualizados.
* Siga as instruções em [Como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](classic/reset-access.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) para:
  
  * Redefinir a senha ou a chave SSH.
  * Criar uma nova conta de usuário *sudo*.
  * Redefinir a configuração de SSH.
* Verifique se há problemas de plataforma na integridade do recurso da VM.<br>
     Selecione sua VM e role para baixo para **Configurações** > **Verificar Integridade**.

## <a name="additional-resources"></a>Recursos adicionais
* Se ainda não puder se conectar com SSH à VM após seguir essas etapas, veja [etapas de solução de problemas mais detalhadas](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para examinar etapas adicionais para resolver o problema.
* Para obter mais informações sobre como solucionar problemas de acesso do aplicativo, consulte [Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Para obter mais informações sobre como solucionar problemas de máquinas virtuais que foram criadas usando o modelo de implantação clássico, consulte [Como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](classic/reset-access.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

