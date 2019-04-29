---
title: Criar e usar arquivos de recurso - lote do Azure | Microsoft Docs
description: Saiba como criar arquivos de recursos do lote do Azure de várias fontes de entrada.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: 679a1c60e44694bde86cafba21d7f1d2c6fb94d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616544"
---
# <a name="creating-and-using-resource-files"></a>Criar e usar arquivos de recurso

Uma tarefa de lote do Azure geralmente requer alguma forma de dados a serem processados. Arquivos de recurso são os meios para fornecer esses dados à sua máquina de virtual (VM) do lote por meio de uma tarefa. Todos os tipos de tarefas oferece suporte a arquivos de recurso: Iniciar de tarefas, tarefas, tarefas de preparação de trabalho, tarefas de liberação de trabalho, etc. Este artigo aborda alguns métodos comuns sobre como criar arquivos de recurso e colocá-los em uma máquina virtual.  

Arquivos de recurso são um mecanismo para colocar dados em uma VM em lote, mas o tipo de dados e como ela é usada é flexível. No entanto, há alguns casos de uso comuns:

1. Arquivos comuns de provisionar em cada máquina virtual usando arquivos de recurso em uma tarefa de inicialização
1. Provisionar os dados de entrada a ser processado por tarefas

Arquivos comuns pode ser, por exemplo, arquivos em uma tarefa de inicialização usado para instalar aplicativos que suas tarefas executam. Dados de entrada pode ser qualquer informação a ser processadas por lote ou dados de vídeo ou imagem bruta.

## <a name="types-of-resource-files"></a>Tipos de arquivos de recurso

Há algumas opções diferentes disponíveis para gerar arquivos de recurso. O processo de criação de arquivos de recurso varia dependendo de onde os dados originais são armazenados.

Opções para a criação de um arquivo de recurso:

