---
author: rothja
ms.service: iot-dps
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: bbe928084a758335e5fa3b0531726c77a6d599b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85838918"
---
A tabela a seguir lista os limites que se aplicam aos recursos do Serviço de Provisionamento de Dispositivos no Hub IoT do Azure.

| Recurso | Limite |
| --- | --- |
| Máximo de serviços de provisionamento de dispositivos por assinatura do Azure | 10 |
| Número máximo de cadastros | 1\.000.000 |
| Número máximo de registros | 1\.000.000 |
| Número máximo de grupos de cadastro | 100 |
| Número máximo de CAs | 25 |
| Número máximo de hubs IoT vinculados | 50 |
| Tamanho máximo da mensagem | 96 KB|

> [!NOTE]
> Para aumentar o número de registros e registros em seu serviço de provisionamento, entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).

> [!NOTE]
> Não há suporte para o aumento do número máximo de CAs.

O serviço de Provisionamento de Dispositivos restringe as solicitações quando as cotas a seguir são excedidas.

| Restrição | Valor por unidade |
| --- | --- |
| Operações | 200/min/serviço |
| Registros de dispositivo | 200/min/serviço |
| Operação de sondagem de dispositivo | 5/10 s/dispositivo |
