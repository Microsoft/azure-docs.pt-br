---
title: Registrando erros e exceções em MSAL para Java
titleSuffix: Microsoft identity platform
description: Saiba como registrar erros e exceções no MSAL para Java
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
ms.openlocfilehash: d3fa13a6751b2d8acf1fc99aecbf174f1823fb0b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954911"
---
# <a name="logging-in-msal-for-java"></a>Registrar em log em MSAL para Java

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-java-logging"></a>MSAL para log de Java

O MSAL para Java permite que você use a biblioteca de log que você já está usando com seu aplicativo, desde que seja compatível com SLF4J. O MSAL para Java usa o [log simples fachada para Java](http://www.slf4j.org/) (SLF4J) como uma fachada ou abstração simples para várias estruturas de log, como [Java. util. Logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [Logback](http://logback.qos.ch/) e [Log4J](https://logging.apache.org/log4j/2.x/). O SLF4J permite que o usuário conecte a estrutura de registro em log desejada no momento da implantação.

Por exemplo, para usar Logback como a estrutura de log em seu aplicativo, adicione a dependência Logback ao arquivo Maven POM para seu aplicativo:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Em seguida, adicione o arquivo de configuração Logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

O SLF4J é automaticamente associado ao Logback no momento da implantação. Os logs do MSAL serão gravados no console do.

Para obter instruções sobre como associar a outras estruturas de registro em log, consulte o [manual SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Informações pessoais e da organização

Por padrão, o log do MSAL não captura nem registra dados pessoais ou organizacionais. No exemplo a seguir, os dados de log pessoal ou organizacional estão desativados por padrão:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Ative o registro em log de dados pessoais e organizacionais Configurando `logPii()` no construtor de aplicativos cliente. Se você ativar o registro em log de dados pessoais ou organizacionais, seu aplicativo deverá assumir a responsabilidade de lidar com segurança dados altamente confidenciais e obedecer a quaisquer requisitos regulatórios.

No exemplo a seguir, os dados de log pessoal ou organizacional estão habilitados:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de código, consulte [exemplos de código da plataforma Microsoft Identity](sample-v2-code.md).