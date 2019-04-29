---
title: Monitorar seu dispositivo StorSimple série 8000 | Microsoft Docs
description: Descreve como usar o serviço Gerenciador de Dispositivos do StorSimple para monitorar o uso, desempenho de E/S e utilização da capacidade.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: 602514df69977891167f341db0ab20913bcacc9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634398"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Usar o serviço Gerenciador de Dispositivos do StorSimple para monitorar seu dispositivo StorSimple

## <a name="overview"></a>Visão geral
É possível usar o serviço Gerenciador de Dispositivos do StorSimple para monitorar dispositivos específicos na sua solução do StorSimple. É possível criar gráficos personalizados com base no desempenho de E/S, utilização da capacidade, taxa de transferência de rede e métricas de desempenho do dispositivo e fixá-los no painel. Para obter mais informações, vá para [personalizar seu painel do portal](../azure-portal/azure-portal-dashboards.md).

Para exibir as informações de monitoramento de um dispositivo específico, no Portal do Azure, selecione o serviço Gerenciador de Dispositivos do StorSimple. Na lista de dispositivos, selecione seu dispositivo e acesse **Monitorar**. Em seguida, é possível ver os gráficos **Capacidade**, **Uso** e **Desempenho** para o dispositivo selecionado.

## <a name="capacity"></a>Capacity
**Capacidade** controla o espaço provisionado e o espaço restante no dispositivo. A capacidade restante é exibida como fixado localmente ou em camadas.

A capacidade provisionada e restante é dividida por volumes fixados localmente e em camadas. Para cada volume, a capacidade provisionada e a capacidade restante no dispositivo são exibidas.