- [URL do contêiner de armazenamento](#storage-container-url): Gera um arquivo de recurso de qualquer contêiner de armazenamento no Azure
- [Nome do contêiner de armazenamento](#storage-container-name): Gera um arquivo de recurso do nome de um contêiner em uma conta de armazenamento do Azure vinculada ao lote
- [Ponto de extremidade Web](#web-endpoint): Gera um arquivo de recurso de qualquer URL HTTP válida

### <a name="storage-container-url"></a>URL do contêiner de armazenamento

Usando uma URL de contêiner do armazenamento significa que você pode acessar arquivos em qualquer contêiner de armazenamento no Azure. Com as permissões corretas

Neste C# exemplo, os arquivos já foram carregados em um contêiner de armazenamento do Azure como armazenamento de BLOBs. Para acessar os dados necessários para criar um arquivo de recurso, primeiro precisamos obter acesso ao contêiner de armazenamento.

Crie uma assinatura de acesso compartilhado (SAS) URI com as permissões corretas para acessar o contêiner de armazenamento. Defina o tempo de expiração e as permissões para a SAS. Nesse caso, nenhuma hora de início for especificada, para que a SAS se torna válida imediatamente e expira duas horas após ele ser gerado.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Para acesso ao contêiner, você deve ter `Read` e `List` permissões, enquanto com acesso de blob, você só precisa `Read` permissão.

Depois que as permissões forem configuradas, crie o token SAS e formate a URL de SAS para acessar o contêiner de armazenamento. Usando a URL de SAS formatada para o contêiner de armazenamento, gere um arquivo de recurso com [ `FromStorageContainerUrl` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

É uma alternativa para gerar uma URL SAS habilitar o acesso de leitura anônimo, público para um contêiner e seus blobs no armazenamento de BLOBs do Azure. Ao fazer isso, você pode conceder acesso somente leitura a esses recursos sem compartilhar sua chave de conta e sem a necessidade de uma SAS. Acesso de leitura público é normalmente usado para cenários onde você deseja que determinados blobs sempre estejam disponíveis para acesso de leitura anônimo. Se esse cenário é adequado para sua solução, consulte a [acesso anônimo aos blobs](../storage/blobs/storage-manage-access-to-resources.md) artigo para saber mais sobre como gerenciar o acesso aos seus dados de blob.

### <a name="storage-container-name"></a>Nome do contêiner de armazenamento

Em vez de configurar e criar uma URL SAS, você pode usar o nome do seu contêiner de armazenamento do Azure para acessar seus dados de blob. O contêiner de armazenamento usada precisa na conta de armazenamento do Azure que esteja vinculada à sua conta do lote, conhecida como a conta de armazenamento automático. Usar o nome do contêiner de armazenamento de uma conta de armazenamento automático permite que você ignore a configuração e criação de uma URL de SAS para acessar um contêiner de armazenamento.

Neste exemplo, vamos supor que os dados a ser usado para a criação do arquivo de recurso já estão em uma conta de armazenamento do Azure vinculada a sua conta do lote. Se você não tiver uma conta de armazenamento automático, consulte as etapas em [criar uma conta do lote](batch-account-create-portal.md) para obter detalhes sobre como criar e vincular uma conta.

Usando uma conta de armazenamento vinculada, você não precisa criar e configurar uma URL SAS para um contêiner de armazenamento. Em vez disso, forneça o nome do contêiner de armazenamento em sua conta de armazenamento vinculada.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Ponto de extremidade da Web

Dados que não são carregados no armazenamento do Azure ainda podem ser usados para criar arquivos de recurso. Você pode especificar qualquer URL HTTP válida que contém seus dados de entrada. A URL é fornecida para a API do lote e, em seguida, os dados são usados para criar um arquivo de recurso.

A seguir C# exemplo, os dados de entrada é hospedado no ponto de extremidade de GitHub fictício. A API recupere o arquivo do ponto de extremidade web válida e gera um arquivo de recurso a ser consumido por sua tarefa. Nenhuma credencial é necessária para este cenário.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Dicas e sugestões

Cada tarefa de lote do Azure usa arquivos de forma diferente, que é o motivo pelo qual o lote tem as opções disponíveis para gerenciar arquivos em tarefas. Os cenários a seguir não devem ser abrangente, mas em vez disso, cobrir algumas situações comuns e fornecer recomendações.

### <a name="many-resource-files"></a>Muitos arquivos de recurso

O trabalho do lote pode conter várias tarefas que usam os mesmos arquivos comuns. Se os arquivos de tarefas comuns são compartilhados entre várias tarefas, usando um pacote de aplicativo para conter os arquivos em vez de usar arquivos de recurso pode ser uma opção melhor. Pacotes de aplicativos fornecem otimização para velocidade de download. Além disso, os dados em pacotes de aplicativo é armazenado em cache entre tarefas, portanto, se seus arquivos de tarefa não alteram muitas vezes, os pacotes de aplicativos podem ser uma boa opção para sua solução. Com pacotes de aplicativos, você não precisa gerenciar vários arquivos de recursos manualmente ou gerar as URLs de SAS para acessar os arquivos no armazenamento do Azure. O lote funciona em segundo plano com o armazenamento do Azure para armazenar e implantar pacotes de aplicativos em nós de computação.

Se cada tarefa tem muitos arquivos exclusivos para essa tarefa, os arquivos de recurso mais prováveis são a melhor opção. Tarefas que usam arquivos exclusivos geralmente precisam ser atualizados ou substituídos, que não é tão fácil fazer com o conteúdo de pacotes de aplicativo. Arquivos de recurso fornecem flexibilidade adicional para atualizar, adicionar ou editar arquivos individuais.

### <a name="number-of-resource-files-per-task"></a>Número de arquivos de recurso por tarefa

Se houver vários arquivos de centenas de recursos especificados em uma tarefa, o lote poderá rejeitar a tarefa para que ele está muito grande. É melhor manter suas tarefas pequeno, minimizando o número de arquivos de recurso em que a própria tarefa.

Se não houver nenhuma maneira de minimizar o número de arquivos de sua tarefa necessidades, você pode otimizar a tarefa criação de um único arquivo de recurso que faz referência a um contêiner de armazenamento de arquivos de recurso. Para fazer isso, coloque os arquivos de recurso em um contêiner de armazenamento do Azure e usar os diferentes modos de "Contêiner" de arquivos de recurso. Use as opções de prefixo de blob para especificar as coleções de arquivos a ser baixado para suas tarefas.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [pacotes de aplicativos](batch-application-packages.md) como uma alternativa para arquivos de recurso.
- Para obter mais informações sobre como usar contêineres para arquivos de recurso, consulte [cargas de trabalho de contêiner](batch-docker-container-workloads.md).
- Para saber como obter e salvar os dados de saída de suas tarefas, consulte [manter a saída de trabalho e tarefa](batch-task-output.md).
- Saiba mais sobre as [Ferramentas e APIs do Lote](batch-apis-tools.md) disponíveis para a criação de soluções do Lote.