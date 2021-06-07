---
title: Políticas de erro de saída no Azure Stream Analytics
description: Saiba mais sobre as políticas de tratamento de erro de saída disponíveis no Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 19d762a55127af34e84185b11518aa6584acb5bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012403"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Política de erro de saída no Azure Stream Analytics
Este artigo descreve as políticas de tratamento de erro de dados de saída que podem ser configuradas no Azure Stream Analytics.

As políticas de tratamento de erro de dados de saída aplicam-se apenas a erros de conversão de dados que ocorrem quando o evento de saída produzido por um trabalho do Stream Analytics não está em conformidade com o esquema do coletor de destino. É possível configurar essa política, escolhendo **Repetir** ou **Remover**. No portal do Azure, enquanto estiver em um trabalho do Stream Analytics, em **Configurar**, selecione **Política de Erro** para fazer a seleção.

![Localização da política de erro de saída no Azure Stream Analytics](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Tentar novamente
Quando um erro ocorrer, o Azure Stream Analytics repetirá a gravação do evento indefinidamente até que a gravação seja executada com êxito. Não há tempo limite para novas repetições. Eventualmente, todos os eventos subsequentes são bloqueados do processamento pelo evento que está tentando novamente. Essa opção é a política de tratamento de erro de saída padrão.

## <a name="drop"></a>Remover
O Azure Stream Analytics removerá qualquer evento de saída que resulte em um erro de conversão de dados. Os eventos removidos não poderão ser recuperados para reprocessamento posterior.


Todos os erros transitórios (por exemplo, erros de rede) serão repetidos, independentemente da configuração da política de tratamento de erro de saída.


## <a name="next-steps"></a>Próximas etapas
[Guia de solução de problemas do Azure Stream Analytics](./stream-analytics-troubleshoot-query.md)