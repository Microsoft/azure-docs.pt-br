---
title: Identificar ameaças avançadas com UEBA (análise de comportamentos de usuário e entidade) no Azure Sentinel | Microsoft Docs
description: Crie linhas de base comportamentais para entidades (usuários, nomes de host, endereços IP) e use-as para detectar comportamento anormal e identificar as ameaças persistentes avançadas de dia zero (APT).
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
ms.date: 01/25/2021
ms.author: yelevin
ms.openlocfilehash: 458c801e1434832bf65da669ca89cb5c5eebe2e8
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807556"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Identificar ameaças avançadas com UEBA (análise de comportamentos de usuário e entidade) no Azure Sentinel

> [!IMPORTANT]
>
> - Os recursos de páginas de UEBA e entidade agora estão em **disponibilidade geral** em **_todas as_** regiões e geografias do Azure Sentinel.

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>O que é análise de comportamento de usuário e entidade (UEBA)?

### <a name="the-concept"></a>O conceito

Identificar ameaças dentro de sua organização e seu impacto potencial – seja uma entidade comprometida ou um insider mal-intencionado, sempre foi um processo demorado e trabalhoso. Fazer buscas de alertas, conectar os pontos e buscar ativos se somar a grandes quantidades de tempo e esforço investidos com retornos mínimos e a possibilidade de ameaças sofisticadas simplesmente escaparem da descoberta. Particularmente, ameaças indesejadas, como as ameaças persistentes de dia zero, direcionadas e avançadas, podem ser as mais perigosas para sua organização, tornando sua detecção mais crítica.

O recurso UEBA no Azure Sentinel elimina o trabalho enfadonho das cargas de trabalho dos analistas e as incertezas de seus esforços, além de fornecer inteligência acionável e de alta fidelidade, para que eles possam se concentrar na investigação e na correção.

Como o Azure Sentinel coleta logs e alertas de todas as suas fontes de dados conectadas, ele os analisa e cria perfis comportamentais de linha de base das entidades da sua organização (como usuários, hosts, endereços IP e aplicativos) ao longo do tempo e do horizonte do grupo de sistemas pares. Usando uma variedade de técnicas e recursos de aprendizado de máquina, o Azure Sentinel pode identificar a atividade anômala e ajudá-lo a determinar se um ativo foi comprometido. Não só isso, mas também pode descobrir a sensibilidade relativa de ativos específicos, identificar grupos de ativos de mesmo nível e avaliar o impacto potencial de qualquer ativo comprometido (seu "raio de transmissão"). Munido dessas informações, você pode priorizar efetivamente a investigação e o tratamento de incidentes. 

### <a name="architecture-overview"></a>Visão geral da arquitetura

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="Arquitetura de análise de comportamento de entidade":::

### <a name="security-driven-analytics"></a>Análise orientada por segurança

Inspirado pelo paradigma da Gartner para soluções UEBAs, o Azure Sentinel fornece uma abordagem "externa", com base em três quadros de referência:

- **Casos de uso:** Com a priorização de vetores de ataque e cenários relevantes com base na pesquisa de segurança alinhada com a estrutura MITRE ATT&CK de táticas, técnicas e subtécnicas que coloca várias entidades como vítimas, criminosos ou pontos dinâmicos na cadeia de eliminação; O Azure Sentinel se concentra especificamente nos logs mais valiosos que cada fonte de dados pode fornecer.

- **Fontes de dados:** Embora o suporte às fontes de dados do Azure seja o mais importante, o Azure Sentinel seleciona fontes de dados de terceiros para fornecer dados que correspondam aos nossos cenários de ameaça.

- **Análise:** Usando vários algoritmos de ML (aprendizado de máquina), o Azure Sentinel identifica atividades anormais e apresenta evidências claramente e concisos na forma de aprimoramentos contextuais, alguns exemplos dos quais aparecem abaixo.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="Abordagem de análise de comportamento fora de entrada":::

O Azure Sentinel apresenta artefatos que ajudam os analistas de segurança a obter uma compreensão clara das atividades anormais no contexto e em comparação com o perfil de linha de base do usuário. As ações executadas por um usuário (ou um host ou um endereço) são avaliadas contextualmente, em que um resultado "verdadeiro" indica uma anomalia identificada:
- em locais geográficos, dispositivos e ambientes.
- em horizontes de tempo e frequência (em comparação com o próprio histórico do usuário).
- em comparação com o comportamento de pares.
- em comparação com o comportamento da organização.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="Contexto de entidade":::


### <a name="scoring"></a>Pontuação

Cada atividade é pontuada com "Pontuação de prioridade de investigação" – que determinam a probabilidade de um usuário específico executar uma atividade específica, com base no aprendizado comportamental do usuário e seus colegas. As atividades identificadas como mais anormais recebem as pontuações mais altas (em uma escala de 0-10).

