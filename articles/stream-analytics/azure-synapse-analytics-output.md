---
title: Saída do Azure Synapse Analytics de Azure Stream Analytics
description: Este artigo descreve o Azure Synapse Analytics como saída para Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 7e85df8ae67624a253a9fb617629d7355109c210
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019594"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Saída do Azure Synapse Analytics de Azure Stream Analytics

O [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) é um serviço de análise ilimitado que reúne o data warehousing corporativo e a análise de Big Data. 

Azure Stream Analytics trabalhos podem gerar uma saída para uma tabela de pools SQL dedicada no Azure Synapse Analytics e podem processar taxas de taxa de transferência de até 200 MB/s. Isso oferece suporte à análise em tempo real e às necessidades de processamento de dados de Hot-Path mais exigentes para cargas de trabalho, como relatórios e painéis.  

A tabela de pools SQL dedicada deve existir antes que você possa adicioná-la como saída para seu trabalho de Stream Analytics. O esquema da tabela deve corresponder aos campos e seus tipos na saída de trabalho. 

Para usar o Azure Synapse como saída, é preciso garantir que a conta de armazenamento esteja configurada. Navegue até as configurações da Conta de armazenamento para configurá-la. São permitidos apenas os tipos de conta de armazenamento com suporte para tabelas: Uso geral V2 e Uso geral V1. Selecione somente a camada Standard. A camada Premium não é compatível.

## <a name="output-configuration"></a>Configuração de saída

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída do Azure Synapse Analytics.

|Nome da propriedade|Descrição|
|-|-|
|Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse banco de dados. |
|Banco de dados |nome do pool de SQL dedicado no qual você está enviando a saída. |
|Nome do servidor |Nome do servidor do Azure Synapse.  |
|Nome de Usuário |O nome de usuário que tem acesso de gravação ao banco de dados. O Stream Analytics tem suporte apenas à autenticação do SQL. |
|Senha |A senha para se conectar ao banco de dados. |
|Tabela  | O nome da tabela em que a saída é gravada. O nome da tabela diferencia maiúsculas de minúsculas. O esquema dessa tabela deve corresponder exatamente ao número de campos e tipos gerados pela sua saída de trabalho.|

## <a name="next-steps"></a>Próximas etapas

* [Usar identidades gerenciadas para acessar o banco de dados SQL do Azure ou o Azure Synapse Analytics de um trabalho Azure Stream Analytics (versão prévia)](sql-database-output-managed-identity.md)
* [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)