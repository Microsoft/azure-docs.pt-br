---
title: Usar o resumo do dispositivo StorSimple 8000 series | Microsoft Docs
description: Descreve o resumo de dispositivo do serviço do Gerenciador de Dispositivos do StorSimple e como usá-lo para exibir métricas de armazenamento e iniciadores conectados, bem como localizar o número de série e o IQN.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 1d88af2c0739c30b2562bad7660015b890e8159c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578118"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Usar o resumo de dispositivo do serviço do Gerenciador de Dispositivos do StorSimple

## <a name="overview"></a>Visão geral
A folha de resumo do dispositivo StorSimple proporciona uma visão geral das informações de um dispositivo StorSimple específico, ao contrário da folha de resume de serviço, que fornece informações sobre todos os dispositivos incluídos em sua solução Microsoft Azure StorSimple.

A folha de resumo de dispositivo fornece uma exibição resumida de um dispositivo StorSimple da série 8000 que está registrado em determinado Gerenciador de Dispositivos do StorSimple, destacando os problemas de dispositivos que precisam da atenção do administrador do sistema. Este tutorial apresenta a folha de resumo do dispositivo, explica o conteúdo e a função e descreve as tarefas que podem ser realizadas nessa folha.

A folha de resumo do dispositivo exibe as seguintes informações:

![Folha de resumo do dispositivo](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Barra de comandos de gerenciamento

Na folha do dispositivo StorSimple, veja as opções para gerenciar seu dispositivo StorSimple. Você vê os comandos de gerenciamento na parte superior da folha e no lado esquerdo. Use essas opções para adicionar compartilhamentos ou volumes, atualizar ou fazer failover no seu dispositivo.

![Barra de comandos de gerenciamento](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Conceitos básicos

A área de conceitos básicos captura algumas das propriedades importantes, como status, modelo, IQN de destino e a versão de software. 

![Conceitos básicos do dispositivo](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Monitoramento

* O bloco **Alertas** fornece um instantâneo de todos os alertas ativos no seu dispositivo agrupados por gravidade do alerta.

    ![Bloco de alerta](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Clique no bloco para abrir a folha **Alertas** e clique em um alerta individual para exibir detalhes adicionais sobre ele, incluindo todas as ações recomendadas. Você também pode limpar o alerta se o problema foi resolvido.

    ![Clique no bloco de alerta](./media/storsimple-8000-device-dashboard/device-summary10.png)

* O bloco **Status e integridade** fornece informações sobre a integridade do componente de hardware para um dispositivo, incluindo o status do dispositivo. O status do dispositivo pode estar offline, online, desativado ou pronto para ser configurado.

    ![Bloco de status e integridade](./media/storsimple-8000-device-dashboard/device-summary5.png)

* O bloco **Volumes** fornece um resumo do número de volumes no seu dispositivo agrupado por status.

    ![Bloco Volumes](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Clique no bloco para abrir a folha de lista **Volumes** e clique em um volume individual para exibir ou modificar suas propriedades.
    
    ![Clique no bloco Volumes](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Para obter mais informações, veja como [gerenciar volumes](storsimple-8000-manage-volumes-u2.md).

* No gráfico **Uso**, é possível exibir o armazenamento primário usado no seu dispositivo e o armazenamento na nuvem consumido nos últimos sete dias, o período padrão.

     ![Bloco Uso](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Para escolher uma escala de tempo diferente, use a opção **Editar** no canto superior direito do gráfico.

     ![Editar gráfico de uso](./media/storsimple-8000-device-dashboard/device-summary12.png)

     Nesse gráfico, você pode exibir métricas para o armazenamento principal total (a quantidade de dados gravados pelos hosts no seu dispositivo) e o armazenamento em nuvem total consumido pelo dispositivo em um período.
  
     Nesse contexto, *armazenamento primário* refere-se à quantidade total de dados gravados pelo host e pode ser dividido por tipo de volume: o *armazenamento em camadas primário* inclui tanto dados armazenados localmente quanto dados em camadas na nuvem. O *armazenamento primário fixado localmente* inclui apenas os dados armazenados localmente. O *armazenamento em nuvem*, por outro lado, é uma medição do volume total de dados armazenados na nuvem. Esse armazenamento inclui dados em camadas e backups. Os dados armazenados na nuvem passam pela eliminação de duplicação e são compactados, enquanto o armazenamento primário indica a quantidade de armazenamento usada antes dos dados passarem pela eliminação de duplicação e serem compactados. (Você pode comparar esses dois números para ter uma ideia da taxa de compactação). Tanto para o armazenamento primário, quanto para o armazenamento na nuvem, os volumes mostrados se baseiam na frequência de acompanhamento configurada. Por exemplo, se você escolher a frequência de uma semana, o gráfico mostrará dados para cada dia da semana anterior.

     Para ver o volume de armazenamento em nuvem consumido ao longo do tempo, selecione a opção **ARMAZENAMENTO EM NUVEM USADO**. Para ver o armazenamento total gravado pelo host, selecione as opções **ARMAZENAMENTO PRIMÁRIO EM CAMADAS USADO** e **ARMAZENAMENTO PRIMÁRIO LOCALMENTE AFIXADO USADO**. 
     Para obter mais informações, consulte [Usar o serviço do Gerenciador de Dispositivos do StorSimple para monitorar seu dispositivo StorSimple](storsimple-monitor-device.md).


* O bloco **Capacidade** exibe o armazenamento primário provisionado e restante no dispositivo em relação ao armazenamento total disponível nele. **Provisionado** refere-se à quantidade de armazenamento que é preparada e alocada para uso, enquanto **Restante** refere-se à capacidade restante que pode ser provisionada nesse dispositivo. 

    ![Bloco Uso](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Clique neste bloco para exibir como a capacidade é provisionada em volumes fixados localmente e em camadas. A capacidade **Restante em Camadas** é a capacidade disponível que pode ser provisionada, incluindo a nuvem, enquanto a capacidade **Restante Local** é a capacidade restante nos discos anexados a esse dispositivo.

    ![Clique no gráfico de uso](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [folha de resumo do serviço do StorSimple](storsimple-8000-service-dashboard.md).
* Saiba mais sobre como [usar o serviço Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

