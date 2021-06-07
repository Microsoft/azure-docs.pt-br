---
title: Azure Cosmos DB saída de Azure Stream Analytics
description: Este artigo descreve como gerar dados de saída de Azure Stream Analytics para Azure Cosmos DB.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 9382ac8dc71d1ccb3a85dc0a7a027c8e99296cc6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016570"
---
# <a name="azure-cosmos-db-output-from-azure-stream-analytics"></a>Azure Cosmos DB saída de Azure Stream Analytics

O [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) é um serviço de banco de dados distribuído globalmente que oferece dimensionamento elástico ilimitado em todo o mundo, consulta avançada e indexação automática em modelos de dados independentes de esquemas. Para saber mais sobre opções de contêiner do Azure Cosmos DB para Stream Analytics, consulte o artigo [Stream Analytics com o Cosmos DB como saída](stream-analytics-documentdb-output.md).

A saída do Azure Cosmos DB do Stream Analytics não está disponível atualmente nas regiões Azure China 21Vianet e Azure Alemanha (T-Systems International).

> [!Note]
> Azure Stream Analytics só dá suporte à conexão com o Azure Cosmos DB usando a API do SQL.
> Ainda não há suporte para outras APIs do Azure Cosmos DB. Se você apontar o Azure Stream Analytics para as contas do Azure Cosmos DB criado com as outras APIs, talvez os dados não sejam armazenados corretamente.

A tabela a seguir descreve as propriedades para a criação de uma saída do Azure Cosmos DB.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um alias para se referir a essa saída na sua consulta do Stream Analytics. |
| Coletor | Azure Cosmos DB. |
| Importar opção | Escolha **Selecionar o Cosmos DB de sua assinatura** ou **Fornecer configurações do Cosmos DB manualmente**.
| ID da Conta | O nome ou o URI do ponto de extremidade da conta do Azure Cosmos DB. |
| Chave de conta | A chave de acesso compartilhado da conta do Azure Cosmos DB. |
| Banco de dados | O nome do banco de dados do Azure Cosmos DB. |
| Nome do contêiner | O nome do contêiner a ser usado, o qual deve existir no Cosmos DB. Exemplo:  <br /><ul><li> _MyContainer_: Já deve existir um contêiner chamado "MyContainer".</li>|
| ID do documento |Opcional. O nome do campo em eventos de saída que é usado para especificar a chave primária que serve de base para as operações de inserção ou atualização.

## <a name="partitioning"></a>Particionamento

A chave de partição é baseada na cláusula PARTITION BY na consulta. O número de gravadores de saída segue o particionamento de entrada para [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). Stream Analytics converte a chave de partição de saída Cosmos DB em uma cadeia de caracteres. Por exemplo, se você tiver uma chave de partição com um valor de 1 do tipo bigint, ela será convertida em "1" do tipo cadeia de caracteres. Essa conversão sempre acontece, independentemente de a propriedade de partição ser gravada em Cosmos DB.

## <a name="output-batch-size"></a>Tamanho do lote de saída

Para obter o tamanho máximo da mensagem, consulte [limites de Azure Cosmos DB](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). O tamanho do lote e a frequência de gravação são ajustados dinamicamente com base nas respostas do Cosmos DB. Não há limitações predeterminadas do Stream Analytics.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando a CLI do Azure](quick-create-azure-cli.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando um modelo do Resource Manager](quick-create-azure-resource-manager.md)
* [Início rápido: criar um trabalho de Stream Analytics usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Início rápido: criar um trabalho de Azure Stream Analytics no Visual Studio Code](quick-create-visual-studio-code.md)
