---
title: Planejar um modelo de solução para uma oferta de aplicativo do Azure
description: Saiba o que é necessário para criar um plano de modelo de solução para uma nova oferta de aplicativo do Azure usando o portal do Marketplace comercial no Microsoft Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: aab856b1e1d2d991cc4964d061a990dbedbeddb7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98876504"
---
# <a name="plan-a-solution-template-for-an-azure-application-offer"></a>Planejar um modelo de solução para uma oferta de aplicativo do Azure

Este artigo explica os requisitos para publicar um plano de modelo de solução para uma oferta de Aplicativo Azure. Um plano de modelo de solução é um dos dois tipos de planos com suporte nas ofertas de Aplicativo Azure. Para obter informações sobre a diferença entre esses dois tipos de plano, consulte [tipos de planos](plan-azure-application-offer.md#plans). Se você ainda não tiver feito isso, leia [planejar uma oferta de aplicativo do Azure](plan-azure-application-offer.md).

O tipo de plano de modelo de solução requer um [modelo de Azure Resource Manager (modelo ARM)](../azure-resource-manager/templates/overview.md) para implantar automaticamente sua infraestrutura de solução.

## <a name="solution-template-requirements"></a>Requisitos de modelo de solução

| Requisitos | Detalhes |
| ------------ | ------------- |
| Cobrança e medição | Os planos de modelo de solução não são pagos, mas podem ser usados para implantar ofertas de VM pagas que são cobradas pelo Microsoft Commercial Marketplace. Os recursos que o modelo ARM da solução implanta são configurados na assinatura do Azure do cliente. As máquinas virtuais pagas conforme o uso são transacionadas com o cliente pela Microsoft e cobradas por meio da assinatura do Azure do cliente. <br><br> Para cobrança de BYOL (traga sua própria licença), embora a Microsoft Bille os custos de infraestrutura incorridos na assinatura do cliente, você leva suas tarifas de licenciamento de software com o cliente diretamente. |
| VHD (disco rígido virtual) compatível com Azure | As VMs devem ser criadas em Windows ou Linux. Para obter mais informações, consulte:<ul><li>[Criar um ativo técnico de VM do Azure](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (para VHDs do Windows)</li><li>[Distribuições do Linux endossadas no Azure](../virtual-machines/linux/endorsed-distros.md) (para VHDs do Linux).</li></ul> |
| Atribuição de uso do cliente | É necessário habilitar a atribuição de uso do cliente em todos os modelos de solução publicados no Azure Marketplace. Para obter mais informações sobre a atribuição de uso do cliente e como habilitá-la, consulte [atribuição de uso do cliente do parceiro do Azure](azure-partner-customer-usage-attribution.md). |
| Usar discos gerenciados | O [Managed disks](../virtual-machines/managed-disks-overview.md) é a opção padrão para discos persistentes de VMs de IaaS (infraestrutura como serviço) no Azure. Você deve usar discos gerenciados em modelos de solução.<ul><li>Para atualizar seus modelos de solução, siga as orientações em [usar discos gerenciados em Azure Resource Manager modelos](../virtual-machines/using-managed-disks-template-deployments.md)e use os [exemplos](https://github.com/Azure/azure-quickstart-templates)fornecidos.</li><li>Para publicar o VHD como uma imagem no Azure Marketplace, importe o VHD subjacente dos discos gerenciados para uma conta de armazenamento usando o [Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) ou o [CLI do Azure](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd)</ul> |
| Pacote de implantação | Você precisará de um pacote de implantação que permitirá que os clientes implantem seu plano. Se você criar vários planos que exigem a mesma configuração técnica, poderá usar o mesmo pacote de plano. Para obter detalhes, consulte a próxima seção: pacote de implantação. |
|||

## <a name="deployment-package"></a>Pacote de implantação

O pacote de implantação contém todos os arquivos de modelo necessários para esse plano, bem como quaisquer recursos adicionais, empacotados como um arquivo. zip.

Todos os aplicativos do Azure devem incluir esses dois arquivos na pasta raiz de um arquivo. zip:

- Um arquivo de modelo do Resource Manager chamado [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). Este modelo define os recursos a serem implantados na assinatura do Azure do cliente. Para obter exemplos de modelos do Resource Manager, consulte a [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) ou o [GitHub correspondente: Modelos de Início Rápido do Azure Resource Manager](https://github.com/azure/azure-quickstart-templates).
- Uma definição de interface do usuário para a experiência de criação do aplicativo do Azure denominada [createUiDefinition.json](../azure-resource-manager/managed-applications/create-uidefinition-overview.md). Na interface do usuário, você especifica os elementos que permitem aos consumidores fornecer valores de parâmetro.

Os tamanhos máximos de arquivo com suporte são:

- Até 1 GB no tamanho total de arquivo compactado. zip
- Até 1 GB para qualquer arquivo não compactado individual dentro do arquivo. zip

Todas as novas ofertas de aplicativos do Azure devem incluir um GUID [atribuição de uso do cliente de parceiro do Azure](azure-partner-customer-usage-attribution.md).

## <a name="azure-regions"></a>Regiões do Azure

Você pode publicar seu plano na região pública do Azure, na região do Azure governamental ou em ambos. Antes de publicar para o [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md), teste e valide seu plano no ambiente, pois certos pontos de extremidade podem ser diferentes. Para configurar e testar seu plano, solicite uma conta de avaliação do [Microsoft Azure Government](https://azure.microsoft.com/global-infrastructure/government/request/).

Você, como editor, é responsável por eventuais controles de conformidade, medidas de segurança e melhores práticas. O Azure Government usa datacenters isolados fisicamente e redes (localizadas apenas nos EUA).

Para obter uma lista de países e regiões com suporte no Marketplace comercial, confira [disponibilidade geográfica e suporte a moeda](marketplace-geo-availability-currencies.md).

Os serviços do Azure Government manipulam os dados que estão sujeitos a determinadas normas e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para oferecer reconhecimento das suas certificações para esses programas, você pode fornecer até 100 links que as descrevem. Eles podem ser links para sua listagem diretamente no programa ou links para descrições de sua conformidade com eles em seus próprios sites. Esses links são visíveis somente para clientes do Azure Government.

## <a name="choose-who-can-see-your-plan"></a>Escolha quem pode ver seu plano

Você pode configurar cada plano para ser visível para todos (público) ou para apenas um público específico (privado). Você pode criar até 100 planos e até 45 deles podem ser privados. Talvez você queira criar um plano privado para oferecer opções de preços diferentes ou configurações técnicas a clientes específicos.

Você concede acesso a um plano privado usando IDs de assinatura do Azure com a opção de incluir uma descrição de cada ID de assinatura atribuída. Você pode adicionar no máximo 10 IDs de assinatura manualmente ou até 10.000 IDs de assinatura usando um. Arquivo CSV. As IDs de assinatura do Azure são representadas como GUIDs e todas as letras precisam estar em minúsculas.

> [!NOTE]
> Se você publicar um plano privado, poderá alterar sua visibilidade para o público posteriormente. No entanto, depois de publicar um plano público, você não pode alterar sua visibilidade para particular.

Para planos de modelo de solução, você também pode optar por ocultar o plano do Azure Marketplace. Talvez você queira fazer isso se o plano só for implantado indiretamente por meio de outro modelo de solução ou aplicativo gerenciado.

> [!NOTE]
> Não há suporte para planos privados com assinaturas do Azure estabelecidas por meio de um revendedor do CSP (programa de provedor de soluções na nuvem).

Para obter mais informações, consulte [ofertas privadas no Microsoft Commercial Marketplace](private-offers.md).

## <a name="next-steps"></a>Próximas etapas

- [Como criar uma oferta de aplicativo do Azure no Marketplace comercial](create-new-azure-apps-offer.md)