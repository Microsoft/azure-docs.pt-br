---
title: Atualizar uma oferta VM existente no Azure Marketplace
description: Explica como atualizar uma oferta de VM existente no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 08/27/2018
ms.author: ansud
ms.openlocfilehash: e6099caf949d93abf8c665f61cd9b91d545dd0f4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612546"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Atualizar uma oferta de VM existente no Azure Marketplace

Este artigo explica os diferentes aspectos da atualização da sua oferta de máquina virtual (VM) no [Cloud Partner Portal](https://cloudpartner.azure.com/) e republica a oferta. 

Existem várias razões comuns para você atualizar sua oferta, incluindo:

-  Adicionar uma nova versão de imagem de VM a SKUs existentes
-  Regiões de alterar um SKU está disponível
-  Adicionar novas SKUs
-  Atualizar os metadados do mercado para a oferta ou SKUs individuais
-  Ofertas pagas conforme o uso de preços de atualização

Para ajudá-lo nessas modificações, o portal oferece os recursos **Comparar** e **Histórico**.  

>[!Note]
>Cloud Solution Providers (CSP) parceiro canal participar agora está disponível.  Consulte [Cloud Solution Providers](../../cloud-solution-providers.md) para obter mais informações sobre sua oferta por meio do Microsoft CSP de marketing de parceiros canais.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Alterações não permitidas na oferta de VM ou SKU

Há alguns atributos de uma oferta de VM ou SKU que não podem ser modificados quando a oferta estiver no Mercado do Azure, principalmente:

-  **ID da oferta** e **ID do editor** da oferta
-  **ID do SKU** de SKUs existentes
-  Contagem de SKUs existentes do disco de dados
-  Alterações no modelo de faturamento / licença para SKUs existentes
-  Preço aumenta para um SKU publicado


## <a name="common-update-operations"></a>Operações comuns de atualização

Embora haja uma grande variedade de características que você pode alterar em uma oferta de VM, as operações a seguir são comuns.

### <a name="update-the-vm-image-version-for-a-sku"></a>Atualize a versão da imagem da VM para um SKU

É comum que uma imagem de VM seja atualizada periodicamente com patches de segurança, recursos adicionais e assim por diante.  Em tais cenários, você deseja atualizar a imagem da VM que o seu SKU faz referência usando as seguintes etapas:

1.  Entrar a [Portal do Cloud Partner](https://cloudpartner.azure.com/).

2.  Em **Todas as ofertas**, encontre a oferta para atualizar.

3.  No **SKUs** , clique no SKU associado com a imagem VM para atualizar.

4.  Em **Versão do Disco**, clique em **+ Nova Versão do Disco** para adicionar uma nova imagem de VM.

5.  Forneça a **Versão do disco** das novas imagens de VM. A versão do disco precisa seguir o formato de [versão semântica](https://semver.org/). Versões devem ser no formato X.Y.Z, onde X, Y e Z são inteiros. Verifique se a nova versão que você fornece é maior que todas as versões anteriores; caso contrário, após a republicação, a nova versão não será exibida no portal nem no Azure Marketplace.

6.  Para o **URL do VHD do SO**, insira o [URI de assinatura de acesso compartilhado (SAS)](./cpp-get-sas-uri.md) criado para o VHD do sistema operacional. 

    > [!WARNING] 
    > A contagem de discos de dados não pode mudar entre diferentes versões do SKU. Se as versões anteriores tiverem discos de dados configurados, essa nova versão também deverá ter o mesmo número de discos de dados.

7.  Clique em **Publique** para iniciar o fluxo de trabalho para publicar sua nova versão de VM no Azure Marketplace.


### <a name="change-region-availability-of-a-sku"></a>Alterar a disponibilidade de região de uma SKU

Ao longo do tempo, talvez você queira disponibilizar sua oferta/SKU em regiões mais.  Como alternativa, talvez você queira parar de dar suporte à oferta/SKU em uma determinada região.
Para modificar a disponibilidade, use as seguintes etapas:

1.  Entrar a [Portal do Cloud Partner](https://cloudpartner.azure.com/).

2.  Sob **todas as ofertas** localize a oferta que você deseja atualizar.

3.  Na guia **SKUs**, clique no SKU que você deseja modificar sua disponibilidade.

4.  Clique no botão **Selecionar países** no campo **Disponibilidade por país/região**.

5.  No pop-up de disponibilidade da região, adicione ou remova as regiões para este SKU.

6.  Clique em **Publique** para iniciar o fluxo de trabalho de publicação para atualizar a disponibilidade da região de SKUs.

Se um SKU estiver sendo disponibilizado em uma nova região, você poderá especificar preços para essa região específica por meio da funcionalidade **Exportar dados de preços**. Se você estiver adicionando uma região de volta que já estava disponível antes, não poderá atualizar seus preços porque as alterações de preços não são permitidas.


### <a name="add-a-new-sku"></a>Adicionar uma nova SKU

Use as etapas a seguir para disponibilizar um novo SKU para sua oferta existente: 

1.  Entrar a [Portal do Cloud Partner](https://cloudpartner.azure.com/).

2.  Sob **todas as ofertas** localize a oferta que você deseja atualizar.

3.  Na guia **SKUs**, clique em **Add new SKU** e forneça um **SKU ID** no pop-up.

4.  Republicar a VM conforme detalhado no artigo [Publicar uma máquina virtual no Azure Marketplace](./cpp-publish-offer.md).

5.  Clique em **Publique** para iniciar o fluxo de trabalho para publicar seu novo SKU.


### <a name="update-offer-marketplace-metadata"></a>Atualizar metadados do marketplace da oferta

Utilize os seguintes passos para atualizar os metadados do mercado - nome da empresa, logótipos, etc. - associados à sua oferta: 

1.  Entrar a [Portal do Cloud Partner](https://cloudpartner.azure.com/).

2.  Sob **todas as ofertas** localize a oferta que você deseja atualizar.

3.  Vá para a guia **Marketplace** e siga as instruções no artigo [Publique uma máquina virtual no Azure Marketplace](./cpp-publish-offer.md) para fazer alterações nos metadados.

4.  Clique em **Publicar** para iniciar o fluxo de trabalho para publicar suas alterações.


### <a name="update-pricing-on-published-offers"></a>Atualizar o preço em ofertas publicadas

Quando sua oferta pré-paga for publicada, você não poderá aumentar diretamente o preço da SKU.  (No entanto, você pode criar um novo SKU com a mesma oferta, excluir o SKU antigo e republicar sua oferta para novos clientes.)  Por outro lado, você pode diminuir o preço de uma oferta publicada usando as seguintes etapas:

1.  Entrar a [Portal do Cloud Partner](https://cloudpartner.azure.com/).

2.  Em **Todas as ofertas**, encontre a oferta para atualizar.

3.  Clique no SKU para o qual você deseja diminuir o preço.

4.  Se você definiu o preço na GUI 1x1, poderá alterar o preço diretamente na interface do usuário. Se você definir o preço por meio da planilha de importação / exportação, só poderá diminuir os preços por meio do recurso de importação / exportação.

3.  Clique em **Salvar**.

4.  Clique em **Publicar** para iniciar o fluxo de trabalho para publicar suas alterações.

O novo preço reduzido será visível para novos clientes quando estiver no site.  Esse novo preço afetará seus clientes das seguintes maneiras:

- Novos clientes serão cobrados nessa nova tarifa. 
- Para clientes existentes, a redução de preço será refletida retroativamente ao início do ciclo de faturamento, durante o qual a redução de preço se tornou efetiva.
Se eles já tiverem sido cobrados pelo ciclo durante o qual ocorreu uma redução de preço, eles receberão um reembolso durante o próximo ciclo de faturamento para cobrir o preço reduzido.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Preços Simplificados da Moeda

A partir de 1º de setembro de 2018, uma nova seção chamada **Precificação simplificada de moeda** será adicionada ao portal. A Microsoft está simplificando os negócios do Azure Marketplace, permitindo preços e coleções mais previsíveis de seus clientes em todo o mundo. Essa simplificação incluirá a redução do número de moedas nas quais faturamos seus clientes.

A nova seção terá preços nessas novas moedas. Depois que todos os clientes forem migrados para essas novas moedas de liquidação, a seção de preços original será desativada e somente a seção Preços simplificados de moeda permanecerá.

Você terá até 1º de novembro de 2018 para definir um novo preço para as regiões em que a moeda de liquidação está mudando. Você não poderá aumentar o preço para regiões em que a moeda de liquidação não está mudando.

> [!NOTE] 
> Se você usar APIs para publicar sua oferta, poderá ver uma nova seção no JSON da oferta. Isso seria anotado como `virtualMachinePricingV2` ou `monthlyPricingV2`, dependendo do tipo de oferta. 

Se você tiver alguma dúvida sobre essa alteração, entre em contato com o [Suporte do Azure Marketplace](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Comparar recursos

Ao fazer alterações em uma oferta já publicada, você pode aproveitar o recurso **Comparar** para auditar as alterações feitas. Para usar esse recurso:

1.  A qualquer momento do processo de edição, clique no botão **Comparar** da sua oferta.

    ![Comparar o botão de recurso](./media/publishvm_037.png)


2.  Exibir versões lado a lado de ativos de marketing e metadados.


## <a name="history-of-publishing-actions"></a>Histórico de ações de publicação

Para visualizar qualquer atividade de publicação histórica, clique no item **Histórico** no menu de navegação esquerdo do Cloud Partner Portal. Aqui, você poderá exibir as ações de data/hora que foram executadas durante o tempo de vida das ofertas do Marketplace do Azure.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

