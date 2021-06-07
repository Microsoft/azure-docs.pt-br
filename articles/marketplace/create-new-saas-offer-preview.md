---
title: Como adicionar um público de visualização para sua oferta de SaaS no Microsoft Commercial Marketplace
description: Como adicionar um público de visualização para sua oferta de SaaS (software como serviço) no Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 041df9b77e85f09d2cf680773edc995c6288acaa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96746413"
---
# <a name="how-to-add-a-preview-audience-for-your-saas-offer"></a>Como adicionar um público de visualização para sua oferta de SaaS

Ao criar sua oferta de SaaS (software como serviço) no Partner Center, você precisa definir um público-alvo que pode revisar sua listagem de oferta antes de entrar em tempo real. Este artigo explica como configurar um público de visualização.

> [!NOTE]
> Se você optar por processar as transações de forma independente, essa opção não será exibida. Em vez disso, pule para [como vender sua oferta de SaaS](create-new-saas-offer-marketing.md).

## <a name="define-a-preview-audience"></a>Definir um público de visualização

Na guia **público de visualização** , você pode definir um público limitado que pode examinar sua oferta de SaaS antes de publicá-lo em tempo real para o público do Marketplace mais amplo. Você pode enviar convites para endereços de email da MSA (conta da Microsoft) ou do Azure Active Directory (Azure AD). Adicione um mínimo de um e até 10 endereços de email manualmente ou importe até 20 com um arquivo. csv. Você pode atualizar a lista de audiências de visualização a qualquer momento.

> [!NOTE]
> Um público-alvo de versão prévia difere de um público-alvo privado. Um público de visualização tem permissão de acesso à sua oferta antes de ser publicado em tempo real nas lojas online. Você também pode optar por criar um plano e disponibilizá-lo somente para um público-alvo privado. Os planos privados são explicados em [como criar planos para sua oferta de SaaS](create-new-saas-offer-plans.md).

### <a name="add-email-addresses-manually"></a>Adicionar endereços de email manualmente

1. Na página do **público de visualização** , adicione um único endereço de email do Azure ad ou MSA e uma descrição opcional nas caixas fornecidas.
1. Para adicionar outro endereço de email, selecione o link **Adicionar outro email** .
1. Selecione **salvar rascunho** antes de continuar para a próxima guia: **configuração técnica**.
1. Continue a [adicionar detalhes técnicos para sua oferta de SaaS](create-new-saas-offer-technical.md).

### <a name="add-email-addresses-using-the-csv-file"></a>Adicionar endereços de email usando o arquivo CSV

1. Na página **público de visualização** , selecione o link **Exportar audiência (CSV)** .
1. Abra o. Arquivo CSV em um aplicativo, como o Microsoft Excel.
1. No. Arquivo CSV, na coluna **ID** , insira os endereços de email que você deseja adicionar ao público de visualização.
1. Na coluna **Descrição** , você pode opcionalmente adicionar uma descrição para cada endereço de email.
1. Na coluna **tipo** , adicione **MixedAadMsaId** a cada linha que tenha um endereço de email.
1. Salve o arquivo como um. Arquivo CSV.
1. Na página **público de visualização** , selecione o link **importar audiência (CSV)** .
1. Na caixa de diálogo **confirmar** , selecione **Sim**.
1. Selecione o. Arquivo CSV e, em seguida, selecione **abrir**.
1. Selecione **salvar rascunho** antes de continuar para a próxima guia: **configuração técnica**.

## <a name="next-steps"></a>Próximas etapas

- [Como adicionar detalhes técnicos para sua oferta de SaaS](create-new-saas-offer-technical.md)
