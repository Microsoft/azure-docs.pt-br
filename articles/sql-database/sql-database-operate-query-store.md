---
title: "Operando o Repositório de Consultas no Banco de Dados SQL do Azure"
description: "Saiba como operar o Repositório de Consultas no Banco de Dados SQL do Azure"
keywords: 
services: sql-database
documentationcenter: 
author: bonova
manager: jhubbard
editor: 
ms.assetid: 0cccf6bd-1327-44f7-a6f9-8eff0c210463
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: sqldb-performance
ms.workload: data-management
ms.date: 11/08/2016
ms.author: bonova
ms.openlocfilehash: c76934d96530889f529eb887469e05d5a6a45e1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Operando o Repositório de Consultas no Banco de Dados SQL do Azure
O Repositório de Consultas no Azure é um recurso de banco de dados totalmente gerenciado que continuamente coleta e apresenta informações históricas sobre todas as consultas. Você pode pensar sobre Repositório de Consultas de forma semelhante à caixa-preta de bordo do avião que simplifica significativamente o desempenho da consulta de solução de problemas para a nuvem e os clientes locais. Este artigo explica os aspectos específicos da operação de Repositório de Consultas no Azure. Usando esta consulta de dados previamente coletados, você pode diagnosticar e resolver problemas de desempenho rapidamente e, portanto, gastar mais tempo concentrando-se em seus negócios. 

O Repositório de Consultas ficou [disponível globalmente](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) no Banco de Dados SQL do Azure desde novembro de 2015. O Repositório de Consulta é a base para a análise de desempenho e ajuste de recursos, tais como o [Painel do Orientador e Desempenho do Banco de Dados SQL](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). No momento da publicação deste artigo, o Repositório de Consultas está sendo executado em mais de 200.000 bancos de dados do usuário no Azure, coletando informações relacionadas a consultas por vários meses, sem interrupção.

> [!IMPORTANT]
> A Microsoft está em processo de ativação do Repositório de Consultas para todos os bancos de dados SQL do Azure (novos e existentes). 
> 
> 

## <a name="optimal-query-store-configuration"></a>Configuração do Repositório de Consulta ideais
Esta seção descreve os padrões de configuração ideais que são projetados para garantir a operação confiável do Repositório de Consultas, bem como recursos dependentes como o [Painel de Orientador e Desempenho do Banco de Dados SQL](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). A configuração padrão é otimizada para coleta de dados contínua, ou seja, tempo mínimo gasto nos estados OFF/READ_ONLY.

| Configuração | Descrição | Padrão | Comentário |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Especifica o limite para o espaço de dados que o Repositório de Consultas ocupará no banco de dados do cliente |100 |Imposto para novos bancos de dados |
| INTERVAL_LENGTH_MINUTES |Define o tamanho da janela de tempo durante o qual as estatísticas de tempo de execução coletadas para planos de consulta são agregadas e persistidas. Cada plano de consulta ativa tem no máximo uma linha por um período de tempo definido com esta configuração |60 |Imposto para novos bancos de dados |
| STALE_QUERY_THRESHOLD_DAYS |Política de limpeza com base em tempo que controla o período de retenção de estatísticas de tempo de execução persistidas e consultas inativas |30 |Imposto para novos bancos de dados e bancos de dados padrão anteriores (367) |
| SIZE_BASED_CLEANUP_MODE |Especifica se a limpeza automática de dados ocorrerá quando o tamanho dos dados do Repositório de Consultas aproximar-se do limite |AUTO |Imposto para todos os bancos de dados |
| QUERY_CAPTURE_MODE |Especifica se todas as consultas ou apenas um subconjunto de consultas será controlado |AUTO |Imposto para todos os bancos de dados |
| FLUSH_INTERVAL_SECONDS |Especifica o período máximo durante o qual as estatísticas de tempo de execução coletadas são mantidas na memória antes de liberar para disco |900 |Imposto para novos bancos de dados |
|  | | | |

> [!IMPORTANT]
> Esses padrões são aplicados automaticamente no estágio final da ativação do Repositório de Consultas em todos os bancos de dados SQL do Azure (veja a importante observação anterior). Após essa iluminação, o Banco de Dados SQL do Azure não alterará os valores de configuração definidos por clientes, a menos que afete negativamente as operações confiáveis de armazenamento de consulta ou carga de trabalho principal no Repositório de Consultas.
> 
> 

Se você quiser manter suas configurações personalizadas, use [ALTER DATABASE com opções de Repositório de Consultas](https://msdn.microsoft.com/library/bb522682.aspx) para reverter a configuração ao estado anterior. Confira as [Práticas recomendadas com o Repositório de Consultas](https://msdn.microsoft.com/library/mt604821.aspx) para saber como escolher os parâmetros de configuração ideais.

## <a name="next-steps"></a>Próximas etapas
[Análise de Desempenho de Banco de Dados SQL](sql-database-performance.md)

## <a name="additional-resources"></a>Recursos adicionais
Para obter mais informações, confira os seguintes artigos:

* [Uma caixa-preta de bordo para seu banco de dados](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 
* [Monitorando o desempenho usando o Repositório de Consultas](https://msdn.microsoft.com/library/dn817826.aspx)
* [Cenários de Uso do Repositório de Consultas](https://msdn.microsoft.com/library/mt614796.aspx)
* [Monitorando o desempenho usando o Repositório de Consultas](https://msdn.microsoft.com/library/dn817826.aspx) 

