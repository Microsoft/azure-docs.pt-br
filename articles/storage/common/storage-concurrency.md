---
title: Gerenciando a simultaneidade no Armazenamento do Microsoft Azure
description: Como gerenciar a simultaneidade para os serviços Blob, Fila, Tabela e Arquivo
services: storage
author: jasontang501
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 427cc34cc5a2801a2da98259f932678cdcf71ef7
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67870823"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Gerenciando a simultaneidade no Armazenamento do Microsoft Azure
## <a name="overview"></a>Visão geral
Os aplicativos baseados na Internet modernos consistem normalmente em vários usuários exibindo e atualizando dados de forma simultânea. Isso exige que os desenvolvedores de aplicativos pensem cuidadosamente sobre como fornecer uma experiência previsível para os usuários finais, especialmente para cenários em que vários usuários podem atualizar os mesmos dados. Existem três estratégias de simultaneidade de dados principais que os desenvolvedores normalmente consideram:  

1. Simultaneidade otimista: um aplicativo realizando uma atualização verificará, como parte da atualização, se os dados foram alterados desde a última vez que o aplicativo leu tais dados. Por exemplo, se dois usuários visualizando uma página wiki fazem uma atualização na mesma página, a plataforma wiki deve garantir que a segunda atualização não substitua a primeira e que os dois usuários entendam se suas respectivas atualizações foram bem-sucedidas ou não. Essa estratégia é usada com mais frequência em aplicativos Web.
2. Simultaneidade pessimista: um aplicativo procurando realizar uma atualização bloqueará um objeto, impedindo que outros usuários atualizem os dados até que o bloqueio seja liberado. Por exemplo, em um cenário de replicação de dados mestre/subordinado em que somente o mestre executará atualizações, o mestre normalmente manterá um bloqueio exclusivo por um período de tempo estendido nos dados para garantir que ninguém mais possa atualizá-lo.
3. Último a gravar vence: uma abordagem que permite que quaisquer operações de atualização prossigam sem verificar se algum outro aplicativo atualizou os dados desde que o aplicativo leu os dados pela primeira vez. Essa estratégia (ou falta de uma estratégia formal) normalmente é usada em locais em que os dados estão particionados de tal forma que não há probabilidade de vários usuários acessarem os mesmos dados. Ela também pode ser útil em locais em que fluxos de dados de curta duração estão sendo processados.  

Este artigo fornece uma visão geral de como a plataforma de Armazenamento do Azure simplifica o desenvolvimento fornecendo suporte de primeira classe para essas três estratégias de simultaneidade.  

## <a name="azure-storage--simplifies-cloud-development"></a>Armazenamento do Azure: simplifica o desenvolvimento na nuvem
O serviço de Armazenamento do Azure oferece suporte para as três estratégias, embora seja distinto em sua capacidade de fornecer suporte completo para a simultaneidade otimista e pessimista, pois foi projetado para englobar um modelo de consistência sólido, o qual garante que quando o serviço de Armazenamento confirma uma operação de atualização ou inserção de dados, todos os acessos adicionais a tais dados possam ver a atualização mais recente. As plataformas de armazenamento que usam um modelo de consistência eventual apresentam um retardo entre o momento em que uma gravação é realizada por um usuário e o momento em que os dados atualizados podem ser visualizados por outros usuários, o que complica o desenvolvimento de aplicativos cliente para evitar que as inconsistências afetem os usuários finais.  

Além de selecionar uma estratégia de simultaneidade adequada, os desenvolvedores também devem estar cientes de como uma plataforma de armazenamento isola as alterações, especialmente as alterações no mesmo objeto entre as transações. O serviço de Armazenamento do Azure usa o isolamento de instantâneo para permitir que as operações de leitura ocorram simultaneamente às operações de gravação em uma única partição. Diferente de outros níveis de isolamento, o isolamento de instantâneo garante que todas as leituras vejam um instantâneo consistente dos dados mesmo durante atualizações, retornando basicamente os últimos valores confirmados enquanto uma transação de atualização é processada.  

