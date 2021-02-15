---
title: Conectar dados do perímetro 81 ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do perímetro 81 ao Azure Sentinel.
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
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093090"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>Conectar seus logs de atividade do perímetro 81 ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados do perímetro 81 no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como conectar seu dispositivo de [logs de atividades do perímetro 81](https://www.perimeter81.com/) ao Azure Sentinel. O conector de logs de atividade do perímetro 81 permite que você traga facilmente seus dados do perímetro 81 para o Azure Sentinel, para que você possa exibi-los em pastas de trabalho, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de leitura e gravação no espaço de trabalho do Azure Sentinel.

- Você deve ter permissões de leitura para chaves compartilhadas para o espaço de trabalho.

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>Configurar e conectar logs de atividade do perímetro 81

Os logs de atividade do perímetro 81 podem integrar e exportar logs diretamente para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **conectores de dados** no menu de navegação.

1. Selecione os **logs de atividade do perímetro 81** da galeria e clique no botão **abrir página do conector** .

1. Na página conector de logs de atividades do perímetro 81, copie a **ID do espaço de trabalho** e a **chave primária** e cole-as no perímetro 81, [conforme instruído aqui](https://support.perimeter81.com/hc/en-us/articles/360012680780).

1. Depois de concluir as instruções, você verá os tipos de dados conectados na página conector do Azure Sentinel.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs** em **CustomLogs**  -  **Perimeter81_CL**.

Pode levar até 20 minutos até que os logs comecem a aparecer.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar os logs de atividade do perímetro 81 ao Azure Sentinel. Para aproveitar ao máximo os recursos integrados a esse conector de dados, clique na guia **próximas etapas** na página conector de dados. Lá, você encontrará uma pasta de trabalho pronta e algumas consultas de exemplo para que possa começar a localizar informações úteis.

Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
