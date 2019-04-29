---
title: Arquitetura do Azure Blockchain Workbench
description: Visão geral da arquitetura do Azure Blockchain Workbench e seus componentes.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 83c5e1405c402a1c6c98f9dbcaaf74891eb75e6d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437746"
---
# <a name="azure-blockchain-workbench-architecture"></a>Arquitetura do Azure Blockchain Workbench

O Azure Blockchain Workbench simplifica o desenvolvimento de aplicativo blockchain, fornecendo uma solução que usa vários componentes do Azure. O Blockchain Workbench pode ser implantado usando um modelo de solução no Azure Marketplace. O modelo permite que você escolha módulos e componentes para implantar, incluindo pilha de blockchain, tipo de aplicativo de cliente e suporte para a integração de IoT. Uma vez implantado, o Blockchain Workbench fornece acesso a um aplicativo web, aplicativo iOS e aplicativo Android.

![Arquitetura do Blockchain Workbench](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identidade e autenticação

Usando o Blockchain Workbench, um consórcio pode federar suas identidades corporativas usando o Azure Active Directory (Azure AD). Workbench gera novas contas de usuário para identidades em cadeia com as identidades de empresa armazenadas no Azure AD. O mapeamento de identidade facilita logon autenticado para aplicativos e APIs de cliente e usa as políticas de autenticação das organizações. O Workbench também fornece a capacidade de associar identidades corporativas a funções específicas dentro de um dado contrato inteligente. Além disso, o Workbench também fornece um mecanismo para identificar as ações que essas funções podem executar e o horário.

Após a implantação do Blockchain Workbench, os usuários interagem com Blockchain Workbench através de aplicativos cliente, API de cliente baseado em REST ou API de mensagens. Em todos os casos, as interações devem ser autenticadas, por meio do Azure Active Directory (Azure AD) ou credenciais específicas do dispositivo.

Os usuários associam suas identidades a um Azure AD do consórcio, enviando um convite por e-mail aos participantes pelo seu endereço de e-mail. Ao fazer logon, esses usuários são autenticados usando o nome, a senha e a políticas. Por exemplo, autenticação de dois fatores da sua organização.

O Azure AD é usado para gerenciar todos os usuários que têm acesso ao Blockchain Workbench. Cada dispositivo conectado a um contrato inteligente também está associado com o Azure AD.

O Azure AD também é usado para atribuir usuários a um grupo de administrador especial. Os usuários associados ao grupo de administradores recebem acesso a direitos e ações no Blockchain Workbench, incluindo implantar contratos e conceder permissões a um usuário para acessar um contrato. Os usuários fora deste grupo não tem acesso a ações do administrador.

## <a name="client-applications"></a>Aplicativos cliente

Workbench fornece aplicativos cliente gerados automaticamente para web e dispositivos móveis (iOS, Android), que podem ser usado para validar, testar e exibir aplicativos blockchain. A interface do aplicativo é gerada dinamicamente com base nos metadados do contrato inteligente e pode acomodar qualquer caso de uso. Os aplicativos cliente fornecem um front-end voltado ao usuário para os aplicativos blockchain completos gerados pelo Blockchain Workbench. Os aplicativos cliente autenticam usuários através do Azure Active Directory (Azure AD) e, em seguida, apresentam uma experiência de usuário personalizada para o contexto de negócios do contrato inteligente. A experiência do usuário permite a criação de novas instâncias de contrato inteligente por indivíduos autorizados e, em seguida, apresenta a capacidade de executar certos tipos de transações em pontos apropriados no processo de negócios que representa o contrato inteligente.

No aplicativo web, os usuários autorizados podem acessar o Console do Administrador. O console está disponível para os usuários do grupo administrador no Azure AD e fornece acesso às seguintes funções:

* Implantar contratos inteligentes fornecidos por Microsoft para cenários comuns. Por exemplo, um cenário de transferência de ativo.
* Carregar e implantar seus próprios contratos inteligentes.
* Atribua um acesso de usuário ao contrato inteligente no contexto de uma função específica.

Para obter mais informações, confira os [aplicativos cliente de exemplo do Azure Blockchain Workbench no GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-development-kit/connect/mobile/blockchain-workbench/workbench-client).

## <a name="gateway-service-api"></a>API do serviço de gateway

Blockchain Workbench inclui uma API de serviço de gateway baseado em REST. Ao gravar um blockchain, a API gera e entrega de mensagens a um agente de evento. Quando os dados são solicitados pela API, as consultas são enviadas ao banco de dados SQL fora da cadeia. O banco de dados SQL contém uma réplica dos dados em cadeia e metadados que fornece informações de contexto e configuração para contratos inteligentes com suporte. As consultas retornam os dados necessários da réplica fora de cadeia em um formato informado pelos metadados para o contrato.

Os desenvolvedores podem acessar a API do serviço de gateway para criar ou integrar soluções blockchain sem depender de aplicativos de cliente Blockchain Workbench.

> [!NOTE]
> Para habilitar o acesso autenticado à API, dois aplicativos de cliente são registrados no Azure Active Directory. O Azure Active Directory requer registros de aplicativo distintos de cada tipo de aplicativo (nativo e web). 

## <a name="message-broker-for-incoming-messages"></a>Agente de mensagem para mensagens de entrada

Os desenvolvedores que desejam enviar mensagens diretamente para o Blockchain Workbench podem enviar mensagens diretamente para o Barramento de Serviço. Por exemplo, a API de mensagens pode ser usada para integração de sistema a sistema ou dispositivos IoT.

## <a name="message-broker-for-downstream-consumers"></a>Agente de mensagens para consumidores de downstream

Durante o ciclo de vida do aplicativo, ocorrem os eventos. Os eventos podem ser acionados pela API de Gateway ou em razão. As notificações de eventos podem iniciar o código de downstream com base no evento.

Blockchain Workbench implanta automaticamente os dois tipos de consumidores de evento. Um consumidor é disparado por eventos de blockchain para popular o repositório SQL fora da cadeia. O outro consumidor é para capturar metadados dos eventos gerados pela API relacionados ao upload e ao armazenamento de documentos.

## <a name="message-consumers"></a>Consumidores de mensagem

 Os consumidores de mensagem levam as mensagens do Barramento de Serviço. O modelo subjacente de eventos para consumidores de mensagem permite para extensões de serviços adicionais e sistemas. Por exemplo, você pode adicionar suporte para preencher CosmosDB ou avaliar mensagens usando o Azure Streaming Analytics. As seções a seguir descrevem os consumidores de mensagem incluídos no Blockchain Workbench.

### <a name="distributed-ledger-consumer"></a>Consumidor de razão distribuída

Mensagens de tecnologia razão distribuída (DLT) contém os metadados para as transações a serem gravados no blockchain. O consumidor recupera as mensagens e envia os dados para um construtor de transação, assinante e roteador.

### <a name="database-consumer"></a>Consumidor de banco de dados

O consumidor de banco de dados usa mensagens do Barramento de Serviço e envia os dados para um banco de dados anexado, como o banco de dados SQL.

### <a name="storage-consumer"></a>Consumidor de armazenamento

O consumidor de armazenamento usa mensagens do Barramento de Serviço e envia os dados para um armazenamento anexado. Por exemplo, armazenar documentos em hash no Armazenamento do Azure.

## <a name="transaction-builder-and-signer"></a>Assinante e construtor de transação

Se uma mensagem no agente de mensagens de entrada precisa ser gravada no blockchain, ela será processada pelo consumidor DLT. O consumidor DLT é um serviço, que recupera a mensagem que contém metadados para executar uma transação desejada e, em seguida, envia as informações para o *construtor de transação e o assinante*. O *construtor de transação e o assinante* constituirá uma transação blockchain com base em dados e o destino blockchain desejado. Depois de criada, a transação é assinada. Chaves privadas são armazenadas no Azure Key Vault.

 O Blockchain Workbench recupera a chave privada apropriada do Key Vault e assina a transação fora do Key Vault. Uma vez conectado, a transação é enviada para razões e roteadores de transação.

## <a name="transaction-routers-and-ledgers"></a>Razões e roteadores de transação

As razões e roteadores de transação levam as transações assinadas e as encaminha ao blockchain apropriado. Atualmente, o Blockchain Workbench suporta Ethereum como seu blockchain de destino.

## <a name="dlt-watcher"></a>Inspetor DLT

Um Inspetor de tecnologia de razão distribuída (DLT) monitora os eventos que ocorrem em cadeias de bloco anexadas a Blockchain Workbench.
Os eventos refletem as informações relevantes para indivíduos e sistemas. Por exemplo, a criação de novas instâncias de contrato, execução de transações e alterações de estado. Os eventos são capturados e enviados para o agente de mensagem de saída, para que possam ser consumidos pelos consumidores de downstream.

Por exemplo, o consumidor SQL monitora os eventos, consome e preenche o banco de dados do SQL com os valores incluídos. A cópia permite que a recriação de uma réplica dos dados em cadeia em um repositório fora de cadeia.

## <a name="azure-sql-database"></a>Banco de Dados SQL do Azure

O banco de dados SQL do Azure anexado ao Blockchain Workbench armazena as definições de contrato, metadados de configuração e uma réplica SQL acessível dos dados armazenados no blockchain. Esses dados poderão ser facilmente consultados, visualizados ou analisados acessando diretamente o banco de dados. Os desenvolvedores e outros usuários podem usar o banco de dados para relatórios, análise ou outras integrações centradas em dados. Por exemplo, os usuários podem visualizar dados de transações usando o Power BI.

Esse armazenamento fora de cadeia fornece a capacidade para empresas para consultar dados no SQL, em vez de em uma razão de blockchain. Além disso, ao padronizar em um esquema padrão que é independente de pilhas de tecnologia blockchain, o armazenamento fora de cadeia permite a reutilização de relatórios e outros artefatos em projetos, cenários e organizações.

## <a name="azure-storage"></a>Armazenamento do Azure

O Armazenamento do Azure é usado para armazenar os contratos e metadados associados com contratos.

Desde ordens de compra e documentos de embarque, a imagens usadas nas notícias e imagens médicas, a vídeo provenientes de um conjunto incluindo câmeras de organismo de polícia e imagens principais de animação, os documentos desempenham uma função em vários cenários centrados em blockchain. Os documentos não são apropriados para inserir diretamente no blockchain.

O Blockchain Workbench oferece suporte a capacidade de adicionar documentos ou outro conteúdo de mídia com a lógica de negócios blockchain. Um hash do documento ou conteúdo de mídia é armazenado no blockchain e o documento real ou conteúdo de mídia é armazenado no Armazenamento do Azure. As informações de transação associadas são entregues ao agente de mensagem de entrada, empacotadas, assinadas e roteadas para o blockchain. Esse processo aciona eventos que são compartilhados por meio do agente de mensagens de saída. O banco de dados SQL consome essas informações e as envia para o banco de dados para consultas posteriores. Os sistemas downstream também podem consumir esses eventos para agir conforme apropriado.

## <a name="monitoring"></a>Monitoramento

O Workbench fornece o log de aplicativo usando o Application Insights e o Azure Monitor. O Application Insights é usado para armazenar todas as informações registradas no Blockchain Workbench e inclui erros, avisos e operações bem-sucedidas. O Application Insights pode ser usado por desenvolvedores para depurar problemas com Blockchain Workbench. 

O Azure Monitor fornece informações sobre a integridade da rede blockchain. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantar o Azure Blockchain Workbench](../../blockchain-workbench/blockchain-workbench-deploy.md)