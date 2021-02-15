---
title: Normalização de dados no Azure Sentinel | Microsoft Docs
description: Este artigo explica como o Azure Sentinel normaliza os dados de várias fontes diferentes e detalha o esquema de normalização.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 3d9e436d636fbd5414367efb0e122748a8e9e2cb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390803"
---
# <a name="normalization-in-azure-sentinel"></a>Normalização no Azure Sentinel

Este artigo explica a normalização do esquema de dados no Azure Sentinel e, especificamente, o esquema de conexões e sessões de rede, ao qual um link é incluído.

## <a name="what-is-normalization"></a>O que é normalização

Trabalhar com vários tipos de dados e tabelas juntos apresenta desafios. Você deve se familiarizar com muitos tipos de dados e esquemas diferentes, ter que escrever e usar um conjunto exclusivo de regras de análise, pastas de trabalho e consultas de busca para cada um, mesmo para aqueles que compartilham semelhanças (por exemplo, pertencentes a dispositivos de firewall). A correlação entre os diferentes tipos de dados, necessária para investigação e busca, também é difícil. O Azure Sentinel fornece uma experiência simplificada para lidar com dados de várias fontes em exibições uniformes e normalizadas.

O modelo de **informações comuns** do Azure Sentinel consiste em três aspectos:

- Os **esquemas normalizados** abrangem conjuntos comuns de tipos de eventos previsíveis (tabelas) que são fáceis de trabalhar com e para criar funcionalidades unificadas (por exemplo, tabela de rede). O esquema também inclui uma Convenção de colunas normalizadas e definições para padronização de valor e formato (representação padrão consistente de dados, como endereços IP).

- Os **analisadores** mapeiam dados existentes de tipos diferentes para o esquema normalizado. De acordo com o modelo, os dados podem ser analisados para o esquema normalizado no tempo de consulta (usando funções) ou tempo de ingestão. Neste momento, somente a análise de tempo de consulta tem suporte.

- O **conteúdo de cada esquema normalizado** inclui regras de análise, pastas de trabalho interativas, consultas de busca e conteúdo adicional.

### <a name="current-release"></a>Versão atual

Com esta versão, o [esquema de sessões e conexões de rede normalizadas](./normalization-schema.md) (v 1.0.0) está disponível para visualização pública. O esquema descreve conexões de rede ou sessões como aquelas registradas por firewalls, dados de transmissão, NSG, NetFlow, sistemas de proxy e gateways de segurança da Web.  Analisadores de tempo de consulta selecionados e regras de análise estão disponíveis junto com o esquema e fazem uso dele.

No momento, o esquema está disponível somente por meio de analisadores de tempo de consulta (consulte a seção analisadores).

