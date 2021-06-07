---
title: Usar o emulador de armazenamento do Azure para desenvolvimento e teste
description: O emulador de armazenamento do Azure fornece um ambiente de desenvolvimento local gratuito para desenvolver e testar seus aplicativos de armazenamento do Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/16/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: 6d9e0e63a3586be139620c154616acfcba2ab10f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498210"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Usar o emulador de armazenamento do Azure para desenvolvimento e teste

O Emulador de Armazenamento do Microsoft Azure é uma ferramenta que emula os serviços BLOB, fila e tabela do Azure para fins de desenvolvimento local. Você pode testar seu aplicativo em relação aos serviços de armazenamento localmente sem criar uma assinatura do Azure ou incorrer em custos. Quando estiver satisfeito com o funcionamento do aplicativo no emulador, alterne para o usando uma conta de armazenamento do Azure na nuvem.

> [!IMPORTANT]
> O emulador de armazenamento do Azure não está mais sendo ativamente desenvolvido. [**Azurite**](storage-use-azurite.md) é a plataforma do emulador de armazenamento em andamento. Azurite substitui o emulador de armazenamento do Azure. O azurite continuará a ser atualizado para dar suporte às versões mais recentes das APIs de armazenamento do Azure. Para obter mais informações, consulte [**usar o emulador azurite para o desenvolvimento de armazenamento local do Azure**](storage-use-azurite.md).

## <a name="get-the-storage-emulator"></a>Obter o emulador de armazenamento

