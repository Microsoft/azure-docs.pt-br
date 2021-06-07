---
title: Introdução ao Armazenamento do Azure – Armazenamento em nuvem no Azure | Microsoft Docs
description: A principal plataforma de armazenamento do Azure é a solução de armazenamento em nuvem da Microsoft. O Armazenamento do Azure oferece armazenamento para objetos de dados altamente disponível, seguro, durável, altamente escalonável e redundante.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: b49e780714bd5e0662c6cb7ef208c473a8b45ccc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498142"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>Introdução aos principais serviços de armazenamento do Azure

A plataforma de armazenamento do Azure é a solução de armazenamento em nuvem da Microsoft para cenários de armazenamento de dados modernos. Os serviços de armazenamento de núcleo oferecem um armazenamento de objetos altamente escalonável para objetos de dados, armazenamento em disco para VMs (máquinas virtuais) do Azure, um serviço de sistema de arquivos para a nuvem, um repositório de mensagens para mensagens confiáveis e um repositório NoSQL. Os serviços são:

- **Durável e altamente disponível.** A redundância garante a segurança dos seus dados no caso de falhas de hardware transitórias. Você também pode optar por replicar dados em datacenters ou regiões geográficas para obter mais proteção contra catástrofes ou desastres naturais. Os dados replicados dessa maneira permanecem altamente disponíveis no caso de uma interrupção inesperada.
- **Soquete.** Todos os dados gravados em uma conta de armazenamento do Azure são criptografados pelo serviço. O Armazenamento do Azure oferece um controle refinado sobre quem possui acesso aos seus dados.
- **Chegar.** O Armazenamento do Azure foi concebido para ser altamente escalonável e atender às necessidades de desempenho e armazenamento de dados dos aplicativos atuais.
- **Administra.** O Azure lida com a manutenção de hardware, atualizações e problemas críticos para você.
- **Acessível.** Os dados no Armazenamento do Azure são acessíveis de qualquer lugar no mundo por HTTP ou HTTPS. A Microsoft fornece bibliotecas de cliente para o armazenamento do Azure em uma variedade de linguagens, incluindo .NET, Java, Node.js, Python, PHP, Ruby, Go e outros, bem como uma API REST madura. O Armazenamento do Azure oferece suporte para scripts no Azure PowerShell ou na CLI do Azure. E o Portal do Azure e o Gerenciador de Armazenamento do Azure oferecem soluções visualmente fáceis para o trabalho com os seus dados.  

## <a name="core-storage-services"></a>Serviços de armazenamento de núcleo

A plataforma de armazenamento do Azure inclui os seguintes serviços de dados:

- [Blobs do Azure](../blobs/storage-blobs-introduction.md): um repositório de objetos altamente escalonável para texto e dados binários. Também inclui suporte para análise de Big Data por meio de Data Lake Storage Gen2.
- [Arquivos do Azure](../files/storage-files-introduction.md): compartilhamentos de arquivos gerenciados para implantações locais ou na nuvem.
- [Filas do Azure](../queues/storage-queues-introduction.md): um armazenamento de mensagens para um sistema de mensagens confiável entre componentes do aplicativo.
- [Tabelas do Azure](../tables/table-storage-overview.md): Um armazenamento NoSQL para um armazenamento sem esquemas de dados estruturados.
- [Discos do Azure](../../virtual-machines/managed-disks-overview.md): volumes de armazenamento de nível de bloco para VMs do Azure.

Cada serviço é acessado através de uma conta de armazenamento. Para usá-los, consulte [Criar uma conta de armazenamento](storage-account-create.md).

## <a name="example-scenarios"></a>Cenários de exemplo

A tabela a seguir compara arquivos, BLOBs, discos, filas e tabelas e mostra cenários de exemplo para cada um.

