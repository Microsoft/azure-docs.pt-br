---
title: "Visão geral de como criar e implantar uma oferta no Marketplace | Microsoft Docs"
description: "Entenda as etapas necessárias para tornar-se um desenvolvedor aprovado da Microsoft e criar e implantar uma imagem de máquina virtual, modelo, serviço de dados ou serviço de desenvolvedor no Azure Marketplace"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio
ms.openlocfilehash: 8fbf201343f6710d2781a4b56ae54833ed4c06cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Publicar e gerenciar uma oferta no Azure Marketplace
Este artigo é fornecido para ajudar os desenvolvedores a criar, implantar e gerenciar suas soluções listadas no Azure Marketplace para outros clientes e parceiros do Azure as comprarem e usarem.

## <a name="marketplace-publishing"></a>Publicação do Marketplace
Como um editor do Azure, você pode distribuir e vender sua solução ou serviço inovador a outros desenvolvedores, ISVs e profissionais de TI no Marketplace. Por meio do Marketplace, você pode alcançar clientes que desejam desenvolver rapidamente suas soluções móveis e seus aplicativos baseados em nuvem. Se sua solução tiver como alvo usuários de negócios, poderá ser útil considerar o Marketplace do [AppSource](http://appsource.microsoft.com).


## <a name="supported-types-of-solutions"></a>Tipos de soluções com suporte
A primeira medida que você deve adotar como editor é definir o tipo de solução que sua empresa está oferecendo. O Marketplace dá suporte aos seguintes tipos de oferta:

|Tipo de solução|Máquina virtual|Modelo de solução|
|---|---|---|
|**Definição**|Imagens pré-configuradas com um sistema operacional totalmente instalado e um ou mais aplicativos. Uma imagem da máquina virtual fornece as informações necessárias para criar e implantar máquinas virtuais no serviço de Máquinas Virtuais do Azure.|Uma estrutura de dados que pode fazer referência a um ou mais serviços distintos do Azure, incluindo os serviços publicados por outros vendedores. Assinantes do Azure podem usá-la para implantar uma ou mais ofertas de maneira única e coordenada.|
|**Exemplo**|Como um editor do Azure, você criou e validou uma VM com um serviço de banco de dados inovador. Outros assinantes do Azure desejam adquirir e implantar essa VM em seus ambientes de serviço de nuvem.|Como um editor do Azure, você agrupou um conjunto de serviços de todo o Azure que agilizam a implantação de serviços de nuvem com balanceamento de carga, maior segurança e alta disponibilidade. Outros assinantes do Azure podem poupar tempo ao adquirir o modelo de solução que atenda aos seus objetivos. Eles não precisam localizar, adquirir, implantar e configurar serviços do Azure iguais ou semelhantes manualmente.|

> [!NOTE]
> Algumas etapas são compartilhadas entre os diferentes tipos de soluções e outras são distintas para o respectivo tipo de solução. Este artigo apresenta uma breve visão geral das etapas que você precisará concluir para qualquer tipo de solução.

## <a name="publish-a-solution"></a>Publicar uma solução
![Nomear, registrar, publicar](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>Nomear sua solução para pré-aprovação
Para publicar uma [solução](https://createopportunity.azurewebsites.net) de máquina virtual no Marketplace, preencha o **Formulário de Nominação de Solução** Microsoft Azure Certified.

>[!NOTE]
> Se você estiver trabalhando com um Gerente de Conta de Parceiro ou Gerente de Parceiro DX, peça para ele nomear sua solução para o programa Azure Certified. Você também pode acessar a página da Web do [Microsoft Azure Certified](http://createopportunity.azurewebsites.net) e preencher o formulário de solicitação. Insira o email do Gerente de Conta de Parceiro ou Gerente de Parceiro DX na caixa **Contato do Responsável da Microsoft**.

Se você atender aos critérios de qualificação de acordo com as [políticas de participação do Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833) e sua solicitação for aprovada, começaremos a trabalhar com você para integrar sua solução ao Azure Marketplace.

### <a name="register-your-account-as-a-microsoft-seller"></a>Registrar sua conta como um vendedor da Microsoft
Registre sua conta da Microsoft como uma [conta Microsoft Developer](marketplace-publishing-accounts-creation-registration.md).

### <a name="publish-your-solution"></a>Publicar sua solução
Para publicar uma solução para o Marketplace, siga estas etapas:
1. Atender aos requisitos não técnicos.

    a. Atender aos [pré-requisitos não técnicos](marketplace-publishing-pre-requisites.md).

    b. Atender aos [pré-requisitos técnicos de VM](marketplace-publishing-vm-image-creation-prerequisites.md).

    c. Atender aos [pré-requisitos técnicos do modelo de solução](marketplace-publishing-solution-template-creation-prerequisites.md).

2. Criar sua oferta.

    a. Criar uma oferta de [máquina virtual](marketplace-publishing-vm-image-creation.md).

    b. Criar uma oferta de [modelo de solução](marketplace-publishing-solution-template-creation.md).

3. Criar o [conteúdo de marketing](marketplace-publishing-push-to-staging.md) da sua oferta.

4. Testar sua oferta em preparo.

    a. Testar sua oferta de VM em [preparo](marketplace-publishing-vm-image-test-in-staging.md).

    b. Testar sua oferta de modelo de solução em [preparo](marketplace-publishing-solution-template-test-in-staging.md).

5. Implantar sua oferta no [Marketplace](marketplace-publishing-push-to-production.md).


### <a name="create-and-manage-a-virtual-machine-image"></a>Criar e gerenciar uma imagem de máquina virtual
Criar e gerenciar uma imagem de VM usando estes recursos:
* Criar uma imagem de VM [local](marketplace-publishing-vm-image-creation-on-premise.md).
* Criar uma máquina virtual que execute o [Windows no Portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Criar uma máquina virtual que execute o [Linux no portal do Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Solucionar problemas comuns encontrados durante a [criação do VHD](marketplace-publishing-vm-image-creation-troubleshooting.md).

## <a name="manage-your-solution"></a>Gerenciar sua solução
Gerencie sua solução com a ajuda dos seguintes recursos:
* [Ler o guia de pós-produção para ofertas de máquina virtual](marketplace-publishing-vm-image-post-publishing.md)
* [Atualizar os detalhes não técnicos de uma oferta ou uma SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [Atualizar os detalhes técnicos de uma oferta ou uma SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [Adicionar uma nova SKU em uma oferta listada](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [Alterar a contagem de discos de dados para uma SKU listada](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [Excluir uma oferta listada do Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Excluir uma SKU listada do Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [Excluir a versão atual de uma SKU listada do Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [Reverter o preço de listagem para os valores de produção](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [Reverter o modelo de cobrança para os valores de produção](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [Reverter a configuração de visibilidade de uma SKU listada para o valor de produção](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)
* [Alterar seu incentivo ao revendedor do Provedor de Soluções na Nuvem](marketplace-publishing-csp-incentive.md)
* [Entender seu relatório de pagamento](marketplace-publishing-report-payout.md)
* [Obtenha suporte como um editor](marketplace-publishing-get-publisher-support.md)

## <a name="additional-resources"></a>Recursos adicionais
[Configurar o Azure PowerShell](marketplace-publishing-powershell-setup.md)
