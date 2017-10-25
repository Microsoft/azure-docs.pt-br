---
title: "Notas da versão do StorSimple 8000 Atualização 0.2 | Microsoft Docs"
description: "Descreve os novos recursos e correções, problemas em aberto e as soluções alternativas disponíveis para a versão de janeiro de 2015 do Microsoft Azure StorSimple (Atualização 0.2)."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: d9684ae3-b38f-4678-9d70-e5dbc6b03350
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/16/2016
ms.author: v-sharos
ms.openlocfilehash: 2fc50f7faddb4b61ea5e7d328469fc3cc0eb6f3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-update-02-release-notes---january-2015"></a>Notas de versão da Atualização 0.2 do StorSimple série 8000 - janeiro de 2015
## <a name="overview"></a>Visão geral
As notas de versão a seguir identificam as questões críticas em aberto para a versão de janeiro de 2015 do Microsoft Azure StorSimple. Elas também contêm uma lista das atualizações de firmware e software do StorSimple incluídas nesta versão. Esta é a segunda versão depois que a versão de lançamento do StorSimple série 8000 foi disponibilizada em julho de 2014.

Esta atualização não altera a versão do software do dispositivo físico da atualização de outubro. Ela continua a ser a versão 6.3.9600.17312. A imagem usada pela imagem do dispositivo virtual foi alterada nesta versão. Portanto, todos os novos dispositivos virtuais criados após 20/1/2015 mostrarão a versão 6.3.9600.17361.  

Examine as seguintes informações contidas nas notas de versão para a atualização de janeiro de 2015.

> [!IMPORTANT]
> * Esta atualização não está disponível no Windows Update e não pode ser instalada da mesma forma que outras atualizações. O dispositivo não receberá essa atualização mesmo se você tiver aplicado as atualizações usando o portal clássico do Azure. Esta atualização será aplicada somente a dispositivos virtuais criados depois de 20 de Janeiro de 2015. 
> * A versão de janeiro do StorSimple não contém nenhuma atualização para o dispositivo físico do StorSimple. Você ainda poderá aplicar quaisquer atualizações disponíveis do Windows para o dispositivo virtual, incluindo correções de segurança recentes, mas não verá uma alteração na versão para o dispositivo físico.
> 
> 

