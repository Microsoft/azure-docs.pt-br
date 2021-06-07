---
title: Transformar XML com mapas XSLT
description: Adicionar mapas XSLT para transformar XML nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: 62c3d4533dd04dbb5a2ce0c73afa52b81d433913
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91570778"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Transformar XML com mapas nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack

Para transferir dados XML entre formatos para cenários de integração corporativa nos Aplicativos Lógicos do Azure, seu aplicativo lógico pode usar mapas ou, mais especificamente, mapas XSLT (linguagem XSL Transformations). Um mapa é um documento XML que descreve como converter dados de um documento XML para outro formato. 

Por exemplo, suponha que você recebe regularmente pedidos ou faturas B2B de clientes que usam o formato de data AAAAMMDD. No entanto, sua organização usa o formato de data MMDDAAAA. Você pode usar um mapa para transformar o formato de data AAAAMMDD em MMDDAAAA antes de armazenar os detalhes do pedido ou da fatura no banco de dados de atividade do cliente.

Para obter os limites relacionados aos artefatos e às contas de integração como mapas, confira [Informações de configuração e limites para Aplicativos Lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) na qual você armazena seus mapas e outros artefatos para soluções de business-to-business (B2B) e integração empresarial.

* Se seu mapa faz referência a um assembly externo, você precisa carregar *o assembly e o mapa* na conta de integração. Certifique-se de [*carregar o assembly primeiro*](#add-assembly)e, em seguida, carregue o mapa que faz referência ao assembly.

  Se o assembly tiver 2 MB ou menos, é possível adicioná-lo na sua conta de integração *diretamente* do portal do Azure. No entanto, se o assembly ou o mapa for maior do que 2 MB, mas não maior que o [limite de tamanho para assemblies ou mapas](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), você tem estas opções:

  * Para assemblies, você precisa de um contêiner de blob do Azure no qual possa carregar o assembly e o local desse contêiner. Dessa forma, é possível fornecer esse local mais tarde ao adicionar o assembly à sua conta de integração. 
  Para essa tarefa, são necessários estes itens:

    | Item | Descrição |
    |------|-------------|
    | [Conta de Armazenamento do Azure](../storage/common/storage-account-overview.md) | Nessa conta, crie um contêiner de blob do Azure para o assembly. Saiba [como criar uma conta de armazenamento](../storage/common/storage-account-create.md). |
    | Contêiner de blob | Nesse contêiner, você pode carregar seu assembly. Você também precisa da localização desse contêiner ao adicionar o assembly à sua conta de integração. Saiba como [criar um contêiner de blob](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Gerenciador de Armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Essa ferramenta ajuda a gerenciar de maneira mais fácil as contas de armazenamento e contêineres de blob. Para usar o Gerenciador de Armazenamento, [baixe e instale o Gerenciador de Armazenamento do Azure](https://www.storageexplorer.com/). Em seguida, conecte o Gerenciador de Armazenamento à sua conta de armazenamento seguindo as etapas em [Introdução ao Gerenciador de Armazenamento](../vs-azure-tools-storage-manage-with-storage-explorer.md). Para saber mais, consulte [início rápido: criar um blob no armazenamento de objetos com o Gerenciador de armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Ou, no portal do Azure, encontre e selecione a conta de armazenamento. No menu da sua conta de armazenamento, selecione **Gerenciador de Armazenamento**. |
    |||

  * Para mapas, no momento, você pode adicionar mapas maiores usando a [API de REST de Aplicativos Lógicos do Azure – Mapas](/rest/api/logic/maps/createorupdate).

Não é necessário um aplicativo lógico durante a criação e adição de mapas. No entanto, para usar um mapa, o aplicativo lógico precisa estar vinculado à conta de integração na qual esse mapa está armazenado. Saiba mais [como vincular aplicativos lógicos a contas de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Se você ainda não tiver um aplicativo lógico, saiba [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Adicionar assemblies referenciados

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

1. Para localizar e abrir sua conta de integração, no menu principal do Azure, selecione **Todos os serviços**. 
   Na caixa de pesquisa, digite “conta de integração”. 
   Selecione **contas de integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Escolha a conta de integração à qual deseja adicionar o assembly, por exemplo:

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na página **Visão geral** da conta de integração, em **Componentes**, selecione o bloco **Assemblies**.

   ![Selecione "Assemblies"](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Após a página **Assemblies** ser aberta, escolha **Adicionar**.

   ![Captura de tela que realça o botão Adicionar na página assemblies.](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Com base no tamanho do arquivo de assembly, siga as etapas para carregar um assembly que tenha [até 2 MB](#smaller-assembly) ou [mais de 2 MB, mas até 8 MB](#larger-assembly).
Para obter os limites das quantidades de assembly nas contas de integração, confira [Limites e configuração para Aplicativos Lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> Se você alterar o assembly, também deverá atualizar seu mapa se o mapa tiver ou não alterações.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Adicionar assemblies de até 2 MB

1. Em **Adicionar Assembly**, insira um nome para o assembly. Mantenha **Arquivo pequeno** marcado. Ao lado da caixa **Assembly**, escolha o ícone de pasta. Localize e selecione o assembly que você está carregando, por exemplo:

   ![carregar o assembly menor](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   Na propriedade **Nome do Assembly**, o nome do arquivo do assembly aparecerá automaticamente depois de selecioná-lo.

1. Quando estiver pronto, escolha **OK**.

   Depois que o arquivo de assembly terminar de carregar, ele aparecerá na lista **Assemblies**.

   ![Lista de assemblies carregados](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Na página **Visão geral** da sua conta de integração, em **Componentes**, o bloco **Assemblies** agora mostra o número de assemblies carregados, por exemplo:

   ![Assemblies carregados](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Adicionar assemblies com mais de 2 MB

Para adicionar assemblies maiores, você pode carregar seu assembly em um contêiner de blob do Azure na conta de armazenamento do Azure. Suas etapas para adicionar assemblies diferem se o seu contêiner de blob tem acesso de leitura público. Primeiro, verifique se seu contêiner de BLOBs tem acesso de leitura público seguindo estas etapas: [definir o nível de acesso público para o contêiner de blob](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Verificar o nível de acesso do contêiner

1. Abra o Gerenciador de Armazenamento do Azure. Na janela do Explorer, expanda sua assinatura do Azure, caso ainda não esteja expandida.

1. Expanda **Contas de armazenamento** > {*sua conta de armazenamento*} > **Contêineres de Blob**. Selecione seu contêiner de blob.

1. No menu de atalho do seu contêiner de blob, selecione **Definir o nível de acesso público**.

   * Se o seu contêiner de blob tiver pelo menos acesso público, escolha **Cancelar** e siga estas etapas posteriormente nesta página: [carregar para contêineres com acesso público](#public-access-assemblies)

     ![Acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Se o seu contêiner de BLOB não tiver acesso público, escolha **Cancelar** e siga estas etapas posteriormente nesta página: [carregar para contêineres sem acesso público](#no-public-access-assemblies)

     ![Sem acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Carregar nos contêineres com acesso público

1. Carregue o assembly na sua conta de armazenamento. 
   Na janela à direita, escolha **Carregar**.

1. Depois de terminar de carregar, selecione o assembly carregado. Na barra de ferramentas, escolha **Copiar URL** para copiar a URL do assembly.

1. Retorne ao portal do Azure onde o painel **Adicionar assembly** está aberto. 
   Digite um nome para o seu assembly. 
   Escolha **Arquivo grande (com mais de 2 MB)**.

   A caixa do **URI de conteúdo** agora é exibida, em vez da caixa **Assembly**.

1. Na caixa **URI de conteúdo**, cole a URL do seu assembly. 
   Conclua a adição do assembly.

Depois que o arquivo de assembly terminar de carregar, o esquema aparecerá na lista **Assemblies**.
Na página **Visão geral** da sua conta de integração, em **Componentes**, o bloco **Assemblies** agora mostra o número de assemblies carregados.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Carregar nos contêineres sem acesso público

1. Carregue o assembly na sua conta de armazenamento. 
   Na janela à direita, escolha **Carregar**.

1. Depois de terminar de carregar, gere uma assinatura de acesso compartilhado (SAS) para o assembly. 
   No menu de atalho do assembly, selecione **Obter assinatura de acesso compartilhado**.

1. No painel **Assinatura de acesso compartilhado**, selecione **Gerar URI de assinatura de acesso compartilhado no nível do contêiner** > **Criar**. 
   Depois que a URL de SAS for gerada, ao lado da caixa **URL**, escolha **Copiar**.

1. Retorne ao portal do Azure onde o painel **Adicionar assembly** está aberto. 
   Digite um nome para o seu assembly. 
   Escolha **Arquivo grande (com mais de 2 MB)**.

   A caixa do **URI de conteúdo** agora é exibida, em vez da caixa **Assembly**.

1. Na caixa **URI de conteúdo**, cole o URI de SAS gerado anteriormente. Conclua a adição do assembly.

Depois que o assembly terminar de carregar, ele aparecerá na lista **Esquemas**. Na página **Visão geral** da sua conta de integração, em **Componentes**, o bloco **Assemblies** agora mostra o número de assemblies carregados.

## <a name="create-maps"></a>Criar mapas

Para criar um documento XSLT que você pode usar como mapa, é possível usar o Visual Studio 2015 para criar um projeto de integração do BizTalk usando o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md). Neste projeto, é possível criar um arquivo de mapa de integração, que permite que você mapeie visualmente os itens entre dois arquivos de esquema XML. Após criar esse projeto, você tem um documento XSLT.
Para obter os limites das quantidades de mapas nas contas de integração, confira [Limites e configuração para Aplicativos Lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Adicionar mapas

Depois de carregar todos os assemblies referenciados no mapa, você poderá carregar seu mapa.

1. Se você ainda não fez login, faça login no [portal do Azure](https://portal.azure.com) com suas credenciais de conta do Azure. 

1. Se a sua conta de integração ainda não estiver aberta, no menu principal do Azure, selecione **Todos os serviços**. 
   Na caixa de pesquisa, digite “conta de integração”. 
   Selecione **contas de integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Escolha a conta de integração à qual deseja adicionar o mapa, por exemplo:

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na página **Visão geral** da conta de integração, em **Componentes**, selecione o bloco **Mapas**.

   ![Selecione "Mapas"](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Após a página **Mapas** ser aberta, escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Adicionar mapas de até 2 MB

1. Em **Adicionar mapa**, insira um nome para o mapa. 

1. Em **tipo de mapa**, selecione o tipo, por exemplo **: Liquid**, **XSLT**, **XSLT 2,0** ou **XSLT 3,0**.

1. Mantenha **Arquivo pequeno** marcado. Ao lado da caixa **Mapa**, escolha o ícone de pasta. Localize e selecione o mapa que você está carregando, por exemplo:

   ![Carregar mapa](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Se você deixou a propriedade **Nome** vazia, o nome de arquivo do mapa aparece automaticamente nesta propriedade depois de selecionar o arquivo de mapa. 
   No entanto, você pode usar qualquer nome exclusivo.

1. Quando estiver pronto, escolha **OK**. 
   Depois que o arquivo de mapa termina de carregar, o mapa aparece na lista **Mapas**.

   ![Lista de mapas carregados](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Na página **Visão geral** da sua conta de integração, em **Componentes**, o bloco **Mapas** agora mostra o número de mapas carregados, por exemplo:

   ![Mapas carregados](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Adicionar mapas com mais de 2 MB

No momento, para adicionar mapas maiores, use a [API de REST de Aplicativos Lógicos do Azure – Mapas](/rest/api/logic/maps/createorupdate).

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>Editar mapas

Para atualizar um mapa existente, você precisa carregar um novo arquivo de mapa com as alterações desejadas. No entanto, você pode baixar primeiro o mapa existente para edição.

1. No [portal do Azure](https://portal.azure.com), localize e abra a conta de integração, caso ainda não esteja aberta.

1. No menu principal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, digite “conta de integração”. Selecione **contas de integração**.

1. Escolha a conta de integração na qual você deseja atualizar o mapa.

1. Na página **Visão geral** da conta de integração, em **Componentes**, selecione o bloco **Mapas**.

1. Após a página **Mapas** abrir, selecione o mapa. 
   Para baixar e editar o mapa primeiro, escolha **Baixar** e salve o mapa.

1. Quando estiver pronto para carregar o mapa atualizado, na página **Mapas**, selecione o mapa que deseja atualizar e, em seguida, escolha **Atualizar**.

1. Localize e selecione o mapa atualizado que deseja carregar. 
   Depois que o arquivo de mapa termina de carregar, o mapa atualizado aparece na lista **Mapas**.

## <a name="delete-maps"></a>Excluir mapas

1. No [portal do Azure](https://portal.azure.com), localize e abra a conta de integração, caso ainda não esteja aberta.

1. No menu principal do Azure, selecione **Todos os serviços**. 
   Na caixa de pesquisa, digite “conta de integração”. 
   Selecione **contas de integração**.

1. Escolha a conta de integração na qual você deseja excluir o mapa.

1. Na página **Visão geral** da conta de integração, em **Componentes**, selecione o bloco **Mapas**.

1. Após a página **Mapas** abrir, selecione o mapa e escolha **Excluir**.

1. Para confirmar que deseja excluir o mapa, escolha **Sim**.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Saiba mais sobre os esquemas](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Saiba mais sobre as transformações](../logic-apps/logic-apps-enterprise-integration-transform.md)
