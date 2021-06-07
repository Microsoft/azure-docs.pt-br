---
title: Assinaturas no Gerenciamento de API do Azure|Microsoft Docs
description: Saiba mais sobre o conceito de assinaturas no Gerenciamento de API do Azure. Os consumidores obtêm acesso às APIs usando assinaturas no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: cdc7668b06308bd5532f4885d0e1365fc34ae989
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97511239"
---
# <a name="subscriptions-in-azure-api-management"></a>Assinaturas no Gerenciamento de API do Azure

As assinaturas são um conceito importante no Gerenciamento de API do Azure. Elas são a maneira mais comum para os consumidores de API obterem acesso a APIs publicadas por meio de uma instância de Gerenciamento de API. Este artigo fornece uma visão geral do conceito.

## <a name="what-are-subscriptions"></a>O que são assinaturas?

Quando você publica APIs por meio do Gerenciamento de API, é fácil e normal proteger o acesso a essas APIs usando chaves de assinatura. Os desenvolvedores que precisam consumir as APIs publicadas devem incluir uma chave de assinatura válida em solicitações HTTP ao fazer chamadas para essas APIs. Caso contrário, as chamadas serão imediatamente rejeitadas pelo gateway de Gerenciamento de API. Elas não são encaminhadas para os serviços de back-end.

É necessária uma assinatura para obter uma chave de assinatura para acessar APIs. A assinatura é essencialmente um contêiner nomeado para um par de chaves de assinatura. Os desenvolvedores que precisam consumir as APIs publicadas podem obter as assinaturas. E eles não precisam da aprovação dos editores das APIs. Os editores de API também podem criar assinaturas diretamente para os consumidores da API.

> [!TIP]
> O Gerenciamento de API também oferece suporte a outros mecanismos para proteger o acesso às APIs, incluindo os exemplos a seguir:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Certificados de cliente](api-management-howto-mutual-certificates-for-clients.md)
> - [Restringir IP do autor da chamada](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Escopo das assinaturas

As assinaturas podem ser associadas a vários escopos: produto, todas as APIs ou uma API individual.

### <a name="subscriptions-for-a-product"></a>Assinaturas para um produto

Tradicionalmente, as assinaturas no Gerenciamento de API sempre foram associadas a um único escopo do [produto da API](api-management-terminology.md). Os desenvolvedores podem encontrar a lista de produtos no Portal do Desenvolvedor. Dessa forma, eles podem enviar solicitações de assinatura para os produtos que desejam usar. Após a solicitação de assinatura ser aprovada, automaticamente ou pelos editores de API, o desenvolvedor poderá usar as chaves contidas na assinatura para acessar todas as APIs no produto. No momento, o portal do desenvolvedor mostra apenas as assinaturas de escopo do produto na seção perfil do usuário. 

![Assinaturas de produto](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Em determinados cenários, os editores de API podem querer publicar um produto de API para o público sem o requisito de Assinaturas. Eles podem remover a seleção da opção **Exigir assinatura** na página de **Configurações** do produto no Portal do Azure. Como resultado, todas as APIs do produto podem ser acessadas sem uma chave de API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Assinaturas para todas as APIs ou uma API individual

Quando introduzimos o nível de [Consumo](https://aka.ms/apimconsumptionblog) do Gerenciamento de API, fizemos algumas alterações para aperfeiçoar o gerenciamento de chaves:
- Primeiro, adicionamos mais dois escopos de assinatura: todas as APIs e uma única API. O escopo das assinaturas não está mais limitado a um produto da API. Agora é possível criar chaves que concedem acesso a uma API ou a todas as APIs em uma instância do Gerenciamento de API, sem que seja necessário criar um produto e adicionar as APIs a ele primeiro. Além disso, cada instância de Gerenciamento de API agora vem com uma assinatura imutável para todas as APIs. Essa assinatura torna mais fácil e mais simples testar e depurar APIs dentro do console de teste.

- Em segundo lugar, o Gerenciamento de API agora permite assinaturas **independentes**. Não é mais necessário que as assinaturas sejam associadas a uma conta de desenvolvedor. Esse recurso é útil em cenários em que muitos desenvolvedores ou muitas equipes compartilham uma assinatura.

- Por fim, os editores de API agora podem [criar assinaturas](api-management-howto-create-subscriptions.md) diretamente no portal do Azure:

    ![Assinaturas flexíveis](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o Gerenciamento de API:

+ Aprenda outros [conceitos](api-management-terminology.md) no gerenciamento de API.
+ Siga nossos [tutoriais](import-and-publish.md) para saber mais sobre o gerenciamento de API.
+ Confira nossa [página de perguntas frequentes](api-management-faq.md) para perguntas comuns.