## <a name="whats-new-in-the-january-release"></a>O que há de novo na versão de janeiro
Esta atualização contém uma correção relacionada aos volumes que ficam offline no dispositivo virtual. (Consulte [Problemas corrigidos nesta versão](#issues-fixed-in-the-january-release).)  

Esta atualização não contém novos recursos ou funcionalidades.  

## <a name="issues-fixed-in-the-january-release"></a>Problemas corrigidos na versão de Janeiro
A tabela a seguir descreve o problema que foi corrigido nessa atualização.

| Nº | Recurso | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Volumes offline |Quando as latências de nuvem altas persistirem por vários minutos, os volumes do dispositivo virtual StorSimple ficarão offline nos hosts. Essa correção aumenta o limite de latências de nuvem, minimizando as situações que fazem com que os volumes fiquem offline nos hosts. |Não |Sim |

## <a name="known-issues-in-the-january-release"></a>Problemas conhecidos na versão de janeiro
A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.

| Nº | Recurso | Problema | Comentários/soluções alternativas | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Redefinição de fábrica |Em alguns casos, quando você executa uma redefinição de fábrica, o dispositivo StorSimple pode estar travado e exibe esta mensagem: **A redefinição de fábrica está em andamento (fase 8)** Isso acontece se você pressionar CTRL + C enquanto o cmdlet estiver em andamento. |Não pressione CTRL + C após iniciar uma redefinição de fábrica. Se você já estiver nesse estado, entre em contato com o Suporte da Microsoft para as próximas etapas. |Sim |Não |
| 2 |Quorum de disco |Em casos raros, se a maioria dos discos no invólucro de EBOD de um dispositivo 8600 for desconectada, fazendo com que não haja quórum de disco, em seguida, o pool de armazenamento ficará offline. Permanecerá offline, mesmo que os discos sejam reconectados. |Você precisará reiniciar o dispositivo. Se o problema persistir, entre em contato com o Suporte da Microsoft para as próximas etapas. |Sim |Não |
| 3 |Falhas de instantâneo de nuvem |Em casos raros, um instantâneo de nuvem pode falhar com o erro **Limite máximo de backup atingido**. Isso ocorre se você exceder 255 clones online no mesmo dispositivo, a partir do mesmo volume original que foi excluído. | |Sim |Sim |
| 4 |ID de controlador incorreta |Quando a substituição do controlador é executada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó par, a ID do controlador pode ser exibida inicialmente como a ID do controlador de pares.  Em casos raros, esse comportamento pode ser percebido após uma reinicialização do sistema. |Nenhuma ação do usuário é necessária. Esta situação se resolverá depois que a substituição do controlador for concluída. |Sim |Não |
| 5 |Gráficos de monitoramento de dispositivo |No serviço StorSimple Manager, os gráficos de monitoramento de dispositivos não funcionam quando a autenticação Básica ou NTLM é habilitada na configuração do servidor proxy para o dispositivo. |Modifique a configuração de proxy da Web para o dispositivo registrado no serviço StorSimple Manager para que a autenticação seja definida como NENHUMA. Para fazer isso, execute o Windows PowerShell para o cmdlet do StorSimple Set-HcsWebProxy. |Sim |Sim |
| 6 |Contas de armazenamento |Usar o serviço de Armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso levará a uma situação na qual os dados do usuário não podem ser recuperados. | |Sim |Sim |
| 7 |Failover de dispositivo |Não há suporte para vários failovers de um contêiner de volume do mesmo dispositivo de origem para diferentes dispositivos de destino. |O failover de um único dispositivo inativo para vários dispositivos fará com que os contêineres de volume no primeiro dispositivo com failover percam a propriedade dos dados. Após o failover, esses contêineres de volume serão exibidos ou se comportarão de maneira diferente quando forem exibidos no Portal clássico do Azure. |Sim |Não |
| 8 |Instalação |Durante o Adaptador StorSimple para instalação do SharePoint, você precisa fornecer um IP do dispositivo para que a instalação seja concluída com êxito. | |Sim |Não |
| 9 |Proxy Web |Se a configuração de proxy Web tiver HTTPS como o protocolo especificado, a comunicação de serviço do dispositivo será afetada e o dispositivo ficará offline. Pacotes de suporte também serão gerados no processo, consumindo recursos significativos em seu dispositivo. |Verifique se a URL do proxy Web possui HTTP como o protocolo especificado. Veja mais informações sobre como [Configurar o proxy da Web para seu dispositivo](storsimple-configure-web-proxy.md). |Sim |Não |
| 10 |Proxy Web |Ao configurar e habilitar o proxy Web em um dispositivo registrado, você precisará reiniciar o controlador ativo em seu dispositivo. | |Sim |Não |
| 11 |Latência de nuvem alta e alta carga de trabalho de E/S |Quando seu dispositivo StorSimple encontra uma combinação de latências de nuvem muito altas (ordem de segundos) e alta carga de trabalho de E/S, os volumes do dispositivo entram em um estado degradado e as E/Ss podem falhar com o erro "o dispositivo não está pronto". |Você precisará reiniciar os controladores de dispositivo manualmente ou executar um failover do dispositivo para se recuperar dessa situação. |Sim |Não |

## <a name="physical-device-updates-in-the-january-release"></a>Atualizações de dispositivo físico na versão de janeiro
Esta atualização não contém nenhuma outra alteração para o dispositivo StorSimple.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-january-release"></a>Controlador SCSI (SAS) anexado em série e atualizações de firmware na versão de Janeiro
Esta versão não contém nenhuma atualização para o controlador SCSI (SAS) anexado em série ou para o firmware. A atualização de driver foi em outubro, versão 2014. 

## <a name="virtual-device-updates-in-the-january-release"></a>Atualizações de dispositivo virtual na versão de janeiro
Esta versão contém uma imagem atualizada para o dispositivo virtual. Portanto, todos os dispositivos virtuais criados após 20 de janeiro de 2015 mostrarão a versão de software 6.3.9600.17361.

