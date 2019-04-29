---
title: Padrões de integração de contratos inteligentes no Azure Blockchain Workbench
description: Visão geral de padrões de integração de contratos inteligentes no Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 11e0e1436e3f640c30fec5e8d6fd9ca10adbd707
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60867574"
---
# <a name="smart-contract-integration-patterns"></a>Padrões de integração e contratos inteligentes

Os contratos inteligentes geralmente representam um fluxo de trabalho empresarial que precisa ser integrado com dispositivos e sistemas externos.

Os requisitos desses fluxos de trabalho incluem a necessidade de iniciar transações em uma razão distribuída que incluem dados de um dispositivo, serviço ou sistema externo. Também precisam que os sistemas externos reajam a eventos originados de contratos inteligentes em uma razão distribuída.

A integração de API REST e sistema de mensagens envia transações de sistemas externos para contratos inteligentes incluídos em um aplicativo do Azure Blockchain Workbench. Ela também envia notificações de eventos para sistemas externos, com base nas alterações que ocorrem dentro de um aplicativo.

Para cenários de integração de dados, o Azure Blockchain Workbench inclui um conjunto de exibições de banco de dados que a mesclam uma combinação de dados transacionais do blockchain e metadados sobre os contratos inteligentes e aplicativos.

Além disso, alguns cenários, como aquelas relacionadas ao fornecer a cadeia ou a mídia, também podem exigir a integração de documentos. Embora o Azure Blockchain Workbench não forneça chamadas à API para manipular documentos diretamente, documentos podem ser incorporados em um aplicativo blockchain. Esta seção também inclui esse padrão.

Esta seção inclui os padrões identificados para implementar cada um desses tipos de integrações em suas soluções integrais.

## <a name="rest-api-based-integration"></a>Integração com base em API REST

Os recursos no aplicativo web gerado do Azure Blockchain Workbench são expostos por meio da API REST. Os recursos incluem carregamento, configuração e administração de aplicativos do Azure Blockchain Workbench, envio de transações a um razão distribuído e a consulta de metadados de aplicativo e dados de razão.

A API REST é usada principalmente para clientes interativos, como web, móvel e aplicativos de bot.

Esta seção examina os padrões focados nos aspectos da API REST que envia transações em um Razão distribuído e padrões que consultam dados sobre transações no Banco de Dados SQL *off chain* do Azure Blockchain Workbench.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Enviar transações a uma razão distribuída de um sistema externo

A API REST do Azure Blockchain Workbench envia solicitações autenticadas para executar transações em um Razão distribuído.

![Enviar transações a uma razão distribuída](./media/integration-patterns/send-transactions-ledger.png)

A execução de transações ocorre usando o processo descrito anteriormente, em que:

-   O aplicativo externo autentica para o Azure Active Directory provisionado como parte da implantação do Azure Blockchain Workbench.
-   Os usuários autorizados recebem um token de portador que pode ser enviado com solicitações para a API.
-   Aplicativos externos fazem chamadas para a API REST usando o token de portador.
-   A API REST empacota a solicitação como uma mensagem e a envia para o barramento de serviço. Dele, a solicitação será recuperada, assinada e enviada ao Razão distribuído apropriada.
-   A API REST faz uma solicitação para o banco de dados SQL do Azure Blockchain Workbench para registrar a solicitação e estabelecer o status atual de provisionamento.
-   O banco de dados SQL retorna o status de provisionamento e a chamada à API retorna a ID Para o aplicativo externo que a chamou.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Consultando metadados do Blockchain Workbench e transações de razão distribuída

A API REST do Azure Blockchain Workbench envia solicitações autenticadas para consultar detalhes relacionados à execução do contrato inteligente em um Razão distribuído.

![Consultando metadados](./media/integration-patterns/querying-metadata.png)

A consulta ocorre usando o processo descrito anteriormente, em que:

1. O aplicativo externo autentica para o Azure Active Directory provisionado como parte da implantação do Azure Blockchain Workbench.
2. Os usuários autorizados recebem um token de portador que pode ser enviado com solicitações para a API.
3. Aplicativos externos fazem chamadas para a API REST usando o token de portador.
4. A API REST consulta os dados para a solicitação do banco de dados SQL e o retorna ao cliente.

