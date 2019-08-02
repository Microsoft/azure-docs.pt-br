---
title: Processamento de eventos em tempo real usando o processamento de eventos do Azure Stream Analytics
description: Este artigo descreve a arquitetura de referência para obter análise e processamento de eventos em tempo real usando o Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: ab1ca89427f332151120420c3c087902584706f4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621773"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Arquitetura de referência: Processamento de eventos em tempo real com o Stream Analytics do Microsoft Azure
A arquitetura de referência para processamento de eventos em tempo real com o Stream Analytics do Azure se destina a fornecer um plano gráfico genérico para implantar uma solução de processamento de fluxo de PaaS (plataforma como um serviço) em tempo real com o Microsoft Azure.

## <a name="summary"></a>Resumo
Tradicionalmente, as soluções de análise se baseavam em recursos como ETL (extração, transformação, carregamento) e data warehouse, em que os dados são armazenados antes da análise. Mudanças nos requisitos, incluindo dados que chegam mais rapidamente, estão levando esse modelo existente ao limite. A capacidade de analisar os dados em fluxos em movimento antes do armazenamento de movimentação é uma solução e, embora não seja um novo recurso, a abordagem não foi amplamente adotada em todos os segmentos verticais da indústria. 

O Microsoft Azure oferece um catálogo abrangente de tecnologias de análise que são capazes de dar suporte a uma matriz de diferentes requisitos e cenários de solução. A seleção dos serviços do Azure a serem implantados para uma solução de ponta a ponta pode ser um desafio, devido à diversidade de ofertas. Este documento foi projetado descrever os recursos e a interoperação dos vários serviços do Azure que dão suporte a uma solução de fluxo de eventos. Ele também explica alguns dos cenários em que os clientes podem se beneficiar desse tipo de abordagem.

## <a name="contents"></a>Conteúdo
* Resumo executivo
* Introdução à análise em tempo real
* Proposta de valor de dados em tempo real no Azure
* Cenários comuns de análise em tempo real
* Arquitetura e componentes
  * Fontes de dados
  * Camada de integração de dados
  * Camada de análise em tempo real
  * Camada de armazenamento de dados
  * Camada de apresentação/consumo
* Conclusão

**Autor:** Charles Feddersen, arquiteto de soluções, Data Insights Center of Excellence, Microsoft Corporation

**Publicado:** Janeiro de 2015

**Revisão:** 1.0

**Download:** [Processamento de eventos em tempo real com o Stream Analytics do Microsoft Azure](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente o [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

