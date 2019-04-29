---
title: Monitorar o Azure Site Recovery | Microsoft Docs
description: Monitorar e solucionar problemas de replicação do Azure Site Recovery e operações usando o portal
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: rayne-wiselman
ms.openlocfilehash: 605421196c2ec7f62826e2f6c823d50262af8144
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61278051"
---
# <a name="monitor-site-recovery"></a>Recuperação de Site do monitor

Neste artigo, você aprenderá como usar os recursos internos de monitoramento do Azure Site Recovery para o monitoramento e a solução de problemas. 

## <a name="use-the-dashboard"></a>Usar o painel

1. No cofre, clique em **Visão Geral** para abrir o painel do Site Recovery. Há várias páginas de painel para ambos o Site Recovery e o Backup, e é possível alternar entres essas páginas.

    ![Painel do Site Recovery](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2.  O painel consolida todas as informações de monitoramento do cofre em um local único. A partir do painel, é possível fazer busca detalhada em diferentes áreas. 

    ![Painel do Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. Em **Itens replicados**, clique em **Exibir Todos** para ver todos os servidores no cofre.
4. Faça uma busca detalhada, clicando nos detalhes do status em cada seção. Na **Exibição de infraestrutura**, é possível classificar as informações de monitoramento pelo tipo de computador que está replicando.

## <a name="monitor-replicated-items"></a>Monitorar itens replicados

A seção de itens replicados mostra a integridade de todos os computadores que tenham replicação habilitada no cofre.

**State** | **Detalhes**
--- | ---
Healthy | A replicação está progredindo normalmente. Nenhum erro ou sintomas de aviso são detectados.
Aviso | Um ou mais sintomas de aviso que podem afetar a replicação são detectados.
Crítico | Um ou mais sintomas de erro de replicação críticos foram detectados.<br/><br/> Esses sintomas de erro geralmente são indicadores de que a replicação está paralisada ou não progrediu tão rapidamente quanto a taxa de alteração de dados.
Não aplicável | Servidores que atualmente não parecem estar replicando. Isso pode incluir computadores que tenham feito failover.

## <a name="monitor-test-failovers"></a>Monitorar failovers de teste

É possível visualizar o status de failover de teste para computadores no cofre.

- É recomendável executar um failover de teste nos computadores replicados pelo menos uma vez a cada seis meses. É uma maneira de verificar se o failover está funcionando conforme o esperado sem interromper o ambiente de produção. 
- Um failover de teste será considerado com êxito somente após o failover e a limpeza pós-failover forem concluídos com êxito.

**State** | **Detalhes**
--- | ---
Teste recomendado | Computadores que não executaram um failover de teste desde que a proteção foi habilitada.
Realizado com êxito | Computadores com um ou mais failovers de teste.
Não aplicável | Computadores que atualmente não são elegíveis para um failover de teste. Por exemplo, computadores que executaram failover, tenham replicação inicial/failover de teste/failover em andamento.

## <a name="monitor-configuration-issues"></a>Monitorar problemas de configuração

A seção **Problemas de configuração** mostra uma lista de problemas que podem afetar a capacidade de failover com êxito.

- Os problemas de configuração (exceto a disponibilidade da atualização de software) são detectados por uma operação periódica do validador que é executada a cada 12 horas, por padrão. É possível forçar a operação do validador a ser executada imediatamente, clicando no ícone de atualização próximo ao cabeçalho da seção **Problemas de configuração**.
- Clique nos links para obter mais detalhes. Para problemas que afetam computadores específicos, clique em**requer atenção** na coluna **Configurações de destino**. Os detalhes incluem recomendações de correção.

**State** | **Detalhes**
--- | ---
Configurações ausentes | Uma configuração necessária está ausente, como uma rede de recuperação ou um grupo de recursos.
Recursos ausentes | Um recurso especificado não pode ser localizado ou não está disponível na assinatura. Por exemplo, o recurso foi excluído ou migrado. Os recursos monitorados incluíram o grupo de recursos de destino, subrede/VNet de destino, conta de armazenamento de destino/log, conjunto de disponibilidade de destino, endereço IP de destino.
Cota de assinatura |  O saldo de cota de recursos de assinatura disponível é comparado com o saldo necessário para fazer failover de todos os computadores no cofre.<br/><br/> Se não houver recursos suficientes, será relatado um saldo de cotas insuficiente.<br/><br/> As cotas estão monitorando a contagem de núcleos de VM, contagem de núcleos de família de VMs e contagem do adaptador de rede.
Atualizações de software | A disponibilidade de novas atualizações de software e informações sobre expiração de versões de software.


## <a name="monitoring-errors"></a>Erros de monitoramento 
A seção **Resumo de erros** mostra os sintomas de erro atualmente ativos que podem afetar a replicação de servidores no cofre e o número de computadores afetados.

- No início da seção, os erros que afetam os componentes da infraestrutura local são mostrados. Por exemplo, não recebimento de uma pulsação do Provedor do Azure Site Recovery em execução no servidor de configuração local, no servidor VMM ou host do Hyper-V.
- Em seguida, são mostrados os sintomas de erro de replicação que afetam os servidores replicados.
- As entradas da tabela são classificadas por ordem decrescente da gravidade do erro e, em seguida, pela diminuição da ordem de contagem dos computadores afetados.
- A contagem de servidores afetados é uma maneira útil de reconhecer se um único problema subjacente pode estar afetando vários computadores. Por exemplo, uma falha de rede pode afetar potencialmente todos os computadores replicando no Azure. 
- Vários erros de replicação podem ocorrer em um servidor único. Nesse caso, cada sintoma de erro conta esse servidor na lista de servidores afetados. Depois que o problema é corrigido, os parâmetros de replicação melhoram e o erro é apagado do computador.

## <a name="monitor-the-infrastructure"></a>Monitorar a infraestrutura.

A **Exibição de infraestrutura** mostra os componentes de infraestrutura envolvidos na replicação e a integridade da conectividade entre os servidores e os serviços do Azure.

- Uma linha verde indica que a conexão está íntegra.
- Uma linha vermelha com o ícone de erro sobreposto indica a existência de um ou mais sintomas de erro se a conectividade de impacto.
-  Passe o ponteiro do mouse sobre o ícone de erro para mostrar o erro e o número de entidades afetadas. Clique no ícone de uma lista filtrada de entidades afetadas.

    ![Modo de exibição de infraestrutura do Site Recovery (cofre)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

## <a name="tips-for-monitoring-the-infrastructure"></a>Dicas para monitorar a infraestrutura

- Certifique-se de que os componentes de infraestrutura locais (servidor de configuração, servidores de processo, servidores VMM, hosts Hyper-V, máquinas VMware) estejam executando as últimas versões do Provedor do Azure Site Recovery e/ou agentes.
- Para usar todos os recursos na exibição de infraestrutura, é necessário estar executando o [pacote cumulativo de atualizações 22](https://support.microsoft.com/help/4072852) para esses componentes.
- Para usar a exibição de infraestrutura, selecione o cenário de replicação apropriado no ambiente. É possível fazer busca detalhada na exibição para obter mais detalhes. A tabela a seguir mostra quais cenários são representados.

    **Cenário** | **State**  | **Exibição disponível?**
    --- |--- | ---
    **Replicação entre sites locais** | Todos os estados | Não  
    **Replicação da VM do Azure entre regiões do Azure**  | Replicação habilitada/replicação inicial em andamento | Sim
    **Replicação da VM do Azure entre regiões do Azure** | Com failover/failback | Não    
    **Replicação do VMware para Azure** | Replicação habilitada/replicação inicial em andamento | Sim     
    **Replicação do VMware para Azure** | Com failover/failback | Não       
    **Replicação do Hyper-V para Azure** | Com failover/failback | Não 

- Para ver a exibição de infraestrutura de um único computador de replicação, no menu do cofre, clique em **Itens replicados** e selecione um servidor.  

### <a name="common-questions"></a>Perguntas comuns


**Por que a contagem de máquinas virtuais na exibição de infraestrutura do cofre é diferente da contagem total mostrada nos itens replicados?**

O modo de exibição de infraestrutura do cofre é com escopo por cenários de replicação. Somente computadores no cenário de replicação atualmente selecionado são incluídos na contagem da exibição. Além disso, contamos apenas as VMs configuradas para replicar no Azure. Computadores com failover ou computadores replicando de volta para um site local não são contados na exibição.

**Por que a contagem de itens replicados mostrados na gaveta Essentials é diferente da contagem total de itens replicados no painel?**

Somente computadores para os quais a replicação inicial foi concluída são incluídos na contagem mostrada na gaveta Essentials. Nos itens replicados, o total inclui todos os computadores no cofre, incluindo aqueles para os quais a replicação inicial atualmente está em andamento.


## <a name="monitor-recovery-plans"></a>Monitore planos de recuperação

Na seção **Planos de recuperação**, é possível analisar o número de planos, criar novos planos e modificar os já existentes.  

## <a name="monitor-jobs"></a>Monitorar trabalhos

A seção **Trabalhos** reflete o status do Site Recovery.

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

Além disso, você pode monitorar computadores na página de máquinas virtuais. 

1. No cofre, clique em **Itens replicados** para obter uma lista de computadores replicados.  Como alternativa, é possível acessar uma lista filtrada dos itens protegidos, clicando em qualquer um dos atalhos do escopo na página de painel.

    ![Exibição da lista de itens replicados do Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Na página **Itens replicados**, é possível exibir e filtrar informações. No menu de ação na parte superior, é possível executar ações para um determinado computador, incluindo a execução de um failover de teste ou a exibição de erros específicos.
3. Clique em **Colunas** para mostrar colunas adicionais, por exemplo, mostrar RPO, problemas de configuração de destino e erros de replicação.
4. Clique em **Filtrar** para exibir as informações com base em parâmetros específicos, como integridade de replicação ou uma política de replicação específica.
5. Clique com o botão direito do mouse em um computador para iniciar operações, como teste de failover, ou exibir detalhes de erros específicos associados ao computador.
6. Clique em um computador para obter mais detalhes. Os detalhes incluem:
   - **Informações de replicação**: Status atual e integridade do computador.
   - **RPO** (objetivo de ponto de recuperação): RPO atual para a máquina virtual e a hora em que o RPO foi computado pela última vez.
   - **Pontos de recuperação**: Pontos de recuperação mais recentes disponíveis para o computador.
   - **Prontidão de failover**: Indica se um failover de teste foi executado no computador, a versão do agente em execução no computador (para computadores executando o serviço de Mobilidade) e quaisquer problemas de configuração.
   - **Erros**: Lista de sintomas de erros de replicação observados atualmente no computador e possíveis causas/ações.
   - **Eventos**: Uma lista cronológica de eventos recentes que afetam o computador. Os detalhes do erro mostram os sintomas de erro observáveis no momento, enquanto os eventos são um registro histórico dos problemas que afetaram o computador.
   - **Exibição de infraestrutura**: Mostra o estado da infraestrutura do cenário quando os computadores estão replicando para o Azure.

     ![Visão geral/detalhes do item replicado do Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)


### <a name="common-questions"></a>Perguntas comuns

**Como o RPO é diferente do último ponto de recuperação disponível?**


- O Site Recovery usa um processo assíncrono de várias etapas para replicar computadores para Azure.
- Na penúltima etapa da replicação, as alterações recentes no computador, em conjunto com metadados, são copiadas para uma conta de armazenamento em cache/log.
- Essas alterações, junto com a marca para identificar um ponto recuperável, são gravadas na conta de armazenamento na região de destino.
-  O Site Recovery agora pode gerar um ponto recuperável para a máquina virtual.
- Neste ponto, o RPO foi encontrado para as alterações carregadas na conta de armazenamento até o momento. Em outras palavras, o RPO do computador nesse ponto é igual à quantidade de tempo decorrido do carimbo de data/hora correspondente ao ponto recuperável.
- Agora, o Site Recovery seleciona os dados carregados da conta de armazenamento e aplica-os aos discos de réplica criados para o computador.
- O Site Recovery, em seguida, gera um ponto de recuperação e disponibiliza esse ponto para recuperação no failover. Portanto, o último ponto de recuperação disponível indica o carimbo de data/hora correspondente ao último ponto de recuperação que já foi processado e aplicado aos discos de réplica.

> [!NOTE]
> Um horário de sistema incorreto no computador de origem de replicação ou em servidores de infraestrutura locais distorcerá o valor de RPO calculado. Para relatórios precisos de RPO, verifique se o relógio do sistema está correto em todos os servidores e computadores. 

## <a name="subscribe-to-email-notifications"></a>Assinar as notificações por email

É possível inscrever-se para receber notificações por email para esses eventos críticos:
 
- Estado crítico para computador replicado.
- Nenhuma conectividade entre os componentes de infraestrutura local e o serviço do Site Recovery. A conectividade entre o Site Recovery e os servidores locais registrados em um cofre é detectada usando um mecanismo de pulsação.
- Falhas de failover.

Assine conforme a seguir:

No cofre > seção **Monitoramento e Relatórios**, clique em **Eventos do Azure Site Recovery**.
1. Clique em **Notificações por email**.
1. Na **Notificação por email**, ative as notificações e especifique para quem enviar. É possível enviar para todos os administradores da assinatura receberem notificações e, opcionalmente, endereços de email específicos.

    ![Notificações por email](./media/site-recovery-monitor-and-troubleshoot/email.png)
