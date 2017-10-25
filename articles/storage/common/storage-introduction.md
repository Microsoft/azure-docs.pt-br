---
title: "Introdução ao Armazenamento do Azure | Microsoft Docs"
description: "Introdução ao Armazenamento do Azure, armazenamento de dados da Microsoft na nuvem."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/09/2017
ms.author: tamram
ms.openlocfilehash: e7b32aa2de5d6501e8d7894a936e9ab8b2f4f42f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-microsoft-azure-storage"></a>Introdução ao Armazenamento do Microsoft Azure

Armazenamento do Microsoft Azure é um serviço de nuvem gerenciado pela Microsoft que fornece armazenamento altamente disponível, seguro, durável, escalonável e redundante. A Microsoft cuida da manutenção e lida com problemas importantes para você. 

O Armazenamento do Azure consiste em três serviços de dados: armazenamento de Blobs, armazenamento de Arquivos e armazenamento de Filas. O armazenamento de blobs dá suporte ao armazenamento standard e premium, com o armazenamento premium usando somente SSDs para que o desempenho seja o mais rápido possível. Outro recurso é o armazenamento frio, que permite que você armazene grandes quantidades de dados raramente acessados para reduzir o custo.

Neste artigo, você aprenderá sobre o seguinte:
* os serviços de Armazenamento do Azure
* os tipos de contas de armazenamento
* acessando seus blobs, filas e arquivos
* criptografia
* replicação 
* transferência de dados para dentro ou fora do armazenamento
* as várias bibliotecas de clientes de armazenamento disponíveis. 

Para colocar o Armazenamento do Azure em funcionamento rapidamente, consulte um dos Inícios Rápidos a seguir:
* [Criar uma conta de armazenamento usando o PowerShell](storage-quickstart-create-storage-account-powershell.md)
* [Criar uma conta de armazenamento usando a CLI](storage-quickstart-create-storage-account-cli.md)

## <a name="introducing-the-azure-storage-services"></a>Introdução aos serviços de Armazenamento do Azure

Para usar qualquer um dos serviços fornecidos pelo Armazenamento do Azure – armazenamento de Blobs, armazenamento de Arquivos e armazenamento de Filas -- primeiro crie uma conta de armazenamento e, em seguida, você pode transferir dados para/de um serviço específico nessa conta de armazenamento. 

## <a name="blob-storage"></a>Armazenamento de blob

Blobs são basicamente arquivos, como aqueles que você armazena no seu computador (ou tablet, dispositivo móvel e assim por diante). Eles podem ser imagens, arquivos do Microsoft Excel, arquivos HTML, discos rígidos virtuais (VHDs), big data como logs, backups de banco de dados – basicamente tudo. Blobs são armazenados em contêineres, que são semelhantes às pastas. 

Depois de armazenar arquivos no armazenamento de Blobs, você pode acessá-los de qualquer lugar no mundo usando URLs, a interface REST ou uma das bibliotecas de cliente de armazenamento do Azure SDK. As bibliotecas de clientes de armazenamento estão disponíveis para várias linguagens, incluindo Node.js, Java, PHP, Ruby, Python e .NET. 

Há três tipos de blobs – blobs de bloco, blobs de página (usados para arquivos VHD) e blobs de acréscimo.

* Blobs de blocos são usados para armazenar arquivos comuns de até aproximadamente 4.7 TB. 
* Blobs de páginas são usados para manter arquivos de acesso aleatório de até 8 TB de tamanho. Eles são usados para os arquivos VHD que dão suporte a máquinas virtuais.
* Blobs de acréscimo são compostos de blocos como blobs de blocos, mas são otimizados para operações de acréscimo. Eles são usados para tarefas como registrar em log informações para o mesmo blob a partir de várias VMs.

Para grandes conjuntos de dados onde as restrições de rede fazem com que o carregamento ou download de dados para o armazenamento de Blobs seja virtualmente impossível, você pode enviar um conjunto de discos rígidos para a Microsoft para importar ou exportar dados diretamente a partir do data center. Confira [Usar o serviço de Importação/Exportação do Microsoft Azure para transferir dados para o Armazenamento de Blobs](../storage-import-export-service.md).

## <a name="azure-files"></a>Arquivos do Azure
Os [Arquivos do Azure](../files/storage-files-introduction.md) permite que você configure compartilhamentos de arquivo por rede altamente disponíveis que podem ser acessados usando o protocolo SMB (Server Message Block) padrão. Isso significa que várias VMs podem compartilhar os mesmos arquivos com acesso de leitura e gravação. Você também pode ler os arquivos usando a interface REST ou as bibliotecas de cliente de armazenamento. 

