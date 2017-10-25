---
title: Perguntas frequentes sobre os volumes do StorSimple localmente afixados | Microsoft Docs
description: Fornece respostas a perguntas frequentes sobre volumes localmente afixados do StorSimple.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: 857647e89b7ac5d9bc72bc4b2ace0ea7279353f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>Volume localmente afixado do StorSimple: perguntas frequentes
## <a name="overview"></a>Visão geral
Veja perguntas e respostas que podem ser úteis ao criar um volume localmente afixado do StorSimple, converter um volume em camadas em um volume localmente afixado (e vice-versa) ou fazer backup de um volume localmente afixado e restaurá-lo.

As perguntas e respostas são organizadas nas seguintes categorias

* Criação de um volume localmente afixado
* Backup de um volume localmente afixado
* Conversão de um volume em camadas em um volume localmente afixado
* Restauração de um volume localmente afixado
* Failover de um volume localmente afixado

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Perguntas sobre a criação de um volume localmente afixado
**P.** Qual é o tamanho máximo de um volume localmente afixado que posso criar em dispositivos da série 8000?

**R** Em dispositivos que executam o StorSimple série 8000 Atualização 3.0, você pode provisionar localmente volumes fixos de até 8,5 TB ou volumes em camadas de até 200 TB no dispositivo 8100. No dispositivo 8600, que é maior, você pode provisionar volumes localmente afixados de até 22,5 TB ou volumes em camadas de até 500 TB.

**P.** Atualizei recentemente o dispositivo 8100 para a Atualização 3.0 e, quando tento criar um volume fixado localmente, o tamanho máximo disponível é de apenas 6 TB, não de 8,5 TB. Por que não consigo criar um volume de 8,5 TB?

**R** Se seu dispositivos estiver executando a Atualização 3.0, você poderá provisionar volumes fixados localmente de até 8,5 TB OU volumes em camada de até 200 TB no dispositivo 8100. Se o dispositivo já tiver volumes em camadas, o espaço disponível para a criação de um volume localmente afixado será proporcionalmente menor do que esse limite máximo. Por exemplo, se cerca de 106 TB de volumes em camadas já tiverem sido provisionados no dispositivo 8100 (o que representa metade da capacidade em camadas), o tamanho máximo de um volume local que você poderá criar no dispositivo 8100 será reduzido de forma correspondente para 4 TB (cerca de metade da capacidade máxima do volume fixado localmente).

Como algum espaço no dispositivo local é usado para hospedar o conjunto de trabalho de volumes em camadas, o espaço disponível para a criação de um volume localmente afixado será reduzido se o dispositivo tiver volumes em camadas. Por outro lado, a criação de um volume localmente afixado reduz proporcionalmente o espaço disponível para volumes em camadas. A tabela a seguir resume a capacidade em camadas disponível nos dispositivos 8100 e 8600 quando volumes localmente fixos são criados.

#### <a name="update-30"></a>Atualização 3.0 
| Capacidade provisionada para volumes localmente afixados | Capacidade disponível para ser provisionada para volumes em camadas - 8100 | Capacidade disponível para ser provisionada para volumes em camadas - 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176,5 TB |477,8 TB |
| 4 TB |105,9 TB |411,1 TB |
| 8,5 TB |0 TB |311,1 TB |
| 10 TB |ND |277,8 TB |
| 15 TB |ND |166,7 TB |
| 22,5 TB |ND |0 TB |

**P.** Por que a criação de um volume localmente afixado é uma operação demorada?

**A.** Os volumes localmente afixados são provisionados de forma densa. Para criar espaço nas camadas locais do dispositivo, alguns dados de volumes em camadas existentes podem ser enviados para a nuvem durante o processo de provisionamento. Já que isso depende do tamanho do volume que está sendo provisionado, nos dados existentes no dispositivo e na largura de banda disponível para a nuvem, o tempo necessário para criar um volume local pode consistir em várias horas.

**P.** Quanto tempo leva para criar um volume localmente afixado?

