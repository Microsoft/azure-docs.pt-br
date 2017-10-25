---
title: "Notas de versão da Atualização 2.2 para o StorSimple 8000 Series | Microsoft Docs"
description: "Descreve os novos recursos, problemas e as soluções alternativas para a Atualização 2.2 do StorSimple Série 8000."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2016
ms.author: alkohli
ms.openlocfilehash: 3793df974175a5c41b83800f73748c52c8223572
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>Notas de versão da Atualização 2.2 do StorSimple Série 8000
## <a name="overview"></a>Visão geral
As notas de versão a seguir descrevem os novos recursos e identificam os problemas críticos em aberto da Atualização 2.2 do StorSimple Série 8000. Elas também contêm uma lista das atualizações de software do StorSimple incluídas nesta versão. 

A Atualização 2.2 pode ser aplicada a qualquer dispositivo StorSimple que executa a Versão (GA) ou a Atualização 0.1 à 2.1. A versão do dispositivo associada à Atualização 2.2 é 6.3.9600.17708.

Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução do StorSimple.

> [!IMPORTANT]
> * A Atualização 2.2 traz apenas as atualizações de software. São necessárias cerca de 1,5 a 2 horas para instalar essa atualização. 
> * Caso você esteja executando a Atualização 2.1, recomendamos aplicar a Atualização 2.2 assim que possível.
> * Para novas versões, talvez você não veja atualizações imediatamente porque fazemos uma distribuição em fases das atualizações. Aguarde alguns dias e procure atualizações novamente, uma vez que elas serão disponibilizadas em breve.
> 
> 

## <a name="whats-new-in-update-22"></a>Novidades na Atualização 2.2
Veja a seguir as principais melhorias feitas na Atualização 2.2.

* **Otimização de recuperação automatizada de espaço** – Quando os dados são excluídos em volumes com provisionamento dinâmico, os blocos de armazenamento não utilizados precisam ser recuperados. Esta versão aprimorou o processo de recuperação de espaço da nuvem, o que resultou na disponibilização mais rápida do espaço não utilizado, em comparação com versões anteriores.
* **Melhorias de desempenho de instantâneo** – A Atualização 2.2 melhorou o tempo de processamento de um instantâneo de nuvem em alguns cenários, nos quais grandes volumes são usados e em que há pouca, ou nenhuma, variação nos dados. Um cenário que se beneficiaria desse aprimoramento seria o de volumes de arquivamento.
* **Proteção da coleta do pacote de Suporte** – Houve melhorias na maneira como o pacote de Suporte é coletado e carregado nesta versão. 
* **Melhorias de confiabilidade da Atualização** – Esta versão traz correções de bug que resultam em uma melhoria de confiabilidade da Atualização.

## <a name="issues-fixed-in-update-22"></a>Problemas corrigidos na Atualização 2.2
A tabela a seguir fornece um resumo dos problemas que foram corrigidos nas Atualizações 2.2 e 2.1.    

| Não | Recurso | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Desempenho do host |Na versão anterior, os problemas de desempenho do host foram observados durante a criação de um volume afixado localmente e durante a conversão de um volume em camadas para um volume afixado localmente. Esses problemas foram corrigidos nesta versão, resultando em uma melhoria no desempenho do host durante os procedimentos de criação e conversão de volume. |Sim |Não |
| 2 |Volumes afixados localmente |Em casos raros, o sistema poderia falhar durante a criação de um volume afixado localmente. Este bug foi corrigido nesta atualização. |Sim |Não |
| 3 |Disposição em camadas |Ocorreram falhas esporádicas quando os metadados dos dispositivos de nuvem do StorSimple (8010 e 8020) foram dispostos em camadas na nuvem. Esse problema foi corrigido nesta versão. |Não |Sim |
| 4 |Criação de instantâneos |Houve problemas relacionados à criação de instantâneos incrementais em cenários com grandes volumes e pouca, ou nenhuma, rotatividade de dados. Esses problemas foram corrigidos nesta versão. |Sim |Sim |
| 5 |Autenticação Openstack |Ao usar o Openstack como o provedor de serviços de nuvem, o usuário enfrentaria um bug raro relacionado à autenticação, no qual o analisador JSON resultava em uma falha. Esse bug foi corrigido nesta versão. |Sim |Não |
| 6 |Cópia do host |Nas versões anteriores do software, um bug raro relacionado ao intervalo de ODX foi visto durante a cópia dos dados de um volume para outro. Isso resultaria em um failover de controlador e o sistema potencialmente poderia entrar no modo de recuperação. Esse bug foi corrigido nesta versão. |Sim |Não |
| 7 |WMI (Instrumentação de Gerenciamento do Windows) |Nas versões anteriores do software, havia várias instâncias de falha de proxy Web com a exceção “Falha ao carregar o provedor <ManagementException>”. Esse bug foi atribuído a um vazamento de memória da WMI, e agora está corrigido. |Sim |Não |
| 8 |Atualizar |Em determinados casos raros, nas versões anteriores do software, o usuário recebia um "CisPowershellHcsscripterror" ao tentar verificar ou instalar as atualizações. Esse problema foi corrigido nesta versão. |Sim |Sim |
| 9 |Pacote de suporte |Nesta versão, houve melhorias na forma como o pacote de suporte é coletado e carregado. |Sim |Sim |

