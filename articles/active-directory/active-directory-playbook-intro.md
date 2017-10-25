---
title: "Introdução à cartilha do PoC do Azure Active Directory | Microsoft Docs"
description: "Explorar e implementar rapidamente os cenários de gerenciamento de identidades e acesso"
services: active-directory
keywords: azure active directory, cartilha, prova de conceito, PoC
documentationcenter: 
author: dstefanMSFT
manager: asuthar
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: fb4767bae6a5435f5739162eaf52edec2f0cbf0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-introduction"></a>Guia estratégico de prova de conceito do Azure Active Directory: introdução

Este artigo fornece diretrizes para explorar os diferentes recursos do Azure AD em um PoC (Prova de Conceito). O público-alvo deste documento são Arquitetos de identidade, profissionais de TI e integradores de sistema

## <a name="how-to-use-this-playbook"></a>Como usar este guia estratégico

1. Use a seção [Tema](active-directory-playbook-ingredients.md#theme) e escolha as áreas de interesse com base em suas necessidades.  
2. Defina o escopo do PoC escolhendo os cenários que se alinham com suas metas de negócios. Quanto menor, melhor. É recomendável fazer isso da forma mais curta e concisa possível para transmitir o valor às partes interessadas, minimizando a complexidade para a realização.  
3. Use a seção [Implementação](active-directory-playbook-implementation.md) para compreender os cenários e o que eles significam para o seu ambiente. Em cada cenário, descreveremos como configurá-lo (o que chamamos de [Blocos de construção](active-directory-playbook-building-blocks.md)) e como navegar nos cenários. 
4. Cada bloco de construção explica os pré-requisitos necessários, bem como o tempo aproximado para a conclusão. Isso pode ajudá-lo durante o processo de planejamento. 
5. Com base em 1 a 3 acima, defina o [Ambiente](active-directory-playbook-ingredients.md#environment) no qual executar. Recomendamos procurar um ambiente de produção ter uma boa noção da experiência para seus usuários. 
6. Quando houver requisitos conflitantes, use essa matriz de compensação útil 
   * Exibição centrada no tema do valor  
   * Suavidade para preparar, para configurar e para executar os cenários 
   * Tempo mínimo para executar os cenários de destino 
   * O mais parecido possível com a produção dentro de suas restrições 

>[!NOTE]
> Ao longo deste artigo, você verá alguns aplicativos e produtos de terceiros específicos mencionados como exemplos para sua conveniência. O Azure AD dá suporte a milhares de aplicativos em nossa [galeria de aplicativos](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) que pode ser usada com base em suas necessidades e no seu ambiente. 



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]