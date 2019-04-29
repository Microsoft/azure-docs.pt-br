---
title: Entender como criar a definição da interface do usuário para aplicativos gerenciados do Azure | Microsoft Docs
description: Descreve como criar definições de interface do usuário para aplicativos gerenciados do Azure
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: ab777b487159b009bf2cac6086bb09cc71714b0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587743"
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Criar uma interface do usuário do portal do Azure para seu aplicativo gerenciado
Este documento apresenta os conceitos básicos do arquivo createUiDefinition.json. O portal do Azure usa este arquivo para gerar a interface do usuário para a criação de um aplicativo gerenciado.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

Um CreateUiDefinition sempre contém três propriedades: 

* handler
* version
* parameters

Para aplicativos gerenciados, o manipulador deve sempre ser `Microsoft.Compute.MultiVm`, e a versão mais recente com suporte é `0.1.2-preview`.

O esquema da propriedade parameters depende da combinação do manipulador e da versão especificados. Para aplicativos gerenciados, as propriedades com suporte são `basics`, `steps` e `outputs`. As propriedades basic e steps contêm os _elementos_, como caixas de texto e listas suspensas, que serão exibidos no portal do Azure. A propriedade outputs é usada para mapear os valores de saída de elementos especificados para os parâmetros do modelo de implantação do Azure Resource Manager.

A inclusão de `$schema` é opcional, mas recomendada. Se especificado, o valor de `version` deve corresponder à versão dentro do URI `$schema`.

## <a name="basics"></a>Noções básicas
A etapa Noções Básicas é sempre a primeira etapa do assistente gerada quando o portal do Azure analisa o arquivo. Além de exibir os elementos especificados em `basics`, o portal injeta elementos para que os usuários escolham a assinatura, o grupo de recursos e o local da implantação. Em geral, elementos que fazem consulta de parâmetros de toda a implantação, como nome de um cluster ou credenciais de administrador, devem entrar nesta etapa.

Se o comportamento de um elemento depende da assinatura, do grupo de recursos ou do local do usuário, esse elemento não pode ser usado em basics. Por exemplo, **Microsoft.Compute.SizeSelector** depende da assinatura e do local para determinar a lista de tamanhos disponíveis para o usuário. Portanto, **Microsoft.Compute.SizeSelector** só pode ser usado em steps. Geralmente, apenas os elementos no namespace **Microsoft.Common** podem ser usados em basics. No entanto, alguns elementos de outros namespaces (como **Microsoft.Compute.Credentials**) que não dependem do contexto do usuário ainda são permitidos.

## <a name="steps"></a>Etapas
A propriedade steps pode conter uma ou mais etapas adicionais para exibir depois de basics, cada uma contendo um ou mais elementos. Considere adicionar etapas por função ou camada do aplicativo que está sendo implantado. Por exemplo, adicione uma etapa nas entradas para os nós mestres e uma etapa para os nós de trabalho em um cluster.

## <a name="outputs"></a>outputs
O portal do Azure usa a propriedade `outputs` para mapear os elementos de `basics` e `steps` para os parâmetros do modelo de implantação do Azure Resource Manager. As chaves do dicionário são os nomes dos parâmetros do modelo, e os valores são propriedades dos objetos da saída dos elementos referenciados.

Para definir o nome de recurso do aplicativo gerenciado, inclua um valor chamado `applicationResourceName` na propriedade outputs. Se você não definir esse valor, o aplicativo atribuirá um GUID para o nome. Você pode incluir uma caixa de texto na interface do usuário que solicita um nome de usuário.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="functions"></a>Funções
Do mesmo modo que as funções de modelo no Azure Resource Manager (tanto em sintaxe quanto em funcionalidade), o CreateUiDefinition fornece funções para trabalhar com entradas e saídas dos elementos e recursos como condicionais.

## <a name="next-steps"></a>Próximas etapas
O próprio arquivo createUiDefinition.json tem um esquema simples. A profundidade real dele é proveniente de todos os elementos e funções com suporte. Esses itens são descritos mais detalhadamente em:

- [Elementos](create-uidefinition-elements.md)
- [Funções](create-uidefinition-functions.md)

Um esquema JSON atual para createUiDefinition está disponível aqui: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Para ver um arquivo de interface do usuário de exemplo, consulte [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
