---
title: Solução de problemas do Hub IoT do Azure erro 504101 GatewayTimeout
description: Entenda como corrigir o erro 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 373acc30ed652a7f540e840dfad5eeeda65ca179
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81759562"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

Este artigo descreve as causas e soluções para erros de **504101 GatewayTimeout** .

## <a name="symptoms"></a>Sintomas

Ao tentar invocar um método direto do Hub IoT para um dispositivo, a solicitação falha com o erro **504101 GatewayTimeout**.

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

O Hub IoT encontrou um erro e não pôde confirmar se o método direto foi concluído antes de atingir o tempo limite.

### <a name="cause-2"></a>Causa 2

Ao usar uma versão anterior do SDK do C# do Azure IoT (<1.19.0), o link AMQP entre o dispositivo e o Hub IoT pode ser descartado silenciosamente por causa de um bug.

## <a name="solution"></a>Solução

### <a name="solution-1"></a>Solução 1

Emita uma nova tentativa.

### <a name="solution-2"></a>Solução 2

Atualize para a versão mais recente do SDK do C# do Azure IOT.