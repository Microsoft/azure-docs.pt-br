---
title: Configurar o comportamento da sessão-Azure Active Directory B2C | Microsoft Docs
description: Configurar o comportamento da sessão no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b18a7d98654422951773c0a5497f69db93782f51
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849442"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Configurar o comportamento da sessão no Azure Active Directory B2C

Esse recurso oferece um controle refinado, [com base em cada fluxo de usuário](active-directory-b2c-reference-policies.md), de:

- Vida útil de sessões dos aplicativos Web gerenciados pelo Azure AD B2C.
- Comportamento de logon único (SSO) em vários aplicativos e políticas no locatário do Azure AD B2C.

Essas configurações não estão disponíveis para os fluxos de usuário de redefinição de senha.

O Azure AD B2C dá suporte ao [protocolo de autenticação do OpenID Connect](active-directory-b2c-reference-oidc.md) para habilitar o logon seguro em aplicativos Web. Você pode usar as seguintes propriedades para gerenciar sessões de aplicativos da Web:

## <a name="session-behavior-properties"></a>Propriedades de comportamento da sessão

- **Duração da sessão do aplicativo Web (minutos)** - A vida útil do cookie de sessão do Azure AD B2C armazenado no navegador do usuário após a autenticação bem-sucedida.
    - Padrão = 1440 minutos.
    - Mínimo (inclusive) = 15 minutos.
    - Máximo (inclusive) = 1440 minutos.
- **Tempo limite da sessão do aplicativo da Web**: se essa opção for definida como **Absoluta**, o usuário será forçado a autenticar novamente após o período especificado por **duração da sessão do aplicativo Web (minutos)** decorre. Se essa opção estiver definida como **Rolling** (a configuração padrão), o usuário permanecerá conectado desde que esteja ativo de forma contínua em seu aplicativo Web.
- **Configuração de logon único** Se você tiver vários aplicativos e fluxos de usuário em seu locatário B2C, poderá gerenciar interações de usuário entre eles usando a propriedade de **configuração de logon único** . Você pode definir a propriedade com uma das seguintes configurações:
    - **Locatário** -essa configuração é o padrão. O uso dessa configuração permite a vários aplicativos e fluxos de usuário em seu locatário B2C compartilhar a mesma sessão de usuário. Por exemplo, depois que um usuário faz login em um aplicativo, o usuário também pode fazer login em outro, a Contoso Pharmacy, ao acessá-lo.
    - **Aplicação** - Esta configuração permite que você mantenha uma sessão de usuário exclusivamente para um aplicativo, independente de outros aplicativos. Por exemplo, se você quiser que o usuário entre na Contoso Pharmacy (com as mesmas credenciais), mesmo que o usuário já esteja conectado ao Contoso Shopping, outro aplicativo no mesmo locatário B2C.
    - **Política** - Essa configuração permite que você mantenha uma sessão de usuário exclusivamente para um fluxo de usuário, independentemente dos aplicativos que a usam. Por exemplo, se o usuário já tiver feito login e concluído uma etapa de autenticação de vários fatores (MFA), o usuário poderá ter acesso a partes de maior segurança de vários aplicativos, desde que a sessão vinculada ao fluxo de usuário não expire.
    - **Desabilitado** – essa configuração orces o usuário a percorrer o fluxo do usuário inteira em cada execução da política.

Os casos de uso a seguir são ativados usando estas propriedades:

- Atenda aos requisitos de conformidade e segurança de seu setor definindo adequadamente a vida útil da sessão do aplicativo Web.
- Forçar autenticação após um período de tempo definido durante a interação do usuário com uma parte de alto nível de segurança do seu aplicativo web.

## <a name="configure-the-properties"></a>Configurar as propriedades

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C clicando nos **filtros de pastas e de assinatura** no menu superior e escolhendo o diretório que contém seu locatário do Azure AD B2C.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **fluxos de usuário (políticas)** .
5. Abra o fluxo de usuário que você criou anteriormente.
6. Selecione **Propriedades**.
7. Configure a **vida útil da sessão do aplicativo Web (minutos)** , o **tempo limite da sessão do aplicativo Web**, **a configuração de logon único**e **exija o token de ID em solicitações de logout** , conforme necessário.

    ![Configurações de propriedade de comportamento de sessão no portal do Azure](./media/session-behavior/session-behavior.png)

8. Clique em **Salvar**.
