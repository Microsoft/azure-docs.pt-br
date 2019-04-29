---
title: Oferta de BI App Power - Azure Marketplace | Microsoft Docs
description: Como publicar um aplicativo Power BI no Marketplace do Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
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
ms.date: 03/27/2019
ms.author: pbutlerm
ms.openlocfilehash: f18a1b05e5a38a79945d8df6706dd2147d6b43df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725800"
---
# <a name="power-bi-app-offer"></a>Oferta de Aplicativo do Power BI

|              |                                |
|--------------|--------------------------------|
| Este artigo explica como publicar um aplicativo do Power BI da Microsoft [AppSource marketplace](https://appsource.microsoft.com/).  Um aplicativo do Power BI pacotes personalizável conteúdo do Power BI incluindo painéis, relatórios e conjuntos de dados. Em seguida, implantar o aplicativo em outros locatários do Power BI por meio do AppSource, executar a ajustes e personalizações permitidas pelo desenvolvedor e conectá-lo em seus próprios dados. | ![Ícone do Power BI](./media/powerbi-icon.png) |


Este artigo está dividido em três partes principais:

-   [Pré-requisitos](./cpp-prerequisites.md). Os requisitos técnicos e comerciais para criar e publicar uma oferta de aplicativo do Power BI.
-   [Criar uma oferta de aplicativo do Power BI](./cpp-create-offer.md). Como criar uma entrada de oferta de aplicativo do Power BI usando o [Portal do Cloud Partner](https://cloudpartner.azure.com).
-   [Publicar uma oferta de aplicativo do Power BI](./cpp-publish-offer.md). Como enviar uma nova oferta no AppSource para publicação e como atualizar uma oferta existente.


## <a name="publishing-steps"></a>Etapas de publicação

Aqui estão as etapas de alto nível para publicar uma oferta de aplicativo do Power BI:

![Etapas de publicação de oferta do Power BI App](media/publishing-steps.png)

Aqui está a oferta de aplicativo do Power BI no processo de publicação:

1. Crie um aplicativo de modelo no Power BI. Essa ação gera uma URL de instalação do pacote, que representa o ativo primário técnico para a oferta. Também neste momento, promova o pacote de teste para pré-produção. Para obter mais informações, consulte [o que são aplicativos de modelo do Power BI?](https://docs.microsoft.com/power-bi/service-template-apps-overview). 
2. Coletar ou criar materiais de marketing da oferta, incluindo: nome oficial, descrição, logotipos, etc. 
3. Coletar ou criar legal da oferta e dar suporte a documentos: *termos de uso*, *política de privacidade*, *política de suporte*, a Ajuda do usuário, etc.
4. Criar a oferta: usar o Portal do Cloud Partner para configurar os detalhes da oferta, incluindo a descrição da oferta, materiais de marketing, informações legais, informações de suporte e especificações de ativos.  Depois que a oferta for totalmente especificada, enviá-lo para publicação.
5. Monitore o processo de publicação no Portal do Cloud Partner.  Durante esta etapa, a equipe de integração do AppSource testa, valida e certifica que o seu aplicativo. 
6. Depois que o aplicativo for certificado com sucesso, revisá-lo em seu ambiente de teste e liberá-lo. 
7. O aplicativo do Power BI é listado no AppSource (it "vai ao vivo").
8. No Power BI, promova o pacote de pré-produção para produção. Para obter mais informações, consulte [gerenciar a versão do modelo de aplicativo](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).


## <a name="next-steps"></a>Próximas etapas

Antes de criar sua oferta de aplicativo do Power BI e publicá-lo no AppSource, você deve cumprir os [requisitos](./cpp-prerequisites.md) para publicar um aplicativo do Power BI no AppSource.
