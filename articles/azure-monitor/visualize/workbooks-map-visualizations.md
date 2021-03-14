---
title: Azure Monitor visualizações de mapa da pasta de trabalho
description: Saiba mais sobre Azure Monitor visualizações de mapa da pasta de trabalho.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: lagayhar
ms.openlocfilehash: dd9bafab982923785898b7f135e5c0408aa19a14
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100605439"
---
# <a name="map-visualization"></a>Visualização de mapa

A visualização do mapa ajuda em problemas de pontos de fixação em regiões específicas e mostra exibições agregadas de alto nível dos dados de monitoramento, fornecendo a capacidade de agregar todos os dados mapeados para cada local/país/região.

A captura de tela abaixo mostra o total de transações e a latência de ponta a ponta para diferentes contas de armazenamento. Aqui, o tamanho é determinado pelo número total de transações e as métricas de cor abaixo do mapa mostram a latência de ponta a ponta. Na primeira observação, o número de transações na região **oeste dos EUA** é pequeno em comparação com a região **leste dos EUA** , mas a latência de ponta a ponta para a região **oeste dos EUA** é maior do que a região **leste dos EUA** . Isso fornece uma visão inicial de que algo é aMiss para o **oeste dos EUA**.

![Captura de tela do mapa de localização do Azure](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>Adicionando um mapa

O mapa poderá ser visualizado se os dados/métricas subjacentes tiverem informações de latitude/longitude, informações de recursos do Azure, informações de local do Azure ou país/região, nome ou código de país/região.

### <a name="using-azure-location"></a>Usando o local do Azure

1. Alterne a pasta de trabalho para o modo de edição selecionando o item **Editar** barra de ferramentas.
2. Selecione **Adicionar** e *Adicionar consulta*.
3. Altere a *fonte de dados* para `Azure Resource Graph` , em seguida, selecione qualquer assinatura que tenha conta de armazenamento.
4. Insira a consulta abaixo para sua análise e a **consulta Selecionar executar**.

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. Defina o *tamanho* como `Large` .
6. Defina a *Visualização* como `Map` .
7. Todas as configurações serão preenchidas automaticamente. Para configurações personalizadas, selecione o botão **configurações de mapa** para abrir o painel configurações.
8. Veja abaixo uma captura de tela da visualização de mapa que mostra as contas de armazenamento para cada região do Azure para a assinatura selecionada.

![Captura de tela do mapa de localização do Azure com a consulta acima](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>Usando o recurso do Azure

1. Alterne a pasta de trabalho para o modo de edição selecionando o item **Editar** barra de ferramentas.
2. Selecione **Adicionar** e *Adicionar métrica*.
3. Use uma assinatura que tenha contas de armazenamento.
4. Altere o *tipo de recurso* para `storage account` e em *recurso* selecione várias contas de armazenamento.
5. Selecione **Adicionar métrica** e adicionar uma métrica de transação.
    1. Namespace: `Account`
    2. Medidas `Transactions`
    3. Aggregation `Sum`
    
    ![Captura de tela da métrica da transação](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. Selecione **Adicionar métrica** e adicionar métrica de LATÊNCIA de E2E de sucesso.
    1. Namespace: `Account`
    1. Medidas `Success E2E Latency`
    1. Aggregation `Average`
    
    ![Captura de tela da métrica de latência de ponta a ponta com êxito](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. Defina o *tamanho* como `Large` .
1. Defina o tamanho de *Visualização* como `Map` .
1. Em **configurações de mapa** , defina as seguintes configurações:
    1. Informações de local usando: `Azure Resource`
    1. Campo de recurso do Azure: `Name`
    1. Tamanho: `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. Agregação para o local: `Sum of values`
    1. Tipo de cores: `Heatmap`
    1. Cor por: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Agregação para cor: `Sum of values`
    1. Paleta de cores: `Green to Red`
    1. Valor mínimo: `0`
    1. Valor da métrica: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Agregar outras métricas por: `Sum of values`
    1. Selecionar a caixa de **formatação personalizada**
    1. Unidade `Milliseconds`
    1. Estilo `Decimal`
    1. Dígitos fracionários máximos: `2`

### <a name="using-countryregion"></a>Usando país/região

1. Alterne a pasta de trabalho para o modo de edição selecionando o item **Editar** barra de ferramentas.
2. Selecione **Adicionar* e *Adicionar consulta*.
3. Altere a *fonte de dados* para `Log` .
4. Selecione o *tipo de recurso* como `Application Insights` e escolha qualquer Application insights recurso que tenha dados de pageViews.
5. Use o editor de consultas para inserir o KQL para sua análise e selecione **Executar consulta**.

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. Defina os valores de tamanho como `Large` .
7. Defina a visualização como `Map` .
8. Todas as configurações serão preenchidas automaticamente. Para configurações personalizadas, selecione **configurações de mapa**.

### <a name="using-latitudelocation"></a>Usando latitude/Location

1. Alterne a pasta de trabalho para o modo de edição selecionando o item **Editar** barra de ferramentas.
2. Selecione **Adicionar* e *Adicionar consulta*.
3. Altere a *fonte de dados* para `JSON` .
1. Insira os dados JSON abaixo no editor de consultas e selecione **Executar consulta**.
1. Defina os valores de *tamanho* como `Large` .
1. Defina a *Visualização* como `Map` .
1. Em **configurações de mapa** em "configurações de métrica", defina *rótulo de métrica* como `displayName` , em seguida, selecione **salvar e fechar**.

A visualização de mapa abaixo mostra os usuários para cada localização de latitude e longitude com o rótulo selecionado para métricas.

![Captura de tela de uma visualização de mapa que mostra os usuários para cada localização de latitude e longitude com o rótulo selecionado para métricas](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>Configurações de mapa

### <a name="layout-settings"></a>Configurações de layout

| Configuração | Explicação |
|:-------------|:-------------|
| `Location info using` | Selecione uma maneira de obter o local dos itens mostrados no mapa. <br> **Latitude/longitude**: Selecione esta opção se houver colunas com informações de latitude e longitude. Cada linha com dados de latitude e longitude será mostrada como item distinto no mapa. <br> **Local do Azure**: Selecione essa opção se houver uma coluna que tenha informações de localização do Azure (eastus, westeurope, centralindia, etc.). Especifique essa coluna e ela buscará a latitude e a longitude correspondentes para cada local do Azure, agrupará as mesmas linhas de local (com base na agregação especificada) para mostrar os locais no mapa. <br> **Recurso do Azure**: Selecione esta opção se houver uma coluna que tenha informações de recursos do Azure (conta de armazenamento, conta cosmosdb, etc.). Especifique essa coluna e ela buscará a latitude e a longitude correspondentes de cada recurso do Azure, as linhas do mesmo local (local do Azure) (com base na agregação especificada) em conjunto para mostrar os locais no mapa. <br> **País/região**: Selecione esta opção se houver uma coluna que tenha informações de país/região/código (US, Estados Unidos, in, Índia, CN, China). Especifique essa coluna e ela buscará a latitude e a longitude correspondentes para cada país/região/código e agrupará as linhas com o mesmo nome de Country-Region código/país-região para mostrar os locais no mapa. O nome do país e o código do país não serão agrupados como uma única entidade no mapa.
| `Latitude/Longitude` | Essas duas opções estarão visíveis se o valor do campo de informações de localização for: latitude/longitude. Selecione a coluna que tem latitude no campo latitude e longitude no campo longitude, respectivamente. |
| `Azure location field` | Essa opção estará visível se o valor do campo de informações de localização for: local do Azure. Selecione a coluna informações sobre o local do Azure. |
| `Azure resource field` | Essa opção estará visível se o valor do campo de informações de localização for: recurso do Azure. Selecione a coluna que as informações de recurso do Azure. |
| `Country/Region field` | Essa opção estará visível se o valor do campo de informações de localização for: país ou região. Selecione a coluna informações de país/região. |
| `Size by` | Esta opção controla o tamanho dos itens mostrados no mapa. O tamanho depende do valor na coluna especificada pelo usuário. Atualmente, o raio do círculo é diretamente proporcional à raiz quadrada do valor da coluna. Se ' None... ' for selecionado, todos os círculos mostrarão o tamanho da região padrão.|
| `Aggregation for location` | Este campo especifica como agregar o **tamanho por** colunas que têm o mesmo local do Azure/recurso do Azure/país-região. |
| `Minimum region size` | Este campo especifica qual é o raio mínimo do item mostrado no mapa. Isso é usado quando há uma diferença significativa entre o tamanho pelos valores da coluna, portanto, os itens menores não são visíveis no mapa. |
| `Maximum region size` | Este campo especifica qual é o raio máximo do item mostrado no mapa. Isso é usado quando os valores de tamanho por coluna são extremamente grandes e estão cobrindo uma área enorme do mapa.|
| `Default region size` | Este campo especifica qual é o raio padrão do item mostrado no mapa. O raio padrão é usado quando o tamanho por coluna é ' nenhum... ' ou o valor é 0.|
| `Minimum value` | O valor mínimo usado para computar o tamanho da região. Se não for especificado, o valor mínimo será o menor valor após a agregação. |
| `Maximum value` | O valor máximo usado para computar o tamanho da região. Se não for especificado, o valor máximo será o maior valor após a agregação.|
| `Opacity of items on Map` | Este campo especifica o quão transparente são os itens mostrados no mapa. Opacidade de 1 significa, sem transparência, em que a opacidade de 0 significa, os itens não estarão visíveis no mapa. Se houver muitos itens no mapa, a opacidade poderá ser definida como valor baixo para que todos os itens sobrepostos fiquem visíveis.|

### <a name="color-settings"></a>Configurações de cor

| Tipo de cores | Explicação |
|:------------- |:-------------|
| `None` | Todos os nós têm a mesma cor. |
| `Thresholds` | Nesse tipo, as cores de célula são definidas por regras de limite (por exemplo, _cpu > 90% => vermelho, 60% > cpu > 90% => amarelo, cpu < 60% => verde_). <ul><li> **Cor por**: o valor desta coluna será usado pela lógica de calor/limites.</li> <li>**Agregação para cor**: Este campo especifica como agregar a **cor por** colunas que têm o mesmo local do Azure/recurso do Azure/país-região. </li> <ul> |
| `Heatmap` | Nesse tipo, as células são coloridas com base na paleta de cores e na cor por campo. Isso também terá a mesma **cor** e **agregação para opções de cores** como limites. |

### <a name="metric-settings"></a>Configurações de métrica
| Configuração | Explicação |
|:------------- |:-------------|
| `Metric Label` | Essa opção estará visível se o valor do campo de informações de localização for: latitude/longitude. Usando esse recurso, o usuário pode escolher o rótulo a ser mostrado para as métricas mostradas abaixo do mapa. |
| `Metric Value` | Este campo especifica o valor da métrica a ser mostrado abaixo do mapa. |
| `Create 'Others' group after` | Este campo especifica o limite antes que um grupo "outros" seja criado. |
| `Aggregate 'Others' metrics by` | Esse campo especifica a agregação usada para o grupo "outros" se for mostrado. |
| `Custom formatting` | Use este campo para definir as opções de unidades, estilo e formatação para valores numéricos. Isso é o mesmo que a [formatação personalizada da grade](../visualize/workbooks-grid-visualizations.md#custom-formatting).|

## <a name="next-steps"></a>Próximas etapas

- Saiba como criar [visualizações do Honey Comb em pastas de trabalho](../visualize/workbooks-honey-comb.md).