![Capacidade de E/S](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Uso
**Uso** rastreia as métricas relacionadas à quantidade de espaço de armazenamento de dados usada pelos volumes, contêineres de volume ou dispositivo. Você pode criar relatórios com base na utilização de capacidade do armazenamento principal, armazenamento em nuvem ou armazenamento do dispositivo. A utilização da capacidade pode ser medida em um volume específico, um contêiner de volume específico ou todos os contêineres de volume.
Por padrão, o uso nas últimas 24 horas é informado. É possível editar o gráfico para alterar a duração em que o uso é informado selecionando:
* Últimas 24 horas
* Últimos 7 dias
* Últimos 30 dias
* Últimos 90 dias
* Ano passado

Duas métricas-chave, crescimento e intervalo, são relatadas para os gráficos de uso. Intervalo refere-se ao valor máximo e aos valores mínimos do uso relatado ao longo da duração selecionada (instância fo, Últimos 7 dias).

Crescimento refere-se ao aumento no uso do primeiro dia até o último dia ao longo da duração selecionada. 

Crescimento e intervalo também podem ser representados pelas equações a seguir:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

O armazenamento primário, na nuvem e local pode ser descrito da seguinte maneira:

### <a name="primary-storage-usage"></a>Uso de armazenamento primário
Esses gráficos mostram a quantidade de dados gravados em volumes StorSimple antes desses dados passarem pela eliminação de duplicação e serem compactados. É possível exibir o armazenamento primário usado por todos os volumes em um contêiner de volume ou para um único volume. O armazenamento primário usado é dividido por armazenamento em camadas primário uso e armazenamento fixado localmente usado primário.

Os gráficos seguintes mostram o armazenamento primário usado para um dispositivo StorSimple antes e depois de um instantâneo de nuvem ter sido capturado. Como esses são apenas dados de volume, um instantâneo de nuvem não deve alterar o armazenamento primário. Como você pode ver, o gráfico não mostra nenhuma diferença no armazenamento em camadas ou fixado localmente primário como resultado de tirar um instantâneo de nuvem. O instantâneo de nuvem iniciou aproximadamente às 11h50 nesse dispositivo.

![Utilização da capacidade primária após o instantâneo da nuvem](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Se agora você executar a E/S no host conectado ao seu dispositivo StorSimple, você verá um aumento no armazenamento em camadas primário ou no armazenamento fixado localmente primário usado dependendo de quais volumes (em camadas ou fixado localmente) nos quais você gravou os dados. Veja os gráficos de uso de armazenamento primário para um dispositivo StorSimple. Nesse dispositivo, o host do StorSimple começou a atender gravações aproximadamente às 14h30 em um volume em camadas no dispositivo. É possível ver o pico na gravação em bytes/s correspondente à E/S em execução no host.

![Desempenho quando a E/S está em execução em volumes em camadas](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Se você observar o armazenamento em camadas primário usado, ele aumentou enquanto o uso fixado localmente primário permaneceu inalterado, já que nenhuma gravação foi feita nos volumes fixados localmente no dispositivo.

![Utilização da capacidade primária quando a E/S está em execução nos volumes em camada](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Se você estiver executando a Atualização 3 ou superior, será possível dividir a utilização da capacidade principal do armazenamento por um volume individual, todos os volumes, todos os volumes em camadas e todos os volumes fixados localmente, conforme mostrado abaixo. Dividir por todos os volumes fixados localmente permitirá que você verifique rapidamente o volume da camada local que foi usado.

![Utilização da capacidade primária para todos os volumes em camadas](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Utilização da capacidade principal para todos os volumes fixados localmente](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Além disso, é possível clicar em cada um dos volumes na lista e ver o uso correspondente.

![Utilização da capacidade principal para todos os volumes fixados localmente](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Uso do armazenamento em nuvem
Esses gráficos mostram a quantidade de armazenamento em nuvem usado. Esses dados passam pela eliminação de duplicação e são compactados. O valor inclui instantâneos de nuvem que podem conter dados que não serão refletidos em nenhum volume primário e são mantidos para fins de retenção obrigatória ou herança. Você pode comparar os valores de consumo de armazenamento de nuvem e principal para obter uma ideia da taxa de redução dos dados, embora o número não vá ser exato.

Os gráficos a seguir mostram a utilização da capacidade de armazenamento em nuvem de um dispositivo StorSimple quando um instantâneo de nuvem foi capturado.

* O instantâneo de nuvem foi iniciado aproximadamente às 11h50 nesse dispositivo e é possível ver que, antes do instantâneo de nuvem, não havia nenhum armazenamento em nuvem usado. 
* Quando o instantâneo de nuvem foi concluído, a utilização do armazenamento em nuvem disparou 0,89 GB. 
* Embora o instantâneo de nuvem estivesse em andamento, houve um pico correspondente na E/S do dispositivo para a nuvem.

    ![Utilização do armazenamento em nuvem antes do instantâneo de nuvem](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Utilização do armazenamento em nuvem depois do instantâneo de nuvem](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![E/S de dispositivo para a nuvem durante um instantâneo de nuvem](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Uso do armazenamento local
Esses gráficos mostram a utilização total do dispositivo, que será maior do que o uso do armazenamento primário, porque inclui a camada SSD linear. Essa camada contém uma quantidade de dados que também existe nas outras camadas do dispositivo. A capacidade na camada SSD linear é alternada para que quando novos dados chegam, os dados antigos sejam movidos para a camada HDD (momento no qual eles passam por eliminação de duplicação e são compactados) e subsequentemente para a nuvem.

Ao longo do tempo, o armazenamento primário usado e o armazenamento local usado provavelmente aumentarão juntos até que os dados comecem a ser organizados na nuvem. Nesse momento, o armazenamento local usado provavelmente começará a atingir a estabilidade, mas o armazenamento primário usado aumentará conforme mais dados forem gravados.

Os gráficos seguintes mostram o armazenamento primário usado para um dispositivo StorSimple quando um instantâneo de nuvem foi capturado. O instantâneo de nuvem iniciou às 11h50 e o armazenamento local começou a diminuir naquela hora. O armazenamento local usado diminuiu de 1,445 GB para 1,09 GB. Isso indica que provavelmente os dados descompactados na camada SSD linear tiveram a duplicação eliminada e foram compactados e movidos para a camada de unidade de disco rígido. Observe que, se o dispositivo já tiver uma grande quantidade de dados nas camadas de SSD e unidade de disco rígido, você poderá não ver essa redução. Neste exemplo, o dispositivo tem uma pequena quantidade de dados.

![Utilização do armazenamento local depois do instantâneo de nuvem](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Desempenho
**Desempenho** rastreia métricas relacionadas ao número de operações de leitura e de gravação entre as interfaces do iniciador iSCSI no servidor host e o dispositivo ou o dispositivo e a nuvem. Esse desempenho pode ser medido para um volume específico, um contêiner de volume específico ou todos os contêineres de volume. O desempenho também inclui a utilização da CPU e a taxa de transferência de rede para os vários adaptadores de rede em seu dispositivo.

### <a name="io-performance-for-initiator-to-device"></a>Desempenho de E/S do iniciador para o dispositivo
A tabela abaixo mostra as E/Ss para o iniciador do dispositivo para todos os volumes de um dispositivo de produção. As métricas no gráfico são bytes de leitura e de gravação por segundo. Também é possível elaborar um gráfico de E/S de leitura, gravação e pendente ou latências de leitura e de gravação.

![Desempenho de E/S do iniciador para o dispositivo](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Desempenho de E/S do dispositivo para a nuvem
Para o mesmo dispositivo, as operações de E/S são criados gráficos com os dados do dispositivo para a nuvem para todos os contêineres de volume. Nesse dispositivo, os dados estão apenas na camada de linear e nada vazou para a nuvem. Não há nenhuma operação de leitura/gravação ocorrendo do dispositivo para a nuvem. Portanto, os picos no gráfico estão a um intervalo de 5 minutos, o que corresponde à frequência na qual a pulsação é verificada entre o dispositivo e o serviço.

Para o mesmo dispositivo, um instantâneo de nuvem foi capturado para dados de volume a partir das 11h50. Isso resultou em fluxo de dados do dispositivo para a nuvem. As gravações foram veiculadas para a nuvem nessa duração. O gráfico de E/S mostra um pico nos Bytes de gravação/s que corresponde à hora em que o instantâneo foi capturado.

![E/S de dispositivo para a nuvem durante um instantâneo de nuvem](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Taxa de transferência de rede para adaptadores de rede do dispositivo
**Taxa de transferência de rede** acompanha as métricas relacionadas à quantidade de dados transferidos de interfaces de rede do iniciador iSCSI no servidor host e o dispositivo e entre o dispositivo e a nuvem. Você pode monitorar essa métrica para cada uma das interfaces de rede iSCSI em seu dispositivo.

Os gráficos a seguir mostram a taxa de transferência de rede para a rede Data 0, 1 1 GbE em seu dispositivo, que foi habilitado para a nuvem e para iSCSI. Nesse dispositivo, em 14 de junho, em torno das 21h, os dados foram organizações em camadas na nuvem (nenhum instantâneo de nuvem foi tirado nesse horário que aponta para disposição em camadas que é o mecanismo para mover os dados para a nuvem) que resultou na E/S sendo veiculada para a nuvem. Há um pico correspondente no grafo de taxa de transferência de rede para a mesma hora e a maioria do tráfego de rede é de saída para a nuvem.

![Taxa de transferência de rede para Data 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Se observarmos o gráfico de taxa de transferência da interface Data 1, outro adaptador de rede 1 GbE que era apenas habilitada para iSCSI, então não haveria praticamente nenhum tráfego de rede nessa duração.

![Taxa de transferência de rede para Data 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Utilização da CPU do dispositivo
A **Utilização da CPU** rastreia as métricas relacionadas à CPU utilizada em seu dispositivo. O gráfico a seguir mostra as estatísticas de utilização de CPU para um dispositivo em produção.

![Utilização da CPU do dispositivo](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Próximas etapas
* Saiba como [usar o painel de dispositivo do serviço Gerenciador de Dispositivos do StorSimple](storsimple-device-dashboard.md).
* Saiba como [usar o serviço Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