Veja como a análise de comportamento é usada em [Microsoft Cloud app Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) para obter um exemplo de como isso funciona.

## <a name="entities-in-azure-sentinel"></a>Entidades no Azure Sentinel

### <a name="entity-identifiers"></a>Identificadores de entidade

Quando os alertas são enviados para o Azure Sentinel, eles incluem elementos de dados que o Azure Sentinel identifica e classifica como entidades, como contas de usuário, hosts, endereços IP e outros. Na ocasião, essa identificação pode ser um desafio, se o alerta não contiver informações suficientes sobre a entidade.

Por exemplo, as contas de usuário podem ser identificadas de mais de uma maneira: usando um identificador numérico (GUID) da conta do Azure AD ou seu valor de nome principal do usuário (UPN) ou, como alternativa, usando uma combinação de seu nome de usuário e seu domínio do NT. Diferentes fontes de dados podem identificar o mesmo usuário de diferentes maneiras. Portanto, sempre que possível, o Azure Sentinel mescla esses identificadores em uma única entidade, para que possa ser identificado corretamente.

No entanto, pode acontecer que um de seus provedores de recursos crie um alerta no qual uma entidade não está suficientemente identificada; por exemplo, um nome de usuário sem o contexto do nome de domínio. Nesse caso, a entidade de usuário não pode ser mesclada com outras instâncias da mesma conta de usuário, que seria identificada como uma entidade separada, e essas duas entidades permanecerão separadas em vez de unificadas.

Para minimizar o risco de isso acontecer, você deve verificar se todos os seus provedores de alertas identificam corretamente as entidades nos alertas que eles produzem. Além disso, a sincronização de entidades de conta de usuário com Azure Active Directory pode criar um diretório unificado, que poderá mesclar entidades de conta de usuário.

Os seguintes tipos de entidades estão atualmente identificados no Azure sentinela:

- Conta de usuário (conta)
- Host
- Endereço IP (IP)
- Malware
- Arquivo
- Processar
- Aplicativo de nuvem (CloudApplication)
- Nome de domínio (DNS)
- Recursos do Azure
- Arquivo (FileHash)
- Chave do Registro
- Valor do Registro
- Grupo de segurança
- URL
- Dispositivo IoT
- Mailbox
- Cluster de emails
- Mensagem de email
- Email de envio

### <a name="entity-pages"></a>Páginas de entidade

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
 
As informações de entidade são consultas definidas pelos pesquisadores de segurança da Microsoft para ajudar seus analistas a investigar de forma mais eficiente e eficaz. Os insights são apresentados como parte da página de entidade e fornecem informações de segurança valiosas sobre hosts e usuários, na forma de gráficos e dados tabulares. Ter as informações aqui significa que você não precisa desvio a Log Analytics. As informações incluem dados relacionados a entradas, adições de grupo, eventos anormais e muito mais, e incluem algoritmos de ML avançados para detectar o comportamento anormal. As informações são baseadas nos seguintes tipos de dados:
- syslog
- SecurityEvent
- Logs de Auditoria
- Logs de entrada
- Atividade do Office
- BehaviorAnalytics (UEBA) 
 
### <a name="how-to-use-entity-pages"></a>Como usar páginas de entidade

As páginas de entidade são projetadas para fazer parte de vários cenários de uso e podem ser acessadas do gerenciamento de incidentes, do grafo de investigação, de indicadores ou diretamente da página de pesquisa de entidade em **análise de comportamento de entidade** no menu principal do Sentinela do Azure.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Casos de uso de página de entidade":::


## <a name="data-schema"></a>Esquema de dados

### <a name="behavior-analytics-table"></a>Tabela de análise de comportamento

| Campo                     | Descrição                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | número de ID exclusivo do locatário                                      |
| SourceRecordId            | número de ID exclusivo do evento do EBA                                   |
| TimeGenerated             | carimbo de data/hora da ocorrência da atividade                              |
| Timeprocessado             | carimbo de data/hora do processamento da atividade pelo mecanismo do EBA            |
| ActivityType              | Categoria de alto nível da atividade                                 |
| ActionType                | nome normalizado da atividade                                     |
| UserName                  | nome do usuário que iniciou a atividade                    |
| UserPrincipalName         | username completo do usuário que iniciou a atividade               |
| EventSource               | fonte de dados que forneceu o evento original                        |
| SourceIPAddress           | Endereço IP do qual a atividade foi iniciada                        |
| SourceIPLocation          | país a partir do qual a atividade foi iniciada, aprimorada do endereço IP |
| SourceDevice              | nome do host do dispositivo que iniciou a atividade                  |
| DestinationIPAddress      | Endereço IP do destino da atividade                            |
| DestinationIPLocation     | país do destino da atividade, aprimorado do endereço IP     |
| DestinationDevice         | nome do dispositivo de destino                                           |
| **UsersInsights**         | aprimoramentos contextuais de usuários envolvidos                            |
| **DevicesInsights**       | aprimoramentos contextuais de dispositivos envolvidos                          |
| **ActivityInsights**      | análise contextual de atividade com base em nossa criação de perfil              |
| **InvestigationPriority** | Pontuação de anomalias, entre 0-10 (0 = benigno, 10 = altamente anômala)         |
|

