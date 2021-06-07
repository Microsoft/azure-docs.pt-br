---
title: Como criar planos para sua oferta de aplicativo do Azure
description: Saiba como criar planos para sua oferta de aplicativo do Azure no Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: fd5ee9085cf716128c5b3ae073f963c76d2bd17a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94369938"
---
# <a name="how-to-create-plans-for-your-azure-application-offer"></a>Como criar planos para sua oferta de aplicativo do Azure

As ofertas vendidas por meio do Microsoft Commercial Marketplace devem ter pelo menos um plano para listar sua oferta no Marketplace comercial. Você pode criar uma variedade de planos com diferentes opções na mesma oferta. Esses planos (às vezes chamados de SKUs) podem diferir em termos de tipo de plano (_modelo de solução_ ou _aplicativo gerenciado_), monetização ou público. Para obter diretrizes gerais sobre planos, consulte [planos e preços para ofertas de Marketplace comercial](plans-pricing.md).

## <a name="create-a-plan"></a>Criar um plano

1. Próximo à parte superior da guia **visão geral do plano** , selecione **+ criar novo plano**.
1. Na caixa de diálogo exibida, na caixa **ID do plano** , insira uma ID de plano exclusiva. Essa ID será visível para os clientes na URL do produto. Use até 50 caracteres alfanuméricos minúsculos, traços ou sublinhados. Você não pode modificar a ID do plano depois de selecionar **criar**.
1. Na caixa **nome do plano** , insira um nome exclusivo para este plano. Os clientes verão esse nome ao decidir qual plano selecionar dentro de sua oferta. Use no máximo 50 caracteres.
1. Selecione **Criar**.

## <a name="define-the-plan-setup"></a>Definir a configuração do plano

A guia **configuração do plano** permite que você defina o tipo de plano, se ele reutiliza a configuração técnica de outro plano e em quais regiões do Azure o plano deve estar disponível. Suas respostas nessa guia afetarão quais campos são exibidos em outras guias para este plano.

### <a name="select-the-plan-type"></a>Selecionar o tipo de plano

- Na lista **tipo de plano** , selecione modelo de **solução** ou **aplicativo gerenciado**.

Um plano de **Modelo de solução** é gerenciado inteiramente pelo cliente. Um **Aplicativo gerenciado** permite que o publicador gerencie o aplicativo em nome do cliente. Para obter detalhes sobre esses dois tipos de plano, consulte [tipos de planos](plan-azure-application-offer.md#types-of-plans).

### <a name="re-use-technical-configuration-optional"></a>Reutilizar a configuração técnica (opcional)

Se você tiver criado mais de um plano do mesmo tipo dentro dessa oferta e a configuração técnica for idêntica entre eles, você poderá reutilizar a configuração técnica de outro plano. Essa configuração não pode ser alterada após a publicação do plano.

#### <a name="to-re-use-technical-configuration"></a>Para reutilizar a configuração técnica

1. Marque a caixa de seleção **este plano reutiliza a configuração técnica de outro plano do mesmo tipo** .
1. Na lista exibida, selecione o plano de base desejado.

> [!NOTE]
> Quando você reutiliza pacotes de outro plano, a guia de **configuração técnica** inteira desaparece desse plano. Os detalhes de configuração técnica do outro plano, incluindo todas as atualizações feitas no futuro, também são usados para esse plano.

### <a name="select-azure-regions-clouds"></a>Selecionar regiões do Azure (nuvens)

Seu plano precisa ser disponibilizado em pelo menos uma região do Azure. Depois que o plano for publicado e disponível em uma região específica do Azure, você não poderá remover essa região da sua oferta.

#### <a name="azure-global-region"></a>Região global do Azure

A caixa de seleção **global do Azure** é marcada por padrão. Isso torna seu plano disponível para clientes em todas as regiões globais do Azure que têm integração com o Marketplace comercial. Para planos de aplicativos gerenciados, você pode selecionar com os mercados que deseja disponibilizar para o seu plano.

Para remover sua oferta dessa região, desmarque a caixa de seleção **global do Azure** .

#### <a name="azure-government-region"></a>Região do Azure Governamental

Essa região fornece acesso controlado para clientes de entidades federais, estaduais, locais ou tribal dos EUA, bem como parceiros qualificados para atendê-los. Você, como editor, é responsável por eventuais controles de conformidade, medidas de segurança e melhores práticas. O Azure Government usa datacenters isolados fisicamente e redes (localizadas apenas nos EUA).

Os serviços do Azure Government manipulam os dados que estão sujeitos a determinadas normas e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para oferecer reconhecimento das suas certificações para esses programas, você pode fornecer até 100 links que as descrevem. Eles podem ser links para sua listagem diretamente no programa ou links para descrições de sua conformidade com eles em seus sites. Esses links são visíveis somente para clientes do Azure Government.

##### <a name="to-select-the-azure-government-region"></a>Para selecionar a região do Azure governamental

1. Marque a caixa de seleção **Azure governamental** .
1. Em **certificações do Azure governamental**, selecione **+ Adicionar certificação (máx. 100)**.
1. Nas caixas que aparecem, forneça um nome e um link para uma certificação.
1. Para adicionar outra certificação, repita as etapas 2 e 3.

Selecione **salvar rascunho** antes de continuar para a próxima guia: lista de planos.

## <a name="define-the-plan-listing"></a>Definir a listagem do plano

A guia **listagem de planos** é onde você configura os detalhes da listagem do plano. Essa guia exibe informações específicas que mostram a diferença entre os planos na mesma oferta. Você pode definir o nome do plano, o resumo e a descrição como você deseja que eles apareçam no Marketplace comercial.

1. Na caixa **nome do plano** , o nome que você forneceu anteriormente para esse plano aparece aqui. Você pode alterá-lo a qualquer momento. Esse nome aparecerá no Marketplace comercial como o título do plano de software da sua oferta e é limitado a 100 caracteres.
1. Na caixa **Resumo do plano** , forneça um breve resumo do seu plano (não a oferta). Este resumo é limitado a 100 caracteres.
1. Na caixa **Descrição do plano** , explique o que torna este plano de software exclusivo e quaisquer diferenças de outros planos dentro de sua oferta. Não descreva a oferta, apenas o plano. Esta descrição pode conter até 2.000 caracteres.
1. Selecione **Salvar rascunho** antes de continuar.

## <a name="next-steps"></a>Próximas etapas

Realize uma destas ações:

- Se você estiver configurando um plano de modelo de solução, vá para [configurar um plano de modelo de solução](create-new-azure-apps-offer-solution.md).
- Se você estiver configurando um plano de aplicativo gerenciado, vá para [configurar um plano de aplicativo gerenciado](create-new-azure-apps-offer-managed.md).
