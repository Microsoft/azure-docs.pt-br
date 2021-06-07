---
title: Usar o conector do Ethereum Blockchain com o aplicativo lógico do Azure-serviço Blockchain do Azure
description: Use o conector do Ethereum Blockchain com os Aplicativos Lógicos do Azure para disparar funções de contrato inteligente e responder a eventos de contrato inteligente.
ms.date: 08/31/2020
ms.topic: how-to
ms.reviewer: caleteet
ms.openlocfilehash: 411337908553e58c252a0ed1a42d17f76195c720
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96763784"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Usar o conector do Ethereum Blockchain com os Aplicativos Lógicos do Azure

Use o [conector do Ethereum Blockchain](/connectors/blockchainethereum/) com os [Aplicativos Lógicos do Azure](../../logic-apps/index.yml) para executar ações de contrato inteligente e responder a eventos de contrato inteligente. Este artigo explica como você pode usar o conector Ethereum Blockchain para enviar informações de Blockchain para outro serviço ou chamar uma função Blockchain. Por exemplo, digamos que você deseje criar um microsserviço baseado em REST que retorna informações de um razão do blockchain. Usando um aplicativo lógico, você pode aceitar solicitações HTTP que consultam informações armazenadas em um razão do blockchain.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua o início rápido de pré-requisito opcional [: Use Visual Studio Code para se conectar a uma rede do Azure Blockchain Service Consortium](connect-vscode.md). O início rápido orienta você pela instalação do [Azure Blockchain Development Kit para Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) e pela configuração do ambiente de desenvolvimento do blockchain.
- Se você for novo no aplicativo lógico do Azure, considere examinar os módulos de Microsoft Learn [introdução aos aplicativos lógicos do Azure](/learn/modules/intro-to-logic-apps/) e [chamar uma API de um fluxo de trabalho de aplicativos lógicos usando um conector personalizado](/learn/modules/logic-apps-and-custom-connectors/).

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico

