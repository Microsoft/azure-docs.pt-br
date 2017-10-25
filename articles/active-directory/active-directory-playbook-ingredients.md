---
title: Ingredientes da cartilha do PoC do Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 7d43be3cbfd63b6bc5f06426e9810a37bbf3d071
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-ingredients"></a>Ingredientes do guia estratégico de prova de conceito do Azure Active Directory 

## <a name="theme"></a>Tema
O Azure AD fornece soluções de identidade e de acesso em várias áreas da empresa. Classificamos os cenários nas seguintes áreas: 

* [Muitos aplicativos, uma identidade](active-directory-playbook-implementation.md#theme---lots-of-apps-one-identity) 
* [Aumentar sua segurança](active-directory-playbook-implementation.md#theme---increase-your-security) 
* [Dimensionar com autoatendimento](active-directory-playbook-implementation.md#theme---scale-with-self-service) 

Definir um tema para estruturar o PoC ajuda a concentrar os esforços que ressoam com metas de negócios, que, geralmente, são os gatilhos de interesse em uma prova de conceito em primeiro lugar. 

## <a name="environment"></a>Ambiente

É importante determinar os detalhes do ambiente em que você fornecerá o PoC. O ideal será você criar com base nele depois que o PoC for concluído. O ambiente de destino é fundamental e você deve encontrar o equilíbrio certo entre torná-lo o mais real possível e a sobrecarga de restrições ou considerações adicionais. Os ambientes típicos para PoCs são:
* **Produção:** os cenários serão implementados em seu ambiente em tempo real e já implantaram os serviços do Microsoft Cloud (AD de produção, Office 365, solução SSO/locatário do Azure AD). 
* **Ambiente UAT (Teste de Aceitação do Usuário)/de desenvolvimento:** você tem uma infraestrutura de teste (AD paralelo e eventualmente a solução SSO/locatário do Azure AD) que tem dados de teste que se assemelham à produção. Normalmente, o ambiente de teste é compartilhado em vários projetos na empresa.

A maioria dos cenários deste guia são aditivos por natureza. Como resultado, eles podem ser implantados no locatário de produção sem afetar os usuários fora do PoC. Ao longo deste documento, mencionaremos quais cenários teriam efeito em todo o locatário. Nesses casos, talvez convenha considerar um ambiente de não produção. 


## <a name="target-users"></a>Usuários de destino

É importante determinar o conjunto de destino de usuários que exercerão os cenários, principalmente quando o ambiente é de produção ou de teste. As categorias de usuários de destino do PoC são:
* **Usuários piloto:** usuários reais no ambiente que usarão a solução com a conta que eles usam para suas funções de trabalho diário
* **Usuários de teste:** contas de teste criadas no ambiente 

A maioria dos cenários neste guia pode ser exercida por usuários piloto. Ao longo deste documento, mencionaremos considerações do usuário de destino, se necessário.


[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]