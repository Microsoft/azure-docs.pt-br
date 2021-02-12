---
title: Configurar propriedades de oferta de máquina virtual no Azure Marketplace
description: Saiba como configurar as propriedades de oferta de máquina virtual no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 281553782774c31ec8cfaf614542fd739c4d3dd9
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629522"
---
# <a name="how-to-configure-virtual-machine-offer-properties"></a>Como configurar propriedades de oferta de máquina virtual

Na página **Propriedades** (selecione no menu de navegação à esquerda no Partner Center), você define as categorias usadas para agrupar sua oferta de VM (máquina virtual) no Azure Marketplace, sua versão do aplicativo e os contratos legais que dão suporte à sua oferta.

## <a name="select-a-category"></a>Selecionar uma categoria

Selecione categorias e subcategorias para posicionar sua oferta nas áreas de pesquisa do Azure Marketplace apropriadas. Não se esqueça de descrever mais tarde na descrição da oferta como sua oferta oferece suporte a essas categorias.

- Selecione uma categoria primária.
- Para adicionar uma segunda categoria opcional (secundária), selecione o link **+ categorias** .
- Selecione até duas subcategorias para a categoria primária e/ou secundária. Se nenhuma subcategoria for aplicável à sua oferta, selecione **não aplicável**. Use CTRL + clique para selecionar uma segunda subcategoria.

Veja a lista completa de categorias e subcategorias nas [melhores práticas de listagem de ofertas](gtm-offer-listing-best-practices.md). Ofertas de máquina virtual sempre aparecem na categoria **computação** no Azure Marketplace.

## <a name="provide-terms-and-conditions"></a>Fornecer termos e condições

Sob **legal** , forneça os termos e condições para sua oferta. Você tem duas opções:

- [Use o contrato padrão com emendas opcionais](#use-the-standard-contract)
- [Usar seus termos e condições](#use-your-own-terms-and-conditions)

Para saber mais sobre o contrato padrão e as emendas opcionais, confira [contrato padrão para o Marketplace comercial da Microsoft](standard-contract.md). Você pode baixar o PDF do [contrato padrão](https://go.microsoft.com/fwlink/?linkid=2041178) (verifique se o bloqueador de pop-ups está desativado).

### <a name="use-the-standard-contract"></a>Usar o contrato Standard

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um contrato padrão que você pode usar para suas ofertas no mercado comercial. Quando você oferece seu software sob o contrato Standard, os clientes precisam apenas lê-lo e aceitá-lo uma vez e você não precisa criar termos e condições personalizados.

1. Marque a caixa de seleção **usar o contrato padrão para o Marketplace comercial da Microsoft** .

   ![Ilustra a caixa de seleção usar o contrato padrão para o Marketplace comercial da Microsoft.](partner-center-portal/media/use-standard-contract.png)

1. Na caixa de diálogo de **confirmação** , selecione **aceitar**. Dependendo do tamanho da tela, talvez seja necessário rolar para cima para vê-la.
1. Selecione **Salvar rascunho** antes de continuar.

   > [!NOTE]
   > Depois de publicar uma oferta usando o contrato padrão para o mercado comercial, você não pode usar seus próprios termos e condições personalizados. Ofereça sua solução sob o contrato padrão com emendas opcionais ou em seus próprios termos e condições.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>Adicionar emendas ao contrato padrão (opcional)

Há dois tipos de emendas disponíveis: *Universal* e *personalizada*.

##### <a name="add-universal-amendment-terms"></a>Adicionar termos de emenda universal

Nos **termos de emendas universais para o contrato padrão da caixa do Marketplace comercial da Microsoft** , insira seus termos de emenda universal. Você pode inserir um número ilimitado de caracteres nesta caixa. Esses termos são exibidos para os clientes no AppSource, no Azure Marketplace e/ou no portal do Azure durante o fluxo de compra e de descoberta.

##### <a name="add-one-or-more-custom-amendments"></a>Adicionar uma ou mais emendas personalizadas

1. Em **termos de emendas personalizadas ao contrato padrão do Marketplace comercial da Microsoft** , selecione o link **Adicionar termo de emenda personalizada (máx. 10)** .
2. Insira seus **termos de emenda personalizada** na caixa.
3. Insira a **ID do locatário** na caixa. Somente os clientes associados às IDs de locatário que você especificar para esses termos personalizados poderão vê-los no fluxo de compra da oferta na portal do Azure.

   > [!TIP]
   > Uma ID de locatário identifica seu cliente no Azure. Você pode solicitar que o cliente tenha essa ID e pode encontrá-la acessando [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Propriedades**. O valor da ID de diretório é a ID do locatário (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e` ). Você também pode procurar a ID do locatário da organização de seus clientes usando a URL de nome de domínio deles em [Qual é a minha ID do locatário do Microsoft Azure e do Office 365?](https://www.whatismytenantid.com/).

4. Opcionalmente, insira uma **Descrição** amigável para a ID do locatário. Essa descrição ajuda a identificar o cliente que você está direcionando para o aditamento.
5. Para adicionar outra ID de locatário, selecione o link **Adicionar uma ID de locatário do cliente (máx. 10)** e repita as etapas 3 e 4. Você pode adicionar até 20 IDs de locatário.
6. Para adicionar outro termo de emenda, repita as etapas de 1 a 5. Você pode fornecer até dez termos de aditamento personalizados por oferta.
7. Selecione **Salvar rascunho** antes de continuar.

### <a name="use-your-own-terms-and-conditions"></a>Usar seus termos e condições

Você pode optar por fornecer seus próprios termos e condições, em vez do contrato Standard. Os clientes precisam aceitar esses termos antes de poderem experimentar sua oferta.

1. Em **ofício** , desmarque a caixa de seleção **usar o contrato padrão para o Marketplace comercial da Microsoft** .
1. Na caixa **termos e condições** , insira até 10.000 caracteres de texto.

   > [!NOTE]
   > Se você precisar de uma descrição mais longa, insira um único endereço Web que aponte para onde os termos e condições podem ser encontrados. Ele será exibido aos clientes como um link ativo.

1. Selecione **salvar rascunho** antes de continuar para a próxima guia no menu à esquerda, **lista de ofertas**.

## <a name="next-steps"></a>Próximas etapas

- [Configurar listagem da oferta de VM](azure-vm-create-listing.md)
