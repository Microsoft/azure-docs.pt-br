---
title: Entender a conformidade da Atualização de Dispositivo para o Hub IoT do Azure | Microsoft Docs
description: Entenda como a Atualização de Dispositivo para o Hub IoT do Azure mede a conformidade da atualização de dispositivos.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ac6094efde8a32b1fcc04c55bbc537afeb4166f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101661891"
---
# <a name="device-update-compliance"></a>Conformidade da Atualização de Dispositivo

Na Atualização de Dispositivo para o Hub IoT, a conformidade mede quantos dispositivos instalaram a atualização de maior versão compatível. Um dispositivo está em conformidade quando a atualização de maior versão disponível compatível com ele foi instalada. 

Por exemplo, considere uma instância da Atualização de Dispositivo com as seguintes atualizações:

|Nome da atualização|Versão da atualização|Modelo de dispositivo compatível|
|-----------|--------------|-----------------------|
|Update1    |1.0    |Model1|
|Atualização 2    |1.0    |Model2|
|Update3    |2,0    |Model1|

Digamos que as seguintes implantações foram criadas:

|Nome da implantação    |Nome da atualização    |Grupo de destino|
|-----------|--------------|-------------------|
|Deployment1    |Update1    |Grupo1|
|Deployment2    |Atualização 2    |Grupo 2|
|Deployment3    |Update3    |Grupo 3|

Agora, considere os seguintes dispositivos com as respectivas associações de grupo e versões instaladas:

|DeviceId   |Modelo do Dispositivo   |Versão da atualização instalada|Grupo |Conformidade|
|-----------|--------------|-----------------------|-----|---------|
|Device1    |Model1 |1.0    |Grupo1 |Novas atualizações disponíveis</span>|
|Device2    |Model1 |2,0    |Grupo 3 |Na atualização mais recente|
|Device3    |Model2 |1.0    |Grupo 2 |Na atualização mais recente|
|Device4    |Model1 |1.0    |Grupo 3 |Atualização em andamento|

O Device1 e o Device4 não estão em conformidade porque têm a versão 1.0 instalada, embora haja uma atualização de versão superior, Update3, compatível com o modelo deles na instância da Atualização de Dispositivo. O Device2 e o Device3 estão em conformidade porque têm instaladas as atualizações de versão maior compatíveis com os modelos.

A conformidade não considera se uma atualização foi implantada no grupo de um dispositivo ou não; ela examina as atualizações publicadas na Atualização de Dispositivo. No exemplo acima, embora o Device1 tenha instalado a atualização implantada nele, ele é considerado fora de conformidade. O Device1 será considerado fora de conformidade até que instale o Update3. O status de conformidade ajuda a identificar se são necessárias novas implantações. 

Como mostrado acima, há três estados de conformidade na Atualização de Dispositivo para o Hub IoT:

*   **Na última atualização** – o dispositivo instalou a atualização de versão mais recente compatível publicada na Atualização de Dispositivo.
*   **Atualização em andamento** – uma implantação ativa está entregando a atualização de versão mais alta compatível para o dispositivo.
*   **Novas atualizações disponíveis** – um dispositivo ainda não instalou a atualização da versão compatível mais recente e não está em uma implantação ativa para a atualização.
