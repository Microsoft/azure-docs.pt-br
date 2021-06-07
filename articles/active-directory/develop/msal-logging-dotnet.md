---
title: Registrando erros e exceções em MSAL.NET
titleSuffix: Microsoft identity platform
description: Saiba como registrar erros e exceções no MSAL.NET
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
ms.openlocfilehash: da36ce0a956e0c3ed369a676960bdb9b5c5b1199
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954827"
---
# <a name="logging-in-msalnet"></a>Registro em log no MSAL.NET

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>Configurar o registro em log no MSAL.NET

Na MSAL 3.x, o registro em log é definido por aplicativo na criação do aplicativo usando o modificador de construtor `.WithLogging`. Esse método usa parâmetros opcionais:

- `Level` permite que você decida qual nível de log você deseja. Configurá-lo como Erros só registrará erros
- `PiiLoggingEnabled` permite que você registre dados pessoais e organizacionais se definido como true. Por padrão, ele fica definido como false, para que seu aplicativo não registre dados pessoais.
- `LogCallback` é definido como um delegado que faz o registro em log. Se `PiiLoggingEnabled` for true, esse método receberá as mensagens duas vezes: uma vez com o `containsPii` parâmetro igual a false e a mensagem sem dados pessoais, e uma segunda vez com o `containsPii` parâmetro igual a true e a mensagem poderá conter dados pessoais. Em alguns casos (quando a mensagem não contém dados pessoais), a mensagem será a mesma.
- `DefaultLoggingEnabled` habilita o log padrão para a plataforma. Por padrão, é false. Se você defini-lo como true, ele usará o rastreamento de eventos em aplicativos de área de trabalho/UWP, NSLog no iOS e logcat no Android.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

> [!TIP]
 > Consulte o [wiki do MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) para obter exemplos de registro em log do MSAL.net e muito mais.

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de código, consulte [exemplos de código da plataforma Microsoft Identity](sample-v2-code.md).