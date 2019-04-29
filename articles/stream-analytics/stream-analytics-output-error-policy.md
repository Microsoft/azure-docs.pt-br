---
title: Políticas de erro de saída no Azure Stream Analytics
description: Saiba mais sobre as políticas de tratamento de erro de saída disponíveis no Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: b31530966d2c5ca9a3f82f3e74ba349e66053a83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478906"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Política de erro de saída no Azure Stream Analytics
Este artigo descreve as políticas de tratamento de erro de dados de saída que podem ser configuradas no Azure Stream Analytics.

As políticas de tratamento de erro de dados de saída aplicam-se apenas a erros de conversão de dados que ocorrem quando o evento de saída produzido por um trabalho do Stream Analytics não está em conformidade com o esquema do coletor de destino. É possível configurar essa política, escolhendo **Repetir** ou **Remover**. No portal do Azure, enquanto estiver em um trabalho do Stream Analytics, em **Configurar**, selecione **Política de Erro** para fazer a seleção.

![Localização da política de erro de saída no Azure Stream Analytics](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Retry
Quando um erro ocorrer, o Azure Stream Analytics repetirá a gravação do evento indefinidamente até que a gravação seja executada com êxito. Não há tempo limite para novas repetições. Eventualmente, todos os eventos subsequentes são bloqueados do processamento pelo evento que está tentando novamente. Essa opção é a política de tratamento de erro de saída padrão.

## <a name="drop"></a>Remover
O Azure Stream Analytics removerá qualquer evento de saída que resulte em um erro de conversão de dados. Os eventos removidos não poderão ser recuperados para reprocessamento posterior.


Todos os erros transitórios (por exemplo, erros de rede) serão repetidos, independentemente da configuração da política de tratamento de erro de saída.


## <a name="next-steps"></a>Próximas etapas
[Guia de solução de problemas do Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
