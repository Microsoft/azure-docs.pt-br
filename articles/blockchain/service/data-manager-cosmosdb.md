---
title: Usar o Gerenciador de Dados do Blockchain para atualizar o Azure Cosmos DB – Azure Blockchain Service
description: Usar o Gerenciador de Dados do Blockchain para o Azure Blockchain Service para enviar dados de blockchain para o Azure Cosmos DB
ms.date: 03/08/2020
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 69790787bc888448f2f40178bd12ee7058cc5892
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91283439"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Tutorial: Usar o Gerenciador de Dados do Blockchain para enviar dados para o Azure Cosmos DB

Neste tutorial, você usará o Gerenciador de Dados do Blockchain para o Azure Blockchain Service para registrar dados de transação de blockchain no Azure Cosmos DB. O Gerenciador de Dados do Blockchain captura, transforma e entrega dados de blockchain do razão para tópicos da Grade de Eventos do Azure. Na Grade de Eventos do Azure, você usa um conector de Aplicativo Lógico do Azure para criar documentos em um banco de dados Azure Cosmos DB. Quando terminar com o tutorial, você poderá explorar os dados de transação de blockchain no Data Explorer do Azure Cosmos DB.

[![Captura de tela mostrando detalhes da transação de blockchain.](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

Neste tutorial, você:

> [!div class="checklist"]
> * Criar uma instância do Gerenciador de Dados do Blockchain
> * Adicionar um aplicativo de blockchain para decodificar propriedades e eventos de transação
> * Criar uma conta do Azure Cosmos DB e um banco de dados para armazenar dados de transação
> * Criar um Aplicativo Lógico do Azure para conectar um tópico da Grade de Eventos do Azure ao Azure Cosmos DB
> * Enviar uma transação para um razão do blockchain
> * Exibir os dados de transação decodificados no Azure Cosmos DB

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Concluir [Início Rápido: Criar um membro do blockchain usando o portal do Azure](create-member.md) ou [Início Rápido: Criar um membro do blockchain do Azure Blockchain Service usando a CLI do Azure](create-member-cli.md)
* Concluir [Início Rápido: Usar o Visual Studio Code para se conectar a uma rede de consórcio do Azure Blockchain Service](connect-vscode.md). O início rápido orienta você pela instalação do [Azure Blockchain Development Kit para Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) e pela configuração do ambiente de desenvolvimento do blockchain.
* [Tutorial completo: Usar o Visual Studio Code para criar, compilar e implantar contratos inteligentes](send-transaction.md). O tutorial orienta a criação de um contrato inteligente de exemplo.
* Criar um [tópico da Grade de Eventos](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Aprenda sobre [Manipuladores de eventos na Grade de Eventos do Azure](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Criar instância

Uma instância de Gerenciador de Dados do Blockchain conecta e monitora um nó de transação Azure Blockchain Service. Uma instância captura todos os dados brutos de bloqueio e de transação provenientes do nó de transação. Uma conexão de saída envia os dados de blockchain para a Grade de Eventos do Azure. Você configura uma conexão de saída única quando cria a instância.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Vá para o membro do Azure Blockchain Service que você criou no [Início Rápido: Criar um membro do blockchain usando o portal do Azure](create-member.md), pré-requisito para a etapa atual. Selecione **Gerenciador de Dados do Blockchain**.
1. Selecione **Adicionar**.

    ![Adicionar Gerenciador de Dados do Blockchain](./media/data-manager-cosmosdb/add-instance.png)

    Insira os seguintes detalhes:

    Configuração | Exemplo | Descrição
    --------|---------|------------
    Nome | mywatcher | Insira um nome exclusivo para um Gerenciador de Dados do Blockchain conectado.
    Nó de transação | myblockchainmember | Escolha o nó de transação padrão do membro do Azure Blockchain Service que você criou na etapa de pré-requisito.
    Nome da conexão | cosmosdb | Insira um nome exclusivo para a conexão de saída pela qual os dados da transação de blockchain são enviados.
    Ponto de extremidade da Grade de Eventos | myTopic | Escolha um tópico da Grade de Eventos criada no pré-requisito. Observação: A instância de Gerenciador de Dados do Blockchain e o tópico da Grade de Eventos devem estar na mesma assinatura.

1. Selecione **OK**.

    Leva menos de um minuto para criar uma instância do Gerenciador de Dados do Blockchain. Depois que a instância for implantada, ela será iniciada automaticamente. Uma instância do Gerenciador de Dados do Blockchain que está em execução captura eventos de blockchain do nó de transação e envia os dados para a Grade de Eventos.

## <a name="add-application"></a>Adicionar aplicativo

Adicione o aplicativo de blockchain **helloblockchain** para que o Gerenciador de Dados do Blockchain decodifique o estado do evento e o estado da propriedade. Para que o Gerenciador de Dados do Blockchain possa adicionar o aplicativo, são necessários a ABI do contrato inteligente e o arquivo de código de bytes.

### <a name="get-contract-abi-and-bytecode"></a>Obter a ABI do contrato e código de bytes

O ABI do contrato define as interfaces do contrato inteligente. Ele descreve como interagir com o contrato inteligente. Você pode usar a [Extensão Azure Blockchain Development Kit for Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) para copiar a ABI do contrato para a área de transferência.

1. No painel gerenciador do Visual Studio Code, expanda a pasta **build/contracts** do projeto **helloblockchain** da Solidity que você criou no [Tutorial: Usar o Visual Studio Code para criar, compilar e implantar contratos inteligentes](send-transaction.md), pré-requisito para a etapa atual.
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Selecione **Copiar ABI do Contrato**.

    ![Painel do Visual Studio Code com a seleção de Copiar ABI do Contrato](./media/data-manager-cosmosdb/abi-devkit.png)

    O ABI do contrato é copiado para a área de transferência.

1. Salve a matriz **abi** como um arquivo JSON. Por exemplo, *abi.json*. Você usará esse arquivo em uma etapa posterior.

O Gerenciador de Dados do Blockchain requer o código de bytes implantado para o contrato inteligente. O código de bytes implantado é diferente do código de bytes do contrato inteligente. Use a extensão do Azure Blockchain Development Kit para copiar o código de bytes para a área de transferência.

1. No painel do Visual Studio Code Explorer, expanda a pasta **build/contracts** do projeto do Solidity.
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Selecione **Copiar Código de Bites de Transação**.

    ![Painel do Visual Studio Code com a seleção de Copiar Código de Bytes da Transação](./media/data-manager-cosmosdb/bytecode-devkit.png)

    O código de bytes é copiado para a área de transferência.

1. Salve o valor de **código de bytes** como um arquivo JSON. Por exemplo, *bytecode.json*. Você usará esse arquivo em uma etapa posterior.

O exemplo a seguir mostra os arquivos *abi.json* e *bytecode.json* abertos no editor do VS Code. Os arquivos devem ser semelhantes.

![Exemplo de arquivos abi.json e bytecode.json](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Criar a ABI do contrato e a URL do código de bytes

O Gerenciador de Dados do Blockchain requer que os arquivos de código de bytes e da ABI e do contrato sejam acessíveis por meio de uma URL ao adicionar um aplicativo. Você pode usar uma conta de Armazenamento do Azure para fornecer uma URL acessível de modo privado.

#### <a name="create-storage-account"></a>Criar Conta de Armazenamento

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Fazer upload de arquivos de contrato

1. Crie um novo contêiner para a conta de armazenamento. Selecione **Contêineres > Contêiner**.

    ![Criar um contêiner de conta de armazenamento](./media/data-manager-cosmosdb/create-container.png)

    | Configuração | Descrição |
    |---------|-------------|
    | Nome  | Dê um nome ao contêiner. Por exemplo, *smartcontract* |
    | Nível de acesso público | Escolha *Privado (sem acesso anônimo)* |

1. Selecione **OK** para criar o contêiner.
1. Selecione o contêiner e selecione **Upload**.
1. Escolha os arquivos JSON que você criou na seção [Obter a ABI do contrato e o código de bytes](#get-contract-abi-and-bytecode).

    ![Carregar blob](./media/data-manager-cosmosdb/upload-blobs.png)

    Escolha **Carregar**.

#### <a name="generate-url"></a>Gerar a URL

Para cada blob, gere uma assinatura de acesso compartilhado.

1. Selecione o blob JSON da ABI.
1. Selecione **Gerar SAS**
1. Defina a expiração desejada para a assinatura de acesso e, em seguida, selecione **Gerar token SAS de blob e URL**.

    ![Gerar token SAS](./media/data-manager-cosmosdb/generate-sas.png)

1. Copie a **URL de SAS do blob** e salve-a para a próxima seção.
1. Repita as etapas de [Gerar URL](#generate-url) para o blob do código de bytes JSON.

### <a name="add-helloblockchain-application-to-instance"></a>Adicionar aplicativo helloblockchain à instância

1. Selecione sua instância de Gerenciador de Dados do Blockchain na lista de instâncias.
1. Selecione **Aplicativos de blockchain**.
1. Selecione **Adicionar**.

    ![Adicionar um aplicativo de blockchain](./media/data-manager-cosmosdb/add-application.png)

    Insira o nome do aplicativo de blockchain, a URL da ABI do contrato inteligente e a URL do código de bytes.

    Configuração | Descrição
    --------|------------
    Nome | Insira um nome exclusivo para o aplicativo de blockchain rastrear.
    ABI do Contrato | Caminho da URL para o arquivo da ABI do contrato. Para obter mais informações, confira [Criar a ABI do contrato e a URL do código de bytes](#create-contract-abi-and-bytecode-url).
    Código de bytes do contrato | Caminho da URL para o arquivo de código de bytes. Para obter mais informações, confira [Criar a ABI do contrato e a URL do código de bytes](#create-contract-abi-and-bytecode-url).

1. Selecione **OK**.

    Depois que o aplicativo é criado, ele aparece na lista de aplicativos de blockchain.

    ![Lista de aplicativos de blockchain](./media/data-manager-cosmosdb/artifact-list.png)

Você pode excluir a conta de Armazenamento do Azure ou usá-la para configurar mais aplicativos de blockchain. Se quiser excluir a conta de Armazenamento do Azure, você poderá excluir o grupo de recursos. A exclusão do grupo de recursos também exclui a conta de armazenamento associada e todos os outros recursos associados ao grupo de recursos.

## <a name="create-azure-cosmos-db"></a>Criar Azure Cosmos DB

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Adicionar um banco de dados e um contêiner

Você pode usar o Data Explorer no portal do Azure para criar um banco de dados e um contêiner.

1. Selecione **Data Explorer** no painel de navegação à esquerda na página de sua conta do Azure Cosmos DB e, em seguida, selecione **Novo Contêiner**.
1. No painel **Adicionar contêiner**, insira as configurações do novo contêiner.

    ![Adicionar configurações do contêiner](./media/data-manager-cosmosdb/add-container.png)

    | Configuração | Descrição
    |---------|-------------|
    | ID do banco de dados | Insira **blockchain-data** como o nome para o novo banco de dados. |
    | Produtividade | Deixe a taxa de transferência em **400** RU/s (unidades de solicitação por segundo). Se quiser reduzir a latência, você poderá escalar verticalmente a taxa de transferência mais tarde.|
    | ID do contêiner | Insira **Mensagens** como o nome do novo contêiner. |
    | Chave de partição | Use **/MessageType** como a chave de partição. |

1. Selecione **OK**. O Data Explorer exibe o novo banco de dados e o contêiner que você criou.

## <a name="create-logic-app"></a>Criar aplicativo lógico

Os Aplicativos Lógicos do Azure ajudam você a agendar e automatizar processos empresariais e fluxos de trabalho quando precisar integrar sistemas e serviços. Você pode usar um aplicativo lógico para conectar a Grade de Eventos ao Azure Cosmos DB.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Integração** > **Aplicativo Lógico**.
1. Forneça detalhes sobre a localização em que o aplicativo lógico deverá ser criado. Quando terminar, selecione **Criar**.

    Para obter mais informações sobre como criar aplicativos lógicos, confira [Criar fluxos de trabalho automatizados com os Aplicativos Lógicos do Azure](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Depois que o Azure implantar seu aplicativo, selecione o recurso do aplicativo lógico.
1. No Designer de Aplicativos Lógicos, em **Modelos**, selecione **Aplicativo Lógico em Branco**.

### <a name="add-event-grid-trigger"></a>Adicionar gatilho da Grade de Eventos

Cada aplicativo lógico deve começar com um gatilho, que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o disparador é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico que inicia e executa o fluxo de trabalho. Use um gatilho de Grade de Eventos do Azure para enviar dados de transação de blockchain da Grade de Eventos para o Cosmos DB.

1. No Designer de Aplicativos Lógicos, pesquise e selecione o conector **Grade de Eventos do Azure**.
1. Na guia **Gatilhos**, selecione **Quando um evento de recurso ocorrer**.
1. Crie uma conexão de API ao seu Tópico de Grade de Eventos.

    ![Configurações de gatilho de Grade de Eventos](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Configuração | Descrição
    |---------|-------------|
    | Subscription | Escolha uma assinatura que contenha o Tópico de Grade de Eventos. |
    | Tipo de recurso | Escolha **Microsoft.EventGrid.Topics**. |
    | Nome do Recurso | Escolha o nome do Tópico da Grade de Eventos no qual o Gerenciador de Dados do Blockchain está enviando mensagens de dados de transação. |

### <a name="add-cosmos-db-action"></a>Adicionar ação do Cosmos DB

Adicione uma ação para criar um documento no Cosmos DB para cada transação. Use o tipo de mensagem de transação como a chave de partição para categorizar as mensagens.

1. Selecione **Nova etapa**.
1. Em **Escolher uma ação**, procure **Azure Cosmos DB**.
1. Escolha **Azure Cosmos DB > Ações > Criar ou atualizar documento**.
1. Crie uma conexão de API ao seu banco de dados do Cosmos DB.

    ![Configurações de conexão do Cosmos DB](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Configuração | Descrição
    |---------|-------------|
    | Nome da Conexão | Escolha uma assinatura que contenha o Tópico de Grade de Eventos. |
    | Conta do DocumentDB | Escolha a conta do DocumentDB que você criou na seção [Criar conta do Azure Cosmos DB](#create-azure-cosmos-db). |

1. Insira a **ID do banco de dados** e a **ID da coleção** para o Azure Cosmos DB que você criou anteriormente na seção [Adicionar um banco de dados e um contêiner](#add-a-database-and-container).

1. Selecione a configuração **Documento**. No pop-out *Adicionar conteúdo dinâmico*, selecione **Expressão** e copie e cole a seguinte expressão:

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    A expressão obtém a parte de dados da mensagem e define a ID como um valor de carimbo de data/hora.

1. Selecione **Adicionar novo parâmetro** e escolha **Valor da chave de partição**.
1. Defina o **Valor da chave de partição** como `"@{triggerBody()['data']['MessageType']}"`. O valor deve estar entre aspas duplas.

    ![Designer de Aplicativos Lógicos com configurações do Cosmos DB](./media/data-manager-cosmosdb/create-action.png)

    O valor define a chave de partição para o tipo de mensagem de transação.

1. Clique em **Salvar**.

O aplicativo lógico monitora o Tópico de Grade de Eventos. Quando uma nova mensagem de transação é enviada do Gerenciador de Dados do Blockchain, o aplicativo lógico cria um documento no Cosmos DB.

## <a name="send-a-transaction"></a>Enviar uma transação

Em seguida, envie uma transação para o razão do blockchain para testar o que você criou. Use a função **SendRequest** do contrato do **HelloBlockchain** que você criou no pré-requisito [Tutorial: Usar o Visual Studio Code para criar, compilar e implantar contratos inteligentes](send-transaction.md), pré-requisito para a etapa atual.

1. Use a página de interação do contrato inteligente do Azure Blockchain Development Kit para chamar a função **SendRequest**. Clique com o botão direito do mouse em **HelloBlockchain.sol** e escolha **Mostrar Página de Interação do Contrato Inteligente** no menu.

    ![Escolha Mostrar Página de Interação do Contrato Inteligente no menu](./media/data-manager-cosmosdb/contract-interaction.png)

1. Escolha ação de contrato **SendRequest** e insira **Olá, Blockchain!** no parâmetro **requestMessage**. Selecione **Executar** para chamar a função **SendRequest** por meio de uma transação.

    ![Executar ação SendRequest](./media/data-manager-cosmosdb/sendrequest-action.png)

A função SendRequest define os campos **RequestMessage** e **Estado**. O estado atual para **RequestMessage** é o argumento para o qual você passou **Olá, Blockchain**. O valor do campo **Estado** permanece **Solicitação**.

## <a name="view-transaction-data"></a>Exibir data da transação

Agora que você conectou seu Gerenciador de Dados do Blockchain ao Azure Cosmos DB, você pode exibir as mensagens de transação de blockchain no Data Explorer do Cosmos DB.

1. Vá para a exibição do Data Explorer do Cosmos DB. Por exemplo, **cosmosdb-blockchain > Data Explorer > blockchain-data > Mensagens > Itens**.

    ![Data Explorer do Cosmos DB](./media/data-manager-cosmosdb/data-explorer.png)

    O Data Explorer lista as mensagens de dados de blockchain que foram criadas no banco de dados do Cosmos DB.

1. Navegue pelas mensagens selecionando a ID do item e localize a mensagem com o hash de transação correspondente.

    [![Captura de tela mostrando detalhes da transação de blockchain de um item selecionado.](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    A mensagem de transação bruta contém detalhes sobre a transação. No entanto, as informações de propriedade são criptografadas.

    Já que você adicionou o contrato inteligente HelloBlockchain à instância de Gerenciador de Dados do Blockchain, um tipo de mensagem **ContractProperties** também é enviado e contém informações de propriedade decodificadas.

1. Localize a mensagem **ContractProperties** para a transação. Ela deve ser a próxima mensagem na lista.

    [![Detalhes da transação de blockchain](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    A matriz **DecodedProperties** contém as propriedades da transação.

Parabéns! Você criou com êxito um gerenciador de mensagens de transação usando o Gerenciador de Dados do Blockchain e o Azure Cosmos DB.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos e grupos de recursos que você usou para este tutorial não forem mais necessários, será possível excluí-los. Para excluir um grupo de recursos:

1. No portal do Azure, navegue até **Grupo de recursos** no painel de navegação esquerdo e selecione o grupo de recursos que você deseja excluir.
1. Selecione **Excluir grupo de recursos**. Verifique a exclusão digitando o nome do grupo de recursos e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a integração com os razões do blockchain.

> [!div class="nextstepaction"]
> [Usar o conector do Ethereum Blockchain com os Aplicativos Lógicos do Azure](ethereum-logic-app.md)
