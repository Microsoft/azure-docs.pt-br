---
title: "Notas de versão da Atualização 4 para o StorSimple 8000 Series | Microsoft Docs"
description: "Descreve os novos recursos, problemas e soluções alternativas da Atualização 4 para o StorSimple 8000 Series."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 23f1bbb066c5b6481988ee841ad8979d78abf084
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Notas de versão da Atualização 4 para o StorSimple 8000 Series

## <a name="overview"></a>Visão geral

As notas de versão a seguir descrevem os novos recursos e identificam os problemas críticos abertos da Atualização 4 para o StorSimple 8000 Series. Elas também contêm uma lista das atualizações de software do StorSimple incluídas nesta versão. 

A Atualização 4 pode ser aplicada a qualquer dispositivo StorSimple que executa a Versão (GA) ou a Atualização 0.1 à 3.1. A versão do dispositivo associada à Atualização 4 é 6.3.9600.17820.

Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução do StorSimple.

> [!IMPORTANT]
> * A Atualização 4 tem o software de dispositivo, firmware USM, driver e firmware LSI, firmware de disco, Storport e Spaceport, segurança e outras atualizações do sistema operacional. São necessárias cerca de quatro horas para instalar essa atualização. A atualização de firmware de disco é uma atualização com tempo de inatividade e resulta em um tempo de inatividade para o dispositivo. Recomendamos a aplicação do Update 4 para manter o dispositivo atualizado. 
> * Para novas versões, talvez você não veja atualizações imediatamente porque fazemos uma distribuição em fases das atualizações. Aguarde alguns dias e procure atualizações novamente, uma vez que elas serão disponibilizadas em breve.

## <a name="whats-new-in-update-4"></a>Novidades na Atualização 4

Veja a seguir as principais melhorias e correções de bugs feitas na Atualização 4.

* **Algoritmos de reclamação de espaço automatizados mais inteligentes** - Na Atualização 4, os algoritmos de reclamação de espaço automatizados foram aprimorados para ajustar os ciclos de reclamação de espaço baseados no espaço reclamado esperado disponível na nuvem. 

* **Aprimoramentos de desempenho para volumes localmente fixados** – a Atualização 4 melhorou o desempenho de volumes localmente fixados em cenários com alta ingestão de dados (comparável ao tamanho do volume de dados).

