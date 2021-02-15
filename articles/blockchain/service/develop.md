---
title: Visão geral do desenvolvimento do serviço Blockchain do Azure
description: Introdução ao desenvolvimento de soluções no serviço Blockchain do Azure.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80348374"
---
# <a name="azure-blockchain-service-development-overview"></a>Visão geral do desenvolvimento do serviço Blockchain do Azure

Com o serviço Blockchain do Azure, você pode criar redes Blockchain do consórcio para habilitar cenários empresariais como acompanhamento de ativos, token digital, fidelidade e recompensa, a cadeia de fornecedores financeiros e a comprovada. As seções a seguir introduzem o desenvolvimento do serviço Blockchain do Azure para implementar soluções do Enterprise Blockchain.

## <a name="connecting-to-azure-blockchain-service"></a>Conectando-se ao serviço Blockchain do Azure

Há diferentes tipos de clientes para redes blockchain, incluindo nós completos, nós leves e clientes remotos. O serviço Blockchain do Azure cria uma rede Blockchain que inclui nós. Você pode usar clientes diferentes como seu gateway para o serviço Blockchain do Azure para o desenvolvimento do Blockchain. O serviço Blockchain do Azure oferece autenticação básica ou chave de acesso como um ponto de extremidade de desenvolvimento. Estes são clientes populares que você pode usar conectar.

### <a name="visual-studio-code"></a>Visual Studio Code

Você pode se conectar a membros do Consortium usando a extensão de Visual Studio Code do kit de desenvolvimento do Azure Blockchain. Uma vez conectado a um consórcio, você pode criar, compilar e implantar contratos inteligentes em um membro do consórcio do Azure Blockchain Service.

Para desenvolver soluções de blockchain empresarial sofisticadas, uma estrutura de desenvolvimento é necessária para se conectar a diferentes redes blockchain e gerenciar ciclos de vida de contrato inteligente. A maioria dos projetos interage com pelo menos dois nós blockchain. Os desenvolvedores usam um blockchain local durante o desenvolvimento. Quando o aplicativo estiver pronto para teste ou versão, o desenvolvedor será implantado em uma rede blockchain. Por exemplo, a rede pública Ethereum principal ou o serviço Blockchain do Azure. O kit de desenvolvimento do Azure Blockchain para extensão Ethereum no Visual Studio Code usa Truffle. Truffle é uma estrutura de desenvolvimento de blockchain popular para escrever, compilar, implantar e testar aplicativos descentralizados no Ethereum blockchains. Você também pode considerar o Truffle como uma estrutura que tenta integrar perfeitamente o desenvolvimento de um Smart Contract e o desenvolvimento para a Web tradicional.

Para obter mais informações, consulte [início rápido: usar Visual Studio Code para se conectar a uma rede do Azure Blockchain Service Consortium](connect-vscode.md).

### <a name="metamask"></a>Metamáscara

A metamáscara é uma carteira baseada em navegador (cliente remoto), cliente RPC e Gerenciador de contratos básico. Ao contrário de outras carteiras de navegador, a metamáscara injeta uma instância de Web3 no contexto de JavaScript do navegador, agindo como um cliente RPC que se conecta a uma variedade de Ethereum blockchains (*mainnet*, *Ropsten TESTNET*, *Kovan TESTNET*, nó RPC local, etc.). Você pode configurar o RPC personalizado facilmente para se conectar ao serviço Blockchain do Azure e iniciar o desenvolvimento de Blockchain usando o remix.

Para obter mais informações, consulte [início rápido: usar metamáscara para conectar e implantar um contrato inteligente](connect-metamask.md)

### <a name="geth"></a>Geth

Geth é a interface de linha de comando para executar um nó Ethereum completo implementado em go. Você não precisa executar o nó completo, mas pode iniciar seu console interativo que fornece um ambiente de tempo de execução JavaScript expondo uma API JavaScript para interagir com o serviço Blockchain do Azure.

Para obter mais informações, consulte [início rápido: usar Geth para anexar a um nó de transação do serviço Blockchain do Azure](connect-geth.md).

## <a name="ethereum-quorum-private-transactions"></a>Transações privadas de quorum Ethereum

O quorum é um protocolo contábil distribuído baseado em Ethereum com transação mais privacidade de contrato e novos mecanismos de consenso. Os principais aprimoramentos em Go-Ethereum incluem:

* **Privacidade** – o quorum dá suporte a transações privadas e a contratos privados por meio da separação de estado pública e privada e utiliza trocas de mensagens criptografadas ponto a ponto para transferência direcionada de dados privados para os participantes da rede.
* **Mecanismos de consenso alternativos** – o consenso de prova de trabalho ou de prova de jogo não é necessário para uma rede com permissão. O quorum oferece vários mecanismos de consenso que são projetados para cadeias de consórcio, como reposicionamento e IBFT.O serviço Blockchain do Azure usa o mecanismo de consenso IBFT.
* A permissão de **pares** e a troca de pontos por pares usando contratos inteligentes garante que apenas partes conhecidas possam ingressar na rede.
* **Melhor desempenho** -o quorum oferece maior desempenho do que o Geth público.

## <a name="block-explorers"></a>Bloquear Explorers

Os gerenciadores de blocos são navegadores blockchain online que exibem conteúdo de bloco individual, dados de endereço de transação e histórico. As informações básicas de bloco estão disponíveis por meio de Azure Monitor no serviço Blockchain do Azure. No entanto, se você precisar de informações mais detalhadas durante o desenvolvimento, os gerenciadores de bloqueio podem ser úteis.  Os gerenciadores de blocos a seguir funcionam com o serviço Blockchain do Azure:

* [Epirus o Gerenciador de serviços Blockchain do Azure](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) da Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

Você também pode criar seu próprio Gerenciador de blocos usando o Blockchain Gerenciador de Dados e Azure Cosmos DB, consulte [tutorial: usar o Blockchain Gerenciador de dados para enviar dados para Azure Cosmos DB](data-manager-cosmosdb.md).

## <a name="tps-measurement"></a>Medição da TPS

Como blockchain é usado em cenários mais empresariais, a velocidade de transações por segundo (TPS) é importante para evitar gargalos e ineficiências do sistema. Altas taxas de transações podem ser difíceis de serem mantidas em um blockchain descentralizado. Uma medição precisa da TPS pode ser afetada por diferentes fatores, como thread do servidor, tamanho da fila de transações, latência de rede e segurança. Se você precisar medir a velocidade da TPS durante o desenvolvimento, uma ferramenta popular de código-fonte aberto será [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Próximas etapas

Experimente um início rápido usando o kit de desenvolvimento Blockchain do Azure para Ethereum para anexar a um consórcio no serviço Blockchain do Azure.

> [!div class="nextstepaction"]
> [Usar o Visual Studio Code para se conectar ao Azure Blockchain Service](connect-vscode.md)