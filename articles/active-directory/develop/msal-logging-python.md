---
title: Registrando erros e exceções em MSAL para Python
titleSuffix: Microsoft identity platform
description: Saiba como registrar erros e exceções no MSAL para Python
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
ms.openlocfilehash: 8488325613b05d54b352a19a06860e08f1779877
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063107"
---
# <a name="logging-in-msal-for-python"></a>Registrar em log em MSAL para Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL para registro em log do Python

O registro em log no MSAL Python usa o mecanismo de registro em log do Python padrão, por exemplo, `logging.info("msg")` você pode configurar o registro em log do MSAL da seguinte maneira (e vê-lo em ação no [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Habilitar log de depuração para todos os módulos

Por padrão, o registro em log em qualquer script Python é desativado. Se você quiser habilitar o log de depuração para todos os módulos em todo o script do Python, use:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Silenciar apenas o registro em log MSAL

Para silenciar apenas o registro em log da biblioteca MSAL, ao mesmo tempo em que habilita o log de depuração em todos os outros módulos em seu script Python, desative o agente usado pelo Python MSAL:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Dados pessoais e organizacionais em Python

O MSAL para Python não registra dados pessoais ou dados organizacionais. Não há nenhuma propriedade para ativar ou desativar o log de dados pessoais ou da organização.

Você pode usar o registro em log do Python padrão para registrar o que desejar, mas você é responsável por lidar com segurança dados confidenciais e seguindo os requisitos regulatórios.

Para obter mais informações sobre o registro em log em Python, consulte  [log do Python: instruções](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de código, consulte [exemplos de código da plataforma Microsoft Identity](sample-v2-code.md).
