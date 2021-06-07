---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/09/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 999f3fb054eedab64a1f7bcebd9788e1edbf29f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86220585"
---
Nesta etapa, você pode criar a conexão entre uma VNET e seu hub. Repita as etapas para cada VNET que você deseja se conectar.

1. Na página da WAN Virtual, selecione **Conexões de rede virtual**.
1. Na página de conexão de rede virtual, selecione **+Adicionar conexão**.
1. Na página **Adicionar conexão**, preencha os seguintes campos:

    * **Nome da Conexão**: nomeie sua conexão.
    * **Hubs**: selecione o hub que você deseja associar a essa conexão.
    * **Assinatura**: verifique a assinatura.
    * **Rede virtual:** selecione a rede virtual que você deseja conectar a esse hub. A rede virtual não pode ter um gateway de rede virtual já existente.
1. Selecione **OK** para criar a conexão.