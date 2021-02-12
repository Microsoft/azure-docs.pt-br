---
title: Tutorial – Configurar a recuperação de desastre para VMs do Linux com o Azure Site Recovery
description: Saiba como configurar a recuperação de desastre para VMs do Linux em uma região do Azure diferente usando o serviço do Azure Site Recovery.
author: rayne-wiselman
ms.service: virtual-machines-linux
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d14d276c798e40d417a8038aee5b7550e84f4114
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379719"
---
# <a name="tutorial-set-up-disaster-recovery-for-linux-virtual-machines"></a>Tutorial: configurar a recuperação de desastre para máquinas virtuais do Linux


Neste tutorial, você verá como configurar a recuperação de desastre para VMs do Azure com o Linux instalado. Neste artigo, aprenda a:

> [!div class="checklist"]
> * Habilitar a recuperação de desastre em uma VM do Linux
> * Executar uma análise de recuperação de desastre
> * Interromper a replicação da VM após a análise

Quando você habilita a replicação de uma VM, a extensão do serviço Mobilidade do Site Recovery é instalada na VM e a registra no [Azure Site Recovery](../../site-recovery/site-recovery-overview.md). Durante a replicação, as gravações de disco da VM são enviadas para uma conta de armazenamento em cache na região de origem. Os dados são enviados de lá para a região de destino e os pontos de recuperação são gerados com base nos dados.  Quando você faz o failover da VM para outra região durante a recuperação de desastre, um ponto de recuperação é usado para restaurar a VM na região de destino.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

1. Verifique se a assinatura do Azure permite criar uma VM na região de destino. Se acabou de criar sua conta gratuita do Azure, você é o administrador da assinatura e tem as permissões necessárias.
2. Se você não for o administrador da assinatura, peça para ele atribuir:
    - A função interna de Colaborador da Máquina Virtual ou permissões específicas a:
        - Criar uma VM na rede virtual selecionada.
        - Gravar em uma conta de armazenamento do Azure.
        - Gravar em um disco gerenciado do Azure.
     - A função interna Colaborador do Site Recovery para gerenciar operações do serviço no cofre. 
