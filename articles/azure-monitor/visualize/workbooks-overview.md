---
title: Visão geral das Pastas de Trabalho do Azure Monitor
description: Saiba como as pastas de trabalho fornecem uma tela flexível para análise de dados e a criação de relatórios visuais avançados dentro do portal do Azure.
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: e401eb4a5608db6e5767298f53046099a42679b4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100604989"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor pastas de trabalho

As pastas de trabalho fornecem uma tela flexível para análise de dados e a criação de relatórios visuais avançados no portal do Azure. Com elas, você pode explorar várias fontes de dados em todo o Azure e combiná-las em experiências interativas unificadas. 

Aqui está uma explicação em vídeo sobre como criar pastas de trabalho.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>Fontes de dados

As pastas de trabalho podem consultar dados de várias fontes no Azure. Os autores de pastas de trabalho podem transformar esses dados para fornecer insights sobre a disponibilidade, o desempenho, o uso e a integridade geral dos componentes subjacentes. Por exemplo, analisar os logs de desempenho de máquinas virtuais para identificar altas instâncias de CPU ou memória insuficiente e exibir os resultados como uma grade em um relatório interativo.
  
Mas o poder real das pastas de trabalho é a capacidade de combinar dados de diferentes fontes em um só relatório. Isso permite a criação de modos de exibição de recursos compostos ou junções entre recursos, permitindo dados e informações mais ricos que seriam impossíveis.

Atualmente, as pastas de trabalho são compatíveis com as seguintes fontes de dados:

