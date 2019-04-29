---
title: Ofertas privadas|Azure
description: Ofertas particulares no Azure Marketplace para editores de aplicativos e serviços.
services: Azure, Marketplace, Compute
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/1/2018
ms.author: qianw211
ms.openlocfilehash: c3294aaa2b6c88e08028c8958d2ab3a7297aea74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60735912"
---
# <a name="private-offers"></a>Ofertas privadas

As ofertas particulares no [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) permitem que os editores criem SKUs visíveis apenas para clientes segmentados.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Desbloquear negócios com ofertas privadas

Os clientes empresariais usam cada vez mais os mercados on-line para encontrar, experimentar e comprar soluções em nuvem. Agora, com ofertas privadas, os editores podem usar o marketplace para compartilhar de forma privada soluções personalizadas com clientes segmentados com recursos que as empresas exigem:

- *O preço negociado* permite aos editores estender descontos e preços fora da lista de ofertas disponíveis publicamente.
- *Termos e condições particulares* permitem que os editores personalizem os termos e condições para um cliente específico.
- *Configurações especializadas* permitem que os editores personalizem suas máquinas virtuais, aplicativos do Azure e aplicativos SaaS para atender às necessidades individuais de um cliente. Essa opção também permite que os editores forneçam acesso antecipado a novos recursos do produto antes de serem lançados de forma mais ampla para todos os clientes.

As ofertas privadas permitem que os editores aproveitem a escala e a disponibilidade global de um mercado público, com a flexibilidade e o controle necessários para negociar e entregar ofertas e configurações personalizadas. Juntos, esses recursos abrem as portas para uma forte adoção corporativa de mercados de nuvem.  As empresas agora podem comprar e vender da maneira que esperam e exigem.

As ofertas privadas estão agora disponíveis para a Máquina Virtual, o Aplicativo Azure (implementado como modelos de solução ou aplicativos gerenciados) e as ofertas de Aplicativos SaaS. Como as ofertas públicas, as ofertas privadas podem ser criadas e gerenciadas por meio do [Portal do Microsoft Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  Os clientes podem receber ou revogar o acesso a ofertas privadas em minutos.

## <a name="creating-private-offers-using-skus-and-plans"></a>Criar privada oferece usando SKUs e planos

Para *ofertas novas ou existentes com SKUs ou planos públicos*, os editores podem criar facilmente novas variações privadas, criando novos SKUs ou planos e marcando-os como privados.  [SKUs particulares](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus) e planos são componentes de uma oferta e só são visíveis e comprados pelos clientes segmentados. SKUs e planos particulares podem reutilizar as imagens de base e/ou oferecer metadados já publicados para um SKU ou plano público. Essa opção permite que os editores criem várias variações privadas de uma oferta pública sem precisar publicar várias versões da mesma imagem de base e oferecer metadados. Para ofertas de aplicativos de Máquina Virtual e Azure apenas, quando uma SKU privada compartilha uma imagem de base com uma SKU pública, quaisquer alterações na imagem de base da oferta se propagam em todas as SKUs públicas e privadas que usam essa imagem de base.

Para *novas ofertas que incluam apenas SKUs ou planos privados*, os editores podem criar ofertas como qualquer outra oferta e marcar os SKUs ou os planos como privados. As ofertas que têm somente SKUs ou planos particulares não poderão ser descobertas ou acessadas pelo [Azure Marketplace](https://azuremarketplace.microsoft.com) ou pelo [portal do Microsoft Azure](https://azure.microsoft.com/features/azure-portal/) por clientes que não estejam associados à oferta.

## <a name="targeting-customers-with-private-offers"></a>Segmentar clientes com ofertas privadas
Para ofertas privadas novas e existentes, os editores podem segmentar clientes usando identificadores de assinatura. Os editores que usam uma oferta de Máquina Virtual ou Aplicativo Azure AD podem restringir a disponibilidade de um SKU particular a um ID individual de assinatura do Azure ou carregar um CSV de até 20.000 IDs de assinatura do Azure. Ao usar uma oferta particular de aplicativo SaaS, os editores podem associar um ID de assinatura do Azure ou um ID de locatário para restringir a disponibilidade de um plano particular, usando a abordagem de upload manual ou de CSV.

Depois que uma oferta é certificada e publicada, os clientes podem ser atualizados ou removidos do SKU ou do Plan em questão de minutos, usando o recurso Sincronizar assinaturas privadas. Esse recurso permite que os editores atualizem rápida e facilmente a lista de clientes para os quais o SKU ou plano privado é apresentado sem recertificar ou republicar a oferta.

## <a name="deploying-private-offers"></a>Implantar privada oferece

As ofertas privadas só podem ser descobertas através do [portal do Microsoft Azure](https://azure.microsoft.com/features/azure-portal/) e não são apresentadas através do [Azure Marketplace](https://azuremarketplace.microsoft.com). Uma vez logado no portal do Microsoft Azure, os clientes podem selecionar o elemento de navegação do Marketplace para acessar suas ofertas privadas. As Ofertas Particulares também aparecerão nos resultados de pesquisa e poderão ser implantadas por meio da linha de comando e dos modelos do Azure Resource Manager, como qualquer outra oferta.

![[Ofertas privadas]](./media/marketplace-publishers-guide/private-offer.png)

Ofertas particulares também aparecerão nos resultados da pesquisa. Basta olhar para o emblema "Privado".

> [!Note]
> Ofertas privadas não são compatíveis com assinaturas estabelecidas por meio de um revendedor do programa provedor de soluções de nuvem (CSP).

## <a name="next-steps"></a>Próximas etapas

Se você quiser aproveitar esses novos recursos, comece a vender no [Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
