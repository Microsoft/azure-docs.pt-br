---
title: Linhagem de dados no Azure alcance (versão prévia)
description: Descreve os conceitos para a linhagem de dados.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: 476355f41de5e0e6aaffdedea8947cab5221767a
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200739"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Linhagem de dados no cliente do catálogo de dados alcance do Azure

Este artigo fornece uma visão geral da linhagem de dados no catálogo de dados alcance do Azure. Ele também detalha como os sistemas de dados podem se integrar ao catálogo para capturar a linhagem de dados. O alcance pode capturar a linhagem de dados em diferentes partes do espaço de dados da sua organização e em diferentes níveis de preparação, incluindo:

- Dados totalmente brutos preparados de várias plataformas
- Dados transformados e preparados
- Dados usados por plataformas de visualização.

## <a name="use-cases"></a>Casos de uso

A linhagem de dados é amplamente compreendida como o ciclo de vida que abrange a origem dos dados e onde ela se move ao longo do tempo entre o espaço de dados. Ele é usado para diferentes tipos de cenários de aparência inversa, como solução de problemas, rastreamento de causa raiz em pipelines de dados e depuração. A linhagem também é usada para cenários de análise de qualidade de dados, conformidade e "e se" geralmente chamados de análise de impacto. A linhagem é representada visualmente para mostrar dados que se movem da origem para o destino, incluindo como os dados foram transformados. Devido à complexidade da maioria dos ambientes de dados corporativos, essas exibições podem ser difíceis de entender sem fazer alguma consolidação ou mascaramento de pontos de dados periféricos.

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Experiência de linhagem no catálogo de dados do Azure alcance

O catálogo de dados do alcance se conectará a outros sistemas de processamento, armazenamento e análise de dados para extrair informações de linhagem. As informações são combinadas para representar uma experiência de linhagem genérica e específica do cenário no catálogo.

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="linhagem de extremidade final mostrando os dados copiados do repositório de blob até Power BI painel":::

Seu estado de dados pode incluir sistemas fazendo extração de dados, transformação (sistemas ETL/ELT), análise e sistemas de visualização. Cada um dos sistemas captura metadados avançados e estáticos que descrevem o estado e a qualidade dos dados dentro do limite de sistemas. O objetivo da linhagem em um catálogo de dados é extrair a movimentação, a transformação e os metadados operacionais de cada sistema de dados no menor detalhamento possível.

O exemplo a seguir é um caso de uso típico de dados que se movem entre vários sistemas, em que o catálogo de dados se conectaria a cada um dos sistemas para linhagem.

- Data Factory copia dados da zona local/bruta para uma zona de aterrissagem na nuvem. 
- Sistemas de processamento de dados como o Synapse, o databricks processaria e transformaria dados da zona de aterrissagem para a zona organizada usando blocos de anotações.
- Processamento adicional de dados em modelos analíticos para melhor desempenho e agregação de consulta. 
- Os sistemas de visualização de dados consumirão os conjuntos e o processo por meio de seu meta modelo para criar um painel de BI, os experimentos de ML e assim por diante.

## <a name="lineage-granularity"></a>Granularidade da linhagem

Esta seção aborda os detalhes sobre a granularidade da qual as informações de linhagem são coletadas por um catálogo de dados. Essa granularidade pode variar com base nos sistemas de dados que estão sendo.

### <a name="entity-level-lineage-sources--process--targets"></a>Linhagem no nível da entidade: origem (s) > processo > destino (s) 

- A linhagem é representada como um grafo, normalmente contém entidades de origem e de destino em sistemas de armazenamento de dados que são conectados por um processo invocado por um sistema de computação. 
- Os sistemas de dados se conectam ao catálogo de dados para gerar e relatar um objeto exclusivo que faz referência ao objeto físico do sistema de dados subjacente, por exemplo: procedimento armazenado SQL, notebooks e assim por diante.
- A linhagem de alta fidelidade com metadados adicionais, como a propriedade, é capturada para mostrar a linhagem em um formato legível por humanos para entidades de origem & destino. por exemplo: linhagem em um nível de tabela do hive em vez de partições ou nível de arquivo.

### <a name="column-or-attribute-level-lineage"></a>Linhagem de nível de coluna ou atributo

Identifique os atributos de uma entidade de origem que é usada para criar ou derivar atributo (s) na entidade de destino. O nome do atributo de origem pode ser retido ou renomeado em um destino. Sistemas como o ADF podem fazer uma cópia One-One do ambiente local para a nuvem. Por exemplo: `Table1/ColumnA -> Table2/ColumnA`.

### <a name="process-execution-status"></a>Status de execução do processo

Para dar suporte à análise da causa raiz e cenários de qualidade de dados, capturamos o status de execução dos trabalhos em sistemas de processamento de dados. Esse requisito não tem nada a ver com a substituição dos recursos de monitoramento de outros sistemas de processamento de dados, nem o objetivo é substituí-los. 

## <a name="summary"></a>Resumo

A linhagem é um recurso crítico do catálogo de dados alcance para dar suporte a cenários de qualidade, confiança e auditoria. O objetivo de um catálogo de dados é criar uma estrutura robusta em que todos os sistemas de dados em seu ambiente possam se conectar naturalmente e relatar a linhagem. Depois que os metadados estão disponíveis, o catálogo de dados pode reunir os metadados fornecidos pelos sistemas de dados para os casos de uso de governança de dados de energia.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar uma conta do Azure Purview no portal do Azure](create-catalog-portal.md)
* [Início Rápido: Criar uma conta do Azure Purview usando o Azure PowerShell/a CLI do Azure](create-catalog-powershell.md)
* [Início rápido: usar o alcance Studio](use-purview-studio.md)
