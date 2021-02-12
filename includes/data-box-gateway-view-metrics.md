---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/16/2020
ms.author: alkohli
ms.openlocfilehash: 27503d1d60d961bac6dd41ebfe4fb083948cb251
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581421"
---
Exiba também as métricas para monitorar o desempenho do dispositivo e, em alguns casos, para solução de problemas do dispositivo.

Execute as etapas a seguir no portal do Azure para criar um gráfico para as métricas do dispositivo selecionadas.

1. Para ver seu recurso no portal do Azure, acesse **Monitoramento > Métricas** e selecione **Adicionar métrica**.

    ![Adicionar métrica](media/data-box-gateway-view-metrics/view-metrics-add-metric.png)

2. O recurso é populado automaticamente.  

    ![Recurso atual](media/data-box-gateway-view-metrics/view-metrics-current-resource.png)

    Para especificar outro recurso, selecione o recurso. Na folha **Selecionar um recurso**, selecione a assinatura, o grupo de recursos, o tipo de recurso e o recurso específico para o qual deseja mostrar as métricas e selecione **Aplicar**.

    ![Escolher outro recurso](media/data-box-gateway-view-metrics/view-metrics-choose-another-resource.png)

3. Na lista suspensa, selecione uma métrica para monitorar seu dispositivo. Para obter uma lista completa dessas métricas, consulte [Métricas em seu dispositivo](#metrics-on-your-device).

4. Quando uma métrica é selecionada na lista suspensa, a agregação também pode ser definida. Agregação refere-se ao valor real agregado em um período de tempo especificado. Os valores agregados podem ser o valor médio, mínimo ou máximo. Selecionar Méd., Máx. ou Mín. para a agregação

    ![Exibir gráfico](media/data-box-gateway-view-metrics/view-metrics-view-chart.png)

5. Se a métrica selecionada tiver várias instâncias, a opção de separação estará disponível. Selecione **aplicar divisão** e, em seguida, selecione o valor pelo qual você deseja ver a divisão.

    ![Aplicar a separação](media/data-box-gateway-view-metrics/view-metrics-apply-splitting.png)

6. Caso deseje ver agora o detalhamento somente de algumas instâncias, filtre os dados. Por exemplo, nesse caso, caso deseje ver a taxa de transferência de rede somente para os dois adaptadores de rede conectados no dispositivo, filtre esses adaptadores. Selecione **Adicionar filtro** e especifique o nome do adaptador de rede para filtragem.

    ![Adicionar filtro](media/data-box-gateway-view-metrics/view-metrics-add-filter.png)

7. Você também pode fixar o gráfico no painel para facilitar o acesso.

    ![Fixar no painel](media/data-box-gateway-view-metrics/view-metrics-pin-to-dashboard.png)

8. Para exportar os dados do gráfico para uma planilha do Excel ou obter um link para o gráfico que pode ser compartilhado, selecione a opção de compartilhamento na barra de comandos.

    ![Exportar dados](media/data-box-gateway-view-metrics/view-metrics-export-data.png)