* [Logs](../visualize/workbooks-data-sources.md#logs)
* [Métricas](../visualize/workbooks-data-sources.md#metrics)
* [Gráfico de Recursos do Azure](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [Alertas (versão prévia)](../visualize/workbooks-data-sources.md#alerts-preview)
* [Integridade da carga de trabalho](../visualize/workbooks-data-sources.md#workload-health)
* [Azure Resource Health](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Visualizações

As pastas de trabalho fornecem um conjunto avançado de recursos para visualizar seus dados. Para obter exemplos detalhados de cada tipo de visualização, você pode consultar os links de exemplo abaixo:

* [Text](../visualize/workbooks-text-visualizations.md)
* [Gráficos](../visualize/workbooks-chart-visualizations.md)
* [Grades](../visualize/workbooks-grid-visualizations.md)
* [Blocos](../visualize/workbooks-tile-visualizations.md)
* [Árvores](../visualize/workbooks-tree-visualizations.md)
* [Gráficos](../visualize/workbooks-graph-visualizations.md)
* [Barra de composição](../visualize/workbooks-composite-bar.md)

![Exemplos de visualizações de pastas de trabalho](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Introdução

Para explorar a experiência de pastas de trabalho, primeiro navegue até o serviço de Azure Monitor. Isso pode ser feito digitando **Monitor** na caixa de pesquisa na portal do Azure.

Em seguida, selecione **pastas de trabalho**.

![Captura de tela do botão pastas de trabalho realçado em uma caixa vermelha](./media/workbooks-overview/workbooks.png)

### <a name="gallery"></a>Galeria

Isso leva você para a Galeria de pastas de trabalho:

![Captura de tela da exibição da Galeria Azure Monitor pastas de trabalho](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Pastas de trabalho versus modelos de pasta de trabalho

Você pode ver uma _pasta de trabalho_ em verde e um número de _modelos de pasta de trabalho_ em roxo. Os modelos servem como relatórios estruturados que são projetados para reutilização flexível por vários usuários e equipes. A abertura de um modelo cria uma pasta de trabalho transitória populada com o conteúdo do modelo. 

Você pode ajustar os parâmetros da pasta de trabalho baseada em modelo e executar a análise sem medo de quebrar a experiência de relatório futura para os colegas. Se você abrir um modelo, faça alguns ajustes e, em seguida, selecione o ícone salvar, você salvará o modelo como uma pasta de trabalho que, em seguida, será exibida em verde deixando o modelo original inalterado. 

Nos bastidores, os modelos também diferem das pastas de trabalho salvas. Salvar uma pasta de trabalho cria um recurso de Azure Resource Manager associado, enquanto a pasta de trabalho transitória criada ao simplesmente abrir um modelo não tem nenhum recurso exclusivo associado a ele. Para saber mais sobre como o controle de acesso é gerenciado em pastas de trabalho, consulte o [artigo controle de acesso às pastas de trabalho](../visualize/workbooks-access-control.md).

### <a name="exploring-a-workbook-template"></a>Explorando um modelo de pasta de trabalho

Selecione **análise de falha de aplicativo** para ver um dos modelos de pasta de trabalho do aplicativo padrão.

![Captura de tela do modelo de análise de falha de aplicativo](./media/workbooks-overview/failure-analysis.png)

Como mencionado anteriormente, abrir o modelo cria uma pasta de trabalho temporária para a qual você pode interagir. Por padrão, a pasta de trabalho é aberta no modo de leitura, que exibe somente as informações para a experiência de análise pretendida que foi criada pelo autor do modelo original.

No caso dessa pasta de trabalho específica, a experiência é interativa. Você pode ajustar a assinatura, os aplicativos direcionados e o intervalo de tempo dos dados que deseja exibir. Depois de fazer essas seleções, a grade de solicitações HTTP também é interativa por meio da seleção de uma linha individual que alterará quais dados são renderizados nos dois gráficos na parte inferior do relatório.

### <a name="editing-mode"></a>Modo de edição

Para entender como esse modelo de pasta de trabalho é agrupado, você precisa alternar para o modo de edição selecionando **Editar**.

![Captura de tela do botão Editar em pastas de trabalho.](./media/workbooks-overview/edit.png)

Depois de alternar para o modo de edição, você observará que várias caixas de **edição** aparecem à direita correspondentes a cada aspecto individual de sua pasta de trabalho.

![Captura de tela do botão de edição](./media/workbooks-overview/edit-mode.png)

Se selecionarmos o botão Editar imediatamente sob a grade de dados de solicitação, podemos ver que essa parte da nossa pasta de trabalho consiste em uma consulta Kusto em relação a dados de um recurso Application Insights.

![Captura de tela da consulta Kusto subjacente](./media/workbooks-overview/kusto.png)

Clicar nos outros botões de **edição** à direita revelará um número de componentes principais que compõem pastas de trabalho como caixas de [texto](../visualize/workbooks-text-visualizations.md)baseadas em redução, elementos de interface do usuário de [seleção de parâmetros](../visualize/workbooks-parameters.md) e outros [tipos de gráfico/visualização](#visualizations). 

Explorar os modelos pré-criados no modo de edição e, em seguida, modificá-los de acordo com suas necessidades e salvar sua própria pasta de trabalho personalizada é uma maneira excelente de começar a aprender o que é possível com Azure Monitor pastas de trabalho.

## <a name="pinning-visualizations"></a>Fixação de visualizações

As etapas de texto, consulta e métricas em uma pasta de trabalho podem ser fixadas usando o botão de PIN nesses itens enquanto a pasta de trabalho está no modo PIN, ou se o autor da pasta de trabalho tiver habilitado as configurações para esse elemento para tornar o ícone de fixação visível. 

Para acessar o modo PIN, clique em **Editar** para entrar no modo de edição e selecione o ícone de pino azul na barra superior. Um ícone de PIN individual será exibido acima de cada caixa de *edição* da parte da pasta de trabalho correspondente no lado direito da tela.

![Experiência de fixação](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> O estado da pasta de trabalho é salvo no momento do PIN e as pastas de trabalho fixadas em um painel não serão atualizadas se a pasta de trabalho subjacente for modificada. Para atualizar uma parte da pasta de trabalho fixa, será necessário excluir e fixar novamente essa parte.

## <a name="dashboard-time-ranges"></a>Intervalos de tempo do painel

As partes de consulta da pasta de trabalho fixa respeitarão o intervalo de tempo do painel se o item fixado estiver configurado para usar um parâmetro de *intervalo de tempo* . O valor do intervalo de tempo do painel será usado como o valor do parâmetro de intervalo de tempo e qualquer alteração do intervalo de tempo do painel fará com que o item fixado seja atualizado. Se uma parte fixa estiver usando o intervalo de tempo do painel, você verá o subtítulo da atualização da parte fixada para mostrar o intervalo de tempo do painel sempre que o intervalo de tempo for alterado. 

Além disso, as partes da pasta de trabalho fixadas usando um parâmetro de intervalo de tempo serão atualizadas automaticamente em uma taxa determinada pelo intervalo de tempo do painel. A última vez em que a consulta foi executada aparecerá no subtítulo da parte fixada.

Se uma etapa fixa tiver um intervalo de tempo definido explicitamente (não usa um parâmetro de intervalo de tempo), esse intervalo de tempo sempre será usado para o painel, independentemente das configurações do painel. O subtítulo da parte fixada não mostrará o intervalo de tempo do painel e a consulta não será atualizada automaticamente no painel. O subtítulo mostrará a última vez em que a consulta foi executada.

> [!NOTE]
> Atualmente, não há suporte para consultas que usam a fonte de dados de *mesclagem* ao fixar os painéis.

## <a name="sharing-workbook-templates"></a>Compartilhando modelos de pasta de trabalho

Depois de começar a criar seus próprios modelos de pasta de trabalho, você talvez queira compartilhá-los com a comunidade mais ampla. Para saber mais e explorar outros modelos que não fazem parte do modo de exibição da Galeria de Azure Monitor padrão, visite nosso [repositório GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md). Para procurar pastas de trabalho existentes, visite a [biblioteca de pastas de trabalho](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) no github.

## <a name="next-step"></a>Próxima etapa

* [Comece a aprender mais](#visualizations) sobre pastas de trabalho muitas opções de visualizações ricas.
* [Controle](../visualize/workbooks-access-control.md) e compartilhe o acesso aos recursos da pasta de trabalho.