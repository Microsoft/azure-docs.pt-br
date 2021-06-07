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
ms.openlocfilehash: 5915830e4521399ad322dd4a6f3926428d811455
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94942137"
---
Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

1. No portal, selecione **+ Criar um recurso**. Digite **WAN Virtual** na caixa de pesquisa e selecione **Enter**.
1. Selecione **WAN Virtual** nos resultados. Na página WAN Virtual, selecione **Criar** para abrir a página Criar WAN.
1. Na página **Criar WAN**, na guia **Básico**, preencha os seguintes campos:

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="A captura de tela mostra o painel Criar WAN com a guia Noções Básicas selecionada.":::

   * **Assinatura**: selecione a assinatura que você quer usar.
   * **Grupo de recursos**: crie um novo ou use um existente.
   * **Localização do grupo de recursos**: escolha uma localização de recursos na lista suspensa. Uma WAN é um recurso global e não pode residir em uma região específica. No entanto, você deve selecionar uma região a fim de gerenciar e localizar o recurso de WAN criado.
   * **Nome**: digite o nome que você quer dar à sua WAN.
   * **Tipo**: Básico ou Standard. Selecione **Padrão**. Se você selecionar VWAN básica, entenda que as VWANs básicas podem conter apenas hubs básicos, o que limita o tipo de conexão para site a site.
1. Quando terminar de preencher os campos, selecione **Examinar + Criar**.
1. Depois que a validação for aprovada, selecione **Criar** para criar a WAN Virtual.
