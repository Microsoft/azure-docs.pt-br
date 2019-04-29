---
title: Compilar soluções integradas com o SQL Data Warehouse | Microsoft Docs
description: 'Ferramentas e parceiros com soluções que podem ser integradas ao SQL Data Warehouse '
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: d2fcdd4aa28a7d3b1a8e50629e5be1d88046a12b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60747550"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Integrar outros serviços ao SQL Data Warehouse
Além de suas funcionalidades principais, o SQL Data Warehouse permite aos usuários integrar muitos dos outros serviços no Azure. Alguns desses serviços incluem:

* Power BI
* Fábrica de dados do Azure
* Azure Machine Learning
* Stream Analytics do Azure

O SQL Data Warehouse continua a ser integrado a mais serviços no Azure e mais [Parceiros de integração](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
A integração do Power BI permite que você combine o poder de computação do SQL Data Warehouse com os relatórios e a visualização dinâmica do Power BI. Atualmente, a integração do Power BI inclui:

* **Direct Connect**: Uma conexão mais avançada com a aplicação de lógica no SQL Data Warehouse. A aplicação fornece uma análise mais rápida em uma escala maior.
* **Abrir no Power BI**: O botão “Abrir no Power BI” passa informações de instância para o Power BI para uma maneira simplificada de se conectar.

Para obter mais informações, consulte [Integrar com o Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) ou a [Documentação do Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Fábrica de dados do Azure
O Azure Data Factory oferece aos usuários uma plataforma gerenciada para criar pipelines de extração e carregamento complexos. A integração do SQL Data Warehouse com o Azure Data Factory inclui:

* **Procedimentos Armazenados**: Orquestre a execução de procedimentos armazenados no SQL Data Warehouse.
* **Cópia**: Use o ADF para mover dados para o SQL Data Warehouse. Essa operação pode usar o mecanismo de movimentação de dados padrão do ADF ou PolyBase nos bastidores. 

Para obter mais informações, consulte [Integrar com o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
O Azure Machine Learning é um serviço de análise totalmente gerenciado que permite criar modelos complexos utilizando um grande conjunto de ferramentas de previsão. O SQL Data Warehouse tem suporte como uma origem e um destino para esses modelos com a seguinte funcionalidade:

* **Ler dados:** Encaminhe modelos em escala usando o T-SQL no SQL Data Warehouse.
* **Gravar dados**: Confirme as alterações de qualquer modelo de volta para o SQL Data Warehouse.

Para obter mais informações, consulte [Integrar com o Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Stream Analytics do Azure
O Stream Analytics do Azure é uma infraestrutura complexa, totalmente gerenciada para processamento e consumo de dados de eventos gerados por meio do hub de eventos do Azure.  A integração com o SQL Data Warehouse permite transmitir dados para serem efetivamente processados e armazenados junto com dados relacionais, permitindo análise mais profunda e avançada.  

* **Saída do trabalho:** Envie a saída dos trabalhos do Stream Analytics diretamente para o SQL Data Warehouse.

Para obter mais informações, consulte [Integrar com o Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


