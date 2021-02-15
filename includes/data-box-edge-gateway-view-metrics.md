---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 9ac7966538102273b91d6b7f15b90e18ceedd421
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83779926"
---
Exiba também as métricas para monitorar o desempenho do dispositivo e, em alguns casos, para solução de problemas do dispositivo.

Execute as etapas a seguir no portal do Azure para criar um gráfico para as métricas do dispositivo selecionadas.

1. Para ver seu recurso no portal do Azure, acesse **Monitoramento > Métricas** e selecione **Adicionar métrica**.

    ![Adicionar métrica](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. O recurso é populado automaticamente.  

    ![Recurso atual](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Para especificar outro recurso, selecione o recurso. Na folha **Selecionar um recurso**, selecione a assinatura, o grupo de recursos, o tipo de recurso e o recurso específico para o qual deseja mostrar as métricas e selecione **Aplicar**.

    ![Escolher outro recurso](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. Na lista suspensa, selecione uma métrica para monitorar seu dispositivo. Para obter uma lista completa dessas métricas, consulte [Métricas em seu dispositivo](#metrics-on-your-device).

4. Quando uma métrica é selecionada na lista suspensa, a agregação também pode ser definida. Agregação refere-se ao valor real agregado em um período de tempo especificado. Os valores agregados podem ser a média, o valor mínimo ou o valor máximo. Selecionar Méd., Máx. ou Mín. para a agregação

    ![Exibir gráfico](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Se a métrica selecionada tiver várias instâncias, a opção de separação estará disponível. Selecione **Aplicar separação** e, em seguida, selecione o valor pelo qual deseja ver o detalhamento.

    ![Aplicar a separação](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Caso deseje ver agora o detalhamento somente de algumas instâncias, filtre os dados. Por exemplo, nesse caso, caso deseje ver a taxa de transferência de rede somente para os dois adaptadores de rede conectados no dispositivo, filtre esses adaptadores. Selecione **Adicionar filtro** e especifique o nome do adaptador de rede para filtragem.

    ![Adicionar filtro](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Você também pode fixar o gráfico no painel para facilitar o acesso.

    ![Fixar no painel](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Para exportar os dados do gráfico para uma planilha do Excel ou obter um link para o gráfico que pode ser compartilhado, selecione a opção de compartilhamento na barra de comandos.

    ![Exportar dados](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)
