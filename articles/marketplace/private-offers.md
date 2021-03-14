---
title: Ofertas privadas no Microsoft Commercial Marketplace
description: Ofertas privadas no Microsoft Commercial Marketplace para Publicadores de aplicativos e serviços.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 02/22/2021
ms.openlocfilehash: 7c02c41379cfb886fd7e6f84486d815d3cdbfb5b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097169"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Ofertas privadas no Microsoft Commercial Marketplace

Ofertas privadas, também chamadas de planos privados, permitem que os editores criem planos que são visíveis apenas para clientes direcionados. Este artigo discute as opções e os benefícios das ofertas privadas.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Desbloqueie acordos empresariais com ofertas privadas

Ao criar ofertas privadas, os editores podem oferecer soluções personalizadas para clientes de destino com recursos que as empresas necessitam:

- *O preço negociado* permite aos editores estender descontos e preços fora da lista de ofertas disponíveis publicamente.
- *Termos e condições particulares* permitem que os editores personalizem os termos e condições para um cliente específico.
- *As configurações especializadas* permitem que os editores personalizem suas máquinas virtuais, aplicativos do Azure e software como serviço (SaaS) para as necessidades de um cliente individual. Essa opção também permite que os editores forneçam acesso de visualização aos novos recursos do produto, antes de iniciá-los para todos os clientes.

As ofertas privadas permitem que os editores aproveitem a escala e a disponibilidade global de um Marketplace público, com a flexibilidade e o controle necessários para negociar e fornecer negócios e configurações personalizadas. As empresas agora podem comprar e vender de formas que esperam.

## <a name="create-private-offers-using-plans"></a>Criar ofertas privadas usando planos

Para *ofertas novas ou existentes com planos*, os editores podem criar facilmente novas variações privadas criando novos planos (anteriormente conhecidos como SKUs) e marcá-los como particulares. Cada oferta pode ter até 45 planos privados.

<!--- [Private SKUs]() --->

Os planos privados estão disponíveis para os seguintes tipos de oferta:

- Máquina Virtual do Azure
- Aplicativo Azure (implementado como modelos de solução ou aplicativos gerenciados)
- Serviço gerenciado
- Ofertas de SaaS

Os planos privados são componentes de uma oferta e só são visíveis e compráveisdos pelos clientes de destino. Os planos privados só são visíveis e compráveisdos pelos clientes de destino. Os planos privados podem ser disponibilizados para clientes no Azure global e no Azure governamental.

Os planos privados podem reutilizar as imagens base e/ou oferecer metadados já publicados para um plano público. Essa opção permite que os editores criem várias variações privadas de uma oferta pública sem precisar publicar várias versões da mesma imagem base e oferecer metadados. Para a máquina virtual do Azure e as ofertas de aplicativo do Azure somente, quando um plano privado compartilha uma imagem base com um plano público, todas as alterações na imagem base da oferta serão propagadas em todos os planos públicos e privados usando essa imagem base.

Para *novas ofertas que incluem apenas planos privados*, os editores podem criar suas ofertas como qualquer outra oferta e, em seguida, marcar os planos como particulares. As ofertas que têm apenas planos privados não poderão ser descobertos ou acessíveis em [portal do Azure](https://azure.microsoft.com/features/azure-portal/) por clientes que não estão associados à oferta.

>[!NOTE]
>Uma oferta que contém apenas planos privados não será visível no Azure Marketplace ou no AppSource público.

## <a name="target-customers-with-private-offers"></a>Direcionar clientes com ofertas privadas

Para ofertas privadas novas e existentes, os editores podem segmentar clientes usando identificadores de assinatura. Para as ofertas de máquina virtual, Aplicativo Azure e serviço gerenciado do Azure, os editores podem restringir a disponibilidade de um plano privado para uma ID de assinatura individual do Azure ou carregar um CSV de até 10.000 IDs de assinatura do Azure. Para ofertas de SaaS, os editores podem associar uma ID de locatário Azure Active Directory para restringir a disponibilidade de um plano privado, usando a abordagem de carregamento manual ou CSV.

Depois que uma oferta tiver sido certificada e publicada, os clientes poderão ser atualizados ou removidos do plano usando o recurso Sincronizar assinaturas privadas. Essa funcionalidade permite que os editores atualizem com rapidez e facilidade a lista de clientes para os quais o plano privado é apresentado sem certificar ou publicar a oferta novamente.

## <a name="deploying-private-offers"></a>Implantando ofertas privadas

Depois de entrar no portal do Azure, os clientes podem seguir estas etapas para selecionar suas ofertas particulares.

1. Faça logon no [portal do Azure](https://ms.portal.azure.com/).
1. Em **Serviços do Azure**, selecione **criar um recurso**.
1. Na **nova** página, ao lado **do Azure Marketplace**, selecione **ver tudo**. A página do Marketplace é exibida.
1. No painel de navegação esquerdo, selecione **ofertas privadas**.

> [!NOTE]
> As ofertas privadas só são detectáveis em [portal do Azure](https://azure.microsoft.com/features/azure-portal/). Eles não são mostrados no [Microsoft AppSource](https://appsource.microsoft.com/) ou no [Azure Marketplace](https://azuremarketplace.microsoft.com). Para saber mais sobre como publicar para as diferentes lojas online do Marketplace comercial, consulte [introdução às opções de listagem](./determine-your-listing-type.md).

As ofertas privadas também aparecerão nos resultados da pesquisa e poderão ser implantadas por meio de modelos de linha de comando e Azure Resource Manager, como qualquer outra oferta.

[![[Ofertas privadas que aparecem nos resultados da pesquisa.]](media/marketplace-publishers-guide/private-offer.png)](media/marketplace-publishers-guide/private-offer.png#lightbox)

Ofertas particulares também aparecerão nos resultados da pesquisa. Basta procurar o selo **privado** .

>[!Note]
>Ofertas privadas não são suportadas com assinaturas estabelecidas por meio de um revendedor do programa CSP (provedor de soluções na nuvem).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
