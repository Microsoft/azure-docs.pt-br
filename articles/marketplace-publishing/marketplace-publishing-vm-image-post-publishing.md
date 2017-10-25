---
title: "Gerenciando a imagem da sua máquina virtual no Azure Marketplace | Microsoft Docs"
description: "Guia detalhado sobre como gerenciar a imagem da sua máquina virtual no Azure Marketplace após a publicação inicial"
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: hascipio;
ms.openlocfilehash: e1f90650e71345957c2d353774cb8bef62c1868b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Guia de pós-produção para ofertas de máquina virtual no Azure Marketplace
Este artigo explica como você pode atualizar uma oferta de máquina virtual online no Azure Marketplace. Ele orienta você pelo processo de adicionar uma ou mais novas SKUs a uma oferta existente. Também o orienta pelo processo de remoção de uma SKU ou oferta de máquina virtual online do Marketplace.

Depois de uma oferta/SKU ser testada no [portal do Azure](http://portal.azure.com), você não poderá alterar as seguintes caixas de texto:

* **Identificador de oferta**: no portal de Publicação, vá para **máquinas virtuais** e selecione sua oferta. Em seguida, clique em **IMAGENS DA VM** > **Identificador da oferta**.
* **Identificador de SKUs**: no portal de Publicação, acesse **máquinas virtuais** e selecione sua oferta. Em seguida, clique em **SKUS** > **Adicionar uma SKU**.
* **Namespace do Editor:** no portal de Publicação, acesse **máquinas Virtuais** > **Passo a Passo** > **Conte-nos Sobre sua Empresa** (encontrado em "Etapa 2 Registrar sua Empresa") > **Namespace do Editor** > **Namespace**.

Depois que a oferta/SKU estiver listada no [Marketplace](http://azure.microsoft.com/marketplace), você não poderá alterar as seguintes caixas de texto:

* **Identificador de oferta**: no portal de Publicação, vá para **máquinas virtuais** e selecione sua oferta. Em seguida, clique em **IMAGENS DA VM** > **Identificador da oferta**.
* **Identificador de SKUs**: no portal de Publicação, acesse **máquinas virtuais** e selecione sua oferta. Em seguida, clique em **SKUS** > **Adicionar uma SKU**.
* **Namespace do Editor**: no portal de Publicação, acesse **máquinas Virtuais** > **Passo a Passo** > **Conte-nos Sobre sua Empresa** (encontrado em "Etapa 2 Registrar") **Namespace do Editor** > **Namespace**.
* **Portas**: no portal de Publicação, acesse **máquinas virtuais** e selecione sua oferta. Em seguida, clique em **IMAGENS DA VM** > **Abrir Portas**.
* **Alteração de preços das SKUs listadas**
* **Alteração do modelo de cobrança das SKUs listadas**
* **Remoção de regiões de cobrança de SKUs listados**
* **Alteração da contagem de discos de dados dos SKUs listados**

## <a name="update-the-technical-details-of-a-sku"></a>Atualizar os detalhes técnicos de uma SKU
Para adicionar uma nova versão à SKU listada e republicar sua oferta, siga estas etapas:

1. Entre no [Portal de publicação](https://publish.windowsazure.com).
2. Acesse a guia **Máquinas virtuais** e selecione sua oferta.
3. No menu à esquerda, clique na guia **IMAGENS da VM**.
4. Na seção **SKUs**, localize a SKU que você deseja atualizar.
5. Adicionar um novo número de versão à SKU e clique no botão **+**. A nova versão deve estar no formato X.Y.Z, em que X, Y e Z são inteiros. As alterações de versão só devem ser incrementais.
6. Na caixa **URL do VHD do SO**, adicione o URI da assinatura de acesso compartilhado criado para o VHD do sistema operacional e salve as alterações.

   > [!IMPORTANT]
   > Você não pode aumentar/diminuir a contagem de discos de dados de uma SKU listada. Você precisa criar uma nova SKU nesse caso. Para obter diretrizes detalhadas, consulte a seção [Adicionar uma nova SKU em uma oferta listada](#add-a-new-sku-under-a-listed-offer).
   >
   >
7. Acesse a guia **PUBLICAR** e, em seguida, clique em **ENVIAR POR PUSH PARA PREPARO**. Para obter diretrizes detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte [Testar sua oferta de VM para o Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Depois de testar sua oferta no preparo, acesse a guia **PUBLICAR** no portal de Publicação. Clique em **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para republicar sua oferta no Marketplace.

    ![Imagens da VM](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>Atualizar os detalhes não técnicos de uma oferta ou uma SKU
Você pode atualizar os detalhes não técnicos (marketing, jurídico, suporte, categorias) de sua oferta online ou SKU no Azure Marketplace.

### <a name="update-the-offer-description-and-logos"></a>Atualizar a descrição e os logotipos da oferta
Para atualizar os detalhes da oferta e republicar sua oferta, siga estas etapas:

1. Entre no [Portal de publicação](https://publish.windowsazure.com).
2. Acesse a guia **Máquinas virtuais** e selecione sua oferta.
3. No menu à esquerda, clique na guia **MARKETING**.
4. Clique em **Inglês (EUA)**.
5. Clique na guia **DETALHES**. Na seção **Descrição**, atualize o **TÍTULO**, o **RESUMO** e o **RESUMO LONGO** da oferta e salve as alterações.

   > [!NOTE]
   > Ao atualizar os detalhes da SKU, lembre-se destas restrições: 
   * Não insira texto duplicado na descrição da oferta e da SKU.
   * Não insira texto duplicado no título da SKU nem no resumo longo da oferta. 
   * Não insira texto duplicado no título da SKU nem no resumo da oferta.
   >
   >

    ![Detalhes](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. Na seção **LOGOTIPOS** da guia **DETALHES**, atualize os logotipos. Verifique se os logotipos seguem as [diretrizes do Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).

   > [!NOTE]
   > O ícone Hero é opcional. Você pode optar por não carregar um ícone Hero. No entanto, depois que o ícone Hero for carregado, não há provisão para excluí-lo do portal de Publicação. Siga as [diretrizes do ícone Hero](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
   >
   >
7. Acesse a guia **PUBLICAR** e, em seguida, clique em **ENVIAR POR PUSH PARA PREPARO**. Para obter diretrizes detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte [Testar sua oferta de VM para o Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Depois de testar sua oferta no preparo, acesse a guia **PUBLICAR** no portal de Publicação. Clique em **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para republicar sua oferta no Marketplace.

    ![Logotipos](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>Atualizar a descrição da SKU
Para atualizar os detalhes da SKU e republicar sua oferta, siga estas etapas:

1. Entre no [Portal de publicação](https://publish.windowsazure.com).
2. Acesse a guia **Máquinas virtuais** e selecione sua oferta.
3. No menu à esquerda, clique na guia **MARKETING**.
4. Clique em **Inglês (EUA)**.
5. Clique na guia **PLANOS**. Na seção **SKUs**, atualize o **TÍTULO**, o **RESUMO** e a **DESCRIÇÃO** da SKU e salve as alterações.

   > [!NOTE]
   > Ao atualizar os detalhes da SKU, lembre-se destas restrições: 
   * Não insira texto duplicado na descrição da oferta e da SKU. 
   * Não insira texto duplicado no título da SKU nem no resumo longo da oferta. 
   * Não insira texto duplicado no título da SKU nem no resumo da oferta.
   >
   >
6. Acesse a guia **PUBLICAR** e, em seguida, clique em **ENVIAR POR PUSH PARA PREPARO**. Para obter diretrizes detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte [Testar sua oferta de VM para o Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Depois de testar sua oferta no preparo, acesse a guia **PUBLICAR** no portal de Publicação. Clique em **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para republicar sua oferta no Marketplace.

    ![Planos](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>Alterar os links existentes ou adicionar novos links
Para alterar links existentes ou adicionar novos links e, em seguida, republicar sua oferta, siga estas etapas:

1. Entre no [Portal de publicação](https://publish.windowsazure.com).
2. Acesse a guia **Máquinas virtuais** e selecione sua oferta.
3. No menu à esquerda, clique na guia **MARKETING**.
4. Clique em **Inglês (EUA)**.
5. Clique na guia **LINKS**.
6. Para adicionar um novo link, na seção **Links**, clique em **+ADICIONAR LINK**. Na caixa de diálogo **Adicionar Link**, digite o **TÍTULO** e a **URL** do link e salve as alterações. Você pode inserir qualquer link que contenha informações que possam ajudar os clientes.
7. Para atualizar ou excluir um link existente, selecione o link e clique no botão **Editar** ou no botão **Excluir**.

   > [!NOTE]
   > Verifique se os links inseridos nesta seção estejam funcionando corretamente, pois esses links são validados durante o processo de solicitação de produção.
   >
   >
8. Acesse a guia **PUBLICAR** e, em seguida, clique em **ENVIAR POR PUSH PARA PREPARO**. Para obter diretrizes detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte [Testar sua oferta de VM para o Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. Depois de testar sua oferta no preparo, acesse a guia **PUBLICAR** no portal de Publicação. Clique em **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para republicar sua oferta no Marketplace.

    ![Links](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![Adicionar Link](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>Alterar uma imagem de exemplo existente ou adicionar uma nova imagem de exemplo
Para alterar uma imagem de exemplo existente ou adicionar novas imagens de exemplo e, em seguida, republicar sua oferta, siga estas etapas:

> [!NOTE]
> Somente uma imagem de exemplo é exibida no [portal do Azure](https://portal.azure.com).
>
>

1. Entre no [Portal de publicação](https://publish.windowsazure.com).
2. Acesse a guia **Máquinas virtuais** e selecione sua oferta.
3. No menu à esquerda, clique na guia **MARKETING**.
4. Clique em **Inglês (EUA)**.
5. Clique na guia **IMAGENS DE EXEMPLO**.
6. Para adicionar uma nova imagem de exemplo, na seção **Exemplo de Imagens**, clique em **CARREGAR UMA NOVA IMAGEM** e salve as alterações.

   > [!NOTE]
   > Incluir uma imagem de exemplo é opcional.
   >
7. Acesse a guia **PUBLICAR** e, em seguida, clique em **ENVIAR POR PUSH PARA PREPARO**. Para obter diretrizes detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte [Testar sua oferta de VM para o Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Depois de testar sua oferta no preparo, acesse a guia **PUBLICAR** no portal de Publicação. Clique em **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para republicar sua oferta no Marketplace.

    ![Exemplo de Imagens](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>Atualizar o conteúdo legal
Para atualizar o conteúdo legal e republicar sua oferta, siga estas etapas:

1. Entre no [Portal de publicação](https://publish.windowsazure.com).
2. Acesse a guia **Máquinas virtuais** e selecione sua oferta.
3. No menu à esquerda, clique na guia **MARKETING**.
4. Clique em **Inglês (EUA)**.
5. Clique na guia **LEGAL**. Na seção **Legal**, atualize suas políticas/termos de uso. Insira ou cole as políticas/termos na caixa de texto **TERMOS DE USO** e salve as alterações.
6. O limite de caracteres para os termos de uso legais é de 1 caractere.
7. Acesse a guia **PUBLICAR** e, em seguida, clique em **ENVIAR POR PUSH PARA PREPARO**. Para obter diretrizes detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte [Testar sua oferta de VM para o Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Depois de testar sua oferta no preparo, acesse a guia **PUBLICAR** no portal de Publicação. Clique em **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para republicar sua oferta no Marketplace.

    ![Legal](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>Atualizar as informações de suporte
Para atualizar as informações de suporte e republicar sua oferta, siga estas etapas:

1. Entre no [Portal de publicação](https://publish.windowsazure.com).
2. Acesse a guia **Máquinas virtuais** e selecione sua oferta.
3. No menu à esquerda, clique na guia **SUPORTE**.
4. Na seção **Contato de Engenharia**, atualize os detalhes de contato de engenharia. Esses detalhes são usados apenas para comunicações internas entre o parceiro e a Microsoft.
5. Na seção **Atendimento ao Cliente**, atualize os detalhes de contato de suporte e a **URL DE SUPORTE**. Esses detalhes são usados apenas para comunicações internas entre o parceiro e a Microsoft.

   > [!NOTE]
   > Se você quiser fornecer suporte apenas por email, insira um número de telefone fictício na seção **Atendimento ao Cliente**. Nesse caso, o email fornecido será usado.
   >
   >
6. Acesse a guia **PUBLICAR** e, em seguida, clique em **ENVIAR POR PUSH PARA PREPARO**. Para obter diretrizes detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte [Testar sua oferta de VM para o Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Depois de testar sua oferta no preparo, acesse a guia **PUBLICAR** no portal de Publicação. Clique em **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para republicar sua oferta no Marketplace.

    ![Suporte](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>Atualizar as categorias
Para atualizar a seção de categorias da oferta e republicar sua oferta, siga estas etapas:

1. Entre no [Portal de publicação](https://publish.windowsazure.com).
2. Acesse a guia **Máquinas virtuais** e selecione sua oferta.
3. No menu à esquerda, clique na guia **CATEGORIAS**.
4. Na seção **Categorias**, atualize as categorias da oferta e salve as alterações. Você pode selecionar até cinco categorias para a galeria do Azure Marketplace.
5. Acesse a guia **PUBLICAR** e, em seguida, clique em **ENVIAR POR PUSH PARA PREPARO**. Para obter diretrizes detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte [Testar sua oferta de VM para o Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
6. Depois de testar sua oferta no preparo, acesse a guia **PUBLICAR** no portal de Publicação. Clique em **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para republicar sua oferta no Marketplace.

    ![Categorias](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>Adicionar uma nova SKU em uma oferta listada
Para adicionar uma nova SKU em sua oferta ao vivo, siga estas etapas:

1. Entre no [Portal de publicação](https://publish.windowsazure.com).
2. Acesse a guia **Máquinas virtuais** e selecione sua oferta.
3. No menu à esquerda, clique na guia **SKUS**. Em seguida, clique em **Adicionar uma SKU**. 
4. Na caixa de diálogo, insira um **Identificador de SKU** em letras minúsculas. Marque a caixa de seleção **Modelo de cobrança BYOL (traga sua própria licença)** se desejar publicar a nova SKU com um modelo de cobrança BYOL. Caso contrário, desmarque a caixa de seleção. Clique na marca de escala para criar uma nova SKU. Se você não tiver escolhido o modelo de cobrança BYOL, o modelo de cobrança será definido automaticamente como por hora. Se você quiser obter a avaliação gratuita de 30 dias para o modelo de cobrança por hora, selecione **Um Mês** para **Há uma avaliação gratuita disponível?** Caso contrário, selecione **Sem Avaliação**. (**Há uma avaliação gratuita disponível?**  só aparecerá se você não tiver selecionado BYOL ao criar a nova SKU.)

   > [!IMPORTANT]
   > **Ocultar esta SKU do Marketplace porque ela sempre deve ser comprada por meio de um modelo de solução** deverá ser **Sim** *somente* se você for aprovado para publicar um modelo de solução. Caso contrário, essa opção sempre deverá ser marcada como **Não**.
   >
   >
4. No menu à esquerda, clique na guia **IMAGENS DA VM** e descubra a nova SKU que você criou.
5. Para configurar a nova SKU, consulte [Obter certificação para sua imagem da VM](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) para obter diretrizes.
6. Para adicionar o material de marketing para a nova SKU, consulte [Fornecer conteúdo de marketing do Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Para adicionar informações de preços para a nova SKU, consulte [Definir seus preços](marketplace-publishing-push-to-staging.md#step-2-set-your-prices).
8. Acesse a guia **PUBLICAR** e, em seguida, clique em **ENVIAR POR PUSH PARA PREPARO**. Para obter diretrizes detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte [Testar sua oferta de VM para o Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. Depois de testar sua oferta no preparo, acesse a guia **PUBLICAR** no portal de Publicação. Clique em **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para republicar sua oferta no Marketplace.

    ![SKUs](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![Adicionar uma SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>Alterar a contagem de discos de dados para uma SKU listada
Você não pode aumentar/diminuir a contagem de discos de dados de uma SKU listada. Você precisa criar uma nova SKU nesse caso. Para obter diretrizes detalhadas, consulte a seção [Adicionar uma nova SKU em uma oferta listada](#add-a-new-sku-under-a-listed-offer).

## <a name="delete-a-listed-offer-from-the-marketplace"></a>Excluir uma oferta listada do Marketplace
É preciso cuidar de vários aspectos no caso de uma solicitação para remover uma oferta online. Para obter diretrizes da equipe de suporte para remover uma oferta listada do Marketplace, siga estas etapas:

1. Gere um tíquete de suporte na página [Criar um incidente](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681).

2. Selecione o **Tipo de problema** como **Gerenciando ofertas** e selecione a **Categoria** como **Modificando uma oferta e/ou uma SKU já em produção**.
3. Envie a solicitação.

A equipe de suporte orienta você pelo processo de exclusão da oferta/SKU.

> [!NOTE]
> Você sempre pode excluir a oferta enquanto ela estiver no status Rascunho (mas não Preparo nem Produção). Na guia **HISTÓRICO**, clique em **DESCARTAR RASCUNHO**.
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>Excluir uma SKU listada do Marketplace
Para excluir uma SKU listada do Marketplace, siga estas etapas:

1. Entre no [Portal de publicação](https://publish.windowsazure.com).

2. Acesse a guia **Máquinas virtuais** e selecione sua oferta.
3. No painel à esquerda, clique na guia **SKUS**.
4. Selecione a SKU que você deseja excluir e, em seguida, clique no botão **Excluir**.
5. Acesse a guia **PUBLICAR** no portal de Publicação. Clique em **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para republicar sua oferta no Marketplace.
6. Depois que a oferta for republicada no Marketplace, a SKU será excluída do Marketplace e do portal do Azure.

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>Excluir a versão atual de uma SKU listada do Marketplace
Para excluir a versão atual de uma SKU listada do Marketplace, siga estas etapas: 

1. Entre no [Portal de publicação](https://publish.windowsazure.com).

2. Acesse a guia **Máquinas virtuais** e selecione sua oferta.
3. No menu à esquerda, clique na guia **IMAGENS da VM**.
4. Selecione o SKU cuja versão atual você deseja excluir e, em seguida, clique no botão **Excluir**.
5. Acesse a guia **PUBLICAR** no portal de Publicação. Clique em **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para republicar sua oferta no Marketplace.
6. Após a oferta ser republicada no Marketplace, a versão atual da SKU listada será excluída do Marketplace e do portal do Azure. A SKU então será revertida para sua versão anterior.

## <a name="revert-the-listing-price-to-production-values"></a>Reverter o preço de listagem para os valores de produção
Para reverter o preço de listagem para os valores de produção, siga estas etapas:

1. Entre no [Portal de publicação](https://publish.windowsazure.com).
2. Acesse a guia **Máquinas virtuais** e selecione sua oferta.
3. No menu à esquerda, clique na guia **PREÇOS**.
4. Selecione uma região cujos preços você deseje redefinir.

    ![Regiões de preço](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. No caso de SKUs com modelo de cobrança por hora, redefina os preços de todos os núcleos, uma vez que estão em produção na região selecionada. Para SKUs com um modelo de cobrança BYOL, disponibilize a SKU na região marcando a caixa de seleção para a SKU na seção **DISPONIBILIDADE DA SKU LICENCIADA EXTERNAMENTE (BYOL)**.

    ![Modelos de preços](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Clique em **DEFINIR AUTOMATICAMENTE O PREÇO PARA OUTROS MERCADOS COM BASE NOS PREÇOS PARA OS ESTADOS UNIDOS**.

   > [!NOTE]
   > O rótulo do botão pode ser diferente, dependendo da região que você selecionar. Uma vez que selecionamos os Estados Unidos, o rótulo do botão é **DEFINIR AUTOMATICAMENTE O PREÇO PARA OUTROS MERCADOS COM BASE NOS PREÇOS PARA OS ESTADOS UNIDOS**.
   >
   >

    ![Preço automático](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. Na página 1 do assistente de Preço Automático, escolha o mercado de base e clique no botão de **Seta**.

    ![Mercado de base](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. Na página 2, escolha planos e medidores de serviço (núcleos) e clique no botão de **seta**.

    ![Planos e medidores de serviço](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. Na página 3, clique em **Alternar Todos** para selecionar todas as regiões. Outra opção é marcar manualmente as caixas de seleção para regiões específicas. Clique no botão de **seta**.

    ![Escolher mercados](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. Na página 4, examine as taxas de câmbio e clique em **Concluir**. O assistente redefine os preços de acordo com as suas seleções.

11. Na guia **PREÇOS**, clique em **EXIBIR RESUMO E ALTERAÇÕES**.
    Para **Exibir Versão**, selecione **Rascunho**; para **Comparar com**, selecione **Produção**. Se você não detectar nenhuma diferença de preço, isso significará que o preço foi revertido com êxito para os valores de produção.

    ![Exibir resumo e alterações](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Acesse a guia **PUBLICAR** e, em seguida, clique em **ENVIAR POR PUSH PARA PREPARO**. Para obter diretrizes detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte [Testar sua oferta de VM para o Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
13. Depois de testar sua oferta no preparo, acesse a guia **PUBLICAR** no portal de Publicação. Clique em **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para republicar sua oferta no Marketplace.

## <a name="revert-the-billing-model-to-production-values"></a>Reverter o modelo de cobrança para os valores de produção
Para reverter o modelo de cobrança para os valores de produção, siga estas etapas:

1. Entre no [Portal de publicação](https://publish.windowsazure.com).

2. Acesse a guia **Máquinas virtuais** e selecione sua oferta.
3. No menu à esquerda, clique na guia **SKUS**.
4. Clique o botão **Editar** para reverter o modelo de cobrança. Na janela que se abre, marque ou desmarque a caixa de seleção **Cobrança e licenciamento são feitos externamente ao Azure (também conhecido como Traga sua própria licença)**.

    ![Editar cobrança](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Siga as etapas em "Reverter o preço de listagem para os valores de produção" neste artigo.
6. Acesse a guia **PUBLICAR** e, em seguida, clique em **ENVIAR POR PUSH PARA PREPARO**. Para obter diretrizes detalhadas sobre como testar a sua oferta no ambiente de preparo, consulte [Testar sua oferta de VM para o Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Depois de testar sua oferta no preparo, acesse a guia **PUBLICAR** no portal de Publicação. Clique em **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para republicar sua oferta no Marketplace.

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>Reverter a configuração de visibilidade de uma SKU listada para o valor de produção
Para reverter a configuração de visibilidade de uma SKU listada para o valor de produção, siga estas etapas:

1. Entre no [Portal de publicação](https://publish.windowsazure.com).

2. Acesse a guia **Máquinas virtuais** e selecione sua oferta.
3. No menu à esquerda, clique na guia **SKUS**.
4. Selecione a SKU e reverta a configuração de visibilidade para o valor de produção.

    ![Visibilidade](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. Depois de concluir as alterações, clique em **SOLICITAR APROVAÇÃO PARA ENVIAR POR PUSH PARA PRODUÇÃO** para republicar sua oferta no Marketplace.

## <a name="see-also"></a>Consulte também
* [Introdução: publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)
* [Compreender o relatório de pagamento](marketplace-publishing-report-payout.md)
* [Alterar seu incentivo ao revendedor do Provedor de Soluções na Nuvem](marketplace-publishing-csp-incentive.md)
* [Solução de problemas comuns de publicação no Marketplace](marketplace-publishing-support-common-issues.md)
* [Obtenha suporte como um editor](marketplace-publishing-get-publisher-support.md)
* [Criar uma imagem da VM local](marketplace-publishing-vm-image-creation-on-premise.md)
* [Criar uma máquina virtual que executa o Windows no Portal de Visualização do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
