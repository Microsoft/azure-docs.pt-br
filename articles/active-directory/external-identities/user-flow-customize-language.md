---
title: Personalização de idioma nos fluxos dos usuários do Azure AD
description: Saiba mais sobre como personalizar a experiência de idioma em seus fluxos de usuário em Azure Active Directory.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/06/2020
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cddb40a6a43c38079906699d9fa4865c4daf558
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837083"
---
# <a name="language-customization-in-azure-active-directory-preview"></a>Personalização de idioma no Azure Active Directory (versão prévia)

> [!NOTE]
> A inscrição por autoatendimento é a versão prévia pública de um recurso do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A personalização de idioma no Azure AD (Azure Active Directory) permite que o fluxo do usuário acomode diferentes idiomas de acordo com as necessidades do usuário. A Microsoft fornece as traduções para [36 idiomas](#supported-languages). Mesmo que a sua experiência seja fornecida apenas para um só idioma, você pode personalizar os nomes de atributo na página de coleção de atributos.

## <a name="how-language-customization-works"></a>Como funciona a personalização de idioma

Por padrão, a personalização de idioma é habilitada para os usuários que se inscreveram a fim de garantir uma experiência de inscrição consistente. Use idiomas para modificar as cadeias de caracteres exibidas aos usuários como parte do processo de coleta de atributos durante a inscrição.

> [!NOTE]
> Se você estiver usando atributos de usuário personalizados, precisará fornecer suas próprias traduções. Para obter mais informações, consulte [Personalizar as cadeias de caracteres](#customize-your-strings).

## <a name="customize-your-strings"></a>Como personalizar suas cadeias de caracteres

A personalização de idioma permite personalizar qualquer cadeia de caracteres no fluxo de usuário.

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. No menu à esquerda, selecione **Identidades Externas**.
4. Selecione **fluxos dos usuários (Versão Prévia)** .
3. Selecione o fluxo do usuário que deseja habilitar para traduções.
4. Selecione os **Idiomas**.
5. Na página **Idiomas** do fluxo de usuário, selecione o idioma que você quer personalizar.
6. Expanda a **página Coleção de Atributos**.
7. Selecione **Baixar Padrões** (ou **Baixar substituições**, se esse idioma já foi editado anteriormente).

Essas etapas fornecem um arquivo JSON que você pode usar para começar a editar suas cadeias de caracteres.

### <a name="change-any-string-on-the-page"></a>Alterar qualquer cadeia de caractere na página

1. Abra o arquivo JSON baixado das instruções anteriores em um editor de JSON.
1. Localize o elemento que você deseja alterar. Você pode localizar `StringId` para a cadeia de caracteres que está procurando ou procurar o atributo `Value` que deseja alterar.
1. Atualize o atributo `Value` com o que você deseja exibir.
1. Para cada cadeia de caracteres que você deseja alterar, altere `Override` para `true`.
1. Salve o arquivo e carregue suas alterações. (É possível localizar o controle de upload no mesmo local de onde baixou o arquivo JSON.)

> [!IMPORTANT]
> Se você precisar substituir uma cadeia de caracteres, certifique-se de que o valor `Override` esteja definido para `true`. Se o valor não mudou, a entrada será ignorada.

### <a name="change-extension-attributes"></a>Alterar atributos de extensão

Se você quiser alterar a cadeia de caracteres de um atributo de usuário personalizado ou se deseja adicionar um ao JSON, ele estará no seguinte formato:

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Substitua `<ExtensionAttribute>` pelo nome do seu atributo de usuário personalizado.

Substitua `<ExtensionAttributeValue>` por uma cadeia de caracteres nova a ser exibida.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Fornecer uma lista de valores usando LocalizedCollections

Se você quiser fornecer uma lista configurada de valores para respostas, será necessário criar um atributo `LocalizedCollections`. `LocalizedCollections` é uma matriz de pares `Name` e `Value`. A ordem dos itens será a ordem em que elas são exibidas. Para adicionar `LocalizedCollections`, use o formato a seguir:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` é o atributo do usuário ao qual esse atributo `LocalizedCollections` é uma resposta.
* `Name` é o valor mostrado para o usuário.
* `Value` é o que é retornado na declaração quando essa opção é selecionada.

### <a name="upload-your-changes"></a>Carregamento das suas alterações

1. Depois de concluir as alterações no arquivo JSON, volte ao locatário.
1. Selecione **Fluxos dos Usuários** e clique no fluxo de usuário que você quer habilitar para as traduções.
1. Selecione os **Idiomas**.
1. Selecione o idioma para o qual você deseja traduzir.
1. Selecione a **página Coleção de atributos**.
1. Selecione o ícone da pasta e selecione o arquivo JSON para upload.

As alterações são salvas no fluxo de usuário automaticamente.

## <a name="additional-information"></a>Informações adicionais

### <a name="page-ui-customization-labels-as-overrides"></a>Rótulos de personalização da interface do usuário da página como substituições

Ao habilitar a personalização de idioma, as edições anteriores para rótulos usando a personalização da interface do usuário da página são persistidas em um arquivo JSON para inglês (en). Você pode continuar a alterar os rótulos e outras cadeias de caracteres, carregando recursos de idiomas na personalização de idioma.

### <a name="up-to-date-translations"></a>Atualizar traduções

A Microsoft está comprometida em fornecer as traduções mais atualizadas para seu uso. A Microsoft aprimora continuamente as traduções e as mantém em conformidade para você. A Microsoft identificará bugs e alterações na terminologia global e fará atualizações que funcionarão diretamente no fluxo de usuário.

### <a name="support-for-right-to-left-languages"></a>Suporte para idiomas da direita para a esquerda

A Microsoft atualmente não fornece suporte para idiomas escritos da direita para a esquerda. Você pode fazer isso usando localidades personalizadas e CSS para alterar a maneira como as cadeias de caracteres são exibidas. Se você precisar desse recurso, vote para ele nos [Comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Traduções de provedor de identidade social

A Microsoft fornece o parâmetro OIDC `ui_locales` para logons sociais. Mas alguns provedores de identidade social, incluindo o Facebook e o Google, não os consideram.

### <a name="browser-behavior"></a>Comportamento do navegador

Ambos Chrome e o Firefox solicitam o idioma definido. Se for um idioma com suporte, será exibido antes do padrão. Atualmente, o Microsoft Edge não solicita um idioma e exibe diretamente o idioma padrão.

## <a name="supported-languages"></a>Idiomas com suporte

O Azure AD inclui suporte para os idiomas a seguir. Os idiomas do fluxo do usuário são fornecidos pelo Azure AD. As linguagens de notificação da MFA (autenticação multifator) são fornecidas pelo [Azure ad MFA](../authentication/concept-mfa-howitworks.md).

| Linguagem              | Código de idioma | Fluxos de usuário         | Notificações da MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Árabe                | ar            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Búlgaro             | bg            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Bangla                | bn            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Catalão               | ca            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Tcheco                 | cs            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Dinamarquês                | da            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Alemão                | de            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Grego                 | el            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Inglês               | en            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Espanhol               | es            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Estoniano              | et            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Basco                | eu            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Finlandês               | fi            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Francês                | fr            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Galego              | gl            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Guzerate              | gu            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Hebraico                | he            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Híndi                 | hi            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Croata              | hr            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Húngaro             | hu            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Indonésio            | id            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Italiano               | it            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Japonês              | ja            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Cazaque                | kk            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| canarim               | kn            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Coreano                | ko            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Lituano            | lt            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Letão               | lv            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Malaiala             | ml            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Marati               | mr            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Malaio                 | ms            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Norueguês Bokmal      | nb            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Holandês                 | nl            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Norueguês             | não            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Panjabi               | pa            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Polonês                | pl            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Português - Brasil   | pt-br         | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Português - Portugal | pt-pt         | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Romeno              | ro            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Russo               | ru            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Eslovaco                | sk            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Esloveno             | sl            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Sérvio - Cirílico    | sr-cryl-cs    | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Sérvio - latino       | sr-latn-cs    | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Sueco               | sv            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Tâmil                 | ta            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Télugo                | te            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Tailandês                  | th            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Turco               | tr            | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Ucraniano             | uk            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Vietnamita            | vi            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Chinês - Simplificado  | zh-hans       | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |
| Chinês - Tradicional | zh-hant       | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) | ![Marca de seleção verde.](./media/user-flow-customize-language/yes.png) |