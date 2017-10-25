---
title: "Integração com o OMS (Operations Management Suite) | Microsoft Docs"
description: "Além de usar os recursos padrão do OMS, você pode integrá-los com outros aplicativos de gerenciamento e serviços para proporcionar um ambiente de gerenciamento híbrido, fornecer cenários de gerenciamento personalizado exclusivos em seu ambiente ou oferecer uma experiência de gerenciamento personalizada para seus clientes.  Este artigo proporciona uma visão geral das diferentes opções de integração com o OMS e links para artigos, fornecendo informações técnicas detalhadas."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fc5f3a8a-77f7-4103-bd7e-744c15ffcca7
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7a24df6f2c3b2c091d1b66b8b9c0a61035ffde11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrating-with-operations-management-suite-oms"></a>Integração com o OMS (Operations Management Suite)
O Microsoft Operations Management Suite é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda você a gerenciar e proteger sua infraestrutura local e de nuvem.  Além de usar os recursos padrão do OMS, você pode integrá-los com outros aplicativos de gerenciamento e serviços para proporcionar um ambiente de gerenciamento híbrido, fornecer cenários de gerenciamento personalizado exclusivos em seu ambiente ou oferecer uma experiência de gerenciamento personalizada para seus clientes.  Este artigo proporciona uma visão geral das diferentes opções de integração com os serviços do OMS e links para artigos, fornecendo informações técnicas detalhadas. 

## <a name="log-analytics"></a>Log Analytics
Todos os dados de gerenciamento coletados pelo Log Analytics são armazenados em um repositório, que é hospedado no Azure.  Todos os dados armazenados no repositório estão disponíveis em pesquisas de log que fornecem uma análise rápida de grandes volumes de dados.  Seus requisitos de integração podem ser preencher o repositório com novos dados, tornando-os disponíveis para análise, ou extrair dados do repositório para fornecer uma nova visualização ou para integrá-los com outra ferramenta de gerenciamento.

Cada dado no repositório é armazenado como um registro.  Ao preencher o repositório, você deverá fornecer aos usuários o tipo de registro que sua solução usa e uma descrição de suas propriedades.  Ao recuperar os dados, você precisará das informações sobre os dados com os quais está trabalhando.

![Preenchendo o repositório do OMS](media/operations-management-suite-integration/repository.png)

### <a name="populate-the-log-analytics-repository"></a>Preencha o repositório do Log Analytics
Há vários métodos para preencher o repositório do OMS.  O método usado dependerá de fatores, como onde os dados de origem estão localizados, o formato dos dados e para quais clientes você precisa dar suporte.  Depois que os dados forem armazenados no repositório, a maneira como eles foram coletados não fará diferença.

As seções a seguir descrevem as diferentes opções para preencher o repositório do OMS.

#### <a name="connected-sources-and-data-sources"></a>Fontes conectadas e Fontes de dados
As fontes conectadas são os locais onde os dados podem ser recuperados para o repositório do OMS.  As Fontes de Dados e as Soluções são executadas nas Fontes Conectadas e definem os dados específicos que são coletados.  Se seu aplicativo grava dados em uma das fontes de dados, você poderá coletá-los ao configurar a fonte de dados.  Por exemplo, se seu aplicativo cria eventos de Syslog, eles poderão ser coletados pela fonte de dados de Syslog em um agente do Linux.

