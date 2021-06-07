---
title: Glossário do monitor da área de trabalho virtual do Windows – Azure
description: Um glossário de termos e conceitos relacionados a Azure Monitor para área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 38f34324a0cda518645f8b9231e08bafbd75cca0
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709524"
---
# <a name="azure-monitor-for-windows-virtual-desktop-glossary"></a>Glossário do Azure Monitor para área de trabalho virtual do Windows

Este artigo lista e descreve brevemente os principais termos e conceitos relacionados ao Azure Monitor para área de trabalho virtual do Windows (versão prévia).

## <a name="alerts"></a>Alertas

Todos os alertas do Active Azure Monitor que você configurou na assinatura e classificados como [severidade 0](#severity-0-alerts) serão exibidos na página Visão geral. Para saber como configurar alertas, consulte [responder a eventos com Azure monitor alertas](../azure-monitor/alerts/tutorial-response.md).

## <a name="available-sessions"></a>Sessões disponíveis

Sessões disponíveis mostra o número de sessões disponíveis no pool de hosts. O serviço calcula esse número multiplicando o número de VMs (máquinas virtuais) pelo número máximo de sessões permitidas por máquina virtual e subtraindo o total de sessões.

## <a name="connection-success"></a>Êxito na conexão

Este item mostra a integridade da conexão. "Êxito na conexão" significa que a conexão pode alcançar o host, conforme confirmado pela pilha nessa máquina virtual. Uma conexão com falha significa que a conexão não pôde alcançar o host.

## <a name="daily-active-users-dau"></a>Usuários ativos diariamente (DAU)

O número total de usuários que iniciaram uma sessão nas últimas 24 horas.

## <a name="daily-alerts"></a>Alertas diários

O número total de alertas disparados a cada dia.

## <a name="daily-connections-and-reconnections"></a>Conexões e reconexões diárias

O número total de conexões e reconexões iniciadas ou concluídas nas últimas 24 horas.

## <a name="daily-connected-hours"></a>Horas conectadas diárias

O número total de horas gastas conectadas a uma sessão entre usuários nas últimas 24 horas.

## <a name="diagnostics-and-errors"></a>Diagnósticos e erros

Quando um erro ou alerta aparece em Azure Monitor para área de trabalho virtual do Windows, ele é categorizado por três coisas:

- Tipo de atividade: essa categoria é como o erro é categorizado pelo diagnóstico de área de trabalho virtual do Windows. As categorias são atividades de gerenciamento, feeds, conexões, registros de host, erros e pontos de verificação. Saiba mais sobre essas categorias em [usar log Analytics para o recurso de diagnóstico](diagnostics-log-analytics.md).

- Tipo: essa categoria mostra o local do erro. 

     - Erros marcados como "serviço" ou "erro = verdadeiro" ocorreram no serviço de área de trabalho virtual do Windows.
     - Erros marcados como "implantação" ou marcados como "ServiceName = FALSE" aconteceram fora do serviço de área de trabalho virtual do Windows.
     - Para saber mais sobre a marca de erro, consulte [cenários de erro comuns](diagnostics-role-service.md#common-error-scenarios).

- Fonte: essa categoria fornece uma descrição mais específica de onde o erro ocorreu.

     - Diagnóstico: a função de serviço responsável por monitorar e relatar a atividade do serviço para permitir que os usuários observem e diagnostiquem problemas de implantação.

     - RDBroker: a função de serviço responsável por orquestrar atividades de implantação, manter o estado dos objetos, validar a autenticação e muito mais.

     - RDGateway: a função de serviço responsável por lidar com a conectividade de rede entre os usuários finais e as máquinas virtuais.

     - RDStack: um componente de software que é instalado em suas VMs para permitir que eles se comuniquem com o serviço de área de trabalho virtual do Windows.

     - Cliente: software em execução no computador do usuário final que fornece a interface para o serviço de área de trabalho virtual do Windows. Ele exibe a lista de recursos publicados e hospeda a conexão Área de Trabalho Remota depois de fazer uma seleção.

Cada problema ou erro de diagnóstico inclui uma mensagem que explica o que deu errado. Para saber mais sobre como solucionar erros, consulte [identificar e diagnosticar problemas da área de trabalho virtual do Windows](diagnostics-role-service.md).

## <a name="input-delay"></a>Atraso de entrada

"Atraso de entrada" em Azure Monitor para área de trabalho virtual do Windows significa o atraso de entrada por contador de desempenho de processo para cada sessão. Na página desempenho do host em [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), esse contador de desempenho é configurado para enviar um relatório ao serviço uma vez a cada 30 segundos. Esses intervalos de 30 segundos são chamados de "amostras" e o pior caso nessa janela. Os valores medianos e P95 refletem a mediana e a 95 º percentil em todas as amostras.

Em **atraso de entrada por host**, você pode selecionar uma linha de host de sessão para filtrar todos os outros visuais na página para esse host. Você também pode selecionar um nome de processo para filtrar o atraso de entrada mediana no gráfico de tempo.

Colocamos os atrasos nas seguintes categorias:

- Bom: abaixo de 150 milissegundos.
- Aceitável: 150-500 milissegundos.
- Ruim: 500-2000 milissegundos (abaixo de 2 segundos).
- Inadequado: mais de 2.000 milissegundos (2 segundos e acima).

Para saber mais sobre como o contador de atraso de entrada funciona, consulte [contadores de desempenho de atraso de entrada do usuário](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

##  <a name="monthly-active-users-mau"></a>MAUs (usuários ativos mensais)

O número total de usuários que iniciaram uma sessão nos últimos 28 dias. Se você armazenar dados por 30 dias ou menos, poderá ver valores de conexão e MAU menores que o esperado durante os períodos em que há menos de 28 dias de dados disponíveis.

## <a name="performance-counters"></a>Contadores de desempenho

Os contadores de desempenho mostram o desempenho dos componentes de hardware, sistemas operacionais e aplicativos.

A tabela a seguir lista os contadores de desempenho recomendados e os intervalos de tempo que o Azure Monitor usa para a área de trabalho virtual do Windows:

|Nome do contador de desempenho|Intervalo de tempo|
|---|---|
|Disco lógico (C:) \\ comprimento médio da fila de disco|30 segundos|
|Disco lógico (C:) \\ média de disco s/transferência|60 segundos|
|Disco lógico (C:) \\ comprimento da fila de disco atual|30 segundos|
|Memória ( \* ) \\ MBytes disponíveis|30 segundos|
|Memória ( \* ) \\ falhas de página/s|30 segundos|
|Memória ( \* ) \\ páginas/s|30 segundos|
|Memória ( \* ) \\ % bytes confirmados em uso|30 segundos|
|PhysicalDisk ( \* ) \\ comprimento médio da fila de disco|30 segundos|
|PhysicalDisk ( \* ) \\ média de disco s/leitura|30 segundos|
|PhysicalDisk ( \* ) \\ média de disco s/transferência|30 segundos|
|PhysicalDisk ( \* ) \\ média de disco s/gravação|30 segundos|
|Informações do processador (_Total) \\ % tempo do processador|30 segundos|
|\*Sessões ativas dos serviços de terminal () \\|60 segundos|
|\*Sessões inativas dos serviços de terminal () \\|60 segundos|
|Total de sessões dos serviços de terminal ( \* ) \\|60 segundos|
|\*Atraso de entrada do usuário por processo ( \* ) \\ atraso máximo de entrada|30 segundos|
|\*Atraso de entrada do usuário por sessão ( \* ) \\ atraso máximo de entrada|30 segundos|
|\* \\ RTT de TCP atual () de rede do RemoteFX|30 segundos|
|Rede do RemoteFX ( \* ) \\ largura de banda UDP atual|30 segundos|

Para saber mais sobre como ler contadores de desempenho, confira [Configurando contadores de desempenho](../azure-monitor/agents/data-sources-performance-counters.md).

Para saber mais sobre contadores de desempenho de atraso de entrada, confira [contadores de desempenho de atraso de entrada do usuário](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="potential-connectivity-issues"></a>Possíveis problemas de conectividade

Possíveis problemas de conectividade mostram os hosts, usuários, recursos publicados e clientes com uma alta taxa de falha de conexão. Depois de escolher um filtro "relatório por", você pode avaliar a gravidade do problema verificando os valores nessas colunas:

- Tentativas (número de tentativas de conexão)
- Recursos (número de aplicativos publicados ou áreas de trabalho)
- Hosts (número de VMs)
- Clientes

Por exemplo, se você selecionar o filtro **por usuário** , poderá verificar para ver as tentativas de conexão de cada usuário na coluna **tentativas** .

Se você observar que um problema de conexão abrange vários hosts, usuários, recursos ou clientes, é provável que o problema afete todo o sistema. Se não for, é um problema menor que a prioridade mais baixa.

Você também pode selecionar entradas para exibir informações adicionais. Você pode exibir quais hosts, recursos e versões de cliente estavam envolvidos no problema. A exibição também mostrará todos os erros relatados durante as tentativas de conexão.

## <a name="round-trip-time-rtt"></a>RTT (tempo de ida e volta)

O RTT (tempo de ida e volta) é uma estimativa do tempo de ida e volta da conexão entre o local do usuário final e a região do Azure do host da sessão. Para ver quais locais têm a melhor latência, procure o local desejado na [ferramenta estimador de experiência de área de trabalho virtual do Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/).

## <a name="session-history"></a>Histórico de sessão

O item **sessões** mostra o status de todas as sessões, conectadas e desconectadas. As **sessões ociosas** mostram apenas as sessões desconectadas.

## <a name="severity-0-alerts"></a>Alertas de severidade 0

Os itens mais urgentes dos quais você precisa se encarregar imediatamente. Se você não resolver esses problemas, eles podem fazer com que sua implantação de área de trabalho virtual do Windows pare de funcionar.

## <a name="time-to-connect"></a>Tempo para se conectar

O tempo de conexão é o tempo entre o momento em que um usuário inicia sua sessão e quando ele é contado como sendo conectado ao serviço. O estabelecimento de novas conexões tende a demorar mais do que restabelecer as conexões existentes.

## <a name="user-report"></a>Relatório do usuário

A página relatório do usuário permite exibir informações de diagnóstico e de histórico de conexão de um usuário específico. Cada relatório de usuário mostra padrões de uso, comentários do usuário e quaisquer erros que os usuários encontraram durante suas sessões. A maioria dos problemas menores pode ser resolvida com comentários do usuário. Se você precisar se aprofundar, também poderá filtrar informações sobre uma ID de conexão específica ou um período de tempo.

## <a name="users-per-core"></a>Usuários por núcleo

Este é o número de usuários em cada núcleo de máquina virtual. Acompanhar o número máximo de usuários por núcleo ao longo do tempo pode ajudá-lo a identificar se o ambiente é executado consistentemente em um número alto, baixo ou flutuante de usuários por núcleo. Saber quantos usuários estão ativos ajudará você a criar recursos e dimensionar o ambiente com eficiência.

## <a name="windows-event-logs"></a>Logs de Eventos do Windows

Os logs de eventos do Windows são fontes de dados coletadas por agentes de Log Analytics em máquinas virtuais do Windows. Você pode coletar eventos de logs padrão, como sistema e aplicativo, bem como logs personalizados criados por aplicativos que você precisa monitorar.

A tabela a seguir lista os logs de eventos do Windows necessários para Azure Monitor para a área de trabalho virtual do Windows:

|Nome do evento|Tipo de evento|
|---|---|
|Aplicativo|Erro e aviso|
|Microsoft-Windows-TerminalServices-RemoteConnectionManager/admin|Erro, aviso e informações|
|Microsoft-Windows-TerminalServices-LocalSessionManager/operacional|Erro, aviso e informações|
|Sistema|Erro e aviso|
| Microsoft-FSLogix-apps/operacional|Erro, aviso e informações|
|Microsoft-FSLogix-apps/admin|Erro, aviso e informações|

Para saber mais sobre os logs de eventos do Windows, consulte [Propriedades de registros de eventos do Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o Azure Monitor para área de trabalho virtual do Windows, confira estes artigos:

- [Usar Azure Monitor para área de trabalho virtual do Windows para monitorar sua implantação](azure-monitor.md)
- [Azure Monitor para solução de problemas de área de trabalho virtual do Windows](troubleshoot-azure-monitor.md)

Você também pode configurar o Azure Advisor para ajudá-lo a descobrir como resolver ou impedir problemas comuns. Saiba mais em [usar o Azure Advisor com a área de trabalho virtual do Windows](azure-advisor.md).

Se precisar de ajuda ou tiver dúvidas, Confira nossos recursos da Comunidade:

- Faça perguntas ou faça sugestões para a Comunidade na [área de trabalho virtual do Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
   
- Para saber como deixar comentários, consulte [visão geral da solução de problemas, comentários e suporte para área de trabalho virtual do Windows](troubleshoot-set-up-overview.md#report-issues).

- Você também pode deixar comentários para a área de trabalho virtual do Windows no [Hub de comentários da área de trabalho virtual do Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)
