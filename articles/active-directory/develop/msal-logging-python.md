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
ms.openlocfilehash: 1d52b017f94785f5fb25a25f127ae52d96e97d8b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578746"
---
# <a name="logging-in-msal-for-python"></a>Registrar em log em MSAL para Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL para registro em log do Python

O registro em log no MSAL para Python aproveita o [módulo de log na biblioteca padrão do Python](https://docs.python.org/3/library/logging.html). Você pode configurar o log do MSAL da seguinte maneira (e vê-lo em ação no [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Habilitar log de depuração para todos os módulos

Por padrão, o registro em log em qualquer script Python é desativado. Se você quiser habilitar o log detalhado para **todos os** módulos do Python em seu script, use `logging.basicConfig` com um nível de `logging.DEBUG` :

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

Isso imprimirá todas as mensagens de log dadas ao módulo de log para a saída padrão.

### <a name="configure-msal-logging-level"></a>Configurar o nível de log MSAL

Você pode configurar o nível de log do MSAL para o provedor de log do Python usando o `logging.getLogger()` método com o nome do agente `"msal"` :

```python
import logging

logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="configure-msal-logging-with-azure-app-insights"></a>Configurar o registro em log do MSAL com informações do Azure App

Os logs do Python são fornecidos a um manipulador de log, que por padrão é o `StreamHandler` . Para enviar logs do MSAL para um Application Insights com uma chave de instrumentação, use o `AzureLogHandler` fornecido pela `opencensus-ext-azure` biblioteca.

Para instalar `opencensus-ext-azure` o, adicione o `opencensus-ext-azure` pacote do PyPI às suas dependências ou à instalação do Pip:

```console
pip install opencensus-ext-azure
```

Em seguida, altere o manipulador padrão do `"msal"` provedor de log para uma instância do `AzureLogHandler` com uma chave de instrumentação definida na `APP_INSIGHTS_KEY` variável de ambiente:

```python
import logging
import os

from opencensus.ext.azure.log_exporter import AzureLogHandler

APP_INSIGHTS_KEY = os.getenv('APP_INSIGHTS_KEY')

logging.getLogger("msal").addHandler(AzureLogHandler(connection_string='InstrumentationKey={0}'.format(APP_INSIGHTS_KEY))
```

### <a name="personal-and-organizational-data-in-python"></a>Dados pessoais e organizacionais em Python

O MSAL para Python não registra dados pessoais ou dados organizacionais. Não há nenhuma propriedade para ativar ou desativar o log de dados pessoais ou da organização.

Você pode usar o registro em log do Python padrão para registrar o que desejar, mas você é responsável por lidar com segurança dados confidenciais e seguindo os requisitos regulatórios.

Para obter mais informações sobre o registro em log em Python, consulte  [log do Python: instruções](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de código, consulte [exemplos de código da plataforma Microsoft Identity](sample-v2-code.md).
