---
title: "Failover e recuperação de desastre do StorSimple | Microsoft Docs"
description: "Saiba como fazer o failover do dispositivo StorSimple para si mesmo, para outro dispositivo físico ou para um dispositivo virtual."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5751598e-49c8-42b3-8121-fea5857a7d83
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2016
ms.author: alkohli
ms.openlocfilehash: bf92ffdb16b86c4033cc96ae2abb060d90f9505e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-device"></a>Failover e recuperação de desastres para o seu dispositivo StorSimple
## <a name="overview"></a>Visão geral
Este tutorial descreve as etapas necessárias para fazer failover de um dispositivo StorSimple em caso de desastre. Um failover permitirá que você migre os dados de um dispositivo de origem no datacenter para outro dispositivo físico ou até mesmo virtual localizado no mesmo ou em um local geográfico diferente. 

A DR (Recuperação de desastre) é orquestrada por meio do recurso de failover de dispositivo e é iniciada na página **Dispositivos** . Esta página exibe em formato de tabela todos os dispositivos StorSimple conectados ao seu serviço StorSimple Manager. Para cada dispositivo, o nome amigável, status, capacidade de provisionamento e máxima, tipo e modelo são exibidos.

![Página Dispositivos](./media/storsimple-device-failover-disaster-recovery/IC740972.png)

As diretrizes neste tutorial se aplicam a dispositivos físicos e virtuais do StorSimple em todas as versões de software.

## <a name="disaster-recovery-dr-and-device-failover"></a>Recuperação de desastres (DR) e failover de dispositivo
Em um cenário de recuperação de desastre (DR), o dispositivo principal para de funcionar. Nessa situação, você pode mover os dados de nuvem associados ao dispositivo com falha para outro dispositivo por meio do dispositivo principal como a *origem* e especificando outro dispositivo como o *destino*. Você pode selecionar um ou mais contêineres de volume para migrar para o dispositivo de destino. Esse processo é conhecido como *failover*. 

Durante o failover, os contêineres de volume do dispositivo de origem alteram a propriedade e são transferidos para o dispositivo de destino. Após a alteração da propriedade dos contêineres de volume, eles serão excluídos do dispositivo de origem. Após a conclusão da exclusão, o dispositivo de destino poderá passar pelo failback.

Normalmente, depois de uma DR, o backup mais recente é usado para restaurar os dados no dispositivo de destino. No entanto, se houver várias políticas de backup para o mesmo volume, a política de backup com o maior número de volumes é escolhida e o backup mais recente dessa política é usado para restaurar os dados no dispositivo de destino.

Por exemplo, se houver duas políticas de backup (uma padrão e uma personalizada) *defaultPol*, *customPol* com os seguintes detalhes:

* *defaultPol*: um volume, *vol1*, é executada diariamente a partir de 22h30.
* *customPol*: quatro volumes, *vol1*, *vol2*, *vol3*, *vol4*, são executados diariamente a partir de 22h.

Nesse caso, *customPol* será usada, pois ela tem mais volumes, e priorizamos o controle de falhas. O backup mais recente dessa política é usado para restaurar os dados.

## <a name="considerations-for-device-failover"></a>Considerações para failover de dispositivo
Em caso de desastre, você pode optar por fazer o failover do dispositivo StorSimple:

* Para um dispositivo físico 
* Para ele próprio
* Para um dispositivo virtual

Para o failover de qualquer dispositivo, tenha em mente o seguinte:

* Os pré-requisitos para DR são que todos os volumes em contêineres de volume estejam offline e os contêineres de volume tenham um instantâneo de nuvem associado. 
* Os dispositivos de destino disponíveis para DR são dispositivos que têm espaço suficiente para acomodar os contêineres de volume selecionados. 
* Os dispositivos que estão conectados ao serviço, mas não atendem aos critérios de espaço suficiente não estarão disponíveis como dispositivos de destino.
* Após uma recuperação de desastres, por um período limitado, o desempenho de acesso a dados pode ser afetado significativamente, pois o dispositivo precisará acessar os dados de nuvem e armazená-los localmente.

