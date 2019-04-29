---
title: Visão Geral do Fluxo de Dados do Mapeamento do Azure Data Factory
description: Uma explicação da visão geral dos Fluxos de Dados de Mapeamento no Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 5064f47444b4ca6d9194ed66144938e6e3d51a4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261798"
---
# <a name="what-are-mapping-data-flows-in-azure-data-factory"></a>O que são os Fluxos de Dados de Mapeamento no Azure Data Factory?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Os Fluxos de Dados de Mapeamento permitem que os engenheiros de dados desenvolvam a lógica de transformação de dados gráficos sem escrever código. Os fluxos de dados resultantes são executados como atividades em Pipelines do Azure Data Factory usando clusters do Azure Databricks expandidos.

A intenção do Fluxo de Dados do Azure Data Factory é fornecer uma experiência totalmente visual sem necessidade de codificação. Seus Fluxos de Dados serão executados em seu próprio cluster em execução para o processamento de dados expandidos. O Azure Data Factory manipula a conversão de código, a otimização de caminho e a execução de seus trabalhos de fluxo de dados.

Comece criando fluxos de dados no modo de depuração para que você possa validar sua lógica de transformação de forma interativa. Em seguida, adicione uma atividade de Fluxo de Dados ao seu pipeline para executar e testar seu fluxo de dados na depuração de pipeline ou use "Disparar agora" no pipeline para testar seu Fluxo de Dados de uma Atividade de pipeline.

Você, em seguida, agendará e monitorará suas atividades de fluxo de dados usando pipelines do Azure Data Factory que executam a atividade de Fluxo de Dados.

A opção de alternância do Modo de Depuração na superfície de design de Fluxo de Dados permite a criação interativa de transformações de dados. O Modo de Depuração fornece um ambiente de preparação de dados para a construção do fluxo de dados.
