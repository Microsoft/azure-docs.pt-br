---
title: Personalizar declarações de token SAML para aplicativos empresariais no Azure AD | Microsoft Docs
description: Aprenda a personalizar as declarações emitidas no token SAML para aplicativos empresariais no Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f5930f2d3db94f615321eda480aed0d4d196911
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380821"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Como: Personalizar declarações emitidas no token SAML para aplicativos empresariais

Hoje, o Azure Active Directory (Azure AD) dá suporte ao SSO (logon único) com a maioria dos aplicativos corporativos, incluindo os aplicativos previamente integrados na Galeria de aplicativos do Azure AD, bem como os personalizados. Quando um usuário é autenticado em um aplicativo por meio do Azure AD usando o protocolo SAML 2.0, o Azure AD envia um token ao aplicativo (por um HTTP POST). Em seguida, o aplicativo é validado e usa o token para conectar o usuário em vez de solicitar um nome de usuário e a senha. Esses tokens SAML contêm informações sobre o usuário conhecido como *declarações*.

Uma *declaração* são informações que um provedor de identidade declara sobre um usuário dentro do token que emite para esse usuário. No [Token SAML](https://en.wikipedia.org/wiki/SAML_2.0), esses dados normalmente estão contidos na Instrução de Atributo SAML. A ID única do usuário é normalmente representada na SAML Subject, também denominada Identificador de Nome.

Por padrão, o Azure ad emite um token SAML para seu aplicativo que contém `NameIdentifier` uma declaração com um valor de nome de usuário (também conhecido como o nome principal do usuário) no Azure AD, que pode identificar exclusivamente o usuário. O token SAML também contém declarações adicionais com o endereço de email, nome e sobrenome do usuário.

Para exibir ou editar as declarações emitidas no token SAML para o aplicativo, abra o aplicativo no Portal do Azure. Em seguida, abra a seção **atributos de usuário & declarações** .

![Abra a seção atributos de usuário & declarações no portal do Azure](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Há dois possíveis motivos para você precisar editar as declarações emitidas no token SAML:

* O aplicativo exige que `NameIdentifier` a declaração ou NameID seja algo diferente do nome de usuário (ou nome UPN) armazenado no Azure AD.
* O aplicativo foi escrito para exigir um conjunto diferente de URIs ou valores de declaração.

## <a name="editing-nameid"></a>Editando NameID

Para editar NameID (valor do identificador de nome):

1. Abra a página **valor do identificador de nome** .
1. Selecione o atributo ou a transformação que você deseja aplicar ao atributo. Opcionalmente, você pode especificar o formato que deseja que a declaração NameID tenha.

   ![Editar o valor NameID (identificador de nome)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Formato NameID

Se a solicitação SAML contiver o elemento NameIDPolicy com um formato específico, o Azure AD honrará o formato na solicitação.

Se a solicitação SAML não contiver um elemento para NameIDPolicy, o Azure AD emitirá o NameID com o formato que você especificar. Se nenhum formato for especificado, o Azure AD usará o formato de origem padrão associado à fonte de declaração selecionada.

Na lista suspensa **escolher formato do identificador de nome** , você pode selecionar uma das opções a seguir.

| Formato NameID | Descrição |
|---------------|-------------|
| **Padrão** | O Azure AD usará o formato de origem padrão. |
| **Persistente** | O Azure AD usará persistente como o formato NameID. |
| **EmailAddress** | O Azure AD usará EmailAddress como o formato NameID. |
| **Não especificado** | O AD do Azure usará não especificado como o formato NameID. |
| **Transitório** | O Azure AD usará transitório como o formato NameID. |

Para saber mais sobre o atributo NameIDPolicy, consulte [protocolo SAML de logon único](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Atributos

Selecione a fonte desejada para a declaração `NameIdentifier` (ou NameID). Você pode selecionar entre as opções a seguir.

| Nome | Descrição |
|------|-------------|
| Email | Endereço de email do usuário |
| userprincipalName | UPN (nome principal do usuário) do usuário |
| onpremisessamaccount | Nome da conta SAM sincronizada do Azure AD local |
| objectid | ObjectID do usuário no Azure AD |
| employeeid | EmployeeID do usuário |
| Extensões de diretório | Extensões de diretório [sincronizadas do Active Directory local usando a Sincronização do Azure AD Connect](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atributos de Extensão 1-15 | Atributos de extensão locais usados para estender o esquema do Azure AD |

Para obter mais informações, [consulte a tabela 3: Valores de ID válidos por](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)origem.

### <a name="special-claims---transformations"></a>Declarações especiais – transformações

Você também pode usar as funções de transformações de declarações.

| Função | Descrição |
|----------|-------------|
| **ExtractMailPrefix()** | Remove o sufixo de domínio do endereço de email ou do nome principal do usuário. Isso extrai somente a primeira parte do nome de usuário que está sendo passada (por exemplo, "joe_smith" em vez de joe_smith@contoso.com). |
| **Join()** | Une um atributo a um domínio verificado. Se o valor do identificador de usuário selecionado tiver um domínio, ele extrairá o nome de usuário para anexar o domínio verificado selecionado. Por exemplo, se você selecionar o email (joe_smith@contoso.com) como o valor de identificador de usuário e selecionar contoso.onmicrosoft.com como o domínio verificado, isso resultará em joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Converte os caracteres do atributo selecionado em caracteres minúsculos. |
| **ToUpper()** | Converte os caracteres do atributo selecionado em caracteres maiúsculos. |

## <a name="adding-application-specific-claims"></a>Adicionando declarações específicas do aplicativo

Para adicionar declarações específicas do aplicativo:

1. Em **atributos de usuário & declarações**, selecione **Adicionar nova declaração** para abrir a página **gerenciar declarações do usuário** .
1. Insira o **nome** das declarações. O valor não precisa seguir estritamente um padrão de URI, de acordo com a especificação SAML. Se você precisar de um padrão de URI, poderá colocá-lo no campo **namespace** .
1. Selecione a **origem** na qual a declaração vai recuperar seu valor. Você pode selecionar um atributo de usuário na lista suspensa atributo de origem ou aplicar uma transformação ao atributo de usuário antes de emiti-lo como uma declaração.

### <a name="application-specific-claims---transformations"></a>Declarações específicas do aplicativo – transformações

Você também pode usar as funções de transformações de declarações.

| Função | Descrição |
|----------|-------------|
| **ExtractMailPrefix()** | Remove o sufixo de domínio do endereço de email ou do nome principal do usuário. Isso extrai somente a primeira parte do nome de usuário que está sendo passada (por exemplo, "joe_smith" em vez de joe_smith@contoso.com). |
| **Join()** | Cria um novo valor unindo dois atributos. Opcionalmente, você pode usar um separador entre os dois atributos. |
| **ToLower()** | Converte os caracteres do atributo selecionado em caracteres minúsculos. |
| **ToUpper()** | Converte os caracteres do atributo selecionado em caracteres maiúsculos. |
| **Contains ()** | Gera um atributo ou constante se a entrada corresponde ao valor especificado. Caso contrário, você poderá especificar outra saída se não houver correspondência.<br/>Por exemplo, se você quiser emitir uma declaração em que o valor é o endereço de email do usuário, se ele contiver@contoso.como domínio "", caso contrário, você deseja gerar o nome UPN. Para fazer isso, configure os seguintes valores:<br/>*Parâmetro 1 (entrada)* : user. email<br/>*Valor*: "@contoso.com"<br/>Parâmetro 2 (saída): user. email<br/>Parâmetro 3 (saída se não houver correspondência): user. UserPrincipalName |
| **EndWith()** | Gera um atributo ou constante se a entrada termina com o valor especificado. Caso contrário, você poderá especificar outra saída se não houver correspondência.<br/>Por exemplo, se você quiser emitir uma declaração em que o valor é o EmployeeID do usuário se o EmployeeID terminar com "000", caso contrário, você desejará gerar um atributo de extensão. Para fazer isso, configure os seguintes valores:<br/>*Parâmetro 1 (entrada)* : user. EmployeeID<br/>*Valor*: "000"<br/>Parâmetro 2 (saída): user. EmployeeID<br/>Parâmetro 3 (saída se não houver correspondência): user. extensionAttribute1 |
| **StartWith()** | Gera um atributo ou constante se a entrada começa com o valor especificado. Caso contrário, você poderá especificar outra saída se não houver correspondência.<br/>Por exemplo, se você quiser emitir uma declaração em que o valor é o EmployeeID do usuário, se o país/região começar com "US", caso contrário, você deseja gerar um atributo de extensão. Para fazer isso, configure os seguintes valores:<br/>*Parâmetro 1 (entrada)* : user. Country<br/>*Valor*: DIGAMOS<br/>Parâmetro 2 (saída): user. EmployeeID<br/>Parâmetro 3 (saída se não houver correspondência): user. extensionAttribute1 |
| **Extract ()-após correspondência** | Retorna a subcadeia de caracteres depois que ela corresponde ao valor especificado.<br/>Por exemplo, se o valor da entrada for "Finance_BSimon", o valor correspondente será "Finance_", então a saída da declaração será "BSimon". |
| **Extrair ()-antes da correspondência** | Retorna a subcadeia de caracteres até corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "BSimon_US", o valor correspondente será "_US", então a saída da declaração será "BSimon". |
| **Extract ()-entre correspondência** | Retorna a subcadeia de caracteres até corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "Finance_BSimon_US", o primeiro valor correspondente é "Finance_", o segundo valor correspondente é "_US", então a saída da declaração é "BSimon". |
| **ExtractAlpha ()-prefixo** | Retorna a parte alfabética do prefixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada for "BSimon_123", ele retornará "BSimon". |
| **ExtractAlpha ()-sufixo** | Retorna a parte alfabética do sufixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada for "123_Simon", ele retornará "Simon". |
| **ExtractNumeric ()-prefixo** | Retorna a parte numérica do prefixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada for "123_BSimon", ele retornará "123". |
| **ExtractNumeric ()-sufixo** | Retorna a parte numérica do sufixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada for "BSimon_123", ele retornará "123". |
| **IfEmpty()** | Gera um atributo ou constante se a entrada é nula ou está vazia.<br/>Por exemplo, se você quiser gerar um atributo armazenado em um ExtensionAttribute, se o EmployeeID de um determinado usuário estiver vazio. Para fazer isso, configure os seguintes valores:<br/>Parâmetro 1 (entrada): user. EmployeeID<br/>Parâmetro 2 (saída): user. extensionAttribute1<br/>Parâmetro 3 (saída se não houver correspondência): user. EmployeeID |
| **IfNotEmpty()** | Gera um atributo ou constante se a entrada não for nula ou vazia.<br/>Por exemplo, se você quiser gerar um atributo armazenado em um ExtensionAttribute, se o EmployeeID de um determinado usuário não estiver vazio. Para fazer isso, configure os seguintes valores:<br/>Parâmetro 1 (entrada): user. EmployeeID<br/>Parâmetro 2 (saída): user. extensionAttribute1 |

Se você precisar de transformações adicionais, envie sua ideia no [Fórum de comentários no Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) na categoria de *aplicativo SaaS* .

## <a name="next-steps"></a>Próximas etapas

* [Gerenciamento de aplicativos no Azure AD](../manage-apps/what-is-application-management.md)
* [Configurar logon único para aplicativos que não estão na galeria de aplicativos do Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Solução de problemas de logon único baseado em SAML](howto-v1-debug-saml-sso-issues.md)
