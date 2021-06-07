---
title: Registrando erros e exceções no MSAL para iOS/macOS
titleSuffix: Microsoft identity platform
description: Saiba como registrar erros e exceções no MSAL para iOS/macOS
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
ms.openlocfilehash: ee3837b75d586238e7ca6ac85434cc56f592929d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98763324"
---
# <a name="logging-in-msal-for-iosmacos"></a>Registrar em log em MSAL para iOS/macOS

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL para registro em log do iOS e do macOS-ObjC

Defina um retorno de chamada para capturar o registro em log do MSAL e incorporá-lo no log do seu próprio aplicativo. A assinatura do retorno de chamada tem esta aparência:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Por exemplo:

```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

### <a name="personal-data"></a>Dados pessoais

Por padrão, o MSAL não captura nem registra nenhum dado pessoal. A biblioteca permite que os desenvolvedores de aplicativos ativem isso por meio de uma propriedade na classe MSALLogger. Ao ativar `pii.Enabled` , o aplicativo assume a responsabilidade por lidar com segurança com dados altamente confidenciais e com os requisitos regulatórios a seguir.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Níveis de log

Para definir o nível de log ao fazer logon usando o MSAL para iOS e macOS, use um dos seguintes valores:

|Nível  |Descrição |
|---------|---------|
| `MSALLogLevelNothing`| Desabilitar todo o log |
| `MSALLogLevelError` | Nível padrão, imprime informações somente quando ocorrem erros |
| `MSALLogLevelWarning` | Warnings |
| `MSALLogLevelInfo` |  Pontos de entrada de biblioteca, com parâmetros e várias operações de conjunto de chaves |
|`MSALLogLevelVerbose`     |  Rastreamento de API |

Por exemplo:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Formato de mensagem de log

A parte da mensagem das mensagens de log MSAL está no formato de `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Por exemplo:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornecer IDs de correlação e carimbos de data/hora é útil para rastrear problemas. As informações de carimbo de data e hora e ID de correlação estão disponíveis na mensagem de log. O único local confiável para recuperá-los é de mensagens de registro em log do MSAL.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL para registro em log do iOS e do macOS – Swift

Defina um retorno de chamada para capturar o registro em log do MSAL e incorporá-lo no log do seu próprio aplicativo. A assinatura (representada em Objective-C) para o retorno de chamada é semelhante a:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Por exemplo:

```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-data"></a>Dados pessoais

Por padrão, o MSAL não captura nem registra nenhum dado pessoal. A biblioteca permite que os desenvolvedores de aplicativos ativem isso por meio de uma propriedade na classe MSALLogger. Ao ativar `pii.Enabled` , o aplicativo assume a responsabilidade por lidar com segurança com dados altamente confidenciais e com os requisitos regulatórios a seguir.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Níveis de log

Para definir o nível de log ao fazer logon usando o MSAL para iOS e macOS, use um dos seguintes valores:

|Nível  |Descrição |
|---------|---------|
| `MSALLogLevelNothing`| Desabilitar todo o log |
| `MSALLogLevelError` | Nível padrão, imprime informações somente quando ocorrem erros |
| `MSALLogLevelWarning` | Warnings |
| `MSALLogLevelInfo` |  Pontos de entrada de biblioteca, com parâmetros e várias operações de conjunto de chaves |
|`MSALLogLevelVerbose`     |  Rastreamento de API |

Por exemplo:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Formato de mensagem de log

A parte da mensagem das mensagens de log MSAL está no formato de `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Por exemplo:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornecer IDs de correlação e carimbos de data/hora é útil para rastrear problemas. As informações de carimbo de data e hora e ID de correlação estão disponíveis na mensagem de log. O único local confiável para recuperá-los é de mensagens de registro em log do MSAL.

---

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de código, consulte [exemplos de código da plataforma Microsoft Identity](sample-v2-code.md).