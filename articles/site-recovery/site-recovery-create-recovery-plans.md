---
title: "Criar planos de recuperação para failover e recuperação no Azure Site Recovery | Microsoft Docs"
description: "Descreve como criar e personalizar planos de recuperação no Azure Site Recovery para failover e recuperação de VMs e servidores físicos"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/25/2017
ms.author: raynew
ms.openlocfilehash: 81d8a6e3015ddc4241cce8e888d51d6e2b2cb173
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-recovery-plans"></a>Criar planos de recuperação


Este artigo fornece informações sobre como criar e personalizar planos de recuperação em [Azure Site Recovery](site-recovery-overview.md).

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

 Criar planos de recuperação têm a seguinte função:

* Defina grupos de computadores que fazem failover juntos e então inicialize-os juntos.
* Modele as dependências entre os computadores juntando-os em um grupo de planos de recuperação. Por exemplo, para failover e exibir um aplicativo específico, você agrupar todas as VMs para o aplicativo no mesmo grupo de plano de recuperação.
* Executar um failover. Você pode executar um failover de teste planejado ou não planejado em um plano de recuperação.


## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. Clique em **Planos de Recuperação** > **Criar Plano de Recuperação**.
   Especifique um nome para o plano de recuperação, bem como uma origem e um destino. O local de origem deve ter máquinas virtuais habilitadas para failover e recuperação.

    - Para a replicação de VMM para VMM, selecione **Tipo de Origem** > **VMM** e os servidores VMM de origem e de destino. Clique em **Hyper-V** para ver as nuvens que estão protegidas.
    - Para o VMM no Azure, selecione **tipo de fonte de** > **VMM**.  Selecione o servidor do VMM de origem e **Azure** como o destino.
    - Para replicação do Hyper-V no Azure (sem VMM), selecione **tipo de fonte de** > **site Hyper-V**. Selecione o site como a origem e **Azure** como o destino.
    - Para uma VM VMware ou um servidor local físico para o Azure, selecione um servidor de configuração como a origem e **Azure** como o destino.
    - Para um plano de recuperação do Azure para o Azure, selecione uma região do Azure como a origem e uma região secundária do Azure como o destino. Regiões do Azure secundário são apenas aqueles que máquinas virtuais são protegidas.
2. Em **Selecionar máquinas virtuais**, selecione as máquinas virtuais (ou o grupo de replicação) que deseja adicionar ao grupo padrão (Grupo 1) no plano de recuperação.

## <a name="customize-and-extend-recovery-plans"></a>Personalizar e estender os planos de recuperação

Você pode personalizar e estender os planos de recuperação:

- **Adicionar novos grupos**— adicionar grupos de plano de recuperação adicionais (até sete) ao grupo padrão e, em seguida, adicionar mais computadores ou grupos de replicação a esses grupos de plano de recuperação. Os grupos são numerados na ordem em que você os adiciona. Uma máquina virtual ou um grupo de replicação pode ser incluído apenas em um grupo de planos de recuperação.
- **Adicionar uma ação manual**: você pode adicionar ações manuais que são executadas antes ou depois de um grupo de planos de recuperação. Quando o plano de recuperação é executado, ele parar no ponto em que você inseriu a ação manual. Uma caixa de diálogo solicita que você especificar que a ação manual foi concluída.
- **Adicionar um script** — você pode adicionar scripts executados antes ou depois de um grupo de planos de recuperação. Quando você adiciona um script, ele adiciona um novo conjunto de ações para o grupo. Por exemplo, um conjunto de pré-etapas do Grupo 1 será criado com o nome: Grupo 1: pré-etapas. Todas as pré-etapas serão listadas dentro desse conjunto. Só será possível adicionar um script no site primário se você tiver um servidor VMM implantado.
- **Adicionar runbooks do Azure**— você pode estender os planos de recuperação com runbooks do Azure. Por exemplo, para automatizar tarefas ou criar recuperação de etapa única. [Saiba mais](site-recovery-runbook-automation.md).

## <a name="add-scripts"></a>Adicionar scripts

Você pode usar scripts do PowerShell em seus planos de recuperação.

 - Certifique-se de que os scripts usem blocos try-catch para que as exceções sejam tratadas normalmente.
    - Se houver uma exceção no script, ele será interrompido e a tarefa será mostrada como com falha.
    - Se ocorrer um erro, qualquer parte restante do script não é executado.
    - Se ocorrer um erro quando você executa um failover não planejado, o plano de recuperação continuará.
    - Se ocorrer um erro quando você executa um failover planejado, o plano de recuperação será interrompido. Você precisa corrigir o script, verificar se ele funciona conforme esperado e executar novamente o plano de recuperação.
