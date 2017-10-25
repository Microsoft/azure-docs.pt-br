---
title: "Galerias de runbooks e módulos para a Automação do Azure | Microsoft Docs"
description: "Os runbooks e os módulos da Microsoft e da comunidade estão disponíveis para instalação e uso em seu ambiente da Automação do Azure.  Este artigo descreve como você pode acessar esses recursos e contribuir com seus runbooks para a Galeria."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: d3fee7b4-630a-4c10-8425-9bf51d7c9e58
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte;bwren
ms.openlocfilehash: e46c09a0cfecf07514f85b5801129fbd8162510f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galerias de runbook e de módulos para a Automação do Azure
Em vez de criar seus próprios runbooks e módulos na Automação do Azure, você pode acessar uma variedade de cenários que já foram desenvolvidos pela Microsoft e pela comunidade.  É possível usar esses cenários sem modificação ou usá-los como um ponto de partida e editá-los para seus requisitos específicos.

Você pode obter os runbooks na [Galeria do Runbook](#runbooks-in-runbook-gallery) e os módulos na [Galeria do PowerShell](#modules-in-powerShell-gallery).  Você também pode contribuir com a comunidade compartilhando os cenários que desenvolve.

## <a name="runbooks-in-runbook-gallery"></a>Runbooks na Galeria de Runbook
A [Galeria do Runbook](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) fornece vários runbooks da Microsoft e da comunidade que você pode importar para a Automação do Azure. Você pode baixar um runbook da galeria, que é hospedada no [Script Center da TechNet](https://gallery.technet.microsoft.com/scriptcenter/site/upload) ou pode importar runbooks diretamente da galeria usando o Portal Clássico do Azure ou o Portal do Azure.

Somente é possível importar diretamente da Galeria de Runbook usando o Portal Clássico do Azure ou o Portal do Azure. Não é possível executar essa função usando o Windows PowerShell.

> [!NOTE]
> Você deverá validar o conteúdo de todos os runbooks obtidos da Galeria de Runbook e tomar muito cuidado ao instalá-los e executá-los em um ambiente de produção.|
> 
> 

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-classic-portal"></a>Para importar um runbook da Galeria de Runbook com o Portal Clássico do Azure
1. No Portal do Azure, clique em **Novo**, **Serviços de Aplicativos**, **Automação**, **Runbook**, **Na Galeria**.
2. Selecione uma categoria para exibir os runbooks relacionados e selecione um runbook para exibir seus detalhes. Quando selecionar o runbook desejado, clique no botão de seta para a direita.
   
    ![Galeria de Runbook](media/automation-runbook-gallery/runbook-gallery.png)
3. Examine o conteúdo do runbook e observe todos os requisitos na descrição. Clique no botão com a seta para a direita quando concluir.
4. Insira os detalhes do runbook e clique no botão com a marca de seleção. O nome do runbook já está preenchido.
5. O runbook aparece na guia **Runbooks** da Conta de Automação.

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Para importar um runbook da Galeria de Runbook com o portal do Azure
1. No portal do Azure, abra sua conta da Automação.
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.
3. Clique no botão **Procurar na galeria** .
   
    ![Botão Procurar na galeria](media/automation-runbook-gallery/browse-gallery-button.png)
4. Localize o item da galeria desejado e selecione-o para exibir seus detalhes.
   
    ![Procurar na galeria](media/automation-runbook-gallery/browse-gallery.png)
5. Clique em **Exibir projeto de origem** para exibir o item no [Script Center do TechNet](http://gallery.technet.microsoft.com/).
6. Para importar um item, clique nele para exibir seus detalhes e clique no botão **Importar** .
   
    ![Botão Importar](media/automation-runbook-gallery/gallery-item-detail.png)
7. Opcionalmente, altere o nome do runbook e clique em **OK** para importar o runbook.
8. O runbook aparece na guia **Runbooks** da Conta de Automação.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Adicionando um runbook à galeria de runbook
A Microsoft incentiva você a adicionar à Galeria de Runbook os runbooks que, em sua opinião, seriam úteis para outros clientes.  Você pode adicionar um runbook [carregando-o no Script Center](http://gallery.technet.microsoft.com/site/upload) levando em consideração os detalhes a seguir.

* Você deve especificar *Windows Azure* para a **Categoria** e *Automação* para a **Subcategoria** do runbook ser exibido no assistente.  
* O carregamento deve ser um único arquivo .ps1 ou .graphrunbook.  Se o runbook exigir módulos, runbooks filhos ou ativos, você deverá listá-los na descrição do envio e na seção de comentários do runbook.  Se você tiver um cenário que exija vários runbooks, carregue cada um separadamente e liste os nomes dos runbooks relacionados em cada uma das descrições. Use as mesmas marcas, de forma que elas sejam mostradas na mesma categoria. Um usuário terá de ler a descrição para saber quais outros runbooks serão necessários para que o cenário funcione.
* Adicione a marca "GraphicalPS" se estiver publicando um **Runbook gráfico** (e não um Fluxo de Trabalho Gráfico). 
* Insira um trecho de código do PowerShell ou do Fluxo de Trabalho do PowerShell na descrição usando o ícone **Inserir seção de código** .
* O Resumo do upload é exibido nos resultados da Galeria de Runbooks e, portanto, você deve fornecer informações detalhadas que ajudam um usuário a identificar a funcionalidade do runbook.
* Você deve atribuir ao carregamento de uma a três das Marcas a seguir.  O runbook é listado no assistente nas categorias correspondentes às suas marcas.  As marcas que não estiverem nessa lista serão ignoradas pelo assistente. Se você não especificar nenhuma marca correspondente, o runbook será listado na categoria Outros.
  
  * Backup
  * Gerenciamento de Capacidade
  * Controle de Alterações
  * Conformidade
  * Ambientes de Desenvolvimento/Teste
  * Recuperação de desastre
  * Monitoramento
  * Aplicação de patch
  * Provisionamento
  * Correção
  * Gerenciamento do ciclo de vida de VMs
* A automação atualiza a Galeria uma vez por hora e, portanto, você não verá suas contribuições imediatamente.

## <a name="modules-in-powershell-gallery"></a>Módulos na Galeria do PowerShell
Os módulos do PowerShell contêm cmdlets que você pode usar em seus runbooks, e os módulos existentes que podem ser instalados na Automação do Azure estão disponíveis na [Galeria do PowerShell](http://www.powershellgallery.com).  Você pode inicializar esta galeria no Portal do Azure e instalá-los diretamente na Automação do Azure ou pode baixá-los e instalá-los manualmente.  Não é possível instalar os módulos diretamente do Portal Clássico do Azure, mas você pode baixá-los e instalá-los como o faria com qualquer módulo.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Para importar um módulo da Galeria de Módulos de Automação com o portal do Azure
1. No portal do Azure, abra sua conta da Automação.
2. Selecione **Módulos** em **Recursos Compartilhados** para abrir a lista de módulos.
4. Clique em **Procurar na Galeria** na parte superior da página.
   
    ![Galeria de módulos](media/automation-runbook-gallery/modules-blade.png) <br>
5. Na página **Procurar na Galeria**, é possível pesquisar pelos seguintes campos:
   
   * Nome do Módulo
   * Marcas
   * Autor
   * Nome do recurso do DSC/cmdlet
6. Localize o módulo em que você está interessado e selecione-o para exibir seus detalhes.  
   Ao fazer uma busca detalhada em um módulo específico, é possível exibir mais informações sobre ele, incluindo um link de volta à Galeria do PowerShell, quaisquer dependências necessárias e todos os recursos de cmdlets e/ou DSC que o módulo contém.
   
    ![Detalhes do módulo do PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>
7. Para instalar o módulo diretamente na Automação do Azure, clique no botão **Importar** .
   
    ![Botão Importar módulo](media/automation-runbook-gallery/module-import-button.png)
8. Ao clicar no botão Importar, no painel **Importar**, você verá o nome do módulo que está prestes a importar. Se todas as dependências estiverem instaladas, o botão **OK** será ativado. Se não houver dependências, será necessário importá-las antes de importar esse módulo.
9. Clique em **OK** para importar o módulo. Enquanto a Automação do Azure importa um módulo para sua conta, ela extrai os metadados sobre o módulo e os cmdlets.
   
    ![Página Importar Módulo](media/automation-runbook-gallery/module-import-blade.png)
   
    Isso pode demorar alguns minutos, pois cada atividade precisa ser extraída.
10. Você recebe uma notificação inicial informando que o módulo está sendo implantado e outra notificação quando o processo é concluído.
11. Depois que o módulo for importado, você verá as atividades disponíveis e será possível usar os recursos nos runbooks e a Desired State Configuration.

## <a name="requesting-a-runbook-or-module"></a>Solicitando um runbook ou um módulo
Você pode enviar solicitações para a [Voz do Usuário](https://feedback.azure.com/forums/246290-azure-automation/).  Se precisar de ajuda para escrever um runbook ou se tiver uma pergunta sobre o PowerShell, poste uma pergunta em nosso [fórum](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Próximas etapas
* Para começar a usar os runbooks, confira [Criando ou importando um runbook na Automação do Azure](automation-creating-importing-runbook.md)
* Para entender as diferenças entre o PowerShell e o os fluxos de trabalho do PowerShell com runbooks, confira [Aprendendo sobre o fluxo de trabalho do PowerShell](automation-powershell-workflow.md)

