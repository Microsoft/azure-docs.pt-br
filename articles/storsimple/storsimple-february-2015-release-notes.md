---
title: "Notas da versão do StorSimple 8000 Atualização 0.3 | Microsoft Docs"
description: "Descreve os novos recursos e correções, problemas em aberto e as soluções alternativas disponíveis para a versão de fevereiro de 2015 do Microsoft Azure StorSimple (Atualização 0.3)."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: b01bfd04-f9f8-45f4-ade8-95ac2b979e6a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: v-sharos
ms.openlocfilehash: c059fd74854813615754e67547497b7ededbe4dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-update-03-release-notes---february-2015"></a>Notas de versão da Atualização 0.3 do StorSimple série 8000 - fevereiro de 2015
## <a name="overview"></a>Visão geral
As notas de versão a seguir identificam os problemas críticos abertos da Atualização 0.3 do StorSimple série 8000, lançado em fevereiro de 2015. Elas também contêm uma lista das atualizações de firmware e software do StorSimple incluídas nesta versão. Esta é a terceira versão depois que a versão de lançamento do StorSimple série 8000 foi disponibilizada em julho de 2014.

Esta atualização não altera a versão do software de dispositivo da atualização de Janeiro. Ela continua a ser a versão 6.3.9600.17312. Você pode confirmar que a atualização foi instalada, verificando a data da **Última Atualização** . Se a data for 10/2/2015 ou posterior, a atualização foi instalada com êxito.  

É recomendável que você procure e aplique todas as atualizações disponíveis imediatamente após a instalação do seu dispositivo StorSimple. Você também pode ativar as atualizações automáticas para baixar e instalar atualizações de alta prioridade da Microsoft assim que elas são lançadas. Para obter mais informações, consulte [Atualizar seu dispositivo StorSimple](storsimple-update-device.md).  

Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução do StorSimple.  

> [!IMPORTANT]
> * Use o serviço StorSimple Manager e não o Windows PowerShell para StorSimple para instalar as atualizações de Fevereiro.   
> * A duração é de aproximadamente uma hora para instalar essa atualização. No entanto, se você estiver instalando atualizações cumulativas, o processo pode levar cerca de 3 horas para ser concluído.  
> * A versão de fevereiro do StorSimple não contém nenhuma atualização para o dispositivo virtual do StorSimple. Você ainda poderá aplicar quaisquer atualizações disponíveis do Windows ao dispositivo virtual, incluindo correções de segurança recentes, mas não verá uma alteração na versão para o dispositivo virtual.  
> 
> 

Verifique se os seguintes pré-requisitos foram atendidos antes de atualizar seu dispositivo do StorSimple.  

