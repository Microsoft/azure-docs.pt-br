---
title: Não é possível conectar com o RDP a uma VM do Windows no Azure | Microsoft Docs
description: Solucionar problemas quando você não pode se conectar à máquina virtual do Windows no Azure usando a Área de Trabalho Remota
keywords: Erro de área de trabalho remota, erro de conexão de área de trabalho remota, não é possível conectar-se à VM, solução de problemas da área de trabalho remota
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: roiyz
ms.openlocfilehash: 711c5cb8211de8b5ec27cfd76f12c34c84676e64
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710509"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Solucionar problemas de conexões de Área de Trabalho Remota a uma máquina virtual do Azure
A conexão do protocolo RDP à sua VM (máquina virtual) do Azure baseada no Windows pode falhar por vários motivos, impedindo o seu acesso à VM. O problema pode ser com o serviço de Área de Trabalho Remota na VM, a conexão de rede ou o cliente de Área de Trabalho Remota no computador host. Este artigo explica alguns dos métodos mais comuns para resolver problemas de conexão de RDP. 

Caso precise de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Etapas rápidas para solucionar problemas
Após cada etapa da solução de problemas, tente se reconectar à VM:

1. Redefina a configuração da Área de Trabalho Remota.
2. Verifique as regras do Grupo de Segurança de Rede/de pontos de extremidade dos Serviços de Nuvem.
3. Examine os logs do console da VM.
4. Redefina a NIC para a VM.
5. Verifique a Integridade do Recurso de VM.
6. Redefina a senha da VM.
7. Reinicie a VM.
8. Reimplante a VM.

Caso você precise de etapas e explicações mais detalhadas, continue lendo. Verifique se equipamentos de rede local, como roteadores e firewalls, não estão bloqueando a porta de saída TCP 3389, conforme observado nos [cenários detalhados de solução de problemas de RDP](detailed-troubleshoot-rdp.md).