## <a name="messaging-integration"></a>Integração do sistema de mensagens

A integração do sistema de mensagens facilita a interação com sistemas, serviços e dispositivos em que um logon interativo não é possível ou desejável. A integração do sistema de mensagens se concentra em dois tipos de mensagens, aquelas que solicitam que as transações sejam executadas em um Razão distribuído e eventos que são expostos por essa razão, quando as transações foram executadas.

A integração de mensagens se concentra na execução e monitoramento de transações relacionadas à criação de usuário, a criação do contrato e a execução de transações em contratos e é usado principalmente por sistemas de back-end *sem periféricos*.

Esta seção analisa padrões focados nos aspectos da API baseada em mensagens que envia transações em um Razão distribuído e padrões que representam as mensagens de eventos expostas pelo Razão distribuído subjacente.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Entrega de eventos unidirecional de um contrato inteligente para um consumidor de evento 

Nesse cenário, um evento ocorre dentro de um contrato inteligente, por exemplo, uma alteração de estado ou a execução de um tipo específico de transação. Esse evento é transmitido por meio de uma Grade de Eventos para consumidores de downstream e os consumidores, em seguida, executam as ações apropriadas.

Um exemplo deste cenário é que quando a transação ocorre, um consumidor pode ser alertado e pode executar ações, tais como registrar a informação em um banco de dados SQL ou no Common Data Service. Esse cenário é o mesmo padrão que o Workbench segue para preencher o BD SQL *off chain*.

Outra seria se um contrato inteligente faz a transição para um estado específico, por exemplo, quando um contrato entra em um *OutOfCompliance*. Quando essa alteração de estado ocorre, ele pode disparar um alerta que será enviado para o celular do administrador.

![Entrega de eventos unidirecional](./media/integration-patterns/one-way-event-delivery.png)

Esse cenário ocorre usando o processo descrito anteriormente, em que:

-   O contrato inteligente faz a transição para um novo estado e envia um evento na razão.
-   A razão recebe e fornece o evento para o Azure Blockchain Workbench.
-   Azure Workbench de Blockchain assina eventos da razão e recebe o evento.
-   Azure Workbench de Blockchain publica o evento para os assinantes na Grade de Eventos.
-   Os sistemas externos assinam a Grade de Eventos, consumem a mensagem e executem as ações apropriadas.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Entrega de eventos unidirecional de uma mensagem de um sistema externo a um contrato inteligente

Também há um cenário que flui da direção oposta. Nesse caso, um evento é gerado por um sensor ou um sistema externo e os dados de evento devem ser enviados a um contrato inteligente.

Um exemplo comum é a entrega de dados de mercados financeiros, por exemplo, os preços de mercadorias, ações ou títulos, a um contrato inteligente.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Entrega direta de um Azure Blockchain Workbench no formato esperado

Alguns aplicativos são criados para integrar com o Azure Blockchain Workbench e geram diretamente e enviam mensagens nos formatos esperados.

![Entrega direta](./media/integration-patterns/direct-delivery.png)

Essa entrega ocorre usando o processo descrito anteriormente, em que:

-   Um evento ocorre em um sistema externo que dispara a criação de uma mensagem para o Azure Blockchain Workbench.
-   O sistema externo tem código escrito para criar esta mensagem em um formato conhecido e a envia diretamente para o Barramento de Serviço.
-   O Azure Blockchain Workbench assina eventos do Barramento de Serviço e recebe a mensagem.
-   O Azure Blockchain Workbench inicia uma chamada à razão, enviando os dados do sistema externo para um contrato específico.
-   Após o recebimento da mensagem, o contrato faz a transição para um novo estado.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Entrega de uma mensagem em um formato desconhecido para o Azure Blockchain Workbench

