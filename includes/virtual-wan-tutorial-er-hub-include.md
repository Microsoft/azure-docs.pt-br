---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "73491517"
---
1. Localize o WAN Virtual que você criou. Na página WAN virtual, na seção **Conectividade**, selecione **Hubs**.
2. Na página Hubs, selecione **+Novo Hub** para abrir a página **Criar Hub Virtual**.
3. Na página **Criar Hub Virtual**, na guia **Básico**, preencha os seguintes campos:

   ![Noções básicas](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Noções básicas")

    **Detalhes do projeto**

   * Região (anteriormente conhecida como Local)
   * Nome
   * Espaço de endereço privado do hub. O espaço de endereço mínimo é /24 para criar um hub, o que significa que qualquer intervalo entre /25 e /32 produzirá um erro durante a criação.
4. Selecione a **guia ExpressRoute**.

5. Na guia **ExpressRoute**, preencha os seguintes campos:

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * Selecione **Sim** para criar um gateway **ExpressRoute**.
   * Selecione o valor de **Unidades de escala de gateway** no menu suspenso.
6. Selecione **Examinar + Criar** para validar.
7. Selecione **Criar** para criar o hub. Depois de 30 minutos, **Atualize** para exibir o hub na página **Hubs**. Selecione **Ir para o recurso** para navegar até o recurso.