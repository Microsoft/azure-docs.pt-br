---
title: "Notas de versão da Atualização 1.2 para o StorSimple 8000 Series | Microsoft Docs"
description: "Descreve os novos recursos, problemas e soluções alternativas da Atualização 1.2 para o StorSimple 8000 Series."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6c9aae87-6f77-44b8-b7fa-ebbdc9d8517c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 02c393f632c355ea5fbde554adbd8b1169ad8bc8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Notas de versão da Atualização 1.2 para seu dispositivo StorSimple série 8000

## <a name="overview"></a>Visão geral
As notas de versão a seguir descrevem os novos recursos e identificam os problemas críticos abertos da Atualização 1.2 para o  StorSimple 8000 Series. Elas também contêm uma lista das atualizações de software, driver e firmware de disco do StorSimple incluídas nesta versão. 

A Atualização 1.2 pode ser aplicada a qualquer dispositivo StorSimple que execute o software Versão (GA), Atualização 0.1, Atualização 0.2 ou Atualização 0.3. A Atualização 1.2 não estará disponível se seu dispositivo estiver executando Atualização 1 ou Atualização 1.1. Se o dispositivo estiver executando a Versão (GA), [contate o Suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudar na instalação dessa atualização.

A tabela a seguir lista as versões de software do dispositivo correspondentes às Atualizações 1, 1.1 e 1.2.

| Se estiver executando a atualização... | esta é a versão do software do seu dispositivo. |
| --- | --- |
| Atualização 1.2 |6.3.9600.17584 |
| Atualização 1.1 |6.3.9600.17521 |
| Atualização 1.0 |6.3.9600.17491 |

Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução do StorSimple. Para saber mais, veja como [instalar a Atualização 1.2 no dispositivo StorSimple](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * São necessárias cerca de 5 a 10 horas para instalar essa atualização (incluindo as atualizações do Windows). 
> * A Atualização 1.2 tem atualizações de software, do driver LSI e do firmware de disco. Para instalar, siga as instruções em [instalar a Atualização 1.2 no dispositivo StorSimple](storsimple-install-update-1.md).
> * Para novas versões, talvez você não veja atualizações imediatamente porque fazemos uma distribuição em fases das atualizações. Procure atualizações em poucos dias novamente, uma vez que elas serão disponibilizadas em breve.
> 
> 

## <a name="whats-new-in-update-12"></a>Novidades na Atualização 1.2
Esses recursos foram lançados pela primeira vez com a Atualização 1, que foi disponibilizada a um conjunto limitado de usuários. Com a versão Atualização 1.2, a maioria dos usuários do StorSimple recebe os seguintes recursos novos e melhorias:

* **Migração de dispositivos 5000-7000 Series para dispositivos 8000 Series** – esta versão apresenta um novo recurso de migração que permite que usuários de dispositivos StorSimple 5000-7000 Series migrem seus dados para um dispositivo físico StorSimple 8000 Series ou um dispositivo virtual. O recurso de migração tem duas proposições de valor fundamentais:                                                                  
  
  * **Continuidade dos negócios**, permitindo a migração dos dados existentes nos dispositivos séries 5000-7000 para dispositivos série 8000.
  * **Ofertas de recursos aprimorados dos dispositivos 8000 Series**, como o gerenciamento centralizado eficiente de vários dispositivos por meio do serviço StorSimple Manager, uma classe melhor de hardware e firmware atualizado, dispositivos virtuais, mobilidade de dados e recursos no mapa futuro.
    
    Consulte o [guia de migração](http://www.microsoft.com/download/details.aspx?id=47322) para obter detalhes sobre como migrar um dispositivo StorSimple 5000-7000 Series para um dispositivo 8000 Series. 
* **Disponibilidade no Portal Governamental do Azure** – Agora, o StorSimple está disponível no portal Governamental do Azure. Veja como [implantar um dispositivo StorSimple no Portal de Gerenciamento do Azure](storsimple-deployment-walkthrough-gov.md).
* **Suporte para outros provedores de serviços de nuvem** – Os outros provedores de serviços de nuvem com suporte são Amazon S3, Amazon S3 com RRS, HP e OpenStack (beta).
* **Atualização para as APIs de armazenamento mais recentes** – Com esta versão, o StorSimple foi atualizado para as APIs de serviço de armazenamento do Azure mais recentes. Os dispositivos StorSimple 8000 Series que estão executando as versões de software Atualização 1 (Versão, 0.1, 0.2 e 0.3) estão usando versões das APIs do Serviço de Armazenamento do Azure anteriores a 17 de julho de 2009. Conforme mencionado no [anúncio atualizado sobre remoção de versões de serviço de armazenamento](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), até 1 de agosto de 2016, essas APIs serão preteridas. É imperativo que você aplique a Atualização 1 do StorSimple 8000 Series antes de 1 de agosto de 2016. Se você não conseguir fazer isso, os dispositivos StorSimple deixarão de funcionar corretamente.
* **Suporte para ZRS (armazenamento com redundância de zona)** – com a atualização para a versão mais recente das APIs de armazenamento, o StorSimple 8000 Series oferecerá suporte para ZRS (armazenamento com redundância de zona), além de LRS (armazenamento com redundância local) e GRS (armazenamento com redundância geográfica). Consulte o [artigo sobre opções de redundância de armazenamento do Azure](../storage/common/storage-redundancy.md) para obter detalhes do ZRS.
* **Experiência de implantação inicial e atualização aprimorada** – nesta versão, os processos de instalação e atualização foram aprimorados. A instalação por meio do assistente de instalação foi aprimorada para informar ao usuário se a configuração de rede e as configurações do firewall estão incorretas. Cmdlets de diagnóstico adicionais foram fornecidos para ajudar a solucionar problemas de rede do dispositivo. Consulte o [artigo sobre solução de problemas de implantação](storsimple-troubleshoot-deployment.md) para saber mais sobre os novos cmdlets de diagnóstico usados para solucionar problemas.

## <a name="issues-fixed-in-update-12"></a>Problemas corrigidos na Atualização 1.2
A tabela a seguir fornece um resumo dos problemas que foram corrigidos nas Atualizações 1.2, 1.1 e 1.    

| Nº | Recurso | Problema | Corrigido na Atualização | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell para StorSimple |Quando um usuário acessava remotamente o dispositivo StorSimple por meio do Windows PowerShell para StorSimple e depois iniciava o assistente de instalação, uma falha ocorria assim que o IP 0 de Dados era inserido. Agora, esse bug foi corrigido na Atualização 1. |Atualização 1 |Sim |Sim |
| 2 |Redefinição de fábrica |Em alguns casos, quando você executava uma redefinição de fábrica, o dispositivo StorSimple travava e exibia esta mensagem: **a redefinição de fábrica está em andamento (fase 8)**. Isso acontecia se você pressionasse CTRL+C enquanto o cmdlet estava em andamento. Agora esse bug foi corrigido. |Atualização 1 |Sim |Não |
| 3 |Redefinição de fábrica |Depois de uma redefinição de fábrica de controlador duplo com falha, você podia continuar com o registro do dispositivo. Isso resultava em uma configuração de sistema sem suporte. Na Atualização 1, uma mensagem de erro é mostrada e o registro é bloqueado em um dispositivo que tenha uma redefinição de fábrica com falha. |Atualização 1 |Sim |Não |
| 4 |Redefinição de fábrica |Em alguns casos, foram gerados alertas de incompatibilidade falso positivos. Os alertas de incompatibilidade incorretos não serão mais gerados em dispositivos com a  Atualização 1 em execução. |Atualização 1 |Sim |Não |
| 5 |Redefinição de fábrica |Se uma redefinição de fábrica fosse interrompida antes da conclusão, o dispositivo entrava no modo de recuperação e não permitia que você acessasse o Windows PowerShell para StorSimple. Agora esse bug foi corrigido. |Atualização 1 |Sim |Não |
| 6 |Recuperação de desastre |Um bug de recuperação de desastre foi corrigido no qual a DR falhava durante a descoberta de backups no dispositivo de destino. |Atualização 1 |Sim |Sim |
| 7 |LEDs de monitoramento |Em determinadas circunstâncias, os LEDs de monitoramento na parte posterior do dispositivo não indicavam o status correto. O LED azul ficava apagado. Os LEDs de DADOS 0 e 1 ficavam piscando mesmo quando essas interfaces não estavam configuradas. O problema foi corrigido e os LEDs de monitoramento agora indicam o status correto. |Atualização 1 |Sim |Não |
| 8 |LEDs de monitoramento |Em determinadas instâncias, depois de aplicar Atualização 1, a luz azul no controlador ativo será apagada, o que dificulta a identificação do controlador ativo. Esse problema foi corrigido nesta versão do patch. |Atualização 1.2 |Sim |Não |
| 9 |Interfaces de rede |Nas versões anteriores, um dispositivo StorSimple configurado com um gateway não roteável podia ficar offline. Nesta versão, a métrica de roteamento para Dados 0 foi feita a menor possível; portanto, mesmo que outras interfaces de rede estiverem habilitadas para a nuvem, todo o tráfego de nuvem do dispositivo será roteado por meio de Dados 0. |Atualização 1 |Sim |Sim |
| 10 |Backups |Um bug na Atualização 1 que causou a falha de backups depois de 24 dias foi corrigido na versão do patch Atualização 1.1. |Atualização 1.1 |Sim |Sim |
| 11 |Backups |Um bug nas versões anteriores resultou em baixo desempenho dos instantâneos de nuvem com baixas taxas de alteração. Esse bug foi corrigido nesta versão do patch. |Atualização 1.2 |Sim |Sim |
| 12 |Atualizações |Um bug na Atualização 1 que reportou uma falha de atualização e fez com que os controladores entrassem no Modo de recuperação foi corrigido nesta versão do patch. |Atualização 1.2 |Sim |Sim |

## <a name="known-issues-in-update-12"></a>Problemas conhecidos na Atualização 1.2
A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.

| Nº | Recurso | Problema | Comentários/soluções alternativas | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Quorum de disco |Em casos raros, se a maioria dos discos no invólucro de EBOD de um dispositivo 8600 for desconectada, fazendo com que não haja quórum de disco, em seguida, o pool de armazenamento ficará offline. Permanecerá offline, mesmo que os discos sejam reconectados. |Você precisará reiniciar o dispositivo. Se o problema persistir, entre em contato com o Suporte da Microsoft para as próximas etapas. |Sim |Não |
| 2 |ID de controlador incorreta |Quando a substituição do controlador é executada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó par, a ID do controlador pode ser exibida inicialmente como a ID do controlador de pares. Em casos raros, esse comportamento pode ser percebido após uma reinicialização do sistema. |Nenhuma ação do usuário é necessária. Esta situação se resolverá depois que a substituição do controlador for concluída. |Sim |Não |
| 3 |Contas de armazenamento |Usar o serviço de Armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso levará a uma situação na qual os dados do usuário não podem ser recuperados. |Sim |Sim | |
| 4 |Failover de dispositivo |Não há suporte para vários failovers de um contêiner de volume do mesmo dispositivo de origem para diferentes dispositivos de destino. O failover de dispositivo de um único dispositivo inativo para vários dispositivos fará com que os contêineres de volume no primeiro dispositivo com failover percam a propriedade dos dados. Após o failover, esses contêineres de volume serão exibidos ou se comportarão de maneira diferente quando forem exibidos no Portal clássico do Azure. | |Sim |Não |
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

## <a name="physical-device-updates-in-update-12"></a>Atualizações de dispositivo físico na Atualização 1.2
Se o patch da Atualização 1.2 for aplicado a um dispositivo físico (executando versões anteriores à Atualização 1), a versão do software será alterada para 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Atualizações de controlador e firmware na Atualização 1.2
Essa versão atualiza o driver e o firmware de disco no dispositivo.

* Para saber mais sobre a atualização do controlador SAS, confira [Atualização 1 para controladores SAS LSI no dispositivo do Microsoft Azure StorSimple](https://support.microsoft.com/kb/3043005). 
* Para saber mais sobre a atualização de firmware de disco, confira [Atualização 1 de firmware de disco para o dispositivo do Microsoft Azure StorSimple](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Atualizações do dispositivo virtual na Atualização 1.2
Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtuais precisarão ser criados. 

## <a name="next-steps"></a>Próximas etapas
* [Instalar a Atualização 1.2 no seu dispositivo](storsimple-install-update-1.md).

