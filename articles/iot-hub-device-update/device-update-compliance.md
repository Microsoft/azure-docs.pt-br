---
title: Entender a atualização do dispositivo para conformidade do Hub IoT do Azure | Microsoft Docs
description: Entenda como a atualização de dispositivo para o Hub IoT do Azure mede a conformidade da atualização do dispositivo.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ac6094efde8a32b1fcc04c55bbc537afeb4166f7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661891"
---
# <a name="device-update-compliance"></a>Conformidade de Atualizações de dispositivo

Em atualização do dispositivo para o Hub IoT, a conformidade mede quantos dispositivos instalaram a atualização compatível com a versão mais recente. Um dispositivo será compatível se tiver instalado a atualização de versão mais recente disponível que é compatível para ele. 

Por exemplo, considere uma instância de atualização de dispositivo com as seguintes atualizações:

|Nome da atualização|Versão de atualização|Modelo de dispositivo compatível|
|-----------|--------------|-----------------------|
|Atualização1    |1,0    |Model1|
|Atualização 2    |1,0    |Model2|
|Update3    |2,0    |Model1|

Digamos que as seguintes implantações foram criadas:

|Nome da implantação    |Nome da atualização    |Grupo de destino|
|-----------|--------------|-------------------|
|Deployment1    |Atualização1    |Grupo1|
|Deployment2    |Atualização 2    |Grupo 2|
|Deployment3    |Update3    |Grupo 3|

Agora, considere os seguintes dispositivos, com suas associações de grupo e versões instaladas:

|DeviceId   |Modelo do Dispositivo   |Versão de atualização instalada|Agrupar |Conformidade|
|-----------|--------------|-----------------------|-----|---------|
|Device1    |Model1 |1,0    |Grupo1 |Novas atualizações disponíveis</span>|
|Dispositivo2    |Model1 |2,0    |Grupo 3 |Na atualização mais recente|
|Dispositivo3    |Model2 |1,0    |Grupo 2 |Na atualização mais recente|
|Dispositivo4    |Model1 |1,0    |Grupo 3 |Atualização em andamento|

Device1 e Dispositivo4 não são compatíveis porque têm a versão 1,0 instalada, embora haja uma atualização de versão mais alta, Update3, compatível com seu modelo na instância de atualização de dispositivo. Dispositivo2 e Dispositivo3 são compatíveis porque têm as atualizações de versão mais elevadas compatíveis para seus modelos instalados.

A conformidade não considera se uma atualização é implantada no grupo de um dispositivo ou não; Ele examina todas as atualizações publicadas na atualização do dispositivo. No exemplo acima, embora o Device1 tenha instalado a atualização implantada nela, ele é considerado não compatível. Device1 continuará sendo considerado não compatível até que ele instale o Update3 com êxito. O status de conformidade pode ajudá-lo a identificar se são necessárias novas implantações. 

Como mostrado acima, há três Estados de conformidade na atualização do dispositivo para o Hub IoT:

*   **Na última atualização** – o dispositivo instalou a atualização compatível com a versão mais recente publicada para a atualização do dispositivo.
*   **Atualização em andamento** – uma implantação ativa está no processo de entrega da atualização compatível com a versão mais recente para o dispositivo.
*   **Novas atualizações disponíveis** – um dispositivo ainda não instalou a atualização compatível com a versão mais recente e não está em uma implantação ativa para essa atualização.
