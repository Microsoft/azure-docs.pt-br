---
title: Use o painel do dispositivo StorSimple Manager | Microsoft Docs
description: "Descreve o painel do dispositivo do serviço StorSimple Manager e como usá-lo para exibir iniciadores conectados e métricas de armazenamento e localizar o número de série e o IQN."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6c213969-a385-461f-b698-78ef5b8a79cc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0d8035b9608ca3bac3d4822c7c755b81c96d481e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-device-dashboard-in-storsimple-manager-service"></a>Usar o painel de dispositivo no serviço StorSimple Manager  

## <a name="overview"></a>Visão geral
O painel de dispositivo do StorSimple Manager proporciona uma visão geral das informações de um dispositivo StorSimple específico, ao contrário do painel de serviço, que fornece informações sobre todos os dispositivos incluídos em sua solução Microsoft Azure StorSimple.

![Página Painel de dispositivo](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

O painel contém as seguintes informações:

* **Área do gráfico** : você pode ver as métricas de armazenamento relevantes na área do gráfico, na parte superior do painel. Nesse gráfico, você pode exibir métricas para o armazenamento principal total (a quantidade de dados gravados pelos hosts no seu dispositivo) e o armazenamento em nuvem total consumido pelo dispositivo em um período.
  
     Nesse contexto, *armazenamento primário* refere-se à quantidade total de dados gravados pelo host e pode ser dividido por tipo de volume: o *armazenamento primário em camadas* inclui tanto dados armazenados localmente quanto dados em camadas na nuvem; o *armazenamento primário localmente afixado* inclui apenas os dados armazenados localmente. O *armazenamento em nuvem*, por outro lado, é uma medição do volume total de dados armazenados na nuvem. Isso inclui backups e dados em camadas. Observe que os dados armazenados na nuvem passam pela eliminação de duplicação e são compactados, enquanto o armazenamento principal indica a quantidade de armazenamento usada antes de os dados passarem pela eliminação de duplicação e serem compactados. (Você pode comparar esses dois números para ter uma ideia da taxa de compactação). Tanto para o armazenamento principal, quanto para o armazenamento em nuvem, os volumes mostrados serão baseados na frequência de rastreamento que você configura. Por exemplo, se você escolher a frequência de uma semana, o gráfico mostrará dados para cada dia da semana anterior.
  
     É possível configurar o gráfico como se segue:
  
  * Para ver o volume de armazenamento em nuvem consumido ao longo do tempo, selecione a opção **ARMAZENAMENTO EM NUVEM USADO**. Para ver o armazenamento total gravado pelo host, selecione as opções **ARMAZENAMENTO PRIMÁRIO EM CAMADAS USADO** e **ARMAZENAMENTO PRIMÁRIO LOCALMENTE AFIXADO USADO**. Na ilustração, ambas as opções são selecionadas. Portanto, o gráfico mostra volumes para armazenamento principal e em nuvem. Observe que qualquer armazenamento primário usado antes da instalação da Atualização 2 é representado pela linha **ARMAZENAMENTO PRIMÁRIO EM CAMADAS USADO**.
  * Use o menu suspenso no canto superior direito do gráfico para especificar um período de 1 semana, 1 mês, 3 meses ou 1 ano. Observe que o gráfico no nível superior é atualizado apenas uma vez por dia e, portanto, refletirá os totais do dia anterior.
    
    Para obter mais informações, consulte [Use the StorSimple Manager service to monitor your StorSimple device (Usar o serviço StorSimple Manager para monitorar seu dispositivo StorSimple)](storsimple-monitor-device.md).
* **Visão geral de uso** – Na área de **visão geral de uso**, você pode ver o volume de armazenamento principal usado, o volume do armazenamento provisionado e a capacidade máxima de armazenamento do seu dispositivo. Ao comparar esses números de uso com o volume máximo de armazenamento disponível, você pode ver instantaneamente se precisa de mais armazenamento. Observe que essa visão geral é atualizada a cada 15 minutos e, devido à diferença na frequência de atualização, ela pode mostrar diferentes números dos mostrados na área do gráfico acima, que é atualizada diariamente. Para obter mais informações, consulte [Use the StorSimple Manager service to monitor your StorSimple device (Usar o serviço StorSimple Manager para monitorar seu dispositivo StorSimple)](storsimple-monitor-device.md).
* **Alertas** – A área de **alertas** inclui uma visão geral dos alertas de seu dispositivo. Os alertas são agrupados por severidade, e uma contagem do número de alertas é fornecida em cada nível de severidade. Clicar na severidade do alerta abre uma exibição da guia de alertas em escopo, mostrando apenas os alertas desse nível de severidade para esse dispositivo.
* **Trabalhos** – A área de **trabalhos** mostra o resultado da atividade de trabalho recente. Isso pode assegurar que o sistema está funcionando conforme esperado ou pode informar que você precisa tomar uma medida corretiva. Para obter mais informações sobre trabalhos concluídos recentemente, clique em **Trabalhos bem-sucedidos nas últimas 24 horas**.
* A área **visão rápida** à direita do painel fornece informações úteis, como modelo do dispositivo, número de série, status, descrição e número de volumes.

Você também pode configurar o failover e exibir os iniciadores conectados no painel de dispositivo.

As tarefas comuns que podem ser executadas nessa página são:

* Exibir iniciadores conectados
* Localizar o número de série do dispositivo
* Localizar o IQN de destino do dispositivo

## <a name="view-connected-initiators"></a>Exibir iniciadores conectados
Você pode exibir os iniciadores iSCSI conectados ao seu dispositivo clicando no link **Exibir iniciadores conectados** fornecido na área **visão rápida** do painel do dispositivo. Essa página fornece uma listagem tabular dos iniciadores que se conectaram com êxito ao dispositivo. Para cada iniciador, você pode ver:

* O IQN (Nome Qualificado iSCSI) do iniciador conectado.
* O nome do ACR (registro de controle de acesso) que permite esse iniciador conectado.
* O endereço IP do iniciador conectado.
* As interfaces de rede às quais o iniciador está conectado no seu dispositivo de armazenamento. Isso pode variar de DATA 0 a DATA 5.
* Todos os volumes que o iniciador conectado tem permissão para acessar de acordo com a configuração ACR atual.

Caso você veja iniciadores inesperados nessa lista ou não veja os esperados, revise a configuração ACR. Um máximo de 512 iniciadores pode se conectar ao dispositivo.

## <a name="find-the-device-serial-number"></a>Localizar o número de série do dispositivo
O número de série do dispositivo pode ser necessário quando você configura o MPIO (Microsoft Multipath I/O) no dispositivo. Execute as etapas a seguir para localizar o número de série do dispositivo.

#### <a name="to-find-the-device-serial-number"></a>Para localizar o número de série do dispositivo
1. Navegue até **Dispositivos** > **Painel**.
2. No painel à direita do painel, localize a área **visão rápida** .
3. Role para baixo e localize o número de série.

## <a name="find-the-device-target-iqn"></a>Localizar o IQN de destino do dispositivo
Talvez você precise do IQN de destino do dispositivo ao configurar o protocolo CHAP (Challenge Handshake Authentication Protocol) no seu dispositivo StorSimple. Execute as etapas a seguir para localizar o IQN de destino do dispositivo.

### <a name="to-find-the-device-target-iqn"></a>Para localizar o IQN de destino do dispositivo
1. Navegue até **Dispositivos** > **Painel**.
2. No painel à direita do painel, localize a área **visão rápida** .
3. Role para baixo e localize o IQN de destino.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [painel de serviços do StorSimple Manager](storsimple-service-dashboard.md).
* Saiba mais sobre o [uso do serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

