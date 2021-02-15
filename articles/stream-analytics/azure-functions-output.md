---
title: Azure Functions saída de Azure Stream Analytics
description: Este artigo descreve o Azure Functions como saída para Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: e5ea7a1abbbd6ab4be32955179227fbd539cf641
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019611"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>Azure Functions saída de Azure Stream Analytics

O Azure Functions é um serviço de computação sem servidor que você pode usar para executar o código sob demanda sem precisar provisionar explicitamente ou gerenciar a infraestrutura. Ele permite que você implemente o código que é disparado por eventos que ocorrem no Azure ou por serviços de parceiros. Essa capacidade do Azure Functions de responder a gatilhos o torna uma saída natural para o Azure Stream Analytics. Esse adaptador de saída habilita aos usuários se conectar o Stream Analytics ao Azure Functions e executar um script ou trecho de código em resposta a vários eventos.

Azure Functions saída do Stream Analytics não está disponível nas regiões do Azure China 21Vianet e do Azure Alemanha (T-Systems International). Também não há suporte para a conexão com Azure Functions dentro de uma VNet (rede virtual) de um trabalho de Stream Analytics que esteja sendo executado em um cluster de vários locatários.

O Azure Stream Analytics chama o Azure Functions por meio de gatilhos de HTTP. O adaptador de saída do Azure Functions está disponível com as seguintes propriedades configuráveis:

| Nome da propriedade | Descrição |
| --- | --- |
| Aplicativo de funções |O nome de seu aplicativo do Azure Functions. |
| Função |O nome da função em seu aplicativo do Azure Functions. |
| Chave |Se você quiser usar um Azure Function de outra assinatura, você pode fazer isso fornecendo a chave para acessar sua função. |
| Tamanho máximo do lote |Uma propriedade que permite que você defina o tamanho máximo de cada lote de saída que é enviado para seu Azure Functions. A unidade de entrada é em bytes. Por padrão, esse valor é 262,144 bytes (256 KB). |
| Contagem máxima do lote  |Uma propriedade que permite que você especifique o número máximo de eventos em cada que é enviado ao Azure Functions. O valor padrão é 100. |

O Azure Stream Analytics espera o status 200 do HTTP do aplicativo Functions para lotes que foram processados com êxito.

Quando o Azure Stream Analytics recebe a exceção 413 (“Entidade de solicitação http muito grande”) de uma função do Azure, ele reduz o tamanho dos lotes que envia para o Azure Functions. Em seu código de função do Azure, use essa exceção para verificar se o Azure Stream Analytics não enviará lotes grandes demais. Também verifique se os valores de contagem e o tamanho máximo do lote usados na função sejam consistentes com os valores inseridos no portal do Stream Analytics.

> [!NOTE]
> Durante a conexão de teste, Stream Analytics envia um lote vazio para o Azure Functions para testar se a conexão entre os dois funciona. Verifique se seu aplicativo Functions manipula as solicitações em lote vazias para garantir que os testes de conexão sejam aprovados.

Além disso, em uma situação em que não há nenhum evento caindo em uma janela de tempo, nenhuma saída é gerada. Como resultado, a função **computeResult** não é chamada. Esse comportamento é consistente com as funções de agregação em janelas internas.

## <a name="partitioning"></a>Particionamento

A chave de partição é baseada na cláusula PARTITION BY na consulta. O número de gravadores de saída segue o particionamento de entrada para [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída

O tamanho de lote padrão é 262.144 bytes (256 KB). A contagem de eventos padrão por lote é de 100. O tamanho do lote é configurável e pode ser aumentado ou diminuído nas opções de saída do Stream Analytics.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando a CLI do Azure](quick-create-azure-cli.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando um modelo do Resource Manager](quick-create-azure-resource-manager.md)
* [Início rápido: criar um trabalho de Stream Analytics usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Início rápido: criar um trabalho de Azure Stream Analytics no Visual Studio Code](quick-create-visual-studio-code.md)