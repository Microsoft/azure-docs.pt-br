---
title: Personalizar declarações de token SAML do aplicativo
titleSuffix: Microsoft identity platform
description: Saiba como personalizar as declarações emitidas pela plataforma de identidade da Microsoft no token SAML para aplicativos empresariais.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: kenwith
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 05447f41ca891adfe14533a74dfedf153e3c5773
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102692"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Como personalizar declarações emitidas no token SAML para aplicativos empresariais

Atualmente, a plataforma de identidade da Microsoft dá suporte ao SSO (logon único) com a maioria dos aplicativos empresariais, incluindo os aplicativos previamente integrados na Galeria de aplicativos do Azure AD, bem como os personalizados. Quando um usuário é autenticado em um aplicativo por meio da plataforma Microsoft Identity usando o protocolo SAML 2,0, a plataforma de identidade da Microsoft envia um token para o aplicativo (por meio de um HTTP POST). Em seguida, o aplicativo é validado e usa o token para conectar o usuário em vez de solicitar um nome de usuário e a senha. Esses tokens SAML contêm partes de informações sobre o usuário conhecidas como *declarações*.

Uma *declaração* são informações que um provedor de identidade declara sobre um usuário dentro do token que emite para esse usuário. No [Token SAML](https://en.wikipedia.org/wiki/SAML_2.0), esses dados normalmente estão contidos na Instrução de Atributo SAML. A ID única do usuário é normalmente representada na SAML Subject, também denominada Identificador de Nome.

Por padrão, a plataforma de identidade da Microsoft emite um token SAML para seu aplicativo que contém uma `NameIdentifier` declaração com um valor de nome de usuário (também conhecido como o nome UPN) no Azure AD, que pode identificar exclusivamente o usuário. O token SAML também contém declarações adicionais com o endereço de email, nome e sobrenome do usuário.

Para exibir ou editar as declarações emitidas no token SAML para o aplicativo, abra o aplicativo no Portal do Azure. Em seguida, abra a seção **Declarações e Atributos do Usuário**.

![Abrir a seção Declarações e Atributos do Usuário no portal do Azure](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Há dois possíveis motivos para você precisar editar as declarações emitidas no token SAML:

* O aplicativo exige que a declaração `NameIdentifier` ou NameID seja algo diferente do nome de usuário (ou nome UPN) armazenado no Azure AD.
* O aplicativo foi escrito para exigir um conjunto diferente de URIs ou valores de declaração.

## <a name="editing-nameid"></a>Como editar o NameID

Para editar o NameID (valor do Identificador de nome):

1. Abra a página **Valor do identificador de nome**.
1. Selecione o atributo ou a transformação que você deseja aplicar a ele. Opcionalmente, você pode especificar o formato que deseja que a declaração NameID tenha.

   ![Editar o valor de NameID (identificador de nome)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Formato de NameID

Se a solicitação SAML contiver o elemento NameIDPolicy com um formato específico, a plataforma de identidade da Microsoft respeitará o formato na solicitação.

Se a solicitação SAML não contiver um elemento para NameIDPolicy, a plataforma de identidade da Microsoft emitirá NameID com o formato especificado. Se nenhum formato for especificado, a plataforma de identidade da Microsoft usará o formato de origem padrão associado à fonte de declaração selecionada.

Na lista suspensa **Escolha o formato do identificador de nome**, você pode selecionar uma das opções a seguir.

| Formato de NameID | Descrição |
|---------------|-------------|
| **Default** | A plataforma de identidade da Microsoft usará o formato de origem padrão. |
| **Persistente** | A plataforma de identidade da Microsoft usará persistente como o formato NameID. |
| **EmailAddress** | A plataforma de identidade da Microsoft usará EmailAddress como o formato NameID. |
| **Não especificado** | A plataforma de identidade da Microsoft usará não especificado como o formato NameID. |

O NameID transitório também é compatível, mas não está disponível na lista suspensa e não pode ser configurado no lado do Azure. Para saber mais sobre o atributo NameIDPolicy, confira [Protocolo SAML de logon único](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Atributos

Selecione a fonte desejada para a declaração `NameIdentifier` (ou NameID). Você pode selecionar entre as opções a seguir.

| Nome | Descrição |
|------|-------------|
| Email | Endereço de email do usuário |
| userprincipalName | O nome UPN do usuário |
| onpremisessamaccount | Nome da conta SAM sincronizada do Azure AD local |
| objectid | O objectid do usuário no Azure AD |
| employeeid | A ID de funcionário do usuário |
| Extensões de diretório | Extensões de diretório [sincronizadas do Active Directory local usando a Sincronização do Azure AD Connect](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atributos de Extensão 1-15 | Atributos de extensão locais usados para estender o esquema do Azure AD |

Para obter mais informações, confira a [Tabela 3: valores de ID válidos por origem](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

Você também pode atribuir qualquer valor constante (estático) a qualquer declaração que você definir no Azure AD. Siga as etapas abaixo para atribuir um valor constante:

1. No <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>, na seção **Declarações e Atributos do Usuário**, clique no ícone **Editar** para editar as declarações.
1. Clique na declaração necessária que você deseja modificar.
1. Insira o valor da constante sem aspas no **Atributo de origem** de acordo com sua organização e clique em **Salvar**.

    ![Seção de declarações & atributos org no portal do Azure](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. O valor constante será exibido como mostrado abaixo.

    ![Seção editar atributos & declarações no portal do Azure](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Declarações especiais – transformações

Você também pode usar as funções de transformações de declarações.

| Função | Descrição |
|----------|-------------|
| **ExtractMailPrefix()** | Remove o sufixo de domínio de endereço de email ou nome UPN. Isso extrai somente a primeira parte do nome de usuário que está sendo passada (por exemplo, "joe_smith" em vez de joe_smith@contoso.com). |
| **Join()** | Une um atributo a um domínio verificado. Se o valor do identificador de usuário selecionado tiver um domínio, ele extrairá o nome de usuário para anexar o domínio verificado selecionado. Por exemplo, se você selecionar o email (joe_smith@contoso.com) como o valor de identificador de usuário e selecionar contoso.onmicrosoft.com como o domínio verificado, isso resultará em joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Converte os caracteres do atributo selecionado em caracteres minúsculos. |
| **ToUpper()** | Converte os caracteres do atributo selecionado em caracteres maiúsculos. |

## <a name="adding-application-specific-claims"></a>Como adicionar declarações específicas a um aplicativo

Para adicionar declarações específicas a um aplicativo:

1. Em **Declarações e Atributos do Usuário**, selecione **Adicionar nova declaração** para abrir o painel **Gerenciar declarações de usuário**.
1. Insira o **nome** das declarações. O valor não precisa necessariamente seguir um padrão de URI segundo a especificação de SAML. Se você precisar de um padrão de URI, poderá colocá-lo no campo **Namespace**.
1. Selecione a **Origem** em que a declaração vai recuperar o valor dela. Você pode selecionar um atributo de usuário na lista suspensa de atributo de origem ou aplicar uma transformação ao atributo de usuário antes de emiti-lo como uma declaração.

### <a name="claim-transformations"></a>Transformações de declaração

Para aplicar uma transformação a um atributo de usuário:

1. Em **Gerenciar declaração**, selecione *Transformação* como a origem da declaração para abrir a página **Gerenciar transformação**.
2. Selecione a função na lista suspensa de transformação. Dependendo da função selecionada, você precisará fornecer parâmetros e um valor constante a ser avaliado na transformação. Consulte a tabela abaixo para obter mais informações sobre as funções disponíveis.
3. Para aplicar várias transformações, clique em **adicionar transformação**. Você pode aplicar um máximo de duas transformações a uma declaração. Por exemplo, você pode primeiro extrair o prefixo de email do `user.mail`. Em seguida, converta a cadeia de caracteres em maiúsculas.

   ![Transformação de várias declarações](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

É possível usar as funções a seguir para transformar declarações.

| Função | Descrição |
|----------|-------------|
| **ExtractMailPrefix()** | Remove o sufixo de domínio de endereço de email ou nome UPN. Isso extrai somente a primeira parte do nome de usuário que está sendo passada (por exemplo, "joe_smith" em vez de joe_smith@contoso.com). |
| **Join()** | Cria um valor unindo dois atributos. Opcionalmente, você pode usar um separador entre os dois atributos. Para a transformação da declaração NameID, a junção é restrita a um domínio verificado. Se o valor do identificador de usuário selecionado tiver um domínio, ele extrairá o nome de usuário para anexar o domínio verificado selecionado. Por exemplo, se você selecionar o email (joe_smith@contoso.com) como o valor de identificador de usuário e selecionar contoso.onmicrosoft.com como o domínio verificado, isso resultará em joe_smith@contoso.onmicrosoft.com. |
| **Tominúsculas ()** | Converte os caracteres do atributo selecionado em caracteres minúsculos. |
| **Topercase ()** | Converte os caracteres do atributo selecionado em caracteres maiúsculos. |
| **Contains()** | Gera um atributo ou constante se a entrada corresponde ao valor especificado. Caso contrário, você poderá especificar outra saída se não houver correspondência.<br/>Por exemplo, se você quiser emitir uma declaração em que o valor será o endereço de email do usuário se ele contiver o domínio "@contoso.com", caso contrário, será recomendável que você gere o nome UPN. Para fazer isso, você configuraria os seguintes valores:<br/>*Parâmetro 1 (entrada)* : user.email<br/>*Valor*: "@contoso.com"<br/>Parâmetro 2 (saída): user.email<br/>Parâmetro 3 (saída se não houver correspondência): user.userprincipalname |
| **EndWith()** | Gera um atributo ou constante se a entrada termina com o valor especificado. Caso contrário, você poderá especificar outra saída se não houver correspondência.<br/>Por exemplo, se você quiser emitir uma declaração que terá o valor da ID do funcionário quando essa ID terminar com "000", mas quiser gerar um atributo de extensão quando o final dessa ID for diferente. Para fazer isso, você configuraria os seguintes valores:<br/>*Parâmetro 1 (entrada)* : user.employeeid<br/>*Valor*: "000"<br/>Parâmetro 2 (saída): user.employeeid<br/>Parâmetro 3 (saída se não houver correspondência): user.extensionattribute1 |
| **StartWith()** | Gera um atributo ou constante se a entrada começa com o valor especificado. Caso contrário, você poderá especificar outra saída se não houver correspondência.<br/>Por exemplo, se você quiser emitir uma declaração em que o valor será a ID de funcionário do usuário quando o país/região começar com "EUA", mas quiser gerar um atributo de extensão quando o início do país/região for diferente. Para fazer isso, você configuraria os seguintes valores:<br/>*Parâmetro 1 (entrada)* : user.country<br/>*Valor*: "EUA"<br/>Parâmetro 2 (saída): user.employeeid<br/>Parâmetro 3 (saída se não houver correspondência): user.extensionattribute1 |
| **Extract() – Após a correspondência** | Retorna a substring após ela corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "Finance_BFernandes", o valor correspondente será "Finance_", então a saída da declaração será "BFernandes". |
| **Extract() – Antes da correspondência** | Retorna a substring até ela corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "BFernandes_EUA", o valor correspondente será "_EUA", então a saída da declaração será "BFernandes". |
| **Extract() – Entre a correspondência** | Retorna a substring até ela corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "Finance_BSimon_US", o primeiro valor correspondente é "Finance \_ ", o segundo valor correspondente é " \_ US", então a saída da declaração é "BSimon". |
| **ExtractAlpha() – Prefixo** | Retorna a parte alfabética do prefixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada for "BFernandes_123", ele retornará "BFernandes". |
| **ExtractAlpha() – Sufixo** | Retorna a parte alfabética do sufixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada for "123_Fernandes", ele retornará "Fernandes". |
| **ExtractNumeric() – Prefixo** | Retorna a parte numérica do prefixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada for "123_BFernandes", ele retornará "123". |
| **ExtractNumeric() – Sufixo** | Retorna a parte numérica do sufixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada for "BFernandes_123", ele retornará "123". |
| **IfEmpty()** | Gera um atributo ou constante se a entrada for nula ou vazia.<br/>Por exemplo, se você quiser gerar um atributo armazenado em um extensionattribute no caso de a ID de funcionário de um determinado usuário estar vazia. Para fazer isso, você configuraria os seguintes valores:<br/>Parâmetro 1 (entrada): user.employeeid<br/>Parâmetro 2 (saída): user.extensionattribute1<br/>Parâmetro 3 (saída se não houver correspondência): user.employeeid |
| **IfNotEmpty()** | Gera um atributo ou constante se a entrada não for nula nem vazia.<br/>Por exemplo, se você quiser gerar um atributo armazenado em um extensionattribute quando a ID de funcionário de um determinado usuário não estiver vazia. Para fazer isso, você configuraria os seguintes valores:<br/>Parâmetro 1 (entrada): user.employeeid<br/>Parâmetro 2 (saída): user.extensionattribute1 |

Se você precisar de transformações adicionais, envie sua ideia no [fórum de comentários no Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) na categoria *Aplicativo SaaS*.

## <a name="emitting-claims-based-on-conditions"></a>Como emitir declarações com base em condições

Você pode especificar a origem de uma declaração com base no tipo de usuário e no grupo ao qual o usuário pertence. 

O tipo de usuário pode ser:
- **Qualquer**: todos os usuários têm permissão para acessar o aplicativo.
- **Membros**: membro nativo do locatário
- **Todos os convidados**: o usuário é trazido de uma organização externa com ou sem o Azure AD.
- **Convidados do AAD**: o usuário convidado pertence a outra organização que usa o Azure AD.
- **Convidados externos**: o usuário convidado pertence a uma organização externa que não tem o Azure AD.


Um cenário em que isso é útil é quando a origem de uma declaração é diferente para um convidado e um funcionário acessando um aplicativo. Talvez você queira especificar que, se o usuário for um funcionário, o NameID será originado de user.email, mas se o usuário for um convidado, o NameID será originado de user.extensionattribute1.

Para adicionar uma condição de declaração:

1. Em **Gerenciar declaração**, expanda as Condições de declaração.
2. Selecione o tipo de usuário.
3. Selecione os grupos aos quais o usuário deve pertencer. Você pode selecionar até 50 grupos exclusivos em todas as declarações para um determinado aplicativo. 
4. Selecione a **Origem** em que a declaração vai recuperar o valor dela. Você pode selecionar um atributo de usuário na lista suspensa de atributo de origem ou aplicar uma transformação ao atributo de usuário antes de emiti-lo como uma declaração.

A ordem na qual você adiciona as condições é importante. O Azure AD avalia as condições de cima para baixo para decidir qual valor emitir na declaração. O último valor que corresponde à expressão será emitido na declaração.

Por exemplo, Brenda Fernandes é um usuário convidado no locatário da Contoso. Ela pertence a outra organização que também usa o Azure AD. Dada a configuração abaixo para o aplicativo Fabrikam, quando o Brenda tenta entrar na Fabrikam, a plataforma de identidade da Microsoft avaliará as condições como segue.

Primeiro, a plataforma de identidade da Microsoft verifica se o tipo de usuário do Brenda é `All guests` . Como isso é verdadeiro, a plataforma de identidade da Microsoft atribui a origem para a Declaração `user.extensionattribute1` . Em segundo lugar, a plataforma de identidade da Microsoft verifica se o tipo de usuário do Brenda é `AAD guests` , uma vez que isso também é verdadeiro, a plataforma de identidade da Microsoft atribui a origem para a Declaração `user.mail` . Por fim, a declaração é emitida com o valor `user.mail` para Brenda.

![Configuração condicional de declarações](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Próximas etapas

* [Gerenciamento de aplicativos no Azure AD](../manage-apps/what-is-application-management.md)
* [Configurar logon único para aplicativos que não estão na galeria de aplicativos do Azure AD](../manage-apps/configure-saml-single-sign-on.md)
* [Solução de problemas de logon único baseado em SAML](../manage-apps/debug-saml-sso-issues.md)
