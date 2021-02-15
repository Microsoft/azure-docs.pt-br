---
title: Criar e usar arquivos de recurso
description: Saiba como criar arquivos de recurso do Lote a partir de várias fontes de entrada. Este artigo aborda alguns métodos comuns sobre como criar e colocar arquivos de recursos em uma VM.
ms.date: 03/18/2020
ms.topic: how-to
ms.openlocfilehash: 84a5e9780b4fa0abfec5b736e04d385f14716873
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109282"
---
# <a name="creating-and-using-resource-files"></a>Criar e usar arquivos de recurso

Uma tarefa do Lote do Azure geralmente requer que alguma forma de dados seja processada. Os arquivos de recurso são a maneira de fornecer esses dados para a VM (máquina virtual) do Lote por meio de uma tarefa. Todos os tipos de tarefas dão suporte a arquivos de recurso: tarefas, tarefas de início, tarefas de preparação de trabalho, tarefas de liberação de trabalho etc. Este artigo aborda alguns métodos comuns sobre como criar arquivos de recursos e colocá-los em uma VM.  

Os arquivos de recurso colocam dados em uma VM no Lote, mas o tipo de dados e como ele é usado é flexível. No entanto, há alguns casos de uso comuns:

1. Provisionar arquivos comuns em cada VM usando arquivos de recurso em uma tarefa de início
1. Provisionar dados de entrada a serem processados por tarefas

Os arquivos comuns podem ser, por exemplo, arquivos em uma tarefa de início usada para instalar aplicativos executados por suas tarefas. Os dados de entrada podem ser dados brutos de imagem ou de vídeo, bem como qualquer informação a ser processada pelo Lote.

## <a name="types-of-resource-files"></a>Tipos de arquivos de recurso

Há algumas opções diferentes disponíveis para gerar arquivos de recursos. O processo de criação de arquivos de recursos varia dependendo de onde os dados originais estão armazenados.

Opções para criar um arquivo de recurso:

