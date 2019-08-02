---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 8795c9ab0a4dbb76327d0ead48ed33fb0cff9e86
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172531"
---
## <a name="test-your-code"></a>Testar seu código

Para testar o aplicativo no Visual Studio, pressione **F5** para executar seu projeto. O navegador abre para o local http://<span></span>localhost:{port} e você visualiza o botão **Entrar com a conta da Microsoft**. Selecione o botão para iniciar o processo de entrada.

Quando estiver pronto para executar o teste, use uma conta do Microsoft Azure AD (Microsoft Azure Active Directory) (conta corporativa ou de estudante) ou uma conta pessoal da Microsoft (<span>live.</span>com ou <span>outlook.</span>com) para entrar.

![Entrar com a Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Entre na sua conta da Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Veja os resultados de aplicativo

Depois de entrar, o usuário será redirecionado para a home page do seu site. A home page é a URL HTTPS especificada nas informações de registro do aplicativo no Portal de Registro de Aplicativos da Microsoft. A página inicial inclui uma mensagem de boas-vindas *"Olá \<Usuário>,"* um link para sair e um link para exibir as declarações de usuário. O link para as declarações de usuário navega até o controlador *Declarações* criado anteriormente.

### <a name="browse-to-see-the-users-claims"></a>Navegue para ver as declarações de usuário

Para ver as declarações de usuário, selecione o link para navegar até a exibição do controlador disponível somente para usuários autenticados.

#### <a name="view-the-claims-results"></a>Exibir os resultados de declarações

Depois de navegar para a exibição do controlador, você deverá visualizar uma tabela que contém as propriedades básicas para o usuário:

|Propriedade |Valor |DESCRIÇÃO |
|---|---|---|
|**Nome** |Nome completo do usuário | O nome e sobrenome do usuário.
|**Nome de Usuário** |usuário<span>@domain.com</span> | O nome de usuário que é usado para identificar o usuário.
|**Assunto** |Assunto |Uma cadeia de caracteres que identifica de forma exclusiva o usuário na Web.|
|**ID do locatário** |Guid | Um **guid** que representa exclusivamente a organização do Microsoft Azure Active Directory do usuário.|

Além disso, você deve ver uma tabela de todas as declarações que estão na solicitação de autenticação. Para obter mais informações, consulte a [lista de declarações que estão em um Token de ID do Microsoft Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testar o acesso a um método que tenha um atributo Autorizar (opcional)

Para testar o acesso como um usuário anônimo a um controlador protegido com o atributo `Authorize`, siga estas etapas:

1. Selecione o link para desconectar o usuário e concluir o processo de saída.
2. No navegador, digite http://<span></span>localhost:{port}/claims para acessar o controlador que está protegido com o atributo `Authorize`.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Resultados esperados após o acesso a um controlador protegido

Você será solicitado a autenticar para usar a exibição do controlador protegido.

## <a name="advanced-options"></a>Opções Avançadas

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Proteja todo o seu site
Para proteger todo o site, no arquivo **Global.asax**, adicione o atributo `AuthorizeAttribute` ao filtro `GlobalFilters` no método `Application_Start`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Restringir quem pode se conectar ao seu aplicativo

Por padrão, quando você compila o aplicativo criado por este guia, o aplicativo aceitará conexões de contas pessoais (incluindo outlook.com, live.com e outras), bem como contas corporativas ou de estudante de qualquer empresa ou organização que foi integrada ao Azure Active Directory. Essa é uma opção recomendada para aplicativos SaaS.

Para restringir acesso de entrada de usuário para seu aplicativo, há várias opções disponíveis:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Opção 1: Restrinja usuários da instância do Active Directory de uma única organização entre no seu aplicativo (único locatário)

Essa opção é um cenário comum para *aplicativos de linha de negócios*: Se desejar que seu aplicativo aceite entradas apenas de contas pertencentes a uma instância específica do Azure Active Directory (incluindo *contas convidado* dessa instância), siga estas etapas:

1. No arquivo **web. config**, altere o valor para o parâmetro `Tenant` de `Common` para o nome do locatário da organização, como `contoso.onmicrosoft.com`.
2. Na [classe de inicialização OWIN](#configure-the-authentication-pipeline), defina o argumento `ValidateIssuer` como `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Opção 2: restringir o acesso ao seu aplicativo aos usuários em uma lista específica de organizações

É possível restringir acesso de entrada apenas a contas de usuários que estão em uma organização do Microsoft Azure Active Directory que esteja na lista de organizações permitidas:
1. Na [classe de inicialização OWIN](#configure-the-authentication-pipeline), defina o argumento `ValidateIssuer` como `true`.
2. Defina o valor do parâmetro `ValidIssuers` para a lista de organizações permitidas.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Opção 3: Usar um método personalizado para validar emissores

É possível implementar um método personalizado para validar emissores usando o parâmetro **IssuerValidator**. Para obter mais informações sobre como usar esse parâmetro, leia sobre a [classe TokenValidationParameters](/previous-versions/visualstudio/dn464192(v=vs.114)).
