---
title: "Notas da versão do StorSimple 8000 Atualização 0.1 | Microsoft Docs"
description: "Descreve os novos recursos e correções, problemas em aberto e as soluções alternativas disponíveis para a versão de outubro de 2014 do Microsoft Azure StorSimple (Atualização 0.1)."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: fd35e3c3-4770-460c-999d-f72ab7053a20
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: 4dfd3973593a94adfc15a6e15d69c697e13998af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-update-01-release-notes--october-2014"></a>Notas de versão da Atualização 0.1 do StorSimple série 8000 - outubro de 2014
## <a name="overview"></a>Visão geral
As notas de versão a seguir identificam os problemas críticos abertos da Atualização 0.1 do StorSimple série 8000, em outubro de 2014. Elas também contêm uma lista das atualizações de firmware e software do StorSimple incluídas nesta versão. Esta é a primeira versão depois que a versão de lançamento do StorSimple série 8000 foi disponibilizada em julho de 2014 e corresponde à versão de software 6.3.9600.17312.  

É recomendável que você procure e aplique todas as atualizações disponíveis imediatamente após a instalação do dispositivo. Você também pode ativar as atualizações automáticas para baixar e instalar atualizações de alta prioridade da Microsoft assim que elas são lançadas. Para obter mais informações, consulte como [Atualizar seu dispositivo StorSimple](storsimple-update-device.md).  

Examine as informações contidas nas notas de versão antes de implantar as atualizações em sua solução do StorSimple.  

> [!IMPORTANT]
> * Use o serviço StorSimple Manager e não o Windows PowerShell para StorSimple para instalar as atualizações de Outubro.  
> * As atualizações geralmente levam cerca de três horas.  
> * A versão de outubro do StorSimple não contém todas as atualizações para o dispositivo virtual do StorSimple. Você ainda poderá aplicar quaisquer atualizações disponíveis do Windows, incluindo correções de segurança recentes, mas não verá uma alteração na versão para o dispositivo virtual.  
> 
> 

Verifique se os seguintes pré-requisitos foram atendidos antes de atualizar seu dispositivo do StorSimple.  

