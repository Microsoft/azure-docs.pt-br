---
title: Como configurar o preço e a disponibilidade do serviço de consultoria no Microsoft Partner Center
description: Saiba como configurar os detalhes de preços da oferta de serviço de consultoria e a disponibilidade do mercado no Microsoft Commercial Marketplace usando o Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: 6b386238bd759714bc0c8ad81d67c29aa1774aba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96780222"
---
# <a name="how-to-configure-your-consulting-service-pricing-and-availability"></a>Como configurar o preço e a disponibilidade do serviço de consultoria

Este artigo explica como definir a disponibilidade do mercado e os detalhes de preços para sua oferta de serviço de consultoria no Microsoft Commercial Marketplace.

> [!NOTE]
> As ofertas de serviço de consultoria são apenas para listagem. Todas as transações devem ser gerenciadas entre você e seus clientes fora do mercado comercial.

## <a name="markets"></a>Mercados

Na seção de **mercados** , você seleciona os países ou regiões onde o serviço de consultoria estará disponível.

1. Em **mercados**, selecione o link **Editar mercados** .
2. Na caixa de diálogo que aparece, selecione os locais de mercado onde você deseja disponibilizar sua oferta. Você deve selecionar no mínimo um e no máximo 141 mercados.
3. Selecione **salvar** para fechar a caixa de diálogo.

## <a name="preview-audience"></a>Público-alvo de versão prévia

Quando você publica ou atualiza sua oferta de serviço de consultoria, o Partner Center cria uma versão de visualização da listagem. Essa visualização só é visível para usuários que têm uma **tecla Hide**.

No campo **ocultar chave** , insira uma cadeia de caracteres alfanumérica que você usará para acessar a versão de visualização da sua oferta.

## <a name="pricing-informational-only"></a>Preço (somente informativo)

Na seção de **preços** , defina se esta é uma oferta gratuita ou paga.

Para ofertas pagas, especifique se o preço é fixo ou estimado. Se o preço for estimado, sua descrição da oferta deverá descrever quais fatores afetarão o preço.

Se você escolher um único país ou região na seção **mercados** , forneça o preço em uma moeda com suporte para esse mercado e selecione **salvar rascunho**. Consulte [disponibilidade geográfica e suporte a moeda para o mercado comercial](./marketplace-geo-availability-currencies.md) para obter a lista de moedas com suporte.

Se você escolher vários países ou regiões na seção de **mercados** , forneça o preço em dólares de Estados Unidos (USD) e selecione **salvar rascunho**. O Partner Center converterá esse preço na moeda local de todos os mercados selecionados, usando as taxas de câmbio disponíveis quando você salvou o rascunho.

Para validar a conversão ou definir preços personalizados em um mercado individual, você precisa exportar, modificar e importar a planilha de preços:

1. Em **preços**, selecione o link **exportar dados de preços** . Isso baixará um arquivo para seu dispositivo.
1. Abra o arquivo exportedPrice.xlsx no Microsoft Excel.
1. Na planilha, você pode ajustar os preços e as moedas para cada mercado. Consulte [disponibilidade geográfica e suporte a moeda para o mercado comercial](./marketplace-geo-availability-currencies.md) para obter a lista de moedas com suporte. Quando terminar, salve o arquivo.
1. No Partner Center, em **preços**, selecione o link **importar dados de preços** . A importação do arquivo substituirá as informações de preços anteriores.

> [!IMPORTANT]
> Os preços definidos no Partner Center são estáticos e não seguem variações nas taxas de câmbio. Para alterar o preço em um ou mais mercados após a publicação, atualize e reenvie sua oferta no Partner Center.

Selecione **Salvar rascunho** antes de continuar.

## <a name="next-steps"></a>Próximas etapas

* [Examinar e publicar](review-publish-offer.md)