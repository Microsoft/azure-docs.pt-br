---
title: Tutorial – Criar um aplicativo altamente disponível com o armazenamento de Blobs
titleSuffix: Azure Storage
description: Use o RA-GZRS (armazenamento com redundância de zona geográfica com acesso de leitura) para tornar os dados do aplicativo altamente disponíveis.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc, devx-track-python, devx-track-js, devx-track-csharp
ms.subservice: blobs
ms.openlocfilehash: dfb7e7c7c93a8af2b59f6d3d7049e2c14b8f382a
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611042"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Tutorial: Criar um aplicativo altamente disponível com o armazenamento de Blobs

Este tutorial é a primeira parte de uma série. Nele, você aprenderá a tornar os dados de seu aplicativo altamente disponíveis no Azure.

Quando concluir este tutorial, você terá um aplicativo de console que carrega e recupera um blob de uma conta de [RA-GZRS](../common/storage-redundancy.md) (armazenamento com redundância de zona geográfica com acesso de leitura).

A redundância geográfica no Armazenamento do Azure replica transações de modo assíncrono de uma região primária para uma região secundária que está a centenas de quilômetros de distância. Esse processo de replicação garante que os dados na região secundária acabem sendo consistentes. O aplicativo de console usa o padrão de [disjuntor](/azure/architecture/patterns/circuit-breaker) para determinar a qual ponto de extremidade ele se conectará, alternando automaticamente entre pontos de extremidade conforme as falhas e as recuperações são simuladas.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Na primeira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Definir a cadeia de conexão
> * Executar o aplicativo de console

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

# <a name="net"></a>[.NET](#tab/dotnet)

