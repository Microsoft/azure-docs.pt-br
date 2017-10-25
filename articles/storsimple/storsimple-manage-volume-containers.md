---
title: "Gerenciar os contêineres de volume do StorSimple | Microsoft Docs"
description: "Explica como você pode usar a página de contêineres de volume do serviço Gerenciador do StorSimple para adicionar, modificar ou excluir um contêiner de volume."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 1c64ce75-1fd3-4d3b-9304-d4dc0fc2b069
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/24/2016
ms.author: v-sharos
ms.openlocfilehash: bb55a7a4bff0fd4319de6f6ce958686ad8a4142b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-storsimple-volume-containers"></a>Usar o serviço StorSimple Manager para gerenciar contêineres de volume do StorSimple
## <a name="overview"></a>Visão geral
Esse tutorial explica como usar o serviço Gerenciador do StorSimple para criar e gerenciar contêineres de volume do StorSimple.

Um contêiner de volume em um dispositivo Microsoft Azure StorSimple contém um ou mais volumes que compartilham as configurações da conta de armazenamento, da criptografia e do consumo de largura de banda. Um dispositivo pode ter vários contêineres de volume para todos os seus volumes. 

Um contêiner de volume apresenta os seguintes atributos:

* **Volumes** – os volumes do StorSimple em camadas ou fixados localmente que estão contidos no contêiner do volume. Um contêiner de volume pode conter até 256 volumes do StorSimple.
* **Criptografia** – uma chave de criptografia que pode ser definida para cada contêiner de volume. Essa chave é usada para criptografar os dados enviados do seu dispositivo StorSimple para a nuvem. Uma chave de bits AES-256 de nível militar é usada com a chave inserida pelo usuário. Para proteger seus dados, é recomendável sempre habilitar a criptografia de armazenamento na nuvem.
* **Conta de armazenamento** – a conta de armazenamento que está vinculada ao provedor de serviços do armazenamento na nuvem. Todos os volumes que residem em um contêiner de volume compartilham essa conta de armazenamento. Você pode escolher uma conta de armazenamento em uma lista existente ou criar uma nova conta quando criar o contêiner de volume e especificar as credenciais de acesso para essa conta.
* **Largura de banda da nuvem** – a largura de banda consumida pelo dispositivo quando os dados do dispositivo estão sendo enviados para a nuvem. Você pode impor um controle de largura de banda, especificando um valor entre 1 e 1000 Mbps ao definir esse contêiner. Se desejar que o dispositivo consuma toda a largura de banda disponível, defina esse campo para Ilimitado. Também é possível criar e aplicar um modelo de largura de banda para alocar a largura de banda com base no agendamento.

![Página de contêineres de volume](./media/storsimple-manage-volume-containers/HCS_VolumeContainersPage.png)

Estes procedimentos a seguir explicam como usar a página **Contêineres de volume** do StorSimple para concluir as seguintes operações comuns:

* Adicionar um contêiner de volume 
* Modificar um contêiner de volume 
* Excluir um contêiner de volume 

## <a name="add-a-volume-container"></a>Adicionar um contêiner de volume
Execute as etapas a seguir para adicionar um contêiner de volume.

[!INCLUDE [storsimple-add-volume-container](../../includes/storsimple-add-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modificar um contêiner de volume
Execute as etapas a seguir para modificar um contêiner de volume.

[!INCLUDE [storsimple-modify-volume-container](../../includes/storsimple-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Excluir um contêiner de volume
Um contêiner de volume possui volumes dentro dele. Ele poderá ser excluído somente se todos os volumes contidos nele forem excluídos primeiro. Execute as etapas a seguir para excluir um contêiner de volume.

[!INCLUDE [storsimple-delete-volume-container](../../includes/storsimple-delete-volume-container.md)]

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [como gerenciar volumes do StorSimple](storsimple-manage-volumes.md). 
* Saiba mais sobre o [uso do serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

