---
title: Balanceamento de carga do pool de host da Área de Trabalho Virtual do Windows – Azure
description: Saiba mais sobre métodos de balanceamento de carga do pool de host para um ambiente da Área de Trabalho Virtual do Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: ed0cf568ca8d011beb7150f23c0187bbe262d5f0
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446767"
---
# <a name="host-pool-load-balancing-methods"></a>Métodos de balanceamento de carga de pool de host

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica), sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md).

A Área de Trabalho Virtual do Windows dá suporte a dois métodos de balanceamento de carga. Cada método determina qual host da sessão hospedará a sessão de um usuário quando ele se conectar a um recurso em um pool de host.

Os seguintes métodos de balanceamento de carga estão disponíveis na Área de Trabalho Virtual do Windows:

- O balanceamento de carga em largura permite que você distribua uniformemente as sessões de usuário nos hosts da sessão em um pool de host.
- O balanceamento de carga em profundidade permite saturar um host de sessão com sessões de usuário em um pool de host. Depois que a primeira sessão atingir o limite, o balanceador de carga direcionará todas as novas conexões de usuário para o próximo host da sessão no pool de host até atingir seu limite e assim por diante.

Cada pool de host só pode configurar um tipo de balanceamento de carga específico para ele. No entanto, ambos os métodos de balanceamento de carga compartilham os seguintes comportamentos, independentemente do pool de host em que estão:

- Se um usuário já tiver uma sessão no pool de host e estiver se reconectando a essa sessão, o balanceador de carga vai redirecioná-lo com êxito para o host da sessão existente. Esse comportamento se aplicará mesmo se a propriedade AllowNewConnections do host da sessão estiver definida como False.
- Se um usuário ainda não tiver uma sessão no pool de host, o balanceador de carga não considerará os hosts da sessão cuja propriedade AllowNewConnections está definida como False durante o balanceamento de carga.

## <a name="breadth-first-load-balancing-method"></a>Método de balanceamento de carga em largura

O método de balanceamento de carga em largura permite distribuir conexões de usuário para otimizar esse cenário. Esse método é ideal para organizações que desejam fornecer a melhor experiência para os usuários que se conectam ao ambiente de área de trabalho virtual em pool.

O método de balanceamento em largura consulta primeiro os hosts da sessão que permitem novas conexões. O método seleciona um host de sessão aleatoriamente da metade do conjunto de hosts da sessão com o menor número de sessões. Por exemplo, se houver nove computadores com 11, 12, 13, 14, 15, 16, 17, 18 e 19 sessões, uma sessão criada não vai automaticamente para o primeiro computador. Em vez disso, ela pode ir para qualquer um dos cinco primeiros computadores com o menor número de sessões (11, 12, 13, 14, 15).

## <a name="depth-first-load-balancing-method"></a>Método de balanceamento de carga em profundidade

O método de balanceamento de carga em profundidade permite saturar um host de sessão por vez para otimizar esse cenário. Esse método é ideal para organizações econômicas que desejam um controle mais granular sobre o número de máquinas virtuais alocadas para um pool de host.

O método de balanceamento em profundidade consulta os hosts da sessão que permitem novas conexões e que não ultrapassaram o limite máximo de sessão. O método seleciona o host da sessão com o número mais alto de sessões. Se houver um vínculo, o método selecionará o primeiro host da sessão na consulta.

>[!IMPORTANT]
>O algoritmo de balanceamento de carga em profundidade distribui sessões para hosts de sessão com base no limite máximo de hosts da sessão. Esse parâmetro é necessário quando você usa o algoritmo de balanceamento de carga em profundidade. Para proporcionar a melhor experiência possível para o usuário, altere o parâmetro máximo de limite de host da sessão para um número que melhor se adapte ao seu ambiente.