Os Aplicativos Lógicos do Azure ajudam você a agendar e automatizar processos empresariais e fluxos de trabalho quando precisar integrar sistemas e serviços. Primeiro, crie uma lógica que usa o conector do Ethereum Blockchain.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Integração** > **Aplicativo Lógico**.
1. Em **Criar aplicativo lógico**, forneça detalhes da localização em que o aplicativo lógico deverá ser criado. Quando terminar, selecione **Criar**.

    Para obter mais informações sobre como criar aplicativos lógicos, confira [Criar fluxos de trabalho automatizados com os Aplicativos Lógicos do Azure](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Depois que o Azure implantar seu aplicativo, selecione o recurso do aplicativo lógico.
1. No Designer de Aplicativos Lógicos, em **Modelos**, selecione **Aplicativo Lógico em Branco**.

Cada aplicativo lógico deve começar com um gatilho, que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o disparador é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico que inicia e executa o fluxo de trabalho.

O conector do Ethereum Blockchain tem um gatilho e várias ações. O gatilho ou a ação que você usará depende do cenário. Siga a seção neste artigo que melhor corresponde ao seu cenário.

Se o fluxo de trabalho:

* Dispara quando um evento ocorre no blockchain, [use o gatilho de evento](#use-the-event-trigger).
* Consultas ou implanta um contrato inteligente, [use ações](#use-actions).
* Segue um cenário comum, [gere um fluxo de trabalho usando o kit de desenvolvedor](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Usar o gatilho de evento

Use gatilhos de evento do Ethereum Blockchain quando desejar que um aplicativo lógico seja executado após um evento de contrato inteligente. Por exemplo, você deseja enviar um email quando uma função de contrato inteligente é chamada.

1. No Designer de Aplicativos Lógicos, selecione o conector do Ethereum Blockchain.
1. Na guia **Disparar**, selecione **Quando um evento de contrato inteligente ocorrer**.
1. Altere ou [crie uma conexão de API](#create-an-api-connection) para o Azure Blockchain Service.
1. Insira os detalhes sobre o contrato inteligente que deseja verificar em busca de eventos.

    ![Designer de Aplicativos Lógicos com propriedades do Gatilho de evento](./media/ethereum-logic-app/event-properties.png)

    | Propriedade | Descrição |
    |----------|-------------|
    | **ABI do Contrato** | O ABI (interface binária de aplicativo) do contrato define as interfaces do contrato inteligente. Para obter mais informações, confira [Obter o ABI do contrato](#get-the-contract-abi). |
    | **Endereço do contrato inteligente** | O endereço do contrato é o endereço de destino do contrato inteligente no Ethereum Blockchain. Para obter mais informações, confira [Obter o endereço do contrato](#get-the-contract-address). |
    | **Nome do evento** | Selecione um evento de contrato inteligente a ser verificado. O evento dispara o aplicativo lógico. |
    | **Intervalo** e **Frequência** | Selecione a frequência com que deseja verificar se há eventos. |

1. Clique em **Salvar**.

Para concluir o aplicativo lógico, você pode adicionar uma nova etapa que executa uma ação com base no gatilho de evento do Ethereum Blockchain. Por exemplo, envie um email.

## <a name="use-actions"></a>Usar ações

Use as ações do Ethereum Blockchain quando desejar que um aplicativo lógico execute uma ação no razão do blockchain. Por exemplo, você deseja criar um microsserviço baseado em REST que chama uma função de contrato inteligente quando uma solicitação HTTP é feita a um aplicativo lógico.

As ações do conector exigem um gatilho. Use uma ação de conector do Ethereum Blockchain como a próxima etapa após um gatilho, como um gatilho de solicitação HTTP para um microsserviço.

1. No Designer de Aplicativos Lógicos, selecione **Nova etapa** após um gatilho.
1. Selecione o conector do Ethereum Blockchain.
1. Na guia **Ações**, selecione uma das ações disponíveis.

    ![Designer de Aplicativos Lógicos com propriedades de Ações](./media/ethereum-logic-app/action-properties.png)

1. Altere ou [crie uma conexão de API](#create-an-api-connection) para o Azure Blockchain Service.
1. Dependendo da ação escolhida, forneça os detalhes a seguir sobre a função de contrato inteligente.

    | Propriedade | Descrição |
    |----------|-------------|
    | **ABI do Contrato** | O ABI do contrato define as interfaces do contrato inteligente. Para obter mais informações, confira [Obter o ABI do contrato](#get-the-contract-abi). |
    | **Código de bytes do contrato** | O código de bytes do contrato inteligente compilado. Para obter mais informações, confira [Obter o código de bytes do contrato](#get-the-contract-bytecode). |
    | **Endereço do contrato inteligente** | O endereço do contrato é o endereço de destino do contrato inteligente no Ethereum Blockchain. Para obter mais informações, confira [Obter o endereço do contrato](#get-the-contract-address). |
    | **Nome da função de contrato inteligente** | Selecione o nome da função de contrato inteligente para a ação. A lista é populada com base nos detalhes do ABI do contrato. |

    Depois de selecionar um nome de função de contrato inteligente, você poderá ver campos obrigatórios para parâmetros de função. Insira os valores ou o conteúdo dinâmico necessário para o seu cenário.

Agora você pode usar seu aplicativo lógico. Quando o evento do aplicativo lógico é disparado, a ação do Ethereum Blockchain é executada. Por exemplo, um gatilho de solicitação HTTP executa uma ação do Ethereum Blockchain para consultar um valor de estado do contrato inteligente. Essa consulta resulta em uma resposta HTTP que retorna o valor.

## <a name="generate-a-workflow"></a>Gerar um fluxo de trabalho

A extensão do Visual Studio Code para o Azure Blockchain Development Kit para Ethereum pode gerar fluxos de trabalho de aplicativo lógico para cenários comuns. Quatro cenários estão disponíveis:

* Publicação de dados em uma instância do Banco de Dados SQL do Azure
* Publicação de eventos em uma instância da Grade de Eventos do Azure ou do Barramento de Serviço do Azure
* Publicação de relatório
* Microsserviço baseado em REST

 O Azure Blockchain Development Kit usa o Truffle para simplificar o desenvolvimento do blockchain. Para gerar um aplicativo lógico com base em um contrato inteligente, você precisará de uma solução do Truffle para o contrato inteligente. Você também precisará de uma conexão com a rede de consórcio do Azure Blockchain Service. Para obter mais informações, consulte [usar Visual Studio Code para se conectar a um início rápido de rede do Azure Blockchain Service Consortium](connect-vscode.md).

Por exemplo, as seguintes etapas geram um aplicativo lógico do microsserviço baseado em REST, com base no contrato inteligente **HelloBlockchain** do início rápido:

1. Na barra lateral do gerenciador do Visual Studio Code, expanda a pasta **contracts** na solução.
1. Clique com o botão direito do mouse em **HelloBlockchain.sol** e selecione **Gerar Microsserviços para Contratos Inteligentes** no menu.

    ![Painel do Visual Studio Code com a seleção de Gerar Microsserviços para Contratos Inteligentes](./media/ethereum-logic-app/generate-logic-app.png)

1. Na paleta de comandos, selecione **Aplicativo Lógico**.
1. Insira o **endereço do contrato**. Para obter mais informações, confira [Obter o endereço do contrato](#get-the-contract-address).
1. Selecione a assinatura do Azure e o grupo de recursos para o aplicativo lógico.

    Os arquivos de código e de configuração de aplicativos lógicos são gerados no diretório **generatedLogicApp**.

1. Exiba o diretório **generatedLogicApp/HelloBlockchain**. Há um arquivo JSON do aplicativo lógico para cada função, evento e propriedade de contrato inteligente.
1. Abra o arquivo **generatedLogicApp/HelloBlockchain/Service/property.RequestMessage.logicapp.json** e copie o conteúdo.

    ![Arquivo JSON com o código a ser copiado](./media/ethereum-logic-app/requestmessage.png)

1. No aplicativo lógico, selecione **Exibição de código do aplicativo lógico**. Substitua o JSON existente pelo JSON do aplicativo lógico gerado.

    ![Exibição de código do aplicativo lógico com o novo código do aplicativo substituído](./media/ethereum-logic-app/code-view.png)

1. Selecione **Designer** para alternar para a exibição de designer.
1. O aplicativo lógico inclui as etapas básicas para o cenário. No entanto, você precisa atualizar os detalhes de configuração do conector do Ethereum Blockchain.
1. Selecione a etapa **Conexões** e altere ou [crie uma conexão de API](#create-an-api-connection) para o Azure Blockchain Service.

    ![Exibição do Designer com a seleção de Conexões](./media/ethereum-logic-app/microservice-logic-app.png)

1. Agora você pode usar seu aplicativo lógico. Para testar o microsserviço baseado em REST, emita uma solicitação HTTP POST para a URL de solicitação do aplicativo lógico. Copie o conteúdo da **URL http post** da etapa **quando uma solicitação HTTP é recebida** .

    ![Painel do Designer de Aplicativos Lógicos com a URL HTTP POST](./media/ethereum-logic-app/post-url.png)

1. Use o cURL para criar uma solicitação HTTP POST. Substitua o texto do espaço reservado *\<HTTP POST URL\>* pela URL da etapa anterior.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    O comando do cURL retorna uma resposta do aplicativo lógico. Nesse caso, a resposta é a saída da função de contrato inteligente **RequestMessage**.

    ![Saída de código da função de contrato inteligente RequestMessage](./media/ethereum-logic-app/curl.png)

Para obter mais informações sobre como usar o kit de desenvolvimento, confira a [página wiki do Azure Blockchain Development Kit para Ethereum](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>Criar uma conexão de API

Uma conexão de API com um blockchain é necessária para o conector do Ethereum Blockchain. Você pode usar o conector de API para vários aplicativos lógicos. Algumas propriedades são necessárias e outras dependem do cenário.

> [!IMPORTANT]
> Uma chave privada ou um endereço de conta e uma senha são necessários para a criação de transações em um blockchain. Apenas uma forma de autenticação é necessária. Você não precisa fornecer os detalhes da chave privada e da conta. A consulta de contratos não exige uma transação. Se você estiver usando ações que consultam o estado do contrato, a chave privada ou o endereço da conta e a senha não serão necessários.

Para ajudar você a configurar uma conexão com um membro do Azure Blockchain Service, a lista a seguir traz as propriedades que podem ser necessárias, dependendo do cenário.

| Propriedade | Descrição |
|----------|-------------|
|**Nome da conexão** | Nome da conexão de API. Obrigatórios. |
|**Ponto de extremidade RPC do Ethereum** | Endereço HTTP do nó de transação do Azure Blockchain Service. Obrigatórios. Para obter mais informações, confira [Obter o ponto de extremidade RPC](#get-the-rpc-endpoint). |
|**Chave privada** | Chave privada da conta do Ethereum. A chave privada ou o endereço da conta e a senha são necessários para as transações. Para obter mais informações, confira [Obter a chave privada](#get-the-private-key). |
|**Endereço da conta** | Endereço da conta do membro do Azure Blockchain Service. A chave privada ou o endereço da conta e a senha são necessários para as transações. Para obter mais informações, confira [Obter o endereço da conta](#get-the-account-address). |
|**Senha da conta** | A senha da conta é definida quando você cria o membro. Para obter informações sobre como redefinir a senha, confira [Conta do Ethereum](consortium.md#ethereum-account).|

## <a name="get-the-rpc-endpoint"></a>Obter o ponto de extremidade RPC

O endereço do ponto de extremidade RPC do Azure Blockchain Service é necessário para se conectar a uma rede de blockchain. Obtenha o endereço do ponto de extremidade usando o Azure Blockchain Development Kit para Ethereum ou o portal do Azure.

**Para usar o kit de desenvolvimento:**

1. Em **Azure Blockchain Service** no Visual Studio Code, clique com o botão direito do mouse no consórcio.
1. Selecione **Copiar Endereço do Ponto de Extremidade RPC**.

    ![Painel do Visual Studio Code mostrando o consórcio com a seleção de Copiar Endereço do Ponto de Extremidade RPC](./media/ethereum-logic-app/devkit-rpc.png)

    O ponto de extremidade RPC é copiado para a área de transferência.

**Para usar o portal do Azure:**

1. Entre no [portal do Azure](https://portal.azure.com).
1. Acesse o membro do Azure Blockchain Service. Selecione **Nós de transação** e o link do nó de transação padrão.

    ![Página Nós de transação com a seleção (nó padrão)](./media/ethereum-logic-app/transaction-nodes.png)

1. Selecione chaves de acesso de **cadeias de conexão**  >  .
1. Copie o endereço do ponto de extremidade de **HTTPS (Chave de acesso 1)** ou **HTTPS (Chave de acesso 2)**.

    ![Portal do Azure com as chaves de acesso da cadeia de conexão](./media/ethereum-logic-app/connection-string.png)

    O ponto de extremidade RPC é a URL HTTPS, que inclui o endereço e a chave de acesso do nó de transação do membro do Azure Blockchain Service.

## <a name="get-the-private-key"></a>Obter a chave privada

Use a chave privada da conta do Ethereum para autenticação ao enviar uma transação para o blockchain. As chaves pública e privada da conta do Ethereum são geradas com base em um mnemônico de 12 palavras. O Azure Blockchain Development Kit para Ethereum gera um mnemônico quando você se conecta a um membro do consórcio do Azure Blockchain Service. Obtenha o endereço do ponto de extremidade usando a extensão do kit de desenvolvimento.

1. No Visual Studio Code, abra a paleta de comandos (F1).
1. Selecione **Blockchain: recuperar chave privada**.
1. Selecione o mnemônico que você salvou ao se conectar ao membro do consórcio.

    ![Paleta de comandos com uma opção para selecionar o mnemônico](./media/ethereum-logic-app/private-key.png)

    A chave privada é copiada para a área de transferência.

## <a name="get-the-account-address"></a>Obter o endereço da conta

Use a conta e a senha do membro para autenticação ao enviar uma transação para o blockchain. A senha é definida quando você cria o membro.

1. Na portal do Azure, acesse a página Visão geral do Azure Blockchain Service.
1. Copie o endereço da **conta do membro** .

    ![Página Visão Geral com o endereço da conta do membro](./media/ethereum-logic-app/member-account.png)

Para obter mais informações sobre o endereço da conta e a senha, confira [Conta do Ethereum](consortium.md#ethereum-account).

## <a name="get-the-contract-abi"></a>Obter o ABI do contrato

O ABI do contrato define as interfaces do contrato inteligente. Ele descreve como interagir com o contrato inteligente. Obtenha o ABI do contrato usando o Azure Blockchain Development Kit para Ethereum. Obtenha-o também no arquivo de metadados do contrato criado pelo compilador Solidity.

**Para usar o kit de desenvolvimento:**

Se você usou o kit de desenvolvimento ou o Truffle para criar o contrato inteligente, use a extensão para copiar o ABI do contrato para a área de transferência.

1. No painel do Visual Studio Code Explorer, expanda a pasta **build/contracts** do projeto do Solidity.
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Selecione **Copiar ABI do Contrato**.

    ![Painel do Visual Studio Code com a seleção de Copiar ABI do Contrato](./media/ethereum-logic-app/abi-devkit.png)

    O ABI do contrato é copiado para a área de transferência.

**Para usar o arquivo de metadados do contrato:**

1. Abra o arquivo de metadados do contrato contido na pasta **build/contracts** do projeto do Solidity. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Localize a seção **abi** no arquivo JSON.
1. Copie a matriz JSON de **abi**.

    ![Código do ABI no arquivo de metadados do contrato](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>Obter o código de bytes do contrato

O código de bytes do contrato é o contrato inteligente compilado executado pela máquina virtual do Ethereum. Obtenha o código de bytes do contrato usando o Azure Blockchain Development Kit para Ethereum. Obtenha-o também por meio do compilador Solidity.

**Para usar o kit de desenvolvimento:**

Se você usou o kit de desenvolvimento ou o Truffle para criar o contrato inteligente, use a extensão para copiar o código de bytes do contrato para a área de transferência.

1. No painel do Visual Studio Code Explorer, expanda a pasta **build/contracts** do projeto do Solidity.
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Selecione **Copiar Código de Bytes do Contrato**.

    ![Painel do Visual Studio Code com a seleção de Copiar Código de Bytes do Contrato](./media/ethereum-logic-app/bytecode-devkit.png)

    O código de bytes do contrato é copiado para a área de transferência.

**Para usar o arquivo de metadados do contrato:**

1. Abra o arquivo de metadados do contrato contido na pasta **build/contracts** do projeto do Solidity. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Localize o elemento **bytecode** no arquivo JSON.
1. Copie o valor de **bytecode**.

    ![Painel do Visual Studio Code com o código de bytes nos metadados](./media/ethereum-logic-app/bytecode-metadata.png)

**Para usar o compilador Solidity:**

Use o comando `solc --bin <smart contract>.sol` para gerar o código de bytes do contrato.

## <a name="get-the-contract-address"></a>Obter o endereço do contrato

O endereço do contrato é o endereço de destino do contrato inteligente no Ethereum Blockchain. Use esse endereço para enviar uma transação ou um estado de consulta de um contrato inteligente. Obtenha o endereço do contrato na saída de migração do Truffle ou no arquivo de metadados do contrato.

**Para usar a saída de migração do Truffle:**

O Truffle exibe o endereço do contrato após a implantação do contrato inteligente. Copie o **endereço do contrato** da saída.

![Saída de migração do Truffle com o endereço do contrato no Visual Studio Code](./media/ethereum-logic-app/contract-address-truffle.png)

**Para usar o arquivo de metadados do contrato:**

1. Abra o arquivo de metadados do contrato contido na pasta **build/contracts** do projeto do Solidity. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Localize a seção **networks** no arquivo JSON.
1. As redes privadas são identificadas por uma ID de rede de inteiro. Localize o valor do endereço na seção da rede.
1. Copie o valor de **address**.

![Metadados com o valor do endereço no Visual Studio Code](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Próximas etapas

Assista a cenários comuns no vídeo [Como fazer mais com os Aplicativos Lógicos](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