Você pode analisar dados para representações adicionais e usar o [modelo de nomenclatura de entidades OSSEM](https://ossemproject.com/cdm/entities/intro.html#) para criar colunas que serão consistentes com tabelas normalizadas existentes e futuras.

## <a name="normalized-schema-and-parsing"></a>Esquema normalizado e análise

### <a name="how-our-normalized-schemas-are-defined"></a>Como nossos esquemas normalizados são definidos

O Azure Sentinel está se alinhando com o modelo de informações comuns de [OSSEM (metadados de eventos de segurança](https://ossemproject.com/intro.html) de software livre), permitindo a correlação de entidades previsíveis em tabelas normalizadas. OSSEM é um projeto ministrado pela Comunidade que se concentra principalmente na documentação e na padronização dos logs de eventos de segurança de diversas fontes de dados e sistemas operacionais. Além disso, o projeto fornece um modelo de informações comuns (CIM) que pode ser usado para engenheiros de dados durante procedimentos de normalização de dados para permitir que analistas de segurança consultem e analisem dados em diversas fontes de dados.

O [OSSEM CIM](https://ossemproject.com/cdm/intro.html) define um conjunto de entidades (por exemplo: arquivo, host, IP, processo) e define um conjunto de atributos para cada entidade. Além disso, o CIM define um conjunto de tabelas (por exemplo, tabela de [sessão de rede](https://ossemproject.com/cdm/tables/network_session.html) ) que usam atributos relevantes dessas entidades, permitindo uma correlação direta e previsível. Observe que as entidades podem ser aninhadas (por exemplo, a entidade de origem pode conter uma entidade de arquivo que terá um atributo de nome).

Para saber mais sobre a estrutura de entidade do OSSEM, visite a [referência oficial do OSSEM](https://ossemproject.com/cdm/guidelines/entity_structure.html).

### <a name="how-the-normalized-schemas-are-implemented-in-azure-sentinel"></a>Como os esquemas normalizados são implementados no Azure Sentinel

Na implementação do CIM OSSEM no Azure Sentinel, estamos projetando a representação OSSEM para uma representação Log Analytics tabular, independentemente de essa representação ser uma tabela interna ou criada usando analisadores de tempo de consulta ou funções que mapeiam os dados existentes para essa representação. Para a representação tabular, estamos concatenando nomes de entidade OSSEM e nomes de atributos e mapeando-os coletivamente para um único nome de coluna. Por exemplo, uma entidade de origem que contém uma entidade de arquivo contendo uma entidade hash contendo um atributo MD5 será implementada como a seguinte Log Analytics coluna: SrcFileHashMd5. (O OSSEM usa *snake_case* por padrão, enquanto o Azure Sentinel e log Analytics usam o *PascalCase*. Em OSSEM, essa coluna seria src_file_hash_md5.)

Outros campos personalizados podem existir na implementação do Azure Sentinel, devido a requisitos de plataforma Log Analytics e casos de uso específicos para clientes do Azure Sentinel.

### <a name="schema-reference"></a>Referência de esquema

Consulte o [documento de referência do esquema](./normalization-schema.md), bem como a documentação oficial do [projeto OSSEM](https://ossemproject.com/cdm/intro.html), para saber mais.

A referência de esquema também inclui a padronização de valor e de formato. Os campos de origem, original ou analisados, podem não estar em um formato padrão ou usar a lista de valores padrão do esquema e, portanto, precisam ser convertidos para a representação padrão do esquema para serem totalmente normalizados.

## <a name="parsers"></a>Analisadores

- [O que é análise](#what-is-parsing)
- [Usando analisadores de tempo de consulta](#using-query-time-parsers)

### <a name="what-is-parsing"></a>O que é análise

Com um conjunto base de tabelas normalizadas definidas disponíveis, você precisará transformar (analisar/mapear) seus dados nessas tabelas. Ou seja, você extrairá dados específicos de sua forma bruta em colunas conhecidas no esquema normalizado. A análise no Azure Sentinel acontece no **momento da consulta** -os analisadores são criados como funções de usuário log Analytics (usando a linguagem de consulta KUSTO-KQL) que transforma dados em tabelas existentes (como CommonSecurityLog, tabelas de logs personalizados, syslog) no esquema de tabelas normalizadas.

O outro tipo de análise, que ainda não tem suporte no Azure Sentinel, está no **tempo de ingestão** , permitindo coletar dados diretamente para as tabelas normalizadas, pois eles são ingeridos de suas fontes de dados. A análise de tempo de ingestão fornece desempenho aprimorado, pois o modelo de dados é consultado diretamente sem a necessidade de usar funções.

### <a name="using-query-time-parsers"></a>Usando analisadores de tempo de consulta

- [Instalando um analisador](#installing-a-parser)
- [Usando os analisadores](#using-the-parsers)
- [Personalizando analisadores](#customizing-parsers)

#### <a name="installing-a-parser"></a>Instalando um analisador

Os analisadores de tempo de consulta disponíveis estão disponíveis no [repositório GitHub oficial](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/Normalized%20Schema%20-%20Networking%20(v1.0.0))do Azure Sentinel. Cada analisador tem controle de versão para permitir que os clientes usem e monitorem atualizações futuras facilmente. Para instalar um analisador:

1. Copie o conteúdo relevante do analisador de cada arquivo KQL relevante no link do GitHub acima para a área de transferência

1. No portal do Azure Sentinel, abra a página logs e cole o conteúdo do arquivo KQL na tela logs e clique em **salvar**.

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="Instalar um novo analisador":::

1. Na caixa de diálogo salvar, preencha os campos da seguinte maneira:
    1. **Nome**: é recomendável usar o mesmo valor usado no campo **alias da função** (no exemplo acima, *CheckPoint_Network_NormalizedParser*)
    
    1. **Salvar como**: selecione a **função**

    1. **Alias da função**: deve ser nomeado na seguinte convenção de nomenclatura- *PRODUCT_Network_NormalizedParser* (no exemplo acima, *CheckPoint_Network_NormalizedParser*).

    1. **Categoria**: você pode selecionar uma categoria existente ou criar uma nova categoria (como *NormalizedNetworkSessionsParsers*)
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="Salvar o analisador":::

Para usar corretamente os analisadores, você também deve instalar o analisador de esquema de rede vazio (que cria uma exibição tabular vazia de todos os campos de esquema de sessões de rede) e o metaanalisador de rede (que une todos os analisadores habilitados para criar uma única exibição de dados de várias fontes no esquema de rede). A instalação desses dois analisadores é feita de maneira semelhante às etapas mencionadas anteriormente.

Ao salvar uma função de consulta, pode ser necessário fechar o Gerenciador de consultas e abri-lo novamente para que a nova função seja refletida.

#### <a name="using-the-parsers"></a>Usando os analisadores

Uma vez habilitado, você pode usar o metaanalisador para consultar uma exibição unificada em todos os analisadores atualmente habilitados. Para fazer isso, vá para a página de logs do Sentinela do Azure e consulte o metaanalisador:

:::image type="content" source="./media/normalization/query-parser.png" alt-text="Consultar o analisador":::
 
Você também pode acessar o metaanalisador ou os analisadores individuais usando o Gerenciador de consultas na página logs clicando em ' Gerenciador de consultas ':

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="Gerenciador de consultas":::

No painel à direita, expanda a seção "consultas salvas" e localize a pasta ' NormalizedNetworkParsers ' (ou o nome da categoria que você escolheu ao criar os analisadores):

:::image type="content" source="./media/normalization/find-parser.png" alt-text="Localizar seu analisador":::

Você pode clicar em cada analisador individual e ver a função subjacente que ele usa e executá-lo (ou acessá-lo diretamente por seu alias, conforme descrito acima). Observe que alguns analisadores podem reter os campos originais lado a lado para os campos normalizados para sua conveniência. Isso pode ser facilmente editado na consulta do analisador.

> [!TIP]
> Você pode usar suas funções salvas em vez de tabelas do Azure Sentinel em qualquer consulta, incluindo consultas de busca e detecção. Para obter mais informações, consulte:
>
> - [Normalização de dados no Azure Sentinel](normalization.md#parsers)
> - [Analisar texto em logs de Azure Monitor](/azure/azure-monitor/log-query/parse-text)
>
#### <a name="customizing-parsers"></a>Personalizando analisadores

Você pode repetir as etapas acima (localizando o analisador no Gerenciador de consultas), clicar no analisador relevante e ver sua implementação de função.
Por exemplo, você pode optar por editar o metaanalisador para adicionar/remover analisadores individuais.

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="Personalizar o analisador":::
 
Quando a função for alterada, clique em "salvar" novamente e use o mesmo nome, alias e categoria. Uma caixa de diálogo de substituição será aberta – pressione "OK":

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="Tem certeza":::

#### <a name="additional-information"></a>Informações adicionais

JSON, XML e CSV são especialmente convenientes para análise no momento da consulta. O Azure Sentinel tem funções de análise internas para JSON, XML e CSV, bem como uma ferramenta de análise de JSON.  Para obter mais informações, consulte [usando campos JSON no Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747) (blog). 

Saiba mais sobre as [consultas salvas](../azure-monitor/log-query/example-queries.md) (a implementação de analisadores de tempo de consulta) em log Analytics.


## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu sobre o esquema de normalização do Azure Sentinel. Para o próprio esquema de referência, consulte [referência de esquema de normalização de dados do Azure Sentinel](./normalization-schema.md).

* [Blog do Azure Sentinel](https://aka.ms/azuresentinelblog). Encontre postagens no blog sobre a conformidade e segurança do Azure.