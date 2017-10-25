---
title: "Usar o PowerShell para criar uma atribuição de política para identificar recursos sem conformidade em seu ambiente do Azure | Microsoft Docs"
description: "Use o PowerShell para criar uma atribuição de Política do Azure para identificar recursos sem conformidade."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 10/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 3f9ef7886af20845eddc4c1e71d60911e4b22eca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>Crie uma atribuição de política para identificar recursos sem conformidade em seu ambiente do Azure usando o PowerShell

A primeira etapa para compreender a conformidade no Azure é saber qual é a situação de seus recursos atuais. Este guia de início rápido orienta você quanto ao processo de criação de uma atribuição de política para identificar recursos sem conformidade com a definição de política – *Exigir SQL Server versão 12.0*. No final deste processo, você terá identificado com êxito quais servidores são de uma versão diferente e, essencialmente, sem conformidade.

O PowerShell é usado para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia descreve com detalhes o uso PowerShell para criar uma atribuição de política para identificar recursos sem conformidade em seu ambiente do Azure.

Este guia exige o módulo do Azure PowerShell, versão 4.0 ou posterior. Execute ```Get-Module -ListAvailable AzureRM``` para encontrar a versão. Se você precisa instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

Antes de começar, verifique se a versão mais recente do PowerShell está instalada. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azureps-cmdlets-docs).

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="opt-in-to-azure-policy"></a>Aceitar a Política do Azure

A Política do Azure está disponível na versão prévia limitada e, portanto, você precisa se registrar para solicitar acesso.

1. Vá até a Política do Azure em https://aka.ms/getpolicy e selecione **Inscrever-se** no painel esquerdo.

   ![Pesquisar pela política](media/assign-policy-definition/sign-up.png)

2. Aceite a Política do Azure selecionando na lista **Assinatura** as assinaturas com que gostaria de trabalhar. Em seguida, selecione **Registrar**.

   ![Aceitar o uso da Política do Azure](media/assign-policy-definition/preview-opt-in.png)

   Pode levar alguns dias para aceitarmos sua solicitação de registro, com base na demanda. Após sua solicitação ser aceita, você receberá um email informando que pode começar a usar o serviço.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, criamos uma atribuição de política e atribuímos a definição *Exigir SQL Server versão 12.0*. Esta definição de política identificará recursos que não são compatíveis com as condições configuradas na definição de política.

Siga estas etapas para criar uma nova atribuição de política.

Execute o seguinte comando para exibir todas as definições de política e encontrar a que você deseja atribuir:

```powershell
$definition = Get-AzureRmPolicyDefinition
```

A Política do Azure vem com definições de políticas internas que você pode usar. Você verá definições de políticas internas como:

- Impor marca e seu valor
- Aplicar marca e seu valor
- Requer o SQL Server versão 12.0

Em seguida, atribua a definição de política ao escopo desejado usando o cmdlet `New-AzureRmPolicyAssignment`.

Para este tutorial, estamos fornecendo as informações a seguir para o comando:
- **Nome** de exibição da atribuição de política. Neste caso, vamos usar Atribuição Exigir SQL Server versão 12.0.
- **Política** – trata-se da definição da política, com base naquela que você está usando para criar a atribuição. Neste caso, é a definição de política – *Exigir SQL Server versão 12.0*
- Um **escopo** – um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Pode variar de uma assinatura a grupos de recursos. Neste exemplo, estamos atribuindo a definição de política ao grupo de recursos **FabrikamOMS**.
- **$definition** – você precisa fornecer a ID de recurso da definição de política – Neste caso, estamos usando a ID da definição de política – *Exigir SQL Server 12.0*.

```powershell
$rg = Get-AzureRmResourceGroup -Name "FabrikamOMS"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
New-AzureRMPolicyAssignment -Name Require SQL Server version 12.0 Assignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

Agora, você está pronto para identificar recursos sem conformidade para compreender o estado de conformidade de seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos sem conformidade

1. Navegue de volta até a página de aterrissagem da Política do Azure.
2. Selecione **Conformidade** no painel esquerdo e pesquise a **Atribuição de Política** criada por você.

   ![Conformidade da política](media/assign-policy-definition/policy-compliance.png)

   Se houver recursos sem conformidade com essa nova atribuição, eles aparecerão na guia **Recursos sem conformidade**, como mostrado acima.

## <a name="clean-up-resources"></a>Limpar recursos

Outros guias desta coleção dão continuidade a este guia de início rápido. Se você planeja continuar trabalhando com os tutoriais subsequentes, não limpe os recursos criados neste guia de início rápido. Se você não planeja continuar, exclua a atribuição criada executando este comando:

```powershell
Remove-AzureRmPolicyAssignment -Name “Require SQL Server version 12.0 Assignment” -Scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você atribuiu uma definição de política para identificar recursos sem conformidade em seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas, para garantir que recursos que você criar no **futuro** estejam em conformidade, continue com o tutorial:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./create-manage-policy.md)