#### <a name="device-failover-across-software-versions"></a>Failover de dispositivo em versões de software
Um serviço StorSimple Manager em uma implantação pode ter vários dispositivos físicos e virtuais, todos executando versões de software diferentes. Dependendo da versão do software, os tipos de volume nos dispositivos também podem ser diferentes. Por exemplo, um dispositivo que executa a Atualização 2 ou superior teria volumes em camadas e fixados localmente (com o arquivamento sendo um subconjunto de volumes em camadas). Um dispositivo de pré-atualização 2 por outro lado pode ter volumes em camadas e de arquivamento. 

Use a tabela a seguir para determinar se é possível realizar failover para outro dispositivo executando uma versão de software diferente e o comportamento de tipos de volume durante a recuperação de desastre.

| Failover de | Permitido para dispositivo físico | Permitido para dispositivo virtual |
| --- | --- | --- |
| Atualização 2 para Pré-atualização 1 (versão, 0.1, 0.2, 0.3) |Não |Não |
| Atualização 2 para Atualização 1 (1, 1.1, 1.2) |Sim  <br></br>Se usando volumes fixados localmente ou em camadas ou uma combinação dos dois, o failover dos volumes é sempre realizado como em camadas. |Sim <br></br>Se usando volumes fixados localmente, o failover desses volumes é realizado em camadas. |
| Atualização 2 para Atualização 2 (versão posterior) |Sim <br></br>Se usando volumes fixados localmente ou em camadas ou uma combinação dos dois, o failover dos volumes é sempre realizado como o tipo de volume inicial; em camadas como em camadas e fixados localmente como fixados localmente. |Sim <br></br>Se usando volumes fixados localmente, o failover desses volumes é realizado em camadas. |

#### <a name="partial-failover-across-software-versions"></a>Failover parcial em versões de software
Siga esta orientação se pretender realizar um failover parcial usando um dispositivo de origem StorSimple que executa a pré-Atualização 1 para um de destino que executa a Atualização 1 ou posterior. 

| Failover parcial de | Permitido para dispositivo físico | Permitido para dispositivo virtual |
| --- | --- | --- |
| Pré-Atualização 1 (Versão, 0.1, 0.2, 0.3) para Atualização 1 ou posterior |Sim, veja abaixo para obter a dica de prática recomendada. |Sim, veja abaixo para obter a dica de prática recomendada. |

> [!TIP]
> Houve uma alteração de formato dos metadados e dados de nuvem na Atualização 1 e versões posteriores. Portanto, não recomendamos um failover parcial da pré-Atualização 1 para a Atualização 1 ou versões posteriores. Se você precisar realizar um failover parcial, é recomendável que você primeiro aplique a atualização 1 ou posterior em ambos os dispositivos (origem e destino) e, em seguida, continue com o failover. 
> 
> 

## <a name="fail-over-to-another-physical-device"></a>Failover para outro dispositivo físico
Execute as seguintes etapas para restaurar seu dispositivo para um dispositivo físico de destino.

1. Verifique se o contêiner de volume para o qual você deseja fazer o failover associou instantâneos de nuvem.
2. Na página **Dispositivos**, clique na guia **Contêineres do Volume**.
3. Selecione um contêiner de volume para o qual você gostaria de fazer failover para outro dispositivo. Clique no contêiner de volume para exibir a lista de volumes neste contêiner. Selecione um volume e clique em **Colocar Offline** para colocar o volume offline. Repita esse processo para todos os volumes no contêiner de volume.
4. Repita a etapa anterior para todos os contêineres de volume para os quais você gostaria de fazer o failover para outro dispositivo.
5. Na página **Dispositivos**, clique em **Failover**.
6. No assistente que é aberto, em **Escolher contêiner de volume para failover**:
   
   1. Na lista de contêineres de volume, selecione os contêineres de volume para failover.
      **São exibidos apenas os contêineres de volume com instantâneos de nuvem e volumes offline associados.**
   2. Em **Escolher um dispositivo de destino** para os volumes nos contêineres selecionados, selecione um dispositivo de destino na lista suspensa de dispositivos disponíveis. Apenas os dispositivos que têm a capacidade disponível são exibidos na lista suspensa.
   3. Finalmente, revise as configurações de failover em **Confirmar failover**. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-device-failover-disaster-recovery/IC740895.png).
