---
title: Entender a atualização de dispositivo para grupos de dispositivos do Hub IoT do Azure | Microsoft Docs
description: Entenda como os grupos de dispositivos são usados.
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 18388f067ccb5b8a8876aeae685664694c207613
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678966"
---
# <a name="device-groups"></a>Grupos de dispositivos

Um grupo de dispositivos é uma coleção de dispositivos. Os grupos de dispositivos fornecem uma maneira de dimensionar implantações para vários dispositivos. Cada dispositivo pertence a exatamente um grupo de dispositivos por vez.
Você pode optar por criar vários grupos de dispositivos para organizar seus dispositivos. Por exemplo, a Contoso pode usar o grupo de dispositivos de "voo" para os dispositivos em seu laboratório de teste e o grupo de dispositivos de "avaliação" para os dispositivos que sua equipe de campo usa no centro de operações. Além disso, a Contoso pode optar por agrupar seus dispositivos de produção com base em suas regiões geográficas, para que eles possam atualizar os dispositivos de uma agenda que se alinhem com suas zonas de fuso horário regionais. 


## <a name="using-device-twin-tag-for-device-group-creation"></a>Usando a marca de dispositivo de dispositivos para a criação do grupo de dispositivos

As marcas de dispositivo. Os dispositivos precisam ter uma chave ADUGroup e um valor em seu dispositivo para permitir que sejam agrupados.

### <a name="device-twin-tag-format"></a>Formato de marca de dispositivo

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>Grupo de dispositivos não Categorizado

Não categorizado é uma palavra reservada usada para agrupar dispositivos que:
- Não tem a marca de ADUGroup do dispositivo.
- Ter a marca de dispositivo ADUGroup, mas um grupo não é criado com esse nome de grupo.

Por exemplo, considere os dispositivos com suas marcas de dispositivo em cima abaixo:

```markdown
"deviceId": "Device1",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device2",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device3",
"tags": {
  "ADUGroup": "Group2"
}
```

```markdown
"deviceId": "Device4",
```

Abaixo estão os dispositivos e os possíveis grupos que podem ser criados para eles.

|Dispositivo |Agrupar  |
|-----------|--------------|
|Device1    |Grupo1|
|Dispositivo2    |Grupo1|
|Dispositivo3    |Grupo 2|
|Dispositivo4    |Não categorizado|



## <a name="next-steps"></a>Próximas etapas

[Criar um grupo de dispositivos](./create-update-group.md)
