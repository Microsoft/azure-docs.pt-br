---
title: Usar entidades para classificar e analisar dados no Azure Sentinel | Microsoft Docs
description: Atribua classificações de entidade (usuários, nomes de host e endereços IP) a itens de dados no Azure Sentinel e use-os para comparar, analisar e correlacionar dados de várias fontes.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 43da1af7a3001d7f8e000a878948428a3d63aa4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456204"
---
# <a name="classify-and-analyze-data-using-entities-in-azure-sentinel"></a>Classificar e analisar dados usando entidades no Azure Sentinel

## <a name="what-are-entities"></a>O que são entidades?

Quando os alertas são enviados ou gerados pelo Azure Sentinel, eles contêm itens de dados que o Sentinel pode reconhecer e classificar em categorias como **entidades**. Quando o Azure Sentinel compreende que tipo de entidade um determinado item de dados representa, ele sabe as perguntas certas a serem feitas sobre isso e, depois, pode comparar insights sobre esse item em toda a gama de fontes de dados e rastreá-la com facilidade e consultá-la em toda a experiência do Sentinel – análise, investigação, correção, busca e assim por diante. Alguns exemplos comuns de entidades são usuários, hosts, arquivos, processos, endereços IP e URLs.

### <a name="entity-identifiers"></a>Identificadores de entidade

O Azure Sentinel dá suporte a uma ampla variedade de tipos de entidade. Cada tipo tem atributos exclusivos próprios, incluindo alguns que podem ser usados para identificar uma entidade em particular. Esses atributos são representados como campos na entidade e são chamados de **identificadores**. Confira abaixo a lista completa de entidades com suporte e seus identificadores.

#### <a name="strong-and-weak-identifiers"></a>Identificadores fortes e fracos

Conforme observado logo acima, para cada tipo de entidade, há campos (ou conjuntos de campos) que podem identificá-lo. Esses campos (ou conjuntos de campos) podem ser chamados de **identificadores fortes**, se puderem identificar exclusivamente uma entidade sem nenhuma ambiguidade, ou de **identificadores fracos**, se puderem identificar uma entidade em algumas circunstâncias, mas não houver garantia de identificar exclusivamente uma entidade em todos os casos possíveis. Em muitos casos, no entanto, uma seleção de identificadores fracos pode ser combinada para produzir um identificador forte.

Por exemplo, as contas de usuário podem ser identificadas como entidades de **conta** de mais de uma forma: usando apenas um **identificador forte**, como um identificador numérico de conta do Azure AD (o campo **GUID**) ou seu valor **UPN (nome UPN)** ou, como alternativa, usando uma combinação de **identificadores fracos** como os campos **Nome** e **NTDomain**. Fontes de dados diferentes podem identificar o mesmo usuário de maneiras diferentes. Sempre que o Azure Sentinel encontra duas entidades que ele pode reconhecer como a mesma entidade com base em seus identificadores, ele mescla as duas entidades em apenas uma, para que possa ser gerenciada de maneira adequada e consistente.

No entanto, se um de seus provedores de recursos criar um alerta no qual uma entidade não está suficientemente identificada – por exemplo, usando apenas um **identificador fraco**, como um nome de usuário sem o contexto do nome de domínio – então a entidade do usuário não poderá ser mesclada com outras instâncias da mesma conta de usuário. Essas outras instâncias seriam identificadas como uma entidade separada e essas duas entidades permaneceriam separadas em vez de unificadas.

Para minimizar o risco dessa ocorrência, você deve verificar se todos os provedores de alertas identificam corretamente as entidades nos alertas produzidos. Além disso, a sincronização de entidades de conta de usuário com o Azure Active Directory pode criar um diretório unificado, que pode mesclar entidades de conta de usuário.

#### <a name="supported-entities"></a>Entidades com suporte

Os seguintes tipos de entidades estão identificados no Azure sentinela atualmente:

- Conta de usuário
- Host
- Endereço IP
- Malware
- Arquivo
- Processo
- Aplicativo de nuvem
- Nome de domínio
- Recursos do Azure
- Hash do arquivo
- Chave do Registro
- Valor do Registro
- Grupo de segurança
- URL
- Dispositivo IoT
- Mailbox
- Cluster de e-mail
- Mensagem de e-mail
- E-mail de envio

Você pode ver os identificadores dessas entidades e outras informações relevantes na [referência das entidades](entities-reference.md).

## <a name="entity-mapping"></a>Mapeamento de entidade

Como o Azure Sentinel reconhece uma parte dos dados de um alerta como a identificação de uma entidade?

Vejamos como o processamento de dados é feito no Azure Sentinel. Os dados são ingeridos de várias fontes por meio de [conectores](connect-data-sources.md), seja de serviço para serviço, baseado em agente ou usando um serviço de syslog e um encaminhador de logs. Os dados são armazenados em tabelas no seu workspace do Log Analytics. Em seguida, essas tabelas são consultadas em intervalos agendados regularmente pelas regras de análise definidas e habilitadas por você. Uma das muitas ações tomadas por essas regras de análise é o mapeamento de campos de dados nas tabelas para entidades reconhecidas pelo Azure Sentinel. De acordo com os mapeamentos que você definir nas regras de análise, o Azure Sentinel pegará campos dos resultados retornados pela consulta, reconhecerá esses campos por meio dos identificadores que você especificou para cada tipo de entidade e aplicará aos campos o tipo de entidade identificado por esses identificadores.

