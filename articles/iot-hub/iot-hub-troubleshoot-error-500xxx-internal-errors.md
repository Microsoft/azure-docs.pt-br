---
title: Solucionando problemas de erros internos do 500xxx do Hub IoT do Azure
description: Entenda como corrigir erros internos do 500xxx
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1333a135f3e123757e268513f73e8329537e630b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92538197"
---
# <a name="500xxx-internal-errors"></a>500xxx Internal errors

Este artigo descreve as causas e soluções para **erros internos do 500xxx**.

## <a name="symptoms"></a>Sintomas

A solicitação ao Hub IoT falha com um erro que começa com 500 e/ou algum tipo de "erro de servidor". Algumas possibilidades são:

* **500001 ServerError**: o Hub IOT teve um problema do lado do servidor.

* **500008 GenericTimeout**: o Hub IOT não pôde concluir a solicitação de conexão antes de atingir o tempo limite.

* **ServiceUnavailable (nenhum código de erro)**: o Hub IOT encontrou um erro interno.

* **InternalServerError (nenhum código de erro)**: o Hub IOT encontrou um erro interno.

## <a name="cause"></a>Causa

Pode haver várias causas para uma resposta de erro 500xxx. Em todos os casos, o problema é provavelmente transitório. Enquanto a equipe do Hub IoT trabalha muito para manter [o SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/), pequenos subconjuntos de nós do IoT Hub podem ocasionalmente sofrer falhas transitórias. Quando o dispositivo tenta se conectar a um nó com problemas, você recebe esse erro.

## <a name="solution"></a>Solução

Para atenuar erros de 500xxx, emita uma nova tentativa do dispositivo. Para [gerenciar automaticamente as novas tentativas](./iot-hub-reliability-features-in-sdks.md#connection-and-retry), certifique-se de usar a versão mais recente dos [SDKs do Azure IoT](./iot-hub-devguide-sdks.md). Como prática recomendada no tratamento de falhas transitórias e novas tentativas, consulte [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).  Se o problema persistir, verifique [Resource Health](./iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health) e o [status do Azure](https://status.azure.com/) para ver se o Hub IOT tem um problema conhecido. Você também pode usar o [recurso de failover manual](./tutorial-manual-failover.md). Se não houver nenhum problema conhecido e o problema persistir, [entre em contato com o suporte](https://azure.microsoft.com/support/options/) para obter mais investigações.
