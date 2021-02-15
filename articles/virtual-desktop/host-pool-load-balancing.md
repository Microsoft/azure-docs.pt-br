---
title: Balanceamento de carga do pool de hosts da área de trabalho virtual do Windows-Azure
description: Saiba mais sobre métodos de balanceamento de carga do pool de hosts para um ambiente de área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd8f9e4a3ef63cd97f96af3d4f96a2bb65c3cd09
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951852"
---
# <a name="host-pool-load-balancing-methods"></a>Métodos de balanceamento de carga de pool de host

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica) sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md).

A área de trabalho virtual do Windows dá suporte a dois métodos de balanceamento de carga. Cada método determina qual host de sessão hospedará a sessão de um usuário quando se conectar a um recurso em um pool de hosts.

Os seguintes métodos de balanceamento de carga estão disponíveis na área de trabalho virtual do Windows:

- O balanceamento de carga em primeiro lugar permite que você distribua uniformemente as sessões de usuário nos hosts de sessão em um pool de hosts.
- O balanceamento de carga antes da profundidade permite saturar um host de sessão com sessões de usuário em um pool de hosts. Depois que a primeira sessão atingir seu limite de limite de sessão, o balanceador de carga direcionará todas as novas conexões de usuário para o próximo host de sessão no pool de hosts até atingir seu limite e assim por diante.

Cada pool de hosts só pode configurar um tipo de balanceamento de carga específico para ele. No entanto, ambos os métodos de balanceamento de carga compartilham os seguintes comportamentos, independentemente do pool de hosts em que estão:

- Se um usuário já tiver uma sessão no pool de hosts e estiver se reconectando a essa sessão, o balanceador de carga irá redirecioná-las com êxito para o host de sessão com a sessão existente. Esse comportamento se aplica mesmo se a propriedade AllowNewConnections do host da sessão estiver definida como false.
- Se um usuário ainda não tiver uma sessão no pool de hosts, o balanceador de carga não considerará os hosts de sessão cuja propriedade AllowNewConnections está definida como false durante o balanceamento de carga.

## <a name="breadth-first-load-balancing-method"></a>Método de balanceamento de carga da primeira amplitude

O método de balanceamento de carga da primeira amplitude permite distribuir conexões de usuário para otimizar esse cenário. Esse método é ideal para organizações que desejam fornecer a melhor experiência para os usuários que se conectam ao ambiente de área de trabalho virtual em pool.

O método amplitude-First consulta primeiro os hosts de sessão que permitem novas conexões. O método seleciona um host de sessão aleatoriamente da metade do conjunto de hosts de sessão com o menor número de sessões. Por exemplo, se houver nove computadores com 11, 12, 13, 14, 15, 16, 17, 18 e 19 sessões, uma nova sessão criada não irá automaticamente para o primeiro computador. Em vez disso, ele pode ir para qualquer um dos cinco primeiros computadores com o menor número de sessões (11, 12, 13, 14, 15).

## <a name="depth-first-load-balancing-method"></a>Método de balanceamento de carga da primeira profundidade

O método de balanceamento de carga de profundidade primeiro permite saturar um host de sessão por vez para otimizar esse cenário. Esse método é ideal para organizações econômicas que desejam um controle mais granular sobre o número de máquinas virtuais alocadas para um pool de hosts.

O método depth-first primeiro consulta os hosts de sessão que permitem novas conexões e que não passaram por seu limite máximo de sessão. O método seleciona o host da sessão com o número mais alto de sessões. Se houver um vínculo, o método selecionará o primeiro host de sessão na consulta.

>[!IMPORTANT]
>O algoritmo de balanceamento de carga de profundidade primeiro distribui sessões para hosts de sessão com base no limite máximo de hosts de sessão. Esse parâmetro é necessário quando você usa o algoritmo de balanceamento de carga de profundidade inicial. Para obter a melhor experiência possível para o usuário, certifique-se de alterar o parâmetro máximo de limite de host de sessão para um número que melhor se adapte ao seu ambiente.