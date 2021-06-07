---
title: 'Início Rápido: Sua primeira consulta do portal'
description: Neste início rápido, você segue etapas necessárias para executar sua primeira consulta no portal do Azure usando o Azure Resource Graph Explorer.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 07e336121c6338b27c018acb5b2332653663e1b4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533078"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Início Rápido: Executar a primeira consulta do Resource Graph usando o Azure Resource Graph Explorer

Toda a potência do Azure Resource Graph está disponível diretamente no portal do Azure por meio do Azure Resource Graph Explorer. O Resource Graph Explorer fornece informações navegáveis sobre os tipos de recursos e propriedades do Azure Resource Manager que você pode consultar. O Resource Graph Explorer também fornece uma interface simples para trabalhar com várias consultas, avaliar os resultados e até mesmo converter os resultados de algumas consultas em um gráfico que pode ser fixado a um painel do Azure.

No final deste início rápido, você terá usado o portal do Azure e o Resource Graph Explorer para executar sua primeira consulta do Resource Graph e terá fixado os resultados em um painel.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta ao Resource Graph

Abra o [portal do Azure](https://portal.azure.com) para localizar e usar o Resource Graph Explorer, seguindo estas etapas para executar sua primeira consulta do Resource Graph:

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise pelo **Resource Graph Explorer** e selecione-o.

1. Na parte **Consulta 1** da janela, insira a consulta `Resources | project name, type | limit 5` e selecione **Executar Consulta**.

   > [!NOTE]
   > Por essa consulta de exemplo não fornecer um modificador de classificação, como `order by`, executar essa consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação.

1. Examine a resposta da consulta na guia **Resultados**. Selecione a guia **Mensagens** para ver detalhes sobre a consulta, incluindo a contagem de resultados e a duração da consulta. Se houver erros, eles serão exibidos nessa guia.

1. Atualize a consulta para `order by` a propriedade **Name**: `Resources | project name, type | limit 5 | order by name asc`. Em seguida, selecione **Executar Consulta**.

   > [!NOTE]
   > Assim como ocorre com a primeira consulta, executar esta consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação. A ordem dos comandos de consulta é importante. Neste exemplo, o `order by` vem após o `limit`. Essa ordem de comando limita primeiro os resultados da consulta e os ordena.

1. Atualize a consulta para, primeiro, `order by` a propriedade **Name** e depois `limit` conforme os primeiros cinco resultados: `Resources | project name, type | order by name asc | limit 5`. Em seguida, selecione **Executar Consulta**.

Quando a consulta final for executada várias vezes, supondo que nada em seu ambiente esteja sendo alterado, os resultados retornados serão consistentes e ordenados segundo a propriedade **Name**, mas ainda limitados aos cinco primeiros resultados.

### <a name="schema-browser"></a>Navegador de esquema

O navegador de esquema está localizado no painel esquerdo do Resource Graph Explorer. Esta lista de recursos mostra todos os _tipos de recursos_ dos recursos do Azure com suporte do Azure Resource Graph e que existem em um locatário ao qual você tem acesso. Expandir um tipo de recurso ou suas subpropriedades mostra propriedades filho que podem ser usadas para criar uma consulta do Resource Graph.

Selecionar o tipo de recurso coloca `where type =="<resource type>"` na caixa de consulta. Selecionar uma das propriedades filho adicionada `where <propertyName> == "INSERT_VALUE_HERE"` à caixa de consulta.
O navegador de esquema é uma ótima maneira de descobrir propriedades para uso em consultas. Certifique-se de substituir _INSERT\_VALUE\_HERE_ pelo seu próprio valor e de ajustar a consulta com as condições, os operadores e as funções para chegar aos resultados pretendidos.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Criar um gráfico baseado na consulta do Resource Graph

Após executar a consulta anterior, se selecionar a guia **Gráficos**, você receberá uma mensagem informando que "o conjunto de resultados não é compatível com uma visualização em gráfico de pizza". Consultas que listam os resultados não podem ser transformadas em um gráfico, mas consultas que fornecem contagens de recursos podem. Usando a [Consulta de Exemplo – Contar máquinas virtuais por tipo de sistema operacional](./samples/starter.md#count-os), vamos criar uma visualização baseada na consulta do Resource Graph.

1. Na parte **Consulta 1** da janela, insira a seguinte consulta e selecione **Executar Consulta**.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Selecione a guia **Resultados** e observe que a resposta a essa consulta fornece contagens.

1. Selecione a guia **Gráficos**. Agora, a consulta resulta em visualizações. Altere o tipo de _Selecionar Tipo de Gráfico..._ para _Gráfico de barras_ ou _Gráfico de rosca_ para experimentar as opções de visualização disponíveis.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>Fixar a visualização da consulta a um painel

Quando você tem resultados de uma consulta que podem ser visualizados, essa visualização de dados pode, então, ser fixada a um de seus painéis. Após executar a consulta anterior, siga estas etapas:

1. Selecione **Salvar** e forneça o nome "VMs por tipo de SO". Em seguida, selecione **Salvar** na parte inferior do painel direito.

1. Selecione **Executar Consulta** para executar a consulta novamente agora que ela foi salva.

1. Na guia **Gráficos**, selecione uma visualização de dados. Em seguida, selecione **Fixar no painel**.

1. Selecione a notificação do portal que aparece ou selecione **Painel** no painel esquerdo.

Agora, a consulta está disponível em seu painel, com o título do bloco correspondendo ao nome da consulta. Se a consulta não tiver sido salva quando foi fixada, ela terá o nome "Consulta 1".

A consulta e a visualização de dados resultante são executadas e atualizadas sempre que o painel é carregado, fornecendo insights dinâmicos e em tempo real sobre seu ambiente do Azure diretamente em seu fluxo de trabalho.

> [!NOTE]
> Consultas que resultam em uma lista também podem ser fixadas no painel. Esse recurso não está limitado a visualizações de dados das consultas.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Importar painéis do Resource Graph Explorer de exemplo

Para fornecer exemplos de consultas do Resource Graph e de como o Resource Graph Explorer pode ser usado para aprimorar seu fluxo de trabalho do portal do Azure, experimente esses painéis de exemplo.

- [Resource Graph Explorer – Painel de Amostra 1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  :::image type="content" source="./media/arge-sample1-small.png" alt-text="Exemplo de imagem do Painel de Amostra 1" lightbox="./media/arge-sample1-large.png":::

- [Resource Graph Explorer – Painel de Amostra 2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  :::image type="content" source="./media/arge-sample2-small.png" alt-text="Exemplo de imagem do Painel de Amostra 2" lightbox="./media/arge-sample2-large.png":::

> [!NOTE]
> As contagens e os gráficos nas capturas de tela do painel de exemplo acima variam de acordo com o ambiente do Azure.

1. Selecione e baixe o painel de amostra que você deseja avaliar.

1. No portal do Azure, selecione **Painel** no painel esquerdo.

1. Selecione **Carregar**, localize e selecione o arquivo do painel de amostra baixado. Em seguida, selecione **Abrir**.

O painel importado será exibido automaticamente. Como agora ele existe em seu portal do Azure, você pode explorar e fazer alterações conforme necessário ou criar novos painéis baseados no exemplo para compartilhar com suas equipes. Para saber mais sobre como trabalhar com painéis, confira [Criar compartilhar painéis no portal do Azure](../../azure-portal/azure-portal-dashboards.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser remover os painéis de exemplo do Resource Graph de seu ambiente do portal do Azure, você poderá fazer isso usando as etapas a seguir:

1. Selecione **Painel** no painel esquerdo.

1. Na lista suspensa do painel, selecione o painel de amostra do Resource Graph que você deseja excluir.

1. Selecione **Excluir** no menu do painel na parte superior e selecione **OK** para confirmar.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou o Gerenciador do Azure Resource Graph para executar sua primeira consulta e examinou os exemplos de painel fornecidos pelo Resource Graph. Para saber mais sobre a linguagem do Resource Graph, prossiga para a página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obter mais informações sobre a linguagem de consulta](./concepts/query-language.md)
