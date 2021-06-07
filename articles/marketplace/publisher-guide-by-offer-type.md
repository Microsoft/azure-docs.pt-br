---
title: Guia de publicação por tipo de oferta-Microsoft Commercial Marketplace
description: Este artigo descreve os tipos de oferta que estão disponíveis no Microsoft Commercial Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 10/06/2020
ms.openlocfilehash: 3be967b611f6b75705a60bfa14523832b0652931
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879217"
---
# <a name="publishing-guide-by-offer-type"></a>Guia de publicação por tipo de oferta

Este artigo descreve os tipos de oferta que estão disponíveis no mercado comercial. O *tipo de oferta* define a estrutura da oferta, que inclui os metadados, os artefatos e outros conteúdos apresentados no mercado comercial.

Depois [de decidir sobre uma opção de publicação](determine-your-listing-type.md), você deve escolher um tipo de oferta antes de começar a criar sua oferta no Partner Center. O tipo de oferta corresponderá ao tipo de solução, aplicativo, ou oferta de serviço que você deseja publicar, bem como seu alinhamento com produtos e serviços Microsoft.

Você pode configurar um único tipo de oferta de maneiras diferentes para habilitar diferentes opções de publicação, a opção de listagem, o provisionamento ou o preço. A opção de publicação e a configuração do tipo de oferta também são alinhados aos requisitos técnicos e de qualificação da oferta.

Certifique-se de examinar a loja online e oferecer requisitos de qualificação de tipo e os requisitos de publicação técnica antes de criar sua oferta.

## <a name="list-of-offer-types"></a>Lista de tipos de oferta

A tabela a seguir mostra os tipos de oferta do Marketplace comercial no Partner Center.

| **Tipo de oferta**    | **Descrição**  |
| :------------------- | :-------------------|
| [**Aplicativo do Azure**](plan-azure-application-offer.md) | Há dois tipos de planos de aplicativo do Azure: _modelo de solução_ e _aplicativo gerenciado_. Ambos os tipos de plano dão suporte à automatização da implantação e configuração de uma solução além de uma única VM (máquina virtual). Você pode automatizar o processo de fornecimento de vários recursos, incluindo VMs, rede e recursos de armazenamento para fornecer soluções complexas, como soluções de IaaS. Ambos os tipos de plano podem empregar vários tipos diferentes de recursos do Azure, incluindo, mas não se limitando a VMs.<ul><li>Os planos de **modelo de solução** são uma das principais maneiras de publicar uma solução no Marketplace comercial. Os planos de modelo de solução não são pagos no mercado comercial, mas podem ser usados para implantar ofertas de VM pagas que são cobradas por meio do Marketplace comercial. Use o tipo de plano de modelo de solução quando o cliente gerenciar a solução e as transações forem cobradas por meio de outro plano.</li><br><li>Os planos de **aplicativos gerenciados** permitem que você crie e forneça facilmente aplicativos prontos para uso e totalmente gerenciados para seus clientes. Eles têm os mesmos recursos que os planos de modelo de solução, com algumas diferenças importantes:</li><ul><li> Os recursos são implantados em um grupo de recursos e são gerenciados pelo editor do aplicativo. O grupo de recursos está presente na assinatura do consumidor, mas uma identidade no locatário do fornecedor tem acesso ao grupo de recursos.</li><li>Como o Publicador, você especifica o custo para o suporte contínuo da solução e as transações têm suporte por meio do Marketplace comercial.</li></ul>Use o tipo de plano de aplicativo gerenciado quando você ou seu cliente exigir que a solução seja gerenciada por um parceiro ou se você implantar uma solução baseada em assinatura.</ul> |
| [**Contêiner do Azure**](marketplace-containers.md) | Use o tipo de oferta de contêiner do Azure quando sua solução for uma imagem de contêiner do Docker provisionado como um serviço de contêiner do Azure baseado em kubernetes. |
| [**Máquina virtual do Azure**](marketplace-virtual-machines.md) | Use o tipo de oferta de Máquina Virtual quando implantar um dispositivo virtual na assinatura associada ao cliente. |
| [**Serviço de consultoria**](./plan-consulting-service-offer.md) | Os serviços de consultoria ajudam a conectar clientes com serviços para dar suporte e estender o uso dos serviços Azure, Dynamics 365 ou Power Suite.|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | Você pode publicar ofertas de AppSource que criam ou estendem o Dynamics 365 Business central, o compromisso com o cliente do Dynamics 365, os aplicativos de energia e os aplicativos de finanças e operações.|
| [**Módulo do IoT Edge**](iot-edge-module.md) | Os módulos do Azure IoT Edge são as menores unidades de computação gerenciadas pelo IoT Edge e podem conter serviços da Microsoft (como o Azure Stream Analytics), serviços de terceiros ou o código específico da sua própria solução. |
| [**Serviço gerenciado**](./plan-managed-service-offer.md) | Você pode criar ofertas de serviço gerenciado e gerenciar assinaturas delegadas pelo cliente ou grupos de recursos por meio [do Azure Lighthouse](../lighthouse/overview.md).|
| [**Power bi aplicativo** <br/> **Microsoft 365**](appsource-offer-publishing-guide.md) | Você pode publicar ofertas de AppSource que se baseiam ou estendem Power BI e Microsoft 365.|
| [**Software como serviço**](plan-saas-offer.md) | Use o tipo de oferta SaaS (software como serviço) para permitir que o cliente compre sua solução técnica baseada em SaaS como uma assinatura. Para obter informações sobre os requisitos de logon único para ofertas de SaaS, consulte [Azure AD e ofertas de SaaS transactáveis no Marketplace comercial](azure-ad-saas.md). |


## <a name="next-steps"></a>Próximas etapas

- Examine os requisitos de qualificação no artigo correspondente para seu tipo de oferta para finalizar a seleção e a configuração de sua oferta.
- Examine os padrões de publicação de cada loja online para obter exemplos de como sua solução é mapeada para um tipo de oferta e configuração.