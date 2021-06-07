---
title: Armazenamento de dados do cliente para clientes australiano e Nova Zelândia-Azure AD
description: Saiba mais sobre onde o Azure Active Directory armazena dados relacionados ao cliente para seus clientes australianos e da Nova Zelândia.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 7/21/2020
ms.custom: it-pro, seodec18, references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d7c37e64e4f1b339ae66fe3d9135b1874476eb3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836964"
---
# <a name="customer-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Armazenamento de dados do cliente para clientes da Austrália e da Nova Zelândia no Azure Active Directory 

O Azure Active Directory (AD do Azure) armazena seus dados do cliente em uma localização geográfica com base no país que você forneceu quando se inscreveu em um Microsoft Online Service. Os serviços online da Microsoft incluem o Microsoft 365 e o Azure. 

Para obter informações sobre onde os dados do Azure AD e outros serviços da Microsoft estão localizados, consulte a seção [onde estão os dados localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) da central de confiabilidade da Microsoft.

De 26 de fevereiro de 2020, a Microsoft começou a armazenar os dados do cliente do Azure AD para novos locatários com um endereço de cobrança australiano ou Nova Zelândia dentro dos data centers australianos. Entre 1º de maio de 2020 e 31 de março de 2021, a Microsoft migrará os locatários existentes que têm um endereço de cobrança australiano ou Nova Zelândia para os data centers australianos sem a necessidade de nenhuma ação do cliente. O processo de migração não envolve nenhum tempo de inatividade para os clientes e não afetará nenhuma funcionalidade de um locatário durante a migração.

Além disso, alguns recursos do Azure AD ainda não dão suporte ao armazenamento de dados do cliente na Austrália. Acesse o [mapa de dados do Azure ad](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)para obter informações de recursos específicas. Por exemplo, Microsoft Azure AD a autenticação multifator armazena os dados do cliente nos EUA e os processa globalmente. Consulte [dados de cliente e residência de dados para autenticação multifator do Azure ad](../authentication/concept-mfa-data-residency.md).

> [!NOTE]
> Os produtos, serviços e aplicativos de terceiros da Microsoft que se integram ao Azure AD têm acesso aos dados do cliente. Avalie cada produto, serviço e aplicativo que você usa para determinar como os dados do cliente são processados por esse produto, serviço e aplicativo específicos e se eles atendem aos requisitos de armazenamento de dados da sua empresa. Para obter mais informações sobre a residência de dados de serviços da Microsoft, consulte o [Onde seus dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) seção do Microsoft Trust Center.