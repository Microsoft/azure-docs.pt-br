---
title: "Guia de Solução de Problemas do Azure Mobile Engagement - Análise"
description: "Solução para problemas de Análise, Monitoramento, Segmentação e Painel no Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 04a7020a-ad74-4491-be69-0bd574890029
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: e30c9ac0a8421ffcf4fc3e2548cfd7ac49701900
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>Guia de solução para problemas de Análise, Monitoramento, Segmentação e Painel
Estes são os possíveis problemas que podem ser encontrados em como o Azure Mobile Engagement reúne informações sobre seus aplicativos, dispositivos e usuários.

## <a name="missingdelayed-information"></a>Informações Ausentes/Atrasadas
### <a name="issue"></a>Problema
* As informações ficam atrasadas ao aparecerem na Análise, Segmentação ou Painel.
* Faltam informações no Monitoramento.
* As informações estão ausentes na Análise, Segmentação ou Painel.
* Limite de segmentação atingido.

### <a name="causes"></a>Causas
* Você pode usar a API da Análise, API do Monitor e API dos Segmentos para verificar se todos os dados que faltam na interface do usuário são visíveis através das APIs.
* Se o SDK do Azure Mobile Engagement não estiver integrado corretamente em seu aplicativo, você não conseguirá ver informações na Análise, Segmentação, Monitoramento ou Painéis.
* Os segmentos não podem ser alterados depois de criados; os segmentos só podem ser "clonados" (copiados) ou "destruídos" (excluídos). Os segmentos podem conter apenas 10 critérios.
* A melhor maneira de testar as informações ausentes do monitoramento é configurar um dispositivo de teste, desinstalação e/ou reinstalar o aplicativo no dispositivo de teste.
* As informações são atualizadas a cada 24 horas para a Análise, Segmentação ou Painéis.
* Informações em novos segmentos não podem ser exibidas até 24 horas após elas terem sido criadas, mesmo se o segmento estiver baseado nas informações anteriores.
* Filtrar seus dados de análise na interface do usuário mostrará todos os exemplos desse tipo, independentemente da versão de seu aplicativo (por exemplo, as "Falhas" filtradas pelo nome aparecerão a partir das versões 1 e 2 de seu aplicativo).
* O período de tempo para a Análise baseia-se na data das configurações do dispositivo dos usuários, portanto, um usuário cujo telefone tem a data definida incorretamente pode aparecer no período de tempo incorreto.
* Nenhum dado do lado do servidor é registrado quando você usa o botão “testar” envios, os dados são registrados apenas para campanhas de envio real.

## <a name="cant-locate-items-in-ui"></a>Não é possível localizar itens na interface do usuário
### <a name="issue"></a>Problema
* Não é possível criar segmentos com base em certos critérios incorporados ou de marca de informações do aplicativo personalizado.
* Não é possível localizar certos critérios incorporados ou de marca de informações do aplicativo personalizado na Análise, Monitoramento ou Painéis.
* Não é possível interpretar os dados na Análise, Monitoramento, Segmentação ou Painéis.

### <a name="causes"></a>Causas
* Alguns itens incorporados e marcas de informações do aplicativo só estão disponíveis como critérios por push, mas podem não estar adicionados a um segmento nem visíveis na Análise, Monitoramento ou Painel. 
* Para os itens incorporados e marcas de informações do aplicativo que não podem ser adicionados a um segmento, você precisará configurar uma lista de critérios de destino em cada campanha para executar a mesma função como o destino com base em um segmento.
* Consulte os menus contextuais nas seções Análise, Monitoramento, Segmentação e Painéis da interface do usuário do Azure Mobile Engagement para obter mais ajuda e informações.

## <a name="crash-troubleshooting"></a>Solucionar problemas de falhas
### <a name="issue"></a>Problema
* Falhas do Aplicativo que aparecem na Análise, Monitoramento ou Painel.

### <a name="causes"></a>Causas
* Para solucionar as Falhas do Aplicativo vistas na Análise, Monitoramento ou Painel, verifique as notas de versão para os problemas conhecidos com as versões anteriores do SDK.
* Para solucionar falhas adicionais do aplicativo execute um evento de um dispositivo de teste com seu aplicativo instalado e procure o ID do dispositivo na seção “Monitor – eventos” da interface de usuário do Azure Mobile Engagement. Em seguida, execute o mesmo que está causando a falha no seu aplicativo e pesquise por informações adicionais na seção “Monitoramento – falha” na interface do usuário do Azure Mobile Engagement. 

