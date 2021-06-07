---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 73216b1b089444c1dc92bbe73ed07895de3711b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98951505"
---
## <a name="configure-the-relying-party-policy"></a>Configurar a política de terceira parte confiável

A política de terceira parte confiável, por exemplo [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml), especifica a jornada do usuário que Azure ad B2C será executado. Localize o elemento **DefaultUserJourney** na terceira parte [confiável](../articles/active-directory-b2c/relyingparty.md). Atualize o  **referenceid** para corresponder à ID do percurso do usuário, na qual você adicionou o provedor de identidade. 

No exemplo a seguir, para o percurso do `CustomSignUpOrSignIn` usuário, o **referenceid** é definido como `CustomSignUpOrSignIn` :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>Carregar a política personalizada

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **Identity Experience Framework**.
1. Selecione **carregar política personalizada** e, em seguida, carregue os dois arquivos de política que você alterou, na seguinte ordem: a política de extensão, por exemplo `TrustFrameworkExtensions.xml` , a política de terceira parte confiável, como `SignUpSignIn.xml` .