## <a name="known-issues-in-update-22"></a>Problemas conhecidos na Atualização 2.2
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
| 15 |Cmdlets do Azure PowerShell e volumes fixados localmente |Não é possível criar um volume fixado localmente por meio de cmdlets do Azure PowerShell. (Qualquer volume criado por meio do Azure PowerShell será organizado em camadas.) |Sempre use o serviço do StorSimple Manager para configurar os volumes fixados localmente. |Sim |Não |
| 16 |Espaço disponível para volumes fixados localmente |Se você excluir um volume fixado local, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O serviço do StorSimple Manager atualiza o espaço local disponível aproximadamente a cada hora. |Aguarde uma hora antes de tentar criar o novo volume. |Sim |Não |
| 17 |Volumes afixados localmente |O trabalho de restauração expõe o backup do instantâneo temporário no Catálogo de Backup, mas apenas pelo tempo que durar o trabalho de restauração. Além disso, ele expõe um grupo de discos virtuais com o prefixo **tmpCollection** na página **Backup Policies**, mas apenas durante o trabalho de restauração. |Isso poderá ocorrer se o trabalho de restauração tiver apenas volumes fixados localmente ou uma combinação de volumes fixados e em camadas. Se o trabalho de restauração incluir apenas volumes em camadas, esse comportamento não ocorrerá. Nenhuma intervenção do usuário é necessária. |Sim |Não |
| 18 |Volumes afixados localmente |Se você cancelar um trabalho de restauração e um failover do controlador ocorrer logo em seguida, o trabalho de restauração mostrará **Falha** em vez de **Cancelado**. Se um trabalho de restauração falhar e um failover do controlador ocorrer logo em seguida, o trabalho de restauração mostrará **Cancelado** em vez de **Falha**. |Isso poderá ocorrer se o trabalho de restauração tiver apenas volumes fixados localmente ou uma combinação de volumes fixados e em camadas. Se o trabalho de restauração incluir apenas volumes em camadas, esse comportamento não ocorrerá. Nenhuma intervenção do usuário é necessária. |Sim |Não |
| 19 |Volumes afixados localmente |Se você cancelar um trabalho de restauração ou se uma restauração falhar e ocorrer um failover do controlador, outro trabalho de restauração aparecerá na página **Trabalhos** . |Isso poderá ocorrer se o trabalho de restauração tiver apenas volumes fixados localmente ou uma combinação de volumes fixados e em camadas. Se o trabalho de restauração incluir apenas volumes em camadas, esse comportamento não ocorrerá. Nenhuma intervenção do usuário é necessária. |Sim |Não |
| 20 |Volumes afixados localmente |Se você tentar converter um volume em camadas (criado e clonado com a atualização 1.2 ou anterior) em um volume fixo local e o dispositivo estiver ficando sem espaço ou uma houver interrupção na nuvem, os clones podem estar corrompidos. |Esse problema ocorre apenas com volumes que foram criados e clonados com software anterior à Atualização 2.1. Isso deve ser um cenário incomum. | | |
| 21 |Conversão de volume |Não atualize os ACRs conectados a um volume enquanto a conversão de volume estiver em andamento (em camadas para fixados localmente ou vice-versa). Atualizar os ACRs pode resultar em dados corrompidos. |Se necessário, atualize os ACRs antes da conversão de volume e não faça mais atualizações da ACR enquanto a conversão estiver em andamento. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Atualizações de controlador e firmware na Atualização 2.2
Esta versão tem somente atualizações de software. No entanto, se você estiver atualizando de uma versão anterior à Atualização 2, você precisará instalar o driver Storport, Spaceport e (em alguns casos) as atualizações de firmware do dispositivo de disco.

Para obter mais informações sobre como instalar o driver, Storport, Spaceport e as atualizações de firmware de disco, confira [Instalar a Atualização 2.2 no dispositivo StorSimple](storsimple-install-update-21.md) .

## <a name="virtual-device-updates-in-update-22"></a>Atualizações do dispositivo virtual na Atualização 2.2
Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtuais precisarão ser criados. 

## <a name="next-step"></a>Próxima etapa
Saiba como [Instalar a Atualização 2.2 no dispositivo StorSimple](storsimple-install-update-21.md) .

