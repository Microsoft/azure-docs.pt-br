---
title: Enviar e receber eventos usando o Go - Hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece instruções passo a passo para a criação de um aplicativo Go que envia eventos dos Hubs de Eventos do Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 823ebc985c77785f8b48d12d5919dbbd1b2b1459
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821681"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-go"></a>Enviar eventos para ou receber eventos dos Hubs de eventos usando o Go
Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como escrever aplicativos Go para enviar eventos para ou receber eventos de um hub de eventos. 

> [!NOTE]
> Você pode baixar do [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs) este início rápido como um exemplo, substituir as cadeias de caracteres `EventHubConnectionString` e `EventHubName` pelos valores do hub de eventos e executá-lo. Como alternativa, é possível seguir as etapas deste tutorial para criar sua própria solução.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- Go instalado Localmente. Siga [estas instruções](https://golang.org/doc/install), se necessário.
- Uma conta ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][] antes de começar.
- **Criar um namespace de Hubs de eventos e um hub de eventos**. Use o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento que seu aplicativo precisa se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [nesse artigo](event-hubs-create.md).

## <a name="send-events"></a>Enviar eventos
Esta seção mostra como criar um aplicativo Go para enviar eventos para um hub de eventos. 

### <a name="install-go-package"></a>Instalar o pacote do Go

Obtenha o pacote do Go para Hubs de Eventos com `go get` ou `dep`. Por exemplo: 

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Importe os pacotes no seu arquivo de código

Para importar os pacotes Go, use o exemplo de código a seguir:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Criar uma entidade de serviço

Criar um entidade de serviço seguindo as instruções em [Criar uma entidade de serviço com a CLI do Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Salve as credenciais fornecidas no seu ambiente com os nomes a seguir. O SDK do Azure para linguagem Go e os pacotes de Hubs de Eventos são pré-configurados para procurar esses nomes de variáveis:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Agora, crie um provedor de autorização para seu cliente de Hubs de Eventos que usa estas credenciais:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Criar cliente dos Hubs de Eventos

O código a seguir cria um novo Hub de Eventos:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Gravar código para enviar mensagens

No snippet a seguir, use (1) para enviar mensagens de forma interativa em um terminal ou (2) para enviar mensagens dentro do seu programa:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Extras

Obtenha as IDs das partições no seu hub de eventos:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Execute o aplicativo para enviar eventos ao hub de eventos. 

Parabéns! Agora você enviou mensagens para um hub de eventos.

## <a name="receive-events"></a>Receber eventos

### <a name="create-a-storage-account-and-container"></a>Criar uma conta e um contêiner de armazenamento

Afirme como as concessões em partições e pontos de verificação no evento fluxo são compartilhados entre os destinatários usando um contêiner de Armazenamento do Microsoft Azure. Você pode criar uma conta de armazenamento e um contêiner com o SDK Go, mas você também pode criar um seguindo as instruções em [Sobre Contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).

Exemplos para a criação de artefatos de armazenamento com o SDK Go estão disponíveis na [repositório de exemplos do Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) e na amostra correspondente a este tutorial.

### <a name="go-packages"></a>Vá de pacotes

Para receber as mensagens, obter os pacotes de Go para Hubs de Eventos com `go get` ou `dep`:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Importe os pacotes no seu arquivo de código

Para importar os pacotes Go, use o exemplo de código a seguir:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>Criar uma entidade de serviço

Criar um entidade de serviço seguindo as instruções em [Criar uma entidade de serviço com a CLI do Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Salve as credenciais fornecidas no ambiente com os nomes a seguir: O SDK do Azure para linguagem Go e os pacotes de Hubs de Eventos são pré-configurados para procurar esses nomes de variáveis.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Em seguida, crie um provedor de autorização para seu cliente de Hubs de Eventos que usa estas credenciais:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Obter struc de metadados

Obtenha um struct com metadados sobre seu ambiente do Azure usando o SDK Go do Azure. Operações posteriores usam esse struct para localizar pontos de extremidade corretos.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Criar auxiliar credencial 

Crie um auxiliar credencial que usa as credenciais do Azure Active Directory (AAD) anterior para criar uma credencial de assinatura de acesso compartilhado (SAS) para armazenamento. O último parâmetro informa esse construtor para usar as mesmas variáveis de ambiente que foi usada anteriormente:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>Criar um ponteiro de seleção e um leaser 

Criar uma **leaser**, responsável pela concessão de uma partição para um destinatário específico e uma **verificar ponteiro**, responsáveis por gravar pontos de verificação para o fluxo de mensagens para que podem começar a outros destinatários lendo o deslocamento correto.

Atualmente, um único **StorageLeaserCheckpointer** está disponível que usa o mesmo contêiner de armazenamento para gerenciar as concessões e pontos de verificação. Além dos nomes de conta e o contêiner de armazenamento, o **StorageLeaserCheckpointer** precisa de credenciais criadas na etapa anterior e o struct de ambiente do Azure para acessar corretamente o contêiner.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>Construir o Host do processador de eventos

Agora você tem as partes necessárias para construir um EventProcessorHost, da seguinte maneira. O mesmo **StorageLeaserCheckpointer** é usado como um leaser e o ponteiro de seleção, conforme descrito anteriormente:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>Criar Manipulador 

Agora, crie um manipulador e registre-o com o Host do processador de eventos. Quando o host é iniciado, ele se aplica esse e outros manipuladores especificados às mensagens de entrada:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>Gravar código para receber mensagens

Com tudo configurado, você pode iniciar o Host do processador de eventos com `Start(context)` para mantê-lo permanentemente em execução, ou com `StartNonBlocking(context)` apenas executar, desde que as mensagens estão disponíveis.

Este tutorial é iniciado e executado como se segue; Consulte o exemplo do GitHub para obter um exemplo usando `StartNonBlocking`:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Próximas etapas
Leia os seguintes artigos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Recursos e a terminologia nos Hubs de eventos do Azure](event-hubs-features.md)
- [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
