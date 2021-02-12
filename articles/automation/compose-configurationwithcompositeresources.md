---
title: Compor configurações DSC
description: Este artigo explica como compor configurações usando recursos compostos no State Configuration da Automação do Azure.
keywords: dsc do powershell, configuração de estado desejada, powershell dsc azure, recursos compostos
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 1b1bbb12412deec6ecac8cf1ffd47a00f778862e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98894721"
---
# <a name="compose-dsc-configurations"></a>Compor configurações DSC

Quando você precisa gerenciar recursos com mais de uma única configuração de estado desejada (DSC), a melhor opção é usar [recursos compostos](/powershell/scripting/dsc/resources/authoringresourcecomposite). Um recurso composto é uma configuração aninhada e parametrizada sendo usada como um recurso DSC em outra configuração. O uso de recursos compostos permite criar configurações complexas enquanto os recursos compostos subjacentes são gerenciados e construídos individualmente.

A Automação do Azure permite a [importação e a compilação de recursos compostos](automation-dsc-compile.md). Depois de importar recursos de composição para sua conta de automação, você poderá usar a configuração de estado de automação do Azure por meio do recurso de **configuração de estado (DSC)** no portal do Azure.

## <a name="compose-a-configuration"></a>Compor uma configuração

Antes de atribuir uma configuração definida a partir de recursos compostos no portal do Azure, você deve compor a configuração. Isso pode ser feito usando **Compor configuração** na página State Configuration (DSC) enquanto estiver nas guias **Configurações** ou **Configurações compiladas**.

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página Conta da Automação, selecione **State Configuration (DSC)** em **Gerenciamento de Configuração**.
1. Na página State Configuration (DSC), clique na guia **Configurações** ou **Configurações compiladas**, e clique em **Compor configuração** no menu na parte superior da página.
1. Na etapa **Básico**, forneça o novo nome de configuração (obrigatório) e clique em qualquer lugar na linha de cada recurso composto que você deseja incluir em sua nova configuração. Em seguida, clique em **Avançar** ou clique no passo **código fonte**. Para as etapas a seguir, selecionamos os recursos compostos `PSExecutionPolicy` e `RenameAndDomainJoin`.
   ![Captura de tela da etapa básica da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. O **código-fonte** etapa mostra a aparência a configuração composta dos recursos de composição selecionados. Você pode ver a mesclagem de todos os parâmetros e como eles são passados para o recurso composto. Quando você terminar de revisar o novo código-fonte, clique em **Próximo** ou clique na etapa **Parâmetros**.
   ![Captura de tela da etapa de código de origem da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Na etapa **Parâmetros**, o parâmetro para cada recurso composto é exposto para que os valores possam ser fornecidos. Se um parâmetro tiver uma descrição, ele será exibido ao lado do campo do parâmetro. Se um parâmetro for do tipo `PSCredential`, o menu suspenso fornecerá uma lista de objetos **Credencial** na conta da Automação atual. Uma opção **+ Adicionar uma credencial** também está disponível. Depois que todos os parâmetros necessários tiverem sido fornecidos, clique em **Salvar e compilar**.
   ![Captura de tela da etapa de parâmetros da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

## <a name="submit-the-configuration-for-compilation"></a>Enviar a configuração para compilação

Depois que a nova configuração é salva, ela é enviada para compilação. Você pode exibir o status do trabalho de compilação como em qualquer configuração importada. Para saber mais, confira [Exibir um trabalho de compilação](automation-dsc-getting-started.md#view-a-compilation-job).

Quando a compilação é completada com sucesso, a nova configuração aparece na aba  **Configuração Compilada**. Em seguida, você pode atribuir a configuração a um nó gerenciado, usando as etapas em [Reatribuir um nó a uma configuração de nó diferente](automation-dsc-getting-started.md#reassign-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Próximas etapas

- Para saber como habilitar os nós, confira [Habilitar o serviço State Configuration da Automação do Azure](automation-dsc-onboarding.md).
- Para saber como compilar configurações DSC para que possam ser atribuídas a nós de destino, confira [Compilar configurações DSC no State Configuration da Automação do Azure](automation-dsc-compile.md).
- Para ver um exemplo de uso do State Configuration da Automação do Azure em um pipeline de implantação contínua, confira [Configurar a implantação contínua com o Chocolatey](automation-dsc-cd-chocolatey.md).
- Para saber mais sobre preços, confira [Preços do State Configuration da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