## <a name="managing-concurrency-in-blob-storage"></a>Gerenciando Simultaneidade em Armazenamento de Blobs
Você pode optar por usar os modelos de simultaneidade otimista ou pessimista para gerenciar o acesso a blobs e contêineres no serviço Blob. Se você não especificar explicitamente uma estratégia, o padrão será último a gravar vence.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Simultaneidade otimista para blobs e contêineres
O serviço de Armazenamento atribui um identificador a todo objeto armazenado. Esse identificador é atualizado sempre que uma operação de atualização é realizada em um objeto. O identificador é retornado para o cliente como parte de uma resposta de HTTP GET usando o cabeçalho ETag (marca de entidade) que está definido no protocolo HTTP. Um usuário realizando uma atualização em tal objeto pode enviar a Etag original juntamente com um cabeçalho condicional para garantir que a atualização ocorra apenas se uma determinada condição for atendida; nesse caso, a condição é um cabeçalho "If-Match", que exige que o Serviço de Armazenamento garanta que o valor de Etag especificado na solicitação de atualização seja igual ao armazenado no Serviço de Armazenamento.  

A estrutura desse processo é a seguinte:  

1. Recupere um blob do serviço de Armazenamento, a resposta inclui um valor de cabeçalho HTTP ETag que identifica a versão atual do objeto no serviço de Armazenamento.
2. Ao atualizar o blob, inclua o valor de ETag recebido na etapa 1 no cabeçalho condicional **If-Match** da solicitação enviada para o serviço.
3. O serviço compara o valor da ETag na solicitação com o valor da ETag atual do blob.
4. Se o valor da ETag atual do blob for uma versão diferente da ETag no cabeçalho condicional **If-Match** na solicitação, o serviço retornará um erro 412 para o cliente. Isso indica para o cliente que outro processo atualizou o blob desde que ele o recuperou.
5. Se o valor atual de ETag do blob for a mesma versão de ETag no cabeçalho condicional **If-Match** na solicitação, o serviço realizará a operação solicitada e atualizará o valor da ETag atual do blob para mostrar que foi criada uma nova versão.  

O snippet de C# a seguir (usando a Client Storage Library 4.2.0) mostra um exemplo simples de como criar um **If-Match AccessCondition** com base no valor da ETag acessado nas propriedades de um blob que foi recuperado ou inserido anteriormente. Ele usa então o objeto **AccessCondition** quando atualiza o blob: o objeto **AccessCondition** adiciona o cabeçalho **If-Match** à solicitação. Se outro processo atualizou o blob, o serviço Blob retorna uma mensagem de status HTTP 412 (Falha de precondição). Você pode baixar o exemplo completo aqui: [Gerenciando a simultaneidade usando o Armazenamento do Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

