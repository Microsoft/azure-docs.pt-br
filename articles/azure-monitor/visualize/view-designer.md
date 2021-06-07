---
title: Criar exibições para analisar dados de log em Azure Monitor | Microsoft Docs
description: Usando o designer de exibição no Azure Monitor, você pode criar exibições personalizadas que são exibidas na portal do Azure e conter uma variedade de visualizações nos dados no espaço de trabalho Log Analytics. Este artigo contém uma visão geral do Designer de Exibição e apresenta procedimentos para criar e editar exibições personalizadas.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/04/2020
ms.openlocfilehash: cb0274260022c55ae657b5b28b2c9ad1903d0296
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043262"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Criar exibições personalizadas usando o designer de exibição no Azure Monitor
Usando o designer de exibição no Azure Monitor, você pode criar uma variedade de exibições personalizadas na portal do Azure que podem ajudá-lo a Visualizar dados em seu espaço de trabalho do Log Analytics. Este artigo apresenta uma visão geral do Designer de Exibição e procedimentos para criar e editar exibições personalizadas.

> [!IMPORTANT]
> As exibições no Azure Monitor foram transferidas para [pastas de trabalho](workbooks-overview.md) que fornecem funcionalidade adicional. Confira [Azure monitor guia de transição do designer de exibição para pastas de trabalho](view-designer-conversion-overview.md) para obter detalhes sobre como converter suas exibições existentes em pastas de trabalho.
 


Para obter mais informações sobre o Designer de Exibição, consulte:

* [Referência de bloco](view-designer-tiles.md): fornece um guia de referência para as configurações de cada um dos blocos disponíveis em suas exibições personalizadas.
* [Referência da parte de visualização](view-designer-parts.md): fornece um guia de referência para as configurações das partes de visualização que estão disponíveis em nas exibições personalizadas.


## <a name="concepts"></a>Conceitos
As exibições são exibidas na página de **visão geral** Azure Monitor no portal do Azure. Abra essa página do **Azure Monitor** menu clicando em **Mais** na seção **Insights**. Os blocos em cada exibição personalizada são exibidos em ordem alfabética e os blocos para as soluções de monitoramento são instalados no mesmo espaço de trabalho.

![Página de visão geral](media/view-designer/overview-page.png)

As exibições criadas com o Designer de Exibição contêm os elementos descritos na tabela a seguir:

| Parte | Descrição |
|:--- |:--- |
| Blocos | São exibidos na sua página de **visão geral** do Azure monitor. Cada bloco exibe um resumo visual da exibição personalizada que representa. Cada tipo de bloco fornece uma visualização diferente de seus registros. Você seleciona um bloco para exibir uma exibição personalizada. |
| Exibição personalizada | Exibida quando você seleciona um bloco. Cada exibição contém uma ou mais partes de visualização. |
| Partes de visualização | Apresente uma visualização dos dados no espaço de trabalho Log Analytics com base em uma ou mais [consultas de log](../logs/log-query-overview.md). A maioria das partes inclui um cabeçalho, que fornece uma visualização de alto nível e uma lista que exibe os melhores resultados. Cada tipo de parte fornece uma visualização diferente dos registros no espaço de trabalho do Log Analytics. Você seleciona elementos na parte para executar uma consulta de log que fornece registros detalhados. |

## <a name="required-permissions"></a>Permissões necessárias
Você precisa de pelo menos [permissões de nível de colaborador](../logs/manage-access.md#manage-access-using-azure-permissions) no espaço de trabalho log Analytics para criar ou modificar modos de exibição. Se você não tiver essa permissão, a opção designer de exibição não será exibida no menu.


## <a name="work-with-an-existing-view"></a>Trabalhar com um modo de exibição existente
As exibições que foram criadas com o Designer de Exibição exibem as opções a seguir:

![Menu de visão geral](media/view-designer/overview-menu.png)

As opções estão descritas na tabela a seguir:

| Opção | Descrição |
|:--|:--|
| Atualizar   | Atualiza a exibição com os dados mais recentes. | 
| Logs      | Abre o [log Analytics](../logs/log-query-overview.md) para analisar dados com consultas de log. |
| Editar       | Abre a exibição no Designer de Exibição para editar o conteúdo e a configuração.  |
| Clone      | Cria uma nova exibição e abre no Designer de Exibição. O nome da nova exibição é o mesmo que o nome original, mas com *Copiar* acrescentado a ele. |
| Intervalo de datas | Define um filtro de data e tempo para os dados incluídos na exibição. Este intervalo de datas é aplicado antes de quaisquer intervalos de datas definidos nas consultas na exibição.  |
| +          | Define um filtro personalizado que é definido para a exibição. |


## <a name="create-a-new-view"></a>Criar um novo modo de exibição
É possível criar uma nova exibição no Designer de Exibição, selecionando **Designer de Exibição** no menu do seu espaço de trabalho do Log Analytics.

![Bloco Designer de modo de exibição](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Trabalhar com o Designer de Exibição
O Designer de Exibição é utilizado para criar novas exibições ou editar as existentes. 

O Designer de Exibição tem três painéis: 
* **Design**: contém a exibição personalizada que você está criando ou editando. 
* **Controles**: contém os blocos e as partes que você adiciona ao painel **Design**. 
* **Propriedades**: exibe as propriedades dos blocos ou partes selecionadas.

![Designer de Exibição](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Configurar o bloco de exibição
Um modo de exibição personalizado pode ter apenas um único bloco. Para exibir o bloco atual ou selecionar um alternativo, selecione a guia **Bloco** no painel **Controle**. O painel **Propriedades** exibe as propriedades do bloco atual. 

Você pode configurar as propriedades do bloco de acordo com as informações na [Referência de bloco](view-designer-tiles.md) e, em seguida, clique em **Aplicar** para salvar as alterações.

### <a name="configure-the-visualization-parts"></a>Configurar as partes de visualização
Um modo de exibição pode incluir qualquer número de partes de visualização. Para adicionar partes a uma exibição, selecione a **Exibição** e, em seguida, selecione uma parte de visualização. O painel **Propriedades** exibe as propriedades da parte selecionada. 

Você pode configurar as propriedades da exibição, de acordo com as informações na [Referência da parte de visualização](view-designer-parts.md) e, em seguida, clique em **Aplicar** para salvar as alterações.

As exibições têm apenas uma linha de partes de visualização. É possível reorganizar as partes existentes, arrastando-as para um novo local.

Além disso, é possível remover uma parte de visualização da exibição, selecionando **X** no canto superior direito da parte.


### <a name="menu-options"></a>Opções de menu
As opções para trabalhar com exibições no modo de edição são descritas na tabela a seguir.

![Editar menu](media/view-designer/edit-menu.png)

| Opção | Descrição |
|:--|:--|
| Salvar        | Salva as alterações e fecha a exibição. |
| Cancelar      | Descarta as alterações e fecha a exibição. |
| Excluir Exibição | Exclui a exibição. |
| Exportação      | Exporta a exibição para um [modelo do Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) que você poderá importar para outro workspace. O nome do arquivo é o nome da exibição e tem uma extensão *omsview*. |
| Importar      | Importa o arquivo *omsview* que foi exportado de outro workspace. Essa ação substitui a configuração da exibição existente. |
| Clone       | Cria uma nova exibição e abre no Designer de Exibição. O nome da nova exibição é o mesmo que o nome original, mas com *Copiar* acrescentado a ele. |

## <a name="next-steps"></a>Próximas etapas
* Adicione [Blocos](view-designer-tiles.md) ao modo de exibição personalizado.
* Adicione as [Partes de visualização](view-designer-parts.md) à sua exibição personalizada.