7. É criado um trabalho de failover que pode ser monitorado por meio da página **Trabalhos** . Se o contêiner de volume em que você fez failover tiver volumes locais, verá os trabalhos de restauração individuais para cada volume local (não para volumes em camadas) no contêiner. Esses trabalhos de restauração podem levar algum tempo para ser concluídos. É provável que o trabalho de failover possa ser concluído anteriormente. Observe que esses volumes terão garantias locais somente depois que os trabalhos de restauração forem concluídos. Após a conclusão do failover, vá para a página **Dispositivos** .                                            
   
   1. Selecione o dispositivo que foi usado como o dispositivo de destino para o processo de failover.
   2. Vá para a página **Contêineres de Volume** . Todos os contêineres de volume, juntamente com os volumes do antigo dispositivo devem ser listados.

## <a name="failover-using-a-single-device"></a>Failover usando um único dispositivo
Se você só tiver um único dispositivo e precisa executar um failover, execute as seguintes etapas.

1. Tirar instantâneos de nuvem de todos os volumes em seu dispositivo.
2. Redefina o dispositivo para os padrões de fábrica. Siga as instruções detalhadas em [como redefinir um dispositivo StorSimple para as configurações padrões de fábrica](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Configure o seu dispositivo e registre-o novamente no serviço StorSimple Manager.
4. Na página **Dispositivos**, o antigo dispositivo deve aparecer como **Offline**. Os dispositivos registrados recentemente devem aparecer como **Online**.
5. Para o novo dispositivo, conclua a configuração mínima do dispositivo pela primeira vez. 
   
   > [!IMPORTANT]
   > **Se a configuração mínima não for concluída primeiro, a recuperação de desastres falhará devido a um bug na implementação atual. Esse problema será corrigido em uma versão posterior.**
   > 
   > 
6. Selecione o dispositivo antigo (status offline) e clique em **Failover**. No assistente que é apresentado, faça o failover desse dispositivo e especificar o dispositivo de destino como o dispositivo registrado recentemente. Para obter instruções detalhadas, consulte [Failover para outro dispositivo físico](#fail-over-to-another-physical-device).
7. Será criado um trabalho de restauração de dispositivo para que você possa monitorar a partir da página **Trabalhos** .
8. Depois que o trabalho for concluído com êxito, acesse o novo dispositivo e navegue até a página **Contêineres de Volume** . Todos os contêineres de volume do antigo dispositivo agora devem ser migrados para o novo dispositivo.

## <a name="fail-over-to-a-storsimple-virtual-device"></a>Failover para um dispositivo virtual StorSimple
Você deve ter um dispositivo virtual StorSimple criado e configurado antes de executar este procedimento. Se executar a Atualização 2, considere usar um dispositivo virtual 8020 para a recuperação de desastres que tem 64 TB e usa o Armazenamento Premium. 

Execute as seguintes etapas para restaurar o dispositivo para um dispositivo virtual do StorSimple de destino.

1. Verifique se o contêiner de volume para o qual você deseja fazer o failover associou instantâneos de nuvem.
2. Na página **Dispositivos**, clique na guia **Contêineres do Volume**.
3. Selecione um contêiner de volume para o qual você gostaria de fazer failover para outro dispositivo. Clique no contêiner de volume para exibir a lista de volumes neste contêiner. Selecione um volume e clique em **Colocar Offline** para colocar o volume offline. Repita esse processo para todos os volumes no contêiner de volume.
4. Repita a etapa anterior para todos os contêineres de volume para os quais você gostaria de fazer o failover para outro dispositivo.
5. Na página **Dispositivos**, clique em **Failover**.
6. No assistente que é aberto, em **Escolher contêiner de volume para failover**, faça o seguinte:
   
    a. Na lista de contêineres de volume, selecione os contêineres de volume para failover.
   
    **São exibidos apenas os contêineres de volume com instantâneos de nuvem e volumes offline associados.**
   
    b. Em **Escolher um dispositivo de destino para os volumes nos contêineres selecionados**, selecione o dispositivo virtual StorSimple na lista suspensa de dispositivos disponíveis. **Somente os dispositivos que possuem capacidade suficiente são exibidos na lista suspensa.**  
7. Finalmente, revise as configurações de failover em **Confirmar failover**. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-device-failover-disaster-recovery/IC740895.png).
8. Após a conclusão do failover, vá para a página **Dispositivos** .
   
    a. Selecione o dispositivo virtual StorSimple que foi usado como o dispositivo de destino para o processo de failover.
   
    b. Vá para a página **Contêineres de Volume** . Agora devem estar listados todos os contêineres de volume, juntamente com os volumes do antigo dispositivo.

![Vídeo disponível](./media/storsimple-device-failover-disaster-recovery/Video_icon.png) **Vídeo disponível**

Para assistir a um vídeo que demonstra como é possível restaurar um dispositivo físico que passou por failover em um dispositivo virtual na nuvem, clique [aqui](https://azure.microsoft.com/documentation/videos/storsimple-and-disaster-recovery/).

## <a name="failback"></a>Failback
A partir da Atualização 3, o StorSimple também dá suporte para failback. Após a conclusão do failover, as seguintes ações ocorrem:

* os contêineres de volume que passam pelo failover são removidos do dispositivo de origem.
* Um trabalho em segundo plano por contêiner de volume (failover) é iniciado no dispositivo de origem. Se você tentar o failback enquanto o trabalho estiver em andamento, receberá uma notificação sobre isso. Você precisará aguardar até que o trabalho seja concluído para começar o failback. 
  
    O tempo de conclusão da exclusão de contêineres de volume depende de vários fatores, como a quantidade de dados, o tempo de vida dos dados, o número de backups e a largura de banda de rede disponível para a operação. Se você estiver planejando failbacks/failovers de teste, será recomendável testar contêineres de volume com menos dados (Gbs). Na maioria dos casos, você pode iniciar o failback 24 horas após a conclusão do failover. 

## <a name="frequently-asked-questions"></a>Perguntas frequentes
P. **O que acontecerá se a DR falhar ou tiver êxito parcial?**

a. Caso a DR falhe, recomendamos que você tente novamente. Na segunda vez, a DR saberá o que foi feito e quando o processo foi paralisado na primeira vez. O processo de DR é retomado a partir desse ponto. 

P. **Posso excluir um dispositivo enquanto o failover do dispositivo estiver em andamento?**

a. Você não pode excluir um dispositivo enquanto uma DR está em andamento. Só é possível excluir o dispositivo após a conclusão da DR.

P.    **Quando a coleta de lixo começa no dispositivo de origem para que os dados locais do dispositivo de origem sejam excluídos?**

a. A coleta de lixo será habilitada no dispositivo de origem somente depois que o dispositivo estiver completamente limpo. A limpeza inclui limpar objetos que passaram pelo failover do dispositivo de origem, como volumes, objetos de backup (não dados), contêineres de volume e políticas.

P. **O que acontecerá se o trabalho de exclusão associado aos contêineres de volume no dispositivo de origem falhar?**

a.  Se o trabalho de exclusão falhar, você precisará disparar manualmente a exclusão dos contêineres de volume. Na página **Dispositivos**, selecione o dispositivo de origem e clique em **Contêineres de volume**. Selecione os contêineres de volume dos quais você fez o failover e, na parte inferior da página, clique em **Excluir**. Depois de ter excluído todos os contêineres de volume que passaram pelo failover no dispositivo de origem, você pode iniciar o failback.

## <a name="business-continuity-disaster-recovery-bcdr"></a>BCDR (recuperação de desastre de continuidade de negócios)
Um cenário de BCDR (recuperação de desastre de continuidade de negócios) ocorre quando todo o datacenter do Azure para de funcionar. Isso pode afetar o serviço StorSimple Manager e os dispositivos StorSimple associados.

Se houver dispositivos StorSimple que foram registrados antes da ocorrência de um desastre, talvez eles precisem passar por uma redefinição de fábrica. Após o desastre, o dispositivo StorSimple será mostrado como offline. O dispositivo StorSimple deve ser excluído do portal, e uma redefinição de fábrica deve ser feita, seguida de um novo registro.

## <a name="next-steps"></a>Próximas etapas
* Depois de realizar um failover, talvez seja necessário [desativar ou excluir seu dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).
* Para obter informações sobre como usar o serviço StorSimple Manager, acesse [Usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

