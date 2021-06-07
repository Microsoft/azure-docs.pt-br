---
title: Converter modelos entre JSON e bicep
description: Descreve comandos para converter modelos de Azure Resource Manager de bicep para JSON e de JSON para bicep.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 6d242f5846996cd0f5b9510a1a2b9f2bf063a0c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103422141"
---
# <a name="converting-arm-templates-between-json-and-bicep"></a>Convertendo modelos de ARM entre JSON e bicep

Este artigo descreve como converter modelos de Azure Resource Manager (modelos ARM) de JSON em bicep e de bicep para JSON.

Você deve ter a [CLI do bicep instalada](bicep-install.md) para executar os comandos de conversão.

Os comandos de conversão produzem modelos que são funcionalmente equivalentes. No entanto, eles podem não ser exatamente iguais na implementação. Converter um modelo de JSON em bicep e, em seguida, voltar para JSON provavelmente resultará em um modelo com sintaxe diferente do modelo original. Quando implantado, os modelos convertidos produzem os mesmos resultados.

## <a name="convert-from-json-to-bicep"></a>Converter de JSON para bicep

A CLI do bicep fornece um comando para descompilar qualquer modelo JSON existente em um arquivo bicep. Para descompilar um arquivo JSON, use:

```azurecli
bicep decompile mainTemplate.json
```

Esse comando fornece um ponto de partida para a criação de bicep. O comando não funciona para todos os modelos. Atualmente, os modelos aninhados podem ser descompilados somente se usarem o escopo de avaliação da expressão ' interna '. Modelos que usam loops de cópia não podem ser descompilados.

## <a name="convert-from-bicep-to-json"></a>Converter de bicep para JSON

A CLI do bicep também fornece um comando para converter bicep em JSON. Para criar um arquivo JSON, use:

```azurecli
bicep build mainTemplate.bicep
```

## <a name="export-template-and-convert"></a>Exportar modelo e converter

Você pode exportar o modelo para um grupo de recursos e, em seguida, passá-lo diretamente para o comando de descompilação. O exemplo a seguir mostra como descompilar um modelo exportado.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Exporte o modelo](export-template-portal.md) por meio do Portal.

Use `bicep decompile <filename>` no arquivo baixado.

---

## <a name="side-by-side-view"></a>Exibição lado a lado

O [bicep playground](https://aka.ms/bicepdemo) permite exibir arquivos JSON e bicep equivalentes lado a lado. Você pode selecionar um modelo de exemplo para ver as duas versões. Ou então, selecione `Decompile` para carregar seu próprio modelo JSON e exibir o arquivo bicep equivalente.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre o bicep, consulte o [tutorial do bicep](./bicep-tutorial-create-first-bicep.md).
