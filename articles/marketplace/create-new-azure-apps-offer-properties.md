---
title: Como configurar suas propriedades de oferta de Aplicativo Azure
description: Saiba como configurar as propriedades para sua oferta de aplicativo do Azure no Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 6f92c138a02c9dbdc1d22ca04c733cfbee69dcd0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94488512"
---
# <a name="how-to-configure-your-azure-application-offer-properties"></a>Como configurar suas propriedades de oferta de Aplicativo Azure

Este artigo descreve como configurar as propriedades de uma oferta de Aplicativo Azure no Marketplace comercial.

Na página **Propriedades** , você definirá as categorias aplicáveis à sua oferta e aos contratos legais. Certifique-se de fornecer detalhes completos e precisos sobre sua oferta nesta página, para que ele seja exibido adequadamente e oferecido ao conjunto certo de clientes.

## <a name="select-a-category-for-your-offer"></a>Selecione uma categoria para sua oferta

Em **categorias**, selecione o link **categorias** e, em seguida, escolha pelo menos uma e até duas categorias para agrupar sua oferta nas áreas de pesquisa do Marketplace comercial apropriadas. Selecione até duas subcategorias para cada categoria primária e secundária. Se nenhuma subcategoria for aplicável à sua oferta, selecione **não aplicável**.

## <a name="provide-terms-and-conditions"></a>Fornecer termos e condições

Sob **legal**, forneça os termos e condições para sua oferta. Você tem duas opções:

- [Use o contrato padrão com emendas opcionais](#use-the-standard-contract)
- [Usar seus termos e condições](#use-your-own-terms-and-conditions)

Para saber mais sobre o contrato padrão e as emendas opcionais, confira [contrato padrão para o Marketplace comercial da Microsoft](standard-contract.md). Você pode baixar o PDF do [contrato padrão](https://go.microsoft.com/fwlink/?linkid=2041178) (verifique se o bloqueador de pop-ups está desativado).

### <a name="use-the-standard-contract"></a>Usar o contrato Standard

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um contrato padrão que você pode usar para suas ofertas no mercado comercial. Quando você oferece seu software sob o contrato Standard, os clientes precisam apenas lê-lo e aceitá-lo uma vez e você não precisa criar termos e condições personalizados.

1. Marque a caixa de seleção **usar o contrato padrão do Marketplace comercial da Microsoft** .

   ![Ilustra a caixa de seleção usar o contrato padrão para o Marketplace comercial da Microsoft.](partner-center-portal/media/use-standard-contract.png)

1. Na caixa de diálogo de **confirmação** , selecione **aceitar**. Talvez você precise rolar para cima para vê-lo.
1. Selecione **Salvar rascunho** antes de continuar.

> [!NOTE]
> Depois de publicar uma oferta usando o contrato padrão para o mercado comercial, você não pode usar seus próprios termos e condições personalizados. Ofereça sua solução sob o contrato padrão com emendas opcionais ou em seus próprios termos e condições.

### <a name="add-amendments-to-the-standard-contract-optional"></a>Adicionar emendas ao contrato padrão (opcional)

Há dois tipos de emendas disponíveis: _Universal_ e _personalizada_.

#### <a name="add-universal-amendment-terms"></a>Adicionar termos de emenda universal

Nos **termos de emendas universais para o contrato padrão da caixa do Marketplace comercial da Microsoft** , insira seus termos de emenda universal. Você pode inserir um número ilimitado de caracteres nesta caixa. Esses termos são exibidos para os clientes no AppSource, no Azure Marketplace e/ou no portal do Azure durante o fluxo de compra e de descoberta.

#### <a name="add-one-or-more-custom-amendments"></a>Adicionar uma ou mais emendas personalizadas

1. Em **termos de emendas personalizadas ao contrato padrão do Marketplace comercial da Microsoft**, selecione o link **Adicionar termo de emenda personalizada (máx. 10)** .
1. Na caixa **termos personalizados de emenda** , insira seus termos de emenda.
1. Na caixa **ID do locatário** , insira uma ID de locatário. Somente os clientes associados às IDs de locatário que você especificar para esses termos personalizados poderão vê-los no fluxo de compra da oferta na portal do Azure.

   > [!TIP]
   > Uma ID de locatário identifica seu cliente no Azure. Você pode solicitar que o cliente tenha essa ID e pode encontrá-la acessando [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Propriedades**. O valor da ID de diretório é a ID do locatário (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e` ). Você também pode procurar a ID do locatário da organização de seus clientes usando a URL de nome de domínio deles em [Qual é a minha ID do locatário do Microsoft Azure e do Office 365?](https://www.whatismytenantid.com/).

1. Na caixa **Descrição** , opcionalmente, insira uma descrição amigável para a ID do locatário. Essa descrição ajuda a identificar o cliente que você está direcionando para o aditamento.
1. Para adicionar outra ID de locatário, selecione o link **Adicionar ID de locatário do cliente** e repita as etapas 3 e 4. Você pode adicionar até 20 IDs de locatário.
1. Para adicionar outro termo de emenda, repita as etapas de 1 a 5. Você pode fornecer até dez termos de aditamento personalizados por oferta.
1. Selecione **Salvar rascunho** antes de continuar.

### <a name="use-your-own-terms-and-conditions"></a>Usar seus termos e condições

Você pode optar por fornecer seus próprios termos e condições, em vez do contrato Standard. Os clientes precisam aceitar esses termos antes de poderem experimentar sua oferta.

1. Em **ofício**, verifique se a caixa de seleção **usar o contrato padrão para o Marketplace comercial da Microsoft** está desmarcada.
1. Na caixa **termos e condições** , insira até 10.000 caracteres de texto.
1. Selecione **salvar rascunho** antes de continuar para a próxima guia: lista de ofertas.

## <a name="next-steps"></a>Próximas etapas

- [Como configurar os detalhes de listagem da oferta de Aplicativo Azure](create-new-azure-apps-offer-listing.md)