| Recurso | Descrição | Quando usar |
|--------------|-------------|-------------|
| **Arquivos do Azure** |Oferece compartilhamentos de arquivos de nuvem totalmente gerenciados que você pode acessar de qualquer lugar por meio do protocolo SMB (Server Message Block) padrão do setor.<br><br>Você pode montar compartilhamentos de arquivos do Azure por meio de implantações locais ou na nuvem do Windows, Linux e macOS. | Você deseja "aumentar e deslocar" um aplicativo para a nuvem que já usa as APIs do sistema de arquivos nativo para compartilhar dados entre ele e outros aplicativos em execução no Azure.<br/><br/>Você deseja substituir ou complementar os servidores de arquivos locais ou dispositivos NAS.<br><br> Você deseja armazenar ferramentas de desenvolvimento e depuração que precisam ser acessadas em várias máquinas virtuais. |
| **Blobs do Azure** | Permite que dados não estruturados sejam armazenados e acessados em grande escala em blobs de blocos.<br/><br/>Também dá suporte ao [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) para soluções de análise de big data do enterprise. | Você deseja que o aplicativo dê suporte a cenários de streaming e de acesso aleatório.<br/><br/>Você deseja poder acessar dados do aplicativo em qualquer lugar.<br/><br/>Você deseja criar um lago de dados empresariais no Azure e executar análise de big data. |
| **Discos do Azure** | Permite que os dados sejam armazenados de forma persistente e acessados de um disco rígido virtual anexado. | Você deseja "mover e deslocar" os aplicativos que usam APIs do sistema de arquivos nativo para ler e gravar dados em discos persistentes.<br/><br/>Você deseja armazenar os dados que não precisam ser acessados fora da máquina virtual à qual o disco está anexado. |
| **Filas do Azure** | Permite o enfileiramento de mensagens assíncronas entre componentes do aplicativo. | Você deseja desacoplar componentes de aplicativos e usar mensagens assíncronas para se comunicar entre eles.<br><br>Para obter orientação sobre quando usar o armazenamento de filas versus filas do barramento de serviço, consulte [filas de armazenamento e filas do barramento de serviço – comparações e contrastes](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md). |
| **Tabelas do Azure** | Permitir que você armazene dados NoSQL estruturados na nuvem, fornecendo um repositório de chaves/atributos com um design sem esquema. | Você deseja armazenar conjuntos de dados flexíveis, como os de usuário para aplicativos Web, catálogos de endereços, informações de dispositivo ou outros tipos de metadados que seu serviço requer. <br/><br/>Para obter orientação sobre quando usar o armazenamento de tabela versus o Azure Cosmos DB API de Tabela, consulte [desenvolvendo com Azure Cosmos DB API de tabela e armazenamento de tabelas do Azure](../../cosmos-db/table-support.md). |

## <a name="blob-storage"></a>Armazenamento de Blobs

O Armazenamento de Blobs do Azure é uma solução de armazenamento de objetos da Microsoft para a nuvem. O armazenamento de Blobs é otimizado para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários.

O armazenamento de Blobs é ideal para:

- Fornecimento de imagens ou de documentos diretamente a um navegador.
- Armazenamento de arquivos para acesso distribuído.
- Transmissão por streaming de áudio e vídeo.
- Armazenamento de dados de backup e restauração, recuperação de desastres e arquivamento.
- Armazenamento de dados para análise por um serviço local ou hospedado no Azure.

