---
title: Sobre a personalização da interface do usuário no Azure Active Directory B2C | Microsoft Docs
description: Saiba como personalizar a interface do usuário de seus aplicativos que usam o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 13ae1b74acbcab8d623c24d6a7b8d7e1355b80e8
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227150"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Sobre a personalização da interface do usuário no Azure Active Directory B2C

A capacidade de definir a marca e personalizar a interface do usuário que o Azure AD (Azure Active Directory) B2C fornece a seus aplicativos é importante para fornecer uma experiência perfeita para o seu cliente. Essas experiências incluem inscrição, entrada, edição de perfil e redefinição de senha. Este artigo fornece informações para ajudá-lo a personalizar a interface do usuário dos seus aplicativos.

Dependendo de suas necessidades quando se trata dessas experiências, personalize a interface do usuário do seu aplicativo de maneiras diferentes. Por exemplo:

- Se estiver usando [fluxos de usuário](active-directory-b2c-reference-policies.md) para fornecer inscrição ou entrada, redefinição de senha ou experiências de edição de perfil em seu aplicativo, use o [portal do Azure para personalizar a interface do usuário](tutorial-customize-ui.md).
- Se você estiver usando um fluxo de usuário do v2, você poderá usar um [modelo de layout de página](#page-layout-templates) para alterar a aparência de suas páginas de fluxo do usuário sem personalização adicional. Por exemplo, você pode aplicar um tema Azul oceano ou Cinza-ardósia a todas as páginas no fluxo do usuário.
- Se estiver fornecendo apenas a entrada, sua página de redefinição de senha e emails de verificação, você usa as mesmas etapas de personalização usadas para uma [página de entrada do Azure AD](../active-directory/fundamentals/customize-branding.md).
- Se os clientes tentam editar seu perfil antes que entrar, eles são redirecionados para uma página que você personaliza usando as mesmas etapas que são usadas para personalizar a página de entrada do Azure AD.
- Se estiver usando [políticas personalizadas](active-directory-b2c-overview-custom.md) para fornecer inscrição, entrada, redefinição de senha ou edição de perfil no seu aplicativo, você usa os [arquivos de política para personalizar a interface do usuário](active-directory-b2c-ui-customization-custom.md).
- Se precisar fornecer conteúdo dinâmico com base na decisão do cliente, você usa as [políticas personalizadas que podem alterar o conteúdo da página](active-directory-b2c-ui-customization-custom-dynamic.md) dependendo de um parâmetro que é enviado em uma cadeia de caracteres de consulta. Por exemplo, a imagem de tela de fundo na página de inscrição ou de entrada do Azure AD B2C muda, com base em um parâmetro passado do seu aplicativo Web ou móvel.
- Você pode habilitar o código JavaScript do lado do cliente em seus [fluxos dos usuários](user-flow-javascript-overview.md) ou [políticas personalizadas](page-layout.md) do Azure AD B2C.

O Azure AD B2C executa o código no navegador do cliente e usa uma abordagem moderna chamada [CORS (Compartilhamento de Recursos entre Origens)](https://www.w3.org/TR/cors/). Em tempo de execução, o conteúdo é carregado de uma URL que você especifica em um fluxo de usuário ou política. Você especifica URLs diferentes para páginas diferentes. Após o conteúdo ser carregado da sua URL, ele é mesclado com um fragmento de HTML inserido no Azure AD B2C e exibido para seu cliente.

Ao usar seus próprios arquivos HTML e CSS para personalizar a interface do usuário, examine as diretrizes a seguir antes de começar:

- O Azure AD B2C mescla o conteúdo HTML em suas páginas. Não copie nem tente alterar o conteúdo de padrão fornecido pelo Azure AD B2C. É melhor criar seu conteúdo HTML do zero e usar o conteúdo padrão como referência.
- Agora, o JavaScript pode ser incluído em seu conteúdo personalizado.
- As versões de navegador compatíveis são:
    - Internet Explorer 11, 10 e Microsoft Edge
    - Compatibilidade limitada com Internet Explorer 9 e 8
    - Google Chrome 42.0 e superior
    - Mozilla Firefox 38.0 e superior
- Certifique-se de não incluir marcas de formulário no HTML, pois isso interfere nas operações POST geradas pelo HTML injetado do Azure AD B2C.

## <a name="page-layout-templates"></a>Modelos de layout de página

Para fluxos dos usuários do v2, você pode escolher um modelo predefinido que confere às suas páginas padrão um visual melhor e serve como uma boa base para sua própria personalização.

No menu à esquerda, em **Personalizar**, selecione **Layouts da página**. Em seguida, selecione **Modelo (Versão Prévia)** .

![Lista suspensa seleção de modelos na página fluxo do usuário do portal do Azure](media/customize-ui-overview/template.png)

Selecione um modelo na lista. Por exemplo, o modelo **Azul oceano** aplica o seguinte layout às suas páginas do fluxo de usuário:

![Exemplo do modelo azul do oceano renderizado na página de entrada de inscrição](media/customize-ui-overview/ocean-blue.png)

Quando você escolhe um modelo, o layout selecionado é aplicado a todas as páginas em seu fluxo de usuário e o URI de cada página é visível no campo **URI da página personalizada**.

## <a name="where-do-i-store-ui-content"></a>Onde armazeno o conteúdo da interface do usuário?

Ao usar seus próprios arquivos HTML e CSS para personalizar a interface do usuário, você pode hospedar o conteúdo da interface do usuário em qualquer lugar, como no [Armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md), em servidores da Web, CDNs, AWS S3 ou em sistemas de compartilhamento de arquivos. O ponto importante é que você hospeda o conteúdo em um ponto de extremidade HTTPS disponível publicamente com CORS habilitado. Você deve usar uma URL absoluta ao especificá-lo em seu conteúdo.

## <a name="how-do-i-get-started"></a>Como começar?

Faça o seguinte para personalizar a interface do usuário:

- Crie conteúdo HTML bem formado com um elemento `<div id="api"></div>` vazio localizado em algum lugar no `<body>`. Esse elemento marca onde o conteúdo do Azure AD B2C é inserido. O exemplo a seguir mostra uma página básica:

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- Hospede seu conteúdo em um ponto de extremidade HTTPS (com CORS permitido). Os métodos de solicitação GET e OPTIONS precisam ser habilitados ao configurar o CORS.
- Use CSS para definir o estilo para os elementos de interface do usuário inseridos pelo Azure AD B2C na página. O exemplo a seguir mostra um arquivo CSS simples que também inclui configurações para os elementos HTML injetados da inscrição:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

- Crie ou edite uma política para usar o conteúdo que você criou.

A tabela abaixo lista os fragmentos de HTML que o Azure AD B2C mescla ao elemento `<div id="api"></div>` localizado no seu conteúdo.

| Página inserida | Descrição do HTML |
| ------------- | ------------------- |
| Seleção do provedor de identidade | Contém uma lista de botões para provedores de identidade na qual o cliente pode fazer a seleção durante a inscrição ou a entrada. Esses botões incluem provedores de identidade social como Facebook, Google ou contas locais (baseadas em endereço de email ou nome de usuário). |
| Inscrição da conta local | Contém um formulário para inscrições de conta local baseada em um endereço de email ou um nome de usuário. O formulário pode conter diferentes controles de entrada como caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas de lista suspensa de seleção única e caixas de seleção múltipla. |
| Inscrição de conta social | Pode aparecer ao se inscrever usando uma conta existente de um provedor de identidade social, como o Facebook ou Google. Ela é usada quando é necessário coletar informações adicionais do cliente usando um formulário de inscrição. |
| Inscrição ou entrada unificada | Controla tanto a inscrição quanto a entrada de clientes, que podem usar provedores de identidade social como Facebook, Google ou contas locais. |
| Autenticação multifator | Os clientes podem verificar seus números de telefone (usando mensagem de texto ou de voz) durante a inscrição ou entrada. |
| Erro | Fornece informações de erro para o cliente. |


## <a name="how-do-i-localize-content"></a>Como localizo o conteúdo?

Você localiza seu conteúdo HTML habilitando a [personalização de idioma](active-directory-b2c-reference-language-customization.md) em seu locatário do Azure AD B2C. Habilitar esse recurso permite que o Azure AD B2C encaminhe o parâmetro do Open ID Connect `ui-locales` para seu ponto de extremidade. O servidor de conteúdo pode usar esse parâmetro para fornecer páginas HTML específicas a um idioma.

O conteúdo pode ser extraído de diferentes locais com base na localidade usada. No ponto de extremidade habilitado para CORS, você configura uma estrutura de pastas para hospedar conteúdo para idiomas específicos. Você chamará a correta se usar o valor curinga {Culture:RFC5646}. Por exemplo, seu URI de página personalizada pode parecer com `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`. Você pode carregar a página em francês efetuando o pull de conteúdo de `https://contoso.blob.core.windows.net/fr/myHTML/unified.html`

## <a name="examples"></a>Exemplos

Para obter exemplos de personalização, baixe e examine estes [arquivos de modelo de exemplo](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).

## <a name="next-steps"></a>Próximas etapas

- Se você estiver usando fluxos de usuário, você pode começar a personalizar a interface do usuário com o tutorial: [Personalizar a interface do usuário dos aplicativos no Azure Active Directory B2C](tutorial-customize-ui.md).
- Se você estiver usando políticas personalizadas, você pode começar a personalização da interface com o artigo: [Personalizar a interface do usuário do aplicativo usando uma política personalizada no Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).

