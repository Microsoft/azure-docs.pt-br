---
title: Configurar Gerenciador de Dados Blockchain usando portal do Azure-serviço Blockchain do Azure
description: Criar e gerenciar Blockchain Gerenciador de Dados para o serviço Blockchain do Azure usando o portal do Azure.
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: a151c28d31bf0bb7f21185fb161315d42f9563d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85200674"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Configurar o Gerenciador de Dados Blockchain usando o portal do Azure

Configure o Blockchain Gerenciador de Dados para o serviço Blockchain do Azure para capturar dados do Blockchain e enviá-los para um tópico da grade de eventos do Azure.

Para configurar uma instância de Gerenciador de Dados do Blockchain, você:

* Criar uma instância de Gerenciador de Dados do Blockchain para um nó de transação do serviço Blockchain do Azure
* Adicionar seus aplicativos blockchain

## <a name="prerequisites"></a>Pré-requisitos

* Conclua [o início rápido: criar um membro do blockchain usando o portal do Azure](create-member.md) ou [início rápido: criar um membro Blockchain do serviço blockchain do Azure usando o CLI do Azure](create-member-cli.md). A camada *Standard* do serviço Blockchain do Azure é recomendada ao usar o Blockchain Gerenciador de dados.
* Criar um [tópico da Grade de Eventos](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Aprenda sobre [Manipuladores de eventos na Grade de Eventos do Azure](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Criar instância

Uma instância de Gerenciador de Dados do Blockchain conecta e monitora um nó de transação Azure Blockchain Service. Somente os usuários com acesso ao nó de transação podem criar uma conexão. Uma instância captura todos os dados brutos de bloqueio e de transação provenientes do nó de transação. Blockchain Gerenciador de Dados publica uma mensagem **RawBlockAndTransactionMsg** que é um superconjunto de informações retornadas das consultas [getblock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) e [gettransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) de Web3. ETH.

Uma conexão de saída envia os dados de blockchain para a Grade de Eventos do Azure. Você configura uma conexão de saída única quando cria a instância. O Gerenciador de Dados de Blockchain é compatível com várias conexões de saída aos Tópico da Grade de Eventos para qualquer instância especificada do Gerenciador de Dados de Blockchain. Você pode enviar dados de blockchain para um único destino ou para vários destinos. Para adicionar outro destino, basta adicionar outras conexões de saída à instância.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Vá para o membro do serviço Blockchain do Azure que você deseja conectar ao Blockchain Gerenciador de Dados. Selecione **Gerenciador de Dados do Blockchain**.
1. Selecione **Adicionar**.

    ![Adicionar Gerenciador de Dados do Blockchain](./media/data-manager-portal/add-instance.png)

    Insira os seguintes detalhes:

    Configuração | Descrição
    --------|------------
    Nome | Insira um nome exclusivo para um Gerenciador de Dados do Blockchain conectado. O nome de Gerenciador de Dados Blockchain pode conter letras minúsculas e números e tem um comprimento máximo de 20 caracteres.
    Nó de transação | Escolha um nó de transação. Somente os nós de transação aos quais você tem acesso de leitura são listados.
    Nome da conexão | Insira um nome exclusivo para a conexão de saída pela qual os dados da transação de blockchain são enviados.
    Ponto de extremidade da Grade de Eventos | Escolha um tópico de grade de eventos na mesma assinatura que a instância de Gerenciador de Dados do Blockchain.

1. Selecione **OK**.

    Leva menos de um minuto para criar uma instância do Gerenciador de Dados do Blockchain. Depois que a instância for implantada, ela será iniciada automaticamente. Uma instância de Gerenciador de Dados em execução Blockchain captura eventos de Blockchain do nó de transação e envia dados para as conexões de saída.

    A nova instância aparece na lista de instâncias de Gerenciador de Dados Blockchain para o membro do serviço Blockchain do Azure.

    ![Lista de instâncias de membro de dados Blockchain](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Adicionar aplicativo blockchain

Se você adicionar um aplicativo blockchain, Blockchain Gerenciador de Dados decodificará o evento e o estado da propriedade para o aplicativo. Caso contrário, somente os dados brutos de bloco e de transação bruto serão enviados. Blockchain Gerenciador de Dados também descobre os endereços de contrato quando o contrato é implantado. Você pode adicionar vários aplicativos blockchain a uma instância de Gerenciador de Dados do Blockchain.

> [!IMPORTANT]
> Atualmente, os aplicativos blockchain que declaram [tipos de matriz](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) de solidez ou [tipos de mapeamento](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) não têm suporte total. As propriedades declaradas como tipos de matriz ou de mapeamento não serão decodificadas em mensagens *ContractPropertiesMsg* ou *DecodedContractEventsMsg* .

O Blockchain Gerenciador de Dados requer uma ABI de contrato inteligente e um arquivo de código de bytes implantado para adicionar o aplicativo.

### <a name="get-contract-abi-and-bytecode"></a>Obter ABI do contrato e código de bytes

O ABI do contrato define as interfaces do contrato inteligente. Ele descreve como interagir com o contrato inteligente. Você pode usar a [Extensão Azure Blockchain Development Kit for Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) para copiar a ABI do contrato para a área de transferência.

1. No painel do Visual Studio Code Explorer, expanda a pasta **build/contracts** do projeto do Solidity.
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Selecione **Copiar ABI do Contrato**.

    ![Painel do Visual Studio Code com a seleção de Copiar ABI do Contrato](./media/data-manager-portal/abi-devkit.png)

    O ABI do contrato é copiado para a área de transferência.

1. Salve a matriz **abi** como um arquivo JSON. Por exemplo, *abi.json*. Você usará esse arquivo em uma etapa posterior.

O Gerenciador de Dados do Blockchain requer o código de bytes implantado para o contrato inteligente. O código de bytes implantado é diferente do código de bytes do contrato inteligente. Use a extensão do Azure Blockchain Development Kit para copiar o código de bytes para a área de transferência.

1. No painel do Visual Studio Code Explorer, expanda a pasta **build/contracts** do projeto do Solidity.
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Selecione **Copiar Código de Bites de Transação**.

    ![Painel do Visual Studio Code com a seleção de Copiar Código de Bytes da Transação](./media/data-manager-portal/bytecode-devkit.png)

    O código de bytes é copiado para a área de transferência.

1. Salve o valor de **código de bytes** como um arquivo JSON. Por exemplo, *bytecode.json*. Você usará esse arquivo em uma etapa posterior.

O exemplo a seguir mostra os arquivos *abi.json* e *bytecode.json* abertos no editor do VS Code. Os arquivos devem ser semelhantes.

![Exemplo de arquivos abi.json e bytecode.json](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Criar a ABI do contrato e a URL do código de bytes

O Gerenciador de Dados do Blockchain requer que os arquivos de código de bytes e da ABI e do contrato sejam acessíveis por meio de uma URL ao adicionar um aplicativo. Você pode usar uma conta de Armazenamento do Azure para fornecer uma URL acessível de modo privado.

#### <a name="create-storage-account"></a>Criar Conta de Armazenamento

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Fazer upload de arquivos de contrato

1. Crie um novo contêiner para a conta de armazenamento. Selecione **Contêineres > Contêiner**.

    ![Criar um contêiner de conta de armazenamento](./media/data-manager-portal/create-container.png)

    | Campo | Descrição |
    |-------|-------------|
    | Nome  | Dê um nome ao contêiner. Por exemplo, *smartcontract* |
    | Nível de acesso público | Escolha *Privado (sem acesso anônimo)* |

1. Selecione **OK** para criar o contêiner.
1. Selecione o contêiner e selecione **Upload**.
1. Escolha os arquivos JSON que você criou na seção [Obter a ABI do contrato e o código de bytes](#get-contract-abi-and-bytecode).

    ![Carregar blob](./media/data-manager-portal/upload-blobs.png)

    Escolha **Carregar**.

#### <a name="generate-url"></a>Gerar a URL

Para cada blob, gere uma assinatura de acesso compartilhado.

1. Selecione o blob JSON da ABI.
1. Selecione **Gerar SAS**
1. Defina a expiração desejada para a assinatura de acesso e, em seguida, selecione **Gerar token SAS de blob e URL**.

    ![Gerar token SAS](./media/data-manager-portal/generate-sas.png)

1. Copie a **URL de SAS do blob** e salve-a para a próxima seção.
1. Repita as etapas de [Gerar URL](#generate-url) para o blob do código de bytes JSON.

### <a name="add-application-to-instance"></a>Adicionar aplicativo à instância

1. Selecione sua instância de Gerenciador de Dados do Blockchain na lista de instâncias.
1. Selecione **Aplicativos de blockchain**.
1. Selecione **Adicionar**.

    ![Adicionar um aplicativo de blockchain](./media/data-manager-portal/add-application.png)

    Insira o nome do aplicativo de blockchain, a URL da ABI do contrato inteligente e a URL do código de bytes.

    Configuração | Descrição
    --------|------------
    Nome | Insira um nome exclusivo para o aplicativo de blockchain rastrear.
    ABI do Contrato | Caminho da URL para o arquivo da ABI do contrato. Para obter mais informações, confira [Criar a ABI do contrato e a URL do código de bytes](#create-contract-abi-and-bytecode-url).
    Código de bytes do contrato | Caminho da URL para o arquivo de código de bytes. Para obter mais informações, confira [Criar a ABI do contrato e a URL do código de bytes](#create-contract-abi-and-bytecode-url).

1. Selecione **OK**.

    Depois que o aplicativo é criado, ele aparece na lista de aplicativos de blockchain.

    ![Lista de aplicativos de blockchain](./media/data-manager-portal/artifact-list.png)

Você pode excluir a conta de Armazenamento do Azure ou usá-la para configurar mais aplicativos de blockchain. Se quiser excluir a conta de Armazenamento do Azure, você poderá excluir o grupo de recursos. A exclusão do grupo de recursos também exclui a conta de armazenamento associada e todos os outros recursos associados ao grupo de recursos.

## <a name="stop-instance"></a>Parar instância

Pare a instância do Gerenciador de Blockchain quando desejar parar de capturar eventos de Blockchain e enviar dados para as conexões de saída. Quando a instância é interrompida, nenhum encargo é incorrido para Blockchain Gerenciador de Dados. Para obter mais informações, consulte [preços](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Acesse **visão geral** e selecione **parar**.

    ![Parar instância](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Próximas etapas

Experimente o próximo tutorial Criando um Gerenciador de mensagens de transação blockchain usando o Blockchain Gerenciador de Dados e o Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Usar o Gerenciador de Dados do Blockchain para enviar dados para o Azure Cosmos DB](data-manager-cosmosdb.md)