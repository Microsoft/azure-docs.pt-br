---
title: Tratar erros e exceções em MSAL para Python
titleSuffix: Microsoft identity platform
description: Saiba como lidar com erros e exceções, desafios de declarações de acesso condicional e novas tentativas no MSAL para aplicativos Python.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, rayluo
ms.custom: aaddev
ms.openlocfilehash: 3046787393d38ed60b54236f33acc065db90321d
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761092"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>Tratar erros e exceções em MSAL para Python

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>Tratamento de erro no MSAL para Python

Na MSAL para Python, a maioria dos erros é transmitida como um valor de retorno da chamada à API. O erro é representado como um dicionário contendo a resposta JSON da plataforma de identidade da Microsoft.

* Uma resposta bem-sucedida contém a chave `"access_token"`. O formato da resposta é definido pelo protocolo OAuth2. Para obter mais informações, consulte [5.1 Resposta bem-sucedida](https://tools.ietf.org/html/rfc6749#section-5.1).
* Uma resposta de erro contém `"error"` e, geralmente, `"error_description"`. O formato da resposta é definido pelo protocolo OAuth2. Para obter mais informações, consulte [5.2 Resposta de erro](https://tools.ietf.org/html/rfc6749#section-5.2).

Quando um erro é retornado, a chave `"error_description"` conterá uma mensagem legível, a qual, por sua vez, normalmente contém um código de erro da plataforma de identidade da Microsoft. Para obter detalhes sobre os diversos códigos de erro, consulte [Códigos de erro de autenticação e autorização](./reference-aadsts-error-codes.md).

Na MSAL para Python, as exceções são raras, porque a maioria dos erros é tratada retornando um valor de erro. A `ValueError` exceção é gerada apenas quando há um problema com o modo como você está tentando usar a biblioteca, como quando os parâmetros da API estão malformados.

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Próximas etapas

Considere habilitar o [registro em log no MSAL para Python](msal-logging-python.md) para ajudá-lo a diagnosticar e depurar problemas.