Alguns sistemas não podem ser modificados para entregar mensagens nos formatos padrão usados pelo Azure Blockchain Workbench. Nesses casos, os mecanismos existentes e formatos de mensagens desses sistemas geralmente podem ser usados. Especificamente, os tipos de mensagem nativo desses sistemas podem ser transformados usando Aplicativos Lógicos, Azure Functions ou outro código personalizado para mapear para um dos formatos esperados de mensagens padrão.

![Formato de mensagem desconhecido](./media/integration-patterns/unknown-message-format.png)

Isso ocorre usando o processo descrito anteriormente, em que:

-   Um evento ocorre em um sistema externo que dispara a criação de uma mensagem.
-   Um Aplicativo Lógico ou código personalizado é usado para receber essa mensagem e transformá-la em uma mensagem de formato padrão do Azure Blockchain Workbench.
-   O Aplicativo Lógico envia a mensagem transformada diretamente para o Barramento de Serviço.
-   O Azure Blockchain Workbench assina eventos do Barramento de Serviço e recebe a mensagem.
-   O Azure Blockchain Workbench inicia uma chamada à razão, enviando os dados do sistema externo para uma função específica no contrato.
-   A função executa e normalmente modifica o estado. A alteração do estado avança o fluxo de trabalho de negócios refletido no contrato inteligente, permitindo que outras funções agora sejam executadas conforme apropriado.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Controle de transações para processo externo e aguardar a conclusão

Há cenários em que um contrato inteligente deve interromper a execução interna e liberar para um processo externo. Esse processo externo concluiria, enviaria uma mensagem ao contrato inteligente, e a execução continuaria dentro do contrato inteligente.

#### <a name="transition-to-the-external-process"></a>Transição para o processo externo

Esse padrão geralmente é implementado usando a abordagem a seguir:

-   As transições de contrato inteligente para um estado específico. Nesse estado, nenhum ou um número limitado de funções pode ser executado até que um sistema externo execute uma ação desejada.
-   A alteração de estado é exposta como um evento com um consumidor de downstream.
-   O consumidor de downstream recebe o evento e dispara a execução de código externo.

