---
title: Notas de versão da Atualização 5 para o StorSimple Série 8000 | Microsoft Docs
description: Descreve os novos recursos, os problemas e as soluções alternativas da Atualização 5 para o StorSimple Série 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 462cbd6261723aa91bbfd23292611e758a800ed2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844084"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Notas de versão da Atualização 5 para o StorSimple Série 8000

## <a name="overview"></a>Visão geral

As notas de versão a seguir descrevem os novos recursos e identificam os problemas críticos abertos da Atualização 5 para o StorSimple Série 8000. Elas também contêm uma lista das atualizações de software do StorSimple incluídas nesta versão.

A Atualização 5 pode ser aplicada a qualquer dispositivo StorSimple que executa desde a Atualização 0.1 até a Atualização 4. A versão do dispositivo associada à Atualização 5 é 6.3.9600.17845.

Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução do StorSimple.

> [!IMPORTANT]
> * A atualização 5 é obrigatória e deve ser instalada imediatamente. Para obter mais informações, consulte como [Aplicar a Atualização 5](storsimple-8000-install-update-5.md).
> * A Atualização 5 tem o software do dispositivo, o firmware do disco, a segurança do SO bem como outras atualizações do sistema operacional. São necessárias cerca de quatro horas para instalar essa atualização. A atualização de firmware de disco é uma atualização com tempo de inatividade e resulta em um tempo de inatividade para o dispositivo. Recomendamos a aplicação da Atualização 5 para manter seu dispositivo atualizado.
> * Para novas versões, talvez você não veja atualizações imediatamente porque fazemos uma distribuição em fases das atualizações. Aguarde alguns dias e procure atualizações novamente, pois elas serão disponibilizadas em breve.

## <a name="whats-new-in-update-5"></a>Novidades na Atualização 5

Veja a seguir as principais melhorias e correções de bugs feitas na Atualização 5.

* **Uso do AAD (Azure Active Directory) para autenticar com o serviço do Gerenciador de Dispositivos do StorSimple** – da Atualização 5 em diante, o Azure Active Directory será usado para autenticar com o serviço do Gerenciador de Dispositivos do StorSimple. O mecanismo de autenticação antigo será preterido até dezembro de 2017. Todos os usuários devem incluir novas URLs de autenticação em suas regras de firewall. Para obter mais informações, acesse as [URLs de autenticação listadas nos requisitos de rede para seu dispositivo StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Se a URL de autenticação não estiver incluída nas regras de firewall, os usuários verão um alerta crítico, informando que não foi possível autenticar o dispositivo StorSimple no serviço. Se os usuários virem esse alerta, será necessário incluir a nova URL de autenticação. Para obter mais informações, acesse [Alertas de rede do StorSimple](storsimple-8000-manage-alerts.md#networking-alerts).

* **Nova versão do StorSimple Snapshot Manager** – uma nova versão do StorSimple Snapshot Manager é lançada com a Atualização 5 e é compatível com todos os dispositivos StorSimple que estão executando a Atualização 4 ou posterior. É recomendável que você atualize para esta versão. A versão anterior do StorSimple Snapshot Manager é usada para dispositivos StorSimple que estão executando a Atualização 3 ou anterior. [Baixe a versão apropriada do StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) e consulte [Implantar o StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problemas corrigidos na Atualização 5

A tabela a seguir fornece um resumo dos problemas que foram corrigidos na Atualização 5.

| Não  | Recurso | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Comunicação remota do Windows PowerShell |Na versão anterior, um usuário receberia um erro ao tentar estabelecer uma conexão remota com o Dispositivo de Nuvem StorSimple por meio do Windows PowerShell. Esse problema foi causado pela raiz e corrigido nesta versão. |Não  |Sim |
| 2 |Modelos de largura de banda |Na versão anterior, havia um problema com os modelos de largura de banda, que resultava em menor largura de banda do que para a qual o dispositivo foi configurado. Esse problema foi corrigido nesta versão. |Sim |Sim |
| 3 |Failover |Na versão anterior, quando um dispositivo com um grande número de volumes passava por failover para outro dispositivo que executava a Atualização 4, o processo falhava ao tentar aplicar os registros de controle de acesso. Esse problema foi corrigido nesta versão. |Sim |Sim |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Problemas de versões anteriores conhecidos na Atualização 5

Não há nenhum novo problema conhecido na Atualização 5. Para obter uma lista dos problemas levados para a Atualização 5, desde as versões anteriores, acesse [Notas de versão da Atualização 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Controlador SAS (SCSI anexado em série) e atualizações de firmware na Atualização 5

Esta versão tem controlador SAS e atualizações de firmware e driver LSI. Para saber mais sobre como instalar essas atualizações, consulte [instalar a Atualização 5](storsimple-8000-install-update-5.md) em seu dispositivo StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Atualizações do Dispositivo de Nuvem StorSimple na Atualização 5

Esta atualização não pode ser aplicada ao Dispositivo de Nuvem do StorSimple (também conhecido como o dispositivo virtual). Novos dispositivos de nuvem precisam ser criados usando a imagem da Atualização 5. Para obter informações sobre como criar um Dispositivo de Nuvem StorSimple, acesse [Implantar e gerenciar um Dispositivo de Nuvem StorSimple](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Próxima etapa

Saiba como [instalar a Atualização 5](storsimple-8000-install-update-5.md) em seu dispositivo StorSimple.

