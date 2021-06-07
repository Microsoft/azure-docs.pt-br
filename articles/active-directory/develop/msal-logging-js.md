---
title: Registrando erros e exceções no MSAL.js
titleSuffix: Microsoft identity platform
description: Saiba como registrar erros e exceções no MSAL.js
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d7463e3376847a219750254548c25ca79f4cdfb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954810"
---
# <a name="logging-in-msaljs"></a>Como fazer registro em log no MSAL.js

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msaljs"></a>Configurar o registro em log no MSAL.js

Habilite o logon MSAL.js (JavaScript) passando um objeto do agente durante a configuração para criar uma `UserAgentApplication` instância. O objeto logger tem as seguintes propriedades:

- `localCallback`: uma instância de retorno de chamada que pode ser fornecida pelo desenvolvedor para consumir e publicar logs de maneira personalizada. Implemente o método localCallback, dependendo de como você deseja redirecionar os logs.
- `level` (opcional): o nível de log configurável. Os níveis de log com suporte são: `Error` , `Warning` , `Info` e `Verbose` . O padrão é `Info`.
- `piiLoggingEnabled` (opcional): se definido como true, registra dados pessoais e organizacionais. Por padrão, isso é falso para que seu aplicativo não Registre dados pessoais. Logs de dados pessoais nunca são gravados em saídas padrão como Console, Logcat ou NSLog.
- `correlationId` (opcional): um identificador exclusivo, usado para mapear a solicitação com a resposta para fins de depuração. O padrão é guid RFC4122 versão 4 (128 bits).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
    system: {
        logger: new Msal.Logger(
            loggerCallback , {
                level: Msal.LogLevel.Verbose,
                piiLoggingEnabled: false,
                correlationId: '1234'
            }
        )
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de código, consulte [exemplos de código da plataforma Microsoft Identity](sample-v2-code.md).