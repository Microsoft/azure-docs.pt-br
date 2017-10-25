---
title: "Folha de resumo de serviço da StorSimple Virtual Array | Microsoft Docs"
description: "Descreve a folha de resumo do serviço do StorSimple Device Manager e explica como usá-lo para monitorar a integridade do StorSimple Virtual Array."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 284e404c44505a98d9e0ed5abe87cd945415af56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Use a folha de resumo do serviço do StorSimple Device Manager conectada ao StorSimple Virtual Array
## <a name="overview"></a>Visão geral
A folha de resumo do serviço do StorSimple Device Manager fornece uma exibição resumida dos StorSimple Virtual Arrays (também conhecidos como dispositivos virtuais StorSimple locais ou dispositivos virtuais) que estão conectados ao serviço, realçando aqueles que precisam da atenção de um administrador do sistema. Este tutorial apresenta a folha de resumo do serviço, explica o conteúdo e a função, além de descrever as tarefas que podem ser realizadas nessa folha.

![Painel de serviço](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Informações gerais e comandos de gerenciamento
Na folha de resumo do StorSimple, você vê as opções para gerenciar o serviço do StorSimple Device Manager, bem como as matrizes virtual registradas nesse serviço. Você vê os comandos de gerenciamento na parte superior da folha e no lado esquerdo.

Use essas opções para executar várias operações como adicionar volumes ou compartilhamentos, ou monitorar os vários trabalhos em execução nas matrizes virtuais.

A área de informações gerais captura algumas das propriedades importantes, como o grupo de recursos, a localização e a assinatura em que o StorSimple Device Manager foi criado.

## <a name="storsimple-device-manager-service-summary"></a>Resumo do serviço do StorSimple Device Manager
* O bloco **Alertas** fornece um instantâneo de todos os alertas ativos em todos os dispositivos virtuais, agrupados por severidade do alerta. Se você clicar no bloco, a folha **Alertas** será aberta, em que você poderá clicar em um alerta individual para exibir detalhes adicionais sobre ele, incluindo todas as ações recomendadas. Você também pode limpar o alerta se o problema foi resolvido.
* O bloco **Capacidade** mostra o armazenamento primário provisionado e restante em todos os dispositivos virtuais relativos ao armazenamento total disponível em todos os dispositivos virtuais. **Provisionado** refere-se à quantidade de armazenamento que é preparada e alocada para uso, enquanto **Restante** refere-se à capacidade restante que pode ser provisionada em todos os dispositivos virtuais. A capacidade **Restante em Camadas** é a capacidade disponível que pode ser provisionada, incluindo a nuvem, enquanto a capacidade **Restante Local** é a capacidade restante nos discos anexados às matrizes virtuais.
* No gráfico **Uso**, você pode ver as métricas relevantes para seus dispositivos virtuais. Veja o armazenamento primário usado em todos os dispositivos virtuais, bem como o armazenamento em nuvem consumido pelos dispositivos virtuais nos últimos 7 dias, o período padrão. Use a opção **Editar** no canto superior direito do gráfico para escolher uma escala de tempo diferente.
* O bloco **Dispositivos** fornece um resumo do número de matrizes virtuais no StorSimple Device Manager agrupadas por status do dispositivo. Clique nesse bloco para abrir a folha da lista **Dispositivos** e clique em um dispositivo individual para analisar o resumo de dispositivo específico do dispositivo. Você também pode realizar ações específicas do dispositivo de determinada folha de resumo do dispositivo. Para obter mais informações sobre a folha de resumo do dispositivo, vá para [Folha de resumo do dispositivo](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Exibir os logs de atividade
Para exibir as diversas operações executadas no StorSimple Device Manager, clique no link **Logs de atividade** no lado esquerdo da folha de resumo do serviço do StorSimple. Isso o levará para a folha **Logs de atividade**, na qual você poderá ver um resumo das operações recentes executadas.

![Logs de atividade](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Próximas etapas
Aprenda como [usar a interface do usuário da Web local para administrar sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

