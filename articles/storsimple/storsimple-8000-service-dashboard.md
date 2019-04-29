---
title: Usar o resumo do dispositivo StorSimple 8000 series | Microsoft Docs
description: Descreve a folha de resumo do serviço StorSimple e explica como usá-lo para monitorar a integridade da solução StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: c174f6ce0fb3d40af953be205a7bfcca60fbfeec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632902"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Use a folha de resumo do serviço do dispositivo StorSimple 8000 series

## <a name="overview"></a>Visão geral

A folha de resumo do serviço StorSimple Device Manager fornece uma exibição resumida de todos os dispositivos conectados ao serviço StorSimple Device Manager, realçando os dispositivos que precisam de atenção do administrador do sistema. Este tutorial apresenta a folha de resumo do serviço, explica o conteúdo e a função do painel e descreve as tarefas que podem ser realizadas nessa página.

![Resumo do serviço](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Comandos de gerenciamento

Na folha de resumo do serviço StorSimple, é possível ver as opções para gerenciar o serviço StorSimple Device Manager, bem como os dispositivos StorSimple 8000 series registrados nesse serviço. Você vê os comandos de gerenciamento na parte superior da folha e no lado esquerdo.

![Barra de comandos](./media/storsimple-8000-service-dashboard/service-summary2.png)

Use essas opções para executar várias operações como adicionar volumes ou compartilhamentos ou para monitorar os vários trabalhos em execução nos dispositivos StorSimple.


## <a name="essentials"></a>Conceitos básicos

A área de informações gerais captura algumas das propriedades importantes, como o grupo de recursos, a localização e a assinatura em que o StorSimple Device Manager foi criado.

![Conceitos básicos](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Resumo do serviço do StorSimple Device Manager

* O bloco **Alertas** fornece um instantâneo de todos os alertas ativos em todos os dispositivos, agrupados por gravidade do alerta.

    ![Bloco Alertas](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Se você clicar no bloco, a folha **Alertas** será aberta, em que você poderá clicar em um alerta individual para exibir detalhes adicionais sobre ele, incluindo todas as ações recomendadas. Você também pode limpar o alerta se o problema foi resolvido.

    ![Clique no bloco Alertas](./media/storsimple-8000-service-dashboard/service-summary8.png)

* O bloco **Capacidade** mostra o armazenamento primário provisionado e restante em todos os dispositivos com relação ao armazenamento total disponível em todos os dispositivos. **Provisionado** refere-se à quantidade de armazenamento preparada e alocada para uso, enquanto **Restante** refere-se à capacidade restante que pode ser provisionada em todos os dispositivos.

    ![Bloco Capacidade](./media/storsimple-8000-service-dashboard/service-summary6.png)

    A capacidade **Restante em Camadas** é a capacidade disponível que pode ser provisionada, incluindo a nuvem, enquanto a capacidade **Restante Local** é a capacidade restante nos discos anexados aos dispositivos StorSimple 8000 series.


* No gráfico **Uso**, você pode ver as métricas relevantes dos dispositivos. Veja o armazenamento primário usado em todos os dispositivos, bem como o armazenamento em nuvem consumido pelos dispositivos nos últimos sete dias, que é o período padrão. 

    ![Bloco Uso](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Para escolher uma escala de tempo diferente, use a opção **Editar** no canto superior direito do gráfico.

     ![Clique no bloco Uso](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Exportar dados do gráfico](./media/storsimple-8000-service-dashboard/service-summary11.png)

* O bloco **Dispositivos** fornece um resumo do número de dispositivos StorSimple 8000 series no StorSimple Device Manager agrupados por status do dispositivo. 

    ![Bloco Dispositivos](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Clique nesse bloco para abrir a folha da lista **Dispositivos** e clique em um dispositivo individual para analisar o resumo de dispositivo específico do dispositivo. Você também pode executar ações específicas do dispositivo de uma folha de resumo de um determinado dispositivo. Para obter mais informações sobre a folha de resumo do dispositivo, vá para [Folha de resumo do dispositivo](storsimple-8000-device-dashboard.md).

    ![Clique no bloco Dispositivos](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Exibir os logs de atividade

Para exibir as diversas operações executadas no StorSimple Device Manager, clique no link **Logs de atividade** no lado esquerdo da folha de resumo do serviço do StorSimple. Isso o levará para a folha **Logs de atividade**, na qual você poderá ver um resumo das operações recentes executadas.

![Logs de atividade](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [usar o serviço StorSimple Device Manager para administrar dispositivos StorSimple](storsimple-8000-manager-service-administration.md).