![Controle de transição para o processo externo](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Retorno de controle do contrato inteligente

Dependendo da capacidade de personalizar o sistema externo pode ou não ser capaz de fornecer mensagens em um dos formatos padrão que o Azure Blockchain Workbench espera. Com base na capacidade de sistemas externos gerar uma dessas mensagens determina qual dos seguintes dois caminhos de retorno é seguido.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Entrega direta de um Azure Blockchain Workbench no formato esperado

![](./media/integration-patterns/direct-delivery.png)

Neste modelo, a comunicação com o contrato e alteração de estado subsequente ocorre seguindo o processo acima, em que:

-   Ao atingir a conclusão ou uma etapa específica na execução de código externo, um evento é enviado para o Barramento de Serviço conectado ao Azure Blockchain Workbench.

-   Para sistemas que não podem ser adaptados diretamente para gravar uma mensagem que esteja de acordo com as expectativas da API, ela é transformada.

-   O conteúdo da mensagem é empacotado e enviado para uma função específica no contrato inteligente. Essa entrega é feita em nome do usuário associado com o sistema externo.

-   A função executa e normalmente modifica o estado. A alteração do estado avança o fluxo de trabalho de negócios refletido no contrato inteligente, permitindo que outras funções agora sejam executadas conforme apropriado.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Entrega de uma mensagem em um formato desconhecido para o Azure Blockchain Workbench

![Formato de mensagem desconhecido](./media/integration-patterns/unknown-message-format.png)

Neste modelo, quando uma mensagem em um formato padrão não pode ser enviada diretamente, a comunicação com o contrato e alteração de estado subsequentes ocorre após o processo anterior, em que:

1.  Ao atingir a conclusão ou uma etapa específica na execução de código externo, um evento é enviado para o Barramento de Serviço conectado ao Azure Blockchain Workbench.
2.  Um Aplicativo Lógico ou código personalizado é usado para receber essa mensagem e transformá-la em uma mensagem de formato padrão do Azure Blockchain Workbench.
3.  O Aplicativo Lógico envia a mensagem transformada diretamente para o Barramento de Serviço.
4.  O Azure Blockchain Workbench assina eventos do Barramento de Serviço e recebe a mensagem.
5.  O Azure Blockchain Workbench inicia uma chamada à razão, enviando os dados do sistema externo para um contrato específico.
6. O conteúdo da mensagem é empacotado e enviado para uma função específica no contrato inteligente. Essa entrega é feita em nome do usuário associado com o sistema externo.
7.  A função executa e normalmente modifica o estado. A alteração do estado avança o fluxo de trabalho de negócios refletido no contrato inteligente, permitindo que outras funções agora sejam executadas conforme apropriado.

## <a name="iot-integration"></a>Integração IoT

Um cenário de integração comum é a inclusão de dados de telemetria recuperados de sensores em um contrato inteligente. Com base nos dados fornecidos por sensores, contratos inteligentes podem executar ações informadas e alterar o estado do contrato.

Por exemplo, se a temperatura de um caminhão de entrega de medicamentos dispara a 110 graus, pode afetar a eficiência do medicamento e pode causar um problema de segurança pública se não for detectado e removida da cadeia de fornecimento. Se um motorista alcança 160 km/h, as informações de sensor resultante podem disparar um cancelamento de seguro por sua companhia de seguro. Se um carro for um carro alugado, dados de GPS poderão indicar quando o motorista saiu da área coberta pelo seu contrato de aluguel e cobrar uma multa.

O desafio é que esses sensores podem fornecer dados de forma constante e não é apropriado enviar todos esses dados para um contrato inteligente. É uma abordagem comum limitar o número de mensagens enviadas para o blockchain enquanto fornece todas as mensagens para um armazenamento secundário. Por exemplo, entregar mensagens recebidas em apenas intervalo fixo, por exemplo, uma vez por hora, e quando um valor contido fica fora de um intervalo estabelecido para um contrato inteligente. Verificar valores que estão fora de tolerância garante que os dados relevantes para a lógica de negócios contratos sejam recebidos e executados. Verificar o valor no intervalo de confirma que o sensor ainda está se comunicando. Todos os dados são enviados para um armazenamento secundário de relatórios para habilitar o relatório, análise e aprendizado de máquina mais amplo. Por exemplo, embora obter as leituras do sensor de GPS pode não seja necessária a cada minuto para um contrato inteligente, eles podem fornecer dados interessantes a serem usados em relatórios ou no mapeamento de rotas.

Na plataforma do Azure, a integração com dispositivos normalmente é feita com o Hub IoT. O Hub IoT fornece roteamento de mensagens com base no conteúdo e possibilita o tipo de funcionalidade descrita anteriormente.

![Mensagens de IoT](./media/integration-patterns/iot.png)

O processo representa um padrão:

-   Um dispositivo se comunica diretamente ou por meio de um gateway de campo para o Hub IoT.
-   O Hub IoT recebe as mensagens e avalia as mensagens em rotas estabelecidas que verificam o conteúdo da mensagem, por exemplo. *O sensor relata uma temperatura maior que 50 graus?*
-   O Hub IoT envia mensagens que atendem aos critérios para um Barramento de Serviço definido para a rota.
-   Um Aplicativo Lógico ou outro código escuta o Barramento de Serviço que Hub IoT estabeleceu para a rota.
-   O Aplicativo Lógico ou outro código recupera e transforma a mensagem para um formato conhecido.
-   A mensagem transformada, agora em um formato padrão, é enviada para o Barramento de Serviço para o Azure Blockchain Workbench.
-   O Azure Blockchain Workbench assina eventos do Barramento de Serviço e recebe a mensagem.
-   O Azure Blockchain Workbench inicia uma chamada à razão, enviando os dados do sistema externo para um contrato específico.
-   Após o recebimento da mensagem, o contrato avalia os dados e pode alterar o estado de acordo com o resultado dessa avaliação, por exemplo, de uma alta temperatura, altere o estado para *Out of Compliance*.

## <a name="data-integration"></a>Integração de dados

Além de REST e a API baseada em mensagem, o Azure Blockchain Workbench também fornece acesso a um banco de dados SQL preenchido com o aplicativo e um contrato de metadados, bem como dados transacionais de razões distribuídas.

![Integração de dados](./media/integration-patterns/data-integration.png)

A integração de dados é bem conhecida:

-   O Azure Blockchain Workbench armazena metadados sobre aplicativos, fluxos de trabalho, contratos e transações como parte do comportamento de operação normal.
-   Os sistemas externos ou ferramentas fornecem uma ou mais caixas de diálogo para facilitar a coleta de informações sobre o banco de dados, como nome do servidor de banco de dados, o nome do banco de dados, o tipo de autenticação, as credenciais de logon e exibições de banco de dados a serem utilizadas.
-   As consultas são gravadas em exibições de banco de dados SQL para facilitar o consumo de downstream por sistemas externos, serviços, relatórios, ferramentas de desenvolvedor e ferramentas de produtividade da empresa.

## <a name="storage-integration"></a>Integração de armazenamento

Muitos cenários podem exigir a necessidade de incorporar arquivos que podem ser comprovados. Por vários motivos, é inadequado colocar arquivos em um blockchain. Em vez disso, uma abordagem comum é executar um hash criptográfico (por exemplo, SHA-256) em um arquivo e compartilhar esse hash em um razão distribuído. Executar o hash novamente a qualquer momento futuro deve retornar o mesmo resultado. Se o arquivo é modificado, mesmo que apenas um pixel seja modificado em uma imagem, o hash retorna um valor diferente.

![Integração de armazenamento](./media/integration-patterns/storage-integration.png)

O padrão pode ser implementado onde:

-   Um sistema externo mantém um arquivo em um mecanismo de armazenamento, como o Armazenamento do Azure.
-   Um hash é gerado com o arquivo ou o arquivo e metadados associados, como um identificador para o proprietário, a URL onde o arquivo está localizado, etc.
-   O hash e todos os metadados são enviados para uma função em um contrato inteligente, como *FileAdded*
-   No futuro, o arquivo e os metadados podem ser misturados novamente e comparados com os valores armazenados na razão.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Pré-requisitos para implementar os padrões de integração usando o REST e APIs de mensagem

Para facilitar a capacidade de um sistema externo ou dispositivo interagir com o contrato inteligente usando a REST ou a API de mensagem, deve ocorrer o seguinte -

1. No Azure Active Directory para o consórcio, é criada uma conta que representa o sistema externo ou o dispositivo.
2. Um ou mais contratos inteligentes apropriados para o seu aplicativo do Azure Blockchain Workbench têm funções definidas para aceitar os eventos do seu dispositivo ou sistema externo.
3. O arquivo de configuração de aplicativo para seu contrato inteligente contém a função, ao qual o sistema ou o dispositivo é atribuído.
4. O arquivo de configuração de aplicativo para seu contrato inteligente identifica que informa que essa função é chamada pela função definida.
5. O arquivo de configuração do aplicativo e seus contratos inteligentes são carregados no Azure Blockchain Workbench.

Depois que o aplicativo é carregado, a conta do Azure Active Directory para o sistema externo é atribuída ao contrato e à função associada.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testar os fluxos de integração do sistema externo antes de gravar o código de integração de teste 

A integração com sistemas externos é um requisito fundamental de muitos cenários. É desejável ser capaz de validar o design de contrato inteligente anterior ou em paralelo para o desenvolvimento de código para integração com sistemas externos.

O uso do Azure Active Directory (Azure AD) pode acelerar muito a produtividade do desenvolvedor e a hora para o valor. Especificamente, a integração de código com um sistema externo pode levar uma quantidade incomum de tempo. Usando o Azure AD e a geração automática de UX pelo Azure Blockchain Workbench, você pode permitir que os desenvolvedores entrem no Blockchain Workbench como o sistema externo e populem os valores desde o sistema externo por meio de UX. Você pode rapidamente desenvolver e validar ideias em um ambiente de prova de conceito antes que o código de integração seja escrito para os sistemas externos.
