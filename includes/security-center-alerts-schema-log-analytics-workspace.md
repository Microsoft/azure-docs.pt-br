---
title: incluir arquivo
description: incluir arquivo
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "79538825"
---
### <a name="the-data-model-of-the-schema"></a>O modelo de dados do esquema

|Campo|Descrição|
|----|----|
|**AlertName**|Nome de exibição do alerta|
|**AlertType**|identificador de alerta exclusivo|
|**ConfidenceLevel**|Adicional O nível de confiança deste alerta (alto/baixo)|
|**ConfidenceScore**|Adicional Indicador de confiança numérica do alerta de segurança|
|**Descrição**|Texto de descrição do alerta|
|**DisplayName**|O nome de exibição do alerta|
|**EndTime**|A hora de término do alerta (a hora do último evento que contribui para o alerta)|
|**Entidades**|Uma lista de entidades relacionadas ao alerta. Esta lista pode conter uma mistura de entidades de tipos diferentes|
|**ExtendedLinks**|Adicional Um recipiente para todos os links relacionados ao alerta. Esta bolsa pode conter uma mistura de links para diversos tipos|
|**ExtendedProperties**|Um conjunto de campos adicionais que são relevantes para o alerta|
|**Isincident**|Determina se o alerta é um incidente ou um alerta regular. Um incidente é um alerta de segurança que agrega vários alertas em um incidente de segurança|
|**ProcessingEndTime**|Carimbo de data/hora UTC em que o alerta foi criado|
|**ProductComponentName**|Adicional O nome de um componente dentro do produto que gerou o alerta.|
|**ProductName**|constante (' Central de segurança do Azure ')|
|**ProviderName**|unused|
|**RemediationSteps**|Itens de ação manual a serem necessários para corrigir a ameaça de segurança|
|**ResourceId**|Identificador completo do recurso afetado|
|**Gravidade**|A severidade do alerta (alta/média/baixa/informativa)|
|**SourceComputerId**|um GUID exclusivo para o servidor afetado (se o alerta for gerado no servidor)|
|**SourceSystem**|unused|
|**StartTime**|A hora de início do alerta (a hora do primeiro evento que contribui para o alerta)|
|**SystemAlertId**|Identificador exclusivo desta instância de alerta de segurança|
|**TenantId**|o identificador do locatário pai do Active Directory do Azure da assinatura sob a qual o recurso verificado reside|
|**TimeGenerated**|Carimbo de data/hora UTC em que a avaliação ocorreu (tempo de verificação da central de segurança) (idêntica a DiscoveredTimeUTC)|
|**Tipo**|constante (' SecurityAlert ')|
|**Nome_do_Fornecedor**|O nome do fornecedor que forneceu o alerta (por exemplo, ' Microsoft ')|
|**VendorOriginalId**|unused|
|**WorkspaceResourceGroup**|caso o alerta seja gerado em uma VM, servidor, conjunto de dimensionamento de máquinas virtuais ou instância do serviço de aplicativo que relate a um espaço de trabalho, contenha o nome do grupo de recursos do espaço de trabalho|
|**WorkspaceSubscriptionId**|caso o alerta seja gerado em uma VM, servidor, conjunto de dimensionamento de máquinas virtuais ou instância do serviço de aplicativo que relate a um espaço de trabalho, contenha o espaço de trabalho SubscriptionId|
|||
