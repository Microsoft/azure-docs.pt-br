---
title: Gerenciar as políticas de backup do StorSimple da série 8000 | Microsoft Docs
description: Explica como você pode usar o serviço do Gerenciador de Dispositivos do StorSimple para criar e gerenciar backups manuais, agendas de backup e retenção de backup em um dispositivo StorSimple da série 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 607379f8645226a031646376df9ca18f4d3164bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819188"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Usar o serviço do Gerenciador de Dispositivos do StorSimple no Portal do Azure para gerenciar políticas de backup


## <a name="overview"></a>Visão geral

Este tutorial explica como usar a folha **Políticas de backup** do serviço do Gerenciador de Dispositivos do StorSimple para controlar os processos e a retenção de backup dos volumes do StorSimple. Ele também descreve como concluir um backup manual.

Quando você faz backup de um volume, você pode escolher criar um instantâneo local ou um instantâneo de nuvem. Se você estiver fazendo backup de um volume fixado localmente, é recomendável que você especifique um instantâneo de nuvem. Gravar um grande número de instantâneos locais de um volume fixado localmente juntamente com um conjunto de dados que tem muita variação resultará em uma situação em que você pode, rapidamente, ficar sem espaço local. Se você optar por tirar instantâneos locais, recomendamos que você menos instantâneos diários para fazer backup do estado mais recente, mantê-los por um dia e, em seguida, excluí-los.

Quando você tira um instantâneo de nuvem de um volume fixado localmente, você copiar apenas os dados alterados para a nuvem, em que ocorrem sua eliminação de duplicação e sua compactação.

## <a name="the-backup-policy-blade"></a>A folha Política de backup

A folha **Política de backup** do dispositivo StorSimple permite gerenciar políticas de backup e agendar instantâneos locais e de nuvem. As políticas de backup são usadas para configurar agendamentos e retenção de backup para uma coleção de volumes. Políticas de backup permitem tirar um instantâneo de vários volumes ao mesmo tempo. Isso significa que os backups criados por uma política de backup serão cópias consistentes com falhas.

A listagem tabular das políticas de backup também permite filtrar as políticas de backup existentes por um ou mais dos seguintes campos:

* **Nome da política** – o nome associado à política. Os diferentes tipos de políticas incluem:

  * Políticas agendadas, que são criadas explicitamente pelo usuário.
  * Políticas importadas, que foram originalmente criadas no Gerenciador de Instantâneos do StorSimple. Elas têm uma marca que descreve o host do Gerenciador de Instantâneos do StorSimple do qual as políticas foram importadas.

  > [!NOTE]
  > Políticas de backup automáticas ou padrão não estão mais habilitadas no momento da criação do volume.

* **Último backup bem-sucedido** – a data e hora do último backup bem-sucedido realizado com essa política.

* **Próximo backup** – a data e hora do próximo backup agendado que será iniciado por essa política.

* **Volumes** – os volumes associados à política. Todos os volumes associados a uma política de backup são agrupados quando os backups são criados.

* **Agendas** – o número de agendamentos associados à política de backup.

As operações usadas com frequência que podem ser executadas nas políticas de backup são:

* Adicionar uma política de backup
* Adicionar ou modificar um agendamento
* Adicionar ou remover um volume
* Excluir uma política de backup
* Fazer um backup manual

## <a name="add-a-backup-policy"></a>Adicionar uma política de backup

Adicione uma política de backup para agendar automaticamente seus backups. Quando você cria um volume pela primeira vez, nenhuma política de backup padrão é associada a ele. Você precisa adicionar e atribuir uma política de backup para proteger os dados do volume.

Execute as etapas a seguir no Portal do Azure para adicionar uma política de backup ao dispositivo StorSimple. Depois de adicionar a política, você poderá definir um agendamento (confira [Adicionar ou modificar um agendamento](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Adicionar ou modificar um agendamento

É possível adicionar ou modificar um agendamento que esteja anexado a uma política de backup existente no dispositivo StorSimple. Execute as etapas a seguir no Portal do Azure para adicionar ou modificar um agendamento.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Adicionar ou remover um volume

Você pode adicionar ou remover um volume atribuído a uma política de backup no dispositivo StorSimple. Execute as etapas a seguir no Portal do Azure para adicionar ou remover um volume.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Excluir uma política de backup

Execute as etapas a seguir no Portal do Azure para excluir uma política de backup do dispositivo StorSimple.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Fazer um backup manual

Execute as etapas a seguir no Portal do Azure para criar um backup sob demanda (manual) para um único volume.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [usar o serviço Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

