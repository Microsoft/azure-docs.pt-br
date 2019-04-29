---
title: Compor configurações de DSC na configuração do estado de automação do Azure (DSC) usando recursos compostos
description: Aprenda como redigir configurações usando recursos compostos na Configuração de estado de automação do Azure (DSC)
keywords: dsc do powershell, configuração de estado desejada, powershell dsc azure, recursos compostos
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/21/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 64588829cec964e52dcb44465869e0090f36f9f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61303915"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Compor configurações de DSC na configuração do estado de automação do Azure (DSC) usando recursos compostos

Quando um recurso precisa ser gerenciado com mais de uma configuração de configuração de estado desejado (DSC), o melhor caminho é usar [recursos compostos](/powershell/dsc/authoringresourcecomposite). Um recurso composto é uma configuração aninhada e parametrizada sendo usada como um recurso DSC em outra configuração. Isso permite a criação de configurações complexas, permitindo que os recursos compostos subjacentes (configurações parametrizadas) sejam gerenciados e construídos individualmente.

A Automação do Azure permite a [importação e a compilação de recursos compostos](automation-dsc-compile.md#composite-resources). Depois que os recursos compostos tiverem sido importados para sua conta de automação, você poderá usar a experiência **Compor configuração** na página **Configuração do estado (DSC)**.

## <a name="composing-a-configuration-from-composite-resources"></a>Compor uma configuração a partir de recursos compostos

Antes de atribuir uma configuração feita a partir de recursos compostos no portal do Azure, você deve escrevê-la. Isso pode ser feito usando **Configuração do Compose** na página **Configuração de Estado (DSC)** enquanto estiver nas guias **Configurações** ou  **Configuração Compilada**.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página **Conta de Automação**, selecione **Configuração de estado (DSC)** em **Gerenciamento de Configuração**.
1. Na página **Configuração de estado (DSC)**, clique na guia **Configurações** ou **Configurações compiladas** e clique em **Compor configuração** no menu em o topo da página.
1. Na etapa **Básico**, forneça o novo nome de configuração (obrigatório) e clique em qualquer lugar na linha de cada recurso composto que você deseja incluir em sua nova configuração. Em seguida, clique em **Avançar** ou clique no passo **código fonte**. Para as etapas a seguir, selecionamos **PSExecutionPolicy** e **recursos compostos de RenameAndDomainJoin**.
   ![Captura de tela da etapa básica da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. O **código-fonte** etapa mostra a aparência a configuração composta dos recursos de composição selecionados. Você pode ver a mesclagem de todos os parâmetros e como eles são passados para o recurso composto. Quando você terminar de revisar o novo código-fonte, clique em **Próximo** ou clique na etapa **Parâmetros**.
   ![Captura de tela da etapa de código de origem da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Na etapa **Parâmetros**, o parâmetro que cada recurso composto possui está exposto para que eles possam ser fornecidos. Se um parâmetro tiver uma descrição, ele será exibido ao lado do campo do parâmetro. Se um campo for um parâmetro do tipo **PSCredential**, o menu suspenso a configurar fornecerá uma lista de objetos **Credential** na conta de automação atual. Uma opção **+ adicionar uma credencial** é também estar disponível. Depois que todos os parâmetros necessários tiverem sido fornecidos, clique em **Salvar e compilar**.
   ![Captura de tela da etapa de parâmetros da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Depois que a nova configuração é salva, ela é enviada para compilação. O status do trabalho de compilação pode ser visualizado como qualquer configuração importada. Para mais informações, consulte [Visualizando um trabalho de compilação](automation-dsc-getting-started.md#viewing-a-compilation-job).

Quando a compilação é completada com sucesso, a nova configuração aparece na aba  **Configuração Compilada**. Quando estiver visível nessa guia, ele poderá ser atribuído a um nó gerenciado usando as etapas em [Reatribuindo um nó a uma configuração de nó diferente](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [Introdução à Configuração de Estado da Automação do Azure](automation-dsc-getting-started.md)
- Para saber mai sobre nós de integração, veja [Máquinas de integração para o gerenciamento pela Configuração do Estado de Automação do Azure](automation-dsc-onboarding.md)
- Para saber como compilar configurações de DSC para que possam ser atribuídas a nós de destino, consulte [Compilar configurações na Configuração de Estado da Automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [Cmdlets da Configuração de Estado da Automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [Preço da Configuração de Estado da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de uso da Configuração de Estado da Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua usando Configuração de Estado da Automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