* **Restauração baseado em mapa de dados** -nas versões anteriores, após uma recuperação de desastres (DR), os dados foi restaurados da nuvem com base nos padrões de acesso, resultando em um desempenho lento. 

    Um novo recurso é implementado na Atualização 4, que rastreia dados acessados com frequência para criar um mapa de dados quando o dispositivo estiver em uso antes da recuperação de desastre (os trechos de dados mais usados têm grande aquecimento, enquanto os trechos menos usados têm pouco aquecimento). Após a recuperação de desastre, o StorSimple usa o mapa de calor para restaurar e reidratar automaticamente os dados da nuvem. 

    Todas as restaurações agora são restaurações baseadas em mapas de dados. Para saber mais sobre como consultar e cancelar trabalhos de restauração e de reidratação baseados em mapa de dados, vá para [Referência de cmdlet do Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

* **Ferramenta de diagnóstico de StorSimple** – na atualização 4, uma ferramenta de diagnóstico do StorSimple está sendo lançada para permitir a fácil de diagnóstico e solução de problemas relacionados à integridade de componentes do sistema, rede, desempenho e hardware. Essa ferramenta é executada por meio do Windows PowerShell para StorSimple. Para obter mais informações, acesse [Solucionar problemas usando a ferramenta de Diagnóstico do StorSimple](storsimple-8000-diagnostics.md).

* **Ferramenta de migração de StorSimple baseado na interface do usuário** - antes dessa versão, migração de dados da série 5000-7000 necessárias de usuários para executar uma parte do fluxo de trabalho de migração usando a interface do Azure PowerShell. Nesta versão, uma ferramenta de migração de StorSimple baseada em interface do usuário fácil de usar é disponibilizada para o suporte facilitar o mesmo fluxo de trabalho de migração. Essa ferramenta também permite a consolidação de recipientes de recuperação. 

* **Alterações relacionadas a FIPS** – essa versão em diante, FIPS está habilitada por padrão em todos os dispositivos da série StorSimple 8000 para contas de nuvem pública do Microsoft Azure Governmental e o Azure.

* **Atualizar alterações** - Nesta versão, bugs relacionados a falhas de atualização foram corrigidos.

* **Alerta para falhas de disco** -um novo alerta que avisa o usuário sobre falhas iminentes de disco é adicionado nesta versão. Se você encontrar este alerta, entre em contato com o Suporte da Microsoft para enviar um disco de substituição. Para obter mais informações, acesse [Alertas de hardware no dispositivo StorSimple](storsimple-manage-alerts.md#hardware-alerts).

* **Alterações de substituição de controlador** -um cmdlet que permite ao usuário consultar o status do processo de substituição de controlador é adicionado nesta versão. Para obter mais informações, vá para o [cmdlet ao status de substituição de controlador de consulta](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Problemas corrigidos na Atualização 4

A tabela a seguir fornece um resumo dos problemas que foram corrigidos na Atualização 4.    

| Não | Recurso | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Failover |Na versão anterior, após o failover, houve um problema relacionado à limpeza observada no site do cliente. Esse problema foi corrigido nesta versão. |Sim |Sim |
| 2 |Volumes afixados localmente |Na versão anterior, havia um problema para a criação de volume relacionado para volumes localmente fixos que possam resultar em falhas na criação do volume. Esse problema foi causado pela raiz e corrigido nesta versão. |Sim |Não |
| 3 |Pacote de suporte |Na versão anterior, havia problemas relacionados ao pacote de suporte que resultaria em uma exceção de System.OutOfMemory ou outros erros, resultando em uma falha de criação do pacote de suporte. Esses bugs foram corrigidos nesta versão. |Sim |Sim |
| 4 |Monitoramento |Na versão anterior, existe um problema relacionado ao monitoramento de gráficos para localmente fixada volumes onde consumo foi mostrado na Web. Esse bug foi corrigido nesta versão. |Sim |Sim |
| 5 |Migração |Na versão anterior, havia vários problemas relacionados à confiabilidade da migração da série 5000-7000 para dispositivos 8000 série. Esses problemas foram resolvidos nesta versão. |Sim |Sim |
| 6 |Atualização |Em versões anteriores, se houvesse uma falha na atualização, os controladores entravam no modo de recuperação e, portanto, o usuário não podia continuar com a atualização e precisaria contatar o Suporte da Microsoft. <br> Esse comportamento foi alterado nesta versão. Se o usuário receber uma falha de atualização depois que os dois controladores estiverem executando a mesma versão (Atualização 4), os controladores não entrarão no modo de recuperação. Se o usuário encontrar essa falha, recomendamos que ele aguarde um pouco e tente a atualização novamente. A repetição foi bem-sucedida. Se a repetição falhar, ele deverá contatar o Suporte da Microsoft. |Sim |Sim |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Problemas conhecidos na atualização 4 de versões anteriores

Não há nenhum problema conhecido de novo na atualização 4. Para obter uma lista dos problemas transportado para 4 de atualização de versões anteriores, vá para [atualização 3 notas de versão](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Controlador SCSI (SAS) anexado em série e atualizações na Atualização 4

Esta versão tem controlador SAS e atualizações de firmware e driver LSI. Para saber mais sobre como instalar essas atualizações, veja [instalar a Atualização 4](storsimple-install-update-4.md) no dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Atualizações do dispositivo virtual na Atualização 4

Esta atualização não pode ser aplicada ao Dispositivo de Nuvem do StorSimple (também conhecido como o dispositivo virtual). Novos dispositivos virtuais precisarão ser criados. 

## <a name="next-step"></a>Próxima etapa

Saiba como [instalar a Atualização 4](storsimple-install-update-4.md) no dispositivo StorSimple.

