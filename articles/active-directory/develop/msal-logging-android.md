---
title: Registrando erros e exceções no MSAL para Android.
titleSuffix: Microsoft identity platform
description: Saiba como registrar erros e exceções no MSAL para Android.
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
ms.openlocfilehash: ce0929adbb2b0213cfd4ee61fe795a2d5f33c648
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954877"
---
# <a name="logging-in-msal-for-android"></a>Registrar em log em MSAL para Android

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="logging-in-msal-for-android-using-java"></a>Registro em log no MSAL para Android usando Java

Ative o logon na criação do aplicativo Criando um retorno de chamada de log. O retorno de chamada usa estes parâmetros:

- `tag` é uma cadeia de caracteres passada para o retorno de chamada pela biblioteca. Ele é associado à entrada de log e pode ser usado para classificar mensagens de registro em log.
- `logLevel` permite que você decida qual nível de log você deseja. Os níveis de log com suporte são: `Error` , `Warning` , `Info` e `Verbose` .
- `message` é o conteúdo da entrada de log.
- `containsPII` Especifica se as mensagens que contêm dados pessoais ou dados organizacionais são registradas. Por padrão, isso é definido como false, para que seu aplicativo não Registre dados pessoais. Se `containsPII` for `true` , esse método receberá as mensagens duas vezes: uma vez com o `containsPII` parâmetro definido como `false` e o `message` sem dados pessoais, e uma segunda vez com o `containsPii` parâmetro definido como `true` e a mensagem poderá conter dados pessoais. Em alguns casos (quando a mensagem não contém dados pessoais), a mensagem será a mesma.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

Por padrão, o MSAL Logger não capturará nenhuma informação de identificação pessoal ou informações de identificação organizacional.
Para habilitar o log de informações de identificação pessoal ou informações de identificação organizacional:

```java
Logger.getInstance().setEnablePII(true);
```

Para desabilitar o registro em log de dados pessoais e dados da organização:

```java
Logger.getInstance().setEnablePII(false);
```

Por padrão, o log em logcat está desabilitado. Para habilitar:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de código, consulte [exemplos de código da plataforma Microsoft Identity](sample-v2-code.md).