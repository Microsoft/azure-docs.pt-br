---
title: Usar runbooks e módulos da Automação do Azure no Galeria do PowerShell
description: Este artigo mostra como usar runbooks e módulos da Microsoft e da comunidade na Galeria do PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 01/08/2021
ms.topic: conceptual
ms.openlocfilehash: 590220782a7f43e785cc7885e68eefa99afb7d1d
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98049109"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>Usar runbooks e módulos na Galeria do PowerShell

Em vez de criar seus próprios runbooks e módulos na Automação do Azure, você pode acessar cenários que já foram desenvolvidos pela Microsoft e pela comunidade. Você pode obter runbooks e [módulos](#modules-in-powershell-gallery) do PowerShell dos runbooks Galeria do PowerShell e [Python](#use-python-runbooks) da organização GitHub de automação do Azure. Você também pode contribuir para a comunidade compartilhando [cenários que você desenvolve](#add-a-powershell-runbook-to-the-gallery).

> [!NOTE]
> O TechNet Script Center está sendo desativado. Todos os runbooks do script Center na Galeria de Runbooks foram movidos para nossa [organização do GitHub de automação](https://github.com/azureautomation) para obter mais informações, consulte [aqui](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="runbooks-in-powershell-gallery"></a>Runbooks na Galeria do PowerShell

A [Galeria do PowerShell](https://www.powershellgallery.com/packages) fornece vários runbooks da Microsoft e da comunidade que você pode importar para a Automação do Azure. Para usar runbooks, baixe-os da galeria ou importe-os diretamente da galeria ou da sua conta da Automação no portal do Azure.

> [!NOTE]
> Não há suporte para runbooks gráficos no Galeria do PowerShell.

Você só pode importar diretamente da Galeria do PowerShell por meio do portal do Microsoft Azure. Não é possível executar essa função com o PowerShell.

> [!NOTE]
> Você deve validar o conteúdo de todos os runbooks obtidos da Galeria do PowerShell e ter muito cuidado ao instalá-los e executá-los em um ambiente de produção.

## <a name="modules-in-powershell-gallery"></a>Módulos na Galeria do PowerShell

Os módulos do PowerShell contêm cmdlets que você pode usar em seus runbooks, e os módulos existentes que podem ser instalados na Automação do Azure estão disponíveis na [Galeria do PowerShell](https://www.powershellgallery.com). Você pode iniciar a galeria no portal do Azure e instalá-los diretamente na Automação do Azure. Você também pode baixá-los e instalá-los manualmente.

## <a name="common-scenarios-available-in-powershell-gallery"></a>Cenários comuns disponíveis na Galeria do PowerShell

A lista a seguir contém alguns runbooks que dão suporte a cenários comuns. Para obter uma lista completa de runbooks criados pela equipe de Automação do Azure, confira o [perfil da AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) – importa a versão mais recente de todos os módulos em uma conta de Automação da Galeria do PowerShell.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) – configura o Diagnóstico do Azure e o Log Analytics para receber logs da Automação do Azure que contêm o status do trabalho e os fluxos de trabalho.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/)-copia um arquivo remoto de uma máquina virtual do Windows Azure.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) -copia um arquivo local para uma máquina virtual do Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Importar um runbook do PowerShell da galeria de runbooks com o portal do Azure

1. No portal do Azure, abra sua conta da Automação.
2. Selecione **Galeria de runbooks** em **Automação de Processos**.
3. Selecione **Fonte: Galeria do PowerShell**.
4. Localize o item da galeria desejado e selecione-o para exibir seus detalhes. À esquerda, você pode inserir parâmetros de pesquisa adicionais para o tipo e o fornecedor.

   ![Procurar na galeria](media/automation-runbook-gallery/browse-gallery.png)

5. Clique em **Exibir projeto de origem** para exibir o item na [organização GitHub de automação do Azure](https://github.com/azureautomation).
6. Para importar um item, clique nele para exibir os detalhes e, em seguida, clique em **Importar**.

   ![Botão Importar](media/automation-runbook-gallery/gallery-item-detail.png)

7. Opcionalmente, altere o nome do runbook e clique em **OK** para importar o runbook.
8. O runbook aparece na guia **Runbooks** da conta de Automação.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Adicionar um runbook do PowerShell à Galeria

A Microsoft incentiva você a adicionar runbooks que você considere úteis para outros clientes à Galeria do PowerShell. A Galeria do PowerShell aceita módulos do PowerShell e scripts do PowerShell. Você pode adicionar um runbook [carregando-o na Galeria do PowerShell](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Importar um módulo da galeria de módulos com o portal do Azure

1. No portal do Azure, abra sua conta da Automação.
2. Selecione **Módulos** em **Recursos Compartilhados** para abrir a lista de módulos.
3. Clique em **Procurar na Galeria** na parte superior da página.

   ![Galeria de módulos](media/automation-runbook-gallery/modules-blade.png)

4. Na página Procurar na Galeria, você pode pesquisar pelos seguintes campos:

   * Nome do módulo
   * Marcas
   * Autor
   * Nome do recurso do DSC/cmdlet

5. Localize o módulo em que você está interessado e selecione-o para exibir seus detalhes.

   Quando você analisar um módulo específico, poderá exibir mais informações. Essas informações incluem um link para a Galeria do PowerShell, para as dependências necessárias e para todos os cmdlets ou recursos de DSC que o módulo contém.

   ![Detalhes do módulo do PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Para instalar o módulo diretamente na Automação do Azure, clique em **Importar**.
7. No painel Importar, é possível ver o nome do módulo a ser importado. Se todas as dependências estiverem instaladas, o botão **OK** será ativado. Em caso de dependências ausentes, será necessário importar tais dependências antes de importar o módulo.
8. No painel Importar, clique em **OK** para importar o módulo. Enquanto a Automação do Azure importa um módulo para sua conta, ela extrai os metadados sobre o módulo e os cmdlets. Essa ação pode levar alguns minutos, pois cada atividade precisa ser extraída.
9. Você recebe uma notificação inicial informando que o módulo está sendo implantado e outra notificação quando o processo é concluído.
10. Depois que o módulo é importado, você pode ver as atividades disponíveis. Você pode usar recursos de módulo em seus runbooks e recursos de DSC.

> [!NOTE]
> Os módulos que suportam apenas o núcleo do PowerShell não são suportados na Automação do Azure e não podem ser importados no portal do Azure ou implantados diretamente da Galeria do PowerShell.

## <a name="use-python-runbooks"></a>Usar runbooks do Python

Os Runbooks do Python estão disponíveis na [organização GitHub da automação do Azure](https://github.com/azureautomation). Quando você contribuir com nosso repositório GitHub, adicione a marca **(tópico do GitHub): Python3** ao carregar sua contribuição.

## <a name="request-a-runbook-or-module"></a>Solicitar um runbook ou módulo

Você pode enviar solicitações para a [Voz do Usuário](https://feedback.azure.com/forums/246290-azure-automation/).  Se você precisar de ajuda para escrever um runbook ou tiver dúvidas sobre o PowerShell, poste uma questão na [página de perguntas da Microsoft](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar um runbook do PowerShell, confira [Tutorial: criar um runbook do PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Para se familiarizar com os runbooks, confira [Gerenciar runbooks na Automação do Azure](manage-runbooks.md).
* Para obter detalhes do PowerShell, confira [Documentos do PowerShell](/powershell/scripting/overview).
* Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
