---
title: Monitorar o Azure Site Recovery | Microsoft Docs
description: Monitorar e solucionar problemas de replicação do Azure Site Recovery e operações usando o portal
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: d441284b265ab11dd5ece42ec3737e455d662435
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023560"
---
# <a name="monitor-site-recovery"></a>Monitorar Site Recovery

Neste artigo, saiba como monitorar o Azure [site Recovery](site-recovery-overview.md), usando site Recovery monitoramento embutido.  Você pode monitorar:

- A integridade e o status dos computadores replicados por Site Recovery
- Status do failover de teste de computadores.
- Problemas e erros que afetam a configuração e a replicação.
- Componentes de infraestrutura, como servidores locais.


## <a name="before-you-start"></a>Antes de começar

Talvez você queira examinar as [perguntas comuns de monitoramento](monitoring-common-questions.md) antes de começar.

## <a name="monitor-in-the-dashboard"></a>Monitorar no painel

1. No cofre, clique em **visão geral**. O painel dos serviços de recuperação consolida todas as informações de monitoramento do cofre em um único local. Há páginas para Site Recovery e o serviço de backup do Azure, e você pode alternar entre elas.

    ![Painel do Site Recovery](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. No painel, faça uma busca detalhada em áreas diferentes. 

    ![Captura de tela que mostra as áreas no painel em que você pode fazer drill down.](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. Em **itens replicados**, clique em **Exibir tudo** para ver todos os servidores no cofre.
4. Clique nos detalhes de status em cada seção para fazer drill down.
5. No **modo de exibição de infraestrutura**, classifique as informações de monitoramento pelo tipo de máquinas que você está replicando.

## <a name="monitor-replicated-items"></a>Monitorar itens replicados

Em **itens replicados**, monitore a integridade de todos os computadores no cofre que têm a replicação habilitada.

**State** | **Detalhes**
--- | ---
Healthy | A replicação está progredindo normalmente. Nenhum erro ou sintomas de aviso são detectados.
Aviso | Um ou mais sintomas de aviso que podem afetar a replicação são detectados.
Crítico | Um ou mais sintomas de erro de replicação críticos foram detectados.<br/><br/> Esses sintomas de erro geralmente são indicadores de que a replicação está paralisada ou não progrediu tão rapidamente quanto a taxa de alteração de dados.
Não aplicável | Servidores que atualmente não parecem estar replicando. Isso pode incluir computadores que tenham feito failover.

## <a name="monitor-test-failovers"></a>Monitorar failovers de teste

Em **teste de failover com êxito**, monitore o status de failover para computadores no cofre.

- É recomendável executar um failover de teste nos computadores replicados pelo menos uma vez a cada seis meses. É uma maneira de verificar se o failover está funcionando conforme o esperado, sem interromper o ambiente de produção. 
- Um failover de teste será considerado com êxito somente após o failover e a limpeza pós-failover forem concluídos com êxito.

**State** | **Detalhes**
--- | ---
Teste recomendado | Computadores que não executaram um failover de teste desde que a proteção foi habilitada.
Realizado com êxito | Computadores com um ou mais failovers de teste.
Não aplicável | Computadores que atualmente não são elegíveis para um failover de teste. Por exemplo, computadores que executaram failover, tenham replicação inicial/failover de teste/failover em andamento.

## <a name="monitor-configuration-issues"></a>Monitorar problemas de configuração

Em **problemas de configuração**, monitore quaisquer problemas que possam afetar a sua capacidade de fazer failover com êxito.

- Os problemas de configuração (exceto a disponibilidade da atualização de software) são detectados por uma operação periódica do validador que é executada a cada 12 horas, por padrão. É possível forçar a operação do validador a ser executada imediatamente, clicando no ícone de atualização próximo ao cabeçalho da seção **Problemas de configuração**.
- Clique nos links para obter mais detalhes. Para problemas que afetam computadores específicos, clique em **requer atenção** na coluna **configurações de destino** . Os detalhes incluem recomendações de correção.

**State** | **Detalhes**
--- | ---
Configurações ausentes | Uma configuração necessária está ausente, como uma rede de recuperação ou um grupo de recursos.
Recursos ausentes | Um recurso especificado não pode ser localizado ou não está disponível na assinatura. Por exemplo, o recurso foi excluído ou migrado. Os recursos monitorados incluíram o grupo de recursos de destino, subrede/VNet de destino, conta de armazenamento de destino/log, conjunto de disponibilidade de destino, endereço IP de destino.
Cota de assinatura |  O saldo de cota de recursos de assinatura disponível é comparado com o saldo necessário para fazer failover de todos os computadores no cofre.<br/><br/> Se não houver recursos suficientes, será relatado um saldo de cotas insuficiente.<br/><br/> As cotas estão monitorando a contagem de núcleos de VM, contagem de núcleos de família de VMs e contagem do adaptador de rede.
Atualizações de software | A disponibilidade de novas atualizações de software e informações sobre expiração de versões de software.


## <a name="monitor-errors"></a>Monitorar erros

Em **Resumo de erros**, monitore os sintomas de erro ativos no momento que podem afetar a replicação de servidores no cofre e monitore o número de máquinas afetadas.

- Os erros que afetam os componentes da infraestrutura local são mostrados no início da seção. Por exemplo, não receber uma pulsação do provedor de Azure Site Recovery no servidor de configuração local ou host do Hyper-V.
- Em seguida, são mostrados os sintomas de erro de replicação que afetam os servidores replicados.
- As entradas da tabela são classificadas por ordem decrescente da gravidade do erro e, em seguida, pela diminuição da ordem de contagem dos computadores afetados.
- A contagem de servidores impactados é uma maneira útil de entender se um único problema subjacente pode afetar vários computadores. Por exemplo, uma falha de rede pode afetar potencialmente todos os computadores que replicam para o Azure. 
- Vários erros de replicação podem ocorrer em um servidor único. Nesse caso, cada sintoma de erro conta esse servidor na lista de servidores afetados. Depois que o problema é corrigido, os parâmetros de replicação melhoram e o erro é apagado do computador.

## <a name="monitor-the-infrastructure"></a>Monitorar a infraestrutura.

No **modo de exibição de infraestrutura**, monitore os componentes de infraestrutura envolvidos na replicação e a integridade da conectividade entre os servidores e os serviços do Azure.

- Uma linha verde indica que a conexão está íntegra.
- Uma linha vermelha com o ícone de erro sobreposto indica a existência de um ou mais sintomas de erro que afetam a conectividade.
-  Passe o ponteiro do mouse sobre o ícone de erro para mostrar o erro e o número de entidades afetadas. Clique no ícone de uma lista filtrada de entidades afetadas.

    ![Modo de exibição de infraestrutura do Site Recovery (cofre)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Dicas para monitorar a infraestrutura

- Certifique-se de que os componentes de infraestrutura locais (servidor de configuração, servidores de processo, servidores VMM, hosts Hyper-V, máquinas VMware) estejam executando as últimas versões do Provedor do Azure Site Recovery e/ou agentes.
- Para usar todos os recursos na exibição de infraestrutura, é necessário estar executando o [pacote cumulativo de atualizações 22](https://support.microsoft.com/help/4072852) para esses componentes.
- Para usar a exibição de infraestrutura, selecione o cenário de replicação apropriado no ambiente. É possível fazer busca detalhada na exibição para obter mais detalhes. A tabela a seguir mostra quais cenários são representados.

    **Cenário** | **State**  | **Exibição disponível?**
    --- |--- | ---
    **Replicação entre sites locais** | Todos os estados | No 
    **Replicação da VM do Azure entre regiões do Azure**  | Replicação habilitada/replicação inicial em andamento | Yes
    **Replicação da VM do Azure entre regiões do Azure** | Com failover/failback | No   
    **Replicação do VMware para Azure** | Replicação habilitada/replicação inicial em andamento | Yes     
    **Replicação do VMware para Azure** | Com failover/failback | No      
    **Replicação do Hyper-V para Azure** | Com failover/failback | No

- Para ver a exibição de infraestrutura de um único computador de replicação, no menu do cofre, clique em **Itens replicados** e selecione um servidor.  




## <a name="monitor-recovery-plans"></a>Monitore planos de recuperação

Em **planos de recuperação**, monitore o número de planos, crie novos planos e modifique os existentes.  

## <a name="monitor-jobs"></a>Monitorar trabalhos

Em **trabalhos**, monitore o status das operações de site Recovery.

- A maioria das operações no Azure Site Recovery é executada de forma assíncrona, com um trabalho de acompanhamento sendo criado e usado para controlar o progresso da operação. 
- O objeto de trabalho tem todas as informações necessárias para acompanhar o estado e o andamento da operação. 

Monitore os trabalhos da seguinte forma:

1. No painel > seção **Trabalhos**, é possível ver um resumo dos trabalhos concluídos, os que estão em andamento, ou aguardando entrada, nas últimas 24 horas. Você pode clicar em qualquer estado para obter mais informações sobre os trabalhos relevantes.
2. Clique em **Exibir todos** para ver todos os trabalhos nas últimas 24 horas.

    > [!NOTE]
    > Também é possível acessar as informações do trabalho no menu do cofre > **Trabalhos do Site Recovery**. 

2. Na lista **Trabalhos do Site Recovery**, é exibida uma lista de trabalhos. No menu superior, é possível obter detalhes do erro para trabalhos específicos, filtrar a lista de trabalhos com base em critérios específicos e exportar detalhes de trabalhos selecionados para o Excel.
3. É possível detalhar um trabalho, clicando nele. 

## <a name="monitor-virtual-machines"></a>Monitorar máquinas virtuais

Em **itens replicados**, obtenha uma lista de máquinas replicadas. 
    ![Exibição da lista de itens replicados do Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Você pode exibir e filtrar informações. No menu de ação na parte superior, é possível executar ações para um determinado computador, incluindo a execução de um failover de teste ou a exibição de erros específicos.
3. Clique em **Colunas** para mostrar colunas adicionais, por exemplo, mostrar RPO, problemas de configuração de destino e erros de replicação.
4. Clique em **Filtrar** para exibir as informações com base em parâmetros específicos, como integridade de replicação ou uma política de replicação específica.
5. Clique com o botão direito do mouse em um computador para iniciar operações, como teste de failover, ou exibir detalhes de erros específicos associados ao computador.
6. Clique em um computador para obter mais detalhes. Os detalhes incluem:
   - **Informações de replicação**: status atual e integridade do computador.
   - **RPO** (objetivo de ponto de recuperação): RPO atual para a máquina virtual e a hora em que o RPO foi calculado pela última vez.
   - **Pontos de recuperação**: últimos pontos de recuperação disponíveis para o computador.
   - **Prontidão de failover**: indica se um failover de teste foi executado para o computador, a versão do agente em execução no computador (para computadores executando o serviço de Mobilidade) e quaisquer problemas de configuração.
   - **Erros**: lista de sintomas de erros de replicação observados atualmente no computador e possíveis causas/ações.
   - **Eventos**: uma lista cronológica de eventos recentes que afetam o computador. Os detalhes do erro mostram os sintomas de erro observáveis no momento, enquanto os eventos são um registro histórico dos problemas que afetaram o computador.
   - **Exibição de infraestrutura**: mostra o estado da infraestrutura para o cenário quando os computadores estão replicando para Azure.

     ![Visão geral/detalhes do item replicado do Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Assinar as notificações por email

É possível inscrever-se para receber notificações por email para esses eventos críticos:
 
- Estado crítico para computador replicado.
- Nenhuma conectividade entre os componentes de infraestrutura local e o serviço do Site Recovery. A conectividade entre o Site Recovery e os servidores locais registrados em um cofre é detectada usando um mecanismo de pulsação.
- Falhas de failover.

Assine conforme a seguir:

Na seção **monitoramento** de > do cofre, clique em **site Recovery eventos**.
1. Clique em **Notificações por email**.
1. Na **Notificação por email**, ative as notificações e especifique para quem enviar. É possível enviar para todos os administradores da assinatura receberem notificações e, opcionalmente, endereços de email específicos.

    ![Notificações por email](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre como](monitor-log-analytics.md) monitorar Site Recovery com Azure monitor.
