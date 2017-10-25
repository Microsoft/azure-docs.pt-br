---
title: "Notas de versão da Atualização 2 para o StorSimple 8000 Series | Microsoft Docs"
description: "Descreve os novos recursos, problemas e soluções alternativas para a Atualização 2 da série 8000 do StorSimple."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/25/2017
ms.author: v-sharos
ms.openlocfilehash: d03e45b839e3630e7f5df4b3144b823955920088
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>Notas de versão da Atualização 2 da série 8000 do StorSimple
## <a name="overview"></a>Visão geral
As notas de versão a seguir descrevem os novos recursos e identificam os problemas críticos em aberto da Atualização 2 da série 8000 do StorSimple. Elas também contêm uma lista das atualizações de software, driver e firmware de disco do StorSimple incluídas nesta versão. 

A Atualização 2 pode ser aplicada a qualquer dispositivo do StorSimple que executa a Versão (GA) ou a Atualização 0.1 à 1.2. A versão do dispositivo associada à Atualização 2 é 6.3.9600.17673.

Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução do StorSimple.

> [!IMPORTANT]
> * São necessárias cerca de 4 a 7 horas para instalar essa atualização (incluindo as atualizações do Windows). 
> * A Atualização 2 tem atualizações de software, driver LSI e firmware de SSD.
> * Para novas versões, talvez você não veja atualizações imediatamente porque fazemos uma distribuição em fases das atualizações. Aguarde alguns dias e procure atualizações novamente, uma vez que elas serão disponibilizadas em breve.
> 
> 

## <a name="whats-new-in-update-2"></a>Novidades na Atualização 2
A Atualização 2 apresenta os novos recursos descritos a seguir.

* **Volumes fixados localmente** – Em versões anteriores da série 8000 do StorSimple, os blocos de dados eram organizados em camadas na nuvem com base no uso. Não havia uma maneira de assegurar que os blocos permaneceriam no local. Na Atualização 2, quando você cria um volume, você pode designar um volume como fixado localmente, e os dados principais desse volume não serão organizados em camadas na nuvem. Instantâneos de volumes fixados localmente ainda serão copiados na nuvem para backup, para que a nuvem possa ser usada para fins de recuperação de desastre e mobilidade de dados. Além disso, é possível alterar o tipo de volume (ou seja, converter volumes em camadas em volumes fixados localmente e vice-versa). 
* **Aprimoramentos do dispositivo virtual do StorSimple** – Anteriormente, a série 8000 do StorSimple posicionava o dispositivo virtual como uma solução de teste/desenvolvimento ou de recuperação de desastre. Havia apenas um modelo de dispositivo virtual (modelo 1100). A Atualização 2 apresenta dois modelos de dispositivo virtual: 
  
  * 8010 (anteriormente chamado de 1100) – Nenhuma alteração; tem uma capacidade de 30 TB e usa o armazenamento padrão do Azure.
  * 8020 – Tem uma capacidade de 64 TB e usa o armazenamento Premium do Azure para um melhor desempenho.
    
    Há um único VHD para ambos os modelos de dispositivo virtual (8010/8020). Quando você inicia o dispositivo virtual, ele detecta os parâmetros de plataforma e aplica a versão de modelo correta.
* **Aprimoramentos de rede** – A Atualização 2 contém os seguintes aprimoramentos de rede:
  
  * Várias NICs podem ser habilitadas para a nuvem, para que o failover possa ocorrer em caso de falha de uma NIC.
  * Aprimoramentos de roteamento, com métricas fixas para blocos habilitados para a nuvem.
  * Nova tentativa online de recursos com falha antes de um failover.
  * Novos alertas para falhas de serviço.
* **Aprimoramentos de atualização** – Na Atualização 1.2 e anterior, a série 8000 do StorSimple era atualizada por meio de dois canais: Windows Update para clustering, iSCSI e assim por diante, e Microsoft Update para binários e firmware.
    A Atualização 2 usa o Microsoft Update para todos os pacotes de atualização. Isso deve levar a menos tempo em execução de failovers ou aplicação de patches. 
* **Atualizações de firmware** – As seguintes atualizações de firmware estão incluídas:
  
  * LSI: lsi_sas2.sys Versão do Produto 2.00.72.10
  * Somente SSD (não há atualizações de HDD): XMGG, XGEG, KZ50, F6C2 e VR08
* **Suporte proativo** : a Atualização 2 permite que a Microsoft efetue pull de informações adicionais de diagnóstico do dispositivo. Quando nossa equipe de operações identifica dispositivos com problemas, estamos mais bem equipados para coletar informações do dispositivo e diagnosticar problemas. **Ao aceitar a Atualização 2, você nos permite oferecer esse suporte proativo**.    

## <a name="issues-fixed-in-update-2"></a>Problemas corrigidos na Atualização 2
A tabela a seguir fornece um resumo dos problemas que foram corrigidos nas Atualizações 2.    