Uma coisa que diferencia os Arquivos do Azure dos arquivos em um compartilhamento de arquivos corporativo é a capacidade de acessar os arquivos de qualquer lugar do mundo usando uma URL que aponte para o arquivo, e que inclua um token SAS (Assinatura de Acesso Compartilhado). Você pode gerar tokens SAS; eles permitem o acesso específico a um ativo privado durante período especificado. 

Os compartilhamentos de arquivos podem ser usados para muitos cenários comuns: 

* Muitos aplicativos locais usam compartilhamentos de arquivos. Esse recurso facilita a migração dos aplicativos que compartilham dados com o Azure. Se você montar o compartilhamento de arquivos na mesma letra de unidade usada pelo aplicativo local, a parte de seu aplicativo que acessa o compartilhamento de arquivos deverá funcionar com o mínimo de, ou nenhuma, alteração.

* Os arquivos de configuração podem ser armazenados em um compartilhamento de arquivos e acessados de várias VMs. As ferramentas e utilitários usados por vários desenvolvedores em um grupo podem ser armazenados em um compartilhamento de arquivos, garantindo que todas as pessoas possam encontrá-los, e que usem a mesma versão.

* Logs de diagnóstico, métricas e despejos de memória são apenas três exemplos de dados que podem ser gravados em um compartilhamento de arquivos e processados ou analisados posteriormente.

No momento, a autenticação baseada no Active Directory e as ACLs (Listas de controle de acesso) não têm suporte, mas terão em algum momento futuro. As credenciais da conta de armazenamento são usadas para fornecer a autenticação para acesso ao compartilhamento de arquivos. Isso significa que qualquer pessoa com o compartilhamento montado terá acesso completo de leitura/gravação no compartilhamento.

## <a name="queue-storage"></a>Armazenamento de filas

O serviço Fila do Azure é usado para armazenar e recuperar mensagens. A fila de mensagens pode ser de até 64 KB de tamanho e uma fila pode conter milhões de mensagens. Filas são geralmente usadas para armazenar as listas de mensagens a serem processadas de forma assíncrona. 

Por exemplo, digamos que você deseja que seus clientes possam carregar imagens e você deseja criar miniaturas para cada imagem. Você pode fazer com que o cliente aguarde que você crie miniaturas ao carregar as imagens. Uma alternativa seria usar uma fila. Quando o cliente conclui o carregamento, grave uma mensagem na fila. Em seguida, faça com que uma Função do Azure recupere a mensagem da fila e crie as miniaturas. Cada uma das partes desse processamento pode ser dimensionada separadamente oferecendo mais controle durante o ajuste para seu uso.

## <a name="table-storage"></a>Armazenamento de tabela

