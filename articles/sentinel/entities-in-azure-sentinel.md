---
title: Usar entidades para classificar e analisar dados no Azure Sentinel | Microsoft Docs
description: Atribua classificações de entidade (usuários, nomes de host, endereços IP) a itens de dados no Azure Sentinel e use-os para comparar, analisar e correlacionar dados de várias fontes.
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
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456204"
---
# <a name="classify-and-analyze-data-using-entities-in-azure-sentinel"></a>Classificar e analisar dados usando entidades no Azure Sentinel

## <a name="what-are-entities"></a>O que são entidades?

Quando os alertas são enviados ou gerados pelo Azure Sentinel, eles contêm itens de dados que o sentinela pode reconhecer e classificar em categorias como **entidades**. Quando o Azure Sentinel compreende que tipo de entidade um determinado item de dados representa, ele sabe as perguntas certas a serem feitas sobre ele e, em seguida, pode comparar informações sobre esse item em toda a gama de fontes de dados e rastreá-la facilmente e consultá-la em toda a experiência do Sentinela – análise, investigação, correção, busca e assim por diante. Alguns exemplos comuns de entidades são usuários, hosts, arquivos, processos, endereços IP e URLs.

### <a name="entity-identifiers"></a>Identificadores de entidade

O Azure Sentinel dá suporte a uma ampla variedade de tipos de entidade. Cada tipo tem seus próprios atributos exclusivos, incluindo alguns que podem ser usados para identificar uma entidade específica. Esses atributos são representados como campos na entidade e são chamados de **identificadores**. Consulte a lista completa de entidades com suporte e seus identificadores abaixo.

#### <a name="strong-and-weak-identifiers"></a>Identificadores fortes e fracos

Conforme observado acima, para cada tipo de entidade, há campos, ou conjuntos de campos, que podem identificá-lo. Esses campos ou conjuntos de campos podem ser chamados de **identificadores fortes** se puderem identificar exclusivamente uma entidade sem nenhuma ambiguidade ou como **identificadores fracos** se puderem identificar uma entidade em algumas circunstâncias, mas não houver garantia de identificar exclusivamente uma entidade em todos os casos. Em muitos casos, no entanto, uma seleção de identificadores fracos pode ser combinada para produzir um identificador forte.

Por exemplo, as contas de usuário podem ser identificadas como entidades de **conta** de mais de uma maneira: usando um único **identificador forte** , como um identificador numérico da conta do Azure AD (o campo **GUID** ) ou seu valor **UPN (nome principal do usuário)** ou, como alternativa, usando uma combinação de **identificadores fracos** como seus campos **Name** e **domínio do NT** . Diferentes fontes de dados podem identificar o mesmo usuário de diferentes maneiras. Sempre que o Azure Sentinel encontra duas entidades que ele pode reconhecer como a mesma entidade com base em seus identificadores, ele mescla as duas entidades em uma única entidade, para que possa ser manipulado de forma adequada e consistente.

No entanto, se um de seus provedores de recursos criar um alerta no qual uma entidade não está suficientemente identificada, por exemplo, usando apenas um único **identificador fraco** , como um nome de usuário sem o contexto do nome de domínio, a entidade do usuário não poderá ser mesclada com outras instâncias da mesma conta de usuário. Essas outras instâncias seriam identificadas como uma entidade separada, e essas duas entidades permanecerão separadas em vez de unificadas.

Para minimizar o risco de isso acontecer, você deve verificar se todos os seus provedores de alertas identificam corretamente as entidades nos alertas que eles produzem. Além disso, a sincronização de entidades de conta de usuário com Azure Active Directory pode criar um diretório unificado, que poderá mesclar entidades de conta de usuário.

#### <a name="supported-entities"></a>Entidades com suporte

Os seguintes tipos de entidades estão atualmente identificados no Azure sentinela:

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
- Cluster de emails
- Mensagem de email
- Email de envio

Você pode exibir os identificadores dessas entidades e outras informações relevantes na [referência de entidades](entities-reference.md).

## <a name="entity-mapping"></a>Mapeamento de entidade

Como o Azure Sentinel reconhece uma parte dos dados em um alerta como identificação de uma entidade?

Vejamos como o processamento de dados é feito no Azure Sentinel. Os dados são ingeridos de várias fontes por meio de [conectores](connect-data-sources.md), seja o serviço a serviço, baseado em agente ou usando um serviço de syslog e um encaminhador de log. Os dados são armazenados em tabelas em seu espaço de trabalho do Log Analytics. Em seguida, essas tabelas são consultadas em intervalos agendados regularmente pelas regras de análise definidas e habilitadas. Uma das muitas ações tomadas por essas regras de análise é o mapeamento de campos de dados nas tabelas para entidades reconhecidas pelo sentinela do Azure. De acordo com os mapeamentos que você definir em suas regras de análise, o Azure Sentinel pegará campos dos resultados retornados por sua consulta, os reconhecerá pelos identificadores especificados para cada tipo de entidade e se aplicará a eles o tipo de entidade identificado por esses identificadores.

