---
title: Solução de problemas do Hub IoT do Azure erro 400027 ConnectionForcefullyClosedOnNewConnection
description: Entenda como corrigir o erro 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: e5d1dc345c72d77be6172fb9c3a10eb2f38d186a
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506321"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Este artigo descreve as causas e soluções para erros de **400027 ConnectionForcefullyClosedOnNewConnection** .

## <a name="symptoms"></a>Sintomas

O dispositivo é desconectado com **Communication_Error** como **CONNECTIONSTATUSCHANGEREASON** usando o MQTT do SDK do .net e o tipo de transporte.

A operação de conexão do dispositivo para a nuvem (como propriedades relatadas de leitura ou patch) ou invocação de método direto falha com o código de erro **400027**.

## <a name="cause"></a>Causa

Outro cliente criou uma nova conexão com o Hub IoT usando as mesmas credenciais, portanto, o Hub IoT fechou a conexão anterior. O Hub IoT não permite que mais de um cliente se conecte usando o mesmo conjunto de credenciais.

## <a name="solution"></a>Solução

Certifique-se de que cada cliente se conecte ao Hub IoT usando sua própria identidade.