> [!TIP]
> Se o botão **Conectar** para a VM estiver esmaecido e você não estiver conectado ao Azure por meio de uma [Rota Expressa](../../expressroute/expressroute-introduction.md) ou de uma conexão [VPN Site a Site](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), será necessário criar um endereço IP público e atribui-lo à VM antes que seja possível usar o RDP. Leia mais sobre [endereços IP públicos no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Maneiras de solucionar problemas com RDP
Usando um dos seguintes métodos, você pode solucionar problemas de VMs criadas com o modelo de implantação do Resource Manager:

* Portal do Azure – excelente se você precisar redefinir rapidamente as credenciais de usuário ou configurações de RDP e não tiver as Ferramentas do Azure instaladas.
* Azure PowerShell – caso você esteja familiarizado com um prompt do PowerShell, redefina rapidamente as credenciais de usuário ou configurações de RDP usando os cmdlets do Azure PowerShell.

Você também pode encontrar etapas sobre como solucionar problemas de VMs criadas usando o [Modelo de implantação clássico](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Solucionar problemas usando o Portal do Azure
Após cada etapa de solução de problemas, tente se conectar à VM novamente. Caso você ainda não consiga conectar, tente a próxima etapa.

1. **Redefina a conexão RDP**. Essa etapa de solução de problemas redefine a configuração de RDP quando as Conexões Remotas estão desabilitadas ou as regras do Firewall do Windows estão bloqueando o RDP, por exemplo.
   
    No Portal do Azure, selecione sua VM. Role para baixo no painel de configurações até a seção **Suporte + Solução de Problemas**, próximo à parte inferior da lista. Clique no botão **Redefinir senha**. Defina o **Modo** como **Redefinir somente configuração** e, em seguida, clique no botão **Atualizar**:
   
    ![Redefinir a configuração de RDP no Portal do Azure](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Verifique as regras de Grupo de Segurança de Rede**. Use a [verificação de fluxo de IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) para confirmar se uma regra em um Grupo de Segurança de Rede está bloqueando o tráfego de ou para uma máquina virtual. Você também pode examinar as regras de grupo de segurança efetivas para garantir que a regra "Permitir" NSG existe e é priorizada para a porta RDP(padrão 3389). Para saber mais, confira [Usar regras de segurança efetivas para solucionar problemas de fluxo de tráfego de VM](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Examine o diagnóstico de inicialização da VM**. Essa etapa de solução de problemas examina os logs do console da VM para determinar se a VM está reportando um problema. Nem todas as VMs têm diagnóstico de inicialização habilitado, portanto, essa etapa de solução de problemas pode ser opcional.
   
    Etapas de solução de problemas específicas estão além do escopo deste artigo, mas podem indicar um problema maior que está afetando a conectividade RDP. Para obter mais informações sobre como verificar os logs de console e a captura de tela de VM, consulte [Diagnósticos de Inicialização para VMs](boot-diagnostics.md).

4. **Redefina a NIC para a VM**. Para obter mais informações, confira [como redefinir a NIC para a VM do Windows Azure](../windows/reset-network-interface.md).
5. **Verifique a Integridade do Recurso de VM**. Essa etapa de solução de problemas verifica se não existem problemas conhecidos com a plataforma Azure que possam afetar a conectividade com a VM.
   
    No Portal do Azure, selecione sua VM. Role para baixo no painel de configurações até a seção **Suporte + Solução de Problemas**, próximo à parte inferior da lista. Clique no botão **Integridade do recurso**. Uma VM íntegra é reportada como estando **Disponível**:
   
    ![Verifique a integridade do recurso da VM no Portal do Azure](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Redefina as credenciais do usuário**. Essa etapa de solução de problemas redefinirá a senha de uma conta de administrador local quando você não tiver certeza ou tiver esquecido quais são as credenciais.  Após conectar a VM, será necessário redefinir a senha para esse usuário.
   
    No Portal do Azure, selecione sua VM. Role para baixo no painel de configurações até a seção **Suporte + Solução de Problemas**, próximo à parte inferior da lista. Clique no botão **Redefinir senha**. Certifique-se de que o **Modo** está definido como **Redefinir senha** e, em seguida, digite seu nome de usuário e uma nova senha. Por fim, clique no botão **Atualizar**:
   
    ![Redefinir as credenciais do usuário no Portal do Azure](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Reinicie a VM**. Essa etapa de solução de problemas pode corrigir qualquer problema subjacente experienciado pela própria VM.
   
    Selecione sua VM no Portal do Azure e clique na guia **Visão Geral**. Clique no botão **Reiniciar**:
   
    ![Reiniciar a VM no Portal do Azure](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Reimplante a VM**. Essa etapa de solução de problemas reimplanta sua VM para outro host dentro do Azure para corrigir problemas subjacentes de rede ou de plataforma.
   
    No Portal do Azure, selecione sua VM. Role para baixo no painel de configurações até a seção **Suporte + Solução de Problemas**, próximo à parte inferior da lista. Clique no botão **Reimplantar** e, em seguida, clique em **Reimplantar**:
   
    ![Reimplantar a VM no Portal do Azure](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Após a conclusão dessa operação, os dados de disco efêmeros são perdidos e os endereços IP dinâmicos associados à VM são atualizados.

9. **Verificar o roteamento**. Use a funcionalidade [Próximo salto](../../network-watcher/network-watcher-check-next-hop-portal.md) do Observador de Rede para confirmar que uma rota não está impedindo que o tráfego seja roteado de ou para uma máquina virtual. Você também pode examinar as rotas efetivas para ver todas as rotas efetivas para uma interface de rede. Para saber mais, confira [Usar regras efetivas para solucionar problemas de fluxo de tráfego de VM](../../virtual-network/diagnose-network-routing-problem.md).

10. Certifique-se de que qualquer firewall local, ou firewall em seu computador, permita o tráfego de saída TCP 3389 no Azure.

Caso ainda esteja tendo problemas de RDP, você poderá [abrir uma solicitação](https://azure.microsoft.com/support/options/) de suporte ou ler [conceitos e etapas de solução de problemas de RDP mais detalhados](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Solucionar problemas usando o Azure PowerShell
Se você ainda não fez isso, [instale e configure o último Azure PowerShell](/powershell/azure/overview).

Os exemplos a seguir usam variáveis como `myResourceGroup`, `myVM` e `myVMAccessExtension`. Substitua essas localizações e nomes de variáveis por seus próprios valores.

> [!NOTE]
> Você redefine as credenciais do usuário e a configuração de RDP usando o cmdlet [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) do PowerShell. Nos exemplos a seguir, `myVMAccessExtension` é um nome que você especifica como parte do processo. Caso você tenha trabalhado anteriormente com o VMAccessAgent, será possível obter o nome da extensão existente usando `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` para verificar as propriedades da VM. Veja a seção 'Extensões' da saída para exibir o nome.

Após cada etapa de solução de problemas, tente se conectar à VM novamente. Caso você ainda não consiga conectar, tente a próxima etapa.

1. **Redefina a conexão RDP**. Essa etapa de solução de problemas redefine a configuração de RDP quando as Conexões Remotas estão desabilitadas ou as regras do Firewall do Windows estão bloqueando o RDP, por exemplo.
   
    O exemplo a seguir redefine a conexão de RDP em uma VM denominada `myVM` na localização `WestUS` e no grupo de recursos denominado `myResourceGroup`:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Verifique as regras de Grupo de Segurança de Rede**. Essa etapa de solução de problemas verifica se você tem uma regra em seu Grupo de Segurança de Rede para permitir o tráfego RDP. A porta padrão para RDP é a porta TCP 3389. É possível que uma regra para permitir o tráfego RDP não seja criada automaticamente quando você criar a VM.
   
    Primeiro, atribua todos os dados de configuração do Grupo de Segurança de Rede à variável `$rules`. O exemplo a seguir obtém informações sobre o Grupo de Segurança de Rede denominado `myNetworkSecurityGroup` no grupo de recursos denominado `myResourceGroup`:
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Agora, exiba as regras que estão configuradas para esse Grupo de Segurança de Rede. Verifique se existe uma regra para permitir o uso da porta TCP 3389 para conexões de entrada, conforme demonstrado a seguir:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    O exemplo a seguir mostra uma regra de segurança válida que permite o tráfego RDP. Você pode ver que `Protocol`, `DestinationPortRange`, `Access` e `Direction` estão configuradas corretamente:
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    Se você não tiver uma regra que permita o tráfego RDP, deverá [criar uma regra de Grupo de Segurança de Rede](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Permita a porta TCP 3389.
3. **Redefina as credenciais do usuário**. Essa etapa de solução de problemas redefinirá a senha da conta de administrador local, que você especificará quando não tiver certeza ou tiver esquecido quais são as credenciais.
   
    Primeiro, especifique o nome de usuário e uma nova senha, atribuindo as credenciais à variável `$cred` da seguinte maneira:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Agora, atualize as credenciais na VM. O exemplo a seguir atualiza as credenciais em uma VM denominada `myVM` na localização `WestUS` e no grupo de recursos denominado `myResourceGroup`:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Reinicie a VM**. Essa etapa de solução de problemas pode corrigir qualquer problema subjacente experienciado pela própria VM.
   
    O exemplo a seguir reinicia a VM denominada `myVM` no grupo de recursos denominado `myResourceGroup`:
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Reimplante a VM**. Essa etapa de solução de problemas reimplanta sua VM para outro host dentro do Azure para corrigir problemas subjacentes de rede ou de plataforma.
   
    O exemplo a seguir reimplanta a VM denominada `myVM` na localização `WestUS` e no grupo de recursos denominado `myResourceGroup`:
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Verificar o roteamento**. Use a funcionalidade [Próximo salto](../../network-watcher/network-watcher-check-next-hop-portal.md) do Observador de Rede para confirmar que uma rota não está impedindo que o tráfego seja roteado de ou para uma máquina virtual. Você também pode examinar as rotas efetivas para ver todas as rotas efetivas para uma interface de rede. Para saber mais, confira [Usar regras efetivas para solucionar problemas de fluxo de tráfego de VM](../../virtual-network/diagnose-network-routing-problem.md).

7. Certifique-se de que qualquer firewall local, ou firewall em seu computador, permita o tráfego de saída TCP 3389 no Azure.

Caso ainda esteja tendo problemas de RDP, você poderá [abrir uma solicitação](https://azure.microsoft.com/support/options/) de suporte ou ler [conceitos e etapas de solução de problemas de RDP mais detalhados](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Solucionar problemas de VMs criadas com o modelo de implantação Clássico
Após cada etapa de solução de problemas, tente se reconectar à VM.

1. **Redefina a conexão RDP**. Essa etapa de solução de problemas redefine a configuração de RDP quando as Conexões Remotas estão desabilitadas ou as regras do Firewall do Windows estão bloqueando o RDP, por exemplo.
   
    No Portal do Azure, selecione sua VM. Clique no botão **...Mais** e clique em **Redefinir Acesso Remoto**:
   
    ![Redefinir a configuração de RDP no Portal do Azure](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Verifique os pontos de extremidade dos Serviços de Nuvem**. Essa etapa de solução de problemas verifica se você tem pontos de extremidade em seus Serviços de Nuvem para permitir o tráfego RDP. A porta padrão para RDP é a porta TCP 3389. É possível que uma regra para permitir o tráfego RDP não seja criada automaticamente quando você criar a VM.
   
   No Portal do Azure, selecione sua VM. Clique no botão **Pontos de extremidade** para exibir os pontos de extremidade atualmente configurados para sua VM. Verifique se existem pontos de extremidade que permitem o tráfego RDP na porta TCP 3389.
   
   O exemplo a seguir mostra os pontos de extremidade válidos que permitem o tráfego RDP:
   
   ![Verificar os pontos de extremidade dos Serviços de Nuvem no Portal do Azure](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Se você não tiver um ponto de extremidade que permita o tráfego RDP, deverá [criar um ponto de extremidade dos Serviços de Nuvem](../windows/classic/setup-endpoints.md). Permita tráfego TCP na porta privada 3389.
3. **Examine o diagnóstico de inicialização da VM**. Essa etapa de solução de problemas examina os logs do console da VM para determinar se a VM está reportando um problema. Nem todas as VMs têm diagnóstico de inicialização habilitado, portanto, essa etapa de solução de problemas pode ser opcional.
   
    Etapas de solução de problemas específicas estão além do escopo deste artigo, mas podem indicar um problema maior que está afetando a conectividade RDP. Para obter mais informações sobre como verificar os logs de console e a captura de tela de VM, consulte [Diagnósticos de Inicialização para VMs](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Verifique a Integridade do Recurso de VM**. Essa etapa de solução de problemas verifica se não existem problemas conhecidos com a plataforma Azure que possam afetar a conectividade com a VM.
   
    No Portal do Azure, selecione sua VM. Role para baixo no painel de configurações até a seção **Suporte + Solução de Problemas**, próximo à parte inferior da lista. Clique no botão **Integridade do Recurso**. Uma VM íntegra é reportada como estando **Disponível**:
   
    ![Verifique a integridade do recurso da VM no Portal do Azure](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Redefina as credenciais do usuário**. Essa etapa de solução de problemas redefinirá a senha da conta de administrador local, que você especificará quando não tiver certeza ou tiver esquecido quais são as credenciais.  Após conectar a VM, será necessário redefinir a senha para esse usuário.
   
    No Portal do Azure, selecione sua VM. Role para baixo no painel de configurações até a seção **Suporte + Solução de Problemas**, próximo à parte inferior da lista. Clique no botão **Redefinir senha**. Insira seu nome de usuário e uma nova senha. Por fim, clique no botão **Salvar**:
   
    ![Redefinir as credenciais do usuário no Portal do Azure](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Reinicie a VM**. Essa etapa de solução de problemas pode corrigir qualquer problema subjacente experienciado pela própria VM.
   
    Selecione sua VM no Portal do Azure e clique na guia **Visão Geral**. Clique no botão **Reiniciar**:
   
    ![Reiniciar a VM no Portal do Azure](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Certifique-se de que qualquer firewall local, ou firewall em seu computador, permita o tráfego de saída TCP 3389 no Azure.

Caso ainda esteja tendo problemas de RDP, você poderá [abrir uma solicitação](https://azure.microsoft.com/support/options/) de suporte ou ler [conceitos e etapas de solução de problemas de RDP mais detalhados](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Solucionar problemas de erros de RDP específicos
Você pode encontrar uma mensagem de erro específica ao tentar se conectar à sua VM por meio do RDP. Veja abaixo as mensagens de erro mais comuns:

* [A sessão remota foi desconectada porque não há Servidores de Licença da Área de Trabalho Remota disponíveis para fornecer uma licença](troubleshoot-specific-rdp-errors.md#rdplicense).
* [A Área de Trabalho Remota não consegue localizar o "nome" do computador](troubleshoot-specific-rdp-errors.md#rdpname).
* [Ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contatada](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Erro de segurança do Windows: suas credenciais não funcionaram](troubleshoot-specific-rdp-errors.md#wincred).
* [Este computador não pode se conectar ao computador remoto](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Recursos adicionais
Se nenhum desses erros ocorreu e ainda não for possível se conectar à VM por meio da Área de Trabalho Remota, leia o [guia detalhado de solução de problemas para Área de Trabalho Remota](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para obter etapas de solução de problema de acesso a aplicativos em execução, consulte [Solucionar problemas de acesso a um aplicativo em execução em uma VM do Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Se você estiver enfrentando problemas ao usar o Secure Shell (SSH) para se conectar a uma VM do Linux no Azure, consulte [Solucionar problemas de conexões SSH a uma VM do Linux no Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


