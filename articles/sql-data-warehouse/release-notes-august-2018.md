---
title: Do Azure SQL Data Warehouse notas de versão de agosto de 2018 | Microsoft Docs
description: Notas de versão do SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/13/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 9547380d4636c1a72add9dab7a9eed850abd0307
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474990"
---
# <a name="whats-new-in-azure-sql-data-warehouse-august-2018"></a>O que há de novo no SQL Data Warehouse do Azure? Agosto de 2018
O SQL Data Warehouse do Azure recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidos em agosto de 2018.

## <a name="automatic-intelligent-insights"></a>O Intelligent Insights automática
A Microsoft introduziu [Insights inteligentes automáticos](https://azure.microsoft.com/blog/automatic-intelligent-insights-to-optimize-performance-with-sql-data-warehouse/) para oferecer a promessa de automação da nuvem para seu data warehouse. Você não precisará monitorar seu data warehouse para estatísticas de tabela abaixo do ideal e distorção de dados. Sem custo adicional, o SQL Data Warehouse cria insights inteligentes para todas as instâncias Gen2. Integrando-se ao [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), você pode receber automaticamente recomendações de práticas recomendadas para melhorar o desempenho de suas cargas de trabalho ativas. O SQL Data Warehouse analisa suas recomendações de carga de trabalho e de superfícies com base no seu uso. Essa análise acontece diariamente, permitindo que você monitore os relatórios de uso e recomendações para melhorias em sua carga de trabalho.

Você pode exibir as recomendações no portal do Assistente do Azure: ![Recomendações do Portal do Assistente do Azure para o SQL Data Warehouse do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/4e205b6d-df04-48db-8eec-d591f2592cf4.png)

Você pode analisar cada categoria para ver as recomendações para o alerta específico: ![Detalhes de Recomendações do Portal do Assistente do Azure para o SQL Data Warehouse do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/3c42426e-6969-46e3-9025-c34c0755a302.png)


## <a name="bug-fixes"></a>Correções de bug

| Title | DESCRIÇÃO |
|:---|:---|
| **Possíveis falhas de consulta quando a contagem de divisão exceder o limite máximo** |Quando o limite de divisão de arquivo de limite superior de 1 milhão é excedido, uma exceção não tratada causou o despejo do SQL Engine e todas as consultas falharam. Essa correção resolveu o problema manipulando a exceção corretamente e retornando um erro sem causar falhas nas consultas. |
| **Valor padrão externo de ExternalMoveReadersPerNode aumentado para melhorar o desempenho da carga** |Esse problema foi causado pela configuração da propriedade ExternalMoveReadersPerNode estar fora de sincronia com a configuração de malha de serviço. Essa regressão causou uma degradação do desempenho de carga Gen2. A correção traz o desempenho de carregamento de Gen2 volta dentro dos parâmetros de design otimizado.|


## <a name="next-steps"></a>Próximas etapas
Agora que você conhece um pouco do SQL Data Warehouse, aprenda como [criar um SQL Data Warehouse][create a SQL Data Warehouse] rapidamente. Se você ainda não conhece o Azure, poderá achar o [Glossário do Azure][Azure glossary] útil à medida que encontrar nova terminologia. Ou, dê uma olhada em alguns desses outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogs]
* [Solicitações de recursos]
* [Vídeos]
* [Blogs da Equipe Consultoria para Clientes]
* [Fórum Stack Overflow]
* [Twitter]


[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs da Equipe Consultoria para Clientes]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
