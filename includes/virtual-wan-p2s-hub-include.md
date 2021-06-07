---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 134f1dc7cb6e53c181b2f518055e5cb758fccf31
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91812637"
---
1. Em sua WAN Virtual, selecione Hubs e **+Novo Hub**.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.jpg" alt-text="novo hub":::

1. Na página de criação do hub virtual, preencha os seguintes campos.

   * **Região** – selecione a região na qual deseja implantar o hub virtual.
   * **Nome** – digite o nome que deseja dar ao seu hub virtual.
   * **Espaço de endereço privado do hub** – o intervalo de endereços do hub na notação CIDR.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.jpg" alt-text="criar hub virtual":::

1. Na guia Ponto a site, preencha os seguintes campos:

   * **Unidades de escala do gateway** – que representa a capacidade agregada do Gateway de VPN do Usuário.
   * **Configuração de ponto a site** – que você criou na etapa anterior.
   * **Pool de endereços do cliente** – para os usuários remotos.
   * **IP do Servidor DNS Personalizado**.

   :::image type="content" source="media/virtual-wan-p2s-hub/hub-with-p2s.png" alt-text="hub com ponto a site":::

1. Selecione **Examinar + criar**.
1. Na página **validação aprovada**, selecione **Criar**.