---
title: "Solução Análise de DNS no Azure Log Analytics | Microsoft Docs"
description: "Configure e use a solução Análise de DNS no Log Analytics para coletar informações relacionadas à segurança, ao desempenho e às operações na infraestrutura DNS."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.openlocfilehash: 0e8fc0ffb8e0d0bdf00bea46594fe050c00b6c8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Coletar informações sobre a infraestrutura DNS com a solução Visualização da Análise de DNS

![Símbolo da Análise de DNS](./media/log-analytics-dns/dns-analytics-symbol.png)

Este artigo descreve como configurar e usar a solução Análise de DNS do Azure no Azure Log Analytics para coletar informações relacionadas à segurança, ao desempenho e às operações na infraestrutura DNS.

A Análise de DNS ajuda você a:

- Identificar clientes que tentam resolver nomes de domínio mal-intencionados.
- Identificar registros de recursos obsoletos.
- Identificar os nomes de domínio consultados com frequência e clientes DNS comunicativos.
- Exibir a carga de solicitação em servidores DNS.
- Exibir falhas de registro de DNS dinâmico.

A solução coleta, analisa e correlaciona logs de auditoria e analíticos de DNS do Windows, bem como outros dados relacionados de seus servidores DNS.

## <a name="connected-sources"></a>Fontes conectadas

A seguinte tabela descreve as fontes conectadas que têm suporte nessa solução:

| **Fonte conectada** | **Suporte** | **Descrição** |
| --- | --- | --- |
| [Agentes do Windows](log-analytics-windows-agents.md) | Sim | A solução coleta informações de DNS dos agentes do Windows. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não | A solução não coleta informações de DNS dos agentes diretos do Linux. |
| [Grupo de gerenciamento do System Center Operations Manager](log-analytics-om-agents.md) | Sim | A solução coleta informações de DNS dos agentes em um grupo de gerenciamento conectado do Operations Manager. Não é necessário ter uma conexão direta do agente do Operations Manager com o Operations Management Suite. Os dados são encaminhados do grupo de gerenciamento para o repositório do Operations Management Suite. |
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não | O armazenamento do Azure não é usado pela solução. |

### <a name="data-collection-details"></a>Detalhes da coleta de dados

