---
title: Visualização de área de trabalho Virtual do Windows pool balanceamento de carga de métodos de host - Azure
description: Pool de balanceamento de carga métodos de host para um ambiente de visualização de área de trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 8b18224339654c067d8ab9b543fa49a9c7d55ddd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870516"
---
# <a name="host-pool-load-balancing-methods"></a>Métodos de balanceamento de carga de pool de host

Visualização de área de trabalho Virtual do Windows oferece suporte a dois métodos de balanceamento de carga. Cada método determina qual host de sessão irá hospedar uma sessão do usuário quando eles se conectam a um recurso em um pool de host.

Os seguintes métodos de balanceamento de carga estão disponíveis na área de trabalho Virtual do Windows:

- Balanceamento de carga de amplitude permite distribuir uniformemente as sessões de usuário entre os hosts de sessão em um pool de host.
- Balanceamento de carga de profundidade permite saturar um host de sessão com sessões de usuário em um pool de host. Depois que a primeira sessão atinge seu limite de sessão, o balanceador de carga direciona qualquer novas conexões de usuário para o próximo host de sessão no pool de host, até atingir seu limite e assim por diante.

Cada pool de host só pode configurar um tipo de balanceamento de carga específica para ele. No entanto, balanceamento de carga métodos compartilham os seguintes comportamentos, não importa qual hospedam o pool estiverem em:

- Se um usuário já tiver uma sessão no pool de host e reconectar-se para a sessão, o balanceador de carga redirecionará com êxito para o host de sessão com a sessão existente. Esse comportamento se aplica mesmo se a propriedade de AllowNewConnections do host de sessão é definida como False.
- Se um usuário ainda não tiver uma sessão no pool de host, o balanceador de carga não considere hosts de sessão cuja propriedade AllowNewConnections é definida como False durante o balanceamento de carga.

## <a name="breadth-first-load-balancing-method"></a>Método de balanceamento de carga do amplitude

O método de balanceamento de carga de amplitude permite que você distribua as conexões de usuário para otimizar esse cenário. Esse método é ideal para organizações que desejam oferecer a melhor experiência para usuários que se conectam ao seu ambiente de área de trabalho virtual em pool.

O método amplitude primeiro consulta hosts de sessão que permitir que novas conexões. O método, em seguida, seleciona o host da sessão com o menor número de sessões. Se houver uma ligação, o método seleciona o primeiro host de sessão na consulta.

## <a name="depth-first-load-balancing-method"></a>Método de balanceamento de carga de profundidade

O método de balanceamento de carga de profundidade permite que você saturar um host de sessão por vez para otimizar esse cenário. Esse método é ideal para organizações de atento aos custos que quiser um controle mais granular no número de máquinas virtuais que alocou para um pool de host.

O método de profundidade primeiro consulta hosts de sessão que permitir que novas conexões e não deixarão de acima do limite máximo de sessão. O método, em seguida, seleciona o host da sessão com maior número de sessões. Se houver uma ligação, o método seleciona o primeiro host de sessão na consulta.