- [URL do contêiner de armazenamento](#storage-container-url): gera um arquivo de recurso de qualquer contêiner de armazenamento no Azure
- [Nome do contêiner de armazenamento](#storage-container-name): gera um arquivo de recurso do nome de um contêiner em uma conta de armazenamento do Azure vinculada ao Lote
- [Ponto de extremidade da Web](#web-endpoint): gera um arquivo de recurso de qualquer URL HTTP válida

### <a name="storage-container-url"></a>URL do contêiner de armazenamento

Usar uma URL de contêiner de armazenamento significa que, com as permissões corretas, você pode acessar arquivos em qualquer contêiner de armazenamento no Azure. 

Neste exemplo de C#, os arquivos já foram carregados em um contêiner de armazenamento do Azure como armazenamento de Blobs. Para acessar os dados necessários para criar um arquivo de recurso, primeiro precisamos obter acesso ao contêiner de armazenamento.

Crie um URI de SAS (assinatura de acesso compartilhado ) com as permissões corretas para acessar o contêiner de armazenamento. Defina o tempo de expiração e as permissões para a SAS. Nesse caso, nenhuma hora de início é especificada. Portanto, a SAS se torna válida imediatamente e expira duas horas após sua geração.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Para acesso ao contêiner, você deve ter as permissões `Read` e `List`. Já para acessar o Blob, você precisa apenas da permissão `Read`.

Depois que as permissões forem configuradas, crie o token SAS e formate a URL de SAS para acesso ao contêiner de armazenamento. Usando a URL de SAS formatada para o contêiner de armazenamento, gere um arquivo de recurso com [`FromStorageContainerUrl`](/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Uma alternativa para gerar uma URL de SAS é habilitar o acesso de leitura pública anônima para um contêiner e seus blobs no armazenamento de Blobs do Azure. Ao fazer isso, você poderá conceder acesso somente leitura a esses recursos sem compartilhar a chave da conta nem exigir uma SAS. O acesso de leitura público é geralmente usado para cenários em que você quer que determinados blobs estejam sempre disponíveis para acesso de leitura anônimo. Se esse cenário se adequar à sua solução, confira o artigo [Acesso anônimo a blobs](../storage/blobs/anonymous-read-access-configure.md) para saber mais sobre como gerenciar o acesso aos seus dados de blob.

### <a name="storage-container-name"></a>Nome do contêiner de armazenamento

Em vez de configurar e criar uma URL de SAS, você pode usar o nome do seu contêiner de armazenamento do Azure para acessar os dados de blob. O contêiner de armazenamento que você usa deve estar na conta de armazenamento do Azure vinculada à sua conta do Lote. Essa conta de armazenamento é conhecida como a conta de armazenamento automático. O uso do contêiner de armazenamento automático permite ignorar a configuração e criação de uma URL de SAS para acessar um contêiner de armazenamento.

Neste exemplo, vamos supor que os dados a serem usados para a criação do arquivo de recurso já estejam em uma conta de Armazenamento do Azure vinculada à sua conta do Lote. Se você não tiver uma conta de armazenamento automático, confira as etapas em [Criar uma conta do Lote](batch-account-create-portal.md) para obter detalhes sobre como criar e vincular uma conta.

Ao usar uma conta de armazenamento vinculada, você não precisa criar e configurar uma URL de SAS para um contêiner de armazenamento. Em vez disso, forneça o nome do contêiner de armazenamento em sua conta de armazenamento vinculada.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Ponto de extremidade da Web

Os dados que não são carregados no Armazenamento do Azure ainda podem ser usados para criar arquivos de recurso. Você pode especificar qualquer URL HTTP válida que contém seus dados de entrada. A URL é fornecida para a API do Lote e, em seguida, os dados são usados para criar um arquivo de recurso.

No seguinte exemplo de C#, os dados de entrada são hospedados em um ponto de extremidade GitHub fictício. A API recupera o arquivo do ponto de extremidade da Web válido e gera um arquivo de recurso a ser consumido pela tarefa. Nenhuma credencial é necessária para este cenário.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Dicas e sugestões

Cada tarefa do Lote do Azure usa arquivos de maneira diferente. É por isso que o Lote tem opções disponíveis para o gerenciamento de arquivos em tarefas. Os cenários a seguir não pretendem ser abrangentes. Em vez disso, abordam algumas situações comuns e fornecem recomendações.

### <a name="many-resource-files"></a>Muitos arquivos de recurso

O trabalho do Lote pode conter várias tarefas que usam os mesmos arquivos comuns. Se arquivos de tarefas comuns forem compartilhados entre muitas tarefas, usar um pacote de aplicativos para incluir os arquivos em vez de usar arquivos de recurso poderá ser uma opção melhor. Os pacotes de aplicativos fornecem otimização para a velocidade de download. Além disso, os dados nos pacotes de aplicativos são armazenados em cache entre as tarefas. Portanto, se os arquivos de tarefas não forem alterados com frequência, os pacotes de aplicativos poderão ser uma boa opção para sua solução. Com os pacotes de aplicativos, você não precisa gerenciar manualmente vários arquivos de recurso nem gerar URLs de SAS para acessar os arquivos no Armazenamento do Azure. O Lote funciona em segundo plano com o Armazenamento do Azure para armazenar e implantar os pacotes de aplicativos nos nós de computação.

Se cada tarefa tiver muitos arquivos exclusivos para essa tarefa, os arquivos de recursos serão a melhor opção, pois as tarefas que usam arquivos exclusivos geralmente precisam ser atualizadas ou substituídas, o que não é tão fácil com o conteúdo dos pacotes de aplicativos. Os arquivos de recurso fornecem flexibilidade adicional para atualizar, adicionar ou editar arquivos individuais.

### <a name="number-of-resource-files-per-task"></a>Número de arquivos de recurso por tarefa

Se houver várias centenas de arquivos de recurso especificados em uma tarefa, o Lote poderá rejeitar a tarefa por ser muito grande. É melhor manter suas tarefas em tamanho pequeno, minimizando o número de arquivos de recurso na própria tarefa.

Se não houver como reduzir o número de arquivos de que sua tarefa precisa, você poderá otimizar a tarefa criando um único arquivo de recurso que faça referência a um contêiner de armazenamento de arquivos de recurso. Para isso, coloque os arquivos de recurso em um contêiner de Armazenamento do Azure e use os [métodos](/dotnet/api/microsoft.azure.batch.resourcefile#methods) de "contêiner" para os arquivos de recurso. Use as opções de prefixo de blob a fim de especificar coleções de arquivos a serem baixadas para suas tarefas.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [pacotes de aplicativos](batch-application-packages.md) como uma alternativa aos arquivos de recurso.
- Para obter mais informações sobre como usar contêineres para arquivos de recurso, confira [Cargas de trabalho do contêiner](batch-docker-container-workloads.md).
- Para saber como reunir e salvar os dados de saída de suas tarefas, confira [Persistir saída de tarefa e de trabalho](batch-task-output.md).
- Saiba mais sobre as [Ferramentas e APIs do Lote](batch-apis-tools.md) disponíveis para a criação de soluções do Lote.