A solução coleta dados relacionados a eventos e ao inventário DNS dos servidores DNS, onde um agente do Log Analytics está instalado. Esses dados são então carregados no Log Analytics e exibidos no painel da solução. Os dados relacionados ao inventário, como número de servidores DNS, zonas e registros de recursos, são coletados pela execução de cmdlets do PowerShell do DNS. Os dados são atualizados uma vez a cada dois dias. Os dados relacionados a eventos são coletados quase em tempo real dos [logs de análise e auditoria](https://technet.microsoft.com/library/dn800669.aspx#enhanc) fornecidos pelo log e diagnóstico avançado de DNS no Windows Server 2012 R2.

## <a name="configuration"></a>Configuração

Use as seguintes informações para configurar a solução:

- É necessário ter um agente do [Windows](log-analytics-windows-agents.md) ou do [Operations Manager](log-analytics-om-agents.md) em cada servidor DNS que você deseja monitorar.
- É possível adicionar a solução Análise de DNS ao espaço de trabalho do Operations Management Suite por meio do [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Você também pode usar o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).

A solução inicia a coleta de dados sem a necessidade de configuração adicional. No entanto, você pode usar a configuração a seguir para personalizar a coleta de dados.

### <a name="configure-the-solution"></a>Configurar a solução

No painel de solução, clique em **Configuração** para abrir a página Configuração de Análise de DNS. Há dois tipos de alteração de configuração que podem ser feitos:

- **Nomes de Domínio na Lista de Permissões**. A solução não processa todas as consultas de pesquisa. Ela mantém uma lista de permissões de sufixos de nome de domínio. As consultas de pesquisa que são resolvidas para os nomes de domínio que correspondem aos sufixos de nome de domínio na lista de permissões não são processadas pela solução. O não processamento de nomes de domínio na lista de permissões ajuda a otimizar os dados enviados ao Log Analytics. A lista de permissões padrão inclui nomes de domínio público populares, como www.google.com e www.facebook.com. Você pode exibir a lista padrão completa com a barra de rolagem.

 É possível modificar a lista para adicionar qualquer sufixo de nome de domínio do qual você deseja exibir informações de pesquisa. Você também pode remover qualquer sufixo de nome de domínio do qual você não deseja exibir informações de pesquisa.

- **Limite de Cliente Comunicativo**. Os clientes DNS que excedem o limite do número de solicitações de pesquisa são realçados na folha **Clientes DNS**. O limite padrão é 1.000. Você pode editar o limite.

    ![Nomes de domínio na lista de permissões](./media/log-analytics-dns/dns-config.png)

## <a name="management-packs"></a>Pacotes de gerenciamento

Se você estiver usando o Microsoft Monitoring Agent para se conectar ao espaço de trabalho do Operations Management Suite, o seguinte pacote de gerenciamento será instalado:

- Pacote de Inteligência do Coletor de Dados DNS da Microsoft (Microsft.IntelligencePacks.Dns)

Se o grupo de gerenciamento do Operations Manager estiver conectado ao espaço de trabalho do Operations Management Suite, os pacotes de gerenciamento a seguir serão instalados no Operations Manager quando você adicionar essa solução. Não há nenhuma manutenção nem configuração obrigatória destes pacotes de gerenciamento:

- Pacote de Inteligência do Coletor de Dados DNS da Microsoft (Microsft.IntelligencePacks.Dns)
- Configuração da Análise de DNS do Microsoft System Center Advisor (Microsoft.IntelligencePack.Dns.Configuration)

Para saber mais sobre como os pacotes de gerenciamento da solução são atualizados, veja [Conectar o Operations Manager ao Log Analytics](log-analytics-om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Usar a solução Análise de DNS

Esta seção explica todas as funções do painel e como usá-las.

Depois de adicionar a solução ao espaço de trabalho, o bloco da solução na página Visão Geral do Operations Management Suite fornecerá um resumo rápido da infraestrutura DNS. Ela inclui o número de servidores DNS nos quais os dados estão sendo coletados. Também inclui o número de solicitações feitas pelos clientes para resolver domínios mal-intencionados nas últimas 24 horas. Quando você clica no bloco, o painel da solução é aberto.

![bloco Análise de DNS](./media/log-analytics-dns/dns-tile.png)

### <a name="solution-dashboard"></a>Painel da solução

O painel da solução mostra informações resumidas para os vários recursos da solução. Ele também inclui links para a exibição detalhada de diagnóstico e análise investigativa. Por padrão, os dados são mostrados para os últimos sete dias. Você pode alterar o intervalo de data e hora usando o **controle de seleção de data/hora**, conforme mostrado na seguinte imagem:

![Controle de seleção de hora](./media/log-analytics-dns/dns-time.png)

O painel da solução mostra as seguintes folhas:

**Segurança DNS**. Relata os clientes DNS que estão tentando se comunicar com domínios mal-intencionados. Ao usar os feeds de inteligência em ameaças da Microsoft, a Análise de DNS pode detectar IPs do cliente que estão tentando acessar domínios mal-intencionados. Em muitos casos, os dispositivos infectados por malware “discam” para o centro de “comando e controle” do domínio mal-intencionado resolvendo o nome de domínio do malware.

![folha Segurança DNS](./media/log-analytics-dns/dns-security-blade.png)

Quando você clica em um IP do cliente na lista, a Pesquisa de Logs é aberta e mostra os detalhes da pesquisa da respectiva consulta. No seguinte exemplo, a Análise de DNS detectou que a comunicação foi feita com um [IRCbot](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=Win32/IRCbot):

![Resultados da pesquisa de logs mostrando ircbot](./media/log-analytics-dns/ircbot.png)

As informações ajudam você a identificar:

- o IP do cliente que iniciou a comunicação.
- O nome de domínio que é resolvido para o IP mal-intencionado.
- Os endereços IP para os quais o nome de domínio é resolvido.
- O endereço IP mal-intencionado.
- A gravidade do problema.
- O motivo para colocar o IP mal-intencionado na lista de bloqueios.
- O tempo de detecção.

**Domínios Consultados**. Fornece os nomes de domínio consultados com mais frequência pelos clientes DNS no ambiente. É possível exibir a lista de todos os nomes de domínio consultados. Você também pode fazer uma busca detalhada dos detalhes da solicitação de pesquisa de um nome de domínio específico na Pesquisa de Logs.

![Folha Domínios Consultados](./media/log-analytics-dns/domains-queried-blade.png)

**Clientes DNS**. Relata os clientes que *violam o limite* do número de consultas no período escolhido. Você pode exibir a lista de todos os clientes DNS e os detalhes das consultas feitas por eles na Pesquisa de Logs.

![Folha Clientes DNS](./media/log-analytics-dns/dns-clients-blade.png)

**Registros de DNS Dinâmico**. Relata as falhas de registro de nome. Todas as falhas de [registros de recursos](https://en.wikipedia.org/wiki/List_of_DNS_record_types) do endereço (Tipo A e AAAA) são destacadas com os IPs do cliente que fizeram as solicitações de registro. Em seguida, é possível usar essas informações para encontrar a causa raiz da falha de registro seguindo estas etapas:

1. Encontre a zona que é autoritativa para o nome que o cliente está tentando atualizar.

2. Use a solução para verificar as informações de inventário dessa zona.

3. Verifique se a atualização dinâmica para a zona está habilitada.

4. Verifique se a zona está configurada para atualização dinâmica segura ou não.

    ![folha Registros de DNS Dinâmico](./media/log-analytics-dns/dynamic-dns-reg-blade.png)

**Solicitações de registro de nome**. O bloco superior mostra uma linha de tendência das solicitações de atualização dinâmica de DNS com êxito e com falha. O bloco inferior lista os 10 principais clientes que enviam solicitações de atualização DNS com falha aos servidores DNS, classificados pelo número de falhas.

![folha Solicitações de registro de nome ](./media/log-analytics-dns/name-reg-req-blade.png)

**Consultas de Análise de DDI de Exemplo**. Contém uma lista das consultas de pesquisa mais comuns que buscam dados analíticos brutos diretamente.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Consultas de exemplo](./media/log-analytics-dns/queries.png)

Você pode usar essas consultas como um ponto de partida para criar suas próprias consultas para relatórios personalizados. As consultas são vinculadas à página Pesquisa de Logs da Análise de DNS na qual os resultados são exibidos:

- **Lista de Servidores DNS**. Mostra uma lista de todos os servidores DNS com seus FQDNs, nomes de domínio, nomes da floresta e IPs de servidor associados.
- **Lista de Zonas DNS**. Mostra uma lista de todas as zonas DNS com o nome da zona, status de atualização dinâmica, servidores de nome e status de autenticação do DNSSEC associados.
- **Registros de Recursos não Utilizados**. Mostra uma lista de todos os registros de recursos não utilizados/obsoletos. Essa lista contém o nome do registro de recurso, o tipo de registro de recurso, o servidor DNS associado, o tempo de geração de registro e o nome da zona. Você pode usar essa lista para identificar os registros de recursos DNS que não estão mais em uso. Com base nessas informações, é possível então remover essas entradas dos servidores DNS.
- **Carga de Consulta de Servidores DNS**. Mostra informações de forma que você possa obter uma perspectiva da carga DNS nos servidores DNS. Essas informações podem ajudá-lo a planejar a capacidade dos servidores. Acesse a guia **Métricas** para alterar a exibição para uma visualização gráfica. Essa exibição ajuda a entender como a carga DNS é distribuída nos servidores DNS. Ela mostra as tendências da taxa de consulta DNS para cada servidor.

    ![Resultados da pesquisa de logs de consulta de servidores DNS](./media/log-analytics-dns/dns-servers-query-load.png)

- **Carga de Consulta de Zonas DNS**. Mostra as estatísticas por segundo de consulta de zona DNS de todas as zonas nos servidores DNS que estão sendo gerenciados pela solução. Clique na guia **Métricas** para alterar a exibição de registros detalhados para uma visualização gráfica dos resultados.
- **Eventos de Configuração**. Mostra todos os eventos de alteração de configuração de DNS e as mensagens associadas. Você pode filtrar esses eventos com base no horário do evento, na ID do evento, no servidor DNS ou na categoria da tarefa. Os dados podem ajudá-lo a auditar as alterações feitas em servidores DNS específicos em horários específicos.
- **Log Analítico de DNS**. Mostra todos os eventos analíticos em todos os servidores DNS gerenciados pela solução. Em seguida, é possível filtrar esses eventos com base na hora do evento, na ID do evento, no servidor DNS, no IP do cliente que faz a consulta de pesquisa e na categoria da tarefa do tipo de consulta. Os eventos analíticos do servidor DNS permitem o rastreamento da atividade no servidor DNS. Um evento analítico é registrado em log sempre que o servidor envia ou recebe informações DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Pesquisar usando a Pesquisa de Logs da Análise de DNS

Na página Pesquisa de Logs, é possível criar uma consulta. Você pode filtrar os resultados da pesquisa usando controles de faceta. Você também pode criar consultas avançadas para transformar, filtrar e relatar sobre seus resultados. Comece usando as seguintes consultas:

1. Na **caixa de consulta de pesquisa**, digite `Type=DnsEvents` para exibir todos os eventos DNS gerados pelos servidores DNS gerenciados pela solução. Os resultados listam os dados de logs de todos os eventos relacionados às consultas de pesquisa, aos registros dinâmicos e às alterações de configuração.

    ![Pesquisa de logs de DnsEvents](./media/log-analytics-dns/log-search-dnsevents.png)  

    a. Para exibir os dados de logs das consultas de pesquisa, selecione **LookUpQuery** como o filtro **Subtipo** no controle de faceta à esquerda. É exibida uma tabela que lista os eventos de consulta de pesquisa do período selecionado.

    b. Para exibir os dados de logs dos registros dinâmicos, selecione **DynamicRegistration** como o filtro **Subtipo** no controle de faceta à esquerda. É exibida uma tabela que lista os eventos de registro dinâmico do período selecionado.

    c. Para exibir os dados de logs das alterações de configuração, selecione **ConfigurationChange** como o filtro **Subtipo** no controle de faceta à esquerda. É exibida uma tabela que lista os eventos de alteração de configuração do período selecionado.

2. Na **caixa de consulta de pesquisa**, digite `Type=DnsInventory` para exibir todos os dados relacionados ao inventário DNS dos servidores DNS gerenciados pela solução. Os resultados listam os dados de log dos servidores DNS, das zonas DNS e dos registros de recursos.

    ![Pesquisa de logs de DnsInventory](./media/log-analytics-dns/log-search-dnsinventory.png)

## <a name="feedback"></a>Comentários

Há duas maneiras de enviar comentários:

- **UserVoice**. Poste ideias para melhoria dos recursos da Análise de DNS. Visite a [página do UserVoice do Operations Management Suite](https://aka.ms/dnsanalyticsuservoice).
- **Ingressar em nosso coorte**. Sempre estamos interessados em ter novos clientes em nossos coortes para que eles tenham acesso antecipado a novos recursos e nos ajudem a melhorar a Análise de DNS. Se estiver interessado em participar de nossos coortes, preencha esta [pesquisa rápida](https://aka.ms/dnsanalyticssurvey).

## <a name="next-steps"></a>Próximas etapas

[Pesquisar logs](log-analytics-log-searches.md) para exibir registros de log DNS detalhados.
