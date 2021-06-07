---
title: Conectar dados do Perimeter 81 ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Perimeter 81 ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: e70bfd12b018e785e7b3b91e098c99fea1101c04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100093090"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>Conectar os logs de atividades do Perimeter 81 ao Azure Sentinel

> [!IMPORTANT]
> Atualmente, o conector de dados do Perimeter 81 no Azure Sentinel está em versão prévia pública.
> Esse recurso é fornecido sem um Contrato de Nível de Serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como conectar seu dispositivo de [logs de atividades do Perimeter 81](https://www.perimeter81.com/) ao Azure Sentinel. O conector de logs de atividades do Perimeter 81 permite que você traga com facilidade seus dados do Perimeter 81 para o Azure Sentinel, de modo que possa vê-los em pastas de trabalho, usá-los para criar alertas personalizados e incorporá-los para aprimorar a investigação.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do workspace no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você precisa ter permissões de leitura e gravação no workspace do Azure Sentinel.

- Você precisa ter permissões de leitura nas chaves compartilhadas do workspace.

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>Configurar e conectar logs de atividades do Perimeter 81

Os logs de atividades do Perimeter 81 podem integrar os logs diretamente ao Azure Sentinel, além de exportá-los.

1. No portal do Azure Sentinel, clique em **Conectores de dados** no menu de navegação.

1. Selecione os **Logs de atividades do Perimeter 81** na galeria e clique no botão **Abrir página do conector**.

1. Na página do conector dos logs de atividades do Perimeter 81, copie a **ID do Workspace** e a **Chave Primária** e cole-as no Perimeter 81, [conforme instruído aqui](https://support.perimeter81.com/hc/en-us/articles/360012680780).

1. Depois de concluir as instruções, você verá os tipos de dados conectados na página do conector do Azure Sentinel.

## <a name="find-your-data"></a>Encontrar seus dados

Depois que uma conexão bem-sucedida for estabelecida, os dados serão exibidos nos **Logs** em **CustomLogs** - **Perimeter81_CL**.

Pode levar até 20 minutos até que os logs comecem a ser exibidos.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Perimeter 81 ao Azure Sentinel. Para aproveitar ao máximo as funcionalidades integradas a esse conector de dados, clique na guia **Próximas etapas** na página do conector de dados. Nela, você encontrará uma pasta de trabalho predefinida e algumas consultas de exemplo para começar a encontrar informações úteis.

Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