Os objetos no armazenamento de Blobs podem ser acessados de qualquer lugar no mundo via HTTP ou HTTPS. Usuários ou aplicativos cliente podem acessar blobs por meio de URLs, da [API REST do Armazenamento do Azure](/rest/api/storageservices/blob-service-rest-api), do [Azure PowerShell](/powershell/module/azure.storage), da [CLI do Azure](/cli/azure/storage) ou de uma biblioteca de cliente de Armazenamento do Azure. As bibliotecas de clientes de armazenamento estão disponíveis para várias linguagens, incluindo [.NET](/dotnet/api/overview/azure/storage), [Java](/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) e [Ruby](https://azure.github.io/azure-storage-ruby).

Para mais informações sobre o Armazenamento de Blobs, confira [Introdução ao Armazenamento de Blobs](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Arquivos do Azure

[Os arquivos do Azure](../files/storage-files-introduction.md) permitem que você configure compartilhamentos de arquivos de rede altamente disponíveis que podem ser acessados usando o protocolo SMB (Server Message Block) padrão. Isso significa que várias VMs podem compartilhar os mesmos arquivos com acesso de leitura e gravação. Você também pode ler os arquivos usando a interface REST ou as bibliotecas de cliente de armazenamento.

Uma coisa que diferencia os Arquivos do Azure dos arquivos em um compartilhamento de arquivos corporativo é a capacidade de acessar os arquivos de qualquer lugar do mundo usando uma URL que aponte para o arquivo, e que inclua um token SAS (Assinatura de Acesso Compartilhado). Você pode gerar tokens SAS; eles permitem o acesso específico a um ativo privado durante período especificado.

Os compartilhamentos de arquivos podem ser usados para muitos cenários comuns:

- Muitos aplicativos locais usam compartilhamentos de arquivos. Esse recurso facilita a migração dos aplicativos que compartilham dados com o Azure. Se você montar o compartilhamento de arquivos na mesma letra de unidade usada pelo aplicativo local, a parte de seu aplicativo que acessa o compartilhamento de arquivos deverá funcionar com o mínimo de, ou nenhuma, alteração.

- Os arquivos de configuração podem ser armazenados em um compartilhamento de arquivos e acessados de várias VMs. As ferramentas e utilitários usados por vários desenvolvedores em um grupo podem ser armazenados em um compartilhamento de arquivos, garantindo que todas as pessoas possam encontrá-los, e que usem a mesma versão.

- Os logs de recursos, as métricas e os despejos de memória são apenas três exemplos de dados que podem ser gravados em um compartilhamento de arquivos e processados ou analisados posteriormente.

Para obter mais informações sobre Arquivos do Azure, consulte [Introdução aos Arquivos do Azure](../files/storage-files-introduction.md).

Alguns recursos do SMB não são aplicáveis à nuvem. Para obter mais informações, consulte [Recursos sem suporte no serviço Arquivo do Azure](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

## <a name="queue-storage"></a>Armazenamento de filas

O serviço Fila do Azure é usado para armazenar e recuperar mensagens. A fila de mensagens pode ser de até 64 KB de tamanho e uma fila pode conter milhões de mensagens. Filas são geralmente usadas para armazenar as listas de mensagens a serem processadas de forma assíncrona.

Por exemplo, digamos que você deseja que seus clientes possam carregar imagens e você deseja criar miniaturas para cada imagem. Você pode fazer com que o cliente aguarde que você crie miniaturas ao carregar as imagens. Uma alternativa seria usar uma fila. Quando o cliente concluir o carregamento, grave uma mensagem na fila. Em seguida, faça com que uma Função do Azure recupere a mensagem da fila e crie as miniaturas. Cada uma das partes desse processamento pode ser dimensionada separadamente oferecendo mais controle durante o ajuste para seu uso.

Para obter mais informações sobre Filas do Azure, consulte [Introdução às Filas do Azure](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Armazenamento de tabela

O armazenamento de Tabelas do Azure agora faz parte do Azure Cosmos DB. Para ver a documentação do armazenamento de Tabelas do Azure, veja a [Visão geral do Armazenamento de Tabelas do Azure](../tables/table-storage-overview.md). Além do serviço de armazenamento de Tabelas do Azure existente, há uma nova oferta de API de Tabela do Azure Cosmos DB que fornece tabelas otimizadas de taxa de transferência, distribuição global e índices secundários automáticos. Para saber mais e experimentar a nova experiência Premium, confira [Azure Cosmos DB API de tabela](../../cosmos-db/table-introduction.md).

Para saber mais informações sobre o armazenamento de tabelas, consulte [Visão geral do armazenamento de Tabelas do Azure](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Armazenamento em disco

Um disco gerenciado do Azure é um disco rígido virtual (VHD). Você pode pensar nisso como um disco físico em um servidor local, mas virtualizado. Os discos gerenciados pelo Azure são armazenados como BLOBs de páginas, que são um objeto de armazenamento de e/s aleatório no Azure. Chamamos um disco gerenciado "Managed" porque ele é uma abstração em blobs de página, contêineres de BLOB e contas de armazenamento do Azure. Com discos gerenciados, tudo o que você precisa fazer é provisionar o disco e o Azure cuidará do resto.

Para obter mais informações sobre discos gerenciados, consulte [introdução aos Azure Managed disks](../../virtual-machines/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

O Armazenamento do Azure oferece diversos tipos de contas de armazenamento. Cada tipo é compatível com recursos diferentes e tem um modelo de preços próprio. Para obter mais informações sobre os tipos de conta de armazenamento, consulte [Visão geral da conta de armazenamento do Azure](storage-account-overview.md).

## <a name="secure-access-to-storage-accounts"></a>Acesso seguro a contas de armazenamento

Cada solicitação para o armazenamento do Azure deve ser autorizada. O armazenamento do Azure dá suporte aos seguintes métodos de autorização:

- **Integração do Azure Active Directory (Azure AD) para dados de BLOB e fila.** O armazenamento do Azure dá suporte à autenticação e autorização com o Azure AD para os serviços BLOB e fila por meio do controle de acesso baseado em função do Azure (RBAC do Azure). A autorização de solicitações com o Azure AD é recomendada para segurança superior e facilidade de uso. Para obter mais informações, consulte [autorizar o acesso a BLOBs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md).
- **Autorização do Azure AD sobre SMB para arquivos do Azure.** Os arquivos do Azure oferecem suporte à autorização baseada em identidade sobre SMB (protocolo de mensagem de servidor) por meio de Azure Active Directory Domain Services (Azure AD DS) ou Active Directory Domain Services local (versão prévia). Suas VMs do Windows ingressadas no domínio podem acessar compartilhamentos de arquivos do Azure usando as credenciais do Azure AD. Para obter mais informações, consulte [visão geral do suporte à autenticação baseada em identidade de arquivos do Azure para acesso SMB](../files/storage-files-active-directory-overview.md) e [planejamento para uma implantação de arquivos do Azure](../files/storage-files-planning.md#identity).
- **Autorização com chave compartilhada.** Os serviços de Azure Storage Blob, arquivos, fila e tabela dão suporte à autorização com chave compartilhada. Um cliente que usa a autorização de chave compartilhada passa um cabeçalho com cada solicitação que é assinada usando a chave de acesso da conta de armazenamento. Para obter mais informações, consulte [Autorizar com Chave Compartilhada](/rest/api/storageservices/authorize-with-shared-key).
- **Autorização usando SAS (assinaturas de acesso compartilhado).** Uma SAS (assinatura de acesso compartilhado) é uma cadeia de caracteres que contém um token de segurança que pode ser anexado ao URI para um recurso de armazenamento. O token de segurança encapsula restrições como permissões e o intervalo de acesso. Para obter mais informações, consulte [usando SAS (assinaturas de acesso compartilhado)](storage-sas-overview.md).
- **Acesso anônimo a contêineres e blobs.** Um contêiner e seus BLOBs podem estar publicamente disponíveis. Quando você especifica que um contêiner ou BLOB é público, qualquer pessoa pode lê-lo anonimamente; nenhuma autenticação é necessária. Para obter mais informações, confira [Gerenciar acesso anônimo de leitura aos contêineres e blobs](../blobs/anonymous-read-access-configure.md).

## <a name="encryption"></a>Criptografia

Há dois tipos básicos de criptografia disponíveis para os serviços de armazenamento principal. Para saber mais sobre segurança e criptografia, consulte o [Guia de segurança do Armazenamento do Azure](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Criptografia em repouso

A criptografia de armazenamento do Azure protege e protege seus dados para atender aos compromissos de segurança e conformidade da organização. O armazenamento do Azure criptografa automaticamente todos os dados antes de persistir para a conta de armazenamento e descriptografá-los antes da recuperação. Os processos de criptografia, descriptografia e gerenciamento de chaves são transparentes para os usuários. Os clientes também podem optar por gerenciar suas próprias chaves usando Azure Key Vault. Para obter mais informações, consulte [Criptografia do Armazenamento do Azure para dados em repouso](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Criptografia do cliente

As bibliotecas de cliente de armazenamento do Azure fornecem métodos para criptografar dados da biblioteca de cliente antes de enviá-los pela rede e descriptografar a resposta. Os dados criptografados por meio da criptografia do lado do cliente também são criptografados em repouso pelo armazenamento do Azure. Para obter mais informações sobre criptografia do lado do cliente, consulte [criptografia do lado do cliente com .net para o armazenamento do Azure](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundância

Para garantir que seus dados sejam duráveis, o armazenamento do Azure armazena várias cópias de seus dados. Quando você configura sua conta de armazenamento, você seleciona uma opção de redundância. Para mais informações, confira [Redundância do Armazenamento do Microsoft Azure](./storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="transfer-data-to-and-from-azure-storage"></a>Transferir dados de e para o armazenamento do Azure

Você tem várias opções para movimentar dados para dentro ou para fora do Armazenamento do Azure. A opção escolhida depende do tamanho de seu conjunto de dados e da largura de banda da rede. Para obter mais informações, consulte [Escolher uma solução do Azure para transferência de dados](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Preços

Ao tomar decisões sobre como os dados são armazenados e acessados, você também deve considerar os custos envolvidos. Para obter mais informações, consulte [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="storage-apis-libraries-and-tools"></a>APIs, bibliotecas e ferramentas de armazenamento

Você pode acessar recursos em uma conta de armazenamento por qualquer linguagem que possa fazer solicitações HTTP/HTTPS. Além disso, os principais serviços de armazenamento do Azure oferecem bibliotecas de programação para várias linguagens populares. Essas bibliotecas simplificam muitos aspectos do trabalho com o Armazenamento do Azure manipulando detalhes, como invocação síncrona e assíncrona, processamento em lotes de operações, gerenciamento de exceções, novas tentativas automáticas, comportamento operacional e assim por diante. Atualmente, as bibliotecas estão disponíveis para as seguintes linguagens e plataformas, com outras no pipeline:

### <a name="azure-storage-data-api-and-library-references"></a>Bibliotecas de referência e API de dados do Armazenamento do Azure

- [API REST de Armazenamento do Azure](/rest/api/storageservices/)
- [Biblioteca de cliente de armazenamento do Azure para .NET](/dotnet/api/overview/azure/storage)
- [Biblioteca de cliente de armazenamento do Azure para Java/Android](/java/api/overview/azure/storage)
- [Biblioteca de cliente de armazenamento do Azure para Node.js](/javascript/api/overview/azure/storage-overview)
- [Biblioteca de cliente de armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python)
- [Biblioteca de cliente de armazenamento do Azure para PHP](https://github.com/Azure/azure-storage-php)
- [Biblioteca de cliente de armazenamento do Azure para Ruby](https://github.com/Azure/azure-storage-ruby)
- [Biblioteca de cliente de armazenamento do Azure para C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Bibliotecas de referência e API de gerenciamento do Armazenamento do Azure

- [API REST do Provedor de Recursos de Armazenamento](/rest/api/storagerp/)
- [Biblioteca do Cliente do Provedor de Recursos de Armazenamento para .NET](/dotnet/api/overview/azure/storage/management)
- [API REST do Gerenciamento de Serviços de Armazenamento (clássico)](/previous-versions/azure/reference/ee460790(v=azure.100))

### <a name="azure-storage-data-movement-api-and-library-references"></a>Bibliotecas de referência e API de movimentação de dados do Armazenamento do Azure

- [API REST do Serviço de Importação/Exportação do Armazenamento](/rest/api/storageimportexport/)
- [Biblioteca do Cliente de Movimentação de Dados do Armazenamento para .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Ferramentas e utilitários

- [Cmdlets do Azure PowerShell para Armazenamento](/powershell/module/az.storage)
- [Cmdlets da CLI do Azure para Armazenamento](/cli/azure/storage)
- [Utilitário de linha de comando AzCopy](https://aka.ms/downloadazcopy)
- [O Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.
- [Modelos de Azure Resource Manager para o armazenamento do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

## <a name="next-steps"></a>Próximas etapas

Para começar a executar o com os principais serviços de armazenamento do Azure, consulte [criar uma conta de armazenamento](storage-account-create.md).