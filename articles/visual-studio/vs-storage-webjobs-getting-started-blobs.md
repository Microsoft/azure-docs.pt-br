---
title: "Introdução ao armazenamento de blob e aos serviços conectados do Visual Studio (projetos de Trabalho Web) | Microsoft Docs"
description: "Como começar a usar o armazenamento de Blob em um projeto WebJob depois de se conectar a um armazenamento do Azure usando os serviços conectados do Visual Studio."
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 7d683f950e8847a18f38158a8f8727b1274fc711
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Introdução ao armazenamento de Blob do Azure e aos serviços conectados do Visual Studio (projetos WebJob)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Visão geral
Este artigo fornece exemplos de código C# que mostram como disparar um processo quando um blob do Azure é criado ou atualizado. Os exemplos de código usam o [SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki) versão 1.x. Quando você adiciona uma conta de armazenamento a um projeto de WebJob usando a caixa de diálogo **Adicionar Serviços Conectados** do Visual Studio, o pacote do NuGet do Armazenamento do Azure apropriado é instalado, as referências apropriadas .NET são adicionadas ao projeto e cadeias de conexão para a conta de armazenamento são atualizadas no arquivo App.config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Como disparar uma função quando um blob é criado ou atualizado
Esta seção mostra como usar o atributo **BlobTrigger** .

 **Observação:** o SDK dos trabalhos Web verifica os arquivos de log para observar blobs novos ou alterados. Esse processo é inerentemente lento; uma função não poderá ser disparada até vários minutos ou mais depois que o blob for criado.  Se seu aplicativo precisar processar blobs imediatamente, o método recomendado é criar uma mensagem da fila ao criar o blob e usar o atributo **QueueTrigger** em vez do atributo **BlobTrigger** na função que processa o blob.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Espaço reservado único para nome de blob com extensão
O seguinte exemplo de código copia blobs de texto que aparecem no contêiner de *entrada* para o contêiner de *saída*:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

O construtor de atributo utiliza um parâmetro de cadeia de caracteres que especifica o nome do contêiner e um espaço reservado para o nome do blob. Neste exemplo, se um blob denominado *Blob1.txt* for criado no contêiner de *entrada*, a função criará um blob denominado *Blob1.txt* no contêiner de *saída*.

Você pode especificar um padrão de nome com o espaço reservado de nome de blob, conforme é mostrado no seguinte exemplo de código:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Esse código copia somente os blobs que têm nomes que começam com "original-". Por exemplo, *original-Blob1.txt* no contêiner de *entrada* é copiado para *copy-Blob1.txt* no contêiner de *saída*.

Se você precisar especificar um padrão de nome para nomes de blob que têm chaves no nome, duplique as chaves. Por exemplo, para localizar blobs no contêiner *imagens* que têm nomes como este:

        {20140101}-soundfile.mp3

Use o seguinte para o padrão:

        images/{{20140101}}-{name}

No exemplo, o valor do espaço reservado para *nome* seria *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Separar espaços reservados de nome de blob e extensão
O exemplo de código a seguir altera a extensão do arquivo à medida que ele copia blobs que aparecem no contêiner de *entrada* para o contêiner de *saída*. O código registra a extensão do blob de *entrada* e define a extensão do blob de *saída* como *.txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Tipos que você pode associar a blobs
Você pode usar o atributo **BlobTrigger** nos seguintes tipos:

* **string**
* **TextReader**
* **Fluxo**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Outros tipos desserializados por [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Se quer trabalhar diretamente com a conta de armazenamento do Azure, você também pode adicionar um parâmetro **CloudStorageAccount** à assinatura do método.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Obtendo o conteúdo do blob de texto associando à cadeia de caracteres
Se blobs de texto forem esperados, **BlobTrigger** poderá ser aplicado a um parâmetro **string**. O exemplo de código a seguir associa um blob de texto a um parâmetro **string** denominado **logMessage**. A função usa esse parâmetro para gravar o conteúdo do blob no painel do SDK de Trabalhos Web.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Obtendo conteúdo de blob serializado usando ICloudBlobStreamBinder
O exemplo de código a seguir usa uma classe que implementa **ICloudBlobStreamBinder** para permitir que o atributo **BlobTrigger** associe um blob ao tipo **WebImage**.

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

O código de associação **WebImage** é fornecido em uma classe **WebImageBinder** que deriva de **ICloudBlobStreamBinder**.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Como manipular blobs suspeitos
Quando uma função **BlobTrigger** falha, o SDK a chama novamente caso a falha tenha sido causada por um erro transitório. Se a falha for causada pelo conteúdo do blob, a função falhará sempre que tentar processar o blob. Por padrão, o SDK chama uma função até cinco vezes para um blob específico. Se a quinta tentativa falhar, o SDK adicionará uma mensagem a uma fila denominada *webjobs-blobtrigger-poison*.

O número máximo de novas tentativas é configurável. A mesma **MaxDequeueCount** é usada para manipular blob suspeitos e manipular mensagens de filas suspeitas.

A mensagem da fila para blobs suspeitos é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *{Nome do Trabalho Web}*.Functions.*{Nome da função}*, por exemplo: Trabalho Web1.Functions.CopyBlob)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

