---
title: Elemento de interface do usuário ServicePrincipalSelector
description: Descreve o elemento de interface do usuário Microsoft. Common. ServicePrincipalSelector para portal do Azure. Fornece um controle para escolher um aplicativo e uma caixa de texto para inserir uma senha ou impressão digital do certificado.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: tomfitz
ms.openlocfilehash: 2fdbbad467d8c762db485fc7935e9cef78313fd0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184443"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Elemento de interface do usuário Microsoft. Common. ServicePrincipalSelector

Um controle que permite aos usuários selecionar uma [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) existente ou registrar um novo aplicativo. Ao selecionar **criar novo**, siga as etapas para registrar um novo aplicativo. Quando você seleciona um aplicativo existente, o controle fornece uma caixa de texto para inserir uma senha ou impressão digital do certificado.

## <a name="ui-samples"></a>Exemplos de interface do usuário

Você pode usar um aplicativo padrão, criar um novo aplicativo ou usar um aplicativo existente.

### <a name="use-default-application-or-create-new"></a>Usar aplicativo padrão ou criar novo

A exibição padrão é determinada pelos valores na `defaultValue` propriedade e o tipo de **entidade de serviço** é definido como **criar novo**. Se a `principalId` Propriedade contiver um GUID (identificador global exclusivo) válido, o controle pesquisará o aplicativo `objectId` . O valor padrão se aplicará se o usuário não fizer uma seleção do controle.

Se você quiser registrar um novo aplicativo, selecione **alterar seleção** e a caixa de diálogo **registrar um aplicativo** será exibida. Insira **nome**, **tipo de conta com suporte** e selecione o botão **registrar** .

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-default.png" alt-text="Exibição inicial Microsoft. Common. ServicePrincipalSelector.":::

Depois de registrar um novo aplicativo, use o **tipo de autenticação** para inserir uma senha ou impressão digital do certificado.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-authenticate.png" alt-text="Autenticação Microsoft. Common. ServicePrincipalSelector.":::

### <a name="use-existing-application"></a>Usar aplicativo existente

Para usar um aplicativo existente, escolha **selecionar existente** e selecione **fazer Seleção**. Use a caixa de diálogo **selecionar um aplicativo** para procurar o nome do aplicativo. Nos resultados, selecione o aplicativo e, em seguida, o botão **selecionar** . Depois de selecionar um aplicativo, o controle exibe o **tipo de autenticação** para inserir uma senha ou impressão digital do certificado.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-existing.png" alt-text="Microsoft. Common. ServicePrincipalSelector selecionar aplicativo existente.":::

## <a name="schema"></a>Esquema

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Comentários

- As propriedades obrigatórias são as seguintes:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Especifica o padrão `principalId` e `name` .

- As propriedades opcionais são as seguintes:
  - `toolTip`: Anexa uma dica `infoBalloon` de ferramenta a cada rótulo.
  - `visible`: Oculta ou exibe o controle.
  - `options`: Especifica se a opção de impressão digital do certificado deve ser disponibilizada ou não.
  - `constraints`: Restrições Regex para validação de senha.

## <a name="example"></a>Exemplo

Veja a seguir um exemplo do `Microsoft.Common.ServicePrincipalSelector` controle. A `defaultValue` propriedade `principalId` é definida `<default guid>` como um espaço reservado para um GUID de identificador de aplicativo padrão.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Saída de exemplo

O `appId` é a ID do registro do aplicativo que você selecionou ou criou. O `objectId` é uma matriz de IDs de objeto para as entidades de serviço configuradas para o registro de aplicativo selecionado.

Quando nenhuma seleção é feita do controle, o `newOrExisting` valor da propriedade é **novo**:

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

Quando **criar novo** ou um aplicativo existente é selecionado no controle, o `newOrExisting` valor da propriedade é **existente**:

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>Próximas etapas

- Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
- Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).