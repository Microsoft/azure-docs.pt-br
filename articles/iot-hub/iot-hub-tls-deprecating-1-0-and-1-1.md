---
title: Preterindo o TLS 1.0 e 1.1 no Hub IoT | Microsoft Docs
description: Diretrizes sobre a substituição do TLS 1.0 e 1.1 e das codificações com suporte no Hub IoT.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: fcf7620f53c9bfdb51eb62598f2c8b441574eca6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90006073"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Substituição do TLS 1.0 e 1.1 no Hub IoT

Para fornecer a melhor criptografia de classe, o Hub IoT está migrando para o protocolo TLS 1.2 como o mecanismo de criptografia de escolha para dispositivos e serviços de IoT. 

## <a name="timeline"></a>Linha do tempo

O Hub IoT continuará a dar suporte ao TLS 1.0/1.1 até um aviso adicional. No entanto, recomendamos que todos os clientes migrem para o TLS 1.2 assim que possível.

## <a name="deprecating-tls-11-ciphers"></a>Preterindo codificações do TLS 1.1

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>Preterindo codificações do TLS 1.0

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-cipher-suites"></a>Pacotes de criptografia TLS 1,2

Consulte [pacotes de codificação TLS 1,2 do Hub IOT](iot-hub-tls-support.md#cipher-suites).
 
## <a name="customer-feedback"></a>Comentários do cliente

Embora a imposição do TLS 1.2 seja uma opção de criptografia de nível mais abrangente do setor e seja habilitada como planejada, ainda gostaríamos de ouvir os clientes em relação a suas implantações específicas e dificuldades ao adotar o TLS 1.2. Para essa finalidade, envie seus comentários para [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
