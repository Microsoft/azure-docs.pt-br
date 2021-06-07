---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: 4f6ba0892438d49dcc982a01a6b30dfa36ed43b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98674211"
---
## <a name="add-a-user-journey"></a>Adicionar uma jornada do usuário 

Neste ponto, o provedor de identidade foi configurado, mas ainda não está disponível em nenhuma das páginas de entrada. Se você não tiver seu próprio percurso de usuário personalizado, crie uma duplicata de um percurso de usuário de modelo existente, caso contrário, continue na próxima etapa. 

1. Abra o arquivo *TrustFrameworkBase.xml* do starter pack.
1. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
1. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
1. Cole todo o conteúdo do elemento **UserJourney** que você copiou como filho do elemento **UserJourneys**.
1. Renomeie a ID da jornada do usuário. Por exemplo, `Id="CustomSignUpSignIn"`.

## <a name="add-the-identity-provider-to-a-user-journey"></a>Adicionar o provedor de identidade a um percurso do usuário 

Agora que você tem uma jornada do usuário, adicione o novo provedor de identidade ao percurso do usuário. Primeiro, adicione um botão de entrada e, em seguida, vincule o botão a uma ação. A ação é o perfil técnico criado anteriormente.

1. Localize o elemento etapa de orquestração que inclui `Type="CombinedSignInAndSignUp"` ou `Type="ClaimsProviderSelection"` no percurso do usuário. Geralmente é a primeira etapa de orquestração. O elemento **ClaimsProviderSelections** contém uma lista de provedores de identidade com os quais um usuário pode se conectar. A ordem dos elementos controla a ordem dos botões de entrada apresentados ao usuário. Adicione um elemento XML **ClaimsProviderSelection** . Defina o valor de **TargetClaimsExchangeId** como um nome amigável.

1. Na próxima etapa de orquestração, adicione um elemento **ClaimsExchange** . Defina a **ID** como o valor da ID de troca de declarações de destino. atualize o valor de **TechnicalProfileReferenceId** para a ID do perfil técnico criado anteriormente.

O XML a seguir demonstra as duas primeiras etapas de orquestração de um percurso do usuário com o provedor de identidade: