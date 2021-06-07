---
title: Usar o emulador azurite para o desenvolvimento de armazenamento local do Azure
description: O emulador de código-fonte aberto do azurite fornece um ambiente local gratuito para testar seus aplicativos de armazenamento do Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/15/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: b5051a5e29f9e34c43963a9a264ee47e2ad7b04a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92490774"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development"></a>Usar o emulador azurite para o desenvolvimento de armazenamento local do Azure

O emulador de código-fonte aberto do azurite fornece um ambiente local gratuito para testar seus aplicativos de armazenamento de BLOBs e filas do Azure. Quando estiver satisfeito com o modo como seu aplicativo está funcionando localmente, alterne para o usando uma conta de armazenamento do Azure na nuvem. O emulador fornece suporte de plataforma cruzada no Windows, Linux e macOS.

Azurite é a plataforma de emulador de armazenamento futura. Azurite substitui o [emulador de armazenamento do Azure](storage-use-emulator.md). O azurite continuará a ser atualizado para dar suporte às versões mais recentes das APIs de armazenamento do Azure.

Há várias maneiras diferentes de instalar e executar o azurite em seu sistema local:

  1. [Instalar e executar a extensão de Visual Studio Code azurite](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Instalar e executar o azurite usando o NPM](#install-and-run-azurite-by-using-npm)
  1. [Instalar e executar a imagem do Docker azurite](#install-and-run-the-azurite-docker-image)
  1. [Clonar, compilar e executar azurite do repositório GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Instalar e executar a extensão de Visual Studio Code azurite

Em Visual Studio Code, selecione o painel **extensões** e procure *azurite* nas **extensões: MARKETPLACE**.

![Marketplace de extensões de Visual Studio Code](media/storage-use-azurite/azurite-vs-code-extension.png)

Você também pode navegar até [Visual Studio Code mercado de extensão](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) em seu navegador. Selecione o botão **instalar** para abrir Visual Studio Code e vá diretamente para a página extensão azurite.

A extensão oferece suporte aos seguintes comandos de Visual Studio Code. Para abrir a paleta de comandos, pressione F1 em Visual Studio Code. 

   - **Azurite: Clean** -redefinir todos os serviços do azurite persistência dados
   - **Azurite: limpar serviço blob** -limpar serviço blob
   - **Azurite: limpar serviço de fila** -serviço de fila de limpeza
   - **Azurite: fechar** todos os serviços do azurite
   - **Azurite: fechar serviço blob** -fechar serviço blob
   - **Azurite: fechar serviço de fila** -fechar serviço de fila
   - **Azurite: iniciar** todos os serviços do azurite
   - **Azurite: iniciar serviço blob** -iniciar serviço blob
   - **Azurite: iniciar serviço de fila** -iniciar serviço de fila

Para configurar o azurite em Visual Studio Code, selecione o painel extensões. Selecione o ícone **gerenciar** (engrenagem) para **azurite**. Selecione **configurações de extensão**.

![Azurites definir configurações de extensão](media/storage-use-azurite/azurite-configure-extension-settings.png)

Há suporte para as seguintes configurações:

   - **Azurite: host de blob** -o ponto de extremidade de escuta do serviço BLOB. A configuração padrão é 127.0.0.1.
   - **Azurite: porta de blob** -a porta de escuta do serviço BLOB. A porta padrão é 10000.
   - **Azurite: CERT** -Path para um local de arquivo de certificado do PEM ou pfx confiável localmente para habilitar o modo HTTPS.
   - **Azurite: Depurar** -saída do log de depuração para o canal azurite. O valor padrão é **false**.
   - **Azurite: Key** -Path para um arquivo de chave PEM confiável localmente, necessário quando **azurite: CERT** aponta para um arquivo PEM.
   - **Azurite: Location** -o caminho do local do espaço de trabalho. O padrão é a pasta de trabalho Visual Studio Code.
   - **Azurite: flexível** – habilita o modo livre, que ignora os cabeçalhos e os parâmetros sem suporte.
   - **Azurite: OAuth** – nível OAuth opcional.
   - **Azurite: pwd** -senha para o arquivo PFX. Necessário quando **azurite: o certificado** aponta para um arquivo PFX.
   - **Azurite: host de fila** -o ponto de extremidade de escuta serviço fila. A configuração padrão é 127.0.0.1.
   - **Azurite: porta da fila** -a porta de escuta do serviço fila. A porta padrão é 10001.
   - **Azurite:** o modo silencioso-silencioso desabilita o log de acesso. O valor padrão é **false**.
   - **Azurite: ignorar verificação de versão da API** -ignorar a verificação de versão da API de solicitação. O valor padrão é **false**.

## <a name="install-and-run-azurite-by-using-npm"></a>Instalar e executar o azurite usando o NPM

Esse método de instalação requer que você tenha [Node.js versão 8,0 ou posterior](https://nodejs.org) instalada. O Gerenciador de pacotes de nó (NPM) é a ferramenta de gerenciamento de pacotes incluída em cada instalação do Node.js. Depois de instalar o Node.js, execute o comando a seguir `npm` para instalar o azurite.

```console
npm install -g azurite
```

Depois de instalar o azurite, consulte [executar o azurite de uma linha de comando](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Instalar e executar a imagem do Docker azurite

Use o [DockerHub](https://hub.docker.com/) para efetuar pull da [imagem azurite mais recente](https://hub.docker.com/_/microsoft-azure-storage-azurite) usando o seguinte comando:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Execute a imagem do Docker azurite**:

O comando a seguir executa a imagem do Docker azurite. O `-p 10000:10000` parâmetro redireciona as solicitações da porta 10000 da máquina host para a instância do Docker.

```console
docker run -p 10000:10000 -p 10001:10001 \
    mcr.microsoft.com/azure-storage/azurite
```

**Especifique o local do espaço de trabalho**:

No exemplo a seguir, o `-v c:/azurite:/data` parâmetro especifica *c:/azurite* como o local de dados persistentes azurite. O diretório, *c:/azurite*, deve ser criado antes da execução do comando Docker.

```console
docker run -p 10000:10000 -p 10001:10001 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Executar apenas o serviço blob**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

Para obter mais informações sobre como configurar o azurite na inicialização, consulte [Opções de linha de comando](#command-line-options).

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Clonar, compilar e executar azurite do repositório GitHub

Esse método de instalação requer que você tenha o [git](https://git-scm.com/) instalado. Clone o [repositório do GitHub](https://github.com/azure/azurite) para o projeto azurite usando o comando do console a seguir.

```console
git clone https://github.com/Azure/Azurite.git
```

Depois de clonar o código-fonte, execute os comandos a seguir da raiz do repositório clonado para compilar e instalar o azurite.

```console
npm install
npm run build
npm install -g
```

Depois de instalar e compilar o azurite, consulte [executar o azurite de uma linha de comando](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Executar azurite de uma linha de comando

> [!NOTE]
> Azurite não pode ser executado na linha de comando se você instalou apenas a extensão Visual Studio Code. Em vez disso, use a paleta de comandos Visual Studio Code. Para obter mais informações, consulte [instalar e executar a extensão de Visual Studio Code azurite](#install-and-run-the-azurite-visual-studio-code-extension).

Para começar imediatamente com a linha de comando, crie um diretório chamado *c:\azurite* e, em seguida, inicie o azurite emitindo o seguinte comando:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Esse comando informa ao azurite para armazenar todos os dados em um diretório específico, *c:\azurite*. Se a `--location` opção for omitida, ela usará o diretório de trabalho atual.

## <a name="command-line-options"></a>Opções de linha de comando

Esta seção detalha as opções de linha de comando disponíveis ao iniciar o azurite.

### <a name="help"></a>Help

**Opcional** -Obtenha ajuda de linha de comando usando a `-h` `--help` opção ou.

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>Host de escuta de BLOB

**Opcional** – por padrão, azurite escutará 127.0.0.1 como o servidor local. Use a `--blobHost` opção para definir o endereço para seus requisitos.

Aceitar solicitações somente no computador local:

```console
azurite --blobHost 127.0.0.1
```

Permitir solicitações remotas:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Permitir solicitações remotas pode tornar o sistema vulnerável a ataques externos.

### <a name="blob-listening-port-configuration"></a>Configuração da porta de escuta de BLOB

**Opcional** – por padrão, o azurite escutará o serviço blob na porta 10000. Use a `--blobPort` opção para especificar a porta de escuta que você precisa.

> [!NOTE]
> Depois de usar uma porta personalizada, você precisa atualizar a cadeia de conexão ou a configuração correspondente em suas ferramentas de armazenamento do Azure ou SDKs.

Personalizar a porta de escuta do serviço blob:

```console
azurite --blobPort 8888
```

Permitir que o sistema selecione automaticamente uma porta disponível:

```console
azurite --blobPort 0
```

A porta em uso é exibida durante a inicialização do azurite.

### <a name="queue-listening-host"></a>Host de escuta de fila

**Opcional** – por padrão, azurite escutará 127.0.0.1 como o servidor local. Use a `--queueHost` opção para definir o endereço para seus requisitos.

Aceitar solicitações somente no computador local:

```console
azurite --queueHost 127.0.0.1
```

Permitir solicitações remotas:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Permitir solicitações remotas pode tornar o sistema vulnerável a ataques externos.

### <a name="queue-listening-port-configuration"></a>Configuração da porta de escuta da fila

**Opcional** – por padrão, o azurite escutará a serviço fila na porta 10001. Use a `--queuePort` opção para especificar a porta de escuta que você precisa.

> [!NOTE]
> Depois de usar uma porta personalizada, você precisa atualizar a cadeia de conexão ou a configuração correspondente em suas ferramentas de armazenamento do Azure ou SDKs.

Personalizar a porta de escuta do serviço Fila:

```console
azurite --queuePort 8888
```

Permitir que o sistema selecione automaticamente uma porta disponível:

```console
azurite --queuePort 0
```

A porta em uso é exibida durante a inicialização do azurite.

### <a name="workspace-path"></a>Caminho do espaço de trabalho

**Opcional** -azurite armazena dados no disco local durante a execução. Use a `-l` `--location` opção ou para especificar um caminho como o local do espaço de trabalho. Por padrão, o diretório de trabalho do processo atual será usado. Observe o ' L' minúsculo.

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>Log de acesso

**Opcional** – por padrão, o log de acesso é exibido na janela do console. Desabilite a exibição do log de acesso usando a `-s` `--silent` opção ou.

```console
azurite -s
azurite --silent
```
### <a name="debug-log"></a>Log de depuração

**Opcional** -o log de depuração inclui informações detalhadas sobre cada solicitação e rastreamento de pilha de exceção. Habilite o log de depuração fornecendo um caminho de arquivo local válido para a `-d` `--debug` opção ou.

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>Modo livre

**Opcional** – por padrão, azurite aplica o modo estrito para bloquear os cabeçalhos de solicitação e os parâmetros sem suporte. Desabilite o modo estrito usando `-L` a `--loose` opção ou. Observe a letra maiúscula ' L'.

```console
azurite -L
azurite --loose
```
### <a name="version"></a>Versão

**Opcional** – exiba o número de versão do azurite instalado usando `-v` a `--version` opção ou.

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>Configuração de certificado (HTTPS)

**Opcional** – por padrão, azurite usa o protocolo http. Habilite o modo HTTPS fornecendo um caminho para um arquivo de certificado Privacy Enhanced Mail (. pem) ou [troca de informações pessoais (. pfx)](/windows-hardware/drivers/install/personal-information-exchange---pfx--files) para o `--cert` comutador.

Quando `--cert` é fornecido para um arquivo PEM, você deve fornecer um `--key` comutador correspondente.

```console
azurite --cert path/server.pem --key path/key.pem
```

Quando `--cert` é fornecido para um arquivo PFX, você deve fornecer um `--pwd` comutador correspondente.

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

Para obter informações detalhadas sobre como criar arquivos PEM e PFX, consulte [configuração de HTTPS](https://github.com/Azure/Azurite/blob/master/README.md#https-setup).

### <a name="oauth-configuration"></a>Configuração do OAuth

**Opcional** -habilite a autenticação OAuth para azurite usando a `--oauth` opção.

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> O OAuth requer um ponto de extremidade HTTPS. Certifique-se de que HTTPS esteja habilitado fornecendo `--cert` switch juntamente com a `--oauth` opção.

O azurite dá suporte à autenticação básica especificando o `basic` parâmetro para o `--oauth` comutador. Azurite fará a autenticação básica, como validar o token de portador de entrada, verificar o emissor, o público e a expiração. Azurite não verificará a assinatura ou as permissões do token.

### <a name="skip-api-version-check"></a>Ignorar verificação de versão da API

**Opcional** – ao iniciar, azurite verifica se a versão de API solicitada é válida. O comando a seguir ignora a verificação de versão da API:

```console
azurite --skipApiVersionCheck
```


## <a name="authorization-for-tools-and-sdks"></a>Autorização para ferramentas e SDKs

Conecte-se ao azurite de SDKs ou ferramentas do armazenamento do Azure, como [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), usando qualquer estratégia de autenticação. A autenticação é necessária. O azurite dá suporte à autorização com OAuth, chave compartilhada e SAS (assinaturas de acesso compartilhado). O azurite também dá suporte ao acesso anônimo a contêineres públicos.

Se você estiver usando os SDKs do Azure, inicie o azurite com as `--oauth basic and --cert --key/--pwd` opções.

### <a name="well-known-storage-account-and-key"></a>Chave e conta de armazenamento bem conhecidas

O azurite aceita a mesma conta e chave conhecidas usados pelo emulador de armazenamento do Azure herdado.

- Nome da conta: `devstoreaccount1`
- Chave de conta: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>Chaves e contas de armazenamento personalizadas

O azurite dá suporte a chaves e nomes de conta de armazenamento personalizados, definindo a `AZURITE_ACCOUNTS` variável de ambiente no seguinte formato: `account1:key1[:key2];account2:key1[:key2];...` .

Por exemplo, use uma conta de armazenamento personalizada que tenha uma chave:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

```bash
export AZURITE_ACCOUNTS="account1:key1"
```

Ou use várias contas de armazenamento com duas chaves cada:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

```bash
export AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

O azurite atualiza nomes de conta e chaves personalizadas da variável de ambiente a cada minuto, por padrão. Com esse recurso, você pode girar dinamicamente a chave de conta ou adicionar novas contas de armazenamento sem reiniciar o azurite.

> [!NOTE]
> A `devstoreaccount1` conta de armazenamento padrão é desabilitada quando você define contas de armazenamento personalizadas.

### <a name="connection-strings"></a>Cadeias de conexão

A maneira mais fácil de se conectar ao azurite de seu aplicativo é configurar uma cadeia de conexão no arquivo de configuração do aplicativo que faz referência ao atalho *UseDevelopmentStorage = true*. Aqui está um exemplo de uma cadeia de conexão em um arquivo de *app.config* :

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>Cadeias de conexão HTTP

Você pode passar as seguintes cadeias de conexão para os SDKs ou ferramentas [do Azure](https://aka.ms/azsdk) , como CLI do Azure 2,0 ou Gerenciador de armazenamento.

A cadeia de conexão completa é:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

Para se conectar somente ao serviço BLOB, a cadeia de conexão é:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

Para se conectar somente ao serviço fila, a cadeia de conexão é:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>Cadeias de conexão HTTPS

A cadeia de conexão HTTPS completa é:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Para usar apenas o serviço BLOB, a cadeia de conexão HTTPS é:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

Para usar apenas o serviço fila, a cadeia de conexão HTTPS é:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Se você usou `dotnet dev-certs` para gerar seu certificado autoassinado, use a cadeia de conexão a seguir.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

Atualize a cadeia de conexão ao usar as [chaves e contas de armazenamento personalizadas](#custom-storage-accounts-and-keys).

Para saber mais, confira [Configurar cadeias de conexão do Armazenamento do Azure](storage-configure-connection-string.md).

### <a name="azure-sdks"></a>SDKs do Azure

Para usar o azurite com os [SDKs do Azure](https://aka.ms/azsdk), use as opções de OAuth e https:

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Armazenamento do Blobs do Azure

Em seguida, você pode criar uma instância de um BlobContainerClient, BlobServiceClient ou BlobClient.

```csharp
// With container URL and DefaultAzureCredential
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new BlobContainerClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name"
  );

// With account name and key
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

#### <a name="azure-queue-storage"></a>Armazenamento de Filas do Azure

Você também pode criar uma instância de um QueueClient ou QueueServiceClient.

```csharp
// With queue URL and DefaultAzureCredential
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new QueueClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name"
  );

// With account name and key
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

### <a name="microsoft-azure-storage-explorer"></a>Gerenciador do Armazenamento do Microsoft Azure

Você pode usar Gerenciador de Armazenamento para exibir os dados armazenados em azurite.

#### <a name="connect-to-azurite-using-http"></a>Conectar-se ao azurite usando HTTP

Em Gerenciador de Armazenamento, conecte-se ao azurite seguindo estas etapas:

 1. Selecione o ícone **gerenciar contas**
 1. Selecione **Adicionar uma conta**
 1. Selecione **anexar a um emulador local**
 1. Selecione **Avançar**
 1. Edite o campo **nome de exibição** para um nome de sua escolha
 1. Selecione **Avançar** novamente
 1. Selecione **conectar**

#### <a name="connect-to-azurite-using-https"></a>Conectar-se ao azurite usando HTTPS

Por padrão Gerenciador de Armazenamento não abrirá um ponto de extremidade HTTPS que usa um certificado autoassinado. Se você estiver executando o azurite com HTTPS, provavelmente está usando um certificado autoassinado. Em Gerenciador de armazenamento, importe certificados SSL por meio da caixa de diálogo **Editar**  ->  certificados de importação de **certificados SSL**  ->   .

##### <a name="import-certificate-to-storage-explorer"></a>Importar certificado para Gerenciador de Armazenamento

1. Localize o certificado no computador local.
1. Em Gerenciador de armazenamento, vá para **Editar**  ->  **certificados SSL**  ->  **importar certificados** e importar seu certificado.

Se você não importar um certificado, obterá um erro:

`unable to verify the first certificate` ou `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>Adicionar azurite via cadeia de conexão HTTPS

Siga estas etapas para adicionar azurite HTTPS ao Gerenciador de Armazenamento:

1. Selecione **alternar Gerenciador**
1. Selecionar **& locais anexados**
1. Clique com o botão direito do mouse em **contas de armazenamento** e selecione **conectar ao armazenamento do Azure**.
1. Selecione **usar uma cadeia de conexão**
1. Selecione **Avançar**.
1. Insira um valor no campo **nome de exibição** .
1. Insira a [cadeia de conexão HTTPS](#https-connection-strings) da seção anterior deste documento
1. Selecione **Avançar**
1. Selecione **conectar**

## <a name="workspace-structure"></a>Estrutura do espaço de trabalho

Os seguintes arquivos e pastas podem ser criados no local do espaço de trabalho ao inicializar o azurite.

- `__blobstorage__` -Diretório contendo dados binários persistentes do serviço blob azurite
- `__queuestorage__` -Diretório contendo dados binários persistentes do serviço fila de azurite
- `__azurite_db_blob__.json` -Arquivo de metadados do serviço blob azurite
- `__azurite_db_blob_extent__.json` -Arquivo de metadados de extensão do serviço blob azurite
- `__azurite_db_queue__.json` -Arquivo de metadados do serviço fila de azurite
- `__azurite_db_queue_extent__.json` -Arquivo de metadados de extensão de serviço da fila azurite

Para limpar o azurite, exclua os arquivos e as pastas acima e reinicie o emulador.

## <a name="differences-between-azurite-and-azure-storage"></a>Diferenças entre o azurite e o armazenamento do Azure

Há diferenças funcionais entre uma instância local do azurite e uma conta de armazenamento do Azure na nuvem.

### <a name="endpoint-and-connection-url"></a>Ponto de extremidade e URL de conexão

Os pontos de extremidade de serviço para azurite são diferentes dos pontos de extremidade de uma conta de armazenamento do Azure. O computador local não faz a resolução de nomes de domínio, exigindo que os pontos de extremidade azurite sejam endereços locais.

Quando você endereça um recurso em uma conta de armazenamento do Azure, o nome da conta faz parte do nome de host do URI. O recurso que está sendo endereçado faz parte do caminho do URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

O URI a seguir é um endereço válido para um blob em uma conta de armazenamento do Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Como o computador local não faz a resolução de nomes de domínio, o nome da conta faz parte do caminho do URI em vez do nome do host. Use o seguinte formato de URI para um recurso no azurite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

O endereço a seguir pode ser usado para acessar um blob no azurite:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Dimensionamento e desempenho

O azurite não dá suporte a um grande número de clientes conectados. Não há garantia de desempenho. O azurite destina-se a fins de desenvolvimento e teste.

### <a name="error-handling"></a>Tratamento de erros

O azurite está alinhado com a lógica de tratamento de erros do armazenamento do Azure, mas há diferenças. Por exemplo, as mensagens de erro podem ser diferentes, enquanto os códigos de status de erro são alinhados.

### <a name="ra-grs"></a>RA-GRS

O azurite dá suporte à replicação com redundância geográfica com acesso de leitura (RA-GRS). Para recursos de armazenamento, acesse o local secundário anexando `-secondary` ao nome da conta. Por exemplo, o endereço a seguir pode ser usado para acessar um BLOB usando o secundário somente leitura no azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

### <a name="table-support"></a>Suporte a tabelas

O suporte para tabelas no azurite está atualmente em desenvolvimento e aberto para contribuição! Para obter o progresso mais recente, verifique o projeto de [tabela azurite v3](https://github.com/Azure/Azurite/wiki/Azurite-V3-Table) .

O suporte para funções duráveis requer tabelas.

## <a name="azurite-is-open-source"></a>Azurite é código-fonte aberto

As contribuições e sugestões para azurite são boas-vindas. Vá para a página do [projeto azurite GitHub](https://github.com/Azure/Azurite/projects) ou problemas do [GitHub](https://github.com/Azure/Azurite/issues) para Marcos e itens de trabalho que estamos acompanhando para futuros recursos e correções de bugs. Os itens de trabalho detalhados também são acompanhados no GitHub.

## <a name="next-steps"></a>Próximas etapas

- [Use o emulador de armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md) de documentos o emulador de armazenamento do Azure herdado, que está sendo substituído pelo azurite.
- [Configurar cadeias de conexão do armazenamento do Azure](storage-configure-connection-string.md) explica como montar uma cadeia de conexão válida do armazenamento do Azure.
