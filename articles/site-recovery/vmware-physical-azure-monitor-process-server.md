---
title: Monitorar o servidor de processo do Azure Site Recovery
description: Este artigo descreve como monitorar o servidor de processo do Azure Site Recovery usado para recuperação de desastres de servidor físico/VM da VMware.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 0dcee641a2de73238557a6a29173f71ee1c4312a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580559"
---
# <a name="monitor-the-process-server"></a>Monitorar o servidor de processo

Este artigo descreve como monitorar o servidor de processo do [Site Recovery](site-recovery-overview.md).

- O servidor de processo é usado quando você configura a recuperação de desastres de servidores físicos e VMs da VMware no Azure.
- Por padrão, o servidor de processo é executado no servidor de configuração. Ele é instalado por padrão quando você implanta o servidor de configuração.
- Opcionalmente, para escalar e processar números maiores de computadores replicados e volumes maiores de tráfego de replicação, você pode implantar servidores de processo adicionais de expansão.

[Saiba mais](vmware-physical-azure-config-process-server-overview.md) sobre a função e a implantação de servidores de processo.

## <a name="monitoring-overview"></a>Visão geral de monitoramento

Como o servidor de processo tem muitas funções, especialmente em cache de dados replicados, compactação e transferência para o Azure, é importante monitorar a integridade do servidor de processo continuamente.

Há várias situações que geralmente afetam o desempenho do servidor de processo. Problemas que afetam o desempenho terão um efeito em cascata na integridade da VM, podendo, ocasionalmente, colocar o servidor de processo e seus computadores replicados em um estado crítico. As situações incluem:

- Números elevados de VMs usam um servidor de processo, próximo ou acima das limitações recomendadas.
- As VMs que usam o servidor de processo têm uma taxa elevada de rotatividade.
- A taxa de transferência da rede entre VMs e o servidor de processo não é suficiente para carregar dados de replicação para o servidor de processo.
- A taxa de transferência da rede entre o servidor de processo e o Azure não é suficiente para carregar dados de replicação do servidor de processo para o Azure.

Todos esses problemas podem afetar o RPO (objetivo de ponto de recuperação) das VMs. 

**Por quê?** Porque a geração de um ponto de recuperação de uma VM exige que todos os discos na VM tenham um ponto comum. Se um disco tiver uma taxa elevada de rotatividade, a replicação for lenta ou o servidor de processo não for o ideal, a maneira como os pontos de recuperação são criados será afetada.

## <a name="monitor-proactively"></a>Monitorar proativamente

Para evitar problemas com o servidor de processo, é importante:

- Entender os requisitos específicos dos servidores de processo usando [diretrizes de capacidade e dimensionamento](site-recovery-plan-capacity-vmware.md#capacity-considerations) e verificar se os servidores de processo estão implantados e sendo executados de acordo com as recomendações.
- Monitorar alertas e solucionar problemas conforme ocorrerem, para manter a execução eficiente dos servidores de processo.


## <a name="process-server-alerts"></a>Alertas do servidor de processo

O servidor de processo gera vários alertas de integridade, resumidos na tabela a seguir.

**Tipo de alerta** | **Detalhes**
--- | ---
![Healthy][green] | O servidor de processo está conectado e íntegro.
![Aviso][yellow] | Utilização da CPU > 80% nos últimos 15 minutos.
![Aviso][yellow] | Uso de memória > 80% nos últimos 15 minutos.
![Aviso][yellow] | Espaço livre na pasta de cache < 30% nos últimos 15 minutos.
![Aviso][yellow] | O Site Recovery monitora os dados pendentes/enviados a cada cinco minutos e estima que os dados no cache do servidor de processo não podem ser carregados no Azure dentro de 30 minutos.
![Aviso][yellow] | Os serviços do servidor de processo não estão em execução nos últimos 15 minutos.
![Crítico][red] | Utilização da CPU > 95% nos últimos 15 minutos.
![Crítico][red] | Uso de memória > 95% nos últimos 15 minutos.
![Crítico][red] | Espaço livre na pasta de cache < 25% nos últimos 15 minutos.
![Crítico][red] | O Site Recovery monitora os dados pendentes/enviados a cada cinco minutos e estima que os dados no cache do servidor de processo não podem ser carregados no Azure dentro de 45 minutos.
![Crítico][red] | Não há pulsação no servidor de processo por 15 minutos.

![Legenda da tabela](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> O status geral da integridade do servidor de processo é baseado no pior alerta gerado.



## <a name="monitor-process-server-health"></a>Monitorar a integridade do servidor de processo

Você pode monitorar o estado da integridade dos seus servidores de processo da seguinte maneira: 

1. Para monitorar a integridade e o status da replicação de um computador replicado e do seu servidor de processo, no cofre > **Itens replicados**, clique no computador que você deseja monitorar.
2. Em **Integridade da replicação**, você pode monitorar o status da integridade da VM. Clique no status para fazer busca detalhada nos erros.

    ![Integridade do servidor de processo no painel da VM](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. Em **Integridade do Servidor de Processo**, você pode monitorar o status do servidor de processo. Faça uma busca nos detalhes.

    ![Detalhes do servidor de processo no painel da VM](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. A integridade também pode ser monitorada usando a representação gráfica na página da VM.
    - Um servidor de processo de expansão será realçado em laranja se houver avisos associados a ele e em vermelho se ele tiver problemas críticos. 
    - Se o servidor de processo estiver sendo executado na implantação padrão no servidor de configuração, o servidor de configuração terá o devido realce.
    - Para fazer uma busca detalhada, clique no servidor de configuração ou no servidor de processo. Observe quaisquer problemas e quaisquer recomendações de correção.

Você também pode monitorar servidores de processo no cofre, em **Infraestrutura do Site Recovery**. Em **Gerenciar sua infraestrutura do Site Recovery**, clique em **Servidores de Configuração**. Selecione o servidor de configuração associado ao servidor de processo e faça uma busca nos detalhes do servidor de processo.


## <a name="next-steps"></a>Próximas etapas

- Se você tiver problemas nos servidores de processo, siga nossas [diretrizes de solução de problemas](vmware-physical-azure-troubleshoot-process-server.md).
- Se precisar de mais ajuda, poste sua pergunta na [página de Perguntas e Respostas da Microsoft para o Azure Site Recovery](/answers/topics/azure-site-recovery.html). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
