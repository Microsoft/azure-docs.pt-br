---
title: Pré-requisitos de Oferta do Aplicativo Azure | Microsoft Docs
description: Pré-requisitos de publicação de uma oferta do Aplicativo Azure no Azure Marketplace.
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
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: df127eec662f8598246f276ef9b1608ae3021512
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744917"
---
# <a name="azure-application-prerequisites"></a>Pré-requisitos do Aplicativo Azure

Esse artigo descreve os pré-requisitos técnicos e comerciais para a publicação de uma oferta de aplicativo gerenciado no Azure Marketplace.  Se você ainda não fez isso, examine as seguintes fontes de informações:
- Dependendo do seu tipo de SKU ou [aplicativos do Azure: Modelo de solução do guia de publicação da oferta](../../marketplace-solution-templates.md) ou [aplicativos do Azure: Guia de publicação de oferta de aplicativo gerenciado](../../marketplace-managed-apps.md)
- [Criação de modelos de solução e os aplicativos gerenciados para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) vídeo


## <a name="technical-requirements"></a>Requisitos técnicos

Os requisitos técnicos incluem os seguintes itens:

*   Modelos do Azure Resource Manager Para obter mais informações, confira [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Este artigo descreve a estrutura de um modelo do Azure Resource Manager. Ele apresenta as diferentes seções de um modelo e as propriedades que estão disponíveis nessas seções. O modelo consiste em JSON e expressões que podem ser usados na criação de valores para sua implantação. 
* Modelos de Início Rápido do Azure.<br> Para obter mais informações, consulte:

  * [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/). Implante recursos do Azure por meio do Azure Resource Manager com modelos fornecidos pela comunidade para fazer mais. O Gerenciador de Recursos do Azure permite que você provisione seus aplicativos usando um modelo declarativo. Em um único modelo, você pode implantar diversos serviços junto com suas dependências. Use o mesmo modelo para implantar repetidamente seu aplicativo durante cada estágio do ciclo de vida do aplicativo.
  * [GitHub: Modelos de Início Rápido do Azure Resource Manager](https://github.com/azure/azure-quickstart-templates). Esse repositório contém todos os modelos do Azure Resource Manager atualmente disponíveis enviados pela comunidade. Um índice de pesquisa de modelo é mantido no https://azure.microsoft.com/en-us/documentation/templates/.
* Criar uma definição de interface do usuário<br>
Para obter mais informações, confira [Criar uma interface do usuário do portal do Azure para seu aplicativo gerenciado](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). Esse artigo apresenta os conceitos básicos do arquivo createUiDefinition.json. O portal do Azure usa este arquivo para gerar a interface do usuário para a criação de um aplicativo gerenciado.


## <a name="business-requirements"></a>Requisitos de negócios

Os requisitos comerciais incluem as seguintes obrigações contratuais, legais e de procedimentos:

* Você deve ser um Editor de Marketplace de Nuvem registrado. Se você não estiver registrado, siga as etapas no artigo [se tornar um editor do Marketplace de nuvem](../../become-publisher.md).

>[!NOTE]
>Você deve usar a mesma conta de registro do Microsoft Developer Center para entrar no Portal do Cloud Partner. Você deve ter apenas uma conta da Microsoft para suas ofertas do Azure Marketplace. Esta conta não deve ser específica para serviços ou ofertas individuais.

* Sua empresa (ou sua subsidiária) deve estar em um país de origem de venda ao qual o Azure Marketplace dá suporte. Para obter uma lista atual desses países, consulte [Políticas de Participação do Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* O produto deve ser licenciado de forma que seja compatível com modelos de cobrança com suporte no Azure Marketplace. Para obter mais informações, confira [opções de cobrança](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) no Azure Marketplace.
* Você é responsável por disponibilizar o suporte técnico a clientes de modo comercialmente razoável. Esse suporte pode ser gratuito, pago ou por meio de abordagens de comunidade.
* Você é responsável pelo licenciamento do software e quaisquer dependências de software de terceiros.
* Você deve fornecer conteúdo que atenda aos critérios da oferta a serem listados no Azure Marketplace e no portal do Azure.
* Você deve concordar com os termos do Contrato de Publicador e Políticas de Participação do Microsoft Azure Marketplace.
* Você deve estar em conformidade com os Termos de Uso do Site do Microsoft Azure, Declaração de Privacidade da Microsoft e Contrato de Programa de Certificação do Microsoft Azure.


## <a name="publishing-requirements"></a>Publicação de requisitos

Para publicar uma nova oferta do Aplicativo Azure, você deve cumprir os seguintes pré-requisitos:

* Tenha seus metadados prontos para uso. A lista a seguir (não exaustiva) mostra um exemplo desses metadados:
  * Um título
  * Uma descrição (no formato HTML)
  * Uma imagem de logotipo (no formato PNG) e nesses tamanhos de imagem fixos: 40 x 40 pixels, 90 x 90 pixels, 115 x 115 pixels e 255 x 115 pixels.
* Um *termos de uso* e uma *política de privacidade* documentos
* Documentação do aplicativo
* Contatos de suporte


## <a name="next-steps"></a>Próximas etapas

Depois que você cumpriu todos os requisitos, você estará pronto para [criar uma oferta de aplicativo do Azure](./cpp-create-offer.md). 
 
