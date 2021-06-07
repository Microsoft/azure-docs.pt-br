---
title: APIs de cumprimento de SaaS no Microsoft Commercial Marketplace
description: Uma introdução às APIs de preenchimento que permitem que você integre suas ofertas de SaaS no Microsoft AppSource e no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2e6381afb19018822f6f37171a5ca4b3d929b42e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88037517"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>APIs de cumprimento de SaaS no Microsoft Commercial Marketplace

As APIs de cumprimento de SaaS habilitam editores, também conhecidos como ISVs (fornecedores independentes de software), para publicar e vender seus aplicativos SaaS em Microsoft AppSource, no Azure Marketplace e portal do Azure. Essas APIs permitem que aplicativos ISV participem de todos os canais habilitados para comércio: direto, orientado por parceiros (revendedor) e orientado por campo.  A integração com essas APIs é um requisito para criar e publicar uma oferta de SaaS transactável no Partner Center.

Os ISVs devem implementar os seguintes fluxos de API adicionando ao código de serviço SaaS para manter o mesmo status de assinatura para ISVs e Microsoft:

* Fluxo de página de aterrissagem: a Microsoft notifica o Publicador que a oferta de SaaS do editor foi adquirida por um cliente no Marketplace.
* Fluxo de ativação: o Publicador notifica a Microsoft de que uma conta SaaS comprada recentemente foi configurada no lado do editor.
* Fluxo de atualização: alteração do plano comprado e/ou do número de estações compradas.
* Suspender e reaplicar fluxo: suspendendo a oferta de SaaS adquirida caso o método de pagamento do cliente não seja mais válido. A oferta suspensa pode ser restabelecida quando o problema com o método de pagamento é resolvido.
* Fluxos de webhook: a Microsoft notificará o Publicador sobre alterações de assinatura de SaaS e cancelamento disparado pelo cliente do lado da Microsoft.

Para o cancelamento da assinatura de SaaS adquirida, a integração é opcional, pois ela pode ser feita pelo cliente da Microsoft.

A integração correta com as APIs de cumprimento de SaaS é essencial para garantir que

* Os clientes finais que compraram a oferta de SaaS do editor são cobrados corretamente pela Microsoft.
* Os clientes finais estão obtendo a experiência do usuário correta com a compra, configuração, uso e gerenciamento de assinaturas de SaaS adquiridas no Marketplace.

Essas APIs permitem que as ofertas do Publicador participem de todos os canais habilitados para comércio:

* direct
* orientado por parceiro (revendedor, CSP)
* LED de campo

No cenário de revendedor (CSP), um CSP está comprando a oferta de SaaS em nome do cliente final. Um cliente deve usar a oferta de SaaS, mas o CSP é a entidade que faz o seguinte:

* cobrança do cliente
* alterando planos de assinatura/quantidade de estações compradas
* cancelando as assinaturas

O Publicador não precisa implementar nenhum dos fluxos de chamada à API de forma diferente para esse cenário.

Para obter mais informações sobre o CSP, consulte https://partner.microsoft.com/licensing .

>[!Warning]
>A versão atual desta API é a versão 2, que deve ser usada para todas as novas ofertas de SaaS. A versão 1 da API foi preterida e está sendo mantida para dar suporte às ofertas existentes.

>[!Note]
>As APIs de cumprimento de SaaS se destinam apenas a serem chamadas de um serviço de back-end do Publicador. Não há suporte para a integração com as APIs diretamente da página da Web do Publicador. Somente o fluxo de autenticação de serviço a serviço deve ser usado.

## <a name="next-steps"></a>Próximas etapas

Se você ainda não tiver feito isso, Registre seu aplicativo SaaS na [portal do Azure](https://ms.portal.azure.com) , conforme explicado em [registrar um aplicativo do Azure ad](./pc-saas-registration.md).  Posteriormente, use a versão mais atual desta interface para desenvolvimento: [API de preenchimento de SaaS versão 2](./pc-saas-fulfillment-api-v2.md).
