---
title: "Acesso just in time à máquina virtual na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento orienta você sobre como o acesso just in time à VM, na Central de Segurança do Azure, ajuda você a controlar o acesso às suas máquinas virtuais do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2017
ms.author: terrylan
ms.openlocfilehash: c715afe55a3aedd5c4f826bc34c3c56e167d2f82
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-virtual-machine-access-using-just-in-time-preview"></a>Gerenciar o acesso à máquina virtual usando o just in time (versão prévia)

O acesso just in time à VM (máquina virtual) pode ser usado para bloquear o tráfego de entrada às suas VMs do Azure, reduzindo a exposição aos ataques, fornecendo acesso fácil para conectar às VMs quando necessário.

> [!NOTE]
> O recurso just in time está em versão prévia e está disponível na camada Standard da Central de Segurança.  Confira os [Preços](security-center-pricing.md) para saber mais sobre os tipos de preço da Central de Segurança.
>
>

## <a name="attack-scenario"></a>Cenário de ataque

Ataques de força bruta geralmente se destinam às portas de gerenciamento como um meio para obter acesso a uma VM. Se for bem-sucedido, um invasor poderá assumir o controle sobre a VM e estabelecer uma base em seu ambiente.

Uma maneira de reduzir a exposição a um ataque de força bruta é limitar a quantidade de tempo que uma porta fica aberta. As portas de gerenciamento não precisam ficar abertas o tempo todo. Elas só precisam ser abertas enquanto você estiver conectado à VM, por exemplo, para realizar tarefas de manutenção ou gerenciamento. Quando o just in time está habilitado, a Central de Segurança usa regras de [NSG](../virtual-network/virtual-networks-nsg.md) (Grupo de Segurança de Rede), que restringem o acesso às portas de gerenciamento para que elas não se tornem alvo de invasores.

![Cenário just in time][1]

## <a name="how-does-just-in-time-access-work"></a>Como funciona o acesso just in time?

Quando o just in time está habilitado, a Central de Segurança bloqueia o tráfego de entrada às suas VMs do Azure, criando uma regra de NSG. Você seleciona as portas na VM para as quais o tráfego de entrada será bloqueado. Essas portas são controladas pela solução just in time.

Quando um usuário solicita acesso a uma VM, a Central de Segurança verifica se o usuário tem permissões de [RBAC (Controle de acesso baseado em função)](../active-directory/role-based-access-control-configure.md) que fornecem acesso de gravação para a VM. Se o usuário tem permissões de gravação, a solicitação é aprovada e a Central de Segurança configura automaticamente os NSGs (Grupos de Segurança de Rede) para permitir o tráfego de entrada às portas de gerenciamento pelo período de tempo que você especificou. Depois que o tempo expirar, a Central de Segurança restaura os NSGs aos seus estados anteriores.

