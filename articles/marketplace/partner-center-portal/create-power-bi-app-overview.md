---
title: Visão geral da criação de aplicativos Power BI-Microsoft AppSource
description: Este artigo descreve as etapas de alto nível para publicar um aplicativo de Power BI para Microsoft AppSource. Os requisitos técnicos e comerciais que seu aplicativo Power BI deve atender para serem publicados no mercado comercial também são fornecidos.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 04/02/2020
ms.openlocfilehash: b02c5bbf2e6accbea9c7a33dc26c4e89e46bb697
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679141"
---
# <a name="power-bi-app-creation-overview"></a>Visão geral da criação de aplicativos Power BI

Este artigo explica como publicar um aplicativo Power BI no Microsoft [AppSource](https://appsource.microsoft.com/). Um aplicativo Power BI empacota conteúdo personalizável, incluindo conjuntos de valores, relatórios e painéis. Em seguida, você pode usar o aplicativo com outras plataformas Power BI usando o AppSource, executar os ajustes e as personalizações permitidos pelo desenvolvedor e conectá-lo aos seus próprios dados.

## <a name="publishing-benefits"></a>Benefícios da publicação

Benefícios da publicação no Marketplace comercial:

- Promova sua empresa usando a marca Microsoft.
- Pode alcançar mais de 100 milhões usuários Microsoft 365 e Dynamics 365 em AppSource e mais de 200.000 organizações por meio do Azure Marketplace.
- Receba clientes potenciais de alta qualidade desses Marketplaces.
- Faça com que seus serviços sejam promovidos pelas equipes de campo e televendas da Microsoft.

## <a name="overview"></a>Visão geral

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="Visão geral das etapas para publicar um aplicativo Power BI" border="false":::

Estas são as principais etapas de publicação:

1. Crie seu aplicativo no Power BI. Você receberá um link de instalação de pacote, que é o principal ativo técnico para a oferta. Envie o pacote de teste para pré-produção antes de criar a oferta no Partner Center. Para obter detalhes, consulte [o que são aplicativos Power bi?](/power-bi/service-template-apps-overview)
2. Adicione os materiais de marketing, como nome oficial, descrição e logotipos.
3. Inclua os documentos legais e de suporte da oferta, como termos de uso, política de privacidade, política de suporte e ajuda do usuário.
4. Crie a oferta – use o Partner Center para editar os detalhes, incluindo a descrição da oferta, materiais de marketing, informações legais, informações de suporte e especificações de ativos.
5. Envie-o para publicação.
6. Monitore o processo no Partner Center, em que a equipe de integração do AppSource testa, valida e certifica seu aplicativo.
7. Depois de certificado, examine o aplicativo em seu ambiente de teste e libere-o. Isso irá listá-lo em AppSource (ele "fica ativo").
8. Em Power BI, envie o pacote para produção. Para obter detalhes, consulte [gerenciar a versão do aplicativo Power bi](/power-bi/service-template-apps-create#manage-the-template-app-release).

## <a name="before-you-begin"></a>Antes de começar

Examine os links abaixo, que fornecem modelos, dicas e exemplos.

- [Criar um aplicativo do Power BI](/power-bi/service-template-apps-create)
- [Dicas para criar um aplicativo Power BI](/power-bi/service-template-apps-tips)
- [Amostras](/power-bi/service-template-apps-samples)

## <a name="requirements"></a>Requisitos

Para ser publicado no Marketplace comercial, sua oferta de aplicativo Power BI deve atender aos seguintes requisitos técnicos e de negócios.

### <a name="technical-requirements"></a>Requisitos técnicos

O principal ativo técnico necessário é um [aplicativo Power bi](/power-bi/connect-data/service-template-apps-overview). Trata-se de uma coleção de conjuntos de gráficos, relatórios ou painéis primários. Ele também inclui serviços conectados opcionais e conjuntos de valores incorporados, anteriormente conhecidos como um [pacote de conteúdo](/power-bi/service-organizational-content-pack-introduction). Para obter mais informações sobre como desenvolver esse tipo de aplicativo, consulte [o que são aplicativos Power bi?](/power-bi/connect-data/service-template-apps-overview).

#### <a name="get-an-installation-web-address"></a>Obter um endereço da Web de instalação

Você só pode compilar um aplicativo Power BI dentro do ambiente de [Power bi](https://powerbi.microsoft.com/) .

1. Entre com uma [licença Power bi pro](/power-bi/service-admin-purchasing-power-bi-pro).
2. Crie e teste seu aplicativo no Power BI.
3. Ao receber o endereço Web de instalação do aplicativo, adicione-o à página de **configuração técnica** no Partner Center.

Depois que o aplicativo for criado e testado no Power BI, salve o endereço Web de instalação do aplicativo, pois você precisará dele para [criar uma oferta de aplicativo Power bi](create-power-bi-app-offer.md).

### <a name="business-requirements"></a>Requisitos empresariais

Os requisitos de negócios incluem o procedimento, o contratual e as obrigações legais. Você deve:

- Seja um editor de Marketplace comercial registrado. Se você não estiver registrado, siga as etapas em [criar uma conta do Marketplace comercial no Partner Center](create-account.md).
- Forneça o conteúdo que atende aos critérios para sua oferta a ser listada em AppSource. Para obter mais informações, consulte fazer com que [um aplicativo seja listado no AppSource? Veja como](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how).
- Concorde e siga a [declaração de privacidade da Microsoft](https://privacy.microsoft.com/privacystatement).

## <a name="next-step"></a>Próxima etapa

- [Criar uma oferta de aplicativo Power BI no Partner Center](create-power-bi-app-offer.md)
