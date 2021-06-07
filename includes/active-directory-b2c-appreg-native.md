---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/02/2020
ms.author: mimart
ms.openlocfilehash: 2ad6b90616077a6d18550e86692b109bda622af7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026740"
---
Para registrar um aplicativo no locatário do Azure AD B2C, você pode usar a nossa nova experiência unificada de **Registros de aplicativos** ou a nossa experiência herdada **Aplicativos (Herdado)** . [Saiba mais sobre a nova experiência](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registros de aplicativo](#tab/app-reg-ga/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de aplicativo** e **Novo registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *nativeapp1*.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional ou qualquer provedor de identidade**.
1. Em **URI de Redirecionamento**, use a lista suspensa para selecionar **Cliente Público/Nativo (móvel e área de trabalho)** .
1. Insira um URI de redirecionamento com um esquema exclusivo. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Há considerações importantes ao escolher um URI de redirecionamento:
    * **Desenvolvimento** Para uso de desenvolvimento, é possível definir o URI de redirecionamento para `http://localhost`, o que faz com que o Azure AD B2C respeite qualquer porta na solicitação. Se o URI registrado contiver uma porta, Azure AD B2C usará apenas essa porta. Por exemplo, se o URI de redirecionamento registrado for `http://localhost`, o URI de redirecionamento na solicitação poderá ser `http://localhost:<randomport>`. Se o URI de redirecionamento registrado for `http://localhost:8080`, o URI de redirecionamento na solicitação precisará ser `http://localhost:8080`.
    * **Exclusivo**: O esquema do URI de redirecionamento deve ser exclusivo para cada aplicativo. No exemplo `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` é o esquema. Esse padrão deve ser seguido. Se dois aplicativos compartilharem o mesmo esquema, o usuário terá a opção de escolher um aplicativo. Se o usuário escolher incorretamente, o acesso falhará.
    * **Completo**: O URI de redirecionamento deve ter um esquema e um caminho. O caminho deve conter pelo menos uma barra após o domínio. Por exemplo, `//oauth/` funciona e `//oauth` falha. Não inclua caracteres especiais no URI, por exemplo, sublinhados.
1. Em **Permissões**, marque a caixa de seleção *Dar consentimento do administrador às permissões OpenID e offline_access*.
2. Selecione **Registrar**.

#### <a name="applications-legacy"></a>[Aplicativos (Herdado)](#tab/applications-legacy/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Selecione **Aplicativos (Herdado)** e, em seguida, selecione **Adicionar**.
1. Insira um nome para o aplicativo. Por exemplo, *nativeapp1*.
1. Para **Cliente nativo**, selecione **Sim**.
1. Insira um **URI de redirecionamento personalizado** com um esquema exclusivo. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Há duas considerações importantes ao escolher um URI de redirecionamento:
    * **Exclusivo**: O esquema do URI de redirecionamento deve ser exclusivo para cada aplicativo. No exemplo `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` é o esquema. Esse padrão deve ser seguido. Se dois aplicativos compartilharem o mesmo esquema, o usuário terá a opção de escolher um aplicativo. Se o usuário escolher incorretamente, o acesso falhará.
    * **Completo**: O URI de redirecionamento deve ter um esquema e um caminho. O caminho deve conter pelo menos uma barra após o domínio. Por exemplo, `//oauth/` funciona e `//oauth` falha. Não inclua caracteres especiais no URI, por exemplo, sublinhados.
1. Selecione **Criar**.