* Verifique se ambos os controladores de dispositivo estão em execução antes de verificar se há atualizações. Se o controlador não estiver em execução, a verificação falhará. Para verificar se os controladores estão em um estado íntegro, navegue até **Status de Hardware** na página **Manutenção**. Se houver componentes que **Precisam de atenção**, contate o Suporte da Microsoft antes de avançar.  
* Verifique se os IPs fixos para o Controlador 0 e Controlador 1 são roteáveis e podem se conectar à Internet, pois são usados para atender às atualizações para o dispositivo. Você pode usar o [cmdlet Test-Connection](https://technet.microsoft.com/library/hh849808.aspx) para executar o ping de um endereço conhecido fora da rede, como outlook.com, para verificar se o controlador tem conectividade com a rede externa.  
* Certifique-se de que as portas de saída necessárias estejam disponíveis em seu dispositivo StorSimple para comunicação de saída. Para obter mais informações, consulte os [Requisitos de rede do dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).  
* Se a versão de software do dispositivo for anterior a 6.3.9600.17312 (atualização de outubro de 2014), desabilite as portas de Dados 2 e 3, se habilitadas, antes de iniciar a atualização. Se você deixar as portas de Dados 2 ou 3 habilitadas ao aplicar a atualização, isso pode fazer com que o controlador de dispositivo entre no modo de recuperação. Observe que ao desabilitar as interfaces de rede, todos os volumes associados serão colocados offline e a E/S será interrompida durante a atualização.  

## <a name="whats-new-in-the-october-release"></a>O que há de novo na versão de Outubro
Esta atualização inclui os seguintes aprimoramentos:

* Agora você pode usar a interface do usuário do serviço StorSimple Manager para gerenciar os controladores de dispositivo. As ações de gerenciamento incluem reiniciar, desligar ou ativar um controlador. Para obter mais informações, vá para [Gerenciar controladores de dispositivo StorSimple](storsimple-manage-device-controller.md).  
* Você pode agendar a alocação de largura de banda WAN conforme as combinações de dia da semana e hora do dia. Isso permite fazer melhor uso da largura de banda WAN durante os horários de pico. Modelos diferentes de largura de banda são permitidos para contêineres de volume diferente. Para obter mais informações, vá para [Gerenciar seus modelos de largura de banda do StorSimple](storsimple-manage-bandwidth-templates.md).  
* Você pode configurar notificações por email para notificar de forma pró-ativa os administradores de TI e outros sobre problemas existentes ou possivelmente futuros. Para obter mais informações, consulte [Definir configurações de alerta](storsimple-manage-alerts.md#configure-alert-settings).  

## <a name="issues-fixed-in-the-october-release"></a>Problema corrigidos na versão de outubro
A tabela a seguir fornece um resumo dos problemas que foram corrigidos nesta atualização.  

| Nº | Recurso | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Interfaces de rede |Na versão anterior, as interfaces de rede DADOS 2 e DADOS 3 foram trocadas no software. Esse problema foi corrigido nesta atualização. Limpe as configurações e desabilite essas interfaces de rede antes de instalar a atualização. Depois de instalar a atualização, você terá que reconfigurar essas interfaces. |Sim |Não |
| 2 |Pacote de suporte |Na versão anterior, se você executasse o cmdlet do Windows PowerShell **Export-HcsSupportPackage** para recuperar os logs do BMC (Controlador BMC), a operação iria falhar com o seguinte aviso: "A operação teve êxito nesse controlador, mas falhou no controlador par devido aos erros a seguir. Verifique se o par está íntegro e se o nó atual pode se conectar ao par." Esse problema está corrigido. |Sim |Não |
| 3 |Failover de dispositivo |Na versão anterior, havia uma possibilidade de inconsistência de dados se um trabalho de **backup de descoberta** falhasse durante um failover de dispositivo. Esse problema está corrigido. |Sim |Não |
| 4 |Failover de dispositivo |Na versão anterior, após um failover de dispositivo, os backups eram visíveis, mas o contêiner de volume associado não estava presente no dispositivo de destino. Esse problema está corrigido. |Sim |Não |
| 5 |Failover de dispositivo |Na versão anterior, havia um bug na enumeração de backups de nuvem durante a operação de restauração do registro que poderia levar à inconsistência de dados se houvessem problemas de conectividade de nuvem. |Sim |Não |
| 6 |Atualização de firmware |Na versão anterior, o trabalho de atualização de firmware do dispositivo falhou e exibiu um erro que declarou que os cmdlets não foram reconhecidos e que, como resultado, a atualização falhou. O controlador, em seguida, entrou no modo de recuperação. Esse problema está corrigido. |Sim |Não |
| 7 |Instalação |Erros causados pelo fato de o dispositivo não estar sendo espelhado corretamente durante a instalação agora foram corrigidos. |Sim |Não |
| 8 |Redefinição de fábrica |Agora você pode opcionalmente ignorar a verificação do firmware para a redefinição de fábrica. Esta é uma alteração da versão anterior. |Sim |Não |
| 9 |Redefinição de fábrica |Na versão anterior, quando um cmdlet de redefinição de fábrica era executado, as verificações de versão de firmware eram feitas apenas para alguns componentes de hardware. Verificações de firmware adicionais foram feitas após a primeira reinicialização do processo, o que pode causar falha na redefinição. Essa correção garante que todas as verificações de firmware são feitas quando o cmdlet de redefinição de fábrica é executado e antes do primeiro sistema reinicializar. |Sim |Não |
| 10 |Rotação de chaves da conta de armazenamento |O cmdlet **Invoke-HcsmServiceDataEncryptionKeyChange** usado para girar as chaves da conta de armazenamento agora solicita que o usuário insira a chave de criptografia dos dados de serviço. Essa é uma alteração da versão anterior em que a chave de criptografia dos dados de serviço foi passada como um parâmetro embutido. |Sim |Não |
| 11 |Failback dentro de 24 horas |Durante a recuperação de desastre, a limpeza no dispositivo de origem não aconteceu corretamente, causando a falha do failback. Isso foi corrigido nesta atualização. |Sim |Não |

## <a name="known-issues-in-the-october-release"></a>Problemas conhecidos na versão de Outubro
A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.

| Nº | Recurso | Problema | Comentários/soluções alternativas | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Redefinição de fábrica |Em alguns casos, quando você executa uma redefinição de fábrica, o dispositivo StorSimple pode estar travado e exibe esta mensagem: **a redefinição de fábrica está em andamento (fase 8)**. Isso acontece se você pressionar CTRL + C enquanto o cmdlet estiver em andamento. |Não pressione CTRL + C após iniciar uma redefinição de fábrica. Se você já estiver nesse estado, entre em contato com o Suporte da Microsoft para as próximas etapas. |Sim |Não |
| 2 |Redefinição de fábrica |Não redefina um dispositivo StorSimple que esteja atualizado a partir da Disponibilidade Geral com o lançamento de outubro de 2014. |Esta operação só funcionará se um patch for instalado. Contate o Suporte da Microsoft para obter esse patch necessário. |Sim |Não |
| 3 |Quorum de disco |Em casos raros, se a maioria dos discos no invólucro de EBOD de um dispositivo 8600 for desconectada, fazendo com que não haja quórum de disco, em seguida, o pool de armazenamento ficará offline. Permanecerá offline, mesmo que os discos sejam reconectados. |Você precisará reiniciar o dispositivo. Se o problema persistir, entre em contato com o Suporte da Microsoft para as próximas etapas. |Sim |Não |
| 4 |Falhas de instantâneo de nuvem |Em casos raros, um instantâneo de nuvem pode falhar com o erro **Limite máximo de backup atingido**. Isso ocorre se você exceder 255 clones online no mesmo dispositivo, a partir do mesmo volume original que foi excluído. | |Sim |Sim |
| 5 |ID de controlador incorreta |Quando a substituição do controlador é executada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó par, a ID do controlador pode ser exibida inicialmente como a ID do controlador de pares. Em casos raros, esse comportamento pode ser percebido após uma reinicialização do sistema. |Nenhuma ação do usuário é necessária. Esta situação se resolverá depois que a substituição do controlador for concluída. |Sim |Não |
| 6 |Gráficos de monitoramento de dispositivo |No serviço StorSimple Manager, os gráficos de monitoramento de dispositivos não funcionam quando a autenticação Básica ou NTLM é habilitada na configuração do servidor proxy para o dispositivo. |Modifique a configuração de proxy da Web para o dispositivo registrado no serviço StorSimple Manager para que a autenticação seja definida como NENHUMA. Para fazer isso, execute o Windows PowerShell para o cmdlet do StorSimple Set-HcsWebProxy. |Sim |Sim |
| 7 |Contas de armazenamento |Usar o serviço de Armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso levará a uma situação na qual os dados do usuário não podem ser recuperados. | |Sim |Sim |
| 8 |Failover de dispositivo |Não há suporte para vários failovers de um contêiner de volume do mesmo dispositivo de origem para diferentes dispositivos de destino. |O failover de um único dispositivo inativo para vários dispositivos fará com que os contêineres de volume no primeiro dispositivo com failover percam a propriedade dos dados. Após o failover, esses contêineres de volume serão exibidos ou se comportarão de maneira diferente quando forem exibidos no Portal clássico do Azure. |Sim |Não |
| 9 |Instalação |Durante o Adaptador StorSimple para instalação do SharePoint, você precisa fornecer um IP do dispositivo para que a instalação seja concluída com êxito. | |Sim |Não |
| 10 |Proxy Web |Se a configuração de proxy Web tiver HTTPS como o protocolo especificado, a comunicação de serviço do dispositivo será afetada e o dispositivo ficará offline. Pacotes de suporte também serão gerados no processo, consumindo recursos significativos em seu dispositivo. |Verifique se a URL do proxy Web possui HTTP como o protocolo especificado. Para obter mais informações sobre como [Configurar proxy Web para seu dispositivo](storsimple-configure-web-proxy.md). |Sim |Não |
| 11 |Proxy Web |Ao configurar e habilitar o proxy Web em um dispositivo registrado, você precisará reiniciar o controlador ativo em seu dispositivo. | |Sim |Não |
| 12 |Latência de nuvem alta e alta carga de trabalho de E/S |Quando seu dispositivo StorSimple encontra uma combinação de latências de nuvem muito altas (ordem de segundos) e alta carga de trabalho de E/S, os volumes do dispositivo entram em um estado degradado e as E/Ss podem falhar com o erro "o dispositivo não está pronto". |Você precisará reiniciar os controladores de dispositivo manualmente ou executar um failover do dispositivo para se recuperar dessa situação. |Sim |Não |

## <a name="physical-device-updates-in-the-october-release"></a>As atualizações de dispositivo físico na versão de outubro
Quando essas atualizações são aplicadas a um dispositivo físico, a versão do software é alterada para 6.3.9600.17312. Salvo indicação em contrário, estas notas de versão se aplicam a todos os modelos do dispositivo StorSimple. Para obter mais informações sobre essas atualizações, consulte [Atualização de software do dispositivo físico de outubro de 2014 para o dispositivo do Microsoft Azure StorSimple](http://support.microsoft.com/kb/2986997).  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-october-release"></a>Controlador SCSI (SAS) anexado em série e as atualizações de firmware na versão de Outubro
Esta versão atualiza o driver e o firmware no controlador SAS de seu dispositivo físico. Para obter mais informações sobre a atualização do controlador SAS, consulte [Atualização de outubro de 2014 para controladores SAS LSI no dispositivo do Microsoft Azure StorSimple](http://support.microsoft.com/kb/2987020).   

Esta versão também se aplica a uma atualização de firmware cumulativa que resolve problemas de confiabilidade com os componentes de hardware do dispositivo. Para obter mais informações sobre a atualização de firmware, consulte [Atualização de firmware de outubro de 2014 para o dispositivo do Microsoft Azure StorSimple](http://support.microsoft.com/kb/2987015).  

## <a name="virtual-device-updates-in-the-october-release"></a>Atualizações de dispositivo virtual na versão de Outubro
Esta versão não contém todas as atualizações para o dispositivo virtual. Aplicar esta atualização não alterará a versão do software de um dispositivo virtual.

