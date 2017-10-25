---
title: "Criar exibições para analisar dados no Log Analytics do OMS | Microsoft Docs"
description: "O Designer de modos de exibição do Log Analytics permite que você crie Exibições personalizadas que são exibidas no portal do Azure e no OMS e que contêm diferentes visualizações de dados no repositório do OMS. Este artigo contém uma visão geral do Designer de modos de exibição e dos procedimentos para criar e editar exibições personalizadas."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: e3c463d749dc4179df58286b9bb75584880a6bc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>Use o Designer de modos de exibição para criar exibições personalizadas no Log Analytics
O Designer de modos de exibição do [Log Analytics](log-analytics-overview.md) permite que você crie modos de exibição personalizados no console do OMS que contêm diferentes visualizações de dados no repositório do OMS. Este artigo contém uma visão geral do Designer de modos de exibição e dos procedimentos para criar e editar exibições personalizadas.

Outros artigos disponíveis para o Designer de Modos de Exibição são:

* [Referência de bloco](log-analytics-view-designer-tiles.md) – Referência das configurações de cada um dos blocos disponíveis para uso nas exibições personalizadas.
* [Referência de parte de visualização](log-analytics-view-designer-parts.md) – Referência das configurações para cada um dos blocos disponíveis para uso nas exibições personalizadas.

