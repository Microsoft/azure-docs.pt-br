---
title: Conectar dados do Alcide kAudit ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Alcide kAudit ao Azure Sentinel.
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
ms.openlocfilehash: 6d1314b79f09f1bed2de5d6964b0a6224b5962ca
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096864"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Conectar seu Alcide kAudit ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados Alcide kAudit no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) ajuda a identificar comportamentos irregulares de kubernetes e a concentrar-se em violações e incidentes de kubernetes, ao mesmo tempo em que reduz o período de detecção Este artigo explica como conectar sua solução Alcide kAudit ao Azure Sentinel. O conector de dados Alcide kAudit permite que você traga facilmente seus dados de log do kAudit para o Azure Sentinel, para que você possa exibi-los em pastas de trabalho, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. A integração entre o Alcide kAudit e o Azure Sentinel usa a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de leitura e gravação no espaço de trabalho do Azure Sentinel.

- Você deve ter permissões de leitura para chaves compartilhadas para o espaço de trabalho.

## <a name="configure-and-connect-alcide-kaudit"></a>Configurar e conectar Alcide kAudit

Alcide kAudit pode exportar logs diretamente para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **conectores de dados** no menu de navegação.

1. Selecione **Alcide kAudit** na galeria e, em seguida, clique no botão **abrir página do conector** .

1. Siga as instruções detalhadas fornecidas no [Guia de instalação do Alcide kAudit](https://get.alcide.io/hubfs/Azure%20Sentinel%20Integration%20with%20kAudit.pdf).

1. Quando solicitado pela ID do espaço de trabalho e a chave primária, você pode copiá-las da página do conector de dados do Alcide kAudit.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="ID do espaço de trabalho e chave primária":::

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs** sob os seguintes tipos de dados no **CustomLogs**:

- detecções de kAudit de **alcide_kaudit_detections_1_CL** Alcide 
- logs de atividades **alcide_kaudit_activity_1_CL** -Alcide kaudit
- contagens de atividades de kAudit de **alcide_kaudit_selections_count_1_CL** Alcide
- detalhes da atividade do **alcide_kaudit_selections_details_1_CL** -Alcide kaudit

Para usar o esquema relevante nos logs para o Alcide kAudit, procure os tipos de dados mencionados acima.

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Alcide kAudit ao Azure sentinela. Para aproveitar ao máximo os recursos integrados a esse conector de dados, clique na guia **próximas etapas** na página conector de dados. Lá, você encontrará algumas consultas de exemplo prontas para que possa começar a localizar informações úteis.

Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
