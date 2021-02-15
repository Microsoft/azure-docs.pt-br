---
title: Habilitar o monitoramento em serviços de nuvem (suporte estendido) usando o portal do Azure
description: Habilitar o monitoramento de instâncias de serviços de nuvem (suporte estendido) usando o portal do Azure
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4591253e1a305632b7f41afe692f297d71366382
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744192"
---
# <a name="enable-monitoring-for-cloud-services-extended-support-using-the-azure-portal"></a>Habilitar o monitoramento de serviços de nuvem (suporte estendido) usando o portal do Azure

Este artigo explica como habilitar alertas em implantações de serviço de nuvem (suporte estendido) existentes. 

## <a name="add-monitoring-rules"></a>Adicionar regras de monitoramento
1. Entre no [portal do Azure](https://portal.azure.com). 
2. Selecione a implantação do serviço de nuvem (suporte estendido) para a qual você deseja habilitar alertas. 
3. Selecione a folha **alertas** . 

    :::image type="content" source="media/enable-alerts-1.png" alt-text="Imagem mostra a seleção da guia alertas na portal do Azure.":::

4. Selecione o ícone **novo alerta** .
     :::image type="content" source="media/enable-alerts-2.png" alt-text="Imagem mostra a seleção da opção Adicionar novo alerta.":::

5. Insira as condições desejadas e as ações necessárias com base em quais métricas você está interessado em controlar. Você pode definir as regras com base em métricas individuais ou no log de atividades. 

     :::image type="content" source="media/enable-alerts-3.png" alt-text="Imagem mostra onde adicionar condições a alertas.":::

     :::image type="content" source="media/enable-alerts-4.png" alt-text="Imagem mostra a configuração da lógica de sinal.":::

     :::image type="content" source="media/enable-alerts-5.png" alt-text="Imagem mostra a configuração da lógica do grupo de ações.":::

6. Quando você terminar de configurar alertas, salve as alterações e com base nas métricas configuradas, você começará a ver a folha **alertas** popular ao longo do tempo.

## <a name="next-steps"></a>Próximas etapas 
- Examine as [perguntas](faq.md) frequentes sobre os serviços de nuvem (suporte estendido).
- Implantar um serviço de nuvem (suporte estendido) usando o [portal do Azure](deploy-portal.md), o [PowerShell](deploy-powershell.md), o [modelo](deploy-template.md) ou o [Visual Studio](deploy-visual-studio.md).