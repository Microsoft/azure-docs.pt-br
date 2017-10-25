---
title: "Entender como criar definição de interface do usuário para aplicativos gerenciados do Azure | Microsoft Docs"
description: "Descreve como criar definições de interface do usuário para aplicativos gerenciados do Azure"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2017
ms.author: tomfitz
ms.openlocfilehash: 077012a2fe9b4b6e6e042fdeb9be2b1d37685bc9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-createuidefinition"></a>Introdução a CreateUiDefinition
Este documento apresenta os conceitos básicos de um CreateUiDefinition, que é usado pelo portal do Azure para gerar a interface do usuário para a criação de um aplicativo gerenciado.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
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
A etapa Noções básicas é sempre a primeira etapa do assistente gerada quando o portal do Azure analisa um CreateUiDefinition. Além de exibir os elementos especificados em `basics`, o portal injeta elementos para que os usuários escolham a assinatura, o grupo de recursos e o local da implantação. Em geral, elementos que fazem consulta de parâmetros de toda a implantação, como nome de um cluster ou credenciais de administrador, devem entrar nesta etapa.

Se o comportamento de um elemento depende da assinatura, do grupo de recursos ou do local do usuário, esse elemento não pode ser usado em basics. Por exemplo, **Microsoft.Compute.SizeSelector** depende da assinatura e do local para determinar a lista de tamanhos disponíveis para o usuário. Portanto, **Microsoft.Compute.SizeSelector** só pode ser usado em steps. Geralmente, apenas os elementos no namespace **Microsoft.Common** podem ser usados em basics. No entanto, alguns elementos de outros namespaces (como **Microsoft.Compute.Credentials**) que não dependem do contexto do usuário ainda são permitidos.

## <a name="steps"></a>Etapas
A propriedade steps pode conter uma ou mais etapas adicionais para exibir depois de basics, cada uma contendo um ou mais elementos. Considere adicionar etapas por função ou camada do aplicativo que está sendo implantado. Por exemplo, adicione uma etapa nas entradas para os nós mestres e uma etapa para os nós de trabalho em um cluster.

## <a name="outputs"></a>outputs
O portal do Azure usa a propriedade `outputs` para mapear os elementos de `basics` e `steps` para os parâmetros do modelo de implantação do Azure Resource Manager. As chaves do dicionário são os nomes dos parâmetros do modelo, e os valores são propriedades dos objetos da saída dos elementos referenciados.

## <a name="functions"></a>Funções
Semelhantemente às [funções de modelo](resource-group-template-functions.md) no Azure Resource Manager (tanto em sintaxe quanto em funcionalidade), CreateUiDefinition fornece funções para trabalhar com entradas e saídas dos elementos e recursos como condicionais.

## <a name="next-steps"></a>Próximas etapas
O próprio CreateUiDefinition tem um esquema simples. A profundidade real vem de todos os elementos e funções com suporte que os documentos abaixo descrevem em muitos detalhes:

- [Elementos](managed-application-createuidefinition-elements.md)
- [Funções](managed-application-createuidefinition-functions.md)

Um esquema JSON atual para CreateUiDefinition está disponível aqui: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json. 

Versões mais recentes serão disponibilizadas no mesmo local. Substitua a parte `0.1.2-preview` da URL e o valor `version` pelo identificador de versão que você pretende usar. Os identificadores de versão com suporte atualmente são `0.0.1-preview`, `0.1.0-preview`, `0.1.1-preview` e `0.1.2-preview`.