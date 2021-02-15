---
title: O que é o Delta Lake
description: Visão geral do Delta Lake e como ele funciona como parte do Azure Synapse Analytics
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: cd582c1175d6bff0aa18ee77dde8b7734b6acf25
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121015"
---
# <a name="what-is-delta-lake"></a>O que é o Delta Lake

O Azure Synapse Analytics é compatível com o Linux Foundation Delta Lake. O Delta Lake é uma camada de armazenamento de código aberto que traz transações ACID (atomicidade, consistência, isolamento e durabilidade) para Apache Spark e Big Data cargas de trabalho.

A versão atual do Delta Lake incluída no Azure Synapse tem suporte de idioma para escala, PySpark e .NET. Há links na parte inferior da página para obter exemplos e documentação mais detalhados.

## <a name="key-features"></a>Principais recursos

| Recurso | Descrição |
| --- | --- |
| **Transações ACID** | Os data lagos normalmente são populados por meio de vários processos e pipelines, alguns dos quais estão gravando dados simultaneamente com leituras. Antes do Delta Lake e da adição de transações, os engenheiros de dados tinham que passar por um processo manual propenso a erros para garantir a integridade dos dados. O Delta Lake traz transações ACID familiares para os lagos de dados. Ele fornece serialização, o nível mais alto de isolamento. Saiba mais em [mergulhando no Delta Lake: desempacotando o log de transações](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html).|
| **Manipulação de metadados escalonável** | Em Big Data, até mesmo os próprios metadados podem ser "Big Data". O Delta Lake trata os metadados assim como os dados, aproveitando a capacidade de processamento distribuído do Spark para lidar com todos os seus metadados. Como resultado, o Delta Lake pode lidar com tabelas de escala de petabytes com bilhões de partições e arquivos com facilidade. |
| **Viagem de tempo (controle de versão de dados)** | A capacidade de "desfazer" uma alteração ou voltar para uma versão anterior é um dos principais recursos das transações. O Delta Lake fornece instantâneos de dados, permitindo que você reverta para versões anteriores de dados para auditoria, reversões ou reprodução de experimentos. Saiba mais em [introdução ao tempo do Delta Lake Travel para grandes lagos de dados de grande escala](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html). |
| **Abrir formato** | O Apache parquet é o formato de linha de base para o Delta Lake, permitindo que você aproveite os esquemas de compactação e codificação eficientes que são nativos do formato. |
| **Lote unificado e fonte de streaming e coletor** | Uma tabela no Delta Lake é uma tabela de lote, bem como uma origem e um coletor de streaming. A ingestão de dados de streaming, o aterramento histórico de lote e as consultas interativas acabam funcionando imediatamente. |
| **Imposição de esquema** | A imposição de esquema ajuda a garantir que os tipos de dados estejam corretos e que as colunas necessárias estejam presentes, impedindo que dados inválidos causem inconsistência de dados Para obter mais informações, consulte [mergulhando no Delta Lake: imposição de esquema & evolução](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Evolução do esquema** | O Delta Lake permite que você faça alterações em um esquema de tabela que pode ser aplicado automaticamente, sem precisar escrever a DDL de migração. Para obter mais informações, consulte [mergulhando no Delta Lake: imposição de esquema & evolução](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Histórico de auditoria** | O log de transações do Delta Lake registra detalhes sobre cada alteração feita nos dados, fornecendo uma trilha de auditoria completa das alterações. |
| **Atualizações e exclusões** | O Delta Lake dá suporte a APIs escalares/Java/Python e SQL para uma variedade de funcionalidades. O suporte para operações de mesclagem, atualização e exclusão ajuda a atender aos requisitos de conformidade. Para obter mais informações, consulte [anunciando a versão Delta Lake 0.6.1](https://delta.io/news/delta-lake-0-6-1-released/),  [anunciando a versão Delta Lake 0,7 e as](https://delta.io/news/delta-lake-0-7-0-released/) [exclusões e Upserts simples, confiáveis em tabelas Delta Lake usando APIs do python](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html), que incluem trechos de código para comandos DML de mesclagem, atualização e exclusão. |
| **100% compatível com a API de Apache Spark** | Os desenvolvedores podem usar o Delta Lake com seus pipelines de dados existentes com alterações mínimas, pois são totalmente compatíveis com as implementações do Spark existentes. |

Para obter a documentação completa, confira a [página de documentação do Delta Lake](https://docs.delta.io/latest/delta-intro.html)

Para obter mais informações, confira [Projeto do Delta Lake](https://github.com/delta-io/delta).

## <a name="next-steps"></a>Próximas etapas

- [Documentação do .NET para Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](../index.yml)