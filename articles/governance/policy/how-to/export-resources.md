---
title: Exportar recursos do Azure Policy
description: Aprenda a exportar recursos do Azure Policy para o GitHub, como definições de política e atribuições de política.
ms.date: 10/29/2020
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 923b063244f6f47def1c3e6a63d6e4d6b3b88083
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94919558"
---
# <a name="export-azure-policy-resources"></a>Exportar recursos do Azure Policy

Este artigo fornece informações sobre como exportar os recursos existentes do Azure Policy. Exportar seus recursos é útil e recomendado para backup, mas também é uma etapa importante em sua jornada com governança de nuvem e tratar sua [política como código](../concepts/policy-as-code.md). Azure Policy recursos podem ser exportados por meio de [portal do Azure](#export-with-azure-portal), [CLI do Azure](#export-with-azure-cli), [Azure PowerShell](#export-with-azure-powershell)e cada um dos SDKs com suporte.

## <a name="export-with-azure-portal"></a>Exportar com portal do Azure

Para exportar uma definição de política do portal do Azure, siga estas etapas:

1. Inicie o serviço de Azure Policy no portal do Azure clicando em **Todos os serviços**, em seguida pesquisando e selecionando **Política**.

1. Selecione **Definições** no lado esquerdo da página do Azure Policy.

1. Use o botão **Exportar definições** ou selecione as reticências na linha de uma definição de política. Em seguida, selecione **Exportar definição**.

1. Selecione o botão **Entrar com o GitHub**. Se você ainda não tiver feito a autenticação com o GitHub para autorizar o Azure Policy a exportar o recurso, examine o acesso de que a [Ação do GitHub](https://github.com/features/actions) precisa na nova janela aberta e selecione **Autorizar AzureGitHubActions** para dar continuidade ao processo de exportação. Após a conclusão, a nova janela será fechada automaticamente.

1. Na guia **Noções Básicas**, defina as seguintes opções e selecione a guia **Políticas** ou o botão **Avançar: Políticas** na parte inferior da página.

   - **Filtro de repositório**: defina como _Meus repositórios_ para ver apenas os repositórios que você tem ou _Todos os repositórios_ para ver todos aqueles aos quais você concedeu à Ação do GitHub acesso.
   - **Repositório**: defina como o repositório ao qual você deseja exportar os recursos do Azure Policy.
   - **Branch**: defina o branch no repositório. Usar um branch diferente do padrão é uma boa maneira de validar suas atualizações antes de mesclá-las em seu código-fonte.
   - **Diretório**: a _pasta de nível raiz_ à qual exportar os recursos do Azure Policy. As subpastas nesse diretório são criadas com base em quais recursos são exportados.

1. Na guia **Políticas**, defina o escopo a ser pesquisado selecionando as reticências e escolhendo uma combinação de grupos de gerenciamento, assinaturas ou grupos de recursos.
   
1. Use o botão **Adicionar definições de política** para pesquisar o escopo para os objetos a serem exportados. Na janela lateral aberta, selecione cada objeto a ser exportado. Filtre a seleção pela caixa de pesquisa ou pelo tipo. Após selecionar todos os objetos a serem exportados, use o botão **Adicionar** na parte inferior da página.

1. Para cada objeto selecionado, selecione as opções de exportação desejadas, como _Somente Definição_ ou _Definição e Atribuições_ para uma definição de política. Em seguida, selecione a guia **Examinar + Exportar** ou o botão **Avançar: Examinar + Exportar** na parte inferior da página.

   > [!NOTE]
   > Se a opção _Definição e Atribuições_ for escolhida, somente serão exportadas as atribuições de política dentro do escopo definido pelo filtro quando a definição de política for adicionada.

1. Na guia **Examinar + Exportar**, verifique se os detalhes coincidem e use o botão **Exportar** na parte inferior da página.

1. Verifique seu repositório do GitHub, o branch e a _pasta de nível raiz_ para ver que os recursos selecionados agora são exportados para o controle do código-fonte.

Os recursos do Azure Policy são exportados para a seguinte estrutura dentro do repositório GitHub selecionado e da _pasta de nível raiz_:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Exportar com CLI do Azure

Azure Policy definições, iniciativas e atribuições podem ser exportadas como JSON com [CLI do Azure](/cli/azure/install-azure-cli). Cada um desses comandos usa um parâmetro **Name** para especificar o objeto para o qual obter o JSON. A propriedade **Name** geralmente é um _GUID_ e não é o **DisplayName** do objeto.

- Definição – [Mostrar definição de política AZ](/cli/azure/policy/definition#az_policy_definition_show)
- Iniciativa- [conjunto de políticas de AZ-definição de definições](/cli/azure/policy/set-definition#az_policy_set_definition_show)
- Atribuição- [AZ política de atribuição mostrar](/cli/azure/policy/assignment#az_policy_assignment_show)

Aqui está um exemplo de como obter o JSON para uma definição de política com o **nome** de _VirtualMachineStorage_:

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Exportar com Azure PowerShell

Azure Policy definições, iniciativas e atribuições podem ser exportadas como JSON com [Azure PowerShell](/powershell/azure/). Cada um desses cmdlets usa um parâmetro **Name** para especificar o objeto para o qual obter o JSON. A propriedade **Name** geralmente é um _GUID_ e não é o **DisplayName** do objeto.

- Definição- [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- Iniciativa- [Get-AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- Atribuição- [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

Aqui está um exemplo de como obter o JSON para uma definição de política com o **nome** de _VirtualMachineStorage_:

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage' | ConvertTo-Json -Depth 10
```

## <a name="next-steps"></a>Próximas etapas

- Revise os exemplos em [Exemplos do Azure Policy](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Entenda como [criar políticas de maneira programática](programmatically-create.md).
- Saiba como [corrigir recursos fora de conformidade](remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
