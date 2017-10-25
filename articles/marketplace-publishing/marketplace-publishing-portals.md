---
title: "Visão geral sobre os diversos portais necessários para criar uma oferta de Marketplace | Microsoft Docs"
description: "Visão geral sobre os diversos portais necessários para criar uma oferta de Marketplace"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 89ce82b3-c28a-4b0d-b37a-db3112160a4e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio
ms.openlocfilehash: 093d3ee3ecce89c86a12e7e216072ece57f82f9d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="portals-you-will-need"></a>Portais que você precisará
Antes de iniciar o processo de publicação de uma oferta, vamos apresentar os diversos portais que serão necessários. Abaixo está o resumo sobre os portais - Centro de Desenvolvedores, Portal de Publicação do Azure e Portal do Azure - na ordem em que você vai interagir com eles.                                                                            

## <a name="developer-center"></a>Central de Desenvolvedores
[http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)

### <a name="description"></a>Descrição
Criar sua conta do Centro de Desenvolvedores da Microsoft é uma tarefa única. Certifique-se de que a empresa ainda não tem uma conta do Centro de Desenvolvedores antes de tentar criar uma. Durante o processo, nós coletamos informações de conta bancária, informações fiscais e informações de endereço da empresa.

> [!NOTE]
> Se você estiver publicando apenas ofertas gratuitas (ou ofertas “traga sua própria licença”), nós não exigiremos informações de imposto e banco.
> 
> 

### <a name="identityaccount-used"></a>Identidade/conta usada
De maneira ideal, isso é uma lista de distribuição ou um grupo de segurança (por exemplo, azurepublishing@*partnercompany*.com). A lista de distribuição ou um grupo de segurança **deve** ser registrado como uma conta da Microsoft.

> [!TIP]
> Nós recomendamos usar uma lista de distribuição ou um grupo de segurança, pois ele remove a dependência de qualquer pessoa, embora uma conta individual também possa ser usada.
> 
> 

## <a name="publishing-portal"></a>Portal de publicação
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### <a name="description"></a>Descrição
Isso é o portal que você usa para trabalhar na oferta e publicá-la (marketing, preços, publicação, certificação se aplicável etc.).

### <a name="identityaccount-used"></a>Identidade/conta usada
O grupo de segurança ou a lista de distribuição acima deve ser usada pela primeira vez para entrar no portal de publicação. Posteriormente, outros usuários podem ser adicionados como coadministradores. Isso é como é mapeada para os dados de registro do Centro de Desenvolvedores.

## <a name="azure-portal"></a>Portal do Azure
[https://portal.azure.com](https://portal.azure.com)

### <a name="description"></a>Descrição
Isso é o portal onde você pode exibir suas ofertas preparadas e publicadas no Azure Marketplace (aplicável a máquinas virtuais, modelos de soluções e serviços de desenvolvedor baseados no Gerenciador de Recursos do Azure).

### <a name="identityaccount-used"></a>Identidade/conta usada
Enquanto você estiver preparando uma oferta do portal de publicação, um ID de assinatura deve estar na lista de autorizados. A mesma assinatura (há um nome de usuário e senha associados a ela) precisa ser usada para entrar neste portal para testar a oferta de preparação.

## <a name="see-also"></a>Consulte também
* [Introdução: Como publicar uma oferta para o Azure Marketplace](marketplace-publishing-getting-started.md)