>[!NOTE]
> Se o seu espaço de trabalho tiver sido atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), consultas em todas as exibições deverão ser gravadas na [nova linguagem de consulta](https://go.microsoft.com/fwlink/?linkid=856078).  Todas as exibições que foram criadas antes de atualizar o espaço de trabalho serão automaticamente convertidas.

## <a name="concepts"></a>Conceitos
Exibições criadas com o Designer de modo de exibição contêm os elementos na tabela a seguir.

| Parte | Descrição |
|:--- |:--- |
| Bloco |Exibido no painel de visão geral do Log Analytics.  Inclui um resumo visual das informações contidas no modo de exibição personalizado.  Diferentes tipos de bloco fornecem visualizações diferentes de registros no repositório do OMS.  Clique no bloco para abrir o modo de exibição personalizado. |
| Exibição personalizada |Exibido quando o usuário clica no bloco.  Contém uma ou mais partes da visualização. |
| Partes da visualização |Visualização de dados no repositório do OMS com base em uma ou mais [pesquisas de log](log-analytics-log-searches.md).  A maioria das partes incluirá um cabeçalho que fornece uma visualização de alto nível e uma lista dos principais resultados.  Diferentes tipos de partes fornecem visualizações diferentes de registros no repositório do OMS.  Clique em elementos na parte para realizar uma pesquisa de log fornecendo registros detalhados. |

![Visão geral do Designer de modo de exibição](media/log-analytics-view-designer/overview.png)

## <a name="add-view-designer-to-your-workspace"></a>Adicionar o Designer de modo de exibição ao espaço de trabalho
Enquanto o Designer de modo de exibição está em visualização, você deve adicioná-lo ao espaço de trabalho selecionando **Recursos de Visualização** na seção **Configurações** do portal do OMS.

![Habilitar visualização](media/log-analytics-view-designer/preview.png)

## <a name="creating-and-editing-views"></a>Criar e editar modos de exibição
### <a name="create-a-new-view"></a>Criar um novo modo de exibição
Abra uma novo modo de exibição em **Designer de modo de exibição** clicando no bloco Designer de modo de exibição no painel principal do OMS.

![Bloco Designer de modo de exibição](media/log-analytics-view-designer/view-designer-tile.png)

### <a name="edit-an-existing-view"></a>Editar um modo de exibição existente
Para editar um modo de exibição existente no Designer de modo de exibição, abra o modo de exibição clicando em seu bloco, no painel principal do OMS.  Em seguida, clique no botão **Editar** para abrir o modo de exibição no Designer de modo de exibição.

![Editar um modo de exibição](media/log-analytics-view-designer/menu-edit.png)

### <a name="clone-an-existing-view"></a>Clonar um modo de exibição existente
Quando você clona um modo de exibição, ele cria uma novo modo de exibição e o abre no Designer de modo de exibição.  O novo modo de exibição terá o mesmo nome do original com "Copiar" acrescentado ao final.  Para clonar um modo de exibição existente, abra o modo de exibição existente clicando em seu bloco, no painel principal do OMS.  Em seguida, clique no botão **Clonar** para abrir o modo de exibição no Designer de modo de exibição.

![Clonar um modo de exibição](media/log-analytics-view-designer/edit-menu-clone.png)

### <a name="delete-an-existing-view"></a>Excluir um modo de exibição existente
Para excluir um modo de exibição existente, abra o modo de exibição clicando em seu bloco, no painel principal do OMS.  Em seguida, clique no botão **Editar** para abrir o modo de exibição no Designer de modos de exibição e clique em **Excluir Modo de Exibição**.

![Excluir um modo de exibição](media/log-analytics-view-designer/edit-menu-delete.png)

### <a name="export-an-existing-view"></a>Exportar um modo de exibição existente
Você pode exportar um modo de exibição para um arquivo JSON que pode importar para outro espaço de trabalho ou usar em um [o modelo do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).  Para exportar um modo de exibição existente, abra o modo de exibição clicando em seu bloco, no painel principal do OMS.  Em seguida, clique no botão **Exportar** para criar um arquivo na pasta de download do navegador.  O nome do arquivo será o nome do modo de exibição com a extensão *omsview*.

![Exportar um modo de exibição](media/log-analytics-view-designer/edit-menu-export.png)

### <a name="import-an-existing-view"></a>Importar um modo de exibição existente
Você pode importar um arquivo *omsview* exportado de outro grupo de gerenciamento.  Para importar um modo de exibição existente, primeiro crie uma nova exibição.  Em seguida, clique no botão **Importação** e selecione o arquivo *omsview*.  A configuração no arquivo será copiada para o modo de exibição existente.

![Exportar um modo de exibição](media/log-analytics-view-designer/edit-menu-import.png)

## <a name="working-with-view-designer"></a>Trabalhando com o Designer de modo de exibição
O Designer de modo de exibição tem três painéis.  O painel **Design** representa o modo de exibição personalizado.  Quando você adiciona blocos e partes do painel **Controle** ao painel **Design** eles são adicionados ao modo de exibição.  O painel **Propriedades** exibirá as propriedades do bloco ou a parte selecionada.

![Criador de Modos de Exibição](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Configurar bloco de modo de exibição
Um modo de exibição personalizado pode ter apenas um único bloco.  Selecione a guia **Bloco** no painel **Controle** para exibir o bloco atual ou selecionar um alternativo.  O painel **Propriedades** exibirá as propriedades do bloco atual.  Configure as propriedades de bloco de acordo com as informações detalhadas na [Referência de Bloco](log-analytics-view-designer-tiles.md) e clique em **Aplicar** para salvar as alterações.

### <a name="configure-visualization-parts"></a>Configurar partes da visualização
Um modo de exibição pode incluir qualquer número de partes de visualização.  Selecione a guia **Modo de Exibição** e, em seguida, uma parte da visualização para adicionar ao modo de exibição.  O painel **Propriedades** exibirá as propriedades da parte selecionada.  Configure as propriedades do modo de exibição de acordo com as informações detalhadas na [Referência da parte de visualização](log-analytics-view-designer-parts.md) e clique em **Aplicar** para salvar as alterações.

### <a name="delete-a-visualization-part"></a>Excluir uma parte da visualização
Você pode remover uma parte da visualização do modo de exibição clicando no botão **X** no canto superior direito da parte.

### <a name="rearrange-visualization-parts"></a>Reorganizar partes de visualização
Os modos de exibição têm apenas uma linha de partes da visualização.  Reorganize as partes existentes em um modo de exibição clicando e arrastando-as para um novo local.

## <a name="next-steps"></a>Próximas etapas
* Adicione [Blocos](log-analytics-view-designer-tiles.md) ao modo de exibição personalizado.
* Adicione [Partes da visualização](log-analytics-view-designer-parts.md) ao modo de exibição personalizado.
