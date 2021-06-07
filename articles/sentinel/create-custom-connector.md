---
title: Recursos para criar conectores personalizados do Azure Sentinel | Microsoft Docs
description: Saiba mais sobre os recursos disponíveis para a criação de conectores personalizados para o Azure Sentinel. Os métodos incluem o agente de Log Analytics e a API, Logstash, aplicativos lógicos, PowerShell e Azure Functions.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2021
ms.author: bagol
ms.openlocfilehash: 25f83088bdc55dbafe7ccf0ff06b0c6595c9ea71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724346"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Recursos para criar conectores personalizados do Azure Sentinel

O Azure Sentinel fornece uma ampla variedade de [conectores internos para serviços do Azure e soluções externas](connect-data-sources.md), além de oferecer suporte à ingestão de dados de algumas fontes sem um conector dedicado.

Se não for possível conectar sua fonte de dados ao Azure Sentinel usando qualquer uma das soluções existentes disponíveis, considere criar seu próprio conector de fonte de dados.

Para obter uma lista completa dos conectores com suporte, consulte a postagem do blog do [Sentinela do Azure: os conectores gerais (CEF, syslog, direto, agente, personalizado e mais)](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) .

## <a name="compare-custom-connector-methods"></a>Comparar métodos de conectores personalizados

A tabela a seguir compara os detalhes essenciais sobre cada método para a criação de conectores personalizados descritos neste artigo. Selecione os links na tabela para obter mais detalhes sobre cada método.

