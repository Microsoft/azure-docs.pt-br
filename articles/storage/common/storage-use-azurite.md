---
title: Usar o emulador de código-fonte aberto azurite para desenvolvimento e teste de armazenamento de BLOBs (versão prévia)
description: O emulador de código-fonte aberto do azurite (versão prévia) fornece um ambiente local gratuito para testar seus aplicativos de armazenamento de BLOBs do Azure.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: article
ms.date: 06/12/2019
ms.author: mhopkins
ms.subservice: common
ms.openlocfilehash: 2ccb19253c762bad69875a7b7bba7cd11d46e132
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869788"
---
# <a name="use-the-azurite-open-source-emulator-for-blob-storage-development-and-testing-preview"></a>Usar o emulador de código-fonte aberto azurite para desenvolvimento e teste de armazenamento de BLOBs (versão prévia)

O emulador de código-fonte aberto do azurite versão 3 (visualização) fornece um ambiente local gratuito para testar seus aplicativos de armazenamento de BLOBs do Azure. Quando estiver satisfeito com o modo como seu aplicativo está funcionando localmente, alterne para o usando uma conta de armazenamento do Azure na nuvem. O emulador fornece suporte de plataforma cruzada no Windows, Linux e MacOS. O azurite v3 dá suporte a APIs implementadas pelo serviço blob do Azure.

Azurite é a plataforma de emulador de armazenamento futura. Azurite substitui o emulador de [armazenamento do Azure](storage-use-emulator.md). O azurite continuará a ser atualizado para dar suporte às versões mais recentes das APIs de armazenamento do Azure.

Há várias maneiras diferentes de instalar e executar o azurite em seu sistema local:

  1. [Instalar e executar a extensão de Visual Studio Code azurite](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Instalar e executar o azurite usando o NPM](#install-and-run-azurite-by-using-npm)
  1. [Instalar e executar a imagem do Docker azurite](#install-and-run-the-azurite-docker-image)
  1. [Clonar, compilar e executar azurite do repositório GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Instalar e executar a extensão de Visual Studio Code azurite

Em Visual Studio Code, selecione o painel **extensões** e procure *azurite* nas **extensões: MARKETPLACE**.

![Marketplace de extensões de Visual Studio Code](media/storage-use-azurite/azurite-vs-code-extension.png)

Como alternativa, navegue até [vs Code mercado de extensão](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) em seu navegador. Selecione o botão **instalar** para abrir Visual Studio Code e vá diretamente para a página extensão azurite.

Você pode iniciar ou fechar o azurite rapidamente clicando no **serviço blob azurite** na barra de status vs Code ou emitindo os comandos a seguir na paleta de comandos vs Code. Para abrir a paleta de comandos, pressione **F1** em vs Code.

A extensão oferece suporte aos seguintes comandos de Visual Studio Code:

   * **Azurite: Iniciar** -iniciar todos os serviços Azurites
   * **Azurite: Fechar** -fechar todos os serviços Azurites
   * **Azurite: Limpar** -redefinir todos os dados de persistência dos serviços Azurites
   * **Azurite: Start** -blob iniciar o serviço blob
   * **Azurite: Fechar** o blob fechar o serviço blob
   * **Azurite: Limpar** o serviço blob de limpeza de BLOB

Para configurar o azurite em Visual Studio Code, selecione o painel extensões e clique com o botão direito do mouse em **azurite**. Selecione **definir configurações de extensão**.

![Azurite definir configurações de extensão](media/storage-use-azurite/azurite-configure-extension-settings.png)

Há suporte para as seguintes configurações:

   * **Azurite: Host** de blob-o ponto de extremidade de escuta do serviço BLOB. A configuração padrão é 127.0.0.1.
   * **Azurite: Porta** de blob-a porta de escuta do serviço BLOB. A porta padrão é 10000.
   * **Azurite: Depurar** -saída do log de depuração para o canal azurite. O valor padrão é **false**.
   * **Azurite: Local** -o caminho do local do espaço de trabalho. O padrão é a pasta de trabalho Visual Studio Code.
   * **Azurite: Silencioso** – o modo silencioso desabilita o log de acesso. O valor padrão é **false**.

## <a name="install-and-run-azurite-by-using-npm"></a>Instalar e executar o azurite usando o NPM

Esse método de instalação requer que você tenha o [node. js versão 8,0 ou posterior](https://nodejs.org) instalado. **NPM** é a ferramenta de gerenciamento de pacotes incluída em cada instalação do node. js. Depois de instalar o Node. js, execute o seguinte comando **NPM** para instalar o azurite.

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
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
```

**Especifique o local do espaço de trabalho**:

No exemplo a seguir, o `-v c:/azurite:/data` parâmetro especifica `c:/azurite` como o local de dados persistentes do azurite.

```console
docker run -p 10000:10000 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Defina todos os parâmetros de azurite**:

Este exemplo mostra como definir todos os parâmetros de linha de comando. Todos os parâmetros abaixo devem ser colocados em uma única linha de comando.

```console
docker run -p 8888:8888
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
```

Consulte [Opções de linha de comando](#command-line-options) para obter mais informações sobre como configurar o azurite na inicialização.

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
> Azurite não pode ser executado na linha de comando se você instalou apenas a extensão Visual Studio Code. Em vez disso, use a paleta de comandos VS Code. Para obter mais informações, consulte [instalar e executar a extensão de Visual Studio Code azurite](#install-and-run-the-azurite-visual-studio-code-extension).

Para começar imediatamente com a linha de comando, crie um diretório chamado **c:\azurite**e, em seguida, inicie o azurite emitindo o seguinte comando:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Esse comando informa ao azurite para armazenar todos os dados em um diretório específico, **c:\azurite**. Se a opção **--Location** for omitida, ela usará o diretório de trabalho atual.

## <a name="command-line-options"></a>Opções de linha de comando

Esta seção detalha as opções de linha de comando disponíveis ao iniciar o azurite. Todas as opções de linha de comando são opcionais.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>]
    [-l | --location <workspace path>] [-s | --silent] [-d | --debug <log file path>]
```

A opção **-l** é um atalho para **--Location**, **-s** é um atalho para **--Silent**e **-d** é um atalho para **--debug**.

### <a name="listening-host"></a>Host de escuta

**Opcional** Por padrão, o azurite escutará 127.0.0.1 como o servidor local. Use a opção **--blobHost** para definir o endereço para seus requisitos.

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

### <a name="listening-port-configuration"></a>Configuração de porta de escuta

**Opcional** Por padrão, o azurite escutará o serviço blob na porta 10000. Use a opção **--blobPort** para especificar a porta de escuta que você precisa.

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

### <a name="workspace-path"></a>Caminho do espaço de trabalho

**Opcional** O azurite armazena dados no disco local durante a execução. Use a opção **--Location** para especificar um caminho como o local do espaço de trabalho. Por padrão, o diretório de trabalho do processo atual será usado.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Log de acesso

**Opcional** Por padrão, o log de acesso é exibido na janela do console. Desabilite a exibição do log de acesso usando a opção **--Silent** .

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Log de depuração

**Opcional** O log de depuração inclui informações detalhadas sobre cada solicitação e rastreamento de pilha de exceção. Habilite o log de depuração fornecendo um caminho de arquivo local válido para a opção **--debug** .

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

## <a name="authorization-for-tools-and-sdks"></a>Autorização para ferramentas e SDKs

Conecte-se ao azurite de SDKs ou ferramentas do armazenamento do Azure, como [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), usando qualquer estratégia de autenticação. A autenticação é necessária. O azurite dá suporte à autorização com chaves compartilhadas e SAS (assinaturas de acesso compartilhado). O azurite também dá suporte ao acesso anônimo a contêineres públicos.

### <a name="well-known-storage-account-and-key"></a>Chave e conta de armazenamento bem conhecidas

Você pode usar o seguinte nome de conta e chave com azurite. Essa é a mesma conta bem conhecida e chave usada pelo emulador de armazenamento do Azure herdado.

* Nome da conta:`devstoreaccount1`
* Chave de conta:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Além da autenticação SharedKey, o azurite dá suporte à autenticação de SAS de serviço e conta. O acesso anônimo também está disponível quando um contêiner é definido para permitir acesso público.

### <a name="connection-string"></a>Cadeia de conexão

A maneira mais fácil de se conectar ao azurite de seu aplicativo é configurar uma cadeia de conexão no arquivo de configuração do aplicativo que faz referência ao atalho *UseDevelopmentStorage = true*. Aqui está um exemplo de uma cadeia de conexão em um arquivo *app. config* :

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Para saber mais, confira [Configurar cadeias de conexão do Armazenamento do Azure](storage-configure-connection-string.md).

### <a name="storage-explorer"></a>Gerenciador de Armazenamento

Em Gerenciador de Armazenamento do Azure, conecte-se ao azurite clicando no ícone **adicionar conta** , selecione **anexar a um emulador local** e clique em **conectar**.

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

O azurite não é um serviço de armazenamento escalonável e não oferece suporte a um grande número de clientes simultâneos. Não há garantia de desempenho. O azurite destina-se a fins de desenvolvimento e teste.

### <a name="error-handling"></a>Tratamento de erros

O azurite está alinhado com a lógica de tratamento de erros do armazenamento do Azure, mas há diferenças. Por exemplo, as mensagens de erro podem ser diferentes, enquanto os códigos de status de erro são alinhados.

### <a name="ra-grs"></a>RA-GRS

O azurite dá suporte à replicação com redundância geográfica com acesso de leitura (RA-GRS). Para recursos de armazenamento, acesse o local secundário acrescentando **-secundário** ao nome da conta. Por exemplo, o endereço a seguir pode ser usado para acessar um BLOB usando o secundário somente leitura no azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite é código-fonte aberto

As contribuições e sugestões para azurite são boas-vindas. Vá para a página do [projeto azurite GitHub](https://github.com/Azure/Azurite/projects) ou problemas do [GitHub](https://github.com/Azure/Azurite/issues) para Marcos e itens de trabalho que estamos acompanhando para futuros recursos e correções de bugs. Os itens de trabalho detalhados também são acompanhados no GitHub.

## <a name="next-steps"></a>Próximas etapas

* [Use o emulador de armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md) de documentos o emulador de armazenamento do Azure herdado, que está sendo substituído pelo azurite.
* [Configurar cadeias de conexão do armazenamento do Azure](storage-configure-connection-string.md) explica como montar uma cadeia de conexão válida do armazenamento do Azure.
