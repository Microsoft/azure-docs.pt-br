---
title: Atualizar uma oferta de aplicativo do Power BI - Azure Marketplace | Microsoft Docs
description: Atualize uma oferta de Aplicativo do Power BI após ela ter sido publicada no Microsoft AppSource Marketplace.
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
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 953c53b4ef2244b3444abb745f52e125306468f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725510"
---
# <a name="update-a-power-bi-app-offer"></a>Atualizar uma oferta de aplicativo do Power BI

Este artigo mostra como atualizar sua oferta de aplicativo do Microsoft Power BI na [Portal do Cloud Partner](https://cloudpartner.azure.com/) e, em seguida, republicar sua oferta. Aqui estão algumas razões comuns para atualizar uma oferta:

- Para atualizar o conteúdo do aplicativo no Power BI e obter uma URL de instalação do aplicativo empacotado recentemente
- Para atualizar os metadados do Azure Marketplace da oferta (vendas, marketing, ou informações de suporte e ativos)
 
Usar o portal **Compare** e **histórico** recursos para controlar as alterações como esses.

## <a name="common-update-operations"></a>Operações comuns de atualização

Alguns atributos de uma oferta de aplicativo do Power BI não podem ser alterados depois que a oferta é publicada no AppSource. As alterações não permitidas incluem **ID da oferta** e **ID do publicador**. Mas você ainda pode alterar uma ampla variedade de características. Aqui estão algumas alterações comuns.

### <a name="update-app-content-in-power-bi"></a>Atualizar o conteúdo do aplicativo no Power BI

Aplicativos do Power BI geralmente são atualizados periodicamente com novo conteúdo, patches de segurança, recursos e assim por diante. Quando um aplicativo é alterado, atualize sua URL de instalação seguindo estas etapas:

1.  Entre no [Portal de Parceiros de Nuvem](https://cloudpartner.azure.com/).
2.  Em **Todas as ofertas**, localize a oferta que você gostaria de atualizar.
3.  Sobre o **informações técnicas** , insira uma nova URL de instalação.
4.  Selecione **publicar** para iniciar o fluxo de trabalho para publicar a nova versão do aplicativo no AppSource.


### <a name="update-the-offers-marketplace-metadata"></a>Atualizar os metadados do Marketplace da oferta

Metadados do Marketplace da sua oferta incluem o nome da empresa, logotipos e outras informações. Para atualizar os metadados, siga estas etapas:

1.  Entre no [Portal de Parceiros de Nuvem](https://cloudpartner.azure.com/).
2.  Em **Todas as ofertas**, localize a oferta que você gostaria de atualizar.
3.  Sobre o **detalhes da vitrine eletrônica** , altere os metadados, seguindo as instruções no artigo [guia de detalhes da vitrine eletrônica de aplicativo do Power BI](./cpp-storefront-details-tab.md).
4.  Selecione **Publicar** para iniciar o fluxo de trabalho para publicar suas alterações.


## <a name="the-compare-feature"></a>O recurso de comparação

Conforme você atualiza uma oferta publicada, você pode verificar as alterações feitas. Para usar o **comparar** recurso:

1.  Em qualquer ponto no processo de edição, selecione a oferta **comparar** botão.

    ![Comparar botão](./media/compare-feature-button.png)

2.  Exibir versões lado a lado de ativos de marketing e metadados.


## <a name="history-of-publishing-actions"></a>Histórico de ações de publicação

Para exibir sua oferta de publicação do histórico, no lado esquerdo do Portal do Cloud Partner, abra o **histórico** guia. Aqui você verá que um histórico das ações de carimbo de data / hora em que o AppSource oferece.

## <a name="next-steps"></a>Próximas etapas

No Portal do Cloud Partner, usam regularmente [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) para encontrar informações úteis sobre seus clientes Marketplace e uso.  