* Instale o [Visual Studio 2019](https://www.visualstudio.com/downloads/) com a carga de trabalho de **Desenvolvimento do Azure**.

  ![Desenvolvimento do Azure (em Web e Nuvem)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="python"></a>[Python](#tab/python)

* Instalar o [Python](https://www.python.org/downloads/)
* Baixe e instale o [SDK do Armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

* Instale o [Node.js](https://nodejs.org).

---

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento fornece um namespace exclusivo para armazenar e acessar os objetos de dados do Armazenamento do Azure.

Siga estas etapas para criar uma conta de RA-GZRS (armazenamento com redundância de zona geográfica com acesso de leitura):

1. Selecione o botão **Criar um recurso** no portal do Azure.
2. Selecione **Conta de armazenamento – blob, arquivo, tabela, fila** na página **Novo**.
4. Preencha o formulário de conta de armazenamento com as informações a seguir, conforme mostrado na imagem a seguir e selecione **Criar**:

   | Configuração       | Valor de exemplo | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Assinatura** | *Minha assinatura* | Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.azure.com/Subscriptions). |
   | **ResourceGroup** | *myResourceGroup* | Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming). |
   | **Nome** | *mystorageaccount* | Um nome exclusivo para sua conta de armazenamento. |
   | **Localidade** | *Leste dos EUA* | Escolha um local. |
   | **Desempenho** | *Standard* | O desempenho padrão é uma boa opção para o cenário de exemplo. |
   | **Tipo de conta** | *StorageV2* | É recomendável usar uma conta de armazenamento de uso geral v2. Para saber mais sobre os tipos de contas de Armazenamento do Microsoft Azure, confira [Visão geral da conta de armazenamento do Microsoft](../common/storage-account-overview.md). |
   | **Replicação**| *RA-GZRS (armazenamento com redundância de zona geográfica com acesso de leitura)* | A região primária tem redundância de zona e é replicada para uma região secundária com acesso de leitura à região secundária habilitado. |
   | **Camada de acesso**| *Frequente* | Use a camada de acesso quente para dados acessados com frequência. |

    ![criar conta de armazenamento](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Baixar o exemplo

# <a name="net"></a>[.NET](#tab/dotnet)

[Baixe o projeto de exemplo](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) e extraia (descompacte) o arquivo storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Também é possível usar o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. O projeto de exemplo contém um aplicativo de console.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="python"></a>[Python](#tab/python)

[Baixe o projeto de exemplo](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) e extraia (descompacte) o arquivo storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip file. Também é possível usar o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. O projeto de exemplo contém um aplicativo Python básico.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[Baixe o projeto de exemplo](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) e descompacte o arquivo. Também é possível usar o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. O projeto de exemplo contém um aplicativo Node.js básico.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>Configurar o exemplo

# <a name="net"></a>[.NET](#tab/dotnet)

No aplicativo, você deve fornecer a cadeia de conexão para sua conta de armazenamento. Você pode armazenar essa cadeia de conexão dentro de uma variável de ambiente no computador local que está executando o aplicativo. Siga um dos exemplos abaixo, dependendo do seu sistema operacional para criar a variável de ambiente.

No portal do Azure, navegue até sua conta de armazenamento. Selecione **Chaves de acesso** em **Configurações** na sua conta de armazenamento. Copie a **cadeia de conexão** da chave primária ou secundária. Execute um dos comandos a seguir com base no seu sistema operacional, substituindo \<yourconnectionstring\> pela cadeia de conexão real. Este comando salva uma variável de ambiente no computador local. No Windows, a variável de ambiente não fica disponível enquanto você não recarrega o **Prompt de Comando** ou o shell que está sendo usado.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="python"></a>[Python](#tab/python)

No aplicativo, você deve fornecer suas credenciais da conta de armazenamento. Você pode armazenar essa informação no computador local executando o aplicativo. Siga um dos exemplos abaixo, dependendo do seu sistema operacional para criar as variáveis de ambiente.

No portal do Azure, navegue até sua conta de armazenamento. Selecione **Chaves de acesso** em **Configurações** na sua conta de armazenamento. Cole os valores do **Nome da conta de armazenamento** e da **Chave** nos comandos a seguir, substituindo os espaços reservados \<youraccountname\> e \<youraccountkey\>. Este comando salva as variáveis de ambiente no computador local. No Windows, a variável de ambiente não fica disponível enquanto você não recarrega o **Prompt de Comando** ou o shell que está sendo usado.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Para executar este exemplo, adicione suas credenciais da conta de armazenamento ao `.env.example` do arquivo e, em seguida, renomeie-o como `.env`.

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

Você pode encontrar essas informações no portal do Azure navegando em sua conta de armazenamento e selecionando as **Chaves de Acesso** na seção **Configurações**.

Instale as dependências necessárias. Para fazer isso, abra um prompt de comando, navegue para a mesma pasta e execute o comando `npm install`.

---

## <a name="run-the-console-application"></a>Executar o aplicativo de console

# <a name="net"></a>[.NET](#tab/dotnet)

No Visual Studio, pressione **F5** ou selecione **Iniciar** para iniciar a depuração do aplicativo. O Visual Studio automaticamente restaura os pacotes NuGet ausentes, se configurado. Acesse [Instalando e reinstalando pacotes com a restauração de pacote](/nuget/consume-packages/package-restore#package-restore-overview) para saber mais.

Uma janela de console é inicializada e aplicativo começa a ser executado. O aplicativo carrega a imagem **HelloWorld.png** da solução para a conta de armazenamento. O aplicativo verifica para garantir que a imagem foi replicada para o ponto de extremidade de RA-GZRS secundário. Ele então começa a baixar a imagem até 999 vezes. Cada leitura é representada por um **P** ou um **S**. **P** representa o ponto de extremidade primário e **S** representa o ponto de extremidade secundário.

![Aplicativo de console em execução](media/storage-create-geo-redundant-storage/figure3.png)

No código de exemplo, a tarefa `RunCircuitBreakerAsync` no arquivo `Program.cs` é usada para baixar uma imagem da conta de armazenamento usando o método [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync). Antes da baixar, um [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext) é definido. O contexto de operação define manipuladores de eventos que são acionados quando um download é concluído com êxito ou se um download falha e tenta novamente.

# <a name="python"></a>[Python](#tab/python)

Para executar o aplicativo em um terminal ou prompt de comando, vá para o diretório **circuitbreaker.py**, e insira `python circuitbreaker.py`. O aplicativo carrega a imagem **HelloWorld.png** da solução para a conta de armazenamento. O aplicativo verifica para garantir que a imagem foi replicada para o ponto de extremidade de RA-GZRS secundário. Ele então começa a baixar a imagem até 999 vezes. Cada leitura é representada por um **P** ou um **S**. **P** representa o ponto de extremidade primário e **S** representa o ponto de extremidade secundário.

![Aplicativo de console em execução](media/storage-create-geo-redundant-storage/figure3.png)

No código de exemplo, o método `run_circuit_breaker` no arquivo `circuitbreaker.py` é usado para baixar uma imagem da conta de armazenamento usando o método [get_blob_to_path](/python/api/azure-storage-blob/azure.storage.blob.baseblobservice.baseblobservice#get-blob-to-path-container-name--blob-name--file-path--open-mode--wb---snapshot-none--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--lease-id-none--if-modified-since-none--if-unmodified-since-none--if-match-none--if-none-match-none--timeout-none-).

A função de repetição do Objeto de armazenamento é definida como uma política de repetição linear. A função de repetição determina se uma solicitação será repetida, além de especificar o número de segundos a aguardar antes de repeti-la. Defina o valor **retry\_to\_secondary** como true se a solicitação deve ser repetida para o secundário no caso de a solicitação inicial para o primário falhar. No aplicativo de exemplo, uma política de repetição personalizada é definida na função `retry_callback` do objeto de armazenamento.

Antes de fazer o download, o Objeto de serviço [retry_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient) e a função [response_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient) são definidos. Essas funções definem manipuladores de eventos disparados quando um download é concluído com êxito ou caso um download falhe e seja tentado novamente.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Para executar o exemplo, abra um prompt de comando, navegue para a pasta de exemplo, em seguida insira `node index.js`.

O exemplo cria um contêiner na sua conta de armazenamento de Blob, carrega **HelloWorld.png** no contêiner, em seguida, repetidamente verifica se o contêiner e imagem foram replicados para a região secundária. Após a replicação, ele solicitará que você insira **D** ou **Q** (seguido de ENTER) para baixar ou encerrar. Seu resultado deve ser semelhante ao seguinte exemplo:

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

### <a name="net"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Repetir o manipulador de eventos

O manipulador de eventos `OperationContextRetrying` é chamado quando o download da imagem falha e é definido para repetição. Se o número máximo de repetições definido no aplicativo for atingido o [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) da solicitação será alterado para `SecondaryOnly`. Essa configuração força o aplicativo tentar baixar a imagem do ponto de extremidade secundário. Essa configuração reduz o tempo necessário para solicitar a imagem, uma vez que ponto de extremidade primário não é repetido indefinidamente.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Manipulador de eventos de solicitação concluída

O manipulador de eventos `OperationContextRequestCompleted` é chamado quando o download da imagem é bem-sucedido. Se o aplicativo estiver usando o ponto de extremidade secundário, o aplicativo continuará a usar esse ponto de extremidade até 20 vezes. Depois de 20 vezes, o aplicativo definirá o [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) de volta para `PrimaryThenSecondary` e repetirá o ponto de extremidade primário. Se uma solicitação for bem-sucedida, o aplicativo continuará a leitura do ponto de extremidade primário.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

### <a name="python"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>Repetir o manipulador de eventos

O manipulador de eventos `retry_callback` é chamado quando o download da imagem falha e é definido para repetição. Se o número máximo de repetições definido no aplicativo for atingido o [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode) da solicitação será alterado para `SECONDARY`. Essa configuração força o aplicativo tentar baixar a imagem do ponto de extremidade secundário. Essa configuração reduz o tempo necessário para solicitar a imagem, uma vez que ponto de extremidade primário não é repetido indefinidamente.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Manipulador de eventos de solicitação concluída

O manipulador de eventos `response_callback` é chamado quando o download da imagem é bem-sucedido. Se o aplicativo estiver usando o ponto de extremidade secundário, o aplicativo continuará a usar esse ponto de extremidade até 20 vezes. Depois de 20 vezes, o aplicativo definirá o [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode) de volta para `PRIMARY` e repetirá o ponto de extremidade primário. Se uma solicitação for bem-sucedida, o aplicativo continuará a leitura do ponto de extremidade primário.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Com o SDK do Node. js V10, manipuladores de retorno de chamada são desnecessários. Em vez disso, o exemplo cria um pipeline configurado com opções de repetição e um ponto de extremidade secundário. Isso permite que o aplicativo alterne automaticamente para o pipeline secundário se ele não conseguir acessar os dados por meio do pipeline primário.

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>Próximas etapas

Na primeira parte da série, você aprendeu a tornar um aplicativo altamente disponível com contas de armazenamento RA-GZRS.

Avance para a parte dois da série para saber como simular uma falha e forçar o aplicativo a usar o ponto de extremidade RA-GZRS secundário.

> [!div class="nextstepaction"]
> [Simule uma falha na leitura da região primária](simulate-primary-region-failure.md)