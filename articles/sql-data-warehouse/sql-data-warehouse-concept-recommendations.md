---
title: Recomendações do SQL Data Warehouse - Conceitos | Microsoft Docs
description: Aprenda sobre as recomendações do SQL Data Warehouse e como elas são geradas
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b275f23209979e1a8068ecd99465f7b52392bc6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421215"
---
# <a name="sql-data-warehouse-recommendations"></a>Recomendações do SQL Data Warehouse

Este artigo descreve as recomendações fornecidas pelo SQL Data Warehouse por meio do Azure Advisor.  

O SQL Data Warehouse fornece recomendações para garantir que seu data warehouse seja consistentemente otimizado para desempenho. As recomendações de data warehouse são totalmente integradas ao [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) para fornecer as melhores práticas diretamente no [portal do Azure](https://aka.ms/Azureadvisor). O SQL Data Warehouse analisa o estado atual de seu data warehouse, coleta a telemetria e faz recomendações de superfície para sua carga de trabalho ativa em uma cadência diária. Os cenários de recomendação de data warehouse suportados são descritos abaixo, juntamente com a forma de aplicar as ações recomendadas.

Se você tiver comentários sobre o Assistente do SQL Data Warehouse ou encontrar algum problema, contate [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com).   

Clique [aqui](https://aka.ms/Azureadvisor) para verificar suas recomendações hoje mesmo! Atualmente, esse recurso é aplicável apenas aos armazéns de dados Gen2. 

## <a name="data-skew"></a>Distorção de dados

A distorção de dados pode causar movimentação adicional de dados ou afunilamentos de recursos ao executar sua carga de trabalho. A seguinte documentação descreve mostrar para identificar distorção de dados e impedir que isso aconteça selecionando uma chave de distribuição ideal.

- [Identificar e Remover distorção](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Não ou estatísticas desatualizadas

Ter estatísticas abaixo do ideal pode impactar gravemente o desempenho da consulta, pois pode fazer com que o otimizador de consulta do SQL Data Warehouse gere planos de consulta abaixo do ideal. A documentação a seguir descreve as melhores práticas de criação e atualização de estatísticas:

- [Criando e atualizando estatísticas da tabela](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Para ver a lista de tabelas afetadas por essas recomendações, execute o seguinte [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). O Assistente executa continuamente o mesmo script T-SQL para gerar essas recomendações.

## <a name="replicate-tables"></a>Replicar Tabelas

Para obter recomendações de tabela replicada, o Assistente detecta possíveis tabelas com base nas seguintes características físicas:

- Tamanho da tabela replicada
- Número de colunas
- Tipo de distribuição de tabelas
- Número of partições

O Assistente continuamente utiliza heurística com base em carga de trabalho, como, por exemplo, frequência de acesso de tabela, linhas retornadas em média e limites de tamanho e atividade do data warehouse, para garantir que recomendações de alta qualidade sejam geradas. 

O exemplo a seguir descreve a heurística com base em carga de trabalho que pode ser encontrada no portal do Azure para cada recomendação de tabela replicada:

- Examinar o percentual médio avg- de linhas que foram retornadas da tabela para cada acesso de tabela nos últimos sete dias
- Leitura frequente, nenhuma atualização - indica que a tabela não foi atualizada nos últimos sete dias e mostra a atividade de acesso
- Taxa de leitura/atualização - a frequência em que a tabela foi acessada em relação a quando foi atualizada nos últimos sete dias
- Atividade - mede o uso com base na atividade de acesso. Isso se compara à atividade de acesso de tabela em relação à atividade média de acesso de tabela no data warehouse nos últimos sete dias. 

Atualmente, o Assistente mostrará apenas no máximo quatro tabelas replicadas possíveis por vez, com índices columnstore clusterizados priorizando a atividade mais alta.

> [!IMPORTANT]
> A recomendação de tabela replicada não é à prova de falhas e não leva em consideração as operações de movimentação de dados da conta. Estamos trabalhando para adicionar isso como uma heurística, mas por enquanto você deverá sempre validar sua carga de trabalho após a aplicação da recomendação. Entre em contato com sqldwadvisor@service.microsoft.com se descobrir recomendações de tabela replicada fazem com que sua carga de trabalho retroceda. Para saber mais sobre as tabelas replicadas, visite a seguinte [documentação](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table).