3. Verifique se a VM do Linux está executando um [sistema operacional compatível](../../site-recovery/azure-to-azure-support-matrix.md#linux).
4. Se as conexões de saída da VM usarem um proxy baseado em URL, verifique se ela pode acessar essas URLs. Não é possível usar um proxy autenticado.

    **Nome** | **Nuvem pública** | **Nuvem do Governamental** | **Detalhes**
    --- | --- | --- | ---
    Armazenamento | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| Grave dados da VM na conta de armazenamento em cache da região de origem. 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| Autorize e autentique para as URLs do serviço Site Recovery. 
    Replicação | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |Comunicação da VM com o serviço Site Recovery. 
    Barramento de Serviço | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | A VM grava no Site Recovery dados de monitoramento e diagnóstico. 

4. Se você usa NSGs (grupos de segurança de rede) para limitar o tráfego de rede para as VMs, crie regras de NSG que permitam conectividade de saída (HTTPS 443) para a VM usando essas marcas de serviço (grupos de endereços IP). Experimente as regras em um NSG de teste primeiro.

    **Tag** | **Permitir** 
    --- | --- 
    Tag Storage | Permite que os dados sejam gravados da VM para a conta de armazenamento em cache.
    Tag Azure AD | Permite o acesso a todos os endereços IP que correspondem ao Azure AD.
    Tag EventsHub | Permite o acesso ao monitoramento do Site Recovery.
    Tag AzureSiteRecovery | Permite o acesso ao serviço Site Recovery em qualquer região.
    GuestAndHybridManagement | Use para atualizar automaticamente o agente Mobilidade do Site Recovery em execução nas VMs habilitadas para replicação.
5. Verifique se as VMs têm os certificados raiz mais recentes. Em VMs do Linux, siga as diretrizes do distribuidor Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados na VM.

## <a name="enable-disaster-recovery"></a>Habilitar a recuperação de desastre

1. No portal do Azure, abra a página de propriedades da VM.
2. Em **Operações**, clique em **Recuperação de desastre**.
3. Em **Básico** > **Região de destino**, selecione a região em que você quer replicar a VM. As regiões de origem e destino precisam estar no mesmo locatário do Azure Active Directory.
4. Clique em **Examinar + Iniciar replicação**.

    :::image type="content" source="./media/tutorial-disaster-recovery/disaster-recovery.png" alt-text="Habilite a replicação na página &quot;Recuperação de desastre&quot; das propriedades da VM.":::

5. Em **Examinar + Iniciar replicação**, verifique as configurações:

    - **Configurações de destino**. Por padrão, Site Recovery espelha as configurações da origem para criar recursos de destino.
    - **Configurações de armazenamento – Conta de armazenamento em cache**. O Site Recovery usa uma conta de armazenamento na região de origem. As alterações na VM de origem são armazenadas em cache nessa conta, antes de serem replicadas para o local de destino.
    - **Configurações de armazenamento – Disco de réplica**. Por padrão, o Site Recovery cria discos gerenciados de réplica na região de destino que espelham os discos gerenciados da VM de origem com o mesmo tipo de armazenamento (Standard ou Premium).
    - **Configurações de replicação**. Mostra os detalhes do cofre e indica que os pontos de recuperação criados pelo Site Recovery são mantidos por 24 horas.
    - **Configurações de extensão**. Indica que o Site Recovery gerenciará as atualizações para a extensão de Serviço de Mobilidade do Site Recovery instalada nas VMs replicadas. A conta de automação do Azure indicada gerencia o processo de atualização.

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="Página que mostra o resumo das configurações de destino e replicação.":::

2. Selecione **Iniciar replicação**. A implantação é iniciada e o Site Recovery começa a criar recursos de destino. É possível monitorar o progresso da replicação nas notificações.

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="Notificação de progresso de replicação.":::

## <a name="check-vm-status"></a>Verificar o status da VM

Depois que o trabalho de replicação for concluído, você poderá verificar o status de replicação da VM.

1. Abra a página de propriedades da VM.
2. Em **Operações**, clique em **Recuperação de desastre**.
3. Expanda a seção **Essenciais** para examinar os padrões sobre o cofre, a política de replicação e as configurações de destino.
4. Em **Integridade e status**, veja informações sobre o estado de replicação da VM, a versão do agente, preparação de failover e os pontos de recuperação mais recentes. 

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="Exibição &quot;Essenciais&quot; para recuperação de desastre de VM.":::

5. Na **exibição Infraestrutura**, confira uma visão geral das VMs de origem e destino, dos discos gerenciados e da conta de armazenamento em cache.

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="mapa visual de infraestrutura para recuperação de desastres de VM.":::


## <a name="run-a-drill"></a>Faça uma análise

Faça uma análise para garantir o funcionamento da recuperação de desastre conforme o esperado. Quando você executa um failover de teste, uma cópia da VM é criada, que não afeta a replicação em andamento nem o ambiente de produção.

1. Na página de recuperação de desastre da VM, selecione **Failover de teste**.
2. Em **Failover de teste**, deixe a configuração **Mais recente processado (baixo RPO)** para o ponto de recuperação.

   Essa opção fornece o RPO (objetivo de ponto de recuperação) mais baixo e geralmente inicia a VM mais rapidamente na região de destino. Primeiro, essa opção processa todos os dados enviados ao serviço do Site Recovery para criar um ponto de recuperação para cada VM antes do failover. Esse ponto de recuperação tem todos os dados replicados para o Site Recovery no momento do failover.

3. Selecione a rede virtual em que as VMs estarão localizadas após o failover. 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="Página para definir as opções de failover de teste.":::

4. O processo de failover de teste é iniciado. É possível monitorar o progresso nas notificações.

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="Notificações de failover de teste."::: 
    
   Depois que o failover de teste for concluído, a VM estará no estado de *Failover de teste de limpeza pendente* na página **Essenciais**. 
  
## <a name="clean-up-resources"></a>Limpar recursos

A VM é automaticamente limpa pelo Site Recovery após a análise. 
 
1. Para iniciar a limpeza automática, selecione **Limpar failover de teste**.

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="Inicie a limpeza na página Essenciais."::: 

6. Em **Limpeza do failover de teste**, digite as observações que você quer registrar para o failover e selecione **O teste foi concluído. Exclua a máquina virtual do failover de teste**. Depois, selecione **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="Página para registrar observações e excluir a VM de teste."::: 

7. O processo de exclusão é iniciado. É possível monitorar o progresso nas notificações.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="Notificações para monitorar a VM de teste de exclusão."::: 


### <a name="stop-replicating-the-vm"></a>Parar a replicação da VM

Depois de concluir uma análise de recuperação de desastre, é recomendável continuar tentando um failover completo. Se você não quiser fazer um failover completo, poderá desabilitar a replicação. Isso faz o seguinte:

- Remove a VM da lista do Site Recovery de máquinas replicadas.
- Interrompe o faturamento do Site Recovery para a VM.
- Limpa automaticamente as configurações de replicação da origem.

Interrompa a replicação da seguinte maneira:

1. Na página de recuperação de desastre da VM, selecione **Desabilitar Replicação**.
2. Em **Desabilitar Replicação**, selecione os motivos pelos quais você quer desabilitar a replicação. Depois, selecione **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="Página para desabilitar a replicação e fornecer um motivo."::: 


A extensão do Site Recovery instalada na VM durante a replicação não é removida automaticamente. Se você desabilitar a replicação para a VM e não quiser replicá-la novamente mais tarde, poderá remover a extensão do Site Recovery manualmente, da seguinte maneira: 

1. Acesse VM > **Configurações** > **Extensões**.
2. Na página **Extensões**, selecione cada entrada *Microsoft.Azure.RecoveryServices* para o Linux.
3. Na página de propriedades da extensão, selecione **Desinstalar**.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou a recuperação de desastre para uma VM do Azure e executou uma análise de recuperação de desastre. Agora, você pode executar um failover completo para a VM.

> [!div class="nextstepaction"]
> [Fazer failover de uma VM para outra região](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
