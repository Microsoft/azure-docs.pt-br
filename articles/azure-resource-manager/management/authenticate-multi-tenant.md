---
title: Autenticar entre locatários
description: Descreve como o Azure Resource Manager trata de solicitações de autenticação entre locatários.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75478819"
---
# <a name="authenticate-requests-across-tenants"></a>Autenticar solicitações entre locatários

Ao criar um aplicativo multilocatário, talvez você precise lidar com solicitações de autenticação para recursos que estão em diferentes locatários. Um cenário comum é quando uma máquina virtual em um locatário deve ingressar em uma rede virtual em outro locatário. O Azure Resource Manager fornece um valor de cabeçalho para armazenar os tokens auxiliares para autenticar as solicitações em locatários diferentes.

## <a name="header-values-for-authentication"></a>Valores de cabeçalho para autenticação

A solicitação tem os seguintes valores de cabeçalho de autenticação:

| Nome do cabeçalho | Descrição | Valor de exemplo |
| ----------- | ----------- | ------------ |
| Autorização | Token primário | &lt;Token primário&gt; portador |
| x-ms-authorization-auxiliary | Tokens auxiliares | Portador &lt; auxiliar-token1 &gt; , EncryptedBearer &lt; auxiliar-token2 &gt; , portador &lt; auxiliar-token3&gt; |

O cabeçalho auxiliar pode conter até três tokens auxiliares. 

No código do seu aplicativo multilocatário, obtenha o token de autenticação para outros locatários e armazene-o nos cabeçalhos auxiliares. Todos os tokens devem ser do mesmo usuário ou aplicativo. O usuário ou aplicativo deve ter sido convidado como um convidado para os outros locatários.

## <a name="processing-the-request"></a>Processando a solicitação

Quando seu aplicativo envia uma solicitação para o Resource Manager, a solicitação é executada sob a identidade do token primário. O token primário deve ser válido e não expirado. Esse token deve ser de um locatário que pode gerenciar a assinatura.

Quando a solicitação faz referência a um recurso de um locatário diferente, o Resource Manager verifica os tokens auxiliares para determinar se a solicitação pode ser processada. Todos os tokens auxiliares no cabeçalho devem ser válidos e não estar expirados. Se nenhum token tiver expirado, o Resource Manager retornará um código de resposta 401. A resposta inclui a ID do cliente e a ID do locatário do token que não é válido. Se o cabeçalho auxiliar contiver um token válido para o locatário, a solicitação de locatário cruzada será processada.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre solicitações de autenticação, consulte [fluxos de autenticação e cenários de aplicativos](../../active-directory/develop/authentication-flows-app-scenarios.md).
* Para obter mais informações sobre tokens, veja [Tokens de acesso do Azure Active Directory](../../active-directory/develop/access-tokens.md).