Qual é o objetivo de tudo isso?

Quando o Azure Sentinel for capaz de identificar entidades em alertas de diferentes tipos de fontes de dados e, especialmente, se ele puder fazer isso usando identificadores fortes comuns a cada fonte de dados ou a um terceiro esquema, ele poderá se correlacionar com facilidade entre todos esses alertas e essas fontes de dados. Essas correlações ajudam a criar um armazenamento avançado de informações e insights sobre as entidades, fornecendo a você uma base sólida para as operações de segurança.

Saiba como [mapear campos de dados para as entidades](map-data-fields-to-entities.md).

Saiba [quais identificadores identificam fortemente uma entidade](entities-reference.md).

## <a name="entity-pages"></a>Páginas de entidade

Quando você encontra qualquer entidade (atualmente limitada a usuários e hosts) em uma pesquisa, um alerta ou uma investigação, você pode selecionar a entidade e ser levado a uma **página de entidade**, uma folha de dados cheia de informações úteis sobre essa entidade. Os tipos de informações encontradas nesta página incluem fatos básicos sobre a entidade, uma linha do tempo de eventos notáveis relacionados a essa entidade e informações sobre o comportamento da entidade.

As páginas de entidade consistem em três partes:

- O painel do lado esquerdo contém as informações de identificação da entidade, coletadas de fontes de dados como Azure Active Directory, Azure Monitor, Central de Segurança do Azure e Microsoft Defender.

- O painel central mostra uma linha do tempo gráfica e textual de eventos notáveis relacionados à entidade, como alertas, indicadores e atividades. As atividades são agregações de eventos notáveis do Log Analytics. As consultas que detectam essas atividades são desenvolvidas pelas equipes de pesquisa de segurança da Microsoft.

- O painel do lado direito apresenta informações comportamentais sobre a entidade. Essas informações ajudam a identificar rapidamente as anomalias e ameaças à segurança. As informações são desenvolvidas pelas equipes de pesquisa de segurança da Microsoft e baseadas em modelos de detecção de anomalias.

### <a name="the-timeline"></a>A linha do tempo

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Linha do tempo das páginas da entidade":::

A linha do tempo é uma parte importante da contribuição da página de entidade com a análise de comportamento no Azure Sentinel. Apresenta uma história sobre os eventos relacionados a entidades, ajudando você a entender a atividade da entidade em um período específico.

Você pode escolher o **intervalo de tempo** entre várias opções predefinidas (como *nas últimas 24 horas*) ou defini-lo como qualquer período personalizado. Além disso, você pode definir filtros que limitam as informações na linha do tempo a tipos específicos de eventos ou alertas.

Os seguintes tipos de itens estão incluídos na linha do tempo:

- Alertas – todos os alertas em que a entidade é definida como uma **entidade mapeada**. Observe que, se a organização criou [alertas personalizados usando as regras de análise](./tutorial-detect-threats-custom.md), você deve verificar se o mapeamento de entidade das regras foi feito corretamente.

- Indicadores – todos os indicadores que incluem a entidade específica mostrada na página.

- Atividades – agregação de eventos notáveis relacionados à entidade.

### <a name="entity-insights"></a>Insights de entidade

Os insights de entidade são consultas definidas pelos pesquisadores de segurança da Microsoft para ajudar os analistas a investigar de forma mais eficiente e eficaz. Os insights são apresentados como parte da página de entidade e fornecem informações de segurança importantes sobre hosts e usuários, na forma de dados de tabela e gráficos. Ter as informações aqui significa que você não precisa desviar para o Log Analytics. Os insights incluem dados relacionados a entradas, adições de grupo, eventos anômalos e muito mais, e incluem algoritmos avançados de ML para detectar comportamentos anômalos.

Os insights são baseados nas seguintes fontes de dados:

- Syslog (Linux)
- SecurityEvent (Windows)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (Azure Sentinel UEBA)
- Heartbeat (agente do Azure Monitor)
- CommonSecurityLog (Azure Sentinel)

### <a name="how-to-use-entity-pages"></a>Como usar as páginas de entidade

As páginas de entidade são criadas para fazer parte de vários cenários de uso e podem ser acessadas por meio de gerenciamento de incidentes, grafo de investigação, indicadores ou diretamente na página de pesquisa de entidade em **Análise de comportamento de entidades** no menu principal do Sentinela do Azure.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Casos de uso da página da entidade":::

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu sobre como trabalhar com entidades no Azure Sentinel. Para obter diretrizes práticas sobre a implementação e para usar os insights obtidos, confira os seguintes artigos:

- [Habilitar a análise de comportamento de entidades](./enable-entity-behavior-analytics.md) no Azure Sentinel.
- [Buscar por ameaças de segurança](./hunting.md).
