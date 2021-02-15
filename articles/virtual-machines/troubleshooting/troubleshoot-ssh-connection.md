---
title: Solucionar problemas de conexão SSH com uma VM do Azure | Microsoft Docs
description: Como solucionar problemas como 'conexão SSH com falha' ou 'conexão SSH recusada' para uma VM do Azure executando Linux.
keywords: conexão ssh recusada, erro de ssh, ssh do azure, falha de conexão SSH
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: 63c1e388ecd53d9b827e45a1fa78bdb6feeaab21
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201936"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Solucionar problemas em conexões SSH com uma VM Linux do Azure que falha, apresenta erro ou é recusada
Este artigo ajuda a encontrar e corrigir os problemas que ocorrem em razão de erros do Secure Shell (SSH), falhas na conexão de SSH ou quando o SSH é recusado ao tentar se conectar a uma máquina virtual (VM) Linux. Você pode usar o portal do Azure, a CLI do Azure ou a Extensão de Acesso da VM para Linux para solucionar problemas de conexão.


Caso precise de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Etapas rápidas para solucionar problemas
Após cada etapa de solução de problemas, tente se reconectar à VM.

1. [Redefina a configuração de SSH](#reset-the-ssh-configuration).
2. [Redefina as credenciais](#reset-ssh-credentials-for-a-user) do usuário.
3. Verifique se as regras do [grupo de segurança de rede](../../virtual-network/network-security-groups-overview.md) permitem o tráfego SSH.
   * Verifique se existe uma [regra de grupo de segurança de rede](#check-security-rules) para permitir o tráfego SSH (por padrão, porta TCP 22).
   * Você não pode usar o mapeamento/redirecionamento de porta sem usar um Azure Load Balancer.
4. Verifique a [integridade do recurso da VM](../../service-health/resource-health-overview.md).
   * Certifique-se de que a VM é relatada como íntegra.
   * Se você tiver o [diagnóstico de inicialização habilitado](boot-diagnostics.md), verifique se a VM não está relatando erros de inicialização nos logs.
5. [Reinicie a VM](#restart-a-vm).
6. [Reimplante a VM](#redeploy-a-vm).

Caso você precise de etapas e explicações mais detalhadas para solução de problemas, continue lendo.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Métodos disponíveis para solucionar problemas de conexão SSH
Você pode redefinir as credenciais ou configuração de SSH usando um dos seguintes métodos:

* [Portal do Azure](#use-the-azure-portal) – excelente se você precisar redefinir rapidamente as credenciais de usuário ou configurações de SSH ou chave SSH e não tiver as Ferramentas do Azure instaladas.
* [Console serial da VM do Azure](./serial-console-linux.md) -o console serial da VM funcionará independentemente da configuração do SSH e fornecerá a você um console interativo para sua VM. Na verdade, as situações "não é SSH" são especificamente o que o console serial foi projetado para ajudar a solucioná-lo. Mais detalhes abaixo.
* [CLI do Azure](#use-the-azure-cli) - se você já estiver na linha de comando, redefina rapidamente a configuração ou as credenciais do SSH. Se você estiver trabalhando com uma VM clássica, poderá usar a [CLI clássica do Azure](#use-the-azure-classic-cli).
* [Extensão VMAccessForLinux do Azure](#use-the-vmaccess-extension) – criar e reutilizar os arquivos de definição json para redefinir as credenciais de usuário ou configuração do SSH.

Após cada etapa de solução de problemas, tente se conectar à VM novamente. Caso você ainda não consiga conectar, tente a próxima etapa.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure
O portal do Azure fornece uma maneira rápida para redefinir as credenciais de usuário ou configuração do SSH sem instalar as ferramentas no computador local.

Para começar, selecione sua VM no portal do Azure. Role para baixo até a seção **Suporte + Solução de problemas** e selecione **Redefinir senha** como no exemplo a seguir:

![Redefinir a configuração de SSH ou credenciais no Portal do Azure](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Redefinir a configuração de SSH
Para redefinir a configuração de SSH, selecione `Reset configuration only`, na seção **Modo**, conforme mostrado na captura de tela anterior e, em seguida, selecione **Atualizar**. Quando essa ação for concluída, tente acessar sua VM novamente.

### <a name="reset-ssh-credentials-for-a-user"></a>Redefinir credenciais SSH para um usuário
Para redefinir as credenciais de um usuário existente, selecione `Reset SSH public key` ou `Reset password`, na seção **Modo**, conforme mostrado na captura de tela anterior. Especifique o nome de usuário e uma chave SSH ou a nova senha, depois, selecione **Atualizar**.

Você também pode criar um usuário com privilégios sudo na VM nesse menu. Insira um novo nome de usuário e a senha associada ou a chave SSH e, em seguida, selecione **Atualizar**.

### <a name="check-security-rules"></a>Verificar regras de segurança

Use a [verificação de fluxo de IP](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) para confirmar se uma regra em um grupo de segurança de rede está bloqueando o tráfego para ou de uma máquina virtual. Você também pode examinar as regras de grupo de segurança efetivas para garantir que a regra "Permitir" NSG existe e é priorizada para a porta SSH (padrão 22). Para obter mais informações, consulte [usando regras de segurança efetivas para solucionar problemas de fluxo de tráfego de VM](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Verificar o roteamento

Use a funcionalidade [Próximo salto](../../network-watcher/diagnose-vm-network-routing-problem.md) do Observador de Rede para confirmar que uma rota não está impedindo que o tráfego seja roteado de ou para uma máquina virtual. Você também pode examinar as rotas efetivas para ver todas as rotas efetivas para uma interface de rede. Para saber mais, confira [Usar regras efetivas para solucionar problemas de fluxo de tráfego de VM](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-vm-serial-console"></a>Usar o console serial da VM do Azure
O [console serial da VM do Azure](./serial-console-linux.md) fornece acesso a um console baseado em texto para máquinas virtuais do Linux. Você pode usar o console do para solucionar problemas de conexão SSH em um shell interativo. Verifique se você atendeu os [pré-requisitos](./serial-console-linux.md#prerequisites) para usar o console serial e tente os comandos a seguir para solucionar problemas de conectividade SSH.

### <a name="check-that-ssh-is-running"></a>Verificar se o SSH está em execução
Você pode usar o seguinte comando para verificar se o SSH está em execução em sua VM:

```console
ps -aux | grep ssh
```

Se houver qualquer saída, o SSH estará ativo e em execução.

### <a name="check-which-port-ssh-is-running-on"></a>Verificar em qual porta o SSH está sendo executado

Você pode usar o seguinte comando para verificar em qual porta o SSH está em execução:

```console
sudo grep Port /etc/ssh/sshd_config
```

A saída terá uma aparência semelhante a:

```output
Port 22
```

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure
Se você ainda não fez isso, instale a versão mais recente da [CLI do Azure](/cli/azure/install-az-cli2) e entre na conta Azure usando [login az](/cli/azure/reference-index).

Se você criar e carregar uma imagem de disco Linux personalizada, verifique se o [Agente Linux do Microsoft Azure](../extensions/agent-linux.md) versão 2.0.5 ou posterior está instalado. Para VMs criadas usando imagens da galeria, é possível que essa extensão de acesso já esteja instalada e configurada para você.

### <a name="reset-ssh-configuration"></a>Redefinir a configuração de SSH
Inicialmente, você pode tentar redefinir a configuração de SSH para valores padrão e a reinicialização do servidor SSH na VM. Isso não muda o nome, a senha ou as chaves SSH da conta de usuário.
O exemplo a seguir usa [az vm user reset-ssh](/cli/azure/vm/user) para redefinir a configuração de SSH na VM denominada `myVM` em `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Redefinir credenciais SSH para um usuário
O exemplo a seguir usa [az vm user update](/cli/azure/vm/user) para redefinir as credenciais de `myUsername` para o valor especificado em `myPassword`, na VM chamada `myVM` em `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

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
A extensão de acesso da VM para o Linux lê em um arquivo JSON que define as ações a serem executadas. Essas ações incluem a redefinição de SSHD, a redefinição de uma chave SSH ou a adição de um usuário. Você ainda usa a CLI do Azure para chamar a extensão VMAccess, mas você pode reutilizar os arquivos json em várias VMs, se desejado. Essa abordagem permite que você crie um repositório de arquivos json que podem então ser chamados para determinado cenários.

### <a name="reset-sshd"></a>Redefinir SSHD
Crie um arquivo chamado `settings.json` com o conteúdo a seguir:

```json
{
    "reset_ssh":True
}
```

Usando a CLI do Azure, você chama em seguida a extensão `VMAccessForLinux` para redefinir sua conexão SSHD ao especificar o arquivo json. O exemplo a seguir usa [az vm extension set](/cli/azure/vm/extension) para redefinir SSHD na VM denominada `myVM` em `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

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

## <a name="use-the-azure-classic-cli"></a>Usar a CLI do Azure clássica
Se você ainda não o fez [instalar a CLI de clássica do Azure e conecte-se à sua assinatura do Azure](/cli/azure/install-classic-cli). Certifique-se de que você esteja usando o modo Resource Manager da seguinte forma:

```azurecli
azure config mode arm
```

Se você criar e carregar uma imagem de disco Linux personalizada, verifique se o [Agente Linux do Microsoft Azure](../extensions/agent-linux.md) versão 2.0.5 ou posterior está instalado. Para VMs criadas usando imagens da galeria, é possível que essa extensão de acesso já esteja instalada e configurada para você.

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
Para reiniciar uma VM usando o portal do Azure, selecione sua VM e, então, **Reiniciar** como no exemplo a seguir:

![Reiniciar uma VM no Portal do Azure](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>CLI do Azure
O exemplo a seguir usa [az vm restart](/cli/azure/vm) para reiniciar a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>CLI clássica do Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

O exemplo a seguir reinicia a VM denominada `myVM` no grupo de recursos denominado `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```console
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="redeploy-a-vm"></a>Reimplantar uma VM
Você pode reimplantar uma VM para outro nó no Azure, o que pode corrigir possíveis problemas de rede subjacentes. Para obter informações sobre como reimplantar uma VM, consulte [Reimplantar Máquina Virtual em um novo nó do Azure](./redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json).

> [!NOTE]
> Após a conclusão dessa operação, os dados de disco efêmeros são perdidos e os endereços IP dinâmicos associados à máquina virtual são atualizados.
>
>

### <a name="azure-portal"></a>Portal do Azure
Para reimplantar uma VM usando o Portal do Azure, selecione sua VM e role para baixo até a seção **Suporte + Solução de Problemas**. Selecione **Reimplantar**, como no seguinte exemplo:

![Reimplantar a VM no Portal do Azure](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>CLI do Azure
O exemplo a seguir usa [az vm redeploy](/cli/azure/vm) para reimplantar a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>CLI clássica do Azure

O exemplo a seguir reimplanta a VM denominada `myVM` no grupo de recursos denominado `myResourceGroup`. Use seus próprios valores, da seguinte maneira:

```console
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>VMs criadas com o modelo de implantação clássico

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Experimente essas etapas para resolver as falhas de conexão SSH mais comuns em VMs criadas usando o modelo de implantação clássico. Após cada etapa, tente se reconectar à VM.

* Redefina o acesso remoto no [Portal do Azure](https://portal.azure.com). No portal do Azure, selecione a sua VM e, então, **Redefinir Remoto...**.
* Reinicie a VM. No [portal do Azure](https://portal.azure.com), selecione a sua VM e, então **Reiniciar**.

* Reimplante a VM em um novo nó do Azure. Para obter informações sobre como reimplantar uma VM, veja [Reimplantar Máquina Virtual em um novo nó do Azure](./redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json).

    Após a conclusão dessa operação, os dados de disco efêmeros serão perdidos e os endereços IP dinâmicos associados à máquina virtual serão atualizados.
* Siga as instruções em [Como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic) para:

  * Redefinir a senha ou a chave SSH.
  * Criar uma nova conta de usuário *sudo*.
  * Redefinir a configuração de SSH.
* Verifique se há problemas de plataforma na integridade do recurso da VM.<br>
     Selecione sua VM e role para baixo **configurações**  >  **verificar integridade**.

## <a name="additional-resources"></a>Recursos adicionais
* Se ainda não puder se conectar com SSH à VM após seguir essas etapas, veja [etapas de solução de problemas mais detalhadas](detailed-troubleshoot-ssh-connection.md) para examinar etapas adicionais para resolver o problema.
* Para obter mais informações sobre como solucionar problemas de acesso do aplicativo, consulte [Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)
* Para obter mais informações sobre como solucionar problemas de máquinas virtuais que foram criadas usando o modelo de implantação clássico, consulte [Como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic).