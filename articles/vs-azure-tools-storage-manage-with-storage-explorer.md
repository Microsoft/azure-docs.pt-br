---
title: "Introdução ao Gerenciador de armazenamento (visualização) | Microsoft Docs"
description: "Gerenciar os recursos de armazenamento do Azure com o Gerenciador de Armazenamento (Visualização)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: 58ab8a9c5864ce0cb505b78fd087df2973a7e0d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-storage-explorer-preview"></a>Introdução ao Gerenciador de armazenamento (visualização)
## <a name="overview"></a>Visão geral
O Gerenciador de armazenamento do Azure (visualização) é um aplicativo autônomo que permite que você trabalhe facilmente com dados do Armazenamento do Azure no Windows, no macOS e no Linux. Neste artigo, você aprende as várias maneiras de se conectar e gerenciar suas contas de armazenamento do Azure.

![Gerenciador de Armazenamento do Microsoft Azure (Preview)][15]

## <a name="prerequisites"></a>Pré-requisitos
* [Baixe e instale o Gerenciador de armazenamento (visualização)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Conectar-se a uma conta de armazenamento ou serviço
O Gerenciador de armazenamento (visualização) fornece várias maneiras de se conectar às contas de armazenamento. Por exemplo, você pode:
* Conecte-se às contas de armazenamento associadas às suas assinaturas do Azure.
* Conecte-se às contas de armazenamento e serviços compartilhados a partir de outras assinaturas do Azure.
* Conecte-se e gerencie o armazenamento local usando o Emulador de armazenamento do Azure. 

Além disso, você pode trabalhar com contas nacionais e internacionais de armazenamento no Azure:

* [Como conectar-se a uma assinatura do Azure](#connect-to-an-azure-subscription) - gerencie os recursos de armazenamento que pertencem à sua assinatura do Azure.
* [Como trabalhar com o armazenamento de desenvolvimento local](#work-with-local-development-storage) - gerencie o armazenamento local usando o Emulador de armazenamento do Azure.
* [Como anexar ao armazenamento externo](#attach-or-detach-an-external-storage-account) - Gerencie os recursos de armazenamento que pertencem a outra assinatura do Azure ou que estão sob nuvens nacionais do Azure usando o nome de conta de armazenamento, a chave e os pontos de extremidade.
* [Como anexar uma conta de armazenamento usando uma SAS](#attach-storage-account-using-sas): gerencie recursos de armazenamento que pertencem a outra assinatura do Azure usando uma assinatura de acesso compartilhado (SAS).
* [Como anexar um serviço usando uma SAS](#attach-service-using-sas): gerenciar um serviço de armazenamento específico (contêiner de blobs, fila ou tabela) que pertence a outra assinatura do Azure usando uma SAS.
* [Conectar-se a uma conta do Azure Cosmos DB usando uma cadeia de conexão](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Gerencie a conta do Cosmos DB usando uma cadeia de conexão.

## <a name="connect-to-an-azure-subscription"></a>Conectar-se a uma assinatura do Azure
> [!NOTE]
> Se não tiver uma conta do Azure, você poderá [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. No Gerenciador de Armazenamento (Visualização), selecione **Configurações de conta do Azure**.

    ![Configurações de conta do Azure][0]

2. O painel esquerdo exibe todas as contas da Microsoft em que você fez logon. Para se conectar a outra conta, selecione **Adicionar uma conta** e siga as instruções para entrar com uma conta da Microsoft associada a pelo menos uma assinatura ativa do Azure.

    >[!NOTE]
    >Conectar-se ao Azure nacional (como Azure Alemanha, Azure Governamental e Azure China por meio logon) não é suportado atualmente. Confira a seção "Anexar ou desanexar uma conta de armazenamento externo" para saber como se conectar a contas de armazenamento do Azure nacional.

3. Depois de entrar com êxito usando uma conta da Microsoft, o painel esquerdo é preenchido com as assinaturas do Azure associadas à conta. Selecione as assinaturas do Azure com as quais você deseja trabalhar e selecione **Aplicar**. (A escolha dos botões **Todas as assinaturas** seleciona todas ou nenhuma das assinaturas do Azure listadas).

    ![Selecionar assinaturas do Azure][3]  
    O painel esquerdo exibe as contas de armazenamento associadas às assinaturas do Azure selecionadas.

    ![Assinaturas do Azure selecionadas][4]

## <a name="connect-to-an-azure-stack-subscription"></a>Conexão com uma assinatura do Azure Stack

Para obter informações sobre como se conectar a uma assinatura do Azure Stack, confira [Conectar o Gerenciador de Armazenamento para uma assinatura do Azure Stack](azure-stack/user/azure-stack-storage-connect-se.md).

## <a name="work-with-local-development-storage"></a>Trabalhar com o armazenamento de desenvolvimento local
O Gerenciador de armazenamento (visualização) permite que você trabalhe no armazenamento local usando o Emulador de Armazenamento do Azure. Isso permite que você escreva códigos e teste o armazenamento sem necessariamente ter uma conta de armazenamento implantada no Azure, uma vez que a conta de armazenamento está sendo emulada pelo Emulador de Armazenamento do Azure.

> [!NOTE]
> No momento, o Emulador de Armazenamento do Azure tem suporte somente para Windows.
>
>

1. No painel esquerdo do Gerenciador de armazenamento (visualização), expanda o nó **(Local e Anexados)** > **Contas de Armazenamento** > **(Desenvolvimento)**.

    ![Nó de desenvolvimento local][21]

2. Se você ainda não tiver instalado o Emulador de Armazenamento do Azure, receberá uma solicitação para fazer isso por meio de uma barra de informações. Se a barra de informações for exibida, escolha **Baixar a versão mais recente** e instale o emulador.

    ![Baixar o prompt do Emulador de Armazenamento do Azure][22]

3. Depois que o emulador está instalado, você pode criar e trabalhar com blobs, filas e tabelas locais. Para saber como trabalhar com cada tipo de conta de armazenamento, confira um dos links a seguir:

    * [Gerenciar recursos de Armazenamento de Blobs do Azure](vs-azure-tools-storage-explorer-blobs.md)
    * Gerenciar recursos de armazenamento de compartilhamento de arquivos do Azure: *em breve*
    * Gerenciar recursos de armazenamento de filas do Azure: *em breve*
    * Gerenciar recursos de armazenamento de tabelas do Azure: *em breve*

## <a name="attach-or-detach-an-external-storage-account"></a>Conexão ou desconexão de uma conta de armazenamento externo
O Gerenciador de armazenamento (visualização) permite anexar contas de armazenamento externas para facilitar o compartilhamento das contas de armazenamento. Esta seção explica como anexar e desanexar contas do armazenamento externo.

### <a name="get-the-storage-account-credentials"></a>Obter as credenciais da conta de armazenamento
Para compartilhar uma conta de armazenamento externo, o proprietário dessa conta precisa primeiro obter as credenciais (nome e chave da conta), para depois compartilhar essas informações com a pessoa que deseja ser anexada a essa conta (externa). Você pode obter as credenciais da conta de armazenamento por meio do portal do Azure fazendo o seguinte:

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Selecione **Procurar**.

3. Selecione **Contas de Armazenamento**.

4. Na folha **Contas de Armazenamento** , escolha a conta de armazenamento desejada.

5. Na folha **Configurações** da conta de armazenamento selecionada, escolha **Chaves de acesso**.

    ![Opção Chaves de Acesso][5]

6. Na folha **Chaves de acesso**, copie os valores de **Nome da conta de armazenamento** e **key1** que serão usados para anexação à conta de armazenamento.

    ![Chaves de acesso][6]

### <a name="attach-to-an-external-storage-account"></a>Anexação a uma conta de armazenamento externo
Para anexar a uma conta de armazenamento externo, você precisará do nome da conta e da chave. A seção "Como obter as credenciais de conta de armazenamento" explica como obter esses valores no portal do Azure. No entanto, no portal, a chave de conta é chamada **key1**. Então, quando o Gerenciador de armazenamento (visualização) solicitar uma chave de conta, digite o valor **key1**.

1. No Gerenciador de Armazenamento (Visualização), selecione **Conectar ao armazenamento do Azure**.

    ![Opção Conectar ao Armazenamento do Azure][23]

2. Na caixa de diálogo **Conectar ao Armazenamento do Azure**, especifique a chave de conta (o valor **key1** no portal do Azure) e selecione **Avançar**.

    > [!NOTE]
    > Você pode inserir a cadeia de conexão de armazenamento de uma conta de armazenamento no Azure nacional. Por exemplo, para se conectar às contas de armazenamento do Azure Alemanha, insira cadeias de conexão semelhantes à seguinte: 
    >
    >* DefaultEndpointsProtocol=https
    >* AccountName=cawatest03
    >* AccountKey=<storage_account_key>
    >* EndpointSuffix=core.cloudapi.de
    
    >Você pode obter a cadeia de conexão no portal do Azure da mesma forma que conforme descrito na seção "Como obter as credenciais da conta de armazenamento".

    ![Caixa de diálogo Conectar ao Armazenamento do Azure][24]

3. Na caixa de diálogo **Anexar Armazenamento Externo**, insira o nome da conta de armazenamento na caixa **Nome da conta**, especifique outras configurações desejadas e, então, selecione **Avançar**.

    ![Caixa de diálogo Anexar Armazenamento Externo][8]

4. Na caixa de diálogo **Resumo da Conexão**, verifique as informações. Se você quiser alterar alguma coisa, selecione **Voltar** e insira novamente as configurações desejadas. 

5. Selecione **Conectar**.

6. Depois de conectada, a conta de armazenamento externo será exibida com o texto **(Externo)** anexado ao nome da conta de armazenamento.

    ![Resultado da conexão a uma conta de armazenamento externo][9]

### <a name="detach-from-an-external-storage-account"></a>Desconexão de uma conta de armazenamento externo
1. Clique o botão direito do mouse na conta de armazenamento externo que você deseja desanexar e, em seguida, selecione **Desanexar**.

    ![Opção Desanexar do armazenamento][10]

2. Na mensagem de confirmação, escolha **Sim** para confirmar a desconexão da conta de armazenamento externo.

## <a name="attach-a-storage-account-by-using-an-sas"></a>Como anexar uma conta de armazenamento usando uma SAS
Uma [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) permite que o administrador de uma assinatura do Azure conceda acesso temporário para uma conta de armazenamento sem ter que fornecer credenciais de assinatura do Azure.

Para ilustrar isso, vamos supor que o UsuárioA é um administrador de uma assinatura do Azure, e o UsuárioA deseja permitir que o UsuárioB acesse uma conta de armazenamento por um período limitado com determinadas permissões:

1. O UsuárioA gera uma SAS (formada pela cadeia de conexão da conta de armazenamento) para um período específico e com as permissões desejadas.

2. O UsuárioA compartilha a SAS com a pessoa (no exemplo, o UsuárioB) que deseja acessar a conta de armazenamento.  

3. O UsuárioB usa o Gerenciador de armazenamento (visualização) para se anexar à conta pertencente ao UsuárioA usando a SAS fornecida.

### <a name="get-an-sas-for-the-account-you-want-to-share"></a>Obter uma SAS para a conta que você deseja compartilhar
1. No Gerenciador de armazenamento (visualização), clique o botão direito do mouse na conta de armazenamento que você deseja compartilhar e, então, escolha **Obter Assinatura de Acesso Compartilhado**.

    ![Opção do menu de contexto Obter SAS][13]

2. Na caixa de diálogo **Assinatura de Acesso Compartilhado**, especifique o intervalo e as permissões que você deseja para a conta e, então, escolha **Criar**.

    ![Caixa de diálogo Obter SAS][14]  
    A caixa de diálogo **Assinatura de Acesso Compartilhado** abre e exibe a SAS.

3. Ao lado de **cadeia de conexão**, selecione **Copiar** para copiar para a área de transferência e, em seguida, selecione **Fechar**.

### <a name="attach-to-the-shared-account-by-using-the-sas"></a>Anexar à conta compartilhada usando a SAS
1. No Gerenciador de Armazenamento (Visualização), selecione **Conectar ao armazenamento do Azure**.

    ![Opção Conectar ao Armazenamento do Azure][23]

2. Na caixa de diálogo **Conectar ao Armazenamento do Azure**, especifique a cadeia de conexão e selecione **Avançar**.

    ![Caixa de diálogo Conectar ao Armazenamento do Azure][24]

3. Na caixa de diálogo **Resumo da Conexão**, verifique as informações. Para fazer alterações, selecione **Retornar** e, em seguida, insira as configurações desejadas. 

4. Selecione **Conectar**.

5. Depois que estiver conectado, a conta de armazenamento é exibida com **(SAS)** acrescentado ao nome da conta que você forneceu.

    ![O resultado da anexação a uma conta usando a SAS][17]

## <a name="attach-a-service-by-using-an-sas"></a>Como anexar um serviço usando uma SAS
A seção "Como anexar uma conta de armazenamento usando uma SAS" ilustra como um administrador de assinatura do Azure pode conceder acesso temporário a uma conta de armazenamento gerando e compartilhando uma SAS para a conta de armazenamento. Da mesma forma, uma SAS pode ser gerada para um serviço específico (contêiner de blobs, fila ou tabela) em uma conta de armazenamento.  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Gerar uma SAS para o serviço que você deseja compartilhar
Nesse contexto, um serviço pode ser um contêiner de blob, fila ou tabela. Para gerar o SAS para um serviço listado, confira:

* [Obter a SAS para um contêiner de blob](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* Obter a SAS para um compartilhamento de arquivos: *em breve*
* Obter a SAS para uma fila: *em breve*
* Obter a SAS para uma tabela: *em breve*

### <a name="attach-to-the-shared-account-service-by-using-the-sas"></a>Anexar ao serviço de conta compartilhada usando a SAS
1. No Gerenciador de Armazenamento (Visualização), selecione **Conectar ao armazenamento do Azure**.

    ![Opção Conectar ao Armazenamento do Azure][23]

2. Na caixa de diálogo **Conectar ao Armazenamento do Azure**, especifique o URI da SAS e selecione **Avançar**.

    ![Caixa de diálogo Conectar ao Armazenamento do Azure][24]

3. Na caixa de diálogo **Resumo da Conexão**, verifique as informações. Para fazer alterações, selecione **Retornar** e, em seguida, insira as configurações desejadas. 

4. Selecione **Conectar**.

5. Depois de anexado, o serviço recém-anexado será exibido abaixo do nó **(SAS do Serviço)** .

    ![Resultado da anexação a um serviço compartilhado usando SAS][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Conectar-se ao Azure Cosmos DB usando uma cadeia de conexão
Além de gerenciar contas do Azure Cosmos DB por meio de uma assinatura do Azure, um modo alternativo de se conectar a um Azure Cosmos DB é usar uma cadeia de conexão. Siga as etapas abaixo para se conectar usando uma cadeia de conexão.

1. Encontre a opção **Local e Anexo** na árvore à esquerda, clique com o botão direito do mouse em **Contas do Azure Cosmos DB** e escolha **Conectar-se ao Azure Cosmos DB...**

    ![conectar-se ao Azure Cosmos DB por uma cadeia de conexão][33]

2. Escolha a API do Azure Cosmos DB, cole a **Cadeia de Conexão** e, em seguida, clique em **OK** para se conectar à conta do Azure Cosmos DB. Para obter informações sobre como recuperar a cadeia de conexão, consulte [Obtenha a cadeia de conexão](https://docs.microsoft.com/en-us/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][32]

## <a name="search-for-storage-accounts"></a>Pesquisar nas contas de armazenamento
Se você tiver uma lista longa de contas de armazenamento, uma maneira rápida de localizar uma determinada conta de armazenamento é usar a caixa de pesquisa na parte superior do painel esquerdo.

À medida que você digita na caixa de pesquisa, o painel esquerdo exibe somente as contas de armazenamento que correspondem ao valor de pesquisa inserido até esse ponto. Por exemplo, uma pesquisa para todas as contas de armazenamento cujo nome contém **tarcher** é mostrado na seguinte captura de tela:

![Pesquisa na conta de armazenamento][11]

## <a name="next-steps"></a>Próximas etapas
* [Como gerenciar os recursos de armazenamento de Blobs do Azure com o Gerenciador de armazenamento (visualização)](vs-azure-tools-storage-explorer-blobs.md)
* [Gerenciar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure (Versão Prévia)](./cosmos-db/tutorial-documentdb-and-mongodb-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
[25]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-certificate-azure-stack.png
[26]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/export-root-cert-azure-stack.png
[27]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/import-azure-stack-cert-storage-explorer.png
[28]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-target-azure-stack.png
[29]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-azure-stack-account.png
[30]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-accounts-azure-stack.png
[31]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/azure-stack-storage-account-list.png
[32]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.PNG
[33]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.PNG
