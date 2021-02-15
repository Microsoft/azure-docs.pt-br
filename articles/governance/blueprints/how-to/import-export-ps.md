---
title: Importar e exportar plantas com o PowerShell
description: Saiba como trabalhar com suas definições de plano gráfico como código. Compartilhe, controle do código-fonte e gerencie-os usando os comandos exportar e importar.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: a5b1adda0b02e2e2490441c5958ca9334febc24c
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919979"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Importar e exportar definições de Blueprint com o PowerShell

Os planos gráficos do Azure podem ser totalmente gerenciados por meio de portal do Azure. À medida que as organizações avançam em seu uso de plantas do Azure, elas devem começar a pensar em definições de plantas como código gerenciado. Esse conceito é geralmente chamado de infraestrutura como código (IaC). Tratar suas definições de plantas como código oferece vantagens adicionais além do que portal do Azure oferece. Esses benefícios incluem:

- Compartilhando definições de plantas
- Fazendo backup de suas definições de Blueprint
- Reutilizando definições de Blueprint em diferentes locatários ou assinaturas
- Colocando as definições do plano gráfico no controle do código-fonte
  - Teste automatizado de definições de plantas em ambientes de teste
  - Suporte de pipelines de integração contínua e implantação contínua (CI/CD)

Seja qual for o seu motivo, gerenciar suas definições de plantas como código tem benefícios. Este artigo mostra como usar os `Import-AzBlueprintWithArtifact` comandos e `Export-AzBlueprintWithArtifact` no módulo [AZ. Blueprint](https://powershellgallery.com/packages/Az.Blueprint/) .

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe um conhecimento de trabalho moderado de plantas do Azure. Se você ainda não tiver feito isso, trabalhe nos seguintes artigos:

- [Criar um blueprint no portal](../create-blueprint-portal.md)
- Leia sobre os [estágios de implantação](../concepts/deployment-stages.md) e [o ciclo de vida do Blueprint](../concepts/lifecycle.md)
- [Criando](../create-blueprint-powershell.md) e [Gerenciando](./manage-assignments-ps.md) definições e atribuições de plantas com o PowerShell

Se ainda não estiver instalado, siga as instruções em [Adicionar o módulo Az.Blueprint](./manage-assignments-ps.md#add-the-azblueprint-module) para instalar e validar o módulo **Az.Blueprint** na Galeria do PowerShell.

## <a name="folder-structure-of-a-blueprint-definition"></a>Estrutura de pastas de uma definição de Blueprint

Antes de examinar a exportação e a importação de plantas, vejamos como os arquivos que compõem a definição do Blueprint são estruturados. Uma definição de Blueprint deve ser armazenada em sua própria pasta.

> [!IMPORTANT]
> Se nenhum valor for passado para o parâmetro **Name** do `Import-AzBlueprintWithArtifact` cmdlet, o nome da pasta na qual a definição Blueprint é armazenada será usado.

Junto com a definição do Blueprint, que deve ser nomeada `blueprint.json` , são os artefatos dos quais a definição do Blueprint é composta. Cada artefato deve estar na subpasta chamada `artifacts` .
Juntas, a estrutura de sua definição de plantas como arquivos JSON em pastas deve ter a seguinte aparência:

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>Exportar sua definição de Blueprint

As etapas para exportar sua definição de planta são simples. A exportação da definição do Blueprint pode ser útil para compartilhar, fazer backup ou colocar no controle do código-fonte.

- **Blueprint** [obrigatório]
  - Especifica a definição do Blueprint
  - Use `Get-AzBlueprint` para obter o objeto de referência
- **OutputPath** [obrigatório]
  - Especifica o caminho para salvar os arquivos JSON de definição do Blueprint em
  - Os arquivos de saída estão em uma subpasta com o nome da definição do Blueprint
- **Versão** (opcional)
  - Especifica a versão a ser impressa se o objeto de referência **Blueprint** contiver referências a mais de uma versão.

1. Obtenha uma referência à definição do Blueprint para exportar da assinatura representada como `{subId}` :

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Use o `Export-AzBlueprintWithArtifact` cmdlet para exportar a definição de Blueprint especificada:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>Importar sua definição de Blueprint

Depois de ter uma [definição de Blueprint exportada](#export-your-blueprint-definition) ou ter uma definição de Blueprint criada manualmente na [estrutura de pastas necessária](#folder-structure-of-a-blueprint-definition), você poderá importar essa definição de Blueprint para um grupo de gerenciamento ou assinatura diferente.

Para obter exemplos de definições de plantas internas, consulte o [Azure Blueprint repositório GitHub](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins).

- **Nome** [obrigatório]
  - Especifica o nome da nova definição de Blueprint
- **InputPath** [obrigatório]
  - Especifica o caminho do qual criar a definição do Blueprint
  - Deve corresponder à [estrutura de pastas necessária](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (opcional)
  - A ID do grupo de gerenciamento para salvar a definição de plano gráfico se não for o padrão de contexto atual
  - **ManagementGroupId** ou **SubscriptionId** devem ser especificados
- **SubscriptionId** (opcional)
  - A ID da assinatura para salvar a definição do plano gráfico se não for o padrão de contexto atual
  - **ManagementGroupId** ou **SubscriptionId** devem ser especificados

1. Use o `Import-AzBlueprintWithArtifact` cmdlet para importar a definição de Blueprint especificada:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

Depois que a definição do Blueprint for importada, [atribua-a com o PowerShell](./manage-assignments-ps.md#create-blueprint-assignments).

Para obter informações sobre como criar definições de plantas avançadas, consulte os seguintes artigos:

- Use [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Personalize a [ordem de sequenciamento do plano gráfico](../concepts/sequencing-order.md).
- Proteger implantações com o [bloqueio de recursos do Blueprint](../concepts/resource-locking.md).
- [Gerencie plantas como código](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).