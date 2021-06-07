---
title: Guia de Protocolo de Conexões Híbridas de Retransmissão do Azure | Microsoft Docs
description: Este artigo descreve as interações do lado do cliente com a retransmissão das Conexões Híbridas para conectar clientes em funções de ouvinte e de remetente.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 36321f88de173a37c9aa6615c4c0f2b29aec9f20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97976955"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Protocolo de Conexões Híbridas de Retransmissão do Azure

A Retransmissão do Azure é um dos principais pilares de funcionalidades da plataforma do Barramento de Serviço do Azure. A nova funcionalidade _Conexões Híbridas_ da Retransmissão é uma evolução segura e de protocolo em aberto com base em HTTP e WebSockets. Ele substitui o antigo recurso de _Serviços BizTalk_ , igualmente nomeado, criado em uma base de protocolo proprietário. A integração do Conexões Híbridas aos Serviços de Aplicativos do Azure continuará a funcionar no estado em que se encontra.

O Conexões Híbridas permite a comunicação bidirecional, de solicitação-resposta e de fluxo binário e o fluxo de datagrama simples entre dois aplicativos em rede. Uma ou ambas as partes podem estar atrás de NATs ou de firewalls.

Este artigo descreve as interações do lado do cliente com a retransmissão das Conexões Híbridas para conectar clientes em funções de ouvinte e de remetente. Ele também descreve como os ouvintes aceitam novas solicitações e conexões.

## <a name="interaction-model"></a>Modelo de interação

A retransmissão das Conexões Híbridas conecta duas partes, fornecendo um ponto de reunião na nuvem do Azure que as partes podem descobrir e ao qual podem se conectar da perspectiva da sua própria rede. O ponto de reunião é chamado de "Conexão Híbrida" nesta e em outras documentações, nas APIs e também no portal do Azure. O ponto de extremidade de serviço de Conexões Híbridas será referido como o "serviço" no restante deste artigo.

O serviço permite retransmitir conexões do soquete da Web e solicitações e respostas HTTP(S).

O modelo de interação depende da nomenclatura estabelecida por muitas outras APIs de rede. Há um ouvinte que indica primeiro a preparação para lidar com as conexões de entrada e, subsequentemente, aceita-as à medida que elas chegam. Do outro lado, um cliente conecta-se ao ouvinte, esperando que essa conexão seja aceita para estabelecer um caminho de comunicação bidirecional. "Conectar", "Escutar" e "Aceitar" são os mesmos termos que você encontrará na maioria das APIs de soquete.

Em qualquer modelo de comunicação retransmitida, uma das partes realiza conexões de saída em direção a um ponto de extremidade de serviço. Isso torna o "ouvinte" também um "cliente" em uso coloquial e também pode causar outras sobrecargas de terminologia. Portanto, a terminologia precisa usada para as Conexões Híbridas é a seguinte:

Os programas em ambos os lados de uma conexão são chamados de "clientes", pois eles são clientes para o serviço. O cliente que aguarda e aceita conexões é o "ouvinte", ou diz-se que faz a "função de ouvinte". O cliente que inicia uma nova conexão em direção a um ouvinte por meio do serviço é chamado de "remetente" ou faz a "função de remetente".

### <a name="listener-interactions"></a>Interações de ouvinte

O ouvinte tem cinco interações com o serviço. Todos os detalhes de transmissão serão descritos mais adiante neste artigo na seção de referência.

As mensagens de escuta, aceitação e solicitação são recebidas do serviço. As operações de renovação e ping são enviadas pelo ouvinte.

#### <a name="listen-message"></a>Mensagem de escuta

Para indicar a preparação para o serviço sinalizando que um ouvinte está pronto para aceitar conexões, ele cria uma conexão WebSocket de saída. O handshake de conexão recebe o mesmo nome de uma Conexão Híbrida configurada no namespace de Retransmissão, além de um token de segurança que confere o direito "Listen" (de escuta) nesse nome.

Quando o WebSocket é aceito pelo serviço, o registro é concluído e o WebSocket estabelecido é mantido ativo como o "canal de controle" para habilitar todas as interações subsequentes. O serviço permite até 25 ouvintes simultâneos para uma conexão híbrida. A cota de AppHooks deve ser determinada.

Para as Conexões Híbridas, se houver dois ou mais ouvintes ativos, as conexões de entrada serão balanceadas entre eles em ordem aleatória, com o máximo de esforço para realizar uma distribuição justa.

#### <a name="accept-message"></a>Aceitar a mensagem

Quando um remetente abre uma nova conexão no serviço, o serviço escolhe e notifica um os ouvintes ativos na Conexão Híbrida. Essa notificação é enviada ao ouvinte sobre o canal de controle aberto como uma mensagem JSON. A mensagem contém a URL do ponto de extremidade do WebSocket ao qual o ouvinte precisa se conectar para aceitar a conexão.

