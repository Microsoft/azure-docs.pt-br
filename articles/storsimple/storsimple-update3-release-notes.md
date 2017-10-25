---
title: "Notas de versão da Atualização 3 para o StorSimple 8000 Series | Microsoft Docs"
description: "Descreve os novos recursos, problemas e soluções alternativas da Atualização 3 para o StorSimple 8000 Series."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/25/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b8b230904e1e079417c3b39bbc281bc3a87668a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Notas de versão da Atualização 3 para seu dispositivo StorSimple série 8000

## <a name="overview"></a>Visão geral
As notas de versão a seguir descrevem os novos recursos e identificam os problemas críticos abertos da Atualização 3 para o StorSimple 8000 Series. Elas também contêm uma lista das atualizações de software do StorSimple incluídas nesta versão. 

A Atualização 3 pode ser aplicada a qualquer dispositivo StorSimple que executa a Versão (GA) ou a Atualização 0.1 à 2.2. A versão do dispositivo associada à Atualização 3 é 6.3.9600.17759.

Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução do StorSimple.

> [!IMPORTANT]
> * A Atualização 3 tem atualizações de software de dispositivo, firmware e driver LSI, Storport e Spaceport. São necessárias cerca de 1,5 a 2 horas para instalar essa atualização. 
> * Para novas versões, talvez você não veja atualizações imediatamente porque fazemos uma distribuição em fases das atualizações. Aguarde alguns dias e procure atualizações novamente, uma vez que elas serão disponibilizadas em breve.
> 
> 

## <a name="whats-new-in-update-3"></a>Novidades na Atualização 3
Veja a seguir as principais melhorias e correções de bugs feitas na Atualização 3.

