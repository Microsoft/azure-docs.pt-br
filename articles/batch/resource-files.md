---
title: Criando e usando arquivos de recursos – lote do Azure | Microsoft Docs
description: Saiba como criar arquivos de recursos do lote do Azure de várias fontes de entrada.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: 9c55b22d1cb85fb645087cf48b54f9d5ac12d58f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322177"
---
# <a name="creating-and-using-resource-files"></a>Criando e usando arquivos de recursos

Uma tarefa do lote do Azure geralmente requer alguma forma de dados para processar. Os arquivos de recurso são os meios para fornecer esses dados para a VM (máquina virtual) do lote por meio de uma tarefa. Todos os tipos de tarefas oferecem suporte a arquivos de recursos: tarefas, tarefas de início, tarefas de preparação de trabalho, tarefas de liberação de trabalho etc. Este artigo aborda alguns métodos comuns de como criar arquivos de recursos e colocá-los em uma VM.  

Os arquivos de recurso são um mecanismo para colocar dados em uma VM no lote, mas o tipo de dados e como ele é usado é flexível. No entanto, há alguns casos de uso comuns:

1. Provisionar arquivos comuns em cada VM usando arquivos de recursos em uma tarefa inicial
1. Provisionar dados de entrada a serem processados por tarefas

Os arquivos comuns podem ser, por exemplo, arquivos em uma tarefa inicial usada para instalar aplicativos executados por suas tarefas. Os dados de entrada podem ser dados brutos de imagem ou de vídeo ou qualquer informação a ser processada pelo lote.

## <a name="types-of-resource-files"></a>Tipos de arquivos de recurso

Há algumas opções diferentes disponíveis para gerar arquivos de recursos. O processo de criação de arquivos de recursos varia dependendo de onde os dados originais são armazenados.

Opções para criar um arquivo de recurso:

