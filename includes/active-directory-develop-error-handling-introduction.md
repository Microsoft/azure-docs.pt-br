---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760651"
---
Este artigo fornece uma visão geral dos diferentes tipos de erros e das recomendações para lidar com erros comuns de entrada.

## <a name="msal-error-handling-basics"></a>Noções básicas de tratamento de erros da MSAL

As exceções na MSAL (biblioteca de autenticação da Microsoft) são destinadas para os desenvolvedores de aplicativos solucionarem problemas, não para a exibição de usuários finais. Mensagens de exceção não são localizadas.

Ao processar exceções e erros, é possível usar o próprio tipo de exceção e o código de erro para distinguir entre exceções.  Para obter uma lista de códigos de erro, consulte [autenticação do Azure AD e códigos de erro de autorização](../articles/active-directory/develop/reference-aadsts-error-codes.md).

Durante a experiência de entrada, você pode encontrar erros sobre consentimentos, Acesso Condicional (MFA, Gerenciamento de Dispositivo, Restrições baseadas em local), emissão e resgate de token e propriedades de usuário.

A seção a seguir fornece mais detalhes sobre o tratamento de erros para seu aplicativo.