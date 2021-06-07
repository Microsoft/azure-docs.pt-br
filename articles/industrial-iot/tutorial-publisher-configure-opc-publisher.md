---
title: Configurar o Microsoft OPC Publisher
description: Neste tutorial, você aprenderá a configurar o OPC Publisher no modo autônomo.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4d4f9c90fd96365216480164f29f08fad92eb9d0
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787179"
---
# <a name="tutorial-configure-the-opc-publisher"></a>Tutorial: Configurar o OPC Publisher

Este tutorial contém informações sobre a configuração do OPC Publisher. Várias interfaces podem ser usadas para configurá-lo.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar o OPC Publisher por meio do arquivo de configuração
> * Configurar o OPC Publisher por meio de argumentos de linha de comando
> * Configurar o OPC Publisher por meio de métodos diretos do Hub IoT
> * Configurar o OPC Publisher por meio de um microsserviço REST complementar baseado em nuvem

## <a name="configuring-security"></a>Configurando a segurança

O IoT Edge fornece ao OPC Publisher uma configuração de segurança para acessar o Hub IoT automaticamente. O OPC Publisher também pode ser executado como um contêiner autônomo do Docker pela especificação de uma cadeia de conexão do dispositivo para acessar o Hub IoT por meio do parâmetro de linha de comando `dc`. Um dispositivo para o Hub IoT pode ser criado e a cadeia de conexão dele recuperada pelo portal do Azure.

Para acessar os ativos habilitados para OPC UA, os certificados X.509 e as respectivas chaves privadas associadas são usadas pelo OPC UA. Isso é chamado de autenticação de aplicativo OPC UA e é complementar à autenticação de usuário OPC UA. O OPC Publisher usa um repositório de certificados baseado no sistema de arquivos para gerenciar todos os certificados de aplicativo. Durante a inicialização, o OPC Publisher verifica se há um certificado que pode ser usado nesses repositórios de certificados e cria um certificado autoassinado e uma chave privada associada se não há nenhuma. Os certificados autoassinados fornecem uma autenticação fraca, pois não são assinados por uma autoridade de certificação confiável, mas, pelo menos, a comunicação com o ativo habilitado para OPC UA pode ser criptografada dessa forma.

A segurança é habilitada no arquivo de configuração por meio do sinalizador `"UseSecurity": true,`. O ponto de extremidade mais seguro disponível nos servidores OPC UA ao qual o OPC Publisher deve se conectar é selecionado automaticamente.
Por padrão, o OPC Publisher usa a autenticação de usuário anônimo (além da autenticação de aplicativo descrita acima). No entanto, o OPC Publisher também dá suporte à autenticação de usuário por meio de um nome de usuário e uma senha. Isso pode ser especificado por meio da interface de configuração da API REST (descrita abaixo) ou do arquivo de configuração da seguinte maneira:
```
"OpcAuthenticationMode": "UsernamePassword",
"OpcAuthenticationUsername": "usr",
"OpcAuthenticationPassword": "pwd",
```
Além disso, o OPC Publisher versão 2.5 e inferior criptografa o nome de usuário e a senha no arquivo de configuração. A versão 2.6 e superior só dá suporte ao nome de usuário e à senha em texto não criptografado. Isso será aprimorado na próxima versão do OPC Publisher.

Para manter a configuração de segurança do OPC Publisher entre as reinicializações, o certificado e a chave privada localizados no diretório do repositório de certificados precisam ser mapeados para o sistema de arquivos do sistema operacional do host do IoT Edge. Confira "Como especificar opções de criação de contêiner no portal do Azure" acima.

## <a name="configuration-via-configuration-file"></a>Configuração por meio de um arquivo de configuração