- [URL do contêiner de armazenamento](#storage-container-url): Gera um arquivo de recurso de qualquer contêiner de armazenamento no Azure
- [Nome do contêiner de armazenamento](#storage-container-name): Gera um arquivo de recurso do nome de um contêiner em uma conta de armazenamento do Azure vinculada ao lote
- [Ponto de extremidade da Web](#web-endpoint): Gera um arquivo de recurso de qualquer URL HTTP válida

### <a name="storage-container-url"></a>URL do contêiner de armazenamento

Usar uma URL de contêiner de armazenamento significa que você pode acessar arquivos em qualquer contêiner de armazenamento no Azure com as permissões corretas.

Neste C# exemplo, os arquivos já foram carregados em um contêiner de armazenamento do Azure como armazenamento de BLOBs. Para acessar os dados necessários para criar um arquivo de recurso, primeiro precisamos obter acesso ao contêiner de armazenamento.

Crie um URI de assinatura de acesso compartilhado (SAS) com as permissões corretas para acessar o contêiner de armazenamento. Defina o tempo de expiração e as permissões para a SAS. Nesse caso, nenhuma hora de início é especificada, portanto, a SAS se torna válida imediatamente e expira duas horas após sua geração.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Para acesso de contêiner, você deve ter `Read` ambas `List` as permissões e, ao passo que com acesso de `Read` BLOB, você só precisa de permissão.

Depois que as permissões forem configuradas, crie o token SAS e formate a URL SAS para acesso ao contêiner de armazenamento. Usando a URL de SAS formatada para o contêiner de armazenamento, gere um arquivo [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)de recurso com.

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Uma alternativa para gerar uma URL SAS é habilitar o acesso de leitura anônimo e público a um contêiner e seus BLOBs no armazenamento de BLOBs do Azure. Ao fazer isso, você pode conceder acesso somente leitura a esses recursos sem compartilhar sua chave de conta e sem a necessidade de uma SAS. O acesso de leitura público normalmente é usado para cenários em que você deseja que determinados BLOBs estejam sempre disponíveis para acesso de leitura anônimo. Se esse cenário se adequar à sua solução, consulte o artigo [acesso anônimo a BLOBs](../storage/blobs/storage-manage-access-to-resources.md) para saber mais sobre como gerenciar o acesso aos seus dados de BLOB.

### <a name="storage-container-name"></a>Nome do contêiner de armazenamento

Em vez de configurar e criar uma URL SAS, você pode usar o nome do seu contêiner de armazenamento do Azure para acessar seus dados de BLOB. O contêiner de armazenamento usado precisa estar na conta de armazenamento do Azure que está vinculada à sua conta do lote, conhecida como a conta de armazenamento. Usar o nome do contêiner de armazenamento de uma conta de autoarmazenamento permite ignorar a configuração e a criação de uma URL SAS para acessar um contêiner de armazenamento.

Neste exemplo, vamos supor que os dados a serem usados para a criação do arquivo de recurso já estejam em uma conta de armazenamento do Azure vinculada à sua conta do lote. Se você não tiver uma conta de armazenamento, consulte as etapas em [criar uma conta do lote](batch-account-create-portal.md) para obter detalhes sobre como criar e vincular uma conta.

Usando uma conta de armazenamento vinculada, você não precisa criar e configurar uma URL SAS para um contêiner de armazenamento. Em vez disso, forneça o nome do contêiner de armazenamento em sua conta de armazenamento vinculada.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Ponto de extremidade da Web

Os dados que não são carregados no armazenamento do Azure ainda podem ser usados para criar arquivos de recursos. Você pode especificar qualquer URL HTTP válida contendo seus dados de entrada. A URL é fornecida para a API do lote e, em seguida, os dados são usados para criar um arquivo de recurso.

No exemplo a C# seguir, os dados de entrada são hospedados no ponto de extremidade do GitHub fictício. A API recupera o arquivo do ponto de extremidade da Web válido e gera um arquivo de recurso a ser consumido por sua tarefa. Nenhuma credencial é necessária para este cenário.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Dicas e sugestões

Cada tarefa do lote do Azure usa arquivos de maneira diferente, e é por isso que o lote tem opções disponíveis para gerenciar arquivos em tarefas. Os cenários a seguir não devem ser abrangentes, mas em vez disso, aborde algumas situações comuns e forneça recomendações.

### <a name="many-resource-files"></a>Muitos arquivos de recurso

O trabalho do lote pode conter várias tarefas que usam os mesmos arquivos comuns. Se arquivos de tarefas comuns forem compartilhados entre muitas tarefas, usar um pacote de aplicativos para conter os arquivos em vez de usar arquivos de recursos pode ser uma opção melhor. Os pacotes de aplicativos fornecem otimização para a velocidade de download. Além disso, os dados em pacotes de aplicativos são armazenados em cache entre as tarefas, portanto, se os arquivos de tarefas não forem alterados com frequência, os pacotes de aplicativos poderão ser uma boa opção para sua solução. Com os pacotes de aplicativos, você não precisa gerenciar manualmente vários arquivos de recursos ou gerar URLs SAS para acessar os arquivos no armazenamento do Azure. O lote funciona em segundo plano com o armazenamento do Azure para armazenar e implantar pacotes de aplicativos em nós de computação.

Se cada tarefa tiver muitos arquivos exclusivos para essa tarefa, os arquivos de recursos serão, na maioria das vezes, a melhor opção. As tarefas que usam arquivos exclusivos geralmente precisam ser atualizadas ou substituídas, o que não é tão fácil com o conteúdo de pacotes de aplicativos. Os arquivos de recurso fornecem flexibilidade adicional para atualizar, adicionar ou editar arquivos individuais.

### <a name="number-of-resource-files-per-task"></a>Número de arquivos de recurso por tarefa

Se houver várias centenas de arquivos de recursos especificados em uma tarefa, o lote poderá rejeitar a tarefa para que ela seja muito grande. É melhor manter suas tarefas pequenas minimizando o número de arquivos de recursos na própria tarefa.

Se não houver como minimizar o número de arquivos de que sua tarefa precisa, você poderá otimizar a tarefa criando um único arquivo de recurso que faça referência a um contêiner de armazenamento de arquivos de recursos. Para fazer isso, coloque os arquivos de recurso em um contêiner de armazenamento do Azure e use os diferentes modos de "contêiner" dos arquivos de recurso. Use as opções de prefixo de BLOB para especificar coleções de arquivos a serem baixados para suas tarefas.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [pacotes de aplicativos](batch-application-packages.md) como uma alternativa aos arquivos de recursos.
- Para obter mais informações sobre como usar contêineres para arquivos de recursos, consulte [cargas de trabalho de contêiner](batch-docker-container-workloads.md).
- Para saber como reunir e salvar os dados de saída de suas tarefas, consulte [manter saída de trabalho e tarefa](batch-task-output.md).
- Saiba mais sobre as [Ferramentas e APIs do Lote](batch-apis-tools.md) disponíveis para a criação de soluções do Lote.