* **Alterações de reclamação de espaço automatizadas** – a partir da Atualização 3, os algoritmos de reclamação de espaço são executados no controlador em espera do sistema, resultando em uma execução mais rápida. Para obter mais informações sobre as portas que são necessárias para trabalhar com a recuperação de espaço, consulte os [requisitos de rede do StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Aprimoramentos de desempenho** – a Atualização 3 melhorou o desempenho de leitura/gravação para a nuvem.
* **Melhorias relacionadas à migração** – nesta versão, várias correções de bugs e aprimoramentos foram feitos para o recurso de migração de dispositivos da série 5000/7000 para dispositivos da série 8000. Para saber mais sobre como usar o recurso de migração, vá para [Migração do dispositivo série 5000/7000 para dispositivos da série 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Correções relacionadas a monitoramento** – nesta versão, bugs relacionados a gráficos de monitoramento, painel de serviço e painel de dispositivo foram corrigidos.

## <a name="issues-fixed-in-update-3"></a>Problemas corrigidos na Atualização 3
A tabela a seguir fornece um resumo dos problemas que foram corrigidos na Atualização 3.    

| Não | Recurso | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Migração de dados do lado do host |Na versão anterior, o Dispositivo de Nuvem do StorSimple ficaria offline durante a migração de dados do lado do host. Esse problema foi corrigido nesta versão. |Não |Sim |
| 2 |Volumes afixados localmente |Na versão anterior, havia problemas relacionados a falhas de E/S, falhas de conversão de volume e falhas de caminho de dados para volumes fixos localmente. Esses problemas foram causados por raiz e corrigidos nesta versão. |Sim |Não |
| 3 |Monitoramento |Havia vários problemas relacionados a unidades de emissão de relatórios e monitoramento, bem como gráficos de painel de dispositivos em que informações incorretas foram exibidas para volumes fixos localmente. Esses problemas foram corrigidos nesta versão. |Sim |Não |
| 4 |Gravações pesadas E/S |Ao usar o StorSimple para cargas de trabalho que envolvem gravações pesadas, o usuário encontraria um bug não muito frequente em que o conjunto de trabalho estava sendo colocado em camadas na nuvem. Esse bug foi corrigido nesta versão. |Sim |Sim |
| 5 |Backup |Em certos casos raros, nas versões anteriores do software, quando o usuário executava um backup de um clone remoto, ele encontraria erros de nuvem e a operação falharia. Nesta versão, o problema é corrigido e a operação é concluída com êxito. |Sim |Sim |
| 6 |Política do backup |Em certos casos raros, nas versões anteriores do software, havia um bug relacionado à exclusão da política de backup. Esse problema foi corrigido nesta versão. |Sim |Sim |

## <a name="known-issues-in-update-3"></a>Problemas conhecidos na Atualização 3
A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.

| Nº | Recurso | Problema | Comentários/solução alternativa | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Quorum de disco |Em casos raros, se a maioria dos discos no invólucro de EBOD de um dispositivo 8600 for desconectada, fazendo com que não haja quórum de disco, o pool de armazenamento ficará offline. Permanecerá offline, mesmo que os discos sejam reconectados. |Você precisará reiniciar o dispositivo. Se o problema persistir, entre em contato com o Suporte da Microsoft para as próximas etapas. |Sim |Não |
| 2 |ID de controlador incorreta |Quando a substituição do controlador é executada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó par, a ID do controlador pode ser exibida inicialmente como a ID do controlador de pares. Em casos raros, esse comportamento pode ser percebido após uma reinicialização do sistema. |Nenhuma ação do usuário é necessária. Esta situação se resolverá depois que a substituição do controlador for concluída. |Sim |Não |
| 3 |Contas de armazenamento |Usar o serviço de Armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso levará a uma situação na qual os dados do usuário não podem ser recuperados. | |Sim |Sim |
| 4 |Failover de dispositivo |Não há suporte para vários failovers de um contêiner de volume do mesmo dispositivo de origem para diferentes dispositivos de destino. O failover de um único dispositivo inativo para vários dispositivos fará com que os contêineres de volume no primeiro dispositivo com failover percam a propriedade dos dados. Após o failover, esses contêineres de volume serão exibidos ou se comportarão de maneira diferente quando forem exibidos no Portal clássico do Azure. | |Sim |Não |
| 5 |Instalação |Durante o Adaptador StorSimple para instalação do SharePoint, você precisa fornecer um IP do dispositivo para que a instalação seja concluída com êxito. | |Sim |Não |
| 6 |Proxy Web |Se a configuração de proxy Web tiver HTTPS como o protocolo especificado, a comunicação de serviço do dispositivo será afetada e o dispositivo ficará offline. Pacotes de suporte também serão gerados no processo, consumindo recursos significativos em seu dispositivo. |Verifique se a URL do proxy Web possui HTTP como o protocolo especificado. Para obter mais informações, visite [Configurar proxy da Web para seu dispositivo](storsimple-configure-web-proxy.md). |Sim |Não |
| 7 |Proxy Web |Ao configurar e habilitar o proxy Web em um dispositivo registrado, você precisará reiniciar o controlador ativo em seu dispositivo. | |Sim |Não |
| 8 |Latência de nuvem alta e alta carga de trabalho de E/S |Quando seu dispositivo StorSimple encontra uma combinação de latências de nuvem muito altas (ordem de segundos) e alta carga de trabalho de E/S, os volumes do dispositivo entram em um estado degradado e as E/Ss podem falhar com o erro "o dispositivo não está pronto". |Você precisará reiniciar os controladores de dispositivo manualmente ou executar um failover do dispositivo para se recuperar dessa situação. |Sim |Não |
| 9 |Azure PowerShell |Quando você usa o cmdlet StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** para selecionar o primeiro objeto para que possa criar um novo objeto **VolumeContainer**, o cmdlet retorna todos os objetos. |Coloque o cmdlet entre parênteses da seguinte maneira: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Sim |Sim |
| 10 |Migração |Quando contêineres de vários volumes forem passados para a migração, o ETA do backup mais recente será preciso apenas para o contêiner do primeiro volume. Além disso, a migração paralela será iniciada depois que os primeiros 4 backups no primeiro contêiner de volume forem migrados. |É recomendável que você migre um contêiner de volume por vez. |Sim |Não |
| 11 |Migração |Após a restauração, os volumes não são adicionados à política de backup ou ao grupo de discos virtuais. |Você terá que adicionar esses volumes a uma política de backup para criar backups. |Sim |Sim |
| 12 |Migração |Depois que a migração for concluída, o dispositivo série 5000/7000 não deverá acessar os contêineres de dados migrados. |É recomendável que você exclua os contêineres de dados migrados quando a migração estiver concluída e confirmada. |Sim |Não |
| 13 |Clonagem e recuperação de desastre |Um dispositivo StorSimple executando a Atualização 1 não pode clonar ou executar a recuperação de desastre em um dispositivo que executa o software anterior à Atualização 1. |Você precisará atualizar o dispositivo de destino para a Atualização 1 para permitir essas operações |Sim |Sim |
| 14 |Migração |O backup de configuração para a migração poderá falhar em um dispositivo da série 5000-7000 quando houver grupos de volumes sem volumes associados. |Exclua todos os grupos de volumes vazios sem volumes associados e repita o backup de configuração. |Sim |Não |
| 15 |Cmdlets do Azure PowerShell e volumes fixados localmente |Não é possível criar um volume fixado localmente por meio de cmdlets do Azure PowerShell. (Qualquer volume criado por meio do Azure PowerShell será organizado em camadas.) Além disso, não use os cmdlets do Azure PowerShell para modificar as propriedades de um volume fixado localmente, pois isso terá o efeito indesejado de modificar o tipo de volume para um volume em camadas. |Sempre use o serviço do StorSimple Manager para configurar e modificar os volumes fixados localmente.  |Sim |Não |
| 16 |Espaço disponível para volumes fixados localmente |Se você excluir um volume fixado local, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O serviço do StorSimple Manager atualiza o espaço local disponível aproximadamente a cada hora. |Aguarde uma hora antes de tentar criar o novo volume. |Sim |Não |
| 17 |Volumes afixados localmente |O trabalho de restauração expõe o backup do instantâneo temporário no Catálogo de Backup, mas apenas pelo tempo que durar o trabalho de restauração. Além disso, ele expõe um grupo de discos virtuais com o prefixo **tmpCollection** na página **Backup Policies**, mas apenas durante o trabalho de restauração. |Isso poderá ocorrer se o trabalho de restauração tiver apenas volumes fixados localmente ou uma combinação de volumes fixados e em camadas. Se o trabalho de restauração incluir apenas volumes em camadas, esse comportamento não ocorrerá. Nenhuma intervenção do usuário é necessária. |Sim |Não |
| 18 |Volumes afixados localmente |Se você cancelar um trabalho de restauração e um failover do controlador ocorrer logo em seguida, o trabalho de restauração mostrará **Falha** em vez de **Cancelado**. Se um trabalho de restauração falhar e um failover do controlador ocorrer logo em seguida, o trabalho de restauração mostrará **Cancelado** em vez de **Falha**. |Isso poderá ocorrer se o trabalho de restauração tiver apenas volumes fixados localmente ou uma combinação de volumes fixados e em camadas. Se o trabalho de restauração incluir apenas volumes em camadas, esse comportamento não ocorrerá. Nenhuma intervenção do usuário é necessária. |Sim |Não |
| 19 |Volumes afixados localmente |Se você cancelar um trabalho de restauração ou se uma restauração falhar e ocorrer um failover do controlador, outro trabalho de restauração aparecerá na página **Trabalhos** . |Isso poderá ocorrer se o trabalho de restauração tiver apenas volumes fixados localmente ou uma combinação de volumes fixados e em camadas. Se o trabalho de restauração incluir apenas volumes em camadas, esse comportamento não ocorrerá. Nenhuma intervenção do usuário é necessária. |Sim |Não |
| 20 |Volumes afixados localmente |Se você tentar converter um volume em camadas (criado e clonado com a atualização 1.2 ou anterior) em um volume fixo local e o dispositivo estiver ficando sem espaço ou uma houver interrupção na nuvem, os clones podem estar corrompidos. |Esse problema ocorre apenas com volumes que foram criados e clonados com software anterior à Atualização 2.1. Isso deve ser um cenário incomum. | | |
| 21 |Conversão de volume |Não atualize os ACRs conectados a um volume enquanto a conversão de volume estiver em andamento (em camadas para fixados localmente ou vice-versa). Atualizar os ACRs pode resultar em dados corrompidos. |Se necessário, atualize os ACRs antes da conversão de volume e não faça mais atualizações da ACR enquanto a conversão estiver em andamento. | | |
| 22 |Atualizações |Ao aplicar a Atualização 3, a página **Manutenção** no portal clássico do Azure exibirá a mensagem a seguir relacionada à Atualização 2 - "O StorSimple 8000 series Atualização 2 inclui a capacidade de a Microsoft proativamente coletar informações de log do seu dispositivo quando detectamos potenciais problemas". Isso é confuso, pois indica que o dispositivo está sendo atualizado para a Atualização 2. Depois que o dispositivo tiver sido atualizado com êxito para a Atualização 3, essa mensagem desaparecerá. |Esse comportamento será corrigido em uma atualização futura. |Sim |Não |

## <a name="controller-and-firmware-updates-in-update-3"></a>Atualizações de controlador e firmware na Atualização 3
Esta versão tem atualizações de firmware e driver LSI. Para obter mais informações sobre como instalar o driver LSI e as atualizações de firmware, consulte [Instalar a Atualização 3](storsimple-install-update-3.md) no dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-3"></a>Atualizações do dispositivo virtual na Atualização 3
Esta atualização não pode ser aplicada ao Dispositivo de Nuvem do StorSimple (também conhecido como o dispositivo virtual). Novos dispositivos virtuais precisarão ser criados. 

## <a name="next-step"></a>Próxima etapa
Saiba como [instalar a Atualização 3](storsimple-install-update-3.md) no dispositivo StorSimple.