O emulador de armazenamento está disponível como parte do [SDK do Microsoft Azure](https://azure.microsoft.com/downloads/). Você também pode instalar o emulador de armazenamento usando o [instalador autônomo](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (download direto). Para instalar o emulador de armazenamento, você deve ter privilégios administrativos em seu computador.

O emulador de armazenamento é executado atualmente somente no Windows. Se você precisar de um emulador de armazenamento para Linux, uma opção é a Comunidade mantida, emulador de armazenamento de código aberto [azurite](https://github.com/azure/azurite).

> [!NOTE]
> Não há garantia de que os dados criados em uma versão do emulador de armazenamento estejam acessíveis ao usar uma versão diferente. Se você precisar manter seus dados por um longo prazo, recomendamos que armazene esses dados em uma conta de armazenamento do Azure, em vez de no emulador de armazenamento.
> 
> O emulador de armazenamento depende de versões específicas das bibliotecas OData. Não há suporte para a substituição das DLLs OData usadas pelo emulador de armazenamento por outras versões e isso pode causar um comportamento inesperado. No entanto, qualquer versão do OData com suporte no serviço de armazenamento pode ser usada para enviar solicitações para o emulador.

## <a name="how-the-storage-emulator-works"></a>Como o emulador de armazenamento funciona

O emulador de armazenamento usa uma instância LocalDB do Microsoft SQL Server 2012 Express para emular os serviços de armazenamento do Azure. Você pode optar por configurar o emulador de armazenamento para acessar uma instância local do SQL Server em vez da instância de LocalDB. Consulte a seção [Iniciar e inicializar o emulador de armazenamento](#start-and-initialize-the-storage-emulator) mais adiante neste artigo para saber mais.

O emulador de armazenamento se conecta a SQL Server ou LocalDB usando a autenticação do Windows.

Existem algumas diferenças na funcionalidade entre o emulador de armazenamento e os serviços de armazenamento do Azure. Para obter mais informações sobre essas diferenças, consulte a seção [diferenças entre o emulador de armazenamento e o armazenamento do Azure](#differences-between-the-storage-emulator-and-azure-storage) mais adiante neste artigo.

## <a name="start-and-initialize-the-storage-emulator"></a>Iniciar e inicializar o emulador de armazenamento

Para iniciar o emulador de armazenamento do Azure:

1. Selecione o botão **Iniciar** ou pressione a tecla **Windows**.
2. Comece digitando `Azure Storage Emulator`.
3. Selecione o emulador na lista de aplicativos.

Quando o emulador de armazenamento for iniciado, uma janela de prompt de comando será exibida. Você pode usar essa janela do console para iniciar e parar o emulador de armazenamento. Você também pode limpar os dados, obter o status e inicializar o emulador no prompt de comando. Para obter mais informações, consulte a seção [referência da ferramenta de linha de comando do emulador de armazenamento](#storage-emulator-command-line-tool-reference) mais adiante neste artigo.

> [!NOTE]
> O emulador de armazenamento do Azure pode não iniciar corretamente se outro emulador de armazenamento, como azurite, estiver em execução no sistema.

Quando o emulador estiver em execução, você verá um ícone na área de notificação da barra de tarefas do Windows.

Quando você fechar a janela do prompt de comando do emulador de armazenamento, o emulador de armazenamento continuará a ser executado. Para abrir a janela do console do emulador de armazenamento novamente, siga as etapas anteriores como se fosse iniciar o emulador de armazenamento.

Na primeira vez que você executar o emulador de armazenamento, o ambiente de armazenamento local será inicializado para você. O processo de inicialização cria um banco de dados no LocalDB e reserva portas HTTP para cada serviço de armazenamento local.

O emulador de armazenamento é instalado por padrão no `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator` .

> [!TIP]
> Você pode usar o [Gerenciador de armazenamento do Microsoft Azure](https://storageexplorer.com) para trabalhar com recursos de emulador de armazenamento local. Procure "(portas padrão do emulador) (chave)" em "local & anexado" na árvore de recursos do Gerenciador de Armazenamento depois de instalar e iniciar o emulador de armazenamento.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicializar o emulador de armazenamento para usar um banco de dados SQL diferente

Você pode usar a ferramenta de linha de comando do emulador de armazenamento para inicializar o emulador de armazenamento para apontar para uma instância do banco de dados SQL diferente da instância LocalDB padrão:

1. Abra a janela do console do emulador de armazenamento conforme descrito na seção [Iniciar e inicializar o emulador de armazenamento](#start-and-initialize-the-storage-emulator) .
1. Na janela de comando, digite o seguinte comando, em que `<SQLServerInstance>` é o nome da instância do SQL Server. Para usar o LocalDB, especifique `(localdb)\MSSQLLocalDb` como a instância do SQL Server.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   Você também pode usar o seguinte comando, que direciona o emulador para usar a instância padrão do SQL Server:

   `AzureStorageEmulator.exe init /server .`

   Ou, você pode usar o comando a seguir, que inicializa o banco de dados para a instância LocalDB padrão:

   `AzureStorageEmulator.exe init /forceCreate`

Para obter mais informações sobre esses comandos, consulte [referência da ferramenta de linha de comando do emulador de armazenamento](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Use o [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) para gerenciar suas instâncias do SQL Server, incluindo a instalação do LocalDB. Na caixa de diálogo **Conectar-se ao Servidor** do SMSS, especifique `(localdb)\MSSQLLocalDb` no campo **Nome do servidor:** para se conectar à instância do LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Autenticando solicitações no emulador de armazenamento

Depois de instalar e iniciar o emulador de armazenamento, você pode testar seu código em relação a ele. Cada solicitação feita no emulador de armazenamento deve ser autorizada, a menos que seja uma solicitação anônima. Você pode autorizar solicitações no emulador de armazenamento usando a autenticação de chave compartilhada ou com uma SAS (assinatura de acesso compartilhado).

### <a name="authorize-with-shared-key-credentials"></a>Autorizar com credenciais de Chave Compartilhada

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Para obter mais informações sobre cadeias de conexão, consulte [Configurar cadeias de conexão do Armazenamento do Azure](./storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Autorizar com assinatura de acesso compartilhado

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Algumas bibliotecas de cliente de armazenamento do Azure, como a biblioteca do Xamarin, só oferecem suporte à autenticação com um token SAS(Assinatura de Acesso Compartilhado). Você pode criar o token SAS usando [Gerenciador de armazenamento](https://storageexplorer.com/) ou outro aplicativo que ofereça suporte à autenticação de chave compartilhada.

Você também pode gerar um token SAS usando o Azure PowerShell. O exemplo a seguir gera um token SAS com permissões totais em um contêiner de blob:

1. Instale o Azure PowerShell se você ainda não tiver feito isso (recomendamos o uso da versão mais recente do cmdlet do Azure PowerShell). Para obter instruções de instalação, consulte [Instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps).
2. Abra o Azure PowerShell e execute os seguintes comandos, substituindo `CONTAINER_NAME` por um nome de sua escolha:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

O URI da assinatura de acesso compartilhado resultante do novo contêiner deve ser semelhante ao seguinte:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

A assinatura de acesso compartilhado criada com este exemplo é válida por um dia. A assinatura concede acesso completo (leitura, gravação, exclusão e lista) para os blobs do contêiner.

Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](storage-sas-overview.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Endereçando recursos no emulador de armazenamento

Os pontos de extremidade de serviço do emulador de armazenamento são diferentes dos pontos de extremidade de uma conta de armazenamento do Azure. O computador local não faz a resolução de nomes de domínio, exigindo que os pontos de extremidade do emulador de armazenamento sejam endereços locais.

Ao lidar com um recurso em uma conta de armazenamento do Azure, use o esquema a seguir. O nome da conta faz parte do nome do host do URI e o recurso em questão faz parte do caminho do URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Por exemplo, o seguinte URI é um endereço válido para um blob em uma conta de armazenamento do Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Como o computador local não faz a resolução de nomes de domínio, o nome da conta faz parte do caminho do URI em vez do nome do host. Use o seguinte formato de URI para um recurso no emulador de armazenamento:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Por exemplo, o endereço a seguir pode ser usado para acessar um blob no emulador de armazenamento:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Os pontos de extremidade de serviço do emulador de armazenamento são:

* Serviço Blob: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Serviço Fila: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Serviço Tabela: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Endereçamento da conta secundária com RA-GRS

A partir da versão 3,1, o emulador de armazenamento dá suporte à replicação com redundância geográfica com acesso de leitura (RA-GRS). Você pode acessar o local secundário acrescentando-secundário ao nome da conta. Por exemplo, o endereço a seguir pode ser usado para acessar um BLOB usando o secundário somente leitura no emulador de armazenamento:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Para acesso programático ao secundário com o emulador de armazenamento, use a biblioteca de cliente de armazenamento para .NET versão 3,2 ou posterior. Consulte a [Biblioteca de Clientes do Armazenamento do Microsoft Azure para .NET](/previous-versions/azure/dn261237(v=azure.100)) para ver mais detalhes.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Referência da ferramenta de linha de comando do emulador de armazenamento

A partir da versão 3.0, uma janela do console é exibida quando você inicia o Emulador de Armazenamento. Use a linha de comando na janela do console para iniciar e parar o emulador. Você também pode consultar o status e fazer outras operações na linha de comando.

> [!NOTE]
> Se você tiver o emulador de computação Microsoft Azure instalado, um ícone de bandeja do sistema aparecerá quando você iniciar o emulador de armazenamento. Clique com o botão direito do mouse no ícone para revelar um menu, que fornece uma maneira gráfica de iniciar e parar o Emulador de Armazenamento.
>
>

### <a name="command-line-syntax"></a>Sintaxe da linha de comando

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Opções

Para exibir a lista de opções, digite `/help` no prompt de comando.

| Opção | Descrição | Comando | Argumentos |
| --- | --- | --- | --- |
| **Iniciar** |Inicia o emulador de armazenamento. |`AzureStorageEmulator.exe start [-inprocess]` |*-Reprocessar*: Inicie o emulador no processo atual em vez de criar um novo processo. |
| **Parar** |Interrompe o emulador de armazenamento. |`AzureStorageEmulator.exe stop` | |
| **Status** |Imprime o status do emulador de armazenamento. |`AzureStorageEmulator.exe status` | |
| **Limpar** |Limpa os dados em todos os serviços especificados na linha de comando. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*blob*: limpa os dados do blob. <br/>*fila*: limpa os dados da fila. <br/>*tabela*: limpa os dados de tabela. <br/>*todos*: limpa todos os dados em todos os serviços. |
| **Init** |Executa a inicialização única para configurar o emulador. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server nomedoServidor\nomedaInstância*: especifica o servidor que hospeda a instância do SQL. <br/>*-sqlinstance instanceName*: especifica o nome da instância SQL a ser usada na instância do servidor padrão. <br/>*-forcecreate*: força a criação do Banco de Dados SQL, mesmo se ele já existir. <br/>*-skipcreate*: ignora a criação do Banco de Dados SQL. Isso tem precedência sobre -forcecreate.<br/>*-reserveports*: tenta reservar as portas HTTP associadas aos serviços.<br/>*-unreserveports*: tenta remover as reservas de portas HTTP associadas aos serviços. Isso tem precedência sobre -reserveports.<br/>*-inprocess*: executa a inicialização no processo atual em vez de gerar um novo processo. O processo atual deverá ser iniciado com permissões elevadas se reservas de porta forem alteradas. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Diferenças entre o emulador de armazenamento e o armazenamento do Azure

Como o emulador de armazenamento é um ambiente emulado local, há diferenças entre usar o emulador e uma conta de armazenamento do Azure na nuvem:

* O emulador de armazenamento dá suporte a apenas uma única conta fixa e uma chave de autenticação conhecida.
* O emulador de armazenamento não é um serviço de armazenamento escalonável e não dá suporte a um grande número de clientes simultâneos.
* Conforme descrito em [endereçando recursos no emulador de armazenamento](#addressing-resources-in-the-storage-emulator), os recursos são tratados de forma diferente no emulador de armazenamento em comparação com uma conta de armazenamento do Azure. A diferença é porque a resolução de nomes de domínio está disponível na nuvem, mas não no computador local.
* A partir da versão 3,1, a conta do emulador de armazenamento dá suporte à replicação com redundância geográfica com acesso de leitura (RA-GRS). No emulador, todas as contas têm o RA-GRS habilitado e nunca há qualquer retardo entre as réplicas primária e secundária. As operações Obter Estatísticas do Serviço Blob, Obter Estatísticas do Serviço Fila e Obter Estatísticas do Serviço Tabela são compatíveis com a conta secundária e sempre retornarão o valor do elemento da resposta `LastSyncTime` , como a hora atual, de acordo com o Banco de Dados SQL subjacente.
* Atualmente, os pontos de extremidade do serviço de arquivo e do protocolo SMB não têm suporte no emulador de armazenamento.
* Se você usar uma versão dos serviços de armazenamento que não tem suporte no emulador, o emulador retornará um erro VersionNotSupportedByEmulator (código de status HTTP 400-solicitação inválida).

### <a name="differences-for-blob-storage"></a>Diferenças do armazenamento de blob

As diferenças a seguir aplicam-se ao armazenamento de blob no emulador:

* O emulador de armazenamento dá suporte apenas a tamanhos de blob de até 2 GB.
* O comprimento máximo de um nome de blob no emulador de armazenamento é de 256 caracteres, enquanto o comprimento máximo de um nome de blob no armazenamento do Azure é de 1024 caracteres.
* A cópia incremental permite que os instantâneos de blobs substituídos sejam copiados, o que retorna uma falha no serviço.
* A comparação de obter intervalos de página não funciona entre instantâneos copiados usando blob de cópia incremental.
* Uma operação Put Blob pode ter sucesso em um blob que existe no emulador de armazenamento com uma concessão ativa, mesmo que a ID da concessão não tenha sido especificada na solicitação.
* As operações de blob de acréscimo não são suportadas pelo emulador. Tentar uma operação em um blob de anexo retornará um erro FeatureNotSupportedByEmulator (código de status HTTP 400 - Solicitação incorreta).

### <a name="differences-for-table-storage"></a>Diferenças do armazenamento de tabela

As diferenças a seguir aplicam-se ao armazenamento de tabela no emulador:

* As propriedades de data no serviço tabela no emulador de armazenamento dão suporte apenas ao intervalo suportado pelo SQL Server 2005 (eles precisam ser posteriores a 1º de janeiro de 1753). Todas as datas anteriores a 1º de janeiro de 1753 são alteradas para esse valor. A precisão das datas é limitada à precisão do SQL Server 2005, o que significa que as datas são precisas a 1/300 de um segundo.
* O emulador de armazenamento oferece suporte a valores de propriedade de chave de partição e chaves de linha de menos de 512 bytes. O tamanho total do nome da conta, do nome da tabela e dos nomes de propriedade de chave juntos não pode exceder 900 bytes.
* O tamanho total de uma linha em uma tabela no emulador de armazenamento é limitado a menos de 1 MB.
* No emulador de armazenamento, as propriedades do tipo de dados `Edm.Guid` ou `Edm.Binary` oferecem suporte apenas aos `Equal (eq)` operadores de `NotEqual (ne)` comparação e nas cadeias de caracteres de filtro de consulta.

### <a name="differences-for-queue-storage"></a>Diferenças do armazenamento de fila

Não existem diferenças específicas para o armazenamento de fila no emulador.

## <a name="storage-emulator-release-notes"></a>Notas de versão do emulador de armazenamento

### <a name="version-510"></a>Versão 5,10

* O emulador de armazenamento não rejeitará a versão 2019-07-07 dos serviços de armazenamento em pontos de extremidade de serviço de BLOB, fila e tabela.

### <a name="version-59"></a>Versão 5,9

* O emulador de armazenamento não rejeitará a versão 2019-02-02 dos serviços de armazenamento em pontos de extremidade de serviço de BLOB, fila e tabela.

### <a name="version-58"></a>Versão 5,8

* O emulador de armazenamento não rejeitará a versão 2018-11-09 dos serviços de armazenamento em pontos de extremidade de serviço de BLOB, fila e tabela.

### <a name="version-57"></a>Versão 5.7

* Corrigido um erro que causaria uma falha se o log fosse ativado.

### <a name="version-56"></a>Versão 5.6

* O emulador de armazenamento agora dá suporte à versão 2018-03-28 dos serviços de armazenamento em pontos de extremidade de serviço de BLOB, fila e tabela.

### <a name="version-55"></a>Versão 5.5

* O emulador de armazenamento agora dá suporte à versão 2017-11-09 dos serviços de armazenamento em pontos de extremidade de serviço de BLOB, fila e tabela.
* Suporte foi adicionado para a propriedade **Criada** do blob, que retorna o tempo de criação do blob.

### <a name="version-54"></a>Versão 5.4

* Para melhorar a estabilidade da instalação, o emulador não tenta mais reservar portas no momento da instalação. Se você quiser reservas de porta, use a opção *-reserveports* do comando **init** para especificá-las.

### <a name="version-53"></a>Versão 5.3

* O emulador de armazenamento agora dá suporte à versão 2017-07-29 dos serviços de armazenamento em pontos de extremidade de serviço de BLOB, fila e tabela.

### <a name="version-52"></a>Versão 5.2

* O emulador de armazenamento agora dá suporte à versão 2017-04-17 dos serviços de armazenamento em pontos de extremidade de serviço de BLOB, fila e tabela.
* Corrigido um bug em que os valores de propriedade da tabela não estavam sendo codificados corretamente.

### <a name="version-51"></a>Versão 5.1

* Corrigido um bug em que o emulador de armazenamento estava retornando o `DataServiceVersion` cabeçalho em algumas respostas em que o serviço não estava.

### <a name="version-50"></a>Versão 5.0

* O instalador do emulador de armazenamento não verifica mais se há instalações do MSSQL e do .NET Framework existentes.
* O instalador do emulador de armazenamento não cria mais o banco de dados como parte da instalação. O banco de dados ainda será criado como parte da inicialização se necessário.
* A criação de banco de dados não requer elevação.
* Reservas de porta não são mais necessários para a inicialização.
* Adiciona as seguintes opções para `init`: `-reserveports` (exige a elevação), `-unreserveports` (exige a elevação), `-skipcreate`.
* A opção de interface do usuário do emulador de armazenamento no ícone da bandeja do sistema agora inicia a interface de linha de comando. A GUI antiga não está mais disponível.
* Algumas DLLs foram removidas ou renomeadas.

### <a name="version-46"></a>Versão 4.6

* O emulador de armazenamento agora dá suporte à versão 2016-05-31 dos serviços de armazenamento em pontos de extremidade de serviço de BLOB, fila e tabela.

### <a name="version-45"></a>Versão 4.5

* Correção de um bug que causou a falha na instalação e inicialização quando o banco de dados de backup é renomeado.

### <a name="version-44"></a>Versão 4.4

* O emulador de armazenamento agora dá suporte à versão 2015-12-11 dos serviços de armazenamento em pontos de extremidade de serviço de BLOB, fila e tabela.
* A coleta de lixo do emulador de armazenamento de dados de blob agora é mais eficiente ao lidar com grandes números de BLOBs.
* Foi corrigido um bug que fazia o contêiner ACL XML ser validado de forma ligeiramente diferente de como o serviço de armazenamento o faz.
* Foi corrigido um bug que às vezes fazia com que valores máx. e mín. de DateTime fossem relatados no fuso horário incorreto.

### <a name="version-43"></a>Versão 4.3

* O emulador de armazenamento agora dá suporte à versão 2015-07-08 dos serviços de armazenamento em pontos de extremidade de serviço de BLOB, fila e tabela.

### <a name="version-42"></a>Versão 4.2

* O emulador de armazenamento agora dá suporte à versão 2015-04-05 dos serviços de armazenamento em pontos de extremidade de serviço de BLOB, fila e tabela.

### <a name="version-41"></a>Versão 4.1

* O emulador de armazenamento agora dá suporte à versão 2015-02-21 dos serviços de armazenamento em pontos de extremidade de serviço de BLOB, fila e tabela. Ele não dá suporte aos novos recursos de blob de acréscimo.
* O emulador agora retorna uma mensagem de erro significativa para versões sem suporte dos serviços de armazenamento. É recomendável usar a versão mais recente do emulador. Se você receber um erro VersionNotSupportedByEmulator (código de status HTTP 400-solicitação inadequada), baixe a versão mais recente do emulador.
* Correção do bug em que uma condição de corrida fazia com que dados de entidade de tabela ficassem incorretos durante operações de mesclagem simultâneas.

### <a name="version-40"></a>Versão 4.0

* O executável do emulador de armazenamento foi renomeado para *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Versão 3.2

* O emulador de armazenamento agora dá suporte à versão 2014-02-14 dos serviços de armazenamento em pontos de extremidade de serviço de BLOB, fila e tabela. Atualmente, não há suporte para pontos de extremidade de serviço de arquivo no emulador de armazenamento. Consulte [Controle de versão dos serviços de armazenamento do Azure](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) para obter detalhes sobre a versão 2014-02-14.

### <a name="version-31"></a>Versão 3.1

* O armazenamento com redundância geográfica com acesso de leitura (RA-GRS) agora tem suporte no emulador de armazenamento. As `Get Blob Service Stats` `Get Queue Service Stats` APIs,, e `Get Table Service Stats` têm suporte para a conta secundária e sempre retornarão o valor do elemento de resposta LastSyncTime como a hora atual de acordo com o banco de dados SQL subjacente. Para acesso programático ao secundário com o emulador de armazenamento, use a biblioteca de cliente de armazenamento para .NET versão 3,2 ou posterior. Consulte a Referência da Biblioteca de Clientes do Armazenamento do Microsoft Azure para .NET para ver mais detalhes.

### <a name="version-30"></a>Versão 3.0

* O emulador de armazenamento do Azure não é mais fornecido no mesmo pacote que o emulador de computação.
* A interface gráfica do usuário do emulador de armazenamento foi preterida. Ele foi substituído por uma interface de linha de comando programável por scripts. Para obter detalhes sobre a interface de linha de comando consulte Referência da ferramenta de linha de comando do emulador de armazenamento. A interface gráfica continuará presente na versão 3.0, mas só poderá ser acessada quando o emulador de computação for instalado, clicando com o botão direito no ícone de bandeja do sistema e selecionando Mostrar IU do Emulador de Armazenamento.
* A versão 2013-08-15 dos serviços de armazenamento do Azure agora tem total suporte. (Anteriormente nesta versão só tinha suporte do emulador de armazenamento versão 2.2.1 Preview.)

## <a name="next-steps"></a>Próximas etapas

* Avalie o [azurite](https://github.com/azure/azurite)emulador de armazenamento de software livre de plataforma cruzada, mantido pela Comunidade. 
* [Exemplos de Armazenamento do Azure usando .NET](./storage-samples-dotnet.md) contém links para vários exemplos de código que você pode usar ao desenvolver seu aplicativo.
* Você pode usar o [Gerenciador de armazenamento do Microsoft Azure](https://storageexplorer.com) para trabalhar com recursos em sua conta de armazenamento em nuvem e no emulador de armazenamento.

## <a name="see-also"></a>Consulte Também

* [Desenvolvimento local do armazenamento do Azure com azurite, SDKs do Azure e Gerenciador de Armazenamento do Azure](https://blog.jongallant.com/2020/04/local-azure-storage-development-with-azurite-azuresdks-storage-explorer/)
