---
title: Registro em log em aplicativos da MSAL | Azure
description: Saiba como fazer registro em log em aplicativos da MSAL (Biblioteca de Autenticação da Microsoft).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/22/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4288ff4aba216a214d10c56ba448fc03e13b81f2
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693942"
---
# <a name="logging"></a>Registro em log
Os aplicativos da MSAL (Biblioteca de Autenticação da Microsoft) geram mensagens de log que podem ajudar a diagnosticar problemas e fornecer detalhes. Um aplicativo pode configurar o registro em log com algumas linhas de código, ter um controle personalizado sobre o nível de detalhes e determinar se dados pessoais e organizacionais serão registrados. É recomendável que você defina um retorno de chamada de registro em log da MSAL e forneça uma maneira para os usuários enviarem logs quando estiverem com problemas de autenticação.

## <a name="logging-levels"></a>Níveis de registro de log

Agente da MSAL permite que vários níveis de detalhes sejam capturados:

- Erro: indica que algo deu errado e um erro foi gerado. Use para depuração e identificação de problemas.
- Aviso: eventos de pergunta, e o aplicativo precisa de mais informações sobre eles. É destinado a diagnosticar e identificar problemas, mesmo que não tenha havido necessariamente um erro ou falha.
- Informações: a MSAL registrará os eventos destinados a fins informativos, não necessariamente voltados a depuração.
- Detalhado: Padrão. A MSAL registrará uma grande quantidade de informações e fornecerá detalhes completos sobre o comportamento da biblioteca.

## <a name="personal-and-organizational-data"></a>Dados pessoais e organizacionais
Por padrão, o agente da MSAL não captura dados pessoais ou organizacionais altamente confidenciais. A biblioteca oferece a opção para habilitar o registro em log de dados pessoais e organizacionais, se você optar por fazer isso.

## <a name="logging-in-msalnet"></a>Registro em log no MSAL.NET
Na MSAL 3.x, o registro em log é definido por aplicativo na criação do aplicativo usando o modificador de construtor `.WithLogging`. Esse método usa parâmetros opcionais:

- *Level* permite que você decida qual nível de registro em log deseja. Configurá-lo como Erros só registrará erros
- *PiiLoggingEnabled* permite registrar dados pessoais e organizacionais, se definido como true. Por padrão, ele fica definido como false, para que seu aplicativo não registre dados pessoais.
- *LogCallback* é definido como um delegado que faz o registro em log. Se *PiiLoggingEnabled* for true, esse método receberá as mensagens duas vezes: uma vez com o parâmetro *containsPii* igual a false e a mensagem sem dados pessoais e uma segunda vez com o parâmetro *containsPii* igual a true e a mensagem podendo conter dados pessoais. Em alguns casos (quando a mensagem não contém dados pessoais), a mensagem será a mesma.
- *DefaultLoggingEnabled* permite que o registro em log padrão na plataforma. Por padrão, é false. Se você defini-lo como true, ele usará o rastreamento de eventos em aplicativos de área de trabalho/UWP, NSLog no iOS e logcat no Android.

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

 ## <a name="logging-in-msaljs"></a>Como fazer registro em log no MSAL.js

 Você pode habilitar o registro em log em MSAL.js passando um objeto logger durante a configuração para criar uma instância `UserAgentApplication`. O objeto logger tem as seguintes propriedades:

- *localCallback*: uma instância de retorno de chamada que pode ser fornecida pelo desenvolvedor para consumir e publicar os logs de uma maneira personalizada. Implemente o método localCallback, dependendo de como você deseja redirecionar os logs.

- *level* (opcional): o nível de log configurável. Os níveis de log compatíveis são: Erro, Aviso, Informações, Detalhado. O valor padrão é Informações.

- *piiLoggingEnabled* (opcional): habilita o registro de dados pessoais e organizacionais, se definido como true. Por padrão, ele fica definido como false para que seu aplicativo não registre dados pessoais. Logs de dados pessoais nunca são gravados em saídas padrão como Console, Logcat ou NSLog. O padrão é definido como false.

- *correlationId* (opcional): um identificador exclusivo, usado para mapear a solicitação com a resposta para fins de depuração. O padrão é guid RFC4122 versão 4 (128 bits).

```javascript

function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
    system: {
        logger: {
            localCallback: loggerCallback,
            level: Msal.LogLevel.Verbose,
            piiLoggingEnabled: false,
            correlationId: '1234'
        }
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```