Qual é o ponto de tudo isso?

Quando o Sentinela do Azure é capaz de identificar entidades em alertas de diferentes tipos de fontes de dados e, especialmente, se puder fazer isso usando identificadores fortes comuns a cada fonte de dados ou a um terceiro esquema, ele poderá correlacionar facilmente entre todos esses alertas e fontes de dados. Essas correlações ajudam a criar um armazenamento avançado de informações e ideias sobre as entidades, fornecendo a você uma base sólida para suas operações de segurança.

Saiba como [mapear campos de dados para entidades](map-data-fields-to-entities.md).

Saiba [quais identificadores identificam fortemente uma entidade](entities-reference.md).

## <a name="entity-pages"></a>Páginas de entidade

Quando você encontra qualquer entidade (atualmente limitada a usuários e hosts) em uma pesquisa, um alerta ou uma investigação, você pode selecionar a entidade e ser levado a uma **página de entidade**, uma folha de dados cheia de informações úteis sobre essa entidade. Os tipos de informações que você encontrará nesta página incluem fatos básicos sobre a entidade, uma linha do tempo de eventos notáveis relacionados a essa entidade e informações sobre o comportamento da entidade.

As páginas de entidade consistem em três partes:

- O painel do lado esquerdo contém as informações de identificação da entidade, coletadas de fontes de dados como Azure Active Directory, Azure Monitor, central de segurança do Azure e Microsoft defender.

- O painel central mostra uma linha do tempo gráfica e textual de eventos notáveis relacionados à entidade, como alertas, indicadores e atividades. As atividades são agregações de eventos notáveis de Log Analytics. As consultas que detectam essas atividades são desenvolvidas pelas equipes de pesquisa de segurança da Microsoft.

- O painel do lado direito apresenta informações comportamentais sobre a entidade. Essas informações ajudam a identificar rapidamente as anomalias e as ameaças à segurança. As informações são desenvolvidas pelas equipes de pesquisa de segurança da Microsoft e são baseadas em modelos de detecção de anomalias.

### <a name="the-timeline"></a>A linha do tempo

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Linha do tempo das páginas da entidade":::

A linha do tempo é uma parte importante da contribuição da página de entidade para análise de comportamento no Azure Sentinel. Ele apresenta uma história sobre eventos relacionados a entidades, ajudando você a entender a atividade da entidade em um período de tempo específico.

Você pode escolher o **intervalo de tempo** entre várias opções predefinidas (como *últimas 24 horas*) ou defini-las para qualquer período de tempo definido personalizado. Além disso, você pode definir filtros que limitam as informações na linha do tempo a tipos específicos de eventos ou alertas.

Os seguintes tipos de itens estão incluídos na linha do tempo:

- Alertas-todos os alertas nos quais a entidade é definida como uma **entidade mapeada**. Observe que, se a sua organização tiver criado [alertas personalizados usando regras de análise](./tutorial-detect-threats-custom.md), você deverá certificar-se de que o mapeamento de entidade das regras seja feito corretamente.

- Indicadores-todos os indicadores que incluem a entidade específica mostrada na página.

- Atividades – agregação de eventos notáveis relacionados à entidade.

### <a name="entity-insights"></a>Informações de entidade

As informações de entidade são consultas definidas pelos pesquisadores de segurança da Microsoft para ajudar seus analistas a investigar de forma mais eficiente e eficaz. Os insights são apresentados como parte da página de entidade e fornecem informações de segurança valiosas sobre hosts e usuários, na forma de gráficos e dados tabulares. Ter as informações aqui significa que você não precisa desvio a Log Analytics. As informações incluem dados relacionados a entradas, adições de grupo, eventos anormais e muito mais, e incluem algoritmos de ML avançados para detectar o comportamento anormal.

As informações são baseadas nas seguintes fontes de dados:

- Syslog (Linux)
- SecurityEvent (Windows)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (Azure Sentinel UEBA)
- Pulsação (agente de Azure Monitor)
- CommonSecurityLog (Sentinela do Azure)

### <a name="how-to-use-entity-pages"></a>Como usar páginas de entidade

As páginas de entidade são projetadas para fazer parte de vários cenários de uso e podem ser acessadas do gerenciamento de incidentes, do grafo de investigação, de indicadores ou diretamente da página de pesquisa de entidade em **análise de comportamento de entidade** no menu principal do Sentinela do Azure.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Casos de uso de página de entidade":::

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a trabalhar com entidades no Azure Sentinel. Para obter diretrizes práticas sobre a implementação e para usar as informações obtidas, consulte os seguintes artigos:

- [Habilite a análise de comportamento de entidade](./enable-entity-behavior-analytics.md) no Azure Sentinel.
- [Procure ameaças à segurança](./hunting.md).