O serviço de Armazenamento também inclui suporte para cabeçalhos condicionais adicionais, como **If-Modified-Since**, **If-Unmodified-Since** e **If-None-Match**, bem como combinações deles. Para obter mais informações, consulte [Especificando cabeçalhos condicionais para operações de serviço Blob](https://msdn.microsoft.com/library/azure/dd179371.aspx) no MSDN.  

A tabela a seguir resume as operações de contêiner que aceitam cabeçalhos condicionais como **If-Match** na solicitação e retornam um valor de ETag na resposta.  

| Operação | Retorna o valor de ETag do contêiner | Aceita cabeçalhos condicionais |
|:--- |:--- |:--- |
| Create Container |Sim |Não |
| Get Container Properties |Sim |Não |
| Get Container Metadata |Sim |Não |
| Set Container Metadata |Sim |Sim |
| Get Container ACL |Sim |Não |
| Set Container ACL |Sim |Sim (\*) |
| Delete Container |Não |Sim |
| Lease Container |Sim |Sim |
| Listar Blobs |Não |Não |

(\*) As permissões definidas por SetContainerACL são armazenadas em cache e as atualizações dessas permissões levam 30 segundos para serem propagadas, período durante o qual não há garantia de que as atualizações são consistentes.  

A tabela a seguir resume as operações de blob que aceitam cabeçalhos condicionais como **If-Match** na solicitação e retornam um valor de ETag na resposta.

| Operação | Retorna o valor de ETag | Aceita cabeçalhos condicionais |
|:--- |:--- |:--- |
| Put Blob |Sim |Sim |
| Get Blob |Sim |Sim |
| Get Blob Properties |Sim |Sim |
| Set Blob Properties |Sim |Sim |
| Get Blob Metadata |Sim |Sim |
| Set Blob Metadata |Sim |Sim |
| Lease Blob (\*) |Sim |Sim |
| Blob de instantâneo |Sim |Sim |
| Copiar blob |Sim |Sim (para o blob de origem e destino) |
| Anular copiar Blob |Não |Não |
| Delete Blob |Não |Sim |
| Put Block |Não |Não |
| Put Block List |Sim |Sim |
| Get Block List |Sim |Não |
| Put Page |Sim |Sim |
| Get Page Ranges |Sim |Sim |

(\*) Lease Blob não altera a ETag em um blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Simultaneidade pessimista para blobs
Para bloquear um blob para uso exclusivo, é possível obter uma [concessão](https://msdn.microsoft.com/library/azure/ee691972.aspx) sobre ele. Ao adquirir uma concessão, você especifica por quanto tempo precisa dela: esse período pode ser entre 15 e 60 segundos ou infinito, o que resulta em um bloqueio exclusivo. Você pode renovar uma concessão finita para estendê-la e pode liberar qualquer concessão quando terminar de trabalhar com ela. O serviço Blob libera as concessões finitas automaticamente quando elas expiram.  

As concessões permitem que diferentes estratégias de sincronização sejam suportadas, incluindo gravação exclusiva/leitura compartilhada, gravação exclusiva/leitura exclusiva e gravação compartilhada/leitura exclusiva. Nos locais em que há uma concessão, o serviço de Armazenamento impõe gravações exclusivas (operações de exclusão, definição e colocação), mas a garantia de exclusividade para operações de leitura requer que o desenvolvedor garanta que todos os aplicativos cliente usam uma ID de concessão e que apenas um cliente de cada vez tem uma ID de concessão válida. As operações de leitura que não incluem uma ID de concessão resultam em leituras compartilhadas.  

O snippet de C# a seguir mostra um exemplo de aquisição de uma concessão exclusiva por 30 segundos em um blob, atualizando o conteúdo do blob e liberando a concessão. Se já houver uma concessão válida no blob quando você tentar obter uma nova concessão, o serviço Blob retornará um resultado de status "Conflito HTTP (409)". O snippet de código a seguir usa um objeto **AccessCondition** para encapsular as informações da concessão quando ela faz uma solicitação para atualizar o blob no serviço de Armazenamento.  Você pode baixar o exemplo completo aqui: [Gerenciando a simultaneidade usando o Armazenamento do Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}  
```

Se você tentar realizar uma operação de gravação em um blob sob concessão sem enviar a ID de concessão, a solicitação falhará com um erro 412. Observe que se a concessão expirar antes de chamar o método **UploadText**, mas você ainda utilizar a ID de concessão, a solicitação também falhará com um erro **412**. Para obter mais informações sobre o gerenciamento de tempos de expiração de concessão e IDs de concessão, consulte a documentação de REST de [Lease Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx).  

As operações de blob a seguir podem usar concessões para gerenciar a simultaneidade pessimista:  

* Put Blob
* Get Blob
* Get Blob Properties
* Set Blob Properties
* Get Blob Metadata
* Set Blob Metadata
* Delete Blob
* Put Block
* Put Block List
* Get Block List
* Put Page
* Get Page Ranges
* Snapshot Blob - ID de concessão opcional se existir uma concessão
* Copy Blob - ID de concessão obrigatória se existir uma concessão no blob de destino
* Abort Copy Blob - ID de concessão obrigatória se existir uma concessão infinita no blob de destino
* Lease Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Simultaneidade pessimista para contêineres
As concessões em contêineres permitem que as mesmas estratégias de sincronização sejam suportadas como nos blobs (gravação exclusiva/leitura compartilhada, gravação exclusiva/leitura exclusiva e gravação compartilhada/leitura exclusiva), no entanto, diferente dos blobs, o serviço de Armazenamento impõe exclusividade apenas em operações de exclusão. Para excluir um contêiner com uma concessão ativa, o cliente deve incluir a ID da concessão ativa com a solicitação de exclusão. Todas as outras operações de contêiner obtiveram êxito em um contêiner sob concessão sem incluir a ID de concessão, caso em que são operações compartilhadas. Se a exclusividade das operações de leitura ou atualização (colocação ou definição) for obrigatória, os desenvolvedores devem garantir que todos os clientes usem uma ID de concessão e que apenas um cliente de cada vez tenha uma ID de concessão válida.  

As operações de contêiner a seguir podem usar concessões para gerenciar a simultaneidade pessimista:  

* Delete Container
* Get Container Properties
* Get Container Metadata
* Set Container Metadata
* Get Container ACL
* Set Container ACL
* Lease Container  

Para obter mais informações, consulte:  

* [Especificando cabeçalhos condicionais para operações do serviço Blob](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Lease Container](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Blob de concessão](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Gerenciando a simultaneidade no serviço Tabela
O serviço Tabela usa verificações de simultaneidade otimista como o comportamento padrão quando você está trabalhando com entidades, diferente do serviço Blob, em que é necessário escolher explicitamente realizar verificações de simultaneidade otimista. A outra diferença entre os serviços Tabela e Blob é que você pode gerenciar apenas o comportamento de simultaneidade de entidades, enquanto que com o serviço Blob é possível gerenciar a simultaneidade de contêineres e blobs.  

Para usar a simultaneidade otimista e verificar se outro processo modificou uma entidade desde que você a recuperou do serviço de armazenamento de tabela, é possível usar o valor da ETag recebido quando o serviço Tabela retorna uma entidade. A estrutura desse processo é a seguinte:  

1. Recupere uma entidade do serviço de armazenamento de tabela, a resposta inclui um valor de ETag que identifica o identificador atual associado a essa entidade no serviço de Armazenamento.
2. Ao atualizar a entidade, inclua o valor da ETag recebido na etapa 1 no cabeçalho obrigatório **If-Match** da solicitação enviada para o serviço.
3. O serviço compara o valor da ETag na solicitação com o valor da ETag atual da entidade.
4. Se o valor da ETag atual da entidade for diferente da ETag no cabeçalho obrigatório **If-Match** na solicitação, o serviço retornará um erro 412 para o cliente. Isso indica para o cliente que outro processo atualizou a entidade desde que ele a recuperou.
5. Se o valor da ETag atual da entidade for o mesmo da ETag no cabeçalho obrigatório **If-Match** na solicitação ou o cabeçalho **If-Match** contiver o caractere curinga (\*), o serviço realizará a operação solicitada e atualizará o valor da ETag atual da entidade para mostrar que ela foi atualizada.  

Observe que diferente do serviço Blob, o serviço Tabela exige que o cliente inclua um cabeçalho **If-Match** em solicitações de atualização. No entanto, é possível forçar uma atualização incondicional (estratégia último a gravar vence) e ignorar as verificações de simultaneidade se o cliente definir o cabeçalho **If-Match** com o caractere curinga (\*) na solicitação.  

O snippet de C# a seguir mostra uma entidade de cliente que foi criada ou recuperada anteriormente tendo seu endereço de email atualizado. A operação de recuperação ou inserção inicial armazena o valor da ETag no objeto do cliente e, como a amostra usa a mesma instância de objeto ao executar a operação de substituição, ela automaticamente envia o valor da ETag de volta ao serviço Tabela, permitindo que o serviço verifique se há violações de simultaneidade. Se outro processo atualizou a entidade no armazenamento de tabela, o serviço retorna uma mensagem de status HTTP 412 (Falha de precondição).  Você pode baixar o exemplo completo aqui: [Gerenciando a simultaneidade usando o Armazenamento do Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}  
```

Para desabilitar explicitamente a verificação de simultaneidade, você deve definir a propriedade **ETag** do objeto **employee** para "\*" antes de executar a operação de substituição.  

```csharp
customer.ETag = "*";  
```

A tabela a seguir resume como as operações de entidade de tabela usam os valores de ETag:

| Operação | Retorna o valor de ETag | Requer o cabeçalho de solicitação If-Match |
|:--- |:--- |:--- |
| Query Entities |Sim |Não |
| Insert Entity |Sim |Não |
| Update Entity |Sim |Sim |
| Merge Entity |Sim |Sim |
| Delete Entity |Não |Sim |
| Insert or Replace Entity |Sim |Não |
| Insert or Merge Entity |Sim |Não |

Observe que as operações **Insert or Replace Entity** e **Insert or Merge Entity** *não* realizam nenhuma verificação de simultaneidade, pois não enviam um valor de ETag para o serviço Tabela.  

Em geral, os desenvolvedores usando tabelas devem recorrer à simultaneidade otimista ao desenvolver aplicativos escalonáveis. Se o bloqueio pessimista for necessário, uma abordagem que os desenvolvedores podem adotar ao acessar tabelas é atribuir um blob designado para cada tabela e tentar obter uma concessão no blob antes de operar na tabela. Essa abordagem exige que o aplicativo garanta que todos os caminhos de acesso de dados obtenham a concessão antes de operar na tabela. Você também deve observar que o tempo mínimo de concessão é de 15 segundos, o que exige uma consideração cuidadosa quanto à escalabilidade.  

Para obter mais informações, consulte:  

* [Operações em entidades](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Gerenciando a simultaneidade no serviço Fila
Um cenário em que a simultaneidade é uma preocupação no serviço de filas ocorre quando vários clientes recuperam mensagens de uma fila. Quando uma mensagem é recuperada da fila, a resposta inclui a mensagem e um valor de recebimento pop, que é necessário para excluir a mensagem. A mensagem não é automaticamente excluída da fila, mas após ser recuperada, não fica visível para outros clientes pelo intervalo de tempo especificado pelo parâmetro visibilitytimeout. Espera-se que o cliente que recuperou a mensagem a exclua após ela ser processada e antes do tempo especificado pelo elemento TimeNextVisible da resposta, que é calculado com base no valor do parâmetro visibilitytimeout. O valor de visibilitytimeout é adicionado ao horário em que a mensagem é recuperada para determinar o valor de TimeNextVisible.  

O serviço Fila não tem suporte para a simultaneidade pessimista ou otimista e, por essa razão, os clientes que processam mensagens recuperadas de uma fila devem garantir que elas sejam processadas de uma maneira idempotente. A estratégia último a gravar vence é usada para operações de atualização, como SetQueueServiceProperties, SetQueueMetaData, SetQueueACL e UpdateMessage.  

Para obter mais informações, consulte:  

* [API REST do serviço Fila](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Receber mensagens](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Gerenciando a simultaneidade no serviço Arquivo
O serviço Arquivo pode ser acessado usando dois pontos de extremidade de protocolo diferentes: SMB e REST. O serviço REST não tem suporte para o bloqueio otimista ou pessimista e todas as atualizações seguirão a estratégia último a gravar vence. Os clientes SMB que montam compartilhamentos de arquivos podem utilizar os mecanismos de bloqueio do sistema de arquivos para gerenciar o acesso aos arquivos compartilhados, incluindo a capacidade de realizar o bloqueio pessimista. Quando um cliente SMB abre um arquivo, ele especifica o modo de compartilhamento e de acesso do arquivo. Configurar uma opção de Acesso ao Arquivo de “Gravação” ou “Leitura/Gravação” juntamente com um modo de Compartilhamento de Arquivo de “Nenhum” resultará no bloqueio do arquivo por um cliente SMB até o arquivo ser fechado. Se houver a tentativa de realização da operação REST em um arquivo em que um cliente SMB tenha o arquivo bloqueado, o serviço REST retornará o código de status 409 (Conflito) com o código de erro SharingViolation.  

Quando um cliente SMB abre um arquivo para exclusão, ele marca o arquivo como exclusão pendente até que todos os outros identificadores abertos do cliente SMB em tal arquivo sejam fechados. Enquanto um arquivo estiver marcado como com exclusão pendente, todas as operações REST em tal arquivo retornarão o código de status 409 (Conflito) com o código de erro SMBDeletePending. O código de status 404 (Não encontrado) não é retornado, uma vez que é possível que o cliente SMB remova o sinalizador de exclusão pendente antes de fechar o arquivo. Em outras palavras, o código de status 404 (Não encontrado) é esperado apenas se o arquivo tiver sido removido. Observe que enquanto um arquivo estiver em um estado de exclusão pendente de SMB, ele não será incluído nos resultados de Listar Arquivos. Observe também que as operações REST Delete File e REST Delete Directory são confirmadas de forma atômica e não resultam no estado de exclusão pendente.  

Para obter mais informações, consulte:  

* [Gerenciando bloqueios de arquivo](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Resumo e próximas etapas
O serviço de Armazenamento do Microsoft Azure foi desenvolvido para atender às necessidades dos aplicativos online mais complexos sem forçar os desenvolvedores a comprometerem ou repensarem as principais pressuposições de design, como a simultaneidade e a consistência de dados, as quais eles já consideram corriqueiras.  

Para encontrar o aplicativo de amostra completo citado nesse blog:  

* [Gerenciando a simultaneidade usando o Armazenamento do Azure — aplicativo de amostra](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Para obter mais informações sobre Armazenamento do Azure, consulte:  

* [Página inicial do Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/)
* [Introdução ao Armazenamento do Azure](storage-introduction.md)
* Introdução ao Armazenamento para [Blob](../blobs/storage-dotnet-how-to-use-blobs.md), [Tabela](../../cosmos-db/table-storage-how-to-use-dotnet.md), [Filas](../storage-dotnet-how-to-use-queues.md) e [Arquivos](../storage-dotnet-how-to-use-files.md)
* Arquitetura de Armazenamento – [Armazenamento do Azure: Um serviço de armazenamento em nuvem altamente disponível com coerência forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