O Armazenamento de Tabelas do Azure Standard agora é parte do Cosmos DB. Para ver essa documentação, veja a [Visão geral do Armazenamento de Tabelas do Azure](../../cosmos-db/table-storage-overview.md). As Tabelas Premium também estão disponíveis para o Armazenamento de Tabelas do Azure, oferecendo tabelas otimizadas para taxa de transferência, distribuição global e índices secundários automáticos. Para saber mais e experimentar a nova experiência premium, confira [Azure Cosmos DB: API de Tabela](https://aka.ms/premiumtables).

## <a name="disk-storage"></a>Armazenamento em disco

O Armazenamento do Azure também inclui recursos de disco gerenciados e usados por máquinas virtuais. Para obter mais informações sobre esses recursos, consulte a [documentação do Serviço de Computação](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento 

Esta tabela mostra os vários tipos de contas de armazenamento e os objetos que podem ser usados com cada uma.

|**Tipo de conta de armazenamento**|**Standard de uso geral**|**Premium de uso geral**|**Armazenamento de blobs, níveis de acesso quente e frio**|
|-----|-----|-----|-----|
|**Serviços compatíveis**| Serviços de Filas, Arquivos e Blobs | Serviço Blob | Serviço Blob|
|**Tipos de blobs compatíveis**|Blobs de blocos, blobs de páginas e blobs de acréscimo | Blobs de página | Blobs de blocos e blobs de acréscimo|

### <a name="general-purpose-storage-accounts"></a>Contas de armazenamento de uso geral

Há dois tipos de contas de armazenamento de uso geral. 

#### <a name="standard-storage"></a>Armazenamento Standard 

As contas de armazenamento mais usadas são as contas de armazenamento standard, que podem ser usadas para todos os tipos de dados. As contas de armazenamento standard usam mídia magnética para armazenar dados.

#### <a name="premium-storage"></a>Armazenamento Premium

O armazenamento Premium fornece armazenamento de alto desempenho para blobs de páginas, que são usados principalmente para arquivos VHD. As contas de armazenamento Premium usam SSD para armazenar dados. A Microsoft recomenda usar o Armazenamento Premium para todas as suas VMs.

### <a name="blob-storage-accounts"></a>Contas de Armazenamento de Blobs

A conta de Armazenamento de Blobs é uma conta de armazenamento especializada usada para armazenar blobs de blocos e blobs de acréscimo. Não é possível armazenar blobs de páginas nessas contas, portanto você não pode armazenar arquivos VHD. Essas contas permitem que você defina uma camada de acesso Quente ou Frio; a camada pode ser alterada a qualquer momento. 

A camada de acesso quente é usada para arquivos que são acessados com frequência – você paga um custo mais alto para o armazenamento, mas o custo de acessar os blobs é muito menor. Para os blobs armazenados na camada de acesso frio, você paga um custo mais alto para acessar os blobs, mas o custo de armazenamento é muito menor.

## <a name="accessing-your-blobs-files-and-queues"></a>Acessar blobs, arquivos e filas

Cada conta de armazenamento possui duas chaves de autenticação, que podem ser usadas para qualquer operação. Existem duas chaves para que você possa revisar as chaves ocasionalmente para aumentar a segurança. É importante que essas chaves sejam mantidas seguras porque sua posse, juntamente com o nome da conta, permite acesso ilimitado a todos os dados na conta de armazenamento. 

Esta seção aborda duas maneiras de proteger a conta de armazenamento e os seus respectivos dados. Para obter informações detalhadas sobre como proteger sua conta de armazenamento e seus dados, consulte o [Guia de segurança do Armazenamento do Azure](storage-security-guide.md).

### <a name="securing-access-to-storage-accounts-using-azure-ad"></a>Protegendo o acesso a contas de armazenamento usando o Azure AD

Uma maneira de proteger o acesso aos seus dados de armazenamento é controlar o acesso às chaves da conta de armazenamento. Com o controle de acesso baseado em função (RBAC) do Resource Manager, você pode atribuir funções a usuários, grupos ou aplicativos. Essas funções são associadas a um conjunto específico de ações que são permitidas ou não permitidas. Usar o RBAC para conceder acesso a uma conta de armazenamento cuida somente das operações de gerenciamento para a conta de armazenamento, como alterar o nível de acesso. Você não pode usar o RBAC para conceder acesso a objetos de dados como um compartilhamento de arquivo ou contêiner específico. No entanto, você pode usar o RBAC para conceder acesso às chaves de conta de armazenamento, que então podem ser usadas para ler os objetos de dados. 

### <a name="securing-access-using-shared-access-signatures"></a>Protegendo o acesso usando assinaturas de acesso compartilhado 

Você pode usar assinaturas de acesso compartilhado e políticas de acesso armazenadas para proteger os objetos de dados. Uma assinatura de acesso compartilhado (SAS) é uma cadeia de caracteres que contém um token de segurança que pode ser anexado a um URI a um ativo que permite delegar acesso a objetos de armazenamento específicos e especificar restrições, como as permissões e o intervalo de data/hora de acesso. Esta funcionalidade tem recursos abrangentes. Para saber informações detalhadas, consulte [Usando Assinaturas de Acesso Compartilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md).

### <a name="public-access-to-blobs"></a>Acesso público aos blobs

O Serviço Blob permite que você forneça acesso público a um contêiner e seus blobs ou um blob específico. Quando você indica que um contêiner ou blob é público, qualquer pessoa pode lê-lo anonimamente. Nenhuma autenticação é necessária. Um exemplo de quando seria desejável fazer isso é quando você tem um site que está usando imagens, vídeo ou documentos do armazenamento de Blobs. Para obter mais informações, consulte [Gerenciar acesso anônimo de leitura aos contêineres e blobs](../blobs/storage-manage-access-to-resources.md) 

## <a name="encryption"></a>Criptografia

Há dois tipos básicos de criptografia disponíveis para os serviços de Armazenamento. 

### <a name="encryption-at-rest"></a>Criptografia em repouso 

Você pode habilitar a Criptografia do Serviço de Armazenamento (SSE) no serviço de Arquivos (visualização) ou no serviço Blob para uma conta de armazenamento do Azure. Se habilitado, todos os dados gravados para o serviço específico são criptografados antes de serem gravados. Quando você lê os dados, eles são descriptografados antes de serem retornados. 

### <a name="client-side-encryption"></a>Criptografia do cliente

As bibliotecas de cliente de armazenamento possuem métodos que você pode chamar para criptografar os dados por meio de programação antes destes serem enviados do cliente para o Azure. Ele são armazenados criptografados, o que significa que ele também é criptografado em repouso. Ao ler os dados de volta, você descriptografa as informações depois de recebê-las. 

### <a name="encryption-in-transit-with-azure-file-shares"></a>Criptografia durante a transferência com compartilhamentos de Arquivos do Azure

Confira [Usando Assinaturas de Acesso Compartilhado (SAS)](../storage-dotnet-shared-access-signature-part-1.md) para saber mais sobre as assinaturas de acesso compartilhado. Confira [Gerenciar acesso anônimo de leitura aos contêineres e blobs](../blobs/storage-manage-access-to-resources.md) e [Autenticação para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx) para saber mais sobre o acesso seguro à sua conta de armazenamento.

Para saber mais sobre como proteger sua conta de armazenamento e criptografia, consulte o [Guia de segurança do Armazenamento do Azure](storage-security-guide.md).

## <a name="replication"></a>Replicação

Para garantir que seus dados sejam duráveis, o Armazenamento do Azure tem a capacidade de manter (e gerenciar) várias cópias dos seus dados. Isso é chamado de replicação ou, algumas vezes, de redundância. Quando você configura sua conta de armazenamento, você seleciona um tipo de replicação. Na maioria dos casos, essa configuração pode ser modificada depois que a conta de armazenamento é configurada. 

Todas as contas de armazenamento possuem **armazenamento localmente redundante (LRS)**. Isso significa que três cópias de seus dados são gerenciadas pelo Armazenamento do Azure no data center especificado quando a conta de armazenamento foi configurada. Quando as alterações são confirmadas em uma cópia, as outras duas cópias são atualizadas antes das alterações serem confirmadas com sucesso. Isso significa que as três réplicas estão sempre sincronizadas. Além disso, três cópias residem em domínios de falha e domínios de atualização separados, o que significa que os dados estão disponíveis, mesmo se um nó de armazenamento, que guarda os seus dados falha ou é colocado offline para ser atualizado. 

**Armazenamento com redundância local (LRS)**

Conforme explicado acima, com o LRS você possui três cópias de seus dados em um único datacenter. Ele lida com o problema dos dados tornarem-se indisponíveis, se um nó de armazenamento falha ou é colocado offline para ser atualizado, mas não no caso de um datacenter inteiro ficar indisponível.

**Armazenamento com redundância de zona (ZRS)**

O armazenamento com redundância de zona (ZRS) mantém as três cópias locais de seus dados, bem como outro conjunto de três cópias de seus dados. O segundo conjunto de três cópias é replicado assincronamente em data centers dentro de uma ou duas regiões. Observe que o ZRS está disponível somente para blobs de blocos em contas de armazenamento de uso geral. Depois que você tiver criado sua conta de armazenamento e selecionado ZRS, você não poderá convertê-la para utilizar nenhum outro tipo de replicação, ou vice-versa.

Contas ZRS fornecem maior durabilidade do que o LRS, mas contas ZRS não possuem métricas ou recursos de registro em log. 

**Armazenamento com redundância geográfica (GRS)**

O armazenamento com redundância geográfica (GRS) mantém as três cópias locais de seus dados em uma região primária e outro conjunto de três cópias de seus dados em uma região secundária a centenas de quilômetros de distância da região primária. Em caso de falha na região primária, o Armazenamento do Azure realizará failover para a região secundária. 

**Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)** 

O armazenamento com redundância geográfica com acesso de leitura é exatamente como o GRS, exceto que você obtém acesso de leitura aos dados no local secundário. Se o data center principal ficar indisponível temporariamente, você pode continuar a ler os dados do local secundário. Isso pode ser muito útil. Por exemplo, você ter um aplicativo Web que é alterado para o modo somente leitura e aponta para a cópia secundária, permitindo algum acesso, mesmo que as atualizações não estejam disponíveis. 

> [!IMPORTANT]
> Você pode alterar como seus dados são replicados depois que sua conta de armazenamento tiver sido criada, a menos que tenha especificado ZRS quando criou a conta. No entanto, observe que você pode incorrer em custo por uma transferência de dados adicional e ocasional se alternar de LRS para GRS ou RA-GRS.
>

Para saber mais informações sobre replicação, consulte [Replicação do Armazenamento do Azure](storage-redundancy.md).

Para obter informação sobre recuperação de desastre, consulte [O que fazer se ocorrer uma interrupção do Armazenamento do Azure](storage-disaster-recovery-guidance.md).

Para obter um exemplo de como utilizar o armazenamento de RA-GRS para garantir a alta disponibilidade, consulte [Criação de aplicativos altamente disponíveis usando RA-GRS](storage-designing-ha-apps-with-ragrs.md).

## <a name="transferring-data-to-and-from-azure-storage"></a>Transferência de dados do Armazenamento do Azure e para ele

Você pode usar o utilitário de linha de comando AzCopy para copiar dados de arquivos e de blobs dentro de sua conta de armazenamento ou entre contas de armazenamento. Consulte um dos seguintes artigos para obter ajuda:

* [Transferir dados com o AzCopy para Windows](storage-use-azcopy.md)
* [Transferir dados com o AzCopy para Linux](storage-use-azcopy-linux.md)

O AzCopy se baseia na [Biblioteca de Movimentação de Dados do Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), que atualmente está disponível na visualização.

O serviço de Importação/Exportação do Azure pode ser usado para importar ou exportar grandes quantidades de dados de blobs de ou para sua conta de armazenamento. Você prepara e envia vários discos rígidos para um data center do Azure, onde eles transferirão os dados de/para discos rígidos e enviarão os discos rígidos de volta para você. Para obter mais informações sobre o serviço de Importação/Exportação, consulte [Usar o Serviço de Importação/Exportação do Microsoft Azure para Transferir Dados para o Armazenamento de Blobs](../storage-import-export-service.md).

## <a name="pricing"></a>Preços

Para obter informações detalhadas sobre os preços do Armazenamento do Azure, consulte a [Página de preços](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>APIs, bibliotecas e ferramentas de armazenamento
Os recursos do Armazenamento do Azure podem ser acessados por qualquer linguagem que possa fazer solicitações HTTP/HTTPS. Além disso, o Armazenamento do Azure oferece bibliotecas de programação para várias linguagens populares. Essas bibliotecas simplificam muitos aspectos do trabalho com o Armazenamento do Azure manipulando detalhes, como invocação síncrona e assíncrona, processamento em lotes de operações, gerenciamento de exceções, novas tentativas automáticas, comportamento operacional e assim por diante. Atualmente, as bibliotecas estão disponíveis para as seguintes linguagens e plataformas, com outras no pipeline:

### <a name="azure-storage-data-services"></a>Serviços de dados do Armazenamento do Azure
* [API REST dos Serviços de Armazenamento](/rest/api/storageservices/)
* [Biblioteca do Cliente de Armazenamento para .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Biblioteca do Cliente de Armazenamento para C++](https://github.com/Azure/azure-storage-cpp)
* [Biblioteca do Cliente de Armazenamento para Java/Android](https://azure.microsoft.com/develop/java/)
* [Biblioteca do Cliente de Armazenamento para Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Biblioteca do Cliente de Armazenamento para PHP](https://azure.microsoft.com/develop/php/)
* [Biblioteca do Cliente de Armazenamento para Python](https://azure.microsoft.com/develop/python/)
* [Biblioteca do Cliente de Armazenamento para Ruby](https://azure.microsoft.com/develop/ruby/)
* [Cmdlets de Armazenamento para PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)
* [Comandos de Armazenamento para CLI 2.0](/cli/azure/storage)

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Armazenamento de Blobs](../blobs/storage-blobs-introduction.md)
* [Saiba mais sobre o Armazenamento de Arquivos](../storage-files-introduction.md)
* [Saiba mais sobre o Armazenamento de Filas](../queues/storage-queues-introduction.md)

Para colocar o Armazenamento do Azure em funcionamento rapidamente, consulte um dos Inícios Rápidos a seguir:
* [Criar uma conta de armazenamento usando o PowerShell](storage-quickstart-create-storage-account-powershell.md)
* [Criar uma conta de armazenamento usando a CLI](storage-quickstart-create-storage-account-cli.md)

<!-- FIGURE OUT WHAT TO DO WITH ALL THESE LINKS.

Azure Storage resources can be accessed by any language that can make HTTP/HTTPS requests. Additionally, Azure Storage offers programming libraries for several popular languages. These libraries simplify many aspects of working with Azure Storage by handling details such as synchronous and asynchronous invocation, batching of operations, exception management, automatic retries, operational behavior and so forth. Libraries are currently available for the following languages and platforms, with others in the pipeline:

### Azure Storage data services
* [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage Client Library for .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [Storage Client Library for Java/Android](https://azure.microsoft.com/develop/java/)
* [Storage Client Library for Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Storage Client Library for PHP](https://azure.microsoft.com/develop/php/)
* [Storage Client Library for Python](https://azure.microsoft.com/develop/python/)
* [Storage Client Library for Ruby](https://azure.microsoft.com/develop/ruby/)
* [Storage Cmdlets for PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)

### Azure Storage management services
* [Storage Resource Provider REST API Reference](/rest/api/storagerp/)
* [Storage Resource Provider Client Library for .NET](/dotnet/api/microsoft.azure.management.storage)
* [Storage Resource Provider Cmdlets for PowerShell 1.0](/powershell/module/azure.storage)
* [Storage Service Management REST API (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### Azure Storage data movement services
* [Storage Import/Export Service REST API](../storage-import-export-service.md)
* [Storage Data Movement Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### Tools and utilities
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is a free, standalone app from Microsoft that enables you to work visually with Azure Storage data on Windows, macOS, and Linux.
* [Azure Storage Client Tools](../storage-explorers.md)
* [Azure SDKs and Tools](https://azure.microsoft.com/tools/)
* [Azure Storage Emulator](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azure/overview)
* [AzCopy Command-Line Utility](http://aka.ms/downloadazcopy)

## Next steps
To learn more about Azure Storage, explore these resources:

### Documentation
* [Azure Storage Documentation](https://azure.microsoft.com/documentation/services/storage/)
* [Create a storage account](../storage-create-storage-account.md)

-->

### <a name="for-administrators"></a>Para administradores
* [Usando o PowerShell do Azure com o Armazenamento do Azure](storage-powershell-guide-full.md)
* [Uso da CLI do Azure com o Armazenamento do Azure](../storage-azure-cli.md)

### <a name="for-net-developers"></a>Para desenvolvedores do .NET
* [Introdução ao armazenamento de Blobs do Azure usando o .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Desenvolver para os Arquivos do Azure com .NET](../files/storage-dotnet-how-to-use-files.md)
* [Introdução ao armazenamento de Tabelas do Azure usando o .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Introdução ao armazenamento de Fila do Azure usando o .NET](../storage-dotnet-how-to-use-queues.md)

### <a name="for-javaandroid-developers"></a>Para desenvolvedores de Java/Android
* [Como usar o Armazenamento de Blob do Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Desenvolver para os Arquivos do Azure com Java](../files/storage-java-how-to-use-file-storage.md)
* [Como usar o Armazenamento de Tabela do Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Como usar o Armazenamento de Fila no Java](../storage-java-how-to-use-queue-storage.md)

### <a name="for-nodejs-developers"></a>Para desenvolvedores do Node.js
* [Como usar o armazenamento de Blob do Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Como usar o Armazenamento de Tabela do Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Como usar o Armazenamento de Fila do Node.js](../storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Para desenvolvedores do PHP
* [Como usar o Armazenamento de Blob do PHP](../blobs/storage-php-how-to-use-blobs.md)
* [Como usar o Armazenamento de Tabela do PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Como usar o Armazenamento de Fila do PHP](../storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Para desenvolvedores do Ruby
* [Como usar o Armazenamento de blob no Ruby](../blobs/storage-ruby-how-to-use-blob-storage.md)
* [Como usar o Armazenamento de Tabela do Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)
* [Como usar o Armazenamento de fila do Ruby](../storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Para desenvolvedores do Python
* [Como usar o armazenamento de Blob no Python](../blobs/storage-python-how-to-use-blob-storage.md)
* [Desenvolver para os Arquivos do Azure com Python](../files/storage-python-how-to-use-file-storage.md)
* [Como usar o Armazenamento de tabela do Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Como usar o Armazenamento de fila do Python](../storage-python-how-to-use-queue-storage.md)