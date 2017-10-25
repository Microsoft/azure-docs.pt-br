---
title: "Introdução ao servidor Web ASP.NET no Azure AD v2 – Teste | Microsoft Docs"
description: "Implementando a opção Entrar com uma Conta da Microsoft em uma solução ASP.NET com um aplicativo tradicional baseado em navegador da Web usando o padrão OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 00cb963e85111274c36c3a84489894811ad2dabd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
## <a name="test-your-code"></a>Testar seu código

Pressione `F5` para executar o projeto no Visual Studio. O navegador será aberto e direcionará você para *http://localhost:{port}*, em que você verá o botão *Entrar com uma conta da Microsoft*. Vá em frente e clique nele para entrar.

Quando estiver pronto para testar, use uma conta corporativa ou de estudante (Azure Active Directory) ou uma conta pessoal (live.com, outlook.com) para se conectar. 

![Janela do navegador Entrar com uma Conta da Microsoft](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin.png)

![Janela do navegador Entrar com uma Conta da Microsoft](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Resultados esperados
Depois de se conectar, o usuário é redirecionado para a home page do site, que é a URL HTTPS especificada nas informações de registro do aplicativo no Portal de Registro de Aplicativos da Microsoft. Essa página agora mostra *Olá, {User}* e um link para a saída, além de um link para consultar as declarações do usuário – que é um link para o controlador Autorizar criado anteriormente.

### <a name="see-users-claims"></a>Consultar as declarações do usuário
Selecione o hiperlink para consultar as declarações do usuário. Isso leva você para o controlador e a exibição que está disponível somente para usuários autenticados.

#### <a name="expected-results"></a>Resultados esperados
 Você deverá ver uma tabela que contém as propriedades básicas do usuário conectado:

| Propriedade | Valor | Descrição|
|---|---|---|
| Nome | {Nome completo do usuário} | Nome e sobrenome do usuário
|Nome de Usuário | <span>user@domain.com</span>| O nome de usuário usado para identificar o usuário conectado
| Assunto| {Subject}|Uma cadeia de caracteres para identificar exclusivamente o logon do usuário na Web|
| ID do locatário| {Guid}| Um *guid* para representar exclusivamente a organização do Azure Active Directory do usuário.|

Além disso, você verá uma tabela, incluindo todas as declarações de usuário incluídas na solicitação de autenticação. Para obter uma lista de todas as declarações em um Token de ID e uma explicação, veja este [artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "Lista de declarações no Token de ID").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Testar o acesso a um método que tem um atributo *[Authorize]* (opcional)
Nesta etapa, você testará o acesso ao controlador Autenticado como um usuário anônimo:<br/>
Selecione o link para desconectar o usuário e concluir o processo de saída.<br/>
Agora no navegador, digite http://localhost:{port}/authenticated para acessar o controlador que está protegido com o atributo `[Authorize]`

#### <a name="expected-results"></a>Resultados esperados
Você deverá receber o prompt exigindo a autenticação para ver a exibição.

## <a name="additional-information"></a>Informações adicionais

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Proteger todo o seu site
Para proteger todo o seu site, adicione o `AuthorizeAttribute` a `GlobalFilters` no método `Global.asax` `Application_Start`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

> [!NOTE]
> **Como restringir os usuários a somente uma organização para entrar em seu aplicativo**

> Por padrão, contas pessoais (incluindo outlook.com, live.com e outras), bem como contas corporativas ou de estudante de qualquer empresa ou organização que foi integrada ao Azure Active Directory, podem entrar no aplicativo. 

> Se você desejar que o aplicativo aceite conexões de apenas uma organização do Azure Active Directory, substitua o parâmetro `Tenant` em *web.config* de `Common` para o nome de locatário da organização – por exemplo, *contoso.onmicrosoft.com*. Depois disso, altere o argumento `ValidateIssuer` na *Classe de Inicialização OWIN* para `true`.

> Para permitir usuários apenas de uma lista de organizações específicas, defina `ValidateIssuer` como verdadeiro e use o parâmetro `ValidIssuers` para especificar uma lista de organizações.

> Outra opção é implementar um método personalizado para validar os emissores usando o parâmetro IssuerValidator. Para saber mais sobre `TokenValidationParameters`, veja [este artigo do MSDN](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "TokenValidationParameters").