A URL pode e deve ser usada diretamente pelo ouvinte sem nenhum trabalho extra.
As informações codificadas são válidas apenas por um curto período de tempo, basicamente, pelo tempo em que o remetente está disposto a esperar para que a conexão seja estabelecida de ponta a ponta. O máximo a ser considerado é de 30 segundos. A URL pode ser usada apenas para uma tentativa de conexão bem-sucedida. Assim que a conexão do WebSocket com a URL da reunião é estabelecida, todas as atividades adicionais neste WebSocket são retransmitidas de/para o remetente. Esse comportamento ocorre sem qualquer intervenção ou interpretação pelo serviço.

### <a name="request-message"></a>Mensagem de solicitação

Além das conexões do WebSocket, o ouvinte também poderá receber quadros de solicitação HTTP de um remetente, se esse recurso estiver habilitado explicitamente na Conexão Híbrida.

Os ouvintes que se conectam às Conexões Híbridas com o suporte de HTTP PRECISAM manipular o gesto `request`. Um ouvinte que não manipula `request` e, portanto, causa erros de tempo limite repetidos durante a conexão pode ser bloqueado pelo serviço no futuro.

Os metadados de cabeçalho do quadro HTTP são convertidos em JSON para serem manipulados de forma mais simples pela estrutura do ouvinte, também porque as bibliotecas de análise de cabeçalho HTTP são mais raras do que os analisadores JSON. Os metadados HTTP que são relevantes apenas para a relação entre o remetente e o gateway HTTP de retransmissão, incluindo informações de autorização, não são encaminhados. Os corpos de solicitação HTTP são transferidos de forma transparente como quadros de WebSocket binários.

O ouvinte pode responder às solicitações HTTP usando um gesto de resposta equivalente.

O fluxo de solicitação/resposta usa o canal de controle por padrão, mas pode ser "atualizado" para um WebSocket de reunião distinto sempre que necessário. As conexões do WebSocket distinto melhoram a taxa de transferência de cada conversa do cliente, mas sobrecarregam o ouvinte com mais conexões que precisam ser manipuladas, o que talvez não seja desejável para clientes leves.