* [Fontes de dados no Log Analytics](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Soluções
As soluções ampliam os recursos do OMS.  Uma solução pode coletar dados da fonte conectada ou realizar análises em registros já coletados no repositório.  Cada solução fornecida pela Microsoft tem um artigo individual que fornece os detalhes sobre os dados que coleta.

* [Soluções no Log Analytics](../log-analytics/log-analytics-add-solutions.md)

#### <a name="http-data-collector-api"></a>API do coletor de dados HTTP
A API do coletor de dados HTTP do Log Analytics é uma API REST que permite que você adicione dados JSON no repositório do Log Analytics.  Você pode aproveitar essa API quando tiver um aplicativo que não fornece dados por meio de uma das outras fontes de dados ou soluções.  Ela pode ser usada para preencher o repositório de qualquer cliente que pode chamar a API, e não conta com a agenda de coleção de nenhuma fonte de dados ou solução.

* [API do coletor de dados HTTP do Log Analytics](../log-analytics/log-analytics-data-collector-api.md)

### <a name="retrieve-data-from-the-log-analytics-repository"></a>Recuperar dados do repositório do Log Analytics
Há vários métodos para recuperar dados do repositório do OMS.  Talvez você queira que os usuários recuperem dados usando o console do OMS e os forneça com diferentes tipos de visualizações e análises.  Você também pode recuperar os dados de um processo externo, como outra solução de gerenciamento.

#### <a name="log-searches"></a>Pesquisas de log
Todos os dados armazenados no repositório do OMS estão disponíveis por meio de pesquisas de log.  Os usuários podem executar suas próprias análises ad hoc no console do OMS ou criar um painel com uma visualização para uma pesquisa de log específica.  As soluções podem conter exibições personalizadas com visualizações baseadas em pesquisas predefinidas.  Você pode usar a API de Pesquisa de Log para acessar dados no repositório do OMS de um aplicativo externo ou de uma ferramenta de gerenciamento.  

* [Pesquisas de log no Log Analytics](../log-analytics/log-analytics-log-searches.md)
* [API REST de pesquisa de log do Log Analytics](../log-analytics/log-analytics-log-search-api.md)
* [Cmdlets do Log Analytics](https://msdn.microsoft.com/library/mt188224.aspx)

#### <a name="custom-views"></a>Modos de exibição personalizados
O Designer de Exibição permite que você crie modos de exibição personalizados no console do OMS, que forneçam aos usuários visualização e análise dos dados em sua solução.  Cada exibição inclui um bloco, que é exibido na página principal do console, e qualquer número de partes da visualização que são baseadas nas pesquisas de log que você define.

* [Designer de exibição do Log Analytics](../log-analytics/log-analytics-view-designer.md)

#### <a name="power-bi"></a>Power BI
O Log Analytics pode exportar dados do repositório do OMS para o Power BI automaticamente para que você possa aproveitar suas visualizações e ferramentas de análise.  Ele executa essa exportação em uma agenda para que os dados sejam mantidos atualizados. 

* [Exportar dados do Log Analytics para o Power BI](../log-analytics/log-analytics-powerbi.md)

## <a name="automation"></a>Automação
O OMS pode automatizar processos para reagir aos dados coletados ou executar outras funções de gerenciamento.  Ele pode coletar dados de seu aplicativo e inseri-los no repositório do OMS, ou você pode automatizar a correção de um problema conhecido em resposta a dados encontrados no repositório. 

![Automação](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbooks
Os Runbooks na Automação do Azure executam scripts do PowerShell e fluxos de trabalho na nuvem do Azure.  Você pode usá-los para gerenciar recursos no Azure ou outros recursos que podem ser acessados da nuvem.  Os Runbooks também podem ser executados em um data center local usando o Hybrid Runbook Worker.  Você pode iniciar um runbook do Portal do Azure ou de processos externos usando vários métodos, como o PowerShell ou a API de Automação.

* [Como iniciar um Runbook na Automação do Azure](../automation/automation-starting-a-runbook.md)
* [Cmdlets da Automação do Azure](https://msdn.microsoft.com/library/dn690262.aspx)
* [API REST de Automação](https://msdn.microsoft.com/library/mt662285.aspx)
* [Automação .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Alertas
As regras de alerta executam pesquisas de log automaticamente, de acordo com uma agenda.  Se os resultados corresponderem a critérios específicos, o alerta resultante poderá iniciar um runbook na Automação do Azure ou chamar um webhook, que poderá iniciar um processo externo.  Ambas as respostas podem abranger detalhes do alerta, incluindo os dados retornados na pesquisa de log.

* [Alertas no Log Analytics](../log-analytics/log-analytics-alerts.md)
* [API de alerta do Log Analytics](../log-analytics/log-analytics-api-alerts.md)

## <a name="backup-and-site-recovery"></a>Backup e Site Recovery
O Backup do Azure e o Site Recovery prestam serviços para proteger seus dados corporativos e garantir a disponibilidade de servidores e aplicativos.  Você pode aproveitar esses serviços para executar tais cenários, como ao prestar serviços de backup para seu aplicativo ou ao iniciar um failover de uma máquina virtual.

* [Cmdlets do Backup do Azure](https://msdn.microsoft.com/library/mt619253.aspx)
* [API REST do Azure Site Recovery](https://msdn.microsoft.com/library/azure/mt750497.aspx)
* [Cmdlets do Azure Site Recovery](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>Soluções personalizadas
Você pode encapsular a lógica de integração em uma solução personalizada para executar no seu espaço de trabalho ou no espaço de trabalho do cliente.  Sua solução pode incluir qualquer um dos métodos de integração neste artigo, além de outros recursos para fornecer um cenário de gerenciamento completo.  Os recursos na solução são empacotados de modo que, quando a solução for removida, todos os recursos que ela criou serão removidos do espaço de trabalho do OMS e da assinatura do Azure.

Por exemplo, sua solução pode incluir um runbook de automação para coletar e processar dados e, então, preencher o repositório do Log Analytics usando a API do Coletor de Dados HTTP.  Você também pode incluir um modo de exibição personalizado que apresente e analise os dados coletados.  

* Criação de soluções personalizadas (em breve)    

## <a name="next-steps"></a>Próximas etapas
* Recorra ao [SDK do OMS](operations-management-suite-sdk.md) para obter informações técnicas sobre a automatização de serviços do OMS.  

