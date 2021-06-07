---
title: Suporte ao MQTT 5 do Hub IoT do Azure (versão prévia)
description: Saiba mais sobre o suporte do MQTT 5 do Hub IoT
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fb2cc0b81083936a67bcd465e0408b9f4b53996b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96603149"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>Visão geral do suporte do Hub IoT MQTT 5 (versão prévia)

**Versão:** 2,0 **API-Version:** 2020-10-01 – Preview

Este documento define a API do plano de dados do Hub IoT sobre o protocolo MQTT versão 5,0. Consulte [referência de API](iot-hub-mqtt-5-reference.md) para obter definições completas nesta API.

## <a name="prerequisites"></a>Pré-requisitos

- [Habilite o modo de visualização](iot-hub-preview-mode.md) em um hub IOT totalmente novo para experimentar o MQTT 5.
- O conhecimento prévio da [especificação MQTT 5](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html) é necessário.

## <a name="level-of-support-and-limitations"></a>Nível de suporte e limitações

O suporte do Hub IoT para MQTT 5 está em versão prévia e limitado das seguintes maneiras (comunicado ao cliente por meio de `CONNACK` Propriedades, a menos que explicitamente indicado de outra forma):

- Ainda não há suporte oficial para o [SDK do dispositivo do Hub IOT do Azure](iot-hub-devguide-sdks.md) .
- Não há suporte para identificadores de assinatura.
- Não há suporte para assinaturas compartilhadas.
- Não há suporte para `RETAIN`.
- `Maximum QoS` é `1`.
- `Maximum Packet Size` está `256 KiB` (sujeito a restrições adicionais por operação).
- As IDs de cliente atribuídas não têm suporte.
- `Keep Alive` é limitado a `19 min` (atraso máximo para verificação de vida – `28.5 min` ).
- `Topic Alias Maximum` é `10`.
- `Response Information` Não tem suporte; `CONNACK` não retorna `Response Information` Propriedade mesmo que `CONNECT` contém a `Request Response Information` propriedade.
- `Receive Maximum` (o número máximo permitido de pacotes não confirmados pendentes `PUBLISH` (na direção do cliente-servidor) `QoS: 1` é `16` .
- Um único cliente não pode ter mais do que `50` assinaturas.
  Quando o limite for atingido, o `SUBACK` retornará o `0x97` código de motivo para assinaturas (Cota excedida).

## <a name="connection-lifecycle"></a>Ciclo de vida da conexão

### <a name="connection"></a>Conexão

Para conectar um cliente ao Hub IoT usando essa API, estabeleça conexão por especificação MQTT 5.
O cliente deve enviar `CONNECT` o pacote dentro de 30 segundos seguindo o handshake de TLS bem-sucedido ou o servidor fecha a conexão.
Aqui está um exemplo de `CONNECT` pacote:

```yaml
-> CONNECT
    Protocol_Version: 5
    Clean_Start: 0
    Client_Id: D1
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    api-version: 2020-10-10
    host: abc.azure-devices.net
    sas-at: 1600987795320
    sas-expiry: 1600987195320
    client-agent: artisan;Linux
```

- `Authentication Method` a propriedade é necessária e identifica qual método de autenticação é usado. Para obter mais informações sobre o método de autenticação, consulte [autenticação](#authentication).
- `Authentication Data` a manipulação de propriedade depende `Authentication Method` . Se `Authentication Method` é definido como `SAS` , `Authentication Data` é obrigatório e deve conter uma assinatura válida. Para obter mais informações sobre dados de autenticação, consulte [autenticação](#authentication).
- `api-version` a propriedade é necessária e deve ser definida como o valor de versão da API fornecido no cabeçalho desta especificação para que essa especificação seja aplicada.
- `host` a propriedade define o nome do host do locatário. É necessário, a menos que a extensão SNI tenha sido apresentada no registro de saudação do cliente durante o handshake do TLS
- `sas-at` define a hora da conexão.
- `sas-expiry` define o tempo de expiração para a SAS fornecida.
- `client-agent` Opcionalmente, comunica informações sobre o cliente que está criando a conexão.

> [!NOTE]
> `Authentication Method` e outras propriedades em toda a especificação com nomes capitalizados são propriedades de primeira classe no MQTT 5-elas são descritas em detalhes na especificação do MQTT 5. `api-version` e outras propriedades no caso tracejado são propriedades do usuário específicas para a API do Hub IoT.

O Hub IoT responde com `CONNACK` o pacote quando ele termina com a autenticação e busca dados para dar suporte à conexão. Se a conexão for estabelecida com êxito, `CONNACK` é semelhante a:

```yaml
<- CONNACK
    Session_Present: 1
    Reason_Code: 0x00
    Session_Expiry_Interval: 0xFFFFFFFF # included only if CONNECT specified value less than 0xFFFFFFFF and more than 0x00
    Receive_Maximum: 16
    Maximum_QoS: 1
    Retain_Available: 0
    Maximum_Packet_Size: 262144
    Topic_Alias_Maximum: 10
    Subscription_Identifiers_Available: 0
    Shared_Subscriptions_Available: 0
    Server_Keep_Alive: 1140 # included only if client did not specify Keep Alive or if it specified a bigger value
```

Essas `CONNACK` Propriedades de pacote seguem a [especificação MQTT 5](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901080). Eles refletem os recursos do Hub IoT.

### <a name="authentication"></a>Autenticação

A `Authentication Method` propriedade no `CONNECT` cliente define o tipo de autenticação que ele usa para esta conexão:

- `SAS` -A assinatura de acesso compartilhado é fornecida na `CONNECT` `Authentication Data` propriedade de,
- `X509` -o cliente depende da autenticação de certificado do cliente.

 A autenticação falhará se o método de autenticação não corresponder ao método configurado do cliente no Hub IoT.

> [!NOTE]
> Essa API requer `Authentication Method` que a propriedade seja definida no `CONNECT` pacote. Se `Authentication Method` a propriedade não for fornecida, a conexão falhará com a `Bad Request` resposta.

Não há suporte para a autenticação de nome de usuário/senha usada em versões anteriores da API.

#### <a name="sas"></a>SAS

Com a autenticação baseada em SAS, o cliente deve fornecer a assinatura do contexto de conexão. Isso comprova a autenticidade da conexão do MQTT. A assinatura deve ser baseada em uma das duas chaves de autenticação na configuração do cliente no Hub IoT ou em uma de duas chaves de acesso compartilhado de uma [política de acesso compartilhado](iot-hub-devguide-security.md).

A cadeia de caracteres a ser assinada deve ser formada da seguinte maneira:

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name` é derivado de uma extensão SNI (apresentada pelo cliente no registro de saudação do cliente durante o handshake de TLS) ou à `host` Propriedade do usuário no `CONNECT` pacote.
- `Client Id` é identificador de cliente no `CONNECT` pacote.
- `sas-policy` -se presente, define a política de acesso do Hub IoT usada para autenticação. Ele é codificado como uma propriedade de usuário no `CONNECT` pacote. Opcional: omitir isso significa que as configurações de autenticação no registro do dispositivo serão usadas em seu lugar.
- `sas-at` -se presente, especifica a hora da conexão – hora atual. Ele é codificado como propriedade de usuário do `time` tipo no `CONNECT` pacote.
- `sas-expiry` define o tempo de expiração para a autenticação. É uma `time` propriedade de usuário digitada no `CONNECT` pacote. Esta propriedade é necessária.

Para parâmetros opcionais, se omitido, uma cadeia de caracteres vazia deverá ser usada em vez de uma cadeia de caracteres para assinar.

HMAC-SHA256 é usado para aplicar o hash à cadeia de caracteres com base em uma das chaves simétricas do dispositivo. O valor de hash é então definido como o valor da `Authentication Data` propriedade.

#### <a name="x509"></a>X509

Se `Authentication Method` a propriedade for definida como `X509` , o Hub IOT autenticará a conexão com base no certificado de cliente fornecido.

#### <a name="reauthentication"></a>Reautenticação

Se a autenticação baseada em SAS for usada, recomendamos o uso de tokens de autenticação de curta duração. Para manter a conexão autenticada e impedir a desconexão devido à expiração, o cliente deve autenticar novamente enviando um `AUTH` pacote com `Reason Code: 0x19` (reautenticação):

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

Regras:

- `Authentication Method` deve ser o mesmo usado para a autenticação inicial
- se a conexão tiver sido autenticada originalmente usando SAS com base na política de acesso compartilhado, a assinatura usada na reautenticação deverá ser baseada na mesma política.

Se a reautenticação for bem-sucedida, o Hub IoT enviará um `AUTH` pacote com `Reason Code: 0x00` (êxito). Caso contrário, o Hub IoT enviará `DISCONNECT` um pacote com `Reason Code: 0x87` (não autorizado) e fechará a conexão.

### <a name="disconnection"></a>Desconexão

O servidor pode desconectar o cliente por alguns motivos:

- o cliente é inadequado de forma que é impossível responder com a confirmação negativa (ou resposta) diretamente.
- o servidor não está conseguindo manter o estado da conexão atualizado,
- o cliente com a mesma identidade foi conectado.

O servidor pode se desconectar com qualquer código de motivo definido na especificação MQTT 5,0. Menção notáveis:

- `135` (Não autorizado) quando a reautenticação falha, o token SAS atual expira ou a alteração das credenciais do dispositivo
- `142` (Sessão capturada) quando uma nova conexão com a mesma identidade do cliente tiver sido aberta.
- `159` (Taxa de conexão excedida) quando a taxa de conexão para o Hub IoT excede  
- `131` (Erro específico da implementação) é usado para quaisquer erros personalizados definidos nesta API. `status``reason`as propriedades e serão usadas para comunicar mais detalhes sobre a causa da desconexão (consulte a [resposta](#response) para obter detalhes).

## <a name="operations"></a>Operations

Todas as funcionalidades nessa API são expressas como operações. Aqui está um exemplo de operação de envio de telemetria:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 3
    Topic: $iothub/telemetry
    Payload: Hello

<- PUBACK
    Packet_Id: 3
    Reason_Code: 0
```

Para obter a especificação completa das operações nessa API, consulte [referência de API](iot-hub-mqtt-5-reference.md).

> [!NOTE]
> Todos os exemplos nessa especificação são mostrados da perspectiva do cliente. Sign `->` significa que o cliente está enviando o pacote, `<-` recebendo.

### <a name="message-topics-and-subscriptions"></a>Tópicos e assinaturas de mensagens

Os tópicos usados nas mensagens de operações nessa API começam com `$iothub/` .
A semântica do agente MQTT não se aplica a essas operações (consulte "[Tópicos \$ que começam com ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246)" para obter detalhes).
Os tópicos que começam com `$iothub/` isso não são definidos nesta API não têm suporte:

- Enviar mensagens para resultados indefinidos do tópico em `Not Found` resposta (consulte a [resposta](#response) para obter detalhes abaixo),
- Assinatura de resultados indefinidos de tópico no `SUBACK` com `Reason Code: 0x8F` (filtro de tópico inválido).

Nomes de tópico e nomes de propriedade diferenciam maiúsculas de minúsculas e devem ser correspondências exatas. Por exemplo, `$iothub/telemetry/` não tem suporte enquanto `$iothub/telemetry` está.

> [!NOTE]
> Não há suporte para curingas em assinaturas em inscrições `$iothub/..` . Ou seja, um cliente não pode assinar `$iothub/+` ou `$iothub/#` . A tentativa de fazer isso resulta em `SUBACK` com `Reason Code: 0xA2` (assinaturas curinga sem suporte). Somente curingas de segmento único ( `+` ) têm suporte em vez de parâmetros de caminho no nome do tópico para operações que os têm.

### <a name="interaction-types"></a>Tipos de interação

Todas as operações nessa API se baseiam em um dos dois tipos de interação:

- Mensagem com confirmação opcional (MessageAck)
- Request-Response (ReqRep)

As operações também variam de acordo com a direção (determinada pela direção da mensagem inicial no Exchange):

- Cliente para servidor (C2S)
- Servidor para cliente (s2c)

Por exemplo, a telemetria de envio é a operação de cliente para servidor do tipo "mensagem com reconhecimento", enquanto o método direto Handle é uma operação de servidor para cliente do tipo Request-Response.

#### <a name="message-acknowledgement-interactions"></a>Interações de confirmação de mensagem

A mensagem com a interação de confirmação opcional (MessageAck) é expressa como uma troca de `PUBLISH` `PUBACK` pacotes e no MQTT. A confirmação é opcional e o remetente pode optar por não solicitá-la enviando um `PUBLISH` pacote com `QoS: 0` .

> [!NOTE]
> Se as propriedades no `PUBACK` pacote precisarem ser truncadas devido a `Maximum Packet Size` declaradas pelo cliente, o Hub IOT manterá quantas Propriedades de usuário forem ajustadas dentro do limite fornecido. As propriedades do usuário listadas primeiro têm maior chance de serem enviadas do que aquelas listadas posteriormente; `Reason String` a propriedade tem a menor prioridade.

##### <a name="example-of-simple-messageack-interaction"></a>Exemplo de interação MessageAck simples

Mensagem:

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

Confirmação (êxito):

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>Interações de Request-Response

Em interações de Request-Response (ReqRep), a solicitação e a resposta são transvertidas em `PUBLISH` pacotes com `QoS: 0` .

`Correlation Data` a propriedade deve ser definida em ambos e é usada para corresponder o pacote de resposta para solicitar o pacote.

Essa API usa o tópico de resposta única `$iothub/responses` para todas as operações de ReqRep. A assinatura/cancelamento deste tópico para operações de cliente para servidor não é necessária-o servidor assume que todos os clientes serão assinados.

##### <a name="example-of-simple-reqrep-interaction"></a>Exemplo de interação ReqRep simples

Solicitação:

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

Resposta (êxito):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

As interações ReqRep não dão suporte `PUBLISH` a pacotes `QoS: 1` como mensagens de solicitação ou resposta. Enviar `PUBLISH` resultados da solicitação em `Bad Request` resposta.

O comprimento máximo com suporte na `Correlation Data` propriedade é 16 bytes. Se `Correlation Data` a propriedade no `PUBLISH` pacote for definida com um valor maior que 16 bytes, o Hub IOT enviará `DISCONNECT` com `Bad Request` o resultado e fechará a conexão. Esse comportamento se aplica somente a pacotes trocados nessa API.

> [!NOTE]
> Os dados de correlação são uma sequência de bytes arbitrária, por exemplo, não é garantido que seja uma cadeia de caracteres UTF-8.
>
> ReqRep usar tópicos predefinidos para resposta; A propriedade do tópico de resposta no pacote de solicitação `PUBLISH` (se definido pelo remetente) é ignorada.

O Hub IoT assina automaticamente os tópicos de resposta do cliente para todas as operações de ReqRep de cliente para servidor. Mesmo que o cliente cancele a assinatura explicitamente do tópico de resposta, o Hub IoT restabelece a assinatura automaticamente. Para interações de ReqRep de servidor para cliente, ainda é necessário que o dispositivo assine.

### <a name="message-properties"></a>Propriedades da Mensagem

Propriedades da operação – sistema ou definido pelo usuário – são expressas como propriedades do pacote no MQTT 5.

Os nomes de Propriedade do usuário diferenciam maiúsculas de minúsculas e devem ser escritos exatamente conforme definido. Por exemplo, `Trace-ID` não tem suporte enquanto `trace-id` está.

As solicitações com propriedades do usuário fora da especificação e sem o `@` resultado do prefixo resultam em erro.

As propriedades do sistema são codificadas como propriedades da primeira classe (por exemplo, `Content Type` ) ou como propriedades do usuário. A especificação fornece uma lista completa de propriedades de sistema com suporte.
Todas as propriedades da primeira classe são ignoradas, a menos que o suporte para elas seja explicitamente declarado na especificação.

Quando propriedades definidas pelo usuário são permitidas, seus nomes devem seguir o formato `@{property name}` . As propriedades definidas pelo usuário dão suporte apenas a valores de cadeia de caracteres UTF-8 válidos. por exemplo, `MyProperty1` a propriedade com valor `15` deve ser codificada como propriedade de usuário com nome `@MyProperty` e valor `15` .

Se o Hub IoT não reconhecer a propriedade do usuário, ele será considerado um erro e o Hub IoT responderá com `PUBACK` com `Reason Code: 0x83` (erro específico da implementação) e `status: 0100` (solicitação inadequada). Se a confirmação não foi solicitada (QoS: 0), `DISCONNECT` o pacote com o mesmo erro será enviado de volta e a conexão será encerrada.

Essa API define os seguintes tipos de dados, além de `string` :

- `time`: número de milissegundos desde `1970-01-01T00:00:00.000Z` . por exemplo, `1600987195320` para `2020-09-24T22:39:55.320Z` ,
- `u32`: número inteiro de 32 bits sem sinal,
- `u64`: número inteiro de 64 bits sem sinal,
- `i32`: número inteiro de 32 bits assinado.

### <a name="response"></a>Resposta

As interações podem resultar em resultados diferentes: `Success` , `Bad Request` , `Not Found` e outras.
Os resultados são diferenciados uns dos outros por `status` Propriedade do usuário. `Reason Code` em `PUBACK` pacotes (para interações de MessageAck) corresponde `status` em significado quando possível.

> [!NOTE]
> Se o cliente especificar `Request Problem Information: 0` no pacote do Connect, nenhuma Propriedade do usuário será enviada em `PUBACK` pacotes para estar em conformidade com a especificação MQTT 5, incluindo `status` Property. Nesse caso, o cliente ainda pode confiar `Reason Code` para determinar se a confirmação é positiva ou negativa. 

Cada interação tem um padrão (ou sucesso). Ele tem `Reason Code` de `0` e `status` propriedade de "não definido". Caso contrário:

- Para interações de MessageAck, obtém-se `PUBACK` `Reason Code` diferente de 0x0 (êxito). `status` a propriedade pode estar presente para esclarecer ainda mais o resultado.
- Para interações ReqRep, Response `PUBLISH` Obtém o `status` conjunto de propriedades.
- Como não há como responder a interações de MessageAck `QoS: 0` diretamente, o `DISCONNECT` pacote é enviado, em vez de informações de resposta, seguido pela desconexão.

Exemplos:

Solicitação inadequada (MessageAck):

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

Não autorizado (MessageAck):

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

Não autorizado (ReqRep):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

Quando necessário, o Hub IoT define as seguintes propriedades de usuário:

- `status` -Código estendido do Hub IoT para o status da operação. Esse código pode ser usado para diferenciar os resultados.
- `trace-id` – ID de rastreamento para a operação; O Hub IoT pode manter um diagnóstico adicional relativo à operação que poderia ser usada para investigação interna.
- `reason` -mensagem legível por humanos fornecendo mais informações sobre por que a operação foi encerrada em um estado indicado pela `status` propriedade.

> [!NOTE]
> Se o cliente definir `Maximum Packet Size` a propriedade no pacote de conexão com um valor muito pequeno, nem todas as propriedades de usuário poderão se ajustar e não aparecerão no pacote.
> 
> `reason` o destina-se apenas a pessoas e não deve ser usado na lógica do cliente. Essa API permite que as mensagens sejam alteradas em qualquer ponto sem aviso ou alteração de versão.
>
> Se o cliente enviar `RequestProblemInformation: 0` no pacote do Connect, as propriedades do usuário não serão incluídas em confirmações por [especificação MQTT 5](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053).

#### <a name="status-code"></a>Código de status

`status` a propriedade transporta o código de status para a operação. Ele é otimizado para a eficiência de leitura do computador.
Ele consiste em um inteiro não assinado de dois bytes codificado como hex em cadeia de caracteres como `0501` .
Estrutura de código (mapa de bits):

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

O primeiro byte é usado para sinalizadores:

- bits 0 e 1 indicam o tipo de resultados:
  - `00` -êxito
  - `01` -erro do cliente
  - `10` -erro do servidor
- bit 2: `1` indica que o erro é repetido
- os bits 3 a 7 são reservados e devem ser definidos como `0`

O segundo byte contém o código de resposta distinto real. Códigos de erro com sinalizadores diferentes podem ter o mesmo valor de byte segundo. Por exemplo, pode haver `0001` ,, `0101` `0201` , códigos de `0301` erro com significado distinto.

Por exemplo, `Too Many Requests` é um cliente, um erro com nova tentativa com o próprio código de `1` . Seu valor é `0000 0101 0000 0001` ou `0x0501` .

Os clientes podem usar o tipo bits para identificar se a operação foi concluída com êxito. Os clientes também podem usar um bit com nova tentativa para decidir se é sensato tentar novamente a operação.

## <a name="recommendations"></a>Recomendações

### <a name="session-management"></a>Gerenciamento da sessão

`CONNACK` o pacote transporta `Session Present` a propriedade para indicar se o servidor restaurou a sessão criada anteriormente. Use essa propriedade para descobrir se deseja assinar tópicos ou ignorar a assinatura desde que a inscrição tenha sido feita anteriormente.

Para contar com o `Session Present` , o cliente deve manter o controle das assinaturas feitas (ou seja, o `SUBSCRIBE` pacote enviado e recebido `SUBACK` com o código de motivo bem-sucedido) ou não se esqueça de assinar todos os tópicos em uma única `SUBSCRIBE` / `SUBACK` troca. Caso contrário, se o cliente enviar dois `SUBSCRIBE` pacotes, e apenas um deles for processado com êxito pelo servidor, o servidor irá se comunicar `Session Present: 1` `CONNACK` enquanto tem apenas parte das assinaturas do cliente aceitas.

Para evitar o caso em que uma versão mais antiga do cliente não se inscreveu em todos os tópicos, é melhor assinar incondicionalmente quando o comportamento do cliente for alterado (por exemplo, como parte da atualização do firmware). Além disso, para garantir que nenhuma assinatura obsoleta seja deixada para trás (assumindo o número máximo permitido de assinaturas), cancele explicitamente a assinatura de assinaturas que não estão mais em uso.

### <a name="batching"></a>Separação em lotes

Não há nenhum formato especial para enviar um lote de mensagens. Para reduzir a sobrecarga de operações de uso intensivo de recursos em TLS e rede, agrupe pacotes ( `PUBLISH` ,, `PUBACK` `SUBSCRIBE` e assim por diante) antes de entregá-los à pilha TLS/TCP subjacente. Além disso, o cliente pode tornar o alias do tópico mais fácil dentro do "Batch":

- Coloque o nome do tópico completo no primeiro `PUBLISH` pacote para a conexão e associe o alias do tópico a ele,
- Coloque os pacotes a seguir para o mesmo tópico com o nome do tópico e a propriedade de alias do tópico vazios.

## <a name="migration"></a>Migração

Esta seção lista as alterações na API em comparação com a [API MQTT anterior](iot-hub-mqtt-support.md).

- O protocolo de transporte é MQTT 5. MQTT 3.1.1 anteriormente.
- As informações de contexto para autenticação SAS estão contidas no `CONNECT` pacote diretamente, em vez de serem codificadas junto com a assinatura.
- O método de autenticação é usado para indicar o método de autenticação usado.
- A assinatura de acesso compartilhado é colocada na propriedade de dados de autenticação. O campo de senha anterior foi usado.
- Os tópicos para operações são diferentes:
  - Telemetria: `$iothub/telemetry` em vez de `devices/{Client Id}/messages/events` ,
  - Comandos: `$iothub/commands` em vez de `devices/{Client Id}/messages/devicebound` ,
  - O patch de atualização é reportado: `$iothub/twin/patch/reported` em vez de `$iothub/twin/PATCH/properties/reported` ,
  - Notificar o estado desejado do entrelaçamento alterado: `$iothub/twin/patch/desired` em vez de `$iothub/twin/PATCH/properties/desired` .
- A assinatura do tópico de resposta de operações de solicitação-resposta de cliente-servidor não é necessária.
- As propriedades do usuário são usadas em vez das propriedades de codificação no tópico segmento nome.
- os nomes de propriedade são escritos na Convenção de nomenclatura "traço-caso" em vez de abreviações com prefixo especial. Agora, as propriedades definidas pelo usuário exigem o prefixo. Por exemplo, `$.mid` é agora `message-id` , enquanto `myProperty1` se torna `@myProperty1` .
- A propriedade de dados de correlação é usada para correlacionar mensagens de solicitação e resposta para operações de solicitação-resposta em vez da `$rid` Propriedade codificada no tópico.
- `iothub-connection-auth-method` a propriedade não está mais carimbada em eventos de telemetria.
- Os comandos C2D não serão limpos na ausência da assinatura do dispositivo. Eles permanecerão na fila até que o dispositivo assine ou expire.

## <a name="examples"></a>Exemplos

### <a name="send-telemetry"></a>Enviar telemetria

Mensagem:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 31
    Topic: $iothub/telemetry
    @myProperty1: My String Value # optional
    creation-time: 1600987195320 # optional
    @ No_Rules-ForUser-PROPERTIES: Any UTF-8 string value # optional
    Payload: <data>
```

Confirmação:

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 0
```

Confirmação alternativa (limitada):

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>Enviar o estado de Get entrelaçado

Solicitação:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

Resposta (êxito):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

Resposta (não permitido):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    status: 0102
    reason: Operation not allowed for `B2` SKU
    Payload: <empty>
```

---

### <a name="handle-direct-method-call"></a>Tratar chamada de método direto

Solicitação:

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/abc
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Resposta (êxito):

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status` Não está definido-é uma resposta de êxito.

Resposta do dispositivo não disponível:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>Erro ao usar o QoS 0, parte 1

Solicitação:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/gett # misspelled topic name - server won't recognize it as Request-Response interaction
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Resposta:

```yaml
<- DISCONNECT
    Reason_Code: 144
    reason: "Unsupported topic: `$iothub/twin/gett`"
```

---

### <a name="error-while-using-qos-0-part-2"></a>Erro ao usar a QoS 0, parte 2

Solicitação:

```yaml
-> PUBLISH # missing Correlation Data
    QoS: 0
    Topic: $iothub/twin/get
    Payload: <data>
```

Resposta:

```yaml
<- DISCONNECT
    Reason_Code: 131
    status: 0100
    reason: "`Correlation Data` property is missing"
```
## <a name="next-steps"></a>Próximas etapas

- Para examinar a referência da API de visualização do MQTT 5, consulte [referência da API do plano de dados do Hub IOT MQTT 5](iot-hub-mqtt-5-reference.md).
- Para seguir um exemplo de C#, consulte [repositório de exemplo do GitHub](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp).