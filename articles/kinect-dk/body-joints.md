---
title: Junções de acompanhamento de corpo do Azure Kinect
description: Entenda o quadro do corpo, as junções, as coordenadas conjuntas e a hierarquia conjunta no Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, portabilidade, corpo, acompanhamento, junção, hierarquia, Bone, conexão
ms.openlocfilehash: 4cf6ac13a93d0674f9fa144abcc3153a2d7c3350
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276396"
---
# <a name="azure-kinect-body-tracking-joints"></a>Junções de acompanhamento de corpo do Azure Kinect

O acompanhamento de corpo do Azure Kinect pode acompanhar vários corpos humanos ao mesmo tempo. Cada corpo inclui uma ID para a correlação temporal entre os quadros e o esqueleto de cinemática. O número de corpos detectados em cada quadro pode ser adquirido usando `k4abt_frame_get_num_bodies()` .

## <a name="joints"></a>Juntas

Posição e orientação conjuntas são estimativas relativas ao quadro de referência do sensor de profundidade global. A posição é especificada em milímetros. A orientação é expressa como um Quaternion normalizado.

## <a name="joint-coordinates"></a>Coordenadas conjuntas

A posição e a orientação de cada forma conjunta seu próprio sistema de coordenadas conjunta. Todos os sistemas de coordenadas conjuntas são sistemas de coordenadas absolutas relativos ao sistema de coordenadas 3D da câmera de profundidade.

> [!NOTE]
> As coordenadas conjuntas estão na orientação do eixo. A orientação do eixo é amplamente usada com avatars comerciais, mecanismos de jogo e software de renderização. O uso da orientação do eixo simplifica os movimentos espelhados, por exemplo, elevar os dois braços por 20 graus.

![Coordenadas conjuntas](./media/concepts/joint-coordinates.png)

## <a name="joint-hierarchy"></a>Hierarquia conjunta

Um esqueleto inclui junções de 32 com a hierarquia conjunta fluindo do centro do corpo para o extremidades. Cada conexão (Bone) vincula a junção pai a uma junção filho. A figura ilustra os locais e a conexão em conjunto relativos ao corpo humano.

![Hierarquia conjunta](./media/concepts/joint-hierarchy.png)

A tabela a seguir enumera as conexões conjuntas padrão.

|Índice |Nome da junção     | Conjunto pai   |
|------|---------------|----------------|
| 0    |PELVIS         | -              |
| 1    |SPINE_NAVAL    | PELVIS         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |PESCOÇO           | SPINE_CHEST    |
| 4    |CLAVICLE_LEFT  | SPINE_CHEST    |
| 5    |SHOULDER_LEFT  | CLAVICLE_LEFT  |
| 6    |ELBOW_LEFT     | SHOULDER_LEFT  |
| 7    |WRIST_LEFT     | ELBOW_LEFT     |
| 8    |HAND_LEFT      | WRIST_LEFT     |
| 9    |HANDTIP_LEFT   | HAND_LEFT      |
| 10   |THUMB_LEFT     | WRIST_LEFT     |
| 11   |CLAVICLE_RIGHT | SPINE_CHEST    |
| 12   |SHOULDER_RIGHT | CLAVICLE_RIGHT |
| 13   |ELBOW_RIGHT    | SHOULDER_RIGHT |
| 14   |WRIST_RIGHT    | ELBOW_RIGHT    |
| 15   |HAND_RIGHT     | WRIST_RIGHT    |
| 16   |HANDTIP_RIGHT  | HAND_RIGHT     |
| 17   |THUMB_RIGHT    | WRIST_RIGHT    |
| 18   |HIP_LEFT       | PELVIS         |
| 19   |KNEE_LEFT      | HIP_LEFT       |
| 20   |ANKLE_LEFT     | KNEE_LEFT      |
| 21   |FOOT_LEFT      | ANKLE_LEFT     |
| 22   |HIP_RIGHT      | PELVIS         |
| 23   |KNEE_RIGHT     | HIP_RIGHT      |
| 24   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 25   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 26   |HEAD           | PESCOÇO           |
| 27   |NARIZ           | HEAD           |
| 28   |EYE_LEFT       | HEAD           |
| 29   |EAR_LEFT       | HEAD           |
| 30   |EYE_RIGHT      | HEAD           |
| 31   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>Próximas etapas

[Mapa do índice de acompanhamento do corpo](body-index-map.md)
