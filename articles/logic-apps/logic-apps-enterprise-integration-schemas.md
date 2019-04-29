---
title: Validar XML com esquemas – Aplicativos Lógicos do Azure | Microsoft Docs
description: Adicionar esquemas para validar documentos XML nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.date: 02/06/2019
ms.openlocfilehash: 3cca995b353b88cc481cbda68df4211a724f7f09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60846259"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Validar XML com esquemas nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack

Seu aplicativo lógico pode usar esquemas para verificar se os documentos usam um XML válido e tem os dados esperados no formato predefinido para cenários de integração corporativa nos Aplicativos Lógicos do Azure. Um esquema também pode validar as mensagens trocadas por aplicativos lógicos em cenários de business-to-business (B2B).

Para obter os limites relacionados aos artefatos e as contas de integração como esquemas, confira [Informações de configuração e limites para Aplicativos Lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) onde você armazena seus esquemas e outros artefatos para soluções de business-to-business (B2B) e integração empresarial. 

  Se o esquema tiver [2 MB ou menos](#smaller-schema), é possível adicioná-lo na sua conta de integração diretamente do portal do Azure. No entanto, se o esquema for maior do que 2 MB, mas não for maior que o [limite de tamanho do esquema](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), é possível carregá-lo na conta de armazenamento do Azure. 
  Para adicionar esse esquema à sua conta de integração, você pode vincular a conta de armazenamento à sua conta de integração. 
  Aqui estão os itens necessários para essa tarefa: 

  * [Conta de armazenamento do Azure](../storage/common/storage-account-overview.md) na qual você cria um contêiner de blob para o esquema. Saiba como [criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md). 

  * Contêiner de blob para armazenar o esquema. Saiba como [criar um contêiner de blob](../storage/blobs/storage-quickstart-blobs-portal.md). 
  Posteriormente, você precisará do URI do conteúdo do contêiner ao adicionar o esquema na conta de integração.

  * [Gerenciador de Armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md), que você pode usar para gerenciar as contas de armazenamento e os contêineres de blob. 
  Para usar o Gerenciador de Armazenamento, escolha uma das opções:
  
    * No portal do Azure, encontre e selecione a conta de armazenamento. 
    No menu da sua conta de armazenamento, selecione **Gerenciador de Armazenamento**.

    * Para obter a versão da área de trabalho, [baixe e instale o Gerenciador de Armazenamento do Azure](https://www.storageexplorer.com/). 
    Em seguida, conecte o Gerenciador de Armazenamento à sua conta de armazenamento seguindo as etapas em [Introdução ao Gerenciador de Armazenamento](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Para obter mais informações, confira [Início Rápido: criar um blob no armazenamento de objetos com o Gerenciador de Armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Não é necessário um aplicativo lógico durante a criação e adição de esquemas. No entanto, para usar um esquema, o aplicativo lógico precisa ser vinculado a conta de integração na qual esse esquema está armazenado. Saiba mais [como vincular aplicativos lógicos a contas de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Se você ainda não tiver um aplicativo lógico, saiba [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Adicionar esquemas

1. Entre no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com suas credenciais da conta do Azure.

1. Para localizar e abrir sua conta de integração, no menu principal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, digite “conta de integração”. Selecione **Contas de Integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Escolha a conta de integração à qual deseja adicionar o esquema, por exemplo:

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Na página **Visão geral** da conta de integração, em **Componentes**, selecione o bloco **Esquemas**.

   ![Selecione "Esquemas"](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Depois que abrir a página **Esquemas**, escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Com base no tamanho do arquivo de esquema (.xsd), siga as etapas para carregar um esquema que tenha [até 2 MB](#smaller-schema) ou [mais de 2 MB, mas até 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Adicionar esquemas até 2 MB

1. Em **Adicionar Esquema**, insira um nome para seu esquema. 
   Mantenha **Arquivo pequeno** marcado. Ao lado da caixa **Esquema**, escolha o ícone de pasta. Localize e selecione o esquema que você está carregando, por exemplo:

   ![Carregar esquema menor](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Quando estiver pronto, escolha **OK**.

   Depois que o esquema termina de carregar, ele aparece na lista **Esquemas**.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Adicionar esquemas com mais de 2 MB

Para adicionar esquemas maiores, você pode carregar seu esquema em um contêiner de blob do Azure na conta de armazenamento do Azure. As etapas para adicionar esquemas diferem com base se o seu contêiner de blob tem acesso de leitura público. Primeiro, verifique se seu contêiner de blob tem ou não acesso de leitura público seguindo estas etapas: [Definir o nível de acesso público para contêiner de blob](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Verificar o nível de acesso do contêiner

1. Abra o Gerenciador de Armazenamento do Azure. Na janela do Explorer, expanda sua assinatura do Azure, caso ainda não esteja expandida.

1. Expanda **Contas de armazenamento** > {*sua conta de armazenamento*} > **Contêineres de Blob**. Selecione seu contêiner de blob.

1. No menu de atalho do seu contêiner de blob, selecione **Definir o nível de acesso público**.

   * Se seu contêiner de blob tiver pelo menos acesso público, escolha **Cancelar** e siga as etapas posteriores nesta página: [Carregar nos contêineres com acesso público](#public-access)

     ![Acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Se seu contêiner de blob não tiver acesso público, escolha **Cancelar** e siga as etapas posteriores nesta página: [Carregar nos contêineres sem acesso público](#public-access)

     ![Sem acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Carregar nos contêineres com acesso público

1. Carregue o esquema na sua conta de armazenamento. 
   Na janela à direita, escolha **Carregar**.

1. Depois de terminar de carregar, selecione o esquema carregado. Na barra de ferramentas, escolha **Copiar URL** para copiar a URL do esquema.

1. Retorne ao portal do Azure onde o painel **Adicionar esquema** está aberto. 
   Digite um nome para o seu assembly. 
   Escolha **Arquivo grande (com mais de 2 MB)**. 

   A caixa do **URI de conteúdo** agora é exibida, em vez da caixa **Esquema**.

1. Na caixa **URI de conteúdo**, cole a URL do seu esquema. 
   Conclua a adição do esquema.

Depois que o esquema termina de carregar, ele aparece na lista **Esquemas**. Na página **Visão geral** da sua conta de integração, em **Componentes**, o bloco **Esquemas** agora mostra o número de esquemas carregados.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Carregar nos contêineres sem acesso público

1. Carregue o esquema na sua conta de armazenamento. 
   Na janela à direita, escolha **Carregar**.

1. Depois de terminar de carregar, gere uma assinatura de acesso compartilhado (SAS) para o esquema. 
   No menu de atalho do esquema, selecione **Obter assinatura de acesso compartilhado**.

1. No painel **Assinatura de acesso compartilhado**, selecione **Gerar URI de assinatura de acesso compartilhado no nível do contêiner** > **Criar**. 
   Depois que a URL de SAS for gerada, ao lado da caixa **URL**, escolha **Copiar**.

1. Retorne ao portal do Azure onde o painel **Adicionar esquema** está aberto. Escolha **Arquivo grande**.

   A caixa do **URI de conteúdo** agora é exibida, em vez da caixa **Esquema**.

1. Na caixa **URI de conteúdo**, cole o URI de SAS gerado anteriormente. Conclua a adição do esquema.

Depois que o esquema termina de carregar, ele aparece na lista **Esquemas**. Na página **Visão geral** da sua conta de integração, em **Componentes**, o bloco **Esquemas** agora mostra o número de esquemas carregados.

## <a name="edit-schemas"></a>Editar esquemas

Para atualizar um esquema existente, você precisa carregar um novo arquivo de esquema com as alterações desejadas. No entanto, você pode baixar primeiro o esquema existente para edição.

1. No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, localize e abra a conta de integração, caso ainda não esteja aberta.

1. No menu principal do Azure, selecione **Todos os serviços**. 
   Na caixa de pesquisa, digite “conta de integração”. 
   Selecione **Contas de Integração**.

1. Escolha a conta de integração na qual você deseja atualizar o esquema.

1. Na página **Visão geral** da conta de integração, em **Componentes**, selecione o bloco **Esquemas**.

1. Após abrir a página **Esquemas**, selecione seu esquema. 
   Para baixar e editar o esquema primeiro, escolha **Baixar**e salve o esquema.

1. Quando estiver pronto para carregar o esquema atualizado, na página **Esquemas**, selecione o esquema que deseja atualizar e, em seguida, escolha **Atualizar**.

1. Localize e selecione o esquema atualizado que deseja carregar. 
   Depois que o arquivo do esquema termina de carregar, o esquema atualizado aparece na lista **Esquemas**.

## <a name="delete-schemas"></a>Excluir esquemas

1. No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, localize e abra a conta de integração, caso ainda não esteja aberta.

1. No menu principal do Azure, selecione **Todos os serviços**. 
   Na caixa de pesquisa, digite “conta de integração”. 
   Selecione **Contas de Integração**.

1. Escolha a conta de integração na qual você deseja excluir o esquema.

1. Na página **Visão geral** da conta de integração, em **Componentes**, selecione o bloco **Esquemas**.

1. Após abrir a página **Esquemas**, selecione seu esquema e selecione **Excluir**.

1. Clique em **Sim** para confirmar que deseja excluir o esquema.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
* [Saiba mais sobre mapas](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Saiba mais sobre as transformações](../logic-apps/logic-apps-enterprise-integration-transform.md)
