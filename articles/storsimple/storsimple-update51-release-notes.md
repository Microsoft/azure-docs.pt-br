---
title: Notas de versão da atualização 5,1 do StorSimple 8000 Series
description: Descreve os novos recursos, problemas e soluções alternativas para a atualização 5,1 do StorSimple 8000 Series.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: cdb971851ba678ce18f5a1c7954e5620740f3a4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657562"
---
# <a name="storsimple-8000-series-update-51-release-notes"></a>Notas de versão da atualização 5,1 do StorSimple 8000 Series

## <a name="overview"></a>Visão geral

As notas de versão a seguir descrevem os novos recursos e identificam os problemas críticos abertos para a atualização 5,1 do StorSimple 8000 Series. Elas também contêm uma lista das atualizações de software do StorSimple incluídas nesta versão.

A atualização 5,1 pode ser aplicada a qualquer dispositivo StorSimple que esteja executando a atualização 5. Se você estiver usando uma versão inferior a 5, aplique primeiro a atualização 5 e, em seguida, aplique 5,1. A versão do dispositivo associada à atualização 5,1 é 6.3.9600.17885.

Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução do StorSimple.

> [!IMPORTANT]
>
> * A atualização 5,1 é uma atualização obrigatória e deve ser instalada imediatamente. Para obter mais informações, consulte como [aplicar a atualização 5,1](storsimple-8000-install-update-51.md).
> * A atualização 5,1 tem apenas atualizações de segurança. Leva aproximadamente 30 minutos para instalar essa atualização. É altamente recomendável que você aplique a atualização 5,1 para garantir a operação do seu dispositivo.
> * Para novas versões, talvez você não veja atualizações imediatamente porque fazemos uma distribuição em fases das atualizações. Aguarde alguns dias e procure atualizações novamente, pois elas serão disponibilizadas em breve.

## <a name="whats-new-in-update-51"></a>O que há de novo na atualização 5,1

As seguintes melhorias principais e correções de bugs foram feitas na atualização 5,1:

* **Tls 1,2** -esta atualização do StorSimple impedirá o TLS 1,2 em todos os clientes. Esta é uma atualização obrigatória para todos os dispositivos StorSimple 8000 series.

   Se você vir o seguinte aviso, deverá atualizar o software no dispositivo antes de continuar:

   Um ou mais dispositivos StorSimple estão executando uma versão de software mais antiga. A atualização mais recente disponível para o TLS 1,2 é uma atualização obrigatória e deve ser instalada imediatamente nesses dispositivos. O TLS 1,2 é usado para todas as portal do Azure comunicação e sem essa atualização, o dispositivo não poderá se comunicar com o serviço StorSimple.

## <a name="known-issues-in-update-51-from-previous-releases"></a>Problemas conhecidos na atualização 5,1 de versões anteriores

Não há novos problemas conhecidos na atualização 5,1. Para obter uma lista de problemas transferidos para a atualização 5,1 de versões anteriores, acesse [atualização 3 notas de versão](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="storsimple-cloud-appliance-updates-in-update-51"></a>Atualizações do dispositivo de nuvem StorSimple na atualização 5,1

Esta atualização não pode ser aplicada ao Dispositivo de Nuvem do StorSimple (também conhecido como o dispositivo virtual). Novos dispositivos de nuvem precisam ser criados usando a imagem da atualização 5,1. Para obter informações sobre como criar um Dispositivo de Nuvem StorSimple, acesse [Implantar e gerenciar um Dispositivo de Nuvem StorSimple](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Próxima etapa

Saiba como [instalar a atualização 5,1](storsimple-8000-install-update-51.md) em seu dispositivo StorSimple.