| Não. | Recurso | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Interfaces de rede |Após uma atualização para a Atualização 1, o serviço do StorSimple Manager relatou que as portas Data2 e Data3 falharam em um controlador. Esse problema foi corrigido. |Sim |Não |
| 2 |Atualizações |Após atualizar para a Atualização 1, ocorreram alertas de alarme audível no portal clássico do Azure em vários dispositivos. Esse problema foi corrigido. |Sim |Não |
| 3 |Autenticação Openstack |Ao usar o Openstack como seu provedor de serviços de nuvem, você poderá receber um erro informando que sua cadeia de caracteres de autenticação de nuvem é muito longa. Esse problema foi corrigido. |Sim |Não |

## <a name="known-issues-in-update-2"></a>Problemas conhecidos na Atualização 2
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
| 15 |Cmdlets do Azure PowerShell e volumes fixados localmente |Não é possível criar um volume fixado localmente por meio de cmdlets do Azure PowerShell. (Qualquer volume criado por meio do Azure PowerShell será organizado em camadas.) Além disso, não use os cmdlets do Azure PowerShell para modificar as propriedades de um volume fixado localmente, pois isso terá o efeito indesejado de modificar o tipo de volume para um volume em camadas. |Sempre use o serviço do StorSimple Manager para configurar ou modificar os volumes fixados localmente. |Sim |Não |
| 16 |Espaço disponível para volumes fixados localmente |Se você excluir um volume fixado local, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O serviço do StorSimple Manager atualiza o espaço local disponível aproximadamente a cada hora. |Aguarde uma hora antes de tentar criar o novo volume. |Sim |Não |
| 17 |Volumes afixados localmente |O trabalho de restauração expõe o backup do instantâneo temporário no Catálogo de Backup, mas apenas pelo tempo que durar o trabalho de restauração. Além disso, ele expõe um grupo de discos virtuais com o prefixo **tmpCollection** na página **Backup Policies**, mas apenas durante o trabalho de restauração. |Isso poderá ocorrer se o trabalho de restauração tiver apenas volumes fixados localmente ou uma combinação de volumes fixados e em camadas. Se o trabalho de restauração incluir apenas volumes em camadas, esse comportamento não ocorrerá. Nenhuma intervenção do usuário é necessária. |Sim |Não |
| 18 |Volumes afixados localmente |Se você cancelar um trabalho de restauração e um failover do controlador ocorrer logo em seguida, o trabalho de restauração mostrará **Falha** em vez de **Cancelado**. Se um trabalho de restauração falhar e um failover do controlador ocorrer logo em seguida, o trabalho de restauração mostrará **Cancelado** em vez de **Falha**. |Isso poderá ocorrer se o trabalho de restauração tiver apenas volumes fixados localmente ou uma combinação de volumes fixados e em camadas. Se o trabalho de restauração incluir apenas volumes em camadas, esse comportamento não ocorrerá. Nenhuma intervenção do usuário é necessária. |Sim |Não |
| 19 |Volumes afixados localmente |Se você cancelar um trabalho de restauração ou se uma restauração falhar e ocorrer um failover do controlador, outro trabalho de restauração aparecerá na página **Trabalhos** . |Isso poderá ocorrer se o trabalho de restauração tiver apenas volumes fixados localmente ou uma combinação de volumes fixados e em camadas. Se o trabalho de restauração incluir apenas volumes em camadas, esse comportamento não ocorrerá. Nenhuma intervenção do usuário é necessária. |Sim |Não |
| 20 |Volumes afixados localmente |Se você tentar converter um volume em camadas (criado e clonado com a atualização 1.2 ou anterior) em um volume fixo local e o dispositivo estiver ficando sem espaço ou uma houver interrupção na nuvem, os clones podem estar corrompidos. |Esse problema ocorre apenas com volumes que foram criados e clonados com software anterior à Atualização 2. Isso deve ser um cenário incomum. | | |
| 21 |Conversão de volume |Não atualize os ACRs conectados a um volume enquanto a conversão de volume estiver em andamento (em camadas para fixados localmente ou vice-versa). Atualizar os ACRs pode resultar em dados corrompidos. |Se necessário, atualize os ACRs antes da conversão de volume e não faça mais atualizações da ACR enquanto a conversão estiver em andamento. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Atualizações de controlador e firmware na Atualização 2
Essa versão atualiza o driver e o firmware de disco no dispositivo.

* Para obter mais informações sobre a atualização de firmware LSI, confira o artigo 3121900 da Base de Dados de Conhecimento Microsoft. 
* Para obter mais informações sobre a atualização de firmware do disco, veja o artigo da Base de dados de conhecimento Microsoft 3121899.

## <a name="virtual-device-updates-in-update-2"></a>Atualizações do dispositivo virtual na Atualização 2
Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtuais precisarão ser criados. 

## <a name="next-step"></a>Próxima etapa
Saiba como [instalar a Atualização 2](storsimple-install-update-2.md) no seu dispositivo StorSimple.