A maneira mais simples de configurar o OPC Publisher é por meio de um arquivo de configuração. Um arquivo de configuração de exemplo, bem como a documentação sobre o formato dele, é fornecido por meio do arquivo [`publishednodes.json`](https://raw.githubusercontent.com/Azure/iot-edge-opc-publisher/master/opcpublisher/publishednodes.json) neste repositório.
A sintaxe do arquivo de configuração mudou ao longo do tempo, e o OPC Publisher ainda pode ler formatos antigos, mas os converte no formato mais recente ao persistir a configuração, feito regularmente de maneira automatizada.

Um arquivo de configuração básico é parecido com este:
```
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

Os ativos do OPC UA otimizam a largura de banda da rede enviando apenas alterações de dados para o OPC Publisher quando os dados são alterados. Se as alterações de dados precisam ser publicadas com mais frequência ou em intervalos regulares, o OPC Publisher dá suporte a uma "pulsação" para cada item de dados configurado que possa ser habilitado, especificando adicionalmente a chave HeartbeatInterval na configuração do item de dados. O intervalo é especificado em segundos:
```
 "HeartbeatInterval": 3600,
```

Um ativo do OPC UA sempre envia o valor atual de um item de dados quando o OPC Publisher se conecta a ele pela primeira vez. Para evitar a publicação desses dados no Hub IoT, a chave SkipFirst também pode ser especificada na configuração do item de dados:
```
 "SkipFirst": true,
```

As duas configurações também podem ser habilitadas globalmente por meio de opções de linha de comando.

## <a name="configuration-via-command-line-arguments"></a>Configuração por meio de argumentos de linha de comando

Há vários argumentos de linha de comando que podem ser usados para definir configurações globais para o OPC Publisher. Eles são descritos [abaixo](reference-command-line-arguments.md).


## <a name="configuration-via-the-built-in-opc-ua-server-interface"></a>Configuração por meio da interface interna do servidor OPC UA

>[!NOTE] 
> Esse recurso só está disponível na versão 2.5 e inferior do OPC Publisher.**

O OPC Publisher tem um servidor OPC UA interno, em execução na porta 62222. Ele implementa três métodos do OPC UA:

  - PublishNode
  - UnpublishNode
  - GetPublishedNodes

Essa interface pode ser acessada com um aplicativo cliente do OPC UA, por exemplo, o [UA Expert](https://www.unified-automation.com/products/development-tools/uaexpert.html).

## <a name="configuration-via-iot-hub-direct-methods"></a>Configuração por meio de métodos diretos do Hub IoT

>[!NOTE] 
> Esse recurso só está disponível na versão 2.5 e inferior do OPC Publisher.**

O OPC Publisher implementa os seguintes [métodos diretos do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-direct-methods), que podem ser chamados em um aplicativo (de qualquer lugar do mundo), aproveitando o [SDK do Dispositivo do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks):

  - PublishNodes
  - UnpublishNodes
  - UnpublishAllNodes
  - GetConfiguredEndpoints
  - GetConfiguredNodesOnEndpoint
  - GetDiagnosticInfo
  - GetDiagnosticLog
  - GetDiagnosticStartupLog
  - ExitApplication
  - GetInfo

Fornecemos um [exemplo de aplicativo de configuração](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration), bem como um [aplicativo para leitura das informações de diagnóstico](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) de software livre do OPC Publisher, que aproveita essa interface.

## <a name="configuration-via-cloud-based-companion-rest-microservice"></a>Configuração por meio de um microsserviço REST complementar baseado em nuvem

>[!NOTE] 
> Esse recurso só está disponível na versão 2.6 e superior do OPC Publisher.

Um microsserviço complementar baseado em nuvem com uma interface REST é descrito e está disponível [aqui](https://github.com/Azure/Industrial-IoT/blob/master/docs/services/publisher.md). Ele pode ser usado para configurar o OPC Publisher por meio de uma interface compatível com a OpenAPI, por exemplo, por meio do Swagger.

## <a name="configuration-of-the-simple-json-telemetry-format-via-separate-configuration-file"></a>Configuração do formato de telemetria JSON simples por meio de um arquivo de configuração separado

>[!NOTE] 
> Esse recurso só está disponível na versão 2.5 e inferior do OPC Publisher.

O OPC Publisher permite filtrar as partes do formato de telemetria simples não padronizado por meio de um arquivo de configuração separado, que pode ser especificado pela opção de linha de comando tc. Se nenhum arquivo de configuração for especificado, o formato de telemetria JSON completo será enviado ao Hub IoT. O formato do arquivo de configuração de telemetria separado é descrito [aqui](reference-opc-publisher-telemetry-format.md#opc-publisher-telemetry-configuration-file-format).

## <a name="next-steps"></a>Próximas etapas
Agora que você configurou o OPC Publisher, a próxima etapa será aprender a ajustar o desempenho e a memória do módulo de borda:

> [!div class="nextstepaction"]
> [Ajuste de desempenho e de memória](tutorial-publisher-performance-memory-tuning-opc-publisher.md)