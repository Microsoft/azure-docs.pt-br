---
title: "Guia para a criação de um modelo de solução para o Marketplace | Microsoft Docs"
description: "Instruções detalhadas sobre como criar, certificar e implantar um Modelo de Solução de imagem com múltiplas VMs para compra no Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: b753bfb4bd69bd9aacf4eebd8999397394c28bc4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Guia para criar um modelo de solução para o Azure Marketplace
Após concluir a etapa 1, [Criação da conta e registro][link-acct-creation], falamos sobre a criação de um modelo de solução compatível com o Azure em [Pré-requisitos técnicos para criar um modelo de solução](marketplace-publishing-solution-template-creation-prerequisites.md). Agora, orientaremos você pelas etapas de criação de um modelo de solução para várias VMs no [Portal de Publicação][link-pubportal] para o Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Criar sua oferta de modelo de solução no Portal de Publicação
Acesse [https://publish.windowsazure.com](http://publish.windowsazure.com). Ao fazer logon pela primeira vez no [Portal de Publicação](https://publish.windowsazure.com/), use a mesma conta utilizada para o registro de perfil de vendedor de sua empresa. Posteriormente, você poderá adicionar qualquer funcionário de sua empresa como um coadministrador no Portal de Publicação.

### <a name="1-select-solution-templates"></a>1. Selecione "Modelos de solução"
  ![desenho][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Crie um novo modelo de solução
  ![desenho][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. Comece com as topologias
Um modelo de solução é um "pai" para todas as suas topologias. Você pode definir várias topologias em uma oferta/modelo de solução. Quando uma oferta passa para teste, todas as suas topologias a acompanham. Siga as etapas abaixo para definir sua oferta:     

* Criar uma topologia: geralmente, “Identificador de Topologia” é o nome da topologia para o modelo da solução. O identificador de topologia é usado na URL, como mostrado abaixo:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Portal do Azure: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}
* Adicionar uma nova versão.

### <a name="4-get-your-topology-versions-certified"></a>4. Certifique as versões de sua topologia
Carregue um arquivo zip contendo todos os arquivos necessários para provisionar essa versão específica da topologia. Este arquivo zip deve conter o seguinte:

* Arquivo *mainTemplate.json* e *createUiDefinition.json* no diretório raiz.
* Quaisquer modelos vinculados e todos os scripts necessários.

  > [!TIP]
  > Enquanto seus desenvolvedores trabalham na criação e certificação de topologias do modelo de solução, os departamentos comercial, de marketing e/ou jurídico de sua empresa podem trabalhar no conteúdo jurídico e de marketing.
  >
  >

## <a name="next-steps"></a>Próximas etapas
Agora que você criou seu modelo de solução e carregou o arquivo zip, siga as instruções no [guia de conteúdo de marketing do Marketplace](marketplace-publishing-push-to-staging.md) antes de passar a oferta para o preparo. Para ver o conjunto completo de artigos de publicação do Marketplace, acesse [Como publicar e gerenciar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md).

Você também pode se interessar por estes artigos relacionados:

* Imagens de VM: [Sobre Imagens da Máquina Virtual no Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* Extensões de VM: [Visão geral sobre Agente de VM](https://msdn.microsoft.com/library/azure/dn832621.aspx) e [Extensões de VM e Extensões de VM e recursos do Azure](https://msdn.microsoft.com/library/azure/dn606311.aspx)
* Azure Resource Manager: [Criando modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) e [Exemplos de modelos simples](https://github.com/rjmax/ArmExamples)
* Limites da conta de armazenamento: [como monitorar o limite da conta de armazenamento](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) e [Armazenamento Premium](../storage/common/storage-premium-storage.md#scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
