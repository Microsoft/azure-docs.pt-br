---
title: Adicionar atributos personalizados a fluxos de inscrição de autoatendimento-Azure AD
description: Saiba mais sobre como personalizar os atributos para seus fluxos dos usuários de inscrição por autoatendimento.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46b498f8b8512d0202f47dd31ba25cc851ca71e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644095"
---
# <a name="define-custom-attributes-for-user-flows"></a>Definir atributos personalizados para fluxos dos usuários

Para cada aplicativo, você pode ter requisitos diferentes para as informações que deseja coletar durante a inscrição. O Azure AD é fornecido com um conjunto interno de informações armazenadas em atributos, como Nome, Sobrenome, Cidade e CEP. Com o Azure AD, você pode estender o conjunto de atributos armazenados em uma conta de convidado quando o usuário externo se inscreve por meio de um fluxo do usuário.

Você pode criar atributos personalizados no portal do Azure e usá-los nos seus fluxos dos usuários de inscrição por autoatendimento. Você também pode ler e gravar esses atributos usando a [API do Microsoft Graph](../../active-directory-b2c/microsoft-graph-operations.md). A API do Microsoft Graph dá suporte à criação e à atualização de um usuário com atributos de extensão. Atributos de extensão na API do Graph são nomeados usando a convenção `extension_<extensions-app-id>_attributename`. Por exemplo:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

O `<extensions-app-id>` é específico para seu locatário. Para localizar esse identificador, navegue até Azure Active Directory > Registros de aplicativo > todos os aplicativos. Pesquise o aplicativo que começa com "AAD-Extensions-app" e selecione-o. Na página Visão geral do aplicativo, observe a ID do aplicativo (cliente).

## <a name="create-a-custom-attribute"></a>Como criar um atributo personalizado

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. No menu à esquerda, selecione **Identidades Externas**.
4. Selecione **atributos de usuário personalizados**. Os atributos de usuário disponíveis serão listados.

   ![Selecionar atributos de usuário para inscrição](media/user-flow-add-custom-attributes/user-attributes.png)

5. Para adicionar um atributo, selecione **Adicionar**.
6. No painel **Adicionar um atributo**, insira os seguintes valores:

   - **Nome**: forneça um nome para o atributo personalizado (por exemplo, "Tamanho do calçado").
   - **Tipo de Dados**: escolha um tipo de dados (**cadeia de caracteres**, **booliano** ou **Int**).
   - **Descrição**: opcionalmente, insira uma descrição do atributo personalizado para uso interno. Essa descrição não fica visível para o usuário.

   ![Adicionar um atributo](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. Selecione **Criar**.

O atributo personalizado agora está disponível na lista de atributos do usuário e para uso nos fluxos dos usuários. Um atributo personalizado só é criado na primeira vez que é usado em qualquer fluxo do usuário, não quando você o adiciona à lista de atributos de usuário.

Depois que você cria um usuário com um fluxo do usuário que usa o atributo personalizado recém-criado, o objeto pode ser consultado no [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Agora você deve ver **calçados** na lista de atributos coletados durante a jornada de inscrição no objeto de usuário. Você pode chamar o API do Graph de seu aplicativo para obter os dados desse atributo depois que ele for adicionado ao objeto de usuário.

## <a name="next-steps"></a>Próximas etapas

[Adicionar um fluxo do usuário de inscrição por autoatendimento a um aplicativo](self-service-sign-up-user-flow.md)