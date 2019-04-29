---
title: Publicar oferta de módulo do Azure IoT Edge | Microsoft Docs
description: Como publicar uma oferta de módulo do IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: dfa512a26334567301812ecefd8c5673b0ea4094
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60916136"
---
# <a name="publish-iot-edge-module-offer"></a>Publicar oferta de módulo do IoT Edge

 Depois de criar uma nova oferta, fornecendo as informações sobre o **nova oferta** página, você pode publicar a oferta. Selecione **publicar** para iniciar o processo de publicação.

O diagrama a seguir mostra as principais etapas no processo de publicação para uma oferta para "entrar no ar".

![Etapas de publicação para o módulo do IoT Edge da oferta](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Descrição detalhada de etapas de publicação

A tabela a seguir descreve cada etapa de publicação, com um tempo estimado (máximo) para concluir cada etapa.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Etapa de publicação**           | **Hora**    | **Descrição**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validar os pré-requisitos         | 15 min   | Informações de oferta e configurações de oferta são validadas.                        |
| Certificação                  | 2 semanas | A oferta é analisada pela equipe de certificação do Azure. Esta etapa realizará varreduras por vírus, malware, conformidade de segurança e problemas de segurança. Ele verificará se essa oferta de módulo do IoT Edge atende a todos os critérios de qualificação (consulte [pré-requisitos](./cpp-prerequisites.md) e [Preparando seus ativos técnicos](./cpp-create-technical-assets.md)). Comentários serão fornecidos se um problema for encontrado. |
| Empacotamento | 1 hora  | Os recursos técnicos da oferta são empacotados para uso do cliente e os sistemas de leads são configurados e configurados. |
|  O editor termina             |  -        | Revisão final do editor e confirmação antes que a oferta seja publicada. Você pode implantar sua oferta nas assinaturas selecionadas (nas etapas a informações de oferta) para verificar se ele atende a todos os seus requisitos.  Selecione **Go Live** para que sua oferta possa passar para a próxima etapa. |
| Empacotamento                 | 1 hora | A oferta finalizada é replicada nos sistemas e regiões de produção do mercado. | 
| Live                           | 4 dias |A oferta é lançada, replicada para as regiões exigidas e disponibilizada ao público. |

Aguarde até 10 dias úteis para que o processo de publicação seja concluído e a oferta seja liberada. Depois de concluir o processo de publicação, a oferta do módulo IoT Edge será listada no [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Próximas etapas

- [Atualizar uma oferta de módulo do IoT Edge existente no Azure Marketplace](./cpp-update-existing-offer.md)
