---
title: Atualizar uma oferta existente de aplicativo do Azure | Microsoft Docs
description: Como atualizar uma oferta existente de aplicativo do Azure no Azure Marketplace.
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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 1019c06afbdab0a6b0e172133da316aea01a5504
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744611"
---
# <a name="update-an-existing-azure-application-offer"></a>Atualizar uma oferta existente de aplicativo do Azure

Há vários tipos de atualizações que você talvez queira fazer na oferta depois que ela for publicada e estiver ativa. Qualquer alteração que você fizer na nova versão da sua oferta deve ser salva e republicada para ser refletida no Marketplace. Este artigo descreve os diferentes aspectos da atualização da oferta de aplicativo gerenciado no [Portal do Cloud Partner](https://cloudpartner.azure.com/).

Há várias razões por que você talvez queira atualizar sua oferta, tais como:

- Adicionar uma nova versão de imagem aos SKUs existentes.
- Adicionando novo SKUs.
- Atualizar os metadados do mercado para a oferta ou SKUs individuais.

Para ajudá-lo nessas modificações, o portal fornece os recursos **Comparar** e **Histórico**.

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Alterações não permitidas em uma oferta de aplicativo do Azure ou um SKU

Há atributos de uma oferta de contêiner ou um SKU que não podem ser alterados depois que a oferta estiver ativa no Azure Marketplace. Você não pode alterar as configurações a seguir:

- ID da Oferta e ID do Publicador da oferta
- ID da SKU dos SKUs existentes
- Marcas de versão, por exemplo: `1.0.1`
- Alterações no modelo de cobrança/licença para SKUs existentes

## <a name="common-update-operations"></a>Operações comuns de atualização

As operações de inserção e atualização são comuns.

### <a name="update-image-version-for-a-sku"></a>Atualizar a versão da imagem de um SKU

É comum que uma imagem seja atualizada periodicamente com patches de segurança, recursos adicionais e assim por diante. Nesse cenário, você deseja atualizar a imagem referenciada pelo SKU usando as seguintes etapas:

1. Entrar no [Portal do Cloud Partner](https://cloudpartner.azure.com/).
2. Em **Todas as ofertas**, localize a oferta que você gostaria de atualizar.
3. Na guia **SKUs**, selecione o SKU associado à imagem a ser atualizada.
4. Selecione **+ Nova Versão de Imagem** para adicionar uma nova imagem.
5. Forneça as novas versões de imagem. A versão da imagem precisa seguir as mesmas diretrizes de marcas como versões anteriores. As marcas de versão devem estar no formato X.Y.Z, onde X, Y e Z são inteiros. Verifique se a nova versão que você fornecer seja maior que todas as versões anteriores.
6. Clique em **Publicar** para iniciar o fluxo de trabalho e publicar sua nova versão de imagem de contêiner para o Azure Marketplace.

### <a name="add-a-new-sku"></a>Adicionar uma nova SKU

Use as etapas a seguir para disponibilizar uma nova SKU para sua oferta existente:

1. Entrar no [Portal do Cloud Partner](https://cloudpartner.azure.com/).
2. Em **Todas as ofertas**, localize a oferta que você gostaria de atualizar.
3. Na guia **SKUs**, selecione **Adicionar nova SKU** e forneça uma **ID DE SKU** na janela pop-up.
4. Publique a oferta novamente usando as etapas descritas em [Publicar oferta de aplicativo do Azure](./cpp-publish-offer.md).
5. Selecione **Publicar** para iniciar o fluxo de trabalho para publicar a sua nova SKU.

### <a name="update-offer-marketplace-metadata"></a>Atualizar metadados do marketplace da oferta

Utilize os seguintes passos para atualizar os metadados do mercado associados à sua oferta. (por exemplo: nome da empresa, logotipos, etc.)

1. Entrar no [Portal do Cloud Partner](https://cloudpartner.azure.com/).
2. Em **Todas as ofertas**, localize a oferta que você gostaria de atualizar.
3. Acesse a guia **Marketplace**. Use as instruções descritas em [Publicar oferta de aplicativo do Azure](./cpp-publish-offer.md) para fazer alterações nos metadados.
4. Selecione **Publicar** para iniciar o fluxo de trabalho para publicar suas alterações.
 
>[!Note]
>Cloud Solution Providers (CSP) parceiro canal participar agora está disponível.  Consulte [Cloud Solution Providers](../../cloud-solution-providers.md) para obter mais informações sobre sua oferta por meio do Microsoft CSP de marketing de parceiros canais.

## <a name="deleting-an-existing-offer"></a>Excluindo uma oferta existente

Você poderá decidir remover sua oferta do Marketplace. A exclusão de uma oferta não afeta as compras atuais dela. As compras do cliente continuarão funcionando como antes. No entanto, a oferta não estará disponível para novas compras depois que a exclusão for concluída.

O Encerramento de Oferta é o processo de encerrar o contrato de licenciamento e/ou serviço entre você e seus clientes existentes.
Diretrizes e políticas relacionadas à remoção e ao encerramento de ofertas são governadas pelo Contrato do Editor do Microsoft Marketplace (consulte a Seção 7) e pelas Políticas de Participação (consulte a Seção 6.2).

Para obter mais informações, confira [Excluir uma oferta/um SKU do Azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Recurso Comparar

Ao fazer alterações em uma oferta publicada, você pode usar o recurso Comparar para auditar as alterações feitas.

Para usar o recurso Comparar:

1. A qualquer momento do processo de edição, clique no botão Comparar para sua oferta.
2. Veja as versões lado a lado de metadados e ativos de marketing.

## <a name="history-of-publishing-actions"></a>Histórico de ações de publicação

Para ver a atividade de histórico de publicação, selecione a guia **Histórico** na barra de menu de navegação esquerda do Portal do Cloud Partner. Você pode ver as ações de data/hora executadas durante o tempo de vida das ofertas do Marketplace do Azure.

## <a name="next-steps"></a>Próximas etapas

[Oferta de aplicativo do Azure](./cpp-azure-app-offer.md)