|Descrição do método  |Recurso | Sem servidor    |Complexidade  |
|---------|---------|---------|---------|
|**[Agente de Log Analytics](#connect-with-the-log-analytics-agent)** <br>Melhor para coletar arquivos de fontes locais e de IaaS   | Somente coleção de arquivos  |   No      |Baixo         |
|**[Logstash](#connect-with-logstash)** <br>Melhor para fontes locais e IaaS, qualquer fonte para a qual um plug-in esteja disponível e organizações já familiarizados com o Logstash  | Os plug-ins disponíveis, além do plug-in personalizado, fornecem uma flexibilidade significativa.   |   Foi requer que uma VM ou um cluster de VM seja executado           |   Pequena dá suporte a muitos cenários com plug-ins      |
|**[Aplicativos Lógicos](#connect-with-logic-apps)** <br>Alto custo; Evite para dados de alto volume <br>Melhor para fontes de nuvem de baixo volume  | A programação sem código permite flexibilidade limitada, não há suporte para a implementação de algoritmos.<br><br> Se nenhuma ação disponível já oferecer suporte aos seus requisitos, a criação de uma ação personalizada poderá adicionar complexidade.    |    Sim         |   Pequena desenvolvimento simples e com código      |
|**[PowerShell](#connect-with-powershell)** <br>Melhor para protótipos e carregamentos de arquivos periódicos | Suporte direto para a coleta de arquivos. <br><br>O PowerShell pode ser usado para coletar mais fontes, mas exigirá codificação e configuração do script como um serviço.      |No               |  Baixo       |
|**[API de Log Analytics](#connect-with-the-log-analytics-api)** <br>Melhor para os ISVs que implementam a integração e para requisitos de coleção exclusivos   | Dá suporte a todos os recursos disponíveis com o código.  | Depende da implementação           |     Alto    |
|**[Azure Functions](#connect-with-azure-functions)** Melhor para fontes de nuvem de alto volume e requisitos de coleção exclusivos  | Dá suporte a todos os recursos disponíveis com o código.  |  Sim             |     Elevada requer conhecimento de programação    |
|     |         |                |

> [!TIP]
> Para comparações de uso de aplicativos lógicos e Azure Functions para o mesmo conector, consulte:
> 
> - [Ingerir logs de firewall do aplicativo Web com rapidez no Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (Comunidade GitHub do Azure Sentinel): conector de [aplicativo lógico](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)  |  [Azure function Connector](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Conectar-se com o agente de Log Analytics

Se a fonte de dados fornecer eventos em arquivos, recomendamos que você use o agente de Log Analytics de Azure Monitor para criar seu conector personalizado.

- Para obter mais informações, consulte [coletando logs personalizados no Azure monitor](../azure-monitor/agents/data-sources-custom-logs.md).

- Para obter um exemplo desse método, consulte [coletando fontes de dados JSON personalizadas com o agente de log Analytics para Linux no Azure monitor](../azure-monitor/agents/data-sources-json.md).

## <a name="connect-with-logstash"></a>Conectar-se ao Logstash

Se você estiver familiarizado com o [Logstash](https://www.elastic.co/logstash), convém usar o Logstash com o [plug-in de saída Logstash para o Azure Sentinel](connect-logstash.md) para criar seu conector personalizado.

Com o plug-in de saída Logstash do Azure Sentinel, você pode usar quaisquer plug-ins de entrada e filtragem do Logstash e configurar o Azure Sentinel como a saída para um pipeline Logstash. O Logstash tem uma grande biblioteca de plug-ins que habilitam a entrada de várias fontes, como hubs de eventos, Apache Kafka, arquivos, bancos de dados e serviços de nuvem. Use os plug-ins de filtragem para analisar eventos, filtrar eventos desnecessários, ofuscar valores e muito mais.

Para obter exemplos de como usar o Logstash como um conector personalizado, consulte:

- Procurando [TTPS de uma violação de capital em logs de AWS usando o Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) (blog)
- [Guia de implementação do RADWARE Azure Sentinel](https://support.radware.com/ci/okcsFattach/get/1025459_3)

Para obter exemplos de plug-ins úteis do Logstash, consulte:

- [Plug-in de entrada CloudWatch](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Plug-in de hubs de eventos do Azure](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Plug-in de entrada do Google Cloud Storage](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Plug-in de entrada Google_pubsub](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> O Logstash também habilita a coleta de dados dimensionada usando um cluster. Para obter mais informações, consulte [usando uma VM Logstash com balanceamento de carga em escala](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854).
>

## <a name="connect-with-logic-apps"></a>Conectar-se com aplicativos lógicos

Use um [aplicativo lógico do Azure](../logic-apps/index.yml) para criar um conector personalizado sem servidor para o Azure Sentinel.

> [!NOTE]
> Embora a criação de conectores sem servidor usando aplicativos lógicos possa ser conveniente, o uso de aplicativos lógicos para seus conectores pode ser dispendioso para grandes volumes de dados.
>
> É recomendável que você use esse método somente para fontes de dados de volume baixo ou enriquecendo os carregamentos de dados.
>

1. **Use um dos seguintes gatilhos para iniciar seus aplicativos lógicos**:

    |Gatilho  |Descrição  |
    |---------|---------|
    |**Uma tarefa recorrente**     |   Por exemplo, agende seu aplicativo lógico para recuperar dados regularmente de arquivos específicos, bancos de dados ou APIs externas. <br>Para obter mais informações, consulte [criar, agendar e executar tarefas recorrentes e fluxos de trabalho em aplicativos lógicos do Azure](../connectors/connectors-native-recurrence.md).      |
    |**Gatilho sob demanda**     | Execute seu aplicativo lógico sob demanda para a coleta e teste manuais de dados. <br>Para obter mais informações, consulte  [chamar, disparar ou aninhar aplicativos lógicos usando pontos de extremidade https](../logic-apps/logic-apps-http-endpoint.md).        |
    |**Ponto de extremidade HTTP/S**     |  Recomendado para streaming e se o sistema de origem puder iniciar a transferência de dados. <br>Para obter mais informações, consulte [chamar pontos de extremidade de serviço por http ou HTTPS](../connectors/connectors-native-http.md).       |
    |     |         |

1. **Use qualquer um dos conectores de aplicativos lógicos que lêem informações para obter seus eventos**. Por exemplo:

    - [Conectar-se a uma API REST](/connectors/custom-connectors/)
    - [Conectar a um SQL Server](/connectors/sql/)
    - [Conectar-se a um sistema de arquivos](/connectors/filesystem/)

    > [!TIP]
    > Os conectores personalizados para APIs REST, SQL Servers e sistemas de arquivos também oferecem suporte à recuperação de dados de fontes de dados locais. Para obter mais informações, consulte [instalar a documentação do gateway de dados local](/connectors/filesystem/) .
    >

1. **Prepare as informações que você deseja recuperar**.

    Por exemplo, use a [ação analisar JSON](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) para acessar propriedades no conteúdo JSON, permitindo que você selecione essas propriedades na lista de conteúdo dinâmico quando especificar entradas para seu aplicativo lógico.

    Para obter mais informações, consulte [executar operações de dados em aplicativos lógicos do Azure](../logic-apps/logic-apps-perform-data-operations.md).

1. **Grave os dados em log Analytics**.

    Para obter mais informações, consulte a documentação do [coletor de dados do Azure log Analytics](/connectors/azureloganalyticsdatacollector/) .

Para obter exemplos de como você pode criar um conector personalizado para o Azure Sentinel usando aplicativos lógicos, consulte:

- [Criar um pipeline de dados com a API do Coletor de Dados](/connectors/azureloganalyticsdatacollector/)
- [Conector de aplicativos lógicos do Palo Alto prisma usando um webhook](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (Comunidade GitHub do Azure Sentinel)
- [Proteja suas chamadas do Microsoft Teams com a ativação agendada](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (blog)
- [Ingerir indicadores de ameaça do ALIENVAULT OTX no Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) (blog)
- [Enviando PROOFPOINT toque em logs para o Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-proofpoint-tap-logs-to-azure-sentinel/ba-p/767727) (blog)


## <a name="connect-with-powershell"></a>Conectar-se com o PowerShell

O [script do PowerShell upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) permite que você use o PowerShell para transmitir eventos ou informações de contexto para o Azure Sentinel na linha de comando. Esse streaming cria efetivamente um conector personalizado entre sua fonte de dados e o Azure Sentinel.

Por exemplo, o script a seguir carrega um arquivo CSV para o Azure sentinela:

``` PowerShell
Import-Csv .\testcsv.csv
| .\Upload-AzMonitorLog.ps1
-WorkspaceId '69f7ec3e-cae3-458d-b4ea-6975385-6e426'
-WorkspaceKey $WSKey
-LogTypeName 'MyNewCSV'
-AddComputerName
-AdditionalDataTaggingName "MyAdditionalField"
-AdditionalDataTaggingValue "Foo"
```

O script de [script do PowerShell upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) usa os seguintes parâmetros:

|Parâmetro  |Descrição  |
|---------|---------|
|**WorkspaceId**     |   Sua ID do espaço de trabalho do Azure Sentinel, onde você armazenará seus dados.  [Localize sua ID e chave do espaço de trabalho](#find-your-workspace-id-and-key).  |
|**WorkspaceKey**     |   A chave primária ou secundária para o espaço de trabalho do Azure Sentinel em que você armazenará seus dados. [Localize sua ID e chave do espaço de trabalho](#find-your-workspace-id-and-key).  |
|**LogTypeName**     |    O nome da tabela de log Personalizada na qual você deseja armazenar os dados. Um sufixo de **_CL** será adicionado automaticamente ao final do nome da tabela.  |
|**Addcomputername**     |   Quando esse parâmetro existe, o script adiciona o nome do computador atual a cada registro de log, em um campo chamado **computador**.      |
|**TaggedAzureResourceId**     | Quando esse parâmetro existe, o script associa todos os registros de log carregados ao recurso do Azure especificado. <br><br>Essa associação habilita os registros de log carregados para consultas de contexto de recurso e adere ao controle de acesso baseado em função e centrado em recursos.       |
|**AdditionalDataTaggingName**     |      Quando esse parâmetro existe, o script adiciona outro campo a cada registro de log, com o nome configurado e o valor configurado para o parâmetro **AdditionalDataTaggingValue** . <br><br>Nesse caso, **AdditionalDataTaggingValue** não deve estar vazio. |
|**AdditionalDataTaggingValue**     |   Quando esse parâmetro existe, o script adiciona outro campo a cada registro de log, com o valor configurado e o nome do campo configurado para o parâmetro **AdditionalDataTaggingName** . <br><br>Se o parâmetro **AdditionalDataTaggingName** estiver vazio, mas um valor for configurado, o nome do campo padrão será **datatagging**.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>Localizar a ID e a chave do seu espaço de trabalho

Encontre os detalhes dos parâmetros **workspaceid** e **WorkspaceKey** no Azure sentinela:

1. No Azure Sentinel, selecione **configurações** à esquerda e, em seguida, selecione a guia **configurações do espaço de trabalho** .

1. Em introdução **ao log Analytics**  >  **1 conectar uma fonte de dados**, selecione **Gerenciamento de agentes do Windows e Linux**.

1. Localize a ID do espaço de trabalho, a chave primária e a chave secundária nas guias **servidores do Windows** .
## <a name="connect-with-the-log-analytics-api"></a>Conectar-se com a API de Log Analytics

Você pode transmitir eventos para o Azure Sentinel usando a API do coletor de dados Log Analytics para chamar um ponto de extremidade RESTful diretamente.

Embora chamar um ponto de extremidade RESTful diretamente exija mais programação, ele também fornece mais flexibilidade.

Para obter mais informações, consulte a [API do coletor de dados log Analytics](../azure-monitor/logs/data-collector-api.md), especialmente os exemplos a seguir:

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>Conectar-se com Azure Functions

Use Azure Functions junto com uma API RESTful e várias linguagens de codificação, como o [PowerShell](../azure-functions/functions-reference-powershell.md), para criar um conector personalizado sem servidor.

Para obter exemplos desse método, consulte:

- [Conectar seu ponto de extremidade de nuvem do VMware carbono preto padrão ao Azure Sentinel com o Azure function](connect-vmware-carbon-black.md)
- [Conectar o Sign-On único do Okta ao Azure Sentinel com o Azure function](connect-okta-single-sign-on.md)
- [Conectar seu proofpoint TAP ao Azure Sentinel com o Azure function](connect-proofpoint-tap.md)
- [Conectar sua VM Qualys ao Azure Sentinel com o Azure function](connect-qualys-vm.md)
- [Ingerindo XML, CSV ou outros formatos de dados](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Monitorando o zoom com o Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (blog)
- [Implantar um aplicativo de funções para obter dados da API de gerenciamento do Office 365 no Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) (Comunidade GitHub do Azure Sentinel)

## <a name="parse-your-custom-connector-data"></a>Analisar os dados do conector personalizado

Você pode usar a técnica de análise interna do conector personalizado para extrair as informações relevantes e preencher os campos relevantes no Azure Sentinel.

Por exemplo:

- **Se você usou o Logstash**, use o plug-in de filtro do [compreendo](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) para analisar seus dados.
- **Se você usou uma função do Azure**, analise seus dados com o código. Para obter mais informações, consulte [analisadores](normalization.md#parsers).

O Azure Sentinel dá suporte à análise no momento da consulta. A análise no momento da consulta permite que você envie dados por push no formato original e, em seguida, analise sob demanda, quando necessário.

A análise no momento da consulta também significa que você não precisa saber a estrutura exata de seus dados antecipadamente, quando cria seu conector personalizado ou até mesmo as informações que você precisará extrair. Em vez disso, analise seus dados a qualquer momento, mesmo durante uma investigação.

> [!NOTE]
> Atualizar o analisador também se aplica aos dados que você já ingeriu no Azure Sentinel.
> 
## <a name="next-steps"></a>Próximas etapas

Use os dados ingeridos no Azure Sentinel para proteger seu ambiente com qualquer um dos seguintes processos:

- [Obtenha visibilidade sobre os alertas](quickstart-get-visibility.md)
- [Visualize e monitore seus dados](tutorial-monitor-your-data.md)
- [Investigar incidentes](tutorial-investigate-cases.md)
- [Detectar ameaças](tutorial-detect-threats-built-in.md)
- [Automatizar prevenção contra ameaças](tutorial-respond-threats-playbook.md)
- [Procurar ameaças](hunting.md)