* Verifique se ambos os controladores de dispositivo estão em execução antes de verificar se há atualizações. Se o controlador não estiver em execução, a verificação falhará. Para verificar se os controladores estão em um estado íntegro, navegue até **Status de Hardware** na página **Manutenção**. Se houver componentes que **Precisam de atenção**, contate o Suporte da Microsoft antes de avançar.
* Verifique se os IPs fixos para o controlador 0 e o controlador 1 são roteáveis e podem conectar-se à Internet, pois são usados para atender às atualizações para o dispositivo. Você pode usar o [cmdlet Test-Connection](https://technet.microsoft.com/library/hh849808.aspx) para executar o ping de um endereço conhecido fora da rede, como outlook.com, para verificar se o controlador tem conectividade com a rede externa.
* Certifique-se de que as portas 80 e 443 estão disponíveis no seu dispositivo StorSimple para comunicação de saída. Para obter mais informações, consulte os [Requisitos de rede do dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
* Se a versão de software do dispositivo for anterior a 6.3.9600.17312 (atualização de outubro de 2014), desabilite as portas de Dados 2 e 3, se habilitadas, antes de iniciar a atualização. Deixar as portas de Data 2 ou Data 3 habilitadas ao aplicar a atualização pode fazer com que o controlador de dispositivo entre no modo de recuperação. Observe que ao desabilitar as interfaces de rede, todos os volumes associados serão colocados offline e as E/Ss serão interrompidas durante a atualização.  

## <a name="whats-new-in-the-february-release"></a>O que há de novo na versão de Fevereiro
Esta atualização contém uma correção para o problema de redefinição de fábrica que ocorreu em dispositivos que foram atualizados da versão GA para a versão de Outubro de 2014. Para obter mais informações, consulte [Problemas corrigidos nesta versão](#issues-fixed-in-the-february-release).   

Esta atualização não contém novos recursos ou funcionalidades.  

## <a name="issues-fixed-in-the-february-release"></a>Problemas corrigidos na versão de Fevereiro
A tabela a seguir descreve o problema que foi corrigido nessa atualização.  

| Nº | Recurso | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Redefinição de fábrica |Você tenta executar uma redefinição de fábrica em um dispositivo que originalmente tinha a versão de Distribuição Geral (versão 6.3.9600.17215) instalada, mas foi atualizado para a versão de outubro (versão 6.3.9600.17312). Falha na redefinição de fábrica e o dispositivo torna-se instável. |Sim |Não |

## <a name="known-issues-in-the-february-release"></a>Problemas conhecidos na versão de Fevereiro
A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.

| Nº | Recurso | Problema | Comentários/soluções alternativas | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Redefinição de fábrica |Em alguns casos, quando você executa uma redefinição de fábrica, o dispositivo StorSimple pode estar travado e exibe esta mensagem: **a redefinição de fábrica está em andamento (fase 8)**. Isso acontece se você pressionar CTRL + C enquanto o cmdlet estiver em andamento. |Não pressione CTRL + C após iniciar uma redefinição de fábrica. Se você já estiver nesse estado, entre em contato com o Suporte da Microsoft para as próximas etapas. |Sim |Não |
| 2 |Quorum de disco |Em casos raros, se a maioria dos discos no compartimento de EBOD de um dispositivo 8600 for desconectada, fazendo com que não haja quorum de disco, em seguida, o pool de armazenamento ficará offline. Permanecerá offline, mesmo que os discos sejam reconectados. |Você precisará reiniciar o dispositivo. Se o problema persistir, entre em contato com o Suporte da Microsoft para as próximas etapas. |Sim |Não |
| 3 |Falhas de instantâneo de nuvem |Em casos raros, um instantâneo de nuvem pode falhar com o erro **Limite máximo de backup atingido**. Isso ocorre se você exceder 255 clones online no mesmo dispositivo, a partir do mesmo volume original que foi excluído. | |Sim |Sim |
| 4 |ID de controlador incorreta |Quando a substituição do controlador é executada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó par, a ID do controlador pode ser exibida inicialmente como a ID do controlador de pares. Em casos raros, esse comportamento pode ser percebido após uma reinicialização do sistema. |Nenhuma ação do usuário é necessária. Esta situação se resolverá depois que a substituição do controlador for concluída. |Sim |Não |
| 5 |Gráficos de monitoramento de dispositivo |No serviço StorSimple Manager, os gráficos de monitoramento de dispositivos não funcionam quando a autenticação Básica ou NTLM é habilitada na configuração do servidor proxy para o dispositivo. |Modifique a configuração de proxy da Web para o dispositivo registrado no serviço StorSimple Manager para que a autenticação seja definida como NENHUMA. Para fazer isso, execute o Windows PowerShell para o cmdlet do StorSimple Set-HcsWebProxy. |Sim |Sim |
| 6 |Contas de armazenamento |Usar o serviço de Armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso levará a uma situação na qual os dados do usuário não podem ser recuperados. | |Sim |Sim |
| 7 |Failover de dispositivo |Não há suporte para vários failovers de um contêiner de volume do mesmo dispositivo de origem para diferentes dispositivos de destino.    O failover de um único dispositivo inativo para vários dispositivos fará com que os contêineres de volume no primeiro dispositivo com failover percam a propriedade dos dados. Após o failover, esses contêineres de volume serão exibidos ou se comportarão de maneira diferente quando forem exibidos no Portal clássico do Azure. | |Sim |Não |
| 8 |Instalação |Durante o Adaptador StorSimple para instalação do SharePoint, você precisa fornecer um IP do dispositivo para que a instalação seja concluída com êxito. | |Sim |Não |
| 9 |Proxy Web |Se a configuração de proxy Web tiver HTTPS como o protocolo especificado, a comunicação de serviço do dispositivo será afetada e o dispositivo ficará offline. Pacotes de suporte também serão gerados no processo, consumindo recursos significativos em seu dispositivo. |Verifique se a URL do proxy Web possui HTTP como o protocolo especificado. Para obter mais informações sobre como [Configurar proxy Web para seu dispositivo](storsimple-configure-web-proxy.md). |Sim |Não |
| 10 |Proxy Web |Ao configurar e habilitar o proxy Web em um dispositivo registrado, você precisará reiniciar o controlador ativo em seu dispositivo. | |Sim |Não |
| 11 |Latência de nuvem alta e alta carga de trabalho de E/S |Quando seu dispositivo StorSimple encontra uma combinação de latências de nuvem muito altas (ordem de segundos) e alta carga de trabalho de E/S, os volumes do dispositivo entram em um estado degradado e as E/Ss podem falhar com o erro "o dispositivo não está pronto". |Você precisará reiniciar os controladores de dispositivo manualmente ou executar um failover do dispositivo para se recuperar dessa situação. |Sim |Não |

## <a name="physical-device-updates-in-the-february-release"></a>Atualizações de dispositivo físico na versão de Fevereiro
Essa atualização corrige o problema de redefinição de fábrica que ocorreu em dispositivos que foram atualizados da versão de GA para a versão de Outubro de 2014. Ela não contém nenhuma outra atualização para o dispositivo StorSimple.  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-february-release"></a>Controlador SCSI (SAS) anexado em série e atualizações de firmware na versão de Fevereiro
Esta versão não contém nenhuma atualização para o controlador SCSI (SAS) anexado em série ou para o firmware. A atualização de driver foi em outubro, versão 2014.  

## <a name="virtual-device-updates-in-the-february-release"></a>Atualizações de dispositivo virtual na versão de Fevereiro
Esta versão não contém nenhuma atualização para o dispositivo virtual. Aplicar esta atualização não alterará a versão do software de um dispositivo virtual.