No exemplo de código a seguir, a função **CopyBlob** tem código que faz com que ela falhe sempre que for chamada. Depois que o SDK a chamar pelo número máximo de tentativas, será criada uma mensagem na fila de blobs suspeitos, e essa mensagem será processada pela função **LogPoisonBlob** .

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

O SDK automaticamente desserializa a mensagem JSON. Veja aqui a classe **PoisonBlobMessage** :

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritmo de sondagem de blob
O SDK de Trabalhos Web examina todos os contêineres especificados pelos atributos **BlobTrigger** na inicialização do aplicativo. Em uma conta de armazenamento grande, essa verificação pode levar algum tempo. Portanto, pode demorar um pouco até que novos blobs sejam encontrados e as funções **BlobTrigger** sejam executadas.

Para detectar blobs novos ou alterados após a inicialização do aplicativo, o SDK lê periodicamente dos logs do armazenamento de blob. Os logs de blob são armazenados em buffer e gravados fisicamente apenas a cada 10 minutos, aproximadamente. Portanto, pode haver um atraso significativo depois que um blob é criado ou atualizado antes que a função **BlobTrigger** correspondente seja executada.

Há uma exceção para blobs que você cria usando o atributo **Blob** . Quando o SDK de WebJobs cria um novo blob, ele passa o novo blob imediatamente para quaisquer funções **BlobTrigger** correspondentes. Portanto, se você tiver uma cadeia de entradas e saídas de blob, o SDK poderá processá-las com eficiência. Porém, se você quiser que haja baixa latência ao executar as funções de processamento para blobs criados ou atualizados por outros meios, é recomendável usar **QueueTrigger** em vez de **BlobTrigger**.

### <a name="blob-receipts"></a>Recebimentos de blob
O SDK de WebJobs garante que nenhuma função **BlobTrigger** seja chamada mais de uma vez para o mesmo blob novo ou atualizado. Ele faz isso mantendo *recebimentos de blob* para determinar se uma versão de determinado blob foi processada.

Os recebimentos de blob são armazenados em um contêiner denominado *azure-webjobs-hosts* na conta de armazenamento do Azure especificada pela cadeia de conexão AzureWebJobsStorage. Um recebimento de blob tem as seguintes informações:

* A função que foi chamada para o blob ("*{Nome do Trabalho Web}*.Functions.*{Nome da função}*", por exemplo: "Trabalho Web1.Functions.CopyBlob")
* O nome do contêiner
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome do blob
* O ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Para forçar o reprocessamento de um blob, você pode excluir manualmente o recebimento desse blob do contêiner *azure-webjobs-hosts* .

## <a name="related-topics-covered-by-the-queues-article"></a>Tópicos relacionados abordados no artigo sobre filas
Para obter informações sobre como lidar com o processamento de blob disparado por uma mensagem da fila ou para cenários do SDK de Trabalhos Web não específicos do processamento de blob, consulte [Como usar o armazenamento de fila do Azure com o SDK de Trabalhos Web](https://github.com/Azure/azure-webjobs-sdk/wiki).

Os tópicos relacionados abordados neste artigo incluem o seguinte:

* Funções assíncronas
* Várias instâncias
* Desligamento normal
* Usar atributos do SDK de Trabalhos Web no corpo de uma função
* Definir as cadeias de conexão do SDK no código.
* Definir valores para parâmetros do construtor do SDK WebJobs no código
* Configure **MaxDequeueCount** para tratamento de blob suspeito.
* Disparar uma função manualmente
* Gravar logs

## <a name="next-steps"></a>Próximas etapas
Este artigo forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com blobs do Azure. Para obter mais informações sobre como usar o Azure WebJobs e o SDK do WebJobs, consulte [Recursos de documentação do Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

