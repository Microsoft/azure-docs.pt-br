---
title: "Active Directory B2C: Residência de dados e disponibilidade de região | Microsoft Docs"
description: "Um tópico sobre os tipos de locatários do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: bryanla
ms.assetid: 8a0644da-b825-4edc-8ce9-541c3c976afb
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: gsacavdm
ms.openlocfilehash: facd66f0324e382ea7609a035de8129ba433846f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Residência de dados e disponibilidade de região
Residência de dados e disponibilidade de região são dois conceitos muito diferentes que se aplicam a B2C do Azure AD diferentemente do restante do Azure. Este artigo irá explicar as diferenças entre esses dois conceitos e comparar como são aplicáveis para B2C do Azure AD versus Azure.

## <a name="summary"></a>Resumo
B2C do Azure AD está **disponível em todo o mundo** com a opção para **residência de dados nos Estados Unidos ou Europa**.

## <a name="concepts"></a>Conceitos
* **Disponibilidade da região** refere-se onde um serviço está disponível para uso.
* **Residência dados** refere-se ao armazenamento de dados do usuário.

## <a name="region-availability"></a>Disponibilidade de região
B2C do Azure AD está disponível em todo o mundo por meio da nuvem pública do Azure. 

Isso difere do modelo da maioria dos demais serviços do Azure, que une disponibilidade com residência de dados. É possível visualizar exemplos disso na página [Produtos Disponíveis por Região](https://azure.microsoft.com/regions/services/) e [Calculadora de preços B2C do Active Directory](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Residência de dadosResidência de dados
B2C do Azure AD armazena dados do usuário nos Estados Unidos ou na Europa.

Residência de dados é determinada com base em qual país/região é selecionada ao [criar um locatário B2C do Azure AD](active-directory-b2c-get-started.md).

![Captura de tela de um locatário de visualização](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

Os dados residem nos Estados Unidos para os seguintes países/regiões:

> Estados Unidos, Canadá, Costa Rica, República Dominicana, El Salvador, Guatemala, México, Panamá, Porto Rico e Trinidad e Tobago

Os dados residem na Europa para os seguintes países/regiões:

> África do Sul, Alemanha, Arábia Saudita, Argélia, Áustria, Azerbaidjão, Barein, Bélgica, Bielorrússia, Bulgária, Cazaquistão, Chipre, Croácia, Dinamarca, Egito, Emirados Árabes Unidos, Eslováquia, Eslovênia, Espanha, Estônia, Finlândia, França, Grécia, Hungria, Irlanda, Islândia, Israel, Itália, Jordânia, Kuwait, Letônia, Líbano, Liechtenstein, Lituânia, Luxemburgo, Macedônia FYRO, Malta, Marrocos, Montenegro, Nigéria, Noruega, Omã, Países Baixos, Paquistão, Polônia, Portugal, Qatar, Quênia, Reino Unido, República Checa, Romênia, Rússia, Sérvia, Suécia, Suíça, Tunísia, Turquia, Ucrânia.

Os demais países/regiões estão no processo de serem adicionados à lista.  Por enquanto, ainda é possível utilizar B2C do Azure AD, escolhendo qualquer um dos países/regiões acima.

> Afeganistão, Argentina, Austrália, Brasil, Chile, Colômbia, Equador, RAE de Hong Kong, Índia, Indonésia, Iraque, Japão, Coreia, Malásia, Nova Zelândia, Paraguai, Peru, Filipinas, Cingapura, Sri Lanka, Taiwan, Tailândia, Uruguai e Venezuela.

## <a name="preview-tenant"></a>Locatário de visualização
Se você criou um locatário do B2C durante o período de visualização do Azure AD B2C, é provável que seu **Tipo de locatário** seja **Locatário de visualização**. Se esse for o caso, você DEVERÁ usar o locatário somente para fins de teste e desenvolvimento e NÃO para aplicativos de produção.

> [!IMPORTANT]
> Não há um caminho de migração de um locatário do B2C de visualização para um locatário do B2C de produção-escala. Observe que há problemas conhecidos quando você exclui um locatário B2C de visualização e recria um locatário B2C de escala de produção com o mesmo nome de domínio. Você precisa criar um locatário B2C de escala de produção com um nome de domínio diferente.


![Captura de tela de um locatário de visualização](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)
