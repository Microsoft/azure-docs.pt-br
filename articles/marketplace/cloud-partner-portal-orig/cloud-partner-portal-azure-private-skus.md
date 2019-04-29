---
title: SKUs Privados e Planos | Microsoft Docs
description: Como usar SKUs privados para gerenciar a disponibilidade de oferta.
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: dcc8b9fef75ba13b607d7fdda5bd55cc710165b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561867"
---
<a name="private-skus-and-plans"></a>SKUs Privados e Planos
============

SKUs privados permitem restringir a disponibilidade de SKUs para clientes específicos. Quando um SKU é marcado como privado, ele não está disponível em nenhum catálogo público, incluindo no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [portal do Azure](https://portal.azure.com). No portal do Azure, somente os clientes com acesso ao SKU podem vê-lo. Além disso, eles também receberiam um aviso de que têm acesso a ofertas privadas.

>[!NOTE]
>SKUs privados devem ter novas IDs de plano/SKU exclusivas para evitar qualquer conflito com seus SKUs públicos.

Você pode usar SKUs privados para lidar com os seguintes cenários:

1.  Publique software que você deseja que fique disponível apenas publicamente para clientes específicos, mas não disponível publicamente.
2.  Publique as variações de software público a um preço personalizado para clientes específicos.
3.  Publique as variações de software público com uma descrição e termos personalizados (por meio de uma nova oferta).

Se quiser apenas alterar o preço, você poderá reutilizar os discos de outro SKU na mesma oferta. Com os SKUs privados, você não precisa reenviar discos entre SKUs.

<a name="mark-a-sku-private"></a>Marcar um SKU privado
---------------------

Para marcar uma SKU como privado, mude a opção perguntando se o SKU é privado:

![Marcar um SKU como privado](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Você pode reutilizar os discos em outro SKU e modificar a descrição ou o preço. Para reutilizar os discos, selecione **Sim** como uma resposta para a solicitação "Esse SKU reutiliza imagens de um SKU público?".

Se o SKU está marcado como privado e a oferta tem outros SKUs com discos reutilizáveis, é necessário que você indique que o SKU reutiliza discos de outro SKU. Também é necessário que você especifique o público-alvo para o SKU privado.

>[!NOTE]
>Depois que ele é publicado, um SKU público não pode ser transformado em privado.

<a name="select-an-image"></a>Selecionar uma imagem
------------------

Você pode fornecer novos discos para o SKU privado ou reutilizar os mesmos discos já fornecidos em outro SKU, somente modificando os preços ou a descrição. Para reutilizar os discos, selecione **Sim** como uma resposta para a solicitação "Esse SKU reutiliza imagens de um SKU público?".

![Indicar a reutilização de imagem](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Depois de confirmar que a SKU reutiliza imagens de outro SKU, você pode identificar o SKU que é a origem das imagens.

As solicitações na captura de tela a seguir mostram como identificar se o SKU privado reutilizaria as imagens do SKU selecionado:

![Selecionar uma imagem](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Quando você publica a oferta, as imagens do SKU selecionado seriam disponibilizados sob a ID do SKU privado com as taxas/termos personalizados. O SKU privado só estaria visível para o público-alvo.

Para atualizações de imagem, seria necessário apenas que você atualizasse a imagem do SKU subjacente. Nos bastidores, a imagem para o SKU privado será também atualizada automaticamente. Da mesma forma, se você excluir a imagem do SKU subjacente, a imagem também será removida do SKU privado.

<a name="restricting-the-audience"></a>Restringir o público-alvo
------------------------

Ofertas privadas podem ser localizadas e implantadas somente pelos usuários de destino.
Atualmente, damos suporte ao direcionamento a usuários usando IDs de assinatura.

Essas assinaturas podem ser inseridas por meio de um formulário de entrada manual **para até 10 assinaturas**, ou pelo carregamento de um arquivo CSV, que permite **até 20.000 assinaturas**.

Entrada manual para público restrito:

![Restringir o público manualmente](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Upload de CSV para público restrito:

![Usar CSV para restringir o público](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Conteúdo do arquivo CSV de exemplo:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Quando você alternar da exibição de entrada manual para a de upload de CSV ou vice-versa, a lista antiga de IDs de assinatura com acesso ao SKU não é mantida. Um aviso é exibido e a lista é substituída apenas ao salvar a oferta.

<a name="sync-private-subscriptions"></a>Assinaturas de Sincronização Particular
-------------------------

Ao adicionar assinaturas a uma oferta publicada com um SKU Privado ou Plano, você não precisa republicar a oferta para adicionar as informações do público-alvo. Simplesmente use um ID de assinatura do Azure (SKUs e planos) ou a ID de locatário (Planos apenas) para adicionar o público-alvo.

<a name="previewing-private-offers"></a>Ofertas de Versão Prévia Privada
-------------------------

Durante a etapa de preparo/versão prévia, somente as assinaturas de versão prévia em nível de oferta serão capazes de acessar o SKU. Esse é o estágio de testes, no qual você pode validar qual seria a aparência da oferta para seus clientes-alvo, e que é padrão para todos os tipos de publicação.

Ofereça assinaturas de versão prévia no nível de oferta para acessar ofertas em preparo:

![Visualizar IDs de assinatura](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Depois que a oferta estiver ativa, somente as assinaturas de público restrito (inseridas por meio de entrada manual ou de CSV) poderão exibir e implantar o SKU privado. É recomendável que você **sempre inclua suas próprias assinaturas no público restrito** para o SKU privado para fins de validação.

>[!NOTE]
>Para fins de depuração, o suporte da Microsoft e as equipes de engenharia também terão acesso a essas ofertas privadas.