**A.** Como volumes localmente afixados são provisionados de forma densa, alguns dados existentes de volumes em camadas podem ser enviados para a nuvem durante o processo de provisionamento. Portanto, o tempo necessário para criar um volume localmente afixado depende de vários fatores, como o tamanho do volume, os dados no dispositivo e a largura de banda disponível. Em um dispositivo recém-instalado que não tem volumes, o tempo para criar um volume localmente afixado é de cerca de 10 minutos por terabyte de dados. No entanto, a criação de volumes locais pode levar várias horas, com base nos fatores explicados acima, em um dispositivo que esteja em uso.

**P.** Desejo criar um volume localmente afixado. Existem práticas recomendadas que preciso conhecer?

**A.** Os volumes localmente afixados são adequados para cargas de trabalho que exigem garantias locais de dados em todos os momentos e são sensíveis às latências da nuvem. Ao considerar o uso de volumes locais para qualquer uma de suas cargas de trabalho, lembre-se do seguinte:

* Volumes localmente afixados são provisionados de forma densa e a criação de volumes locais tem impacto sobre o espaço disponível para volumes em camadas. Portanto, sugerimos que você comece com volumes menores e escale-os verticalmente à medida que sua necessidade de armazenamento aumentar.
* O provisionamento de volumes locais é uma operação demorada que pode envolver o envio de dados existentes de volumes em camadas para a nuvem. Como resultado, pode ocorrer desempenho reduzido nesses volumes.
* O provisionamento de volumes locais é uma operação demorada. O tempo real envolvidos depende de vários fatores: o tamanho do volume que está sendo provisionado, os dados no dispositivo e a largura de banda disponível. Se você não tiver feito backup dos volumes existentes para a nuvem, a criação do volume será mais lenta. Sugerimos que você crie instantâneos em nuvem dos volumes existentes antes de provisionar um volume local.
* É possível converter os volumes em camadas existentes em volumes fixados localmente e essa conversão envolve o provisionamento de espaço no dispositivo para o volume fixado localmente resultante (além da remoção dos dados em camadas da nuvem, caso existam). Novamente, essa é uma operação demorada que depende dos fatores discutidos anteriormente. Sugerimos que você faça o backup dos volumes existentes antes da conversão, pois o processo será ainda mais lento se não for feito o backup dos volumes existentes. O dispositivo também poderá ter desempenho reduzido durante esse processo.