No canal de controle, os corpos de solicitação e resposta são limitados a no máximo 64 KB de tamanho. Os metadados de cabeçalho HTTP são limitados ao total de 32 KB. Se a solicitação ou a resposta exceder esse limite, o ouvinte PRECISARÁ atualizar para um WebSocket de reunião usando um gesto equivalente à manipulação de [Aceitar](#accept-message).

Para solicitações, o serviço decide se roteará as solicitações pelo canal de controle. Isso inclui, mas pode não estar limitado a casos em que uma solicitação exceda 64 kB (cabeçalhos mais corpo), ou se a solicitação for enviada com a [codificação de transferência "em bloco"](https://tools.ietf.org/html/rfc7230#section-4.1) e o serviço tiver razão para esperar que a solicitação exceda 64 KB ou a leitura da solicitação não seja instantânea. Se o serviço optar por entregar a solicitação por reunião, ele passará apenas o endereço da reunião para o ouvinte.
Em seguida, o ouvinte PRECISARÁ estabelecer o WebSocket de reunião e o serviço entregará imediatamente a solicitação completa, incluindo os corpos sobre o WebSocket de reunião. A resposta também PRECISARÁ usar o WebSocket de reunião.

Para solicitações que chegam por meio do canal de controle, o ouvinte decide se deseja responder pelo canal de controle ou por meio de reunião. O serviço PRECISA incluir um endereço de reunião em cada solicitação roteada pelo canal de controle. Esse endereço somente é válido para a atualização da solicitação atual.

Se o ouvinte optar por atualizar, ele se conectará e fornecerá a resposta imediatamente pelo soquete de reunião estabelecido.

Depois que o WebSocket de reunião for estabelecido, o ouvinte DEVERÁ mantê-lo para manipulação adicional de solicitações e respostas do mesmo cliente. O serviço manterá o WebSocket pelo período em que a conexão do soquete HTTPS com o remetente persistir e roteará todas as próximas solicitações desse remetente pelo WebSocket mantido. Se o ouvinte optar por descartar o WebSocket de reunião de seu lado, o serviço também descartará a conexão com o remetente, mesmo que uma próxima solicitação já esteja em andamento.

#### <a name="renew-operation"></a>Operação de renovação

O token de segurança que deve ser usado para registrar o ouvinte e manter o canal de controle pode expirar enquanto o ouvinte está ativo. A expiração do token não afetará conexões contínuas, mas fará com que o canal de controle seja descartado pelo serviço ou logo após o momento da expiração. A operação "renew" é uma mensagem JSON que o ouvinte pode enviar para substituir o token associado ao canal de controle, para que o canal de controle possa ser mantido por períodos longos.

#### <a name="ping-operation"></a>Operação de ping

Se o canal de controle permanecer ocioso por muito tempo, intermediários no caminho como balanceadores de carga ou NATs poderão remover a conexão TCP. A operação "ping" evita isso por meio do envio de uma pequena quantidade de dados no canal, que lembra a todos na rota de rede que a conexão deve estar ativa e também serve como um teste de atividade para o ouvinte. Se o ping falhar, o canal de controle deverá ser considerado inutilizável e o ouvinte deverá se reconectar.

### <a name="sender-interaction"></a>Interação de remetente

O remetente tem duas interações com o serviço: ele conecta um soquete da Web ou envia solicitações por HTTPS. As solicitações não podem ser enviadas por um soquete da Web proveniente da função de remetente.

#### <a name="connect-operation"></a>Operação de conexão

A operação "connect" abre um WebSocket no serviço, fornecendo o nome da Conexão Híbrida e um token de segurança (opcional, mas necessário por padrão) concedendo a permissão "Send" na cadeia de caracteres de consulta. O serviço interagirá então com o ouvinte no modo descrito anteriormente e fará com que o ouvinte crie uma conexão de encontro que será associada a esse WebSocket. Após o WebSocket ser aceito, todas as outras interações nesse WebSocket serão, portanto, com um ouvinte conectado.

#### <a name="request-operation"></a>Operação de solicitação

Para as Conexões Híbridas para as quais o recurso foi habilitado, o remetente pode enviar solicitações HTTP praticamente irrestritas aos ouvintes.

Com exceção de um token de acesso de retransmissão que é incorporado na cadeia de consulta ou em um cabeçalho HTTP da solicitação, a retransmissão é totalmente transparente para todas as operações de HTTP no endereço de retransmissão e para todos os sufixos do caminho de endereço da retransmissão, deixando o ouvinte totalmente no controle da autorização de ponta a ponta e até mesmo de recursos de extensão de HTTP, como o [CORS](https://www.w3.org/TR/cors/).

A autorização do remetente com o ponto de extremidade de retransmissão é habilitada por padrão, mas é OPCIONAL. O proprietário da Conexão Híbrida pode optar por permitir remetentes anônimos. O serviço interceptará, inspecionará e removerá informações de autorização da seguinte maneira:

1. Se a cadeia de consulta contiver uma expressão `sb-hc-token`, a expressão SEMPRE será removida da cadeia de consulta. Ele será avaliado se a autorização de retransmissão estiver habilitada.
2. Se os cabeçalhos de solicitação contiverem um cabeçalho `ServiceBusAuthorization`, o cabeçalho da expressão SEMPRE será removido da coleção de cabeçalho.
   Ele será avaliado se a autorização de retransmissão estiver habilitada.
3. Somente se a autorização de retransmissão estiver habilitada e se os cabeçalhos de solicitação contiverem um cabeçalho `Authorization` e nenhuma das expressões anteriores estiver presente, o cabeçalho será avaliado e removido. Caso contrário, o `Authorization` sempre será passado no estado em que se encontra.

Se não houver nenhum ouvinte ativo, o serviço retornará um código de erro 502 "Gateway Incorreto". Se, aparentemente, o serviço não manipular a solicitação, o serviço retornará um 504 "Tempo Limite do Gateway" após 60 segundos.

### <a name="interaction-summary"></a>Resumo da interação

O resultado desse modelo de interação é que o cliente remetente sai do handshake com um WebSocket "limpo", que é conectado a um ouvinte e que não precisa de nenhum preâmbulo ou preparação adicionais. Esse modelo permite que praticamente qualquer implementação de cliente WebSocket existente aproveite imediatamente o serviço de Conexões Híbridas fornecendo uma URL construída corretamente na camada do cliente WebSocket.

O WebSocket da conexão de reunião que o ouvinte obtém por meio de interação com accept também é limpa e pode ser entregue a qualquer implementação de servidor de WebSocket existente com alguma abstração extra mínima, que faz a distinção entre operações "accept" nos ouvintes de rede local da sua estrutura e operações "accept" remotas do Conexões Híbridas.

O modelo de solicitação/resposta HTTP fornece ao remetente uma área de superfície de protocolo HTTP amplamente irrestrita com uma camada de autorização OPCIONAL. O ouvinte obtém uma seção de cabeçalho de solicitação HTTP pré-analisada que pode ser transformada novamente em uma solicitação HTTP de downstream ou manipulada no estado em que se encontra, com quadros binários contendo corpos HTTP. As respostas usam o mesmo formato. As interações com menos de 64 KB de corpo de solicitação e resposta podem ser manipuladas por um único soquete da Web compartilhado entre todos os remetentes. Solicitações e respostas maiores podem ser manipuladas usando o modelo de reunião.

## <a name="protocol-reference"></a>Referência de protocolo

Esta seção descreve os detalhes das interações de protocolo descritas anteriormente.

Todas as conexões de WebSocket são feitas na porta 443 como uma atualização do HTTPS 1.1, o que é normalmente abstraído por alguma API ou estrutura de WebSocket. A descrição aqui é mantida neutra em termos de implementação, sem sugerir uma estrutura específica.

### <a name="listener-protocol"></a>Protocolo de ouvinte

O protocolo de ouvinte consiste em dois gestos de conexão e três operações de mensagem.

#### <a name="listener-control-channel-connection"></a>Conexão de canal de controle do ouvinte

O canal de controle é aberto com a criação de uma conexão de WebSocket para:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

O `namespace-address` é o nome de domínio totalmente qualificado do namespace de Retransmissão do Azure que hospeda a Conexão Híbrida, normalmente no formato `{myname}.servicebus.windows.net`.

As opções de parâmetro de cadeia de caracteres de consulta são conforme demonstrado a seguir.

| Parâmetro        | Obrigatório | Descrição
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Sim      | Para a função de ouvinte, o parâmetro deve ser **SB-HC-Action = escutar**
| `{path}`         | Sim      | O caminho de namespace codificado como URL da Conexão Híbrida pré-configurada na qual este ouvinte será registrado. Esta expressão é acrescentada à parte do caminho `$hc/` fixa.
| `sb-hc-token`    | Sim\*    | O ouvinte deve fornecer um Token de Acesso válido, compartilhado com o Barramento de Serviço, em formato codificado de URL para o namespace ou Conexão Híbrida que confere o direito **Listen** (escutar).
| `sb-hc-id`       | Não       | Essa ID opcional fornecida pelo cliente permite o rastreamento de diagnóstico de ponta a ponta.

Se a conexão de WebSocket falhar porque o caminho de Conexão Híbrida não está sendo registrado, porque há um token inválido ou ausente ou por algum outro erro, os comentários de erro serão fornecidos usando o modelo comum de comentários de status HTTP 1.1. A descrição do status conterá uma ID de acompanhamento de erro que poderá ser comunicada ao pessoal de suporte do Azure:

| Código | Erro do          | Descrição
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Não encontrado      | O caminho da Conexão Híbrida é inválido ou a URL base está malformada.
| 401  | Não Autorizado   | O token de segurança está ausente ou malformado ou inválido.
| 403  | Proibido      | O token de segurança não é válido para este caminho para esta ação.
| 500  | Erro Interno | Algo deu errado no serviço.

Se a conexão de WebSocket for desligada intencionalmente pelo serviço depois que ele tiver sido inicialmente configurado, o motivo para fazer isso será comunicado usando código de erro de protocolo WebSocket apropriado juntamente com uma mensagem de erro descritiva, que também incluirá uma ID de acompanhamento. O serviço não desligará o canal de controle sem encontrar uma condição de erro. Qualquer desligamento normal é controlado de cliente.

| Status WS | Descrição
| --------- | -------------------------------------------------------------------------------
| 1001      | O caminho de Conexão Híbrida foi excluído ou desabilitado.
| 1008      | O token de segurança expirou, portanto, a política de autorização foi violada.
| 1011      | Algo deu errado no serviço.

#### <a name="accept-handshake"></a>Handshake de aceitação

A notificação "accept" é enviada pelo serviço ao ouvinte pelo canal de controle estabelecido anteriormente como uma mensagem JSON em um quadro de texto de WebSocket. Não há resposta para esta mensagem.

A mensagem contém um objeto JSON chamado "accept", que define as seguintes propriedades neste momento:

* **endereço** – a cadeia de caracteres de URL a ser usada para estabelecer o WebSocket com o serviço para aceitar uma conexão de entrada.
* **ID** – o identificador exclusivo para esta conexão. Se a ID tiver sido fornecida pelo cliente remetente, será o valor fornecido pelo remetente, caso contrário, será um valor gerado pelo sistema.
* **connectHeaders** – todos os cabeçalhos HTTP que foram fornecidos pelo remetente ao ponto de extremidade de retransmissão, o que também inclui os cabeçalhos Sec-WebSocket-Protocol e Sec-WebSocket-Extensions.

```json
{
    "accept" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?..."
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",
        "connectHeaders" : {
            "Host" : "...",
            "Sec-WebSocket-Protocol" : "...",
            "Sec-WebSocket-Extensions" : "..."
        }
     }
}
```

A URL do endereço fornecida na mensagem JSON é usada pelo ouvinte para estabelecer o WebSocket para aceitar ou rejeitar o soquete do remetente.

##### <a name="accepting-the-socket"></a>Aceitar o soquete

Para aceitar, o ouvinte estabelece uma conexão WebSocket ao endereço fornecido.

Se a mensagem "accept" tiver um cabeçalho `Sec-WebSocket-Protocol`, espera-se que o ouvinte só aceite o WebSocket se ele der suporte a esse protocolo. Além disso, ele define o cabeçalho conforme o WebSocket é estabelecido.

O mesmo se aplica ao cabeçalho `Sec-WebSocket-Extensions`. Se a estrutura der suporte a uma extensão, ela deverá definir o cabeçalho como a resposta do lado do servidor do handshake `Sec-WebSocket-Extensions` necessário para a extensão.

A URL deve ser usada no estado em que se encontra para estabelecer o soquete de aceitação, mas contém os seguintes parâmetros:

| Parâmetro      | Obrigatório | Descrição
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Sim      | Para aceitar um soquete, o parâmetro deverá ser `sb-hc-action=accept`
| `{path}`       | Sim      | (confira no parágrafo a seguir)
| `sb-hc-id`     | Não       | Consulte a descrição anterior de **id**.

`{path}` é o caminho do namespace em formato codificado de URL da Conexão Híbrida pré-configurada na qual este ouvinte deve ser registrado. Esta expressão é acrescentada à parte do caminho `$hc/` fixa.

A expressão `path` pode ser estendida com um sufixo e uma expressão de cadeia de caracteres de consulta que segue o nome registrado após uma barra de separação.
Esse parâmetro permite que o cliente remetente passe argumentos de expedição para o ouvinte de aceitação quando não é possível incluir cabeçalhos HTTP. A expectativa é que a estrutura do ouvinte analise a parte fixa do caminho e o nome registrado do caminho e faça o restante, possivelmente sem nenhum argumento da cadeia de caracteres de consulta precedido por `sb-`, disponível para o aplicativo para decidir se deseja ou não aceitar a conexão.

Para obter mais informações, consulte a seção "Sender Protocol" (Protocolo de remetente) a seguir.

Se houver um erro, o serviço poderá responder da seguinte maneira:

| Código | Erro do          | Descrição
| ---- | -------------- | -----------------------------------
| 403  | Proibido      | A URL não é válida.
| 500  | Erro Interno | Algo deu errado no serviço

 Depois que a conexão tiver sido estabelecida, o servidor será desligado do WebSocket quando o WebSocket do remetente desligar ou com o status a seguir:

| Status WS | Descrição                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | O cliente remetente encerra a conexão.                                    |
| 1001      | O caminho de Conexão Híbrida foi excluído ou desabilitado.                        |
| 1008      | O token de segurança expirou, portanto, a política de autorização foi violada. |
| 1011      | Algo deu errado no serviço.                                            |

##### <a name="rejecting-the-socket"></a>Rejeitar o soquete

 Rejeitar o soquete depois de inspecionar a mensagem `accept` requer um handshake semelhante, para que o código de status e a descrição de status que comunicam o motivo da rejeição possam fluir de volta ao remetente.

 A escolha de design do protocolo aqui é usar um handshake WebSocket (que é projetado para terminar em um estado de erro definido) para que as implementações do ouvinte cliente possam continuar a depender de um cliente WebSocket e não precisem empregar um cliente HTTP básico adicional.

 Para rejeitar o soquete, o cliente usa o URI de endereço da mensagem `accept` e acrescenta dois parâmetros da cadeia de consulta, da seguinte forma:

| Param                   | Obrigatório | Descrição                              |
| ----------------------- | -------- | ---------------------------------------- |
| sb-hc-statusCode        | Sim      | Código de status HTTP numérico.                |
| sb-hc-statusDescription | Sim      | Motivo da rejeição legível por humanos. |

O URI resultante é usado para estabelecer uma conexão WebSocket.

Ao ser concluído corretamente, esse handshake falhará intencionalmente com um código de erro HTTP 410, pois nenhum WebSocket terá sido estabelecido. Se algo der errado, os códigos a seguir descrevem o erro:

| Código | Erro do          | Descrição                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Proibido      | A URL não é válida.                |
| 500  | Erro Interno | Algo deu errado no serviço. |

#### <a name="request-message"></a>Mensagem de solicitação

A mensagem `request` é enviada pelo serviço para o ouvinte pelo canal de controle. A mesma mensagem também é enviada pelo WebSocket de reunião depois que ele é estabelecido.

A `request` consiste em duas partes: um cabeçalho e quadros de corpo binários.
Se não houver nenhum corpo, os quadros de corpo serão omitidos. A propriedade booliana `body` indica se um corpo está presente na mensagem de solicitação.

Para uma solicitação com um corpo de solicitação, a estrutura pode ser semelhante a esta:

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : true,
        ...
    }
}
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame -FIN
FEFEFEFEFEFEFEFEFEFEF...
----------------------------------
```

O ouvinte PRECISA manipular o recebimento do corpo da solicitação dividido entre vários quadros binários [confira [WebSocket fragments](https://tools.ietf.org/html/rfc6455#section-5.4) (Fragmentos do WebSocket)].
A solicitação termina quando um quadro binário com o conjunto de sinalizadores FIN é recebido.

Para uma solicitação sem um corpo, há apenas um quadro de texto.

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : false,
        ...
    }
}
----------------------------------
```

O conteúdo JSON de `request` é o seguinte:

* **address** – cadeia de caracteres do URI. É o endereço de reunião a ser usado para esta solicitação. Se a solicitação de entrada tiver mais de 64 KB, o restante dessa mensagem será deixado em branco e o cliente PRECISARÁ iniciar um handshake de reunião equivalente à operação `accept` descrita abaixo. Em seguida, o serviço colocará a `request` completa no soquete da Web estabelecido. Se a resposta puder exceder 64 KB, o ouvinte também PRECISARÁ iniciar um handshake de reunião e, em seguida, transferir a resposta pelo soquete da Web estabelecido.
* **id** – cadeia de caracteres. O identificador exclusivo desta solicitação.
* **requestHeaders** – este objeto contém todos os cabeçalhos HTTP que foram fornecidos para o ponto de extremidade pelo remetente, com exceção das informações de autorização, conforme explicado [acima](#request-operation) e dos cabeçalhos estritamente relacionados à conexão com o gateway. Especificamente, TODOS os cabeçalhos definidos ou reservados no [RFC7230](https://tools.ietf.org/html/rfc7230), exceto o `Via`, são removidos e não são encaminhados:

  * `Connection` (RFC7230, Seção 6.1)
  * `Content-Length` (RFC7230, Seção 3.3.2)
  * `Host` (RFC7230, Seção 5.4)
  * `TE` (RFC7230, Seção 4.3)
  * `Trailer` (RFC7230, Seção 4.4)
  * `Transfer-Encoding` (RFC7230, Seção 3.3.1)
  * `Upgrade` (RFC7230, Seção 6.7)
  * `Close` (RFC7230, Seção 8.1)

* **requestTarget** – cadeia de caracteres. Esta propriedade contém o ["Destino da solicitação" (RFC7230, Seção 5.3)](https://tools.ietf.org/html/rfc7230#section-5.3) da solicitação. Ele inclui a parte da cadeia de caracteres de consulta, que é removida de todos os `sb-hc-` parâmetros prefixados.
* **method** – cadeia de caracteres. Esse é o método da solicitação, de acordo com o [RFC7231, Seção 4](https://tools.ietf.org/html/rfc7231#section-4). O método `CONNECT` NÃO PODE ser usado.
* **body** – booliano. Indica se um ou mais quadros de corpo binário são mostrados a seguir.

``` JSON
{
    "request" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?...",
        "id" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "requestTarget" : "/abc/def?myarg=value&otherarg=...",
        "method" : "GET",
        "requestHeaders" : {
            "Host" : "...",
            "Content-Type" : "...",
            "User-Agent" : "..."
        },
        "body" : true
     }
}
```

##### <a name="responding-to-requests"></a>Respondendo a solicitações

O receptor PRECISA responder. A falha repetida de responder às solicitações enquanto mantém a conexão pode fazer com que o ouvinte seja bloqueado.

As respostas podem ser enviadas em qualquer ordem, mas cada solicitação precisa ser respondida em até 60 segundos ou a entrega será relatada como com falha. O prazo limite de 60 segundos é contado até que o quadro `response` seja recebido pelo serviço. Uma resposta em andamento com vários quadros binários não pode ficar ociosa por mais de 60 segundos ou será terminada.

Se a solicitação for recebida pelo canal de controle, a resposta PRECISARÁ ser enviada no canal de controle do qual a solicitação foi recebida ou ser enviada por um canal de reunião.

A resposta é um objeto JSON chamado "resposta". As regras para manipular o conteúdo do corpo são exatamente como as da mensagem de `request` e baseadas na propriedade `body`.

* **requestId** – cadeia de caracteres. OBRIGATÓRIO. O valor da propriedade `id` da mensagem `request` que está sendo respondida.
* **statusCode** – número. OBRIGATÓRIO. um código de status HTTP numérico que indica o resultado da notificação. Todos os códigos de status do [RFC7231, Seção 6](https://tools.ietf.org/html/rfc7231#section-6) são permitidos, exceto o [502 "Gateway Incorreto"](https://tools.ietf.org/html/rfc7231#section-6.6.3) e o [504 "Tempo Limite do Gateway"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **statusDescription** – cadeia de caracteres. OPCIONAL. Frase de motivo do código de status HTTP de acordo com o [RFC7230, Seção 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** – cabeçalhos HTTP a serem definidos em uma resposta HTTP externa.
  Assim como acontece com a `request`, cabeçalhos definidos pelo RFC7230 NÃO PODEM ser usados.
* **body** – booliano. Indica se seguem quadros de corpo binário.

``` text
----- Web Socket text frame ----
{
    "response" : {
        "requestId" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "statusCode" : "200",
        "responseHeaders" : {
            "Content-Type" : "application/json",
            "Content-Encoding" : "gzip"
        }
         "body" : true
     }
}
----- Web Socket binary frame -FIN
{ "hey" : "mydata" }
----------------------------------
```

##### <a name="responding-via-rendezvous"></a>Respondendo por meio de reunião

Para as respostas que excedem 64 KB, a resposta PRECISA ser entregue por um soquete de reunião. Além disso, se a solicitação exceder 64 KB e a `request` contiver apenas o campo de endereço, um soquete de reunião precisará ser estabelecido para obter a `request`. Após o estabelecimento de um soquete de reunião, as respostas para o respectivo cliente e as próximas solicitações desse respectivo cliente PRECISARÃO ser entregues pelo soquete de reunião enquanto ele persistir.

A URL `address` na `request` precisa ser usada no estado em que se encontra para estabelecer o soquete de reunião, mas contém os seguintes parâmetros:

| Parâmetro      | Obrigatório | Descrição
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Sim      | Para aceitar um soquete, o parâmetro deverá ser `sb-hc-action=request`

Se houver um erro, o serviço poderá responder da seguinte maneira:

| Código | Erro do           | Descrição
| ---- | --------------- | -----------------------------------
| 400  | Solicitação inválida | Ação não reconhecida ou URL inválida.
| 403  | Proibido       | A URL expirou.
| 500  | Erro Interno  | Algo deu errado no serviço

 Depois que a conexão é estabelecida, o servidor desliga o WebSocket quando o soquete HTTP do cliente é desligado ou com o status a seguir:

| Status WS | Descrição                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | O cliente remetente encerra a conexão.                                    |
| 1001      | O caminho de Conexão Híbrida foi excluído ou desabilitado.                        |
| 1008      | O token de segurança expirou, portanto, a política de autorização foi violada. |
| 1011      | Algo deu errado no serviço.                                            |


#### <a name="listener-token-renewal"></a>Renovação de tokens do ouvinte

Quando o token do ouvinte estiver prestes a expirar, ele poderá ser substituído enviando uma mensagem de quadro de texto ao serviço por meio do canal de controle estabelecido. A mensagem contém um objeto JSON chamado `renewToken`, que define a propriedade a seguir neste momento:

* **token** – um token de acesso compartilhado de barramento de serviço de codificação de URL válido para o namespace ou conexão híbrida que confere o direito de **escuta** .

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Se a validação de token falhar, o acesso será negado e serviço de nuvem fechará o WebSocket do canal de controle com um erro. Caso contrário, não há nenhuma resposta.

| Status WS | Descrição                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | O token de segurança expirou, portanto, a política de autorização foi violada. |

### <a name="web-socket-connect-protocol"></a>Protocolo de conexão de soquete da Web

O protocolo de remetente é efetivamente idêntico ao modo como um ouvinte é estabelecido.
A meta é o máximo de transparência para o WebSocket de ponta a ponta. O endereço ao qual se conectar é o mesmo do ouvinte, mas a "ação" é diferente e o token precisa de uma permissão diferente:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sb-hc-token=...
```

O _namespace-address_ é o nome de domínio totalmente qualificado do namespace de Retransmissão do Azure que hospeda a Conexão Híbrida, normalmente no formato `{myname}.servicebus.windows.net`.

A solicitação pode conter cabeçalhos HTTP adicionais arbitrários, incluindo aqueles definidos pelo aplicativo. Todos os cabeçalhos fornecidos fluem para o ouvinte e podem ser encontrados no objeto `connectHeader` da mensagem de controle **accept**.

As opções de parâmetro de cadeia de caracteres de consulta são conforme demonstrado a seguir:

| Param          | Necessário? | Descrição
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Sim       | Para a função de remetente, o parâmetro deve ser `sb-hc-action=connect`.
| `{path}`       | Sim       | (confira no parágrafo a seguir)
| `sb-hc-token`  | Sim\*     | O ouvinte deve fornecer um Token de Acesso válido, compartilhado com o Barramento de Serviço, em formato codificado de URL para o namespace ou Conexão Híbrida que confere o direito **Send**.
| `sb-hc-id`     | Não        | Uma ID opcional que possibilita o rastreamento de diagnóstico de ponta a ponta e é disponibilizada para o ouvinte durante o handshake de aceitação.

 O `{path}` é o namespace em formato codificado de URL da Conexão Híbrida pré-configurada na qual este ouvinte deve ser registrado. A expressão `path` pode ser estendida com um sufixo e uma expressão de cadeia de caracteres de consulta para se comunicar ainda mais. Se a Conexão Híbrida for registrada no caminho `hyco`, a expressão `path` poderá ser `hyco/suffix?param=value&...` seguida por parâmetros de cadeia de caracteres de consulta definidos aqui. Assim, uma expressão completa pode ser da seguinte maneira:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sb-hc-token=...
```

A expressão `path` é passada por meio do ouvinte no URI do endereço contido na mensagem de controle "accept".

Se a conexão de WebSocket falhar porque o caminho de Conexão Híbrida não está sendo registrado, porque há um token inválido ou ausente ou por algum outro erro, os comentários de erro serão fornecidos usando o modelo comum de comentários de status HTTP 1.1. A descrição do status conterá uma ID de acompanhamento de erro que poderá ser comunicada ao pessoal de suporte do Azure:

| Código | Erro do          | Descrição
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Não encontrado      | O caminho da Conexão Híbrida é inválido ou a URL base está malformada.
| 401  | Não Autorizado   | O token de segurança está ausente ou malformado ou inválido.
| 403  | Proibido      | O token de segurança não é válido para este caminho e para esta ação.
| 500  | Erro Interno | Algo deu errado no serviço.

Se a conexão de WebSocket for desligada intencionalmente pelo serviço depois que ele tiver sido inicialmente configurado, o motivo para fazer isso será comunicado usando código de erro de protocolo WebSocket apropriado juntamente com uma mensagem de erro descritiva, que também incluirá uma ID de acompanhamento.

| Status WS | Descrição
| --------- | ------------------------------------------------------------------------------- 
| 1000      | O ouvinte desliga o soquete.
| 1001      | O caminho de Conexão Híbrida foi excluído ou desabilitado.
| 1008      | O token de segurança expirou, portanto, a política de autorização foi violada.
| 1011      | Algo deu errado no serviço.

### <a name="http-request-protocol"></a>Protocolo de solicitação HTTP

O protocolo de solicitação HTTP permite solicitações HTTP arbitrárias, exceto as atualizações de protocolo.
As solicitações HTTP são apontadas no endereço do runtime normal da entidade, sem o infixo $hc que é usado para clientes WebSocket de conexões híbridas.

```
https://{namespace-address}/{path}?sb-hc-token=...
```

O _namespace-address_ é o nome de domínio totalmente qualificado do namespace de Retransmissão do Azure que hospeda a Conexão Híbrida, normalmente no formato `{myname}.servicebus.windows.net`.

A solicitação pode conter cabeçalhos HTTP adicionais arbitrários, incluindo aqueles definidos pelo aplicativo. Todos os cabeçalhos fornecidos, exceto aqueles diretamente definidos no fluxo RFC7230 (consulte a [mensagem de solicitação](#request-message)) para o ouvinte e podem ser encontrados no `requestHeader` objeto da mensagem de **solicitação** .

As opções de parâmetro de cadeia de caracteres de consulta são conforme demonstrado a seguir:

| Param          | Necessário? | Descrição
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Sim\*     | O ouvinte deve fornecer um Token de Acesso válido, compartilhado com o Barramento de Serviço, em formato codificado de URL para o namespace ou Conexão Híbrida que confere o direito **Send**.

O token também pode estar contido no cabeçalho HTTP `ServiceBusAuthorization` ou `Authorization`. O token poderá ser omitido se a Conexão Híbrida for configurada para permitir solicitações anônimas.

Como o serviço age efetivamente como um proxy, mesmo que não como um proxy HTTP verdadeiro, ele adiciona um cabeçalho `Via` ou anota o cabeçalho `Via` existente compatível com o [RFC7230, Seção 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
O serviço adiciona o nome do host do namespace de retransmissão em `Via`.

| Código | Mensagem  | Descrição                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | A solicitação foi manipulada por pelo menos um ouvinte.  |
| 202  | Aceita | A solicitação foi aceita por pelo menos um ouvinte. |

Se houver um erro, o serviço poderá responder da seguinte maneira. É possível identificar se a resposta se origina do serviço ou do ouvinte por meio da presença do cabeçalho `Via`. Se o cabeçalho estiver presente, a resposta será do ouvinte.

| Código | Erro do           | Descrição
| ---- | --------------- |--------- |
| 404  | Não encontrado       | O caminho da Conexão Híbrida é inválido ou a URL base está malformada.
| 401  | Não Autorizado    | O token de segurança está ausente ou malformado ou inválido.
| 403  | Proibido       | O token de segurança não é válido para este caminho e para esta ação.
| 500  | Erro Interno  | Algo deu errado no serviço.
| 503  | Gateway incorreto     | Não foi possível rotear a solicitação para nenhum ouvinte.
| 504  | Tempo Limite do Gateway | A solicitação foi roteada para um ouvinte, mas o ouvinte não confirmou o recebimento no tempo necessário.

## <a name="next-steps"></a>Próximas etapas

* [Perguntas frequentes sobre retransmissão](relay-faq.md)
* [Criar um namespace](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)