- O comando Write-Host não funciona em um script de plano de recuperação e o script falhará. Para criar, crie um script de proxy que por sua vez executa o script principal. Certifique-se de que toda a saída é direcionada usando o >> comando.
  * O script expira se não retornar em até 600 segundos.
  * Se nada for escrito em STDERR, o script será classificado como com falha. Essa informação é exibida nos detalhes de execução do script.

Se você estiver usando a VMM em sua implantação:

* Os scripts em um plano de recuperação são executados no contexto da conta de Serviço VMM. Verifique se que essa conta tem permissões de leitura para o compartilhamento remoto no qual o script está localizado. Teste o script seja executado no nível de privilégio da conta de serviço do VMM.
* Os cmdlets do VMM são entregues em um módulo do Windows PowerShell. O módulo é instalado quando você instala o console do VMM. Pode ser carregado em seu script, usando o seguinte comando no script:
   - Import-Module-Name virtualmachinemanager. [Saiba mais](https://technet.microsoft.com/library/hh875013.aspx).
* Verifique se você possui pelo menos um servidor de biblioteca na sua implantação do VMM. Por padrão, o caminho de compartilhamento da biblioteca de um servidor VMM pode ser encontrado localmente no servidor VMM com o nome de pasta MSCVMMLibrary.
    * Se o caminho de compartilhamento da biblioteca for remoto (ou local, mas não compartilhado com MSCVMMLibrary), configure o compartilhamento da seguinte forma (usando \\libserver2.contoso.com\share\ como um exemplo):
      * Abra o Editor do Registro e navegue até **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.
      * Edite o valor de **ScriptLibraryPath** e coloque o valor como \\libserver2.contoso.com\share\. Especifique o FQDN completo. Forneça permissões para o local de compartilhamento. Observe que este é o nó raiz do compartilhamento. **Para verificar isso, abra a biblioteca no nó raiz no VMM. O caminho aberto será a raiz, aquela que você precisará usar na variável**.
      * Certifique-se de que você teste o script com uma conta de usuário que tenha as mesmas permissões que a conta de serviço do VMM. Isso verifica que autônomos testados os scripts são executados da mesma maneira como serão nos planos de recuperação. No servidor VMM, defina a política de execução a ser ignorada da seguinte maneira:
        * Abra o console do **Windows PowerShell de 64 bits** usando privilégios elevados.
        * Digite: **Set-executionpolicy bypass**. [Saiba mais](https://technet.microsoft.com/library/ee176961.aspx).

> [!IMPORTANT]
> Você deve definir a política de execução para Ignorar apenas o PowerShell de 64 bits. Se você tiver definido isso para o PowerShell de 32 bits, os scripts não serão executados.

## <a name="add-a-script-or-manual-action-to-a-plan"></a>Adicionar uma ação de script ou manual de um plano

Você pode adicionar um script para o grupo padrão do plano de recuperação depois de VMs ou grupos de replicação são adicionados a ele e criar o plano.

1. Abra o plano de recuperação.
2. Clique em um item de **etapa** lista e, em seguida, clique em **Script** ou **ação Manual**.
3. Especifique se deseja adicionar o script ou a ação antes ou depois do item selecionado. Use os botões **Mover para Cima** e **Mover para Baixo** para mover o script para cima ou para baixo.
4. Se você adicionar um script do VMM, selecione **Failover no script do VMM**. Em **caminho do Script de**, digite o caminho relativo para o compartilhamento. No exemplo do VMM abaixo, você pode especificar o caminho: **\rpscripts\rpscript.ps1**.
5. Se você adicionar um runboook da automação do Azure, especifique a Conta de Automação do Azure na qual o runbook está localizado e selecione o script de runbook do Azure apropriado.
6. Faça um failover do plano de recuperação para garantir que o script funciona conforme esperado.


### <a name="add-a-vmm-script"></a>Criar um script do VMM

Se você tiver um site de origem do VMM, será possível criar um script no servidor VMM e incluí-lo no plano de recuperação.

1. Crie uma nova pasta no compartilhamento de biblioteca. Por exemplo, \<VMMServerName > \MSSCVMMLibrary\RPScripts. Coloque-a nos servidores VMM de origem e de destino.
2. Crie o script (por exemplo, RPScript) e verifique se ele funciona conforme esperado.
3. Coloque o script no local \<NomeDoServidorVMM>\MSSCVMMLibrary nos servidores VMM de origem e de destino.


## <a name="next-steps"></a>Próximas etapas

[Saiba mais](site-recovery-failover.md) sobre a execução de failovers.