Você pode ver o conjunto completo de aprimoramentos contextuais referenciados em **UsersInsights**, **DevicesInsights** e **ActivityInsights** no documento de [referência de aprimoramentos do Ueba](ueba-enrichments.md).

### <a name="querying-behavior-analytics-data"></a>Consultando dados de análise de comportamento

Usando o [KQL](/azure/data-explorer/kusto/query/), é possível consultar a tabela de análise comportamental.

Por exemplo, se quisermos localizar todos os casos de um usuário que não conseguiu entrar em um recurso do Azure, em que era a primeira tentativa do usuário de se conectar de um determinado país, e as conexões desse país não são comuns mesmo para os pares do usuário, podemos usar a seguinte consulta:

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>Metadados de pares de usuário-tabela e notebook

Os metadados de pares de usuários fornecem um contexto importante em detecções de ameaças, na investigação de um incidente e na busca de uma ameaça em potencial. Os analistas de segurança podem observar as atividades normais dos pares de um usuário para determinar se as atividades do usuário são incomuns em comparação com aquelas de seus colegas.

O Azure Sentinel calcula e classifica os pares de um usuário, com base na associação do grupo de segurança do Azure AD, na lista de endereçamento, no et etc e armazena os pares classificados como 1-20 na tabela **UserPeerAnalytics** . A captura de tela abaixo mostra o esquema da tabela UserPeerAnalytics e exibe os oito principais pares classificados do usuário Kendall Collins. O Azure Sentinel usa o algoritmo Frequency-IDF ( *frequência de documentos inversos* ) para normalizar a ponderação para calcular a classificação: quanto menor o grupo, maior o peso. 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="Captura de tela da tabela de metadados de pares do usuário":::

Você pode usar o [Notebook Jupyter](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) fornecido no repositório GitHub do Azure Sentinel para visualizar os metadados de pares do usuário. Para obter instruções detalhadas sobre como usar o bloco de anotações, consulte [análise guiada-notebook de metadados de segurança do usuário](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) .

### <a name="permission-analytics---table-and-notebook"></a>Análise de permissão-tabela e bloco de anotações

A análise de permissões ajuda a determinar o impacto potencial da comprometente de um ativo organizacional por um invasor. Esse impacto também é conhecido como "raio de transmissão" do ativo. Os analistas de segurança podem usar essas informações para priorizar investigações e tratamento de incidentes.

O Azure Sentinel determina os direitos de acesso direto e transitório mantidos por um determinado usuário aos recursos do Azure, avaliando as assinaturas do Azure que o usuário pode acessar diretamente ou por meio de grupos ou entidades de serviço. Essas informações, bem como a lista completa da associação do grupo de segurança do Azure AD do usuário, são armazenadas na tabela **UserAccessAnalytics** . A captura de tela abaixo mostra uma linha de exemplo na tabela UserAccessAnalytics para o usuário Alex Johnson. A **entidade de origem** é a conta de usuário ou entidade de serviço, e a entidade de **destino** é o recurso ao qual a entidade de origem tem acesso. Os valores de **nível de acesso** e **tipo de acesso** dependem do modelo de controle de acesso da entidade de destino. Você pode ver que Alex tem acesso de colaborador ao locatário de *hotéis da Contoso* da assinatura do Azure. O modelo de controle de acesso da assinatura é o RBAC do Azure.   

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="Captura de tela da tabela de análise de acesso do usuário":::

Você pode usar o [Notebook Jupyter](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) (o mesmo bloco de anotações mencionado acima) do repositório GitHub do Azure Sentinel para visualizar os dados de análise de permissão. Para obter instruções detalhadas sobre como usar o bloco de anotações, consulte [análise guiada-notebook de metadados de segurança do usuário](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) .

### <a name="hunting-queries-and-exploration-queries"></a>Procurando consultas e consultas de exploração

O Azure Sentinel fornece um conjunto pronto para uso de consultas buscadas, consultas de exploração e uma pasta de trabalho, com base na tabela BehaviorAnalytics. Essas ferramentas apresentam dados aprimorados, concentrados em casos de uso específicos, que indicam comportamento anormal. 

Saiba mais sobre [a busca e o grafo de investigação](./hunting.md) no Azure Sentinel.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu sobre os recursos de análise de comportamento da entidade do Azure Sentinel. Para obter diretrizes práticas sobre a implementação e para usar as informações obtidas, consulte os seguintes artigos:

- [Habilite a análise de comportamento de entidade](./enable-entity-behavior-analytics.md) no Azure Sentinel.
- [Procure ameaças à segurança](./hunting.md).