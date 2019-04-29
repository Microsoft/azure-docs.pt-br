---
title: Publique uma oferta de máquina virtual no Azure Marketplace | Microsoft Docs
description: Lista as etapas necessárias para publicar uma oferta de máquina virtual existente no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/17/2018
ms.author: pbutlerm
ms.openlocfilehash: 3cf6a3d9bcb9470fd3a6bd4fef964c1966adfa1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844235"
---
# <a name="publish-a-virtual-machine-offer"></a>Publicar uma oferta de máquina virtual

 A última etapa, depois de definir a oferta no portal e criar os recursos técnicos associados, é enviar a oferta para publicação. O diagrama a seguir descreve as principais etapas do processo de publicação para "entrar em operação":

![Etapas de publicação para a máquina virtual da oferta](./media/publishvm_013.png)

A tabela a seguir descreve essas etapas e fornece uma estimativa de tempo máximo para sua conclusão:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Etapa de publicação**           | **Hora**    | **Descrição**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validar os pré-requisitos         | 15 min   | Informações de oferta e configurações de oferta são validadas.                        |
| Teste de unidade validação (opcional) | 2 horas | Se você tiver selecionado para habilitar o Test Drive, a Microsoft valida a configuração do Test Drive, sua implantação e replicação através das regiões selecionadas. |
| Certificação                  | 3 dias | A oferta é analisada pela equipe de certificação do Azure. Esta etapa realizará varreduras por vírus, malware, conformidade de segurança e problemas de segurança. Comentários serão fornecidos se um problema for encontrado. |
| Provisionamento                   | 4 dias   | A oferta de VM é replicada nos sistemas de produção de mercado.               |
| Registro de embalagem e geração de leads | \< 1 hora  | Os recursos técnicos da oferta são empacotados para uso do cliente e os sistemas de leads são configurados e configurados. |
|  Aprovação do publicador             |  -        | Revisão final do editor e confirmação antes que a oferta seja publicada. Você pode implantar sua oferta nas assinaturas selecionadas (nas etapas a informações de oferta) para verificar se ele atende a todos os seus requisitos.  |
| Provisionamento                   | 4 dias | A oferta de VM finalizada é replicada em sistemas e regiões de produção de mercado. | 
| Live                           | 4 dias | A oferta de VM é lançada, replicada para as regiões necessárias e disponibilizada ao público. |
|  |  |

Permitem até 16 dias para a conclusão desse processo.  Depois que você percorrer essas etapas de publicação, sua oferta de VM será listada na [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

