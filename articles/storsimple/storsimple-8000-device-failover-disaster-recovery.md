---
title: Failover e recuperação de desastre do StorSimple para dispositivos da série 8000 | Microsoft Docs
description: Saiba como fazer failover do dispositivo StorSimple para si mesmo, para outro dispositivo físico ou para um dispositivo de nuvem.
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
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 079a2f153f257040d1899a33c9e255d633e526ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576317"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Failover e recuperação de desastre para seu dispositivo StorSimple da série 8000

## <a name="overview"></a>Visão geral

Este artigo descreve o recurso de failover de dispositivo para os dispositivos StorSimple da série 8000 e como esse recurso pode ser usado para recuperar dispositivos StorSimple em caso de desastre. O StorSimple usa um failover de dispositivo para migrar os dados de um dispositivo de origem no datacenter para outro dispositivo de destino. As diretrizes neste artigo se aplicam a dispositivos físicos StorSimple da série 8000 e a dispositivos de nuvem que executam versões de software com a Atualização 3 e posterior.

O StorSimple usa a folha **Dispositivos** para iniciar o recurso de failover de dispositivo em caso de desastre. Esta folha lista todos os dispositivos StorSimple conectados ao seu serviço do Gerenciador de Dispositivos do StorSimple.

![Folha Dispositivos](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Recuperação de desastres (DR) e failover de dispositivo

Em um cenário de recuperação de desastre (DR), o dispositivo principal para de funcionar. O StorSimple usa o dispositivo primário como _fonte_ e move os dados de nuvem associados para outro dispositivo de _destino_. Esse processo é conhecido como *failover*. O gráfico a seguir ilustra o processo de failover.

![O que acontece no failover de dispositivo?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

O dispositivo de destino para um failover poderia ser um dispositivo físico ou até mesmo um dispositivo de nuvem. O dispositivo de destino pode estar localizado no mesmo local geográfico que ou em um diferente do dispositivo de origem.

Durante o failover, você pode selecionar contêineres de volume para a migração. Em seguida, o StorSimple altera a propriedade desses contêineres de volume do dispositivo de origem para o dispositivo de destino. Após a alteração da propriedade dos contêineres de volume, o StorSimple exclui os contêineres do dispositivo de origem. Após a conclusão da exclusão, você poderá fazer failback no dispositivo de destino. _Fazer failback_ transfere a propriedade de volta para o dispositivo de origem original.

### <a name="cloud-snapshot-used-during-device-failover"></a>Instantâneo de nuvem usado durante o failover do dispositivo

Depois de uma recuperação de desastre, o backup de nuvem mais recente é usado para restaurar os dados para o dispositivo de destino. Para obter mais informações sobre instantâneos de nuvem, consulte [Usar o serviço do Gerenciador de Dispositivos do StorSimple para fazer um backup manual](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

Em um StorSimple da série 8000, as políticas de backup são associadas aos backups. Se houver várias políticas de backup para o mesmo volume, o StorSimple selecionará a política de backup com o maior número de volumes. Em seguida, o StorSimple usa o backup mais recente da política de backup selecionado para restaurar os dados no dispositivo de destino.

Suponha que existam duas políticas de backup, *defaultPol* e *customPol*:

* *defaultPol*: Um volume, *vol1*, executada diariamente a partir em 10:30 PM.
* *customPol*: Quatro volumes, *vol1*, *vol2*, *vol3*, *vol4*, executada diariamente a partir às 10:00.

Nesse caso, o StorSimple prioriza o controle de falhas e usa *customPol*, pois ela tem mais volumes. O backup mais recente dessa política é usado para restaurar os dados. Para obter mais informações sobre como criar e gerenciar políticas de backup, acesse [Usar o serviço do Gerenciador de Dispositivos do StorSimple para gerenciar políticas de backup](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Considerações comuns para failover de dispositivo

Antes de fazer failover em um dispositivo, leia as informações a seguir:

* Antes de iniciar um failover de dispositivo, todos os volumes nos contêineres do volume devem estar offline. Em um failover não planejado, os volumes do StotSimple ficarão offline automaticamente. Contudo, se você estiver executando um failover planejado (para testar a recuperação de desastre), deverá deixar todos os volumes offline.
* Somente os contêineres de volume que têm um instantâneo de nuvem são listados para recuperação de desastre. Deve haver pelo menos um contêiner de volume com um instantâneo de nuvem associado para recuperar dados.
* Se houver instantâneos de nuvem que se estendem por vários contêineres de volume, o StorSimple fará failover desses contêineres de volume como um conjunto. Em casos raros, se houver instantâneos locais que se estendem por vários contêineres de volume, mas não instantâneos de nuvem associados, o StorSimple faz failover dos instantâneos locais e os dados locais são perdidos após a recuperação de desastre.
* Os dispositivos de destino disponíveis para DR são dispositivos que têm espaço suficiente para acomodar os contêineres de volume selecionados. Dispositivos que não têm espaço suficiente não são listados como dispositivos de destino.
* Após uma recuperação de desastre, o desempenho de acesso a dados pode ser afetado significativamente (por um período limitado), pois o dispositivo precisará acessar a dados de nuvem e armazená-los localmente.

#### <a name="device-failover-across-software-versions"></a>Failover de dispositivo em versões de software

Um serviço do Gerenciador de Dispositivos do StorSimple em uma implantação pode ter vários dispositivos, tanto físicos quanto de nuvem, todos executando versões de software diferentes.

Use a tabela a seguir para determinar se é possível fazer failover ou failback para outro dispositivo que executa uma versão de software diferente e como os tipos de volume se comportam durante a recuperação de desastre.

#### <a name="failover-and-failback-across-software-versions"></a>Failover e failback entre versões de software

| Failover/failback de | Dispositivo físico | Dispositivo de nuvem |
| --- | --- | --- |
| Atualização 3 para Atualização 4 |Volumes em camadas fazem failover em camadas. <br></br>Volumes fixados localmente fazem failover fixados localmente. <br></br> Após um failover, quando você criar um instantâneo do dispositivo com Atualização 4, o [acompanhamento de mapa de calor](storsimple-update4-release-notes.md#whats-new-in-update-4). |Volumes fixados localmente fazem failover em camadas. |
| Atualização 4 para Atualização 3 |Volumes em camadas fazem failover em camadas. <br></br>Volumes fixados localmente fazem failover fixados localmente. <br></br> Backups usados para restauração retêm os metadados do mapa de calor. <br></br>O acompanhamento de mapa de calor não está disponível na Atualização 3 após um failback. |Volumes fixados localmente fazem failover em camadas. |


## <a name="device-failover-scenarios"></a>Cenários de failover de dispositivo

Em caso de desastre, você pode optar por fazer failover do dispositivo StorSimple:

* [Para um dispositivo físico](storsimple-8000-device-failover-physical-device.md).
* [Para ele próprio](storsimple-8000-device-failover-same-device.md).
* [Para um dispositivo de nuvem](storsimple-8000-device-failover-cloud-appliance.md).

Os artigos anteriores fornecem etapas detalhadas para cada um dos casos de failover acima.


## <a name="failback"></a>Failback

A partir da Atualização 3, o StorSimple também dá suporte para failback. O failback nada mais é do que o inverso do failover, o destino se torna a origem e o dispositivo de origem original durante o failover agora se torna o dispositivo de destino. 

Durante o failback, o StorSimple ressincroniza os dados de volta para o local primário, interrompe a E/S e a atividade do aplicativo e retorna para o local original.

Após a conclusão de um failover, o StorSimple realiza as seguintes ações:

* O StorSimple limpa os contêineres de volume que passam pelo failover do dispositivo de origem.
* O StorSimple inicia um trabalho em segundo plano por contêiner de volume (que passou por failover) no dispositivo de origem. Se você tentar fazer failback enquanto o trabalho está em andamento, receberá uma notificação sobre isso. Aguarde até que o trabalho seja concluído para começar o failback.
* O tempo necessário para concluir a exclusão de contêineres de volume depende de vários fatores, como o volume de dados, o tempo de vida dos dados, o número de backups e a largura de banda de rede disponível para a operação.

Se você estiver planejando fazer failbacks ou failovers de teste, recomendamos testar os contêineres de volume com menos dados (Gbs). Geralmente, o failback pode ser iniciado 24 horas após a conclusão do failover.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

P. **O que acontecerá se a DR falhar ou tiver êxito parcial?**

a. Caso a DR falhe, recomendamos que você tente novamente. O segundo trabalho de failover do dispositivo está ciente do progresso do primeiro trabalho e inicia a partir desse ponto em diante.

P. **Posso excluir um dispositivo enquanto o failover do dispositivo estiver em andamento?**

a. Você não pode excluir um dispositivo enquanto uma DR está em andamento. Só é possível excluir o dispositivo após a conclusão da DR. Você pode monitorar o andamento do trabalho do failover do dispositivo na folha **Trabalhos**.

P. **Quando a coleta de lixo começa no dispositivo de origem para que os dados locais do dispositivo de origem sejam excluídos?**

a. A coleta de lixo será habilitada no dispositivo de origem somente depois que o dispositivo estiver completamente limpo. A limpeza inclui limpar objetos que passaram pelo failover do dispositivo de origem, como volumes, objetos de backup (não dados), contêineres de volume e políticas.

P. **O que acontecerá se o trabalho de exclusão associado aos contêineres de volume no dispositivo de origem falhar?**

a.  Se o trabalho de exclusão falhar, você poderá excluir manualmente os contêineres de volume. Na folha **Dispositivos**, selecione o dispositivo de origem e clique em **Contêineres de volume**. Selecione os contêineres de volume que passaram pelo failover e, na parte inferior da folha, clique em **Excluir**. Depois de ter excluído todos os contêineres de volume que passaram pelo failover no dispositivo de origem, você pode iniciar o failback. Para obter mais informações, acesse [Excluir um contêiner de volume](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>BCDR (recuperação de desastre de continuidade de negócios)

Um cenário de BCDR (recuperação de desastre de continuidade de negócios) ocorre quando todo o datacenter do Azure para de funcionar. Esse cenário pode afetar o serviço do Gerenciador de Dispositivos do StorSimple e os dispositivos StorSimple associados.

Se um dispositivo StorSimple foi registrado logo antes da ocorrência de um desastre, talvez ele precise passar por uma redefinição de fábrica. Após o desastre, o dispositivo StorSimple aparecerá offline no Portal do Azure. Este dispositivo deve ser excluído do portal. Redefina o dispositivo para os padrões de fábrica e registre-o novamente com o serviço.

## <a name="next-steps"></a>Próximas etapas

Se você estiver pronto para executar um failover de dispositivo, escolha um dos cenários a seguir para obter instruções detalhadas:

- [Failover para outro dispositivo físico](storsimple-8000-device-failover-physical-device.md)
- [Failover para o mesmo dispositivo](storsimple-8000-device-failover-same-device.md)
- [Failover para um Dispositivo de Nuvem StorSimple](storsimple-8000-device-failover-cloud-appliance.md)

Se você fez failover do dispositivo, escolha uma das seguintes opções:

* [Desativar e excluir um dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* [Usar o serviço do Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