> [!NOTE]
> O acesso just in time à VM da Central de Segurança atualmente dá suporte somente a VMs implantadas por meio do Azure Resource Manager. Para saber mais sobre os modelos de implantação clássica e do Resource Manager, consulte [Azure Resource Manager vs implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

## <a name="using-just-in-time-access"></a>Usando o acesso just in time

O bloco **Acesso à VM just in time** na **Central de Segurança** mostra o número de VMs configuradas para o acesso just in time e o número de solicitações de acesso aprovadas na última semana.

![Bloco acesso à VM just in time][2]

Selecione o bloco **Acesso à VM just in time** e **Acesso à VM just in time** é aberto.

![Bloco acesso à VM just in time][10]

**Acesso à VM just in time** fornece informações sobre o estado das suas VMs:

- **Configurada** – VMs que foram configuradas para dar suporte ao acesso à VM just in time. Os dados apresentados são da última semana e incluem, para cada VM, o número de solicitações aprovadas, a data e a hora do último acesso e o último usuário.
- **Recomendada** – VMs que podem dar suporte ao acesso à VM just in time, mas não foram configurados para isso. É recomendável que você habilite o controle de acesso à VM just in time para essas VMs. Consulte [Configurando uma política de acesso just in time](#configuring-a-just-in-time-access-policy).
- **Nenhuma recomendação** – as razões que podem fazer com que uma VM não seja recomendada são:
  - NSG ausente – a solução just in time exige que um NSG esteja em vigor.
  - VM Clássica – o acesso à VM just in time da Central de Segurança atualmente dá suporte apenas às VMs implantadas por meio do Azure Resource Manager. Não há suporte para uma implantação clássica na solução just in time.
  - Outros – uma VM estará nessa categoria se a solução just in time estiver desativada na política de segurança da assinatura ou do grupo de recursos ou se estiver faltando um IP público da VM e ela não tiver um NSG em vigor.

## <a name="configuring-a-just-in-time-access-policy"></a>Configurando uma política de acesso just in time

Para selecionar as VMs que você deseja habilitar:

1. Em **Acesso à VM just in time**, selecione a guia **Recomendada**.

  ![Habilitar o acesso just in time][3]

2. Em **MÁQUINA VIRTUAL**, selecione as VMs que deseja habilitar. Isso coloca uma marca de seleção ao lado de uma VM.
3. Selecione **Habilitar JIT nas VMs**.
4. Selecione **Salvar**.

### <a name="default-ports"></a>Portas padrão

Você pode ver as portas padrão nas quais a Central de Segurança recomenda habilitar o just in time.

1. Em **Acesso à VM just in time**, selecione a guia **Recomendada**.

  ![Exibir portas padrão][6]

2. Em **VMs**, selecione uma VM. Isso coloca uma marca de seleção ao lado da VM e abre **Configuração de acesso à VM JIT**. Esta folha exibe as portas padrão.

### <a name="add-ports"></a>Adicionar portas

Em **Configuração de acesso à VM JIT**, você também pode adicionar e configurar uma nova porta na qual deseja habilitar a solução just in time.

1. Em **Configuração de acesso à VM JIT**, selecione **Adicionar**. Isso abre **Adicionar configuração de porta**.

  ![Configuração de portas][7]

2. Em **Adicionar configuração de porta**, você identifica a porta, o tipo de protocolo, os IPs de origem com permissão e o tempo máximo de solicitação.

  Os IPs de origem permitidos são os intervalos de IP que têm permissão para obter acesso após uma solicitação aprovada.

  Tempo máximo de solicitação é a janela de tempo máxima que uma porta específica pode ser aberta.

3. Selecione **OK**.

## <a name="requesting-access-to-a-vm"></a>Solicitando acesso a uma VM

Para solicitar acesso a uma VM:

1. Em **Acesso à VM just in time**, selecione a guia **Configurada**.
2. Em **VMs**, selecione as VMs que você deseja habilitar o acesso. Isso coloca uma marca de seleção ao lado de uma VM.
3. Selecione **Solicitar acesso**. Isso abre **Solicitar acesso**.

  ![Solicitar acesso a uma VM][4]

4. Em **Solicitar acesso**, você configura, para cada VM, as portas a serem abertas juntamente com o IP de origem para o qual a porta está aberta e a janela de tempo para a qual a porta está aberta. Você pode solicitar acesso somente para as portas que estão configuradas na política just in time. Cada porta tem um tempo máximo permitido derivado da política just in time.
5. Selecione **Abrir portas**.

## <a name="editing-a-just-in-time-access-policy"></a>Edição de uma política de acesso just in time

Você pode alterar a política just in time existente de uma VM adicionando e configurando uma nova porta a ser aberta para essa VM ou alterando qualquer outro parâmetro relacionado a uma porta já protegida.

Para editar a política just in time existente de uma VM, usa-se a guia **Configurada**:

1. Em **VMs**, selecione uma VM para a qual adicionar uma porta, clicando nos três pontos dentro da linha dessa VM. Isso abre um menu.
2. Selecione **Editar** no menu. Isso abre **Configuração de acesso à VM JIT**.

  ![Editar política][8]

3. Em **Configuração de acesso à VM JIT**, você pode editar as configurações existentes de uma porta já protegida clicando em sua porta ou você pode selecionar **Adicionar**. Isso abre **Adicionar configuração de porta**.

  ![Adicionar uma porta][7]

4. Em **Adicionar configuração de porta**, identifique a porta, o tipo de protocolo, os IPs de origem com permissão e o tempo máximo de solicitação.
5. Selecione **OK**.
6. Selecione **Salvar**.

## <a name="auditing-just-in-time-access-activity"></a>Auditoria em atividades de acesso just in time

Você pode obter informações sobre as atividades de VM usando a pesquisa de logs. Para exibir os logs:

1. Em **Acesso à VM just in time**, selecione a guia **Configurada**.
2. Em **VMs**, selecione uma VM para exibir as respectivas informações, clicando nos três pontos dentro da linha dessa VM. Isso abre um menu.
3. Selecione **Log de atividades** no menu. Isso abre **Log de atividades**.

  ![Selecionar o log de atividades][9]

  **Log de atividades** fornece uma exibição filtrada das operações anteriores dessa VM junto com a hora, a data e a assinatura.

  ![Exibir log de atividades][5]

Você pode baixar as informações de log selecionando **Clique aqui para baixar todos os itens como CSV**.

Modifique os filtros e selecione **Aplicar** para criar uma pesquisa e um log.

## <a name="using-just-in-time-vm-access-via-powershell"></a>Usando o acesso à VM just in time por meio do PowerShell

Para usar a solução just in time por meio do PowerShell, verifique se você tem a versão [mais recente](/powershell/azure/install-azurerm-ps) do Azure PowerShell.
Depois disso, você precisa instalar a Central de Segurança do Azure [mais recente](https://aka.ms/asc-psgallery) da galeria do PowerShell.

### <a name="configuring-a-just-in-time-policy-for-a-vm"></a>Configurando uma política just in time para uma VM

Para configurar uma política just in time em uma VM específica, você precisa executar este comando em sua sessão do PowerShell: Set-ASCJITAccessPolicy.
Siga a documentação do cmdlet para obter mais informações.

### <a name="requesting-access-to-a-vm"></a>Solicitando acesso a uma VM

Para acessar uma VM específica que esteja protegida com a solução just in time, você precisa executar este comando em sua sessão do PowerShell: Invoke-ASCJITAccess.
Siga a documentação do cmdlet para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
Nesse artigo você aprendeu como o acesso just in time à VM na Central de Segurança ajuda você a controlar o acesso às suas máquinas virtuais do Azure.

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança](security-center-policies.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciar recomendações de segurança](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger seus recursos do Azure.
- [Monitoramento da integridade da segurança](security-center-monitoring.md) – saiba como monitorar a integridade dos seus recursos do Azure.
- [Gerenciar e responder aos alertas de segurança](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e responder aos alertas de segurança.
- [Monitorar as soluções de parceiros](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes da Central de Segurança](security-center-faq.md) - encontre as perguntas frequentes sobre como usar o serviço.
- [Blog de Segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) : encontre postagens no blog sobre conformidade e segurança do Azure.


<!--Image references-->
[1]: ./media/security-center-just-in-time/just-in-time-scenario.png
[2]: ./media/security-center-just-in-time/just-in-time.png
[10]: ./media/security-center-just-in-time/just-in-time-access.png
[3]: ./media/security-center-just-in-time/enable-just-in-time-access.png
[4]: ./media/security-center-just-in-time/request-access-to-a-vm.png
[5]: ./media/security-center-just-in-time/activity-log.png
[6]: ./media/security-center-just-in-time/default-ports.png
[7]: ./media/security-center-just-in-time/add-a-port.png
[8]: ./media/security-center-just-in-time/edit-policy.png
[9]: ./media/security-center-just-in-time/select-activity-log.png