Mais informações sobre como [criar um volume localmente afixado](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**P.** Posso criar vários volumes localmente afixados ao mesmo tempo?

**A.** Sim, mas os trabalhos de criação e expansão de volumes localmente afixados são processados em sequência.

Os volumes localmente afixados são provisionados de forma densa e isso exige a criação de espaço local no dispositivo (o que pode fazer com que dados existentes de volumes em camadas sejam enviados para a nuvem durante o processo de provisionamento). Portanto, se um trabalho de provisionamento estiver em andamento, outros trabalhos de criação de volumes locais serão enfileirados até o trabalho ser concluído.

Da mesma forma, se um volume local existente estiver sendo expandido ou se um volume em camadas estiver sendo convertido em um volume localmente afixado, a criação de um novo volume localmente afixado será enfileirada até que o trabalho anterior seja concluído. A expansão do tamanho de um volume localmente afixado envolve a expansão do espaço local existente para esse volume. A conversão de um volume em camadas em um volume localmente afixado também envolve a criação de espaço local para o volume localmente afixado resultante. Em ambas essas operações, a criação ou a expansão do espaço local é um trabalho de longa execução.

Você pode exibir esses trabalhos na folha **Trabalhos** do serviço do Gerenciador de Dispositivos do StorSimple. O trabalho que está sendo processado ativamente é atualizado de forma contínua para refletir o progresso do provisionamento de espaço. Os trabalhos de volume localmente fixados restantes são marcados como em execução, mas seu progresso é interrompido e eles são selecionados na ordem em que foram colocados na fila.

**P.** Exclui um volume localmente afixado. Por que não vejo o espaço recuperado refletido no espaço disponível ao tentar criar um novo volume?

**A.** Se você excluir um volume fixado local, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O Serviço do Gerenciador de Dispositivos do StorSimple atualiza o espaço local disponível aproximadamente a cada hora. Sugerimos que você aguarde por uma hora antes de tentar criar o novo volume.

**P.** Os volumes localmente afixados são compatíveis com o dispositivo de nuvem?

**A.** Não há suporte para volumes localmente afixados no dispositivo de nuvem (dispositivos 8010 e 8020, conhecidos anteriormente como o dispositivo virtual StorSimple).

**P.** Posso usar os cmdlets do Azure PowerShell para criar e gerenciar volumes localmente afixados?

**A.** Não, você não pode criar volumes localmente afixados por meio de cmdlets do Azure PowerShell (qualquer volume criado por meio do Azure PowerShell é em camadas). Também sugerimos que você não use os cmdlets do Azure PowerShell para modificar as propriedades de um volume localmente afixado, pois isso terá o efeito indesejado de modificar o tipo de volume para em camadas.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Perguntas sobre o backup de um volume localmente afixado
**P.** Há suporte a instantâneos locais de volumes localmente afixados?

**A.** Sim, você pode criar instantâneos locais de volumes localmente afixados. No entanto, é altamente recomendável fazer o backup regular dos volumes localmente afixados com instantâneos de nuvem para garantir que os dados estejam protegidos na eventualidade de um desastre.

Observe que os instantâneos locais de volumes fixados localmente também podem ser dispostos em camadas na nuvem e não há garantiam que eles permanecerão na camada local do dispositivo.

**P.** Há diretrizes para o gerenciamento de instantâneos locais para volumes localmente afixados?

**A.** Instantâneos locais frequentes e uma alta taxa de variação de dados no volume localmente afixado podem fazer com que o espaço local do dispositivo seja consumido rapidamente e dados dos volumes em camadas sejam enviados para a nuvem. Portanto, sugerimos que você minimize o número de instantâneos locais.

**P.** Recebi um alerta informando que meus instantâneos locais de volumes localmente afixados podem ser invalidados. Quando isso acontece?

**A.** Os instantâneos locais frequentes e uma alta taxa de variação de dados no volume localmente afixado podem fazer com que o espaço local no dispositivo seja consumido rapidamente. Se as camadas locais do dispositivo forem usadas intensamente, uma interrupção de nuvem estendida poderá fazer com que o dispositivo fique cheio e as gravações recebidas no volume poderão causar a invalidação dos instantâneos (como não há espaço para atualizar os instantâneos para referir-se aos blocos de dados mais antigos que foram substituídos). Nessa situação, as gravações no volume continuarão a ser feitas, mas os instantâneos locais poderão ser inválidos. Não há impacto sobre os instantâneos de nuvem existentes.

O aviso de alerta é para notificá-lo de que essa situação pode ocorrer e garantir que você a aborde oportunamente, examinando as agendas de instantâneos locais para criar instantâneos locais menos frequentes ou excluindo instantâneos locais mais antigos que não são mais necessários.

Se os instantâneos locais forem invalidados, você receberá um alerta de informação notificando-o de que os instantâneos locais da política de backup específica foram invalidados, juntamente com a lista de carimbos de data/hora dos instantâneos locais que foram invalidados. Esses instantâneos serão excluídos automaticamente e não será mais possível exibi-los na folha **Catálogos de Backup** no Portal do Azure.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Perguntas sobre como converter um volume em camadas em um volume localmente afixado
**P.** Tenho observado uma certa lentidão no dispositivo durante a conversão de um volume em camadas em um volume localmente afixado. Por que isso está acontecendo?

**A.** O processo de conversão envolve duas etapas:

1. Provisionamento de espaço no dispositivo para o volume localmente afixado que será convertido em breve.
2. Download de dados em camadas da nuvem para assegurar que haja garantias locais.

Essas duas etapas são operações de longa execução que dependem do tamanho do volume que está sendo convertido, dos dados no dispositivo e da largura de banda disponível. Como alguns dados dos volumes em camadas existentes podem ser colocados na nuvem como parte do processo de provisionamento, o dispositivo poderá ter o desempenho reduzido durante esse período. Além disso, o processo de conversão poderá ser mais lento se:

* Se o backup na nuvem dos volumes existentes não tiver sido feito. Portanto, sugerimos que você faça backup dos volumes antes de iniciar uma conversão.
* Políticas de limitação de largura de banda foram aplicadas, o que poderá restringir a largura de banda disponível para a nuvem. Portanto, recomendamos que você tenha uma ou mais conexões de 40 Mbps dedicadas para a nuvem.
* O processo de conversão pode levar várias horas devido aos vários fatores explicados anteriormente. Portanto, sugerimos que você execute essa operação fora dos horários de pico ou no fim de semana para evitar o impacto sobre os consumidores finais.

Mais informações sobre como [converter um volume em camadas em um volume localmente afixado](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**P.** Posso cancelar a operação de conversão de volume?

**A.** Não, você não pode cancelar a operação de conversão depois que ela é iniciada. Conforme discutido na pergunta anterior, tenha em mente os possíveis problemas de desempenho que você poderá encontrar durante o processo e, ao planejar a conversão, siga as práticas recomendadas listadas acima.

**P.** O que acontecerá com o volume se a operação de conversão falhar?

**A.** A conversão de volume pode falhar devido a problemas de conectividade de nuvem. O dispositivo pode eventualmente parar o processo de conversão após uma série de tentativas malsucedidas de obter dados em camadas da nuvem. Nesse cenário, o tipo de volume continuará a ser o tipo de volume de origem anterior à conversão e:

* Será gerado um alerta crítico para notificá-lo da falha de conversão do volume. Mais informações sobre [alertas relacionados a volumes localmente afixados](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Se você estiver convertendo um volume em camadas em um volume localmente afixado, o volume continuará a exibir propriedades de um volume em camadas, pois ainda pode haver dados residentes na nuvem. Sugerimos que você resolva os problemas de conectividade e repita a operação de conversão.
* Da mesma forma, quando a conversão de um volume localmente afixado em um volume em camadas falha, embora o volume seja marcado como um volume localmente afixado, ele funcionará como um volume em camadas (porque dados podem ter sido colocados na nuvem). No entanto, ele continuará a ocupar espaço nas camadas locais do dispositivo. Esse espaço não estará disponível para outros volumes localmente afixados. Sugerimos que você repita a operação para garantir que a conversão de volume seja concluída e que o espaço local no dispositivo possa ser recuperado.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Perguntas sobre como restaurar um volume localmente afixado
**P.** Os volumes localmente afixados são restaurados instantaneamente?

**A.** Sim, os volumes localmente afixados são restaurados instantaneamente. Assim que as informações de metadados do volume são obtidas da nuvem como parte da operação de restauração, o volume é colocado online e pode ser acessado pelo host. No entanto, garantias locais para os dados do volume não estarão presentes até que todos os dados sejam baixado da nuvem. Pode ocorrer redução do desempenho nos volumes durante a restauração.

**P.** Quanto tempo leva para restaurar um volume localmente afixado?

**A.** Volumes localmente afixados são restaurados instantaneamente e colocados online assim que as informações de metadados do volume são recuperadas da nuvem, enquanto os dados do volume continuam a ser baixados em segundo plano. Essa última parte da operação de restauração (obter de volta as garantias locais para os dados do volume) é uma operação demorada e pode levar várias horas para que todos os dados estejam disponíveis localmente de novo. O tempo necessário para concluir esse mesmo processo depende de vários fatores, como o tamanho do volume que está sendo restaurado e a largura de banda disponível. Se o volume original que está sendo restaurado foi excluído, levará mais tempo para criar o espaço local no dispositivo como parte da operação de restauração.

**P.** Preciso restaurar meu volume localmente afixado existente para um instantâneo mais antigo (obtido quando o volume era em camadas). Nesse caso, o volume será restaurado em camadas?

**A.** Não, o volume será restaurado como um volume localmente afixado. Embora o instantâneo seja de quando o volume era em camadas, ao restaurar volumes existentes, o StorSimple sempre usa o tipo de volume no disco da forma como ele existe atualmente.

**P.** Estendi meu volume localmente afixado recentemente, mas agora preciso restaurar os dados para uma hora em que o volume tinha tamanho menor. A restauração redimensionará o volume atual e precisarei estender o tamanho do volume após a restauração ser concluída?

**A.** Sim, a restauração redimensionará o volume e você precisará estender o tamanho do volume após a restauração ser concluída.

**P.** Posso alterar o tipo de um volume durante a restauração?

**R.**Não, você não pode alterar o tipo de volume durante a restauração.

* Os volumes excluídos são restaurados como o tipo armazenado no instantâneo.
* Os volumes existentes são restaurados com base em seu tipo atual, independentemente do tipo armazenado no instantâneo (confira as duas perguntas anteriores).

**P.** Preciso restaurar um volume localmente afixado, mas escolhi um ponto incorreto no instantâneo de tempo. Posso cancelar a operação de restauração atual?

**A.** Sim, você pode cancelar uma operação de restauração em andamento. O estado do volume será revertido para o estado do início da restauração. No entanto, as gravações feitas no volume enquanto a restauração estava em andamento serão perdidas.

**P.** Comecei uma operação de restauração em um de meus volumes localmente afixados e agora há um instantâneo no catálogo de lista de pendências que não me lembro de ter criado. Para que isso é usado?

**A.** Esse é o instantâneo temporário criado antes da operação de restauração que é usado para a reversão, caso a restauração seja cancelada ou falhe. Não exclua esse instantâneo. Ele será automaticamente excluído quando a restauração for concluída. Isso poderá ocorrer se o trabalho de restauração tiver apenas volumes fixados localmente ou uma combinação de volumes fixados e em camadas. Se o trabalho de restauração incluir apenas volumes em camadas, esse comportamento não ocorrerá.

**P.** Posso clonar um volume localmente afixado?

**A.** Sim, pode. No entanto, o volume localmente afixado será clonado como um volume em camadas por padrão. Mais informações sobre como [clonar um volume localmente afixado](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Perguntas sobre como fazer o failover de um volume localmente afixado
**P.** Preciso fazer failover de um dispositivo para outro dispositivo físico. O failover será feito nos volumes localmente afixados como localmente afixados ou em camadas?

**A.** Os volumes fixados localmente passam por failover fixado localmente se o dispositivo de destino executar um StorSimple da série 8000 com atualização 3 ou superior.

Mais informações sobre [o failover e a DR de volumes fixos localmente entre versões](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**P.** Os volumes localmente afixados são restaurados instantaneamente durante a RD (recuperação de desastre)?

**A.** Sim, os volumes localmente afixados são restaurados instantaneamente durante o failover. Assim que as informações de metadados do volume são obtidas da nuvem como parte da operação de failover, o volume é colocado online no dispositivo de destino e pode ser acessado pelo host. Enquanto isso, os dados do volume continuarão a ser baixados em segundo plano e o desempenho poderá ser reduzido nos volumes durante o failover.

**P.** Vejo que o trabalho de failover foi concluído. Como posso acompanhar o andamento do volume localmente afixado que está sendo restaurado no dispositivo de destino?

**A.** Durante uma operação de failover, o trabalho de failover é marcado como concluído depois que todos os volumes no conjunto de failover tiverem sido instantaneamente restaurados e colocados online no dispositivo de destino. Isso inclui todos os volumes localmente afixados que podem ter sido submetidos a failover. No entanto, as garantias locais dos dados só estarão disponíveis quando todos os dados do volume forem baixados. Você pode acompanhar o andamento de cada volume localmente afixado que falhou monitorando os trabalhos de restauração correspondentes que são criados como parte do failover. Esses trabalhos de restauração individuais só serão criados para volumes localmente afixados.

**P.** Posso alterar o tipo de um volume durante o failover?

**A.** Não, você não pode alterar o tipo de volume durante um failover. Se você estiver fazendo o failover para outro dispositivo físico que execute o StorSimple da série 8000 com atualização 3, o failover dos volumes será feito com base no tipo de volume armazenado no instantâneo.

**P.** Posso fazer o failover em um contêiner de volume com volumes localmente afixados para o dispositivo de nuvem?

**A.** Sim, pode. Os volumes localmente afixados serão submetidos a failover como volumes em camadas. Mais informações sobre [o failover e a DR de volumes fixos localmente entre versões](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

