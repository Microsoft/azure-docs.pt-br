---
title: "Configuração do token, sessão e logon único - Azure AD B2C | Microsoft Docs"
description: "Token, sessão e configuração de logon único no Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: e78e6344-0089-49bf-8c7b-5f634326f58c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: parakhj
ms.openlocfilehash: 256c93e5c343cba022599f8e13c5b7616bfa8b58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure Active Directory B2C: Configuração de token, de sessão e de logon único

Esse recurso oferece um controle refinado, com base [em cada política](active-directory-b2c-reference-policies.md), de:

1. Vida útil dos tokens de segurança emitidos pelo Azure Active Directory (Azure AD) B2C.
2. Vida útil de sessões dos aplicativos Web gerenciados pelo Azure AD B2C.
3. Formatos de declarações importantes nos tokens de segurança emitidos pelo Azure AD B2C.
4. Comportamento do SSO (logon único) em vários aplicativos e políticas em seu locatário B2C.

Para políticas internas, você pode usar esse recurso em seu diretório do Azure AD B2C da seguinte maneira:

1. Execute estas etapas para [navegar até o menu de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no Portal do Azure.
2. Clique em **Políticas de inscrição ou de entrada**. *Observação: você pode usar esse recurso em qualquer tipo de política, não apenas em **Políticas de inscrição ou entrada***.
3. Clique em uma política para abri-la. Por exemplo, clique em **B2C_1_SiUpIn**.
4. Clique em **Editar** na parte superior do menu.
5. Clique em **Configuração de token, de sessão e de logon único**.
6. Faça as alterações desejadas. Saiba mais sobre as propriedades disponíveis nas seções seguintes.
7. Clique em **OK**.
8. Clique em **Salvar** na parte superior do menu.

## <a name="token-lifetimes-configuration"></a>Configuração da vida útil de tokens

O Azure AD B2C dá suporte ao [protocolo de autorização OAuth 2.0](active-directory-b2c-reference-protocols.md) para habilitar o acesso seguro a recursos protegidos. Para implementar esse suporte, o Azure AD B2C emite vários [tokens de segurança](active-directory-b2c-reference-tokens.md). Estas são as propriedades que você pode usar para gerenciar as vida útil dos tokens de segurança emitidos pelo Azure AD B2C:

* **Vida útil (minutos) dos tokens de acesso e de ID**: a vida útil do token de portador do OAuth 2.0 usado para acessar um recurso protegido.
  * Padrão = 60 minutos.
  * Mínimo (inclusive) = 5 minutos.
  * Máximo (inclusive) = 1440 minutos.
* **Vida útil do token de atualização (dias)**: o período de tempo máximo que um token de atualização pode ser usado para adquirir um novo acesso ou o token de ID (e, opcionalmente, um novo token de atualização se seu aplicativo tiver recebido o escopo `offline_access`).
  * Padrão = 14 dias.
  * Mínimo (inclusive) = 1 dia.
  * Máximo (inclusive) = 90 dias.
* **Vida útil da janela deslizante do token de atualização (dias)**: decorrido esse período de tempo, o usuário será forçado a autenticar novamente, independentemente do período de validade do token de atualização mais recente adquirido pelo aplicativo. Ele só poderá ser fornecido se a opção estiver definida como **Bounded**. Ele precisa ser maior ou igual ao valor **Vida útil do token de atualização (dias)** . Se a opção estiver definida como **Unbounded**, você não poderá fornecer um valor específico.
  * Padrão = 90 dias.
  * Mínimo (inclusive) = 1 dia.
  * Máximo (inclusive) = 365 dias.

Estes são alguns casos de uso que você pode habilitar usando essas propriedades:

* Permitir que um usuário permaneça conectado a um aplicativo móvel indefinidamente, desde que ele esteja sempre ativo no aplicativo. Isso pode ser feito ao definir a opção **Vida útil da janela deslizante do token de atualização (dias)** para **Unbounded** na sua política de entrada.
* Atenda aos requisitos de conformidade e segurança de seu setor definindo a vida de tokens de acesso adequadamente.

    > [!NOTE]
    > Essas configurações não estão disponíveis para as políticas de redefinição de senha.
    > 
    > 

## <a name="token-compatibility-settings"></a>Configurações de compatibilidade de token

Fizemos alterações de formatação em declarações importantes em tokens de segurança emitidos pelo Azure AD B2C. Isso foi feito para melhorar nosso suporte a protocolos padrão e para melhor interoperabilidade com bibliotecas de identidade de terceiros. No entanto, para evitar a interrupção dos aplicativos existentes, criamos as propriedades a seguir para permitir que os clientes aceitem como necessário:

* **Declaração do emissor (iss)**: identifica o locatário do Azure AD B2C que emitiu o token.
  * `https://login.microsoftonline.com/{B2C tenant GUID}/v2.0/`: é o valor padrão.
  * `https://login.microsoftonline.com/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`: esse valor inclui ID para o locatário B2C e a política usada na solicitação de token. Se seu aplicativo ou biblioteca precisar do Azure AD B2C para ser compatível com as [especificações do OpenID Connect Discovery 1.0](http://openid.net/specs/openid-connect-discovery-1_0.html), use esse valor.
* **Declaração de assunto (sub)**: identifica a entidade, ou seja, o usuário, para o qual o token declara informações.
  * **ObjectID**: é o valor padrão. Ele ocupa a ID de objeto do usuário no diretório para a declaração `sub` no token.
  * **Não tem suporte**: é fornecido somente para compatibilidade com versões anteriores, e recomendamos que você alterne para **ObjectID** assim que for possível.
* **Declaração que representa a ID da política**: identifica o tipo de declaração na qual a ID da política usada na solicitação de token é preenchida.
  * **tfp**: é o valor padrão.
  * **Acr**: é fornecido somente para compatibilidade com versões anteriores, e recomendamos que você alterne para `tfp` assim que for possível.

## <a name="session-behavior"></a>Comportamento da sessão

O Azure AD B2C dá suporte ao [protocolo de autenticação do OpenID Connect](active-directory-b2c-reference-oidc.md) para habilitar o logon seguro em aplicativos Web. Estas são as propriedades que você pode usar para gerenciar as sessões do aplicativo Web:

* **Vida útil da sessão do aplicativo Web (minutos)**: o tempo de vida do cookie de sessão do Azure AD B2C armazenado no navegador do usuário após a autenticação bem-sucedida.
  * Padrão = 1440 minutos.
  * Mínimo (inclusive) = 15 minutos.
  * Máximo (inclusive) = 1440 minutos.
* **Tempo limite de sessão do aplicativo Web**: se essa opção for definida como **Absolute**, o usuário será forçado a autenticar novamente após o período de tempo especificado pelo **Tempo limite de sessão de aplicativo Web (minutos)** expirar. Se essa opção estiver definida como **Rolling** (a configuração padrão), o usuário permanecerá conectado desde que esteja ativo de forma contínua em seu aplicativo Web.

Estes são alguns casos de uso que você pode habilitar usando essas propriedades:

* Atenda aos requisitos de conformidade e segurança de seu setor definindo adequadamente a vida útil da sessão do aplicativo Web.
* Force uma nova autenticação após um período de tempo definido durante a interação do usuário com uma parte do alto nível de segurança de seu aplicativo Web. 

    > [!NOTE]
    > Essas configurações não estão disponíveis para as políticas de redefinição de senha.
    > 
    > 

## <a name="single-sign-on-sso-configuration"></a>Configuração de SSO (Logon Único)
Se você tiver vários aplicativos e políticas em seu locatário B2C, poderá gerenciar interações do usuário entre eles usando a propriedade **Configuração de logon único** . Você pode definir a propriedade com uma das seguintes configurações:

* **Locatário**: essa é a configuração padrão. O uso dessa configuração permite a vários aplicativos e políticas em seu locatário B2C compartilhar a mesma sessão de usuário. Por exemplo, quando um usuário entra em um aplicativo, Contoso Shopping, ele também pode entrar sem problemas em outro aplicativo, Contoso Pharmacy, ao acessá-lo.
* **Aplicativo**: isso permite que você mantenha uma sessão de usuário exclusivamente para um aplicativo, independente de outros aplicativos. Por exemplo, se você deseja que o usuário entre em Contoso Pharmacy (com as mesmas credenciais), mesmo que ele já esteja conectado a Contoso Shopping, outro aplicativo no mesmo locatário do B2C. 
* **Política**: isso permite que você mantenha uma sessão de usuário exclusivamente para uma política, independente dos aplicativos que estejam a usá-la. Por exemplo, se o usuário já tiver entrado e concluído uma etapa da MFA (Multi-Factor Authentication), ele pode receber acesso à partes com maior segurança de vários aplicativos enquanto a sessão vinculada à política não expirar.
* **Desabilitado**: isso força o usuário a percorrer toda a jornada do usuário em cada execução da política. Por exemplo, isso permite que vários usuários se inscrevam em seu aplicativo (em um cenário de área de trabalho compartilhada), mesmo quando um usuário permanecer conectado o tempo todo.

    > [!NOTE]
    > Essas configurações não estão disponíveis para as políticas de redefinição de senha.
    > 
    > 

