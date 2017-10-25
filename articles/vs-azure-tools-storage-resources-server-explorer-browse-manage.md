---
title: Navegar e gerenciar recursos de armazenamento usando o Gerenciador de Servidores | Microsoft Docs
description: Navegando e gerenciando recursos de armazenamento com o Gerenciador de Servidores
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 658dc064-4a4e-414b-ae5a-a977a34c930d
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/24/2017
ms.author: kraigb
ms.openlocfilehash: 456daea494ed1daed9bd809b68ae12fceb800299
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="browse-and-manage-storage-resources-by-using-server-explorer"></a>Navegar e gerenciar recursos de armazenamento usando o Gerenciador de Servidores
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Visão geral
Se você instalou as ferramentas do Azure para Microsoft Visual Studio, você pode exibir dados de blob, de fila e de tabela de suas contas de armazenamento do Azure. O nó de **Armazenamento** do Azure no Gerenciador de Servidores mostra os dados que estão em sua conta do emulador de armazenamento local e outras contas de armazenamento do Azure.

Para exibir o Gerenciador de Servidores no Visual Studio, na barra de menus, selecione **Exibir** > **Gerenciador de Servidores**. O nó de **armazenamento** mostra todas as contas de armazenamento que existem em cada assinatura ou certificado do Azure ao qual você está conectado. Se sua conta de armazenamento não aparecer, você pode adicioná-la seguindo as instruções [mais adiante neste tópico](#add-storage-accounts-by-using-server-explorer).

A partir do Azure SDK 2.7, você pode também usar o Cloud Explorer para exibir e gerenciar os recursos do Azure. Para saber mais, consulte [Gerenciando recursos do Azure com o Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md).

## <a name="view-and-manage-storage-resources-in-visual-studio"></a>Exibir e gerenciar recursos de armazenamento no Visual Studio
O Gerenciador de Servidores automaticamente mostra uma lista de blobs, filas e tabelas na conta do emulador de armazenamento. A conta do emulador de armazenamento está listada no Gerenciador de Servidores no nó de **Armazenamento** como o nó de **Desenvolvimento**.

Para ver os recursos da conta do emulador de armazenamento, expanda o nó **Desenvolvimento** . Se o emulador de armazenamento não foi iniciado quando você expande o nó **Desenvolvimento** , ele será iniciado automaticamente. Isso pode levar vários minutos. Você pode continuar a trabalhar em outras áreas do Visual Studio enquanto o emulador de armazenamento é iniciado.

Para exibir recursos em uma conta de armazenamento, expanda o nó da conta de armazenamento no Gerenciador de Servidores. Os seguintes sub-nós aparecem:

* **Blobs**
* **Filas**
* **Tabelas**

## <a name="work-with-blob-resources"></a>Trabalhar com recursos de blob
O nó de **Blobs** exibe uma lista de contêineres para a conta de armazenamento selecionada. Contêineres de blob contêm arquivos de blob e você pode organizar esses blobs em pastas e subpastas. Para obter mais informações, consulte o artigo sobre [Como usar o armazenamento de blobs do .NET](storage/blobs/storage-dotnet-how-to-use-blobs.md).

### <a name="to-create-a-blob-container"></a>Para criar um contêiner de blob
1. Abra o menu de atalho no nó de **Blobs** e escolha **Criar Contêiner de Blob**.
2. Na caixa de diálogo **Criar Contêiner de Blob**, insira o nome do novo contêiner.  
3. Pressione Enter no teclado ou você pode clicar ou tocar fora do campo de nome para salvar o contêiner de blob.
   
   > [!NOTE]
   > O nome do contêiner de blob deve começar com uma letra minúscula (a-z) ou um número (0-9).
   > 
   > 

### <a name="to-delete-a-blob-container"></a>Para excluir um contêiner de blob
* Abra o menu de atalho para o contêiner de blob que você deseja remover e selecione **Excluir**.

### <a name="to-display-a-list-of-the-items-in-a-blob-container"></a>Para exibir uma lista de itens em um contêiner de blob
* Abra o menu de atalho para um nome de contêiner de blob na lista e selecione **Abrir**.
  
Quando você exibe o conteúdo de um contêiner de blob, ele aparece em uma guia conhecida como exibição do contêiner de blob.
  
![Exibição do contêiner de blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)
  
Você pode executar as seguintes operações com blobs usando os botões no canto superior direito da exibição do contêiner de blob:
  
* Insira um valor de filtro e aplique-o.
* Atualize a lista de blobs no contêiner.
* Carregar um arquivo.
* Excluir um blob.
    
  > [!NOTE]
  > Excluir um arquivo de um contêiner de blob não exclui o arquivo subjacente. Ele apenas o remove do contêiner de blob.
  > 
  > 
* Abra um blob.
* Salve um blob no computador local.

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>Para criar uma pasta ou subpasta em um contêiner de blob
1. Escolha o contêiner de blob no Cloud Explorer. Na janela do contêiner, selecione o botão **Carregar blob**.
   
2. Na caixa de diálogo **Carregar Novo Arquivo**, selecione o botão **Procurar** para especificar o arquivo que você deseja carregar e digite o nome da pasta na caixa **Pasta (opcional)**.
   
   ![Carregar um arquivo em uma pasta de blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)
    
   Você pode adicionar as subpastas nas pastas de contêiner, seguindo as mesmas etapas. Se você não especificar um nome de pasta, o arquivo será carregado para o nível superior do contêiner de blob. O arquivo aparece na pasta especificada no contêiner.
   
   ![Pasta adicionada a um contêiner de blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)
3. Clique duas vezes na pasta ou pressione Enter para ver o conteúdo da pasta. Quando estiver na pasta do contêiner, você poderá navegar até a raiz do contêiner selecionando o botão **Abrir Diretório Pai** (seta).

### <a name="to-delete-a-container-folder"></a>Para excluir um contêiner
* Exclua todos os arquivos na pasta.
  
> [!NOTE]
> Como as pastas em contêineres de blob são pastas virtuais, você não pode criar uma pasta vazia. Você também não pode excluir uma pasta para excluir o conteúdo de arquivo dela. Você precisa excluir todo o conteúdo de uma pasta para poder excluí-la.
> 
> 

### <a name="to-filter-blobs-in-a-container"></a>Para filtrar os blobs em um contêiner
Você pode filtrar os blobs que são exibidos especificando um prefixo comum.

Por exemplo, se você inserir o prefixo **hello** na caixa de texto do filtro e, em seguida, selecionar o botão **Executar** (**!**), serão exibidos apenas os blobs que começam com "hello".

![Caixa de texto de filtro](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)

> [!NOTE]
> A caixa de texto de filtro diferencia maiúsculas de minúsculas e não dá suporte a filtragem com caracteres curinga. Os blobs só podem ser filtrados pelo prefixo. O prefixo pode incluir um delimitador se você estiver usando um delimitador para organizar blobs em uma hierarquia virtual. Por exemplo, filtrar pelo prefixo "HelloFabric/" retorna todos os blobs que começam com essa cadeia de caracteres.
> 
> 

### <a name="to-download-blob-data"></a>Para baixar os dados de blob
No Gerenciador de nuvem, use qualquer um dos seguintes métodos:
* Abra o menu de atalho para um ou mais blobs e, em seguida, selecione **Abrir**.
* Escolha o nome do blob e selecione o botão **Abrir**.
* Clique duas vezes no nome do blob.
  
O andamento do download de um blob aparece na janela **Log de atividades do Azure** .
  
O blob é aberto no editor padrão desse tipo de arquivo. Se o sistema operacional reconhecer o tipo de arquivo, o arquivo será aberto em um aplicativo instalado localmente. Caso contrário, será solicitado que você escolha um aplicativo apropriado para o tipo de arquivo do blob. O arquivo local criado quando você baixa um blob está marcado como somente leitura.
  
Os dados do blob são armazenados em cache localmente e verificados em relação à hora da última modificação do blob no Armazenamento de Blobs do Azure. Se o blob tiver sido atualizado desde que foi baixado pela última vez, ele será baixado novamente. Caso contrário, o blob será carregado do disco local. 

Por padrão, um blob é baixado para um diretório temporário. Para baixar blobs em um diretório específico, abra o menu de atalho de nomes dos blobs selecionados e selecione **Salvar Como**. Quando você salva um blob dessa maneira, não é possível abrir o arquivo de blob e o arquivo local é criado com atributos de leitura/gravação.

### <a name="to-upload-blobs"></a>Para carregar blobs
* Selecione o botão **Carregar Blob** quando o contêiner for aberto para visualização na exibição do contêiner de blob.
  
Você pode escolher um ou mais arquivos para carregar e pode carregar arquivos de qualquer tipo. A janela **Log de Atividades do Azure** mostra o andamento do upload. Para saber mais sobre como trabalhar com os dados de blob, consulte [Como usar o Armazenamento de Blobs do Azure no .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### <a name="to-view-logs-transferred-to-blobs"></a>Para exibir os logs transferidos para os blobs
Se você estiver usando o Diagnóstico do Azure para registrar dados de seu aplicativo do Azure e tiver transferido logs para sua conta de armazenamento, verá os contêineres que foram criados pelo Azure para esses logs. Exibir esses logs no Gerenciador de Servidores é uma maneira fácil de identificar problemas com seu aplicativo, especialmente se foi implantado no Azure. 

Para saber mais sobre o Diagnóstico do Azure, consulte [Coletar dados do log usando o Diagnóstico do Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="to-get-the-url-for-a-blob"></a>Para obter a URL para um blob
* Abra o menu de atalho do blob e selecione **Copiar URL**.

### <a name="to-edit-a-blob"></a>Para editar um blob
* Selecione o blob e selecione o botão **Abrir Blob**.
  
O arquivo é baixado para um local temporário e aberto no computador local. Você deve carregar o blob novamente depois de fazer alterações.

## <a name="work-with-queue-resources"></a>Trabalhar com recursos de fila
Filas de serviços de armazenamento são hospedadas em uma conta de armazenamento do Azure. Você pode usá-las para permitir que suas funções do serviço de nuvem se comuniquem entre si e com outros serviços por um mecanismo de transmissão de mensagens. Você pode acessar a fila programaticamente por meio de um serviço de nuvem e em um serviço Web para clientes externos. Você também pode acessar a fila diretamente, usando o Gerenciador de Servidores no Visual Studio.

Ao desenvolver um serviço de nuvem que usa filas, talvez você queira usar o Visual Studio para criar filas e trabalhar interativamente com elas enquanto desenvolve e testa seu código.

No Gerenciador de Servidores, você pode exibir as filas em uma conta de armazenamento, criar e excluir filas, abrir uma fila para exibir suas mensagens e adicionar mensagens a uma fila. Quando abrir uma fila para exibição, pode exibir as mensagens individuais e você pode executar as seguintes ações na fila usando os botões no canto superior esquerdo:

* Atualize a exibição da fila.
* Adicione uma mensagem à fila.
* Remover da fila a mensagem de nível mais alto.
* Limpe a fila inteira.

A imagem a seguir mostra uma fila que contém duas mensagens:

![Exibindo uma fila](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

Para obter mais informações sobre a manipulação de filas no código, consulte a [Introdução ao Armazenamento de Filas do Azure usando o .NET](http://go.microsoft.com/fwlink/?LinkID=264702). Para saber mais sobre o serviço Web para filas de serviços de armazenamento, consulte [Conceitos do serviço Fila](http://go.microsoft.com/fwlink/?LinkId=264788). Para saber mais sobre como enviar mensagens a uma fila de serviços de armazenamento usando o Visual Studio, consulte [Enviando mensagens para uma fila de serviços de armazenamento](https://msdn.microsoft.com/library/azure/jj649344.aspx).

> [!NOTE]
> Filas de serviços de armazenamento são diferentes de filas do Barramento de Serviço do Azure. Para obter mais informações sobre filas do barramento de serviço, consulte [Filas do barramento de serviço, tópicos e assinaturas](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-queues-topics-subscriptions).
> 
> 

## <a name="work-with-table-resources"></a>Trabalhar com recursos de tabela
O Armazenamento de Tabelas do Microsoft Azure armazena grandes quantidades de dados estruturados. O serviço é um repositório de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

### <a name="to-create-a-table"></a>Para criar uma tabela
1. No Cloud Explorer, selecione o nó **Tabelas** da conta de armazenamento e selecione **Criar Tabela**.
2. Na caixa de diálogo **Criar Tabela** , insira um nome para a tabela.

### <a name="to-view-table-data"></a>Para exibir dados da tabela
1. No Cloud Explorer, abra o nó **Azure** e clique no nó **Armazenamento**.
2. Abra o nó de conta de armazenamento no qual está interessado e abra o nó **Tabelas** para ver uma lista de tabelas para a conta de armazenamento.
3. Abra o menu de atalho para uma tabela e selecione **Exibir Tabela**.
   
    ![Uma tabela do Azure no Gerenciador de Soluções](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

A tabela é organizada por entidades (mostradas nas linhas) e propriedades (mostradas nas colunas). Por exemplo, a próxima ilustração mostra entidades listadas no Designer de Tabela.

### <a name="to-edit-table-data"></a>Para editar dados da tabela
* No Designer de Tabela, abra o menu de atalho de uma entidade (uma única linha) ou uma propriedade (uma única célula) e selecione **Editar**.
   
    ![Adicionar ou editar uma entidade de tabela](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)
   
Entidades em uma única tabela não precisam ter o mesmo conjunto de propriedades (colunas). Tenha em mente as seguintes restrições ao exibir e editar dados da tabela:
   
* Você não pode exibir ou editar dados binários (`type byte[]`), mas pode armazená-los em uma tabela.
* Você não pode editar os valores **PartitionKey** ou **RowKey**, porque o armazenamento de tabelas no Azure não dá suporte a essa operação.
* Você não pode criar uma propriedade chamada **Timestamp**. Os serviços de armazenamento do Azure usam uma propriedade com esse nome.
* Se você inserir um valor **DateTime**, deve seguir o formato apropriado para as configurações de região e idioma do seu computador (por exemplo, MM/DD/AAAA HH:MM:SS [AM|PM] para o inglês dos EUA).

### <a name="to-add-entities"></a>Para adicionar entidades
1. No Designer de Tabela, escolha o botão **Adicionar Entidade**.
   
    ![Botão Adicionar Entidade](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)
2. Na caixa de diálogo **Adicionar Entidade**, insira os valores das propriedades **PartitionKey** e **RowKey**.
   
    ![Caixa de diálogo Adicionar Entidade](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)
   
    Insira os valores com cuidado. Você não pode alterá-los depois de fechar a caixa de diálogo, a menos que exclua a entidade e adicione-a novamente.

### <a name="to-filter-entities"></a>Para filtrar entidades
Você pode personalizar o conjunto de entidades que aparecem em uma tabela, se usar o construtor de consultas.

1. Para abrir o construtor de consultas, abra uma tabela para exibir.
2. Escolha o botão **Construtor de Consultas** na barra de ferramentas de exibição de tabela.
   
    A caixa de diálogo **Construtor de Consultas** é exibida. A ilustração a seguir mostra uma consulta sendo criada no construtor de consultas.
   
    ![Construtor de consultas](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)
3. Após criar a consulta, feche a caixa de diálogo. O formulário de texto resultante da consulta aparece em uma caixa de texto como um filtro do WCF Data Services.
4. Para executar a consulta, selecione o ícone de triângulo verde.
   
Você também pode filtrar dados de entidade que aparecem no Designer de tabela se inserir uma cadeia de caracteres de filtro do WCF Data Services diretamente na caixa de texto de filtro. Esse tipo de cadeia de caracteres é semelhante a uma cláusula SQL WHERE, mas é enviado ao servidor como uma solicitação HTTP. Para obter informações sobre como construir cadeias de caracteres de filtro, consulte [Construir cadeias de caracteres de filtro para o Designer de tabela](https://msdn.microsoft.com/library/azure/ff683669.aspx).
   
A ilustração a seguir mostra um exemplo de uma cadeia de caracteres de filtro válida:
   
![Cadeia de caracteres de filtro](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

## <a name="refresh-storage-data"></a>Atualizar dados de armazenamento
Quando o Gerenciador de Servidores é conectado ou obtém dados de uma conta de armazenamento, a operação pode levar até um minuto para ser concluída. Se o Gerenciador de Servidores não conseguir conectar, a operação poderá atingir o tempo limite. Enquanto os dados são recuperados, você pode continuar a trabalhar em outras partes do Visual Studio. Para cancelar a operação se demorar muito, selecione o botão **Parar Atualização** na barra de ferramentas do Gerenciador de Servidores.

### <a name="to-refresh-blob-container-data"></a>Para atualizar dados de contêiner de blob
* Selecione o nó **Blobs** sob **Armazenamento** e selecione o botão **Atualizar** na barra de ferramentas do Gerenciador de Servidores.
* Para atualizar a lista de blobs que é exibida, selecione o botão **Executar**.

### <a name="to-refresh-table-data"></a>Para atualizar dados de tabela
* Selecione o nó **Tabelas** sob **Armazenamento** e selecione o botão **Atualizar** na barra de ferramentas do Gerenciador de Servidores.
* Para atualizar a lista de entidades que é exibida no Designer de Tabela, selecione o botão **Executar** no Designer de Tabela.

### <a name="to-refresh-queue-data"></a>Para atualizar os dados da fila
* Selecione o nó **Filas** sob **Armazenamento** e selecione o botão **Atualizar** na barra de ferramentas do Gerenciador de Servidores.

### <a name="to-refresh-all-items-in-a-storage-account"></a>Para atualizar todos os itens em uma conta de armazenamento
* Escolha o nome da conta e selecione o botão **Atualizar** na barra de ferramentas do Gerenciador de Servidores.

## <a name="add-storage-accounts-by-using-server-explorer"></a>Adicionar contas de armazenamento usando o Gerenciador de Servidores
Há duas maneiras de adicionar contas de armazenamento usando o Gerenciador de Servidores. Você pode criar uma conta de armazenamento na sua assinatura do Azure ou pode anexar uma conta de armazenamento existente.

### <a name="to-create-a-storage-account-by-using-server-explorer"></a>Para criar uma conta de armazenamento usando o Gerenciador de Servidores
1. No Gerenciador de Servidores, abra o menu de atalho para o nó **Armazenamento** e selecione **Criar Conta de Armazenamento**.
   
2. Na caixa de diálogo **Criar Conta de Armazenamento**, selecione ou insira as seguintes informações:
   
   * A assinatura do Azure à qual você deseja adicionar a conta de armazenamento.
   * O nome que você deseja usar para a nova conta de armazenamento.
   * A região ou o grupo de afinidade (como Oeste dos EUA ou Leste Asiático).
   * O tipo de replicação que você deseja usar para a conta de armazenamento, tal como localmente redundante.

   ![Criar uma conta de armazenamento do Azure](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

3. Selecione **Criar**.
   
A nova conta de armazenamento aparece na lista **Armazenamento** no Gerenciador de Soluções.

### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>Para anexar uma conta de armazenamento existente usando o Gerenciador de Servidores
1. No Gerenciador de Servidores, abra o menu de atalho para o nó **Armazenamento** do Azure e selecione **Anexar Armazenamento Externo**.
   
    ![Adicionar uma conta de armazenamento existente](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)
2. Na caixa de diálogo **Criar Conta de Armazenamento**, selecione ou insira as seguintes informações:
   
   * O nome da conta de armazenamento existente que você deseja anexar.
   * A chave para a conta de armazenamento selecionado. Esse valor geralmente é fornecido para você quando seleciona uma conta de armazenamento. Se desejar que o Visual Studio se lembre da chave de conta de armazenamento, marque a caixa de seleção **Lembrar chave de conta**.
   * O protocolo a ser usado para se conectar à conta de armazenamento, como HTTP, HTTPS ou um ponto de extremidade personalizado. Para obter mais informações sobre pontos de extremidade personalizados, consulte [Como configurar cadeias de conexão](https://msdn.microsoft.com/library/azure/ee758697.aspx).

### <a name="to-view-the-secondary-endpoints"></a>Para exibir os pontos de extremidade secundários
Se você criou uma conta de armazenamento usando a opção de replicação **Redundância Geográfica com Acesso de Leitura**, você pode exibir os pontos de extremidade secundários dela:

* Abra o menu de atalho para o nome da conta e, em seguida, selecione **Propriedades**.
  
![Pontos de extremidade de armazenamento secundários](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>Para remover uma conta de armazenamento do Gerenciador de Servidores
* No Gerenciador de Servidores, abra o menu de atalho para o nome da conta e, em seguida, selecione **Excluir**. 

Se você excluir uma conta de armazenamento, qualquer informação de chave salva para essa conta também é removida.
  
> [!NOTE]
> Se você excluir uma conta de armazenamento do Gerenciador de Servidores, isto não afetará sua conta de armazenamento nem nenhum dos os dados que ela contém. Isso simplesmente remove a referência do Gerenciador de Servidores. Para excluir permanentemente uma conta de armazenamento, use o [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como usar os Serviços de Armazenamento do Azure, consulte [Acessando os Serviços de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/ee405490.aspx).

