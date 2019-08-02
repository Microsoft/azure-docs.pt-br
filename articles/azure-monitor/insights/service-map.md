---
title: Usando a solução Mapa do Serviço no Azure | Microsoft Docs
description: O Mapa do Serviço é uma solução no Azure que descobre automaticamente os componentes do aplicativo nos sistemas Windows e Linux, e mapeia a comunicação entre os serviços. Este artigo fornece detalhes sobre a implantação do Mapa do Serviço em seu ambiente e sobre como usá-lo em diversos cenários.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2019
ms.author: magoedte
ms.openlocfilehash: 1f06345995e30f4d7f165230f4292c560c89e2e8
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68489764"
---
# <a name="using-service-map-solution-in-azure"></a>Usando a solução Mapa do Serviço no Azure
O Mapa do Serviço detecta automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Com o Mapa do Serviço é possível exibir seus servidores da maneira desejada: como sistemas interconectados que fornecem serviços críticos. O Mapa do Serviço mostra conexões entre servidores, processos, latência de conexão de entrada e saída e portas em qualquer arquitetura conectada a TCP, sem a necessidade de configuração diferente da instalação de um agente.

Este artigo descreve os detalhes de integração e como usar o Mapa do Serviço. Para obter informações sobre como configurar os pré-requisitos para essa solução, consulte [habilitar a Azure monitor para VMs visão geral](vminsights-enable-overview.md#prerequisites). Para resumir, você precisa do seguinte:

* Um espaço de trabalho Log Analytics para habilitar essa solução.

* O agente de Log Analytics instalado no computador Windows ou no servidor Linux configurado para relatar o mesmo espaço de trabalho com o qual você habilitou a solução.

* O Dependency Agent instalado no computador Windows ou no servidor Linux.

>[!NOTE]
>Se o Mapa do Serviço já foi implantado, também será possível exibir os mapas no Azure Monitor para VMs, que inclui recursos adicionais para monitorar a integridade e o desempenho da VM. Para saber mais, consulte [visão geral do Azure Monitor para VMs](../../azure-monitor/insights/vminsights-overview.md). Para saber mais sobre as diferenças entre a solução de Mapa do Serviço e o recurso de mapa de Azure Monitor para VMs, consulte as [perguntas frequentes](vminsights-faq.md#how-is-azure-monitor-for-vms-map-feature-different-from-service-map)a seguir.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-service-map"></a>Habilitar Mapa do Serviço

1. Habilite a solução de Mapa do Serviço do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceMapOMS?tab=Overview) ou usando o processo descrito em [Adicionar soluções de monitoramento do Galeria de soluções](solutions.md).
1. [Instale o Dependency Agent no Windows](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-windows) ou [Instale o Dependency Agent no Linux](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-linux) em cada computador em que você deseja obter dados. O Agente de Dependência pode monitorar conexões a vizinhos imediatos, portanto, talvez não seja necessário ter um agente em cada computador.

Acesse o Mapa do Serviço no portal do Azure do seu espaço de trabalho do Log Analytics e selecione a opção **Soluções** no painel esquerdo.<br><br> ![Selecione a opção Soluções no workspace](./media/service-map/select-solution-from-workspace.png).<br> Na lista de soluções, selecione **ServiceMap(workspaceName)** e na página de visão geral da solução Mapa do Serviço, clique no bloco de resumo do Mapa do Serviço.<br><br> ![Bloco de resumo do Mapa do Serviço](./media/service-map/service-map-summary-tile.png).

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Casos de uso: fazer com que seus processos de TI reconheçam a dependência

### <a name="discovery"></a>Descoberta

O Mapa do Serviço compila automaticamente um mapa de referência comum de dependências em seus servidores, processos e serviços de terceiros. Ele descobre e mapeia todas as dependências TCP, identificando conexões inesperadas, sistemas remotos de terceiros dos quais você depende e dependências para áreas escuras tradicionais da rede, como o Active Directory. O Mapa do Serviço descobre conexões de rede com falha que seus sistemas gerenciados estão tentando realizar, ajudando você a identificar potenciais configurações incorretas do servidor, interrupções de serviço e problemas de rede.

### <a name="incident-management"></a>Gerenciamento de incidentes

O Mapa do Serviço ajuda a eliminar as suposições de isolamento do problema, mostrando a você como os sistemas estão conectados e afetando uns aos outros. Além de identificar conexões com falha, ele ajuda a identificar balanceadores de carga configurados incorretamente, carga excessiva ou inesperada em serviços críticos e clientes invasores, como computadores de desenvolvedores conversando com sistemas de produção. Ao utilizar fluxos de trabalho integrados com o Controle de Alterações, também é possível verificar se um evento de alteração em um computador de back-end ou serviço explica a causa raiz de um incidente.

### <a name="migration-assurance"></a>Garantia de migração

Ao utilizar o Mapa do Serviço você pode, efetivamente, planejar, acelerar e validar as migrações do Azure, permitindo assegurar que nada seja deixado para trás e que interrupções inesperadas não ocorram. Você pode descobrir todos os sistemas interdependentes que precisam migrar juntos, avaliar a capacidade e a configuração do sistema e identificar se um sistema em execução ainda está atendendo aos usuários ou é candidato para encerramento em vez de migração. Após a conclusão do movimento será possível inspecionar a identidade e carregamento do cliente para verificar se os sistemas de teste e os clientes estão se conectando. Se o seu planejamento de sub-rede e definições de firewall tiverem problemas, as conexões com falha em mapas do Mapa do Serviço apontarão para os sistemas que precisam de conectividade.

### <a name="business-continuity"></a>Continuidade dos negócios

Se você estiver usando o Azure Site Recovery e precisar de ajuda para definir a sequência de recuperação para seu ambiente de aplicativo, o Mapa do Serviço poderá mostrar automaticamente como os sistemas dependem uns dos outros para assegurar que seu plano de recuperação seja confiável. Ao escolher um grupo ou servidor crítico e exibir seus clientes, será possível identificar quais são os sistemas front-end a serem recuperados depois que o servidor estiver restaurado e disponível. Por outro lado, ao analisar as dependências de back-end de servidores críticos, você poderá identificar os sistemas a serem recuperados antes que seus sistemas de foco sejam restaurados.

### <a name="patch-management"></a>Gerenciamento de patch

O Mapa do Serviço aprimora seu uso da Avaliação de atualização do sistema, mostrando que outras equipes e servidores dependem de seu serviço, para que você possa notificá-los com antecedência antes de desativar seus sistemas para aplicação de patch. O Mapa do Serviço também aprimora o gerenciamento de patch mostrando se os seus serviços estão disponíveis e conectados corretamente após terem sido corrigidos e reiniciados.

## <a name="mapping-overview"></a>Visão geral do mapeamento

Os agentes do Mapa do Serviço coletam informações sobre todos os processos conectados a TCP no servidor em que estão instalados e detalhes sobre as conexões de entrada e saída de cada processo.

Na lista no painel esquerdo, você pode selecionar computadores ou grupos que possuem agentes do Mapa do Serviço para visualizar suas dependências em um intervalo de tempo especificado. Os mapas de dependência de computador concentram-se em um computador específico e mostram todos os computadores que são clientes TCP diretos ou servidores daquele computador.  Os mapas do Grupo de Computadores mostram os conjuntos de servidores e suas dependências.

![Visão geral do Mapa do Serviço](media/service-map/service-map-overview.png)

Computadores podem ser expandidos no mapa para mostrar os grupos de processos em execução e os processos com conexões de rede ativas durante o intervalo de tempo selecionado. Quando um computador remoto com um agente do Mapa do Serviço é expandido para mostrar os detalhes do processo, somente os processos comunicando-se com o computador serão mostrados. A contagem de computadores front-end sem agente conectando-se com o computador de foco é indicada no lado esquerdo dos processos aos quais se conectam. Se o computador de foco estiver fazendo uma conexão com um computador back-end que não possui nenhum agente, o servidor back-end será incluído em um Grupo de Portas de Servidor, juntamente com outras conexões com o mesmo número da porta.

Por padrão, os mapas do Mapa do Serviço mostram os últimos 30 minutos de informações de dependência. Ao usar os controles de tempo na parte superior esquerda, você poderá consultar mapas para intervalos de tempo históricos de até uma hora para mostrar como as dependências foram analisadas no passado (por exemplo, durante um incidente ou antes de ocorrer uma alteração). Os dados do Mapa do Serviço são armazenados por 30 dias em workspaces pagos, e por sete dias em workspaces gratuitos.

## <a name="status-badges-and-border-coloring"></a>Notificações de status e a cor de borda

Na parte inferior de cada servidor no mapa pode haver uma lista de notificações de status que passam informações sobre o status do servidor. As notificações indicam que há algumas informações relevantes para o servidor de uma das integrações de solução. Ao clicar em uma notificação, você será direcionado diretamente para os detalhes do status no painel direito. As notificações de status atualmente disponíveis incluem Alertas, Central de Serviços, Alterações, Segurança e Atualizações.

Dependendo da gravidade das notificações de status, as bordas do nó do computador poderão ser coloridas em vermelho (crítico), amarelo (aviso) ou azul (informativo). A cor representa o status mais grave de qualquer uma das notificações de status. Uma borda cinza indica um nó que não possui indicadores de status.

![Notificações de status](media/service-map/status-badges.png)

## <a name="process-groups"></a>Grupos de processos

Grupos de processos combinam os processos que estão associados com um produto ou serviço comum em um grupo de processos.  Quando um nó de computador é expandido, ele exibe processos autônomos junto com grupos de processos.  Se todas as conexões de entrada e de saída para um processo dentro de um grupo de processos tiverem falhado, a conexão será mostrada como com falha para todo o grupo de processos.

## <a name="machine-groups"></a>Grupos de Computadores

Grupos de Computadores permitem que você veja mapas centrados em torno de um conjunto de servidores e não apenas um, de modo que você possa ver todos os membros de um aplicativo multicamada ou cluster de servidores em um mapa.

Os usuários selecionam quais servidores pertencem em um grupo e escolhem um nome para o grupo.  É possível escolher exibir o grupo com todos os seus processos e conexões ou visualizá-lo com apenas os processos e conexões que se relacionam diretamente com os outros membros do grupo.

![Grupo de Computadores](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Criar um Grupo de Computadores

Para criar um grupo, selecione o computador ou os computadores que você deseja na lista Computadores e clique em **Adicionar ao grupo**.

![Criar Grupo](media/service-map/machine-groups-create.png)

Nesse ponto, é possível escolher **Criar novo** e atribuir ao grupo um nome.

![Nome do Grupo](media/service-map/machine-groups-name.png)

>[!NOTE]
>Os grupos de computadores são limitados a 10 servidores.

### <a name="viewing-a-group"></a>Exibir um Grupo

Depois de criar alguns grupos, você poderá exibi-los clicando na guia Grupos.

![Guia Grupos](media/service-map/machine-groups-tab.png)

Em seguida, selecione o nome do Grupo para exibir o mapa para esse Grupo de Computadores.
![Grupo de Computadores](media/service-map/machine-group.png) Os computadores que pertencem ao grupo são contornados em branco no mapa.

Ao expandir o Grupo, serão listados os computadores que constituem o Grupo de Computadores.

![Computadores do Grupo de Computadores](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrar por processos

É possível alternar a exibição do mapa entre mostrar todos os processos e conexões no Grupo e apenas aqueles que se relacionam diretamente com o Grupo de Computadores.  A exibição padrão é mostrar todos os processos.  Você pode alterar a exibição, clicando no ícone do filtro acima do mapa.

![Grupo do Filtro](media/service-map/machine-groups-filter.png)

Quando **Todos os processos** estiver selecionado, o mapa incluirá todos os processos e conexões em cada um dos computadores no Grupo.

![Todos os processos do Grupo de Computadores](media/service-map/machine-groups-all.png)

Se você alterar a exibição para mostrar apenas os **processos conectados por grupo** , o mapa será inoperante apenas para os processos e conexões diretamente conectados a outros computadores do grupo, criando uma exibição simplificada.

![Processos filtrados do Grupo de Computadores](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Adicionar computadores a um grupo

Para adicionar computadores a um grupo existente, marque as caixas ao lado dos computadores que deseja e clique em **Adicionar ao grupo**.  Em seguida, escolha o grupo para o qual deseja adicionar os computadores.
 
### <a name="removing-machines-from-a-group"></a>Remover computadores de um grupo

Na Lista de Grupos, expanda o nome do grupo para listar os computadores no Grupo de Computadores.  Em seguida, clique no menu de reticências próximo ao computador que deseja remover e escolha **Remover**.

![Remova o computador do grupo](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Remover ou renomear um grupo

Clique no menu de reticências próximo ao nome do grupo na Lista de Grupos.

![Menu do Grupo de Computadores](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Ícones de função

Alguns processos possuem funções específicas em computadores: servidores Web, servidores de aplicativos, banco de dados, etc. O Mapa do Serviço adiciona ao processo e às caixas do computador ícones de função para ajudar a identificar rapidamente a função de um processo ou servidor.

| Ícone de função | Descrição |
|:--|:--|
| ![Servidor Web](media/service-map/role-web-server.png) | Servidor Web |
| ![Servidor de aplicativos](media/service-map/role-application-server.png) | Servidor de aplicativos |
| ![Servidor de banco de dados](media/service-map/role-database.png) | Servidor de banco de dados |
| ![Servidor LDAP](media/service-map/role-ldap.png) | Servidor LDAP |
| ![Servidor SMB](media/service-map/role-smb.png) | Servidor SMB |

![Ícones de função](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Conexões com falha

As conexões com falha são mostradas nos mapas do Mapa do Serviço para processos e computadores, com uma linha vermelha tracejada indicando que um sistema cliente está falhando em alcançar um processo ou uma porta. Conexões com falha são reportadas de qualquer sistema com um agente do Mapa do Serviço implantado, se esse sistema estiver tentando a conexão com falha. O Mapa do Serviço mede esse processo, observando os soquetes TCP que falham ao estabelecer uma conexão. Isso pode ocorrer devido a um firewall, uma configuração incorreta no cliente ou servidor ou um serviço remoto não disponível.

![Conexões com falha](media/service-map/failed-connections.png)

Entender as conexões com falha pode ajudar com a solução de problemas, validação da migração, análise de segurança e noções básicas sobre arquitetura em geral. Às vezes, as conexões com falhas são inofensivas, mas frequentemente apontam diretamente para um problema, como um ambiente de failover tornando-se inacessível repentinamente ou, duas camadas de aplicativos sem comunicação após uma migração na nuvem.

## <a name="client-groups"></a>Grupos de Clientes

Os Grupos de Clientes são caixas no mapa que representam computadores cliente que não possuem Agentes de Dependência. Um Grupo de Clientes único representa os clientes para um computador ou processo individual.

![Grupos de Clientes](media/service-map/client-groups.png)

Para ver os endereços IP dos servidores em um Grupo de Clientes, selecione o grupo. O conteúdo do grupo está listado no painel **Propriedades do Grupo de Clientes**.

![Propriedades do Grupo de Clientes](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Grupos de Portas do Servidor

Os Grupos de Portas do Servidor são caixas que representam portas de servidor em servidores que não possuem Agentes de Dependência. A caixa contém a porta do servidor e uma contagem do número de servidores com conexões para essa porta. Expanda a caixa para ver conexões e servidores individuais. Se houver apenas um servidor na caixa, o nome ou endereço IP será listado.

![Grupos de Portas do Servidor](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Menu de contexto

Ao clicar nas reticências (...), no canto superior direito de qualquer servidor, o menu de contexto desse servidor será exibido.

![Conexões com falha](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Carregar mapa do servidor

Ao clicar em **Carregar Mapa do Servidor**, você será direcionado para um novo mapa com o servidor selecionado como um novo computador de foco.

### <a name="show-self-links"></a>Mostrar self-links

Ao clicar em **Mostrar Self-Links** redefine o nó de servidor, incluindo quaisquer self-links, que são conexões TCP que iniciam e finalizam em processos dentro do servidor. Se os self-links forem mostrados, o comando de menu irá alterar para **Ocultar Self-Links**, de modo que você possa desativá-los.

## <a name="computer-summary"></a>Resumo do computador

O painel **Resumo do Computador** inclui uma visão geral do sistema operacional de um servidor, contagens de dependência e dados de outras soluções. Esses dados incluem métricas de desempenho, tíquetes de central serviços, controle de alterações, segurança e atualizações.

![Painel Resumo do Computador](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Propriedades do computador e do processo

Ao navegar no mapa do Mapa do Serviço, é possível selecionar computadores e processos para obter um contexto adicional sobre suas propriedades. As máquinas fornecem informações sobre o nome DNS, endereços IPv4, capacidade de CPU e memória, tipo de VM, sistema operacional e versão, horário da última reinicialização e as IDs de seus agentes OMS e Mapa do Serviço.

![Painel Propriedades do Computador](media/service-map/machine-properties.png)

É possível coletar detalhes do processo dos metadados do sistema operacional sobre processos em execução, incluindo o nome do processo, a descrição do processo, nome de usuário e domínio (no Windows), nome da empresa, nome do produto, a versão do produto, o diretório de trabalho, a linha de comando e a hora de início do processo.

![Painel Propriedades do Processo](media/service-map/process-properties.png)

O painel **Resumo do Processo** fornece informações adicionais sobre a conectividade do processo, incluindo suas portas associadas, conexões de entrada e saída e conexões com falha.

![Painel Resumo do Processo](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Integração de alertas

O Mapa do Serviço integra-se aos Alertas do Microsoft Azure para mostrar os alertas acionados para o servidor selecionado no intervalo de tempo selecionado. Se houver alertas atuais, o servidor exibirá um ícone e o painel **Alertas do Computador** listará os alertas.

![Painel Alertas do Computador](media/service-map/machine-alerts.png)

Para habilitar o Mapa do Serviço para exibir alertas relevantes, crie uma regra de alerta que dispara para um computador específico. Para criar alertas apropriadas:
- Inclua uma cláusula para agrupar por computador (por exemplo, **intervalo de 1 minuto por Computador**).
- Escolha o alerta com base na medida métrica.

## <a name="log-events-integration"></a>Integração de eventos de log

O Mapa do Serviço integra-se à Pesquisa de Logs para mostrar uma contagem de todos os eventos de log disponíveis para o servidor selecionado no intervalo de tempo selecionado. Você pode clicar em qualquer linha na lista de contagens de eventos para pular para a Pesquisa de Logs e ver os eventos de log individuais.

![Painel Eventos de Log do Computador](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Integração da Central de Serviços

A integração do Mapa do Serviço com o Conector de Gerenciamento de Serviços de TI é automática quando as duas soluções estão habilitadas e configuradas no espaço de trabalho do Log Analytics. A integração no Mapa do Serviço é rotulada "Central de Serviços." Para obter mais informações, consulte [Gerenciar itens de trabalho de ITSM de forma centralizada usando o Conector de Gerenciamento de Serviço de TI](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

O painel **Central de Serviços do Computador** lista todos os eventos de Gerenciamento de Serviço de TI para o servidor selecionado no intervalo de tempo selecionado. Se houver itens atuais, o servidor exibirá um ícone e o painel da Central de Serviços do Computador irá listá-los.

![Painel Central de Serviços do Computador](media/service-map/service-desk.png)

Para abrir o item na sua solução ITSM conectada, clique em **Exibir Item de Trabalho**.

Para exibir os detalhes do item na Pesquisa de Logs, clique em **Mostrar na Pesquisa de Logs**.
As métricas de conexão são gravadas em duas novas tabelas no Log Analytics 

## <a name="change-tracking-integration"></a>Integração de Controle de Alterações

A integração do Mapa do Serviço com o Controle de Alterações é automática quando as duas soluções estão habilitadas e configuradas em seu espaço de trabalho do Log Analytics.

O painel **Controle de Alterações do Computador** lista todas as alterações, as mais recentes primeiro, juntamente com um link para fazer busca detalhada da Pesquisa de Logs para obter detalhes adicionais.

![Painel Controle de Alterações do Computador](media/service-map/change-tracking.png)

A imagem a seguir é uma exibição detalhada de um evento ConfigurationChange que poderá ser exibido após selecionar **Mostrar no Log Analytics**.

![Evento ConfigurationChange](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Integração de desempenho

O painel **Desempenho do Computador** exibe métricas de desempenho padrão para o servidor selecionado. As métricas incluem utilização de CPU, utilização de memória, bytes de rede enviados e recebidos, e uma lista dos principais processos por bytes de rede enviados e recebidos.

![Painel Desempenho do Computador](media/service-map/machine-performance.png)

Para ver os dados de desempenho, talvez seja necessário [habilitar os contadores de desempenho apropriados do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  Os contadores que você desejará habilitar:

Windows:
- Processador(*)\\% Tempo do Processador
- Memória\\% Bytes Confirmados em Uso
- Adaptador de Rede(*)\\Bytes Enviados/s
- Adaptador de Rede(*)\\Bytes Recebidos/s

Linux:
- Processador(*)\\% Tempo do Processador
- Memória(*)\\% Memória Utilizada
- Adaptador de Rede(*)\\Bytes Enviados/s
- Adaptador de Rede(*)\\Bytes Recebidos/s

Para obter os dados de desempenho da rede, você deve habilitar também a solução Wire Data 2.0 no workspace.
 
## <a name="security-integration"></a>Integração de segurança

A integração do Mapa do Serviço com Segurança e Auditoria é automática quando as duas soluções estão habilitadas e configuradas em seu espaço de trabalho do Log Analytics.

O painel **Segurança do Computador** mostra dados da solução de Segurança e Auditoria para o servidor selecionado. O painel lista um resumo de quaisquer problemas de segurança pendentes para o servidor durante o intervalo de tempo selecionado. Ao clicar em qualquer um dos problemas de segurança, a busca detalhada será realizada em uma Pesquisa de Logs para obter mais detalhes sobre esses problemas.

![Painel Segurança do Computador](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Integração de atualizações

A integração do Mapa do Serviço com o Gerenciamento de Atualizações é automática quando as duas soluções estão habilitadas e configuradas em seu espaço de trabalho do Log Analytics.

O painel **Atualizações do Computador** exibe dados da solução de Gerenciamento de Atualizações para o servidor selecionado. O painel lista um resumo de quaisquer atualizações ausentes para o servidor durante o intervalo de tempo selecionado.

![Painel Controle de Alterações do Computador](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Registros do Log Analytics

Dados de inventário do processo e do computador do Mapa do Serviço estão disponíveis para [pesquisa](../../azure-monitor/log-query/log-query-overview.md) no Log Analytics. Você pode aplicar esses dados a cenários que incluem planejamento de migração, análise de capacidade, descoberta e solução de problemas de desempenho sob demanda.

Um registro é gerado por hora para cada computador e processo exclusivo, além dos registros que são gerados quando um processo ou computador inicia ou é integrado ao Mapa do Serviço. Esses registros têm as propriedades descritas nas tabelas a seguir. Os campos e valores nos eventos ServiceMapComputer_CL mapeiam para campos do recurso do Computador na API do ServiceMap do Azure Resource Manager. Os campos e valores nos eventos ServiceMapProcess_CL mapeiam para os campos do recurso do Processo na API do ServiceMap do Azure Resource Manager. O campo ResourceName_s corresponde ao campo de nome no recurso do Gerenciador de Recursos correspondente. 

>[!NOTE]
>À medida que os recursos do Mapa do Serviço crescem, esses campos estarão sujeitos a alterações.

Há propriedades geradas internamente que você pode usar para identificar computadores e processos exclusivos:

- Computador: use *ResourceId* ou *ResourceName_s* para identificar exclusivamente um computador em um espaço de trabalho do Log Analytics.
- Processo: use *ResourceId* para identificar exclusivamente um processo em um espaço de trabalho do Log Analytics. O *ResourceName_s* é exclusivo no contexto do computador no qual o processo está em execução (MachineResourceName_s) 

Como vários registros podem existir para um processo e computador específicos em um intervalo de tempo específico, as consultas podem retornar mais de um registro para o mesmo computador ou processo. Para incluir somente o registro mais recente, adicione "| dedup ResourceId" à consulta.

### <a name="connections"></a>Conexões

As métricas de conexão são gravadas em uma nova tabela no Log Analytics: VMConnection. Essa tabela fornece informações sobre as conexões de um computador (entrada e saída). As Métricas de Conexão também são expostas com APIs que fornecem meios para obter uma métrica específica durante um intervalo de tempo.  As conexões TCP resultantes de "*aceitan*-do em um soquete de escuta são de entrada, ao passo que as criadas *conectan*-do em determinada porta e IP são de saída. A direção de uma conexão é representada pela propriedade Direction, o que pode ser definida para **entrada** ou **saída**. 

Os registros nessas tabelas são gerados a partir dos dados relatados pelo agente de Dependência. Cada registro representa uma observação durante o intervalo de tempo de um minuto. A propriedade TimeGenerated indica o início do intervalo de tempo. Cada registro contém informações para identificar a respectiva entidade, ou seja, conexão ou porta, bem como as métricas associadas àquela entidade. Atualmente, apenas as atividades de rede que usam TCP via IPv4 são relatadas.

Para gerenciar o custo e a complexidade, os registros de conexão não representam as conexões de rede física individuais. Várias conexões de rede física são agrupadas em uma conexão lógica, o que é refletido na respectiva tabela.  Ou seja, os registros na tabela *VMConnection* representam um agrupamento lógico, não as conexões físicas individuais sendo observadas. As conexões de rede física que compartilham o mesmo valor para os atributos a seguir durante o intervalo especificado de um minuto são agregadas em um único registro lógico em *VMConnection*. 

| Propriedade | Descrição |
|:--|:--|
| `Direction` |Direção da conexão, o valor é *entrada* ou *saída* |
| `Machine` |O FQDN do computador |
| `Process` |Identidade do processo ou grupos de processos, iniciando/aceitando a conexão |
| `SourceIp` |Endereço IP da origem |
| `DestinationIp` |Endereço IP do destino |
| `DestinationPort` |Número da porta de destino |
| `Protocol` |Protocolo usado para a conexão.  O valor é *tcp*. |

Para levar em conta o impacto do agrupamento, são fornecidas informações sobre o número de conexões físicas agrupadas nas seguintes propriedades do registro:

| Propriedade | Descrição |
|:--|:--|
| `LinksEstablished` |O número de conexões de rede física que foram estabelecidas durante o intervalo de tempo de geração de relatórios |
| `LinksTerminated` |O número de conexões de rede física que foram terminadas durante o intervalo de tempo de geração de relatórios |
| `LinksFailed` |O número de conexões de rede física que falharam durante o intervalo de tempo de geração de relatórios. Essa informação está disponível atualmente apenas para as conexões de saída. |
| `LinksLive` |O número de conexões de rede física que estavam abertas no final do intervalo de tempo de geração de relatórios|

#### <a name="metrics"></a>metrics

Além das métricas de contagem de conexões, as informações sobre o volume de dados enviados e recebidos em determinada conexão lógica ou porta de rede também estão incluídas nas seguintes propriedades do registro:

| Propriedade | Descrição |
|:--|:--|
| `BytesSent` |Número total de bytes enviados durante o intervalo de tempo de geração de relatórios |
| `BytesReceived` |Número total de bytes recebidos durante o intervalo de tempo de geração de relatórios |
| `Responses` |O número de respostas observadas durante o intervalo de tempo de geração de relatórios. 
| `ResponseTimeMax` |O maior tempo de resposta (milissegundos) observado durante o intervalo de tempo de geração de relatórios.  Se não houve valor, a propriedade ficará em branco.|
| `ResponseTimeMin` |O menor tempo de resposta (milissegundos) observado durante o intervalo de tempo de geração de relatórios.  Se não houve valor, a propriedade ficará em branco.|
| `ResponseTimeSum` |A soma de todos os tempos de resposta (milissegundos) observados durante o intervalo de tempo de geração de relatórios.  Se não houver valor, a propriedade ficará em branco|

O terceiro tipo de dados relatado é o tempo de resposta: quanto tempo quem chama fica aguardando uma solicitação enviada em uma conexão para ser processada e respondida pelo ponto de extremidade remoto. O tempo de resposta relatado é uma estimativa do tempo real de resposta do protocolo do aplicativo subjacente. Ele é calculado usando uma heurística baseada na observação do fluxo de dados entre a origem e o destino final de uma conexão de rede física. Conceitualmente, é a diferença entre a hora em que o último byte de uma solicitação deixa o emissor e a hora em que o último byte da resposta volta para ele. Esses dois carimbos de data/hora são usados para delinear os eventos de solicitação e resposta em determinada conexão física. A diferença entre eles representa o tempo de resposta de uma única solicitação. 

Na primeira versão do recurso, nosso algoritmo é uma aproximação que pode funcionar com vários graus de sucesso, dependendo do protocolo do aplicativo real usado em determinada conexão de rede. Por exemplo, a abordagem atual funciona bem para os protocolos baseados em solicitação-resposta, como o HTTP(S), mas não funciona com protocolos baseados em fila de mensagens ou unidirecionais.

Aqui estão alguns pontos importantes a considerar:

1. Se um processo aceita conexões no mesmo endereço IP, mas em várias interfaces de rede, é relatado um registro separado para cada interface. 
2. Os registros com IP curinga não conterão nenhuma atividade. Eles são incluídos para representar o fato de que uma porta no computador está aberta para o tráfego de entrada.
3. Para reduzir o nível de detalhes e o volume de dados, os registros com IP curinga serão omitidos quando houver um registro correspondente (para o mesmo processo, porta e protocolo) com um endereço IP específico. Quando um registro IP curinga for omitido, a propriedade de registro IsWildcardBind com o endereço IP específico será definida para "True" para indicar que a porta está exposta em cada interface do computador que gera os relatórios.
4. As portas associadas somente a uma interface específica tem IsWildcardBind definida para "False".

#### <a name="naming-and-classification"></a>Nomenclatura e Classificação

Para sua conveniência, o endereço IP da extremidade remota de uma conexão é incluído na propriedade RemoteIp. No caso das conexões de entrada, RemoteIp é igual a SourceIp; já para conexões de saída, é igual a DestinationIp. A propriedade RemoteDnsCanonicalNames representa os nomes DNS aceitos relatados pelo computador para RemoteIp. As propriedades RemoteDnsQuestions e RemoteClassification são reservadas para uso futuro. 

#### <a name="geolocation"></a>Geolocalização

*VMConnection* também inclui informações de localização geográfica para a extremidade remota de cada registro de conexão nas seguintes propriedades do registro: 

| Propriedade | Descrição |
|:--|:--|
| `RemoteCountry` |O nome do país/região que hospeda o RemoteIp.  Por exemplo: *Estados Unidos* |
| `RemoteLatitude` |A latitude da localização geográfica.  Por exemplo: *47,68* |
| `RemoteLongitude` |A longitude da localização geográfica.  Por exemplo: *-122,12* |

#### <a name="malicious-ip"></a>IP malicioso

Todas as propriedades RemoteIp na tabela *VMConnection* são verificadas em um conjunto de IPs com atividades maliciosas conhecidas. Se RemoteIp for identificado como malicioso, as propriedades a seguir serão preenchidas (elas ficam em branco quando o IP não é considerado malicioso) nas seguintes propriedades do registro:

| Propriedade | Descrição |
|:--|:--|
| `MaliciousIp` |Endereço de RemoteIp |
| `IndicatorThreadType` |O indicador de ameaça detectado é um dos seguintes valores, *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
| `Description` |Descrição da ameaça observada. |
| `TLPLevel` |O TLP (Traffic Light Protocol) é um dos valores definidos, *Branco*, *Verde*, *Âmbar*, *Vermelho*. |
| `Confidence` |Os valores são *0 – 100*. |
| `Severity` |Os valores são *0 – 5*, onde *5* é o mais grave e *0* não é grave. O valor padrão é *3*.  |
| `FirstReportedDateTime` |A primeira vez que o provedor relatou o indicador. |
| `LastReportedDateTime` |A última vez que o indicador foi visto pelo Interflow. |
| `IsActive` |Indica que os indicadores estão desativados com o valor *Verdadeiro* ou *Falso*. |
| `ReportReferenceLink` |Links para relatórios relacionados a um dado observável. |
| `AdditionalInformation` |Fornece informações adicionais, se aplicáveis, sobre a ameaça observada. |

### <a name="servicemapcomputercl-records"></a>Registros ServiceMapComputer_CL

Registros com um tipo de *ServiceMapComputer_CL* têm dados de inventário para servidores com agentes do Mapa do Serviço. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição |
|:--|:--|
| `Type` | *ServiceMapComputer_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | O identificador exclusivo para um computador dentro do workspace |
| `ResourceName_s` | O identificador exclusivo para um computador dentro do workspace |
| `ComputerName_s` | O FQDN do computador |
| `Ipv4Addresses_s` | Uma lista dos endereços IPv4 do servidor |
| `Ipv6Addresses_s` | Uma lista dos endereços IPv6 do servidor |
| `DnsNames_s` | Uma matriz de nomes DNS |
| `OperatingSystemFamily_s` | Windows ou Linux |
| `OperatingSystemFullName_s` | O nome completo do sistema operacional  |
| `Bitness_s` | O número de bit do computador (32-BIT ou 64-BIT)  |
| `PhysicalMemory_d` | A memória física em MB |
| `Cpus_d` | O número de CPUs |
| `CpuSpeed_d` | A velocidade da CPU em MHz|
| `VirtualizationState_s` | *desconhecido*, *físico*, *virtual*, *hipervisor* |
| `VirtualMachineType_s` | *hyperv*, *vmware*, e assim por diante |
| `VirtualMachineNativeMachineId_g` | A ID da VM conforme atribuída pelo seu hipervisor |
| `VirtualMachineName_s` | O nome da VM |
| `BootTime_t` | O tempo de inicialização |

### <a name="servicemapprocesscl-type-records"></a>Registros do tipo ServiceMapProcess_CL Type

Registros com um tipo de *ServiceMapProcess_CL* têm dados de inventário para processos conectados com TCP em servidores com agentes do Mapa do Serviço. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | O identificador exclusivo para um processo dentro do workspace |
| `ResourceName_s` | O identificador exclusivo para um processo dentro do computador no qual está sendo executado|
| `MachineResourceName_s` | O nome do recurso do computador |
| `ExecutableName_s` | O nome do processo executável |
| `StartTime_t` | O tempo de início do pool de processos |
| `FirstPid_d` | O primeiro PID no pool de processos |
| `Description_s` | A descrição do processo |
| `CompanyName_s` | O nome da empresa |
| `InternalName_s` | O nome interno |
| `ProductName_s` | O nome do produto |
| `ProductVersion_s` | A versão do produto |
| `FileVersion_s` | A versão do arquivo |
| `CommandLine_s` | A linha de comando |
| `ExecutablePath _s` | O caminho para o arquivo executável |
| `WorkingDirectory_s` | O diretório de trabalho |
| `UserName` | A conta sob a qual o processo está sendo executado |
| `UserDomain` | O domínio sob o qual o processo está sendo executado |

## <a name="sample-log-searches"></a>Pesquisas de log de exemplo

### <a name="list-all-known-machines"></a>Lista todas as máquinas conhecidas

ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Lista a capacidade de memória física de todos os computadores gerenciados.

ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Listar o nome, DNS, IP e sistema operacional do computador.

ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Localizar todos os processos com "sql" na linha de comando

ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Localizar uma máquina (registro mais recente) por nome de recurso

search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Localizar um computador (registro mais recente) pelo endereço IP

search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>Listar todos os processos conhecidos em um computador especificado

ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="list-all-computers-running-sql"></a>Listar todos os computadores que executam SQL

ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Listar todas as versões de produtos exclusivas de curl no meu datacenter

ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Criar um grupo de computadores de todos os computadores executando CentOS

ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Resumir as conexões de saída a partir de um grupo de computadores

```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="rest-api"></a>API REST

Todos os dados do servidor, do processo e de dependência no Mapa do Serviço estão disponíveis por meio da [API REST do Mapa do Serviço](https://docs.microsoft.com/rest/api/servicemap/).

## <a name="diagnostic-and-usage-data"></a>Dados de diagnóstico e uso

A Microsoft coleta automaticamente dados de uso e de desempenho por meio do uso do serviço Mapa do Serviço. A Microsoft utiliza esses dados para fornecer e aprimorar a qualidade, segurança e integridade do serviço do Mapa do Serviço. Para fornecer recursos de solução de problemas precisos e eficientes, os dados incluem informações sobre a configuração do seu software, como sistema operacional e versão, endereço IP, nome DNS e nome da estação de trabalho. A Microsoft não coleta nomes, endereços ou outras informações de contato.

Para obter mais informações sobre a coleta e uso de dados, consulte a [Política de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [pesquisas de logs](../../azure-monitor/log-query/log-query-overview.md) no Log Analytics para recuperar dados coletados pelo Mapa do Serviço.


## <a name="troubleshooting"></a>Solução de problemas

Consulte a [seção Solução de problemas do documento Configurando o mapa do serviço]( service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Comentários

Você tem algum comentário sobre o Mapa de Serviço ou sobre esta documentação?  Visita nossa [página Voz do Usuário](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), onde você pode sugerir recursos ou votar em sugestões existentes.
