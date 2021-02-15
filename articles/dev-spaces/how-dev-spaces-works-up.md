---
title: Como executar o código com Azure Dev Spaces funciona
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos de execução do seu código no serviço kubernetes do Azure com Azure Dev Spaces
keywords: azds. YAML, Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres
ms.openlocfilehash: 1cace325f9415d46210636e5c04cc2d75589cc11
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014424"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Como executar o código com Azure Dev Spaces funciona

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

O Azure Dev Spaces fornece várias maneiras de iterar e depurar rapidamente aplicativos Kubernetes e colaborar com sua equipe em um cluster do AKS (Serviço de Kubernetes do Azure). Depois que o [projeto estiver preparado para ser executado em um espaço de desenvolvimento][how-it-works-prep], você poderá usar espaços de desenvolvimento para compilar e executar o projeto em seu cluster AKs.

Este artigo descreve o que acontece em executar seu código em AKS com espaços de desenvolvimento.

## <a name="run-your-code"></a>Executar seu código

Para executar seu código em um espaço de desenvolvimento, emita o `up` comando no mesmo diretório que o `azds.yaml` arquivo:

```cmd
azds up
```

O `up` comando carrega os arquivos de origem do aplicativo e outros artefatos necessários para compilar e executar seu projeto no espaço de desenvolvimento. A partir daí, o controlador em seu espaço de desenvolvimento:

1. Cria os objetos kubernetes para implantar seu aplicativo.
1. Cria o contêiner para seu aplicativo.
1. Implanta seu aplicativo no espaço do dev.
1. Cria um nome DNS acessível publicamente para o ponto de extremidade do aplicativo, se configurado.
1. Usa o *encaminhamento de porta* para fornecer acesso ao ponto de extremidade do aplicativo usando http://localhost .
1. Encaminha stdout e stderr para as ferramentas do lado do cliente.


## <a name="starting-a-service"></a>Iniciando um serviço

Quando você inicia um serviço em um espaço de desenvolvimento, o controlador e as ferramentas do lado do cliente funcionam em coordenação para sincronizar os arquivos de origem, criar seus objetos de contêiner e kubernetes e executar seu aplicativo.

Em um nível mais granular, aqui está o que acontece quando você executa `azds up` :

1. Os [arquivos são sincronizados][sync-section] do computador do usuário para um armazenamento de arquivos do Azure que é exclusivo para o cluster AKs do usuário. O código-fonte, o gráfico Helm e os arquivos de configuração são carregados.
1. O controlador cria uma solicitação para iniciar uma nova sessão. Essa solicitação contém várias propriedades, incluindo uma ID exclusiva, o nome do espaço, o caminho para o código-fonte e um sinalizador de depuração.
1. O controlador substitui o espaço reservado *$ (tag)* no gráfico Helm com a ID de sessão exclusiva e instala o gráfico Helm para o serviço. A adição de uma referência à ID de sessão exclusiva ao gráfico Helm permite que o contêiner implantado no cluster AKS para essa sessão específica seja vinculado à solicitação de sessão e às informações associadas.
1. Durante a instalação do gráfico do Helm, o servidor de admissão do webhook do kubernetes adiciona contêineres adicionais ao Pod do seu aplicativo para instrumentação e acesso ao código-fonte do seu projeto. Os contêineres devspaces-proxy e devspaces-proxy-init são adicionados para fornecer roteamento de HTTP e rastreamento de espaço. O contêiner devspaces-Build é adicionado para fornecer o Pod com acesso à instância do Docker e ao código-fonte do projeto para criar o contêiner do aplicativo.
1. Quando o Pod do aplicativo é iniciado, o contêiner devspaces-Build e o contêiner devspaces-proxy-init são usados para criar o contêiner do aplicativo. O contêiner de aplicativo e os contêineres devspaces-proxy são iniciados.
1. Depois que o contêiner de aplicativo é iniciado, a funcionalidade do lado do cliente usa a funcionalidade de *encaminhamento de porta* kubernetes para fornecer acesso http ao seu aplicativo http://localhost . Esse encaminhamento de porta conecta seu computador de desenvolvimento ao serviço em seu espaço de desenvolvimento.
1. Quando todos os contêineres no pod começaram, o serviço está em execução. Neste ponto, a funcionalidade do lado do cliente começa a transmitir os rastreamentos HTTP, stdout e stderr. Essas informações são exibidas pela funcionalidade do lado do cliente para o desenvolvedor.

## <a name="updating-a-running-service"></a>Atualizando um serviço em execução

Enquanto um serviço estiver em execução, Azure Dev Spaces terá a capacidade de atualizar esse serviço se qualquer um dos arquivos de origem do projeto for alterado. Os espaços de desenvolvimento também lidam com a atualização do serviço de forma diferente, dependendo do tipo de arquivo alterado. Há três maneiras de os espaços de desenvolvimento podem atualizar um serviço em execução:

* Atualizando um arquivo diretamente
* Recompilando e reiniciando o processo do aplicativo dentro do contêiner do aplicativo em execução
* Recompilando e reimplantando o contêiner do aplicativo

![Sincronização de arquivos de Azure Dev Spaces](media/how-dev-spaces-works/file-sync.svg)

Determinados arquivos de projeto que são ativos estáticos, como arquivos HTML, CSS e cshtml, podem ser atualizados diretamente no contêiner do aplicativo sem reiniciar nada. Se um ativo estático for alterado, o novo arquivo será sincronizado com o espaço de desenvolvimento e, em seguida, usado pelo contêiner em execução.

Alterações em arquivos como código-fonte ou arquivos de configuração do aplicativo podem ser aplicadas reiniciando o processo do aplicativo no contêiner em execução. Depois que esses arquivos são sincronizados, o processo do aplicativo é reiniciado dentro do contêiner em execução usando o processo *devhostagent* . Ao criar inicialmente o contêiner do aplicativo, o controlador substitui o comando de inicialização do aplicativo por um processo diferente chamado *devhostagent*. Em seguida, o processo real do aplicativo é executado como um processo filho em *devhostagent*, e sua saída é canalizada usando a saída de *devhostagent*. O processo *devhostagent* também faz parte dos espaços de desenvolvimento e pode executar comandos no contêiner em execução em nome dos espaços de desenvolvimento. Ao executar uma reinicialização, *devhostagent*:

* Interrompe o processo atual ou os processos associados ao aplicativo
* Recria o aplicativo
* Reinicia o processo ou os processos associados ao aplicativo

A maneira como o *devhostagent* executa as etapas anteriores é [configurado `azds.yaml` em][azds-yaml-section].

As atualizações para arquivos de projeto como Dockerfiles, arquivos csproj ou qualquer parte do gráfico Helm exigem que o contêiner do aplicativo seja recriado e reimplantado. Quando um desses arquivos é sincronizado com o espaço de desenvolvimento, o controlador executa o comando de [atualização Helm][helm-upgrade] e o contêiner do aplicativo é recriado e reimplantado.

## <a name="file-synchronization"></a>Sincronização de arquivos

Na primeira vez que um aplicativo é iniciado em um espaço de desenvolvimento, todos os arquivos de origem do aplicativo são carregados. Enquanto o aplicativo está em execução e depois é reiniciado, somente os arquivos alterados são carregados. Dois arquivos são usados para coordenar esse processo: um arquivo do lado do cliente e um arquivo do lado do controlador.

O arquivo do lado do cliente é armazenado em um diretório temporário e é nomeado com base em um hash do diretório do projeto que você está executando em espaços de desenvolvimento. Por exemplo, no Windows, você teria um arquivo como *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* para seu projeto. No Linux, o arquivo do lado do cliente é armazenado no diretório */tmp* . Você pode encontrar o diretório no macOS executando o `echo $TMPDIR` comando.

Este arquivo está no formato JSON e contém:

* Uma entrada para cada arquivo de projeto que é sincronizado com o espaço de desenvolvimento
* Uma ID de sincronização
* O carimbo de data/hora da última operação de sincronização

Cada entrada de arquivo de projeto contém um caminho para o arquivo e seu carimbo de data/hora.

O arquivo do lado do controlador é armazenado no cluster AKS. Ele contém a ID de sincronização e o carimbo de data/hora da última sincronização.

Uma sincronização ocorre quando os carimbos de data/hora de sincronização não coincidem entre os arquivos do lado do cliente e do controlador. Durante uma sincronização, as ferramentas do lado do cliente são iteradas sobre as entradas de arquivo no arquivo do lado do cliente. Se o carimbo de data/hora do arquivo for posterior ao carimbo de data/hora de sincronização, esse arquivo será sincronizado com o espaço de desenvolvimento. Depois que a sincronização for concluída, os carimbos de data/hora de sincronização serão atualizados nos arquivos do lado do cliente e do controlador.

Todos os arquivos de projeto serão sincronizados se o arquivo do lado do cliente não estiver presente. Esse comportamento permite forçar uma sincronização completa excluindo o arquivo do lado do cliente.

## <a name="how-running-your-code-is-configured"></a>Como a execução do código é configurada

Azure Dev Spaces usa o `azds.yaml` arquivo para instalar e configurar seu serviço. O controlador usa a `install` propriedade no `azds.yaml` arquivo para instalar o gráfico Helm e criar os objetos kubernetes:

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

Por padrão, o `prep` comando irá gerar o gráfico Helm. Ele também define a propriedade *install. Chart* como o diretório do gráfico Helm. Se você quisesse usar um gráfico de Helm em um local diferente, você pode atualizar essa propriedade para usar esse local.

Ao instalar os gráficos do Helm, Azure Dev Spaces fornece uma maneira de substituir valores no gráfico do Helm. Os valores padrão para o gráfico Helm estão em `charts/APP_NAME/values.yaml` .

Usando a propriedade *install. Values* , é possível listar um ou mais arquivos que definem os valores que você deseja substituir no gráfico Helm. Por exemplo, se você quisesse uma configuração de nome de host ou de banco de dados especificamente ao executar seu aplicativo em um espaço de desenvolvimento, você pode usar essa funcionalidade de substituição. Você também pode adicionar um *?* no final de qualquer um dos nomes de arquivo para defini-lo como opcional.

A propriedade *install. Set* permite configurar um ou mais valores que você deseja substituir no gráfico Helm. Quaisquer valores configurados em *install. Set* substituirão os valores configurados em arquivos listados em *install. Values*. As propriedades em *install. Set* dependem dos valores no gráfico Helm e podem ser diferentes dependendo do gráfico Helm gerado.

No exemplo acima, a propriedade *install. set. replicaCount* informa ao controlador quantas instâncias do seu aplicativo executar em seu espaço de desenvolvimento. Dependendo do seu cenário, você pode aumentar esse valor, mas terá um impacto na anexação de um depurador ao Pod do seu aplicativo. Para obter mais informações, consulte o [artigo solução de problemas][troubleshooting].

No gráfico Helm gerado, a imagem de contêiner é definida como *{{. Values. Image. Repository}}: {{. Values. Image. Tag}}*. O `azds.yaml` arquivo define a propriedade *install. set. Image. Tag* como *$ (tag)* por padrão, que é usada como o valor de *{{. Values. Image. Tag}}*. Ao definir a propriedade *install. set. Image. Tag* dessa forma, ela permite que a imagem de contêiner do seu aplicativo seja marcada de forma distinta durante a execução de Azure dev Spaces. Nesse caso específico, a imagem é marcada como *\<value from image.repository> : $ (marca)*. Você deve usar a variável *$ (tag)* como o valor de   *install. set. Image. Tag* para que os espaços de desenvolvimento reconheçam e localizem o contêiner no cluster AKs.

No exemplo acima, `azds.yaml` define *install. set. ingress. hosts*. A propriedade *install. set. ingresso. hosts* define um formato de nome de host para pontos de extremidade públicos. Essa propriedade também usa *$ (spacePrefix)*, *$ (rootSpacePrefix)* e *$ (hostSuffix)*, que são os valores fornecidos pelo controlador.

*$ (SpacePrefix)* é o nome do espaço de desenvolvimento filho, que assume a forma de *spacename. s*. O *$ (rootSpacePrefix)* é o nome do espaço pai. Por exemplo, se *azureuser* for um espaço filho de *padrão*, o valor de *$ (rootSpacePrefix)* será *padrão* e o valor de *$ (spacePrefix)* será *azureuser. s*. Se o espaço não for um espaço filho, *$ (spacePrefix)* ficará em branco. Por exemplo, se o espaço *padrão* não tiver espaço pai, o valor de *$ (rootSpacePrefix)* será *padrão* e o valor de *$ (spacePrefix)* ficará em branco. *$ (HostSuffix)* é um sufixo DNS que aponta para o controlador de entrada Azure dev Spaces que é executado em seu cluster AKs. Esse sufixo DNS corresponde a uma entrada DNS de caractere curinga, por exemplo *\* . RANDOM_VALUE. eus. azds. Io*, que foi criado quando o controlador de Azure dev Spaces foi adicionado ao cluster AKs.

No arquivo acima `azds.yaml` , você também pode atualizar *install. set. ingress. hosts* para alterar o nome do host do seu aplicativo. Por exemplo, se você quisesse simplificar o nome do host do seu aplicativo do *$ (spacePrefix) $ (rootSpacePrefix) WebFrontEnd $ (hostSuffix)* para *$ (spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix*).

Para criar o contêiner para seu aplicativo, o controlador usa as seções abaixo do `azds.yaml` arquivo de configuração:

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

O controlador usa um Dockerfile para compilar e executar seu aplicativo.

A propriedade *Build. Context* lista o diretório onde o Dockerfiles existe. A propriedade *build.dockerfile* define o nome do Dockerfile para criar a versão de produção do aplicativo. A propriedade *configurations.develop.build.dockerfile* define o nome do Dockerfile para a versão de desenvolvimento do aplicativo.

Ter diferentes Dockerfiles para desenvolvimento e produção permite que você habilite determinadas coisas durante o desenvolvimento e desabilite esses itens para implantações de produção. Por exemplo, você pode habilitar a depuração ou o log mais detalhado durante o desenvolvimento e a desabilitação em um ambiente de produção. Você também poderá atualizar essas propriedades se suas Dockerfiles forem nomeadas de forma diferente ou estiverem em um local diferente.

Para ajudá-lo a iterar rapidamente durante o desenvolvimento, Azure Dev Spaces sincronizará as alterações de seu projeto local e atualizará o aplicativo de forma incremental. A seção abaixo no `azds.yaml` arquivo de configuração é usada para configurar a sincronização e a atualização:

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

Os arquivos e diretórios que sincronizarão as alterações serão listados na propriedade *configurações. desenvolver. Container. Sync* . Esses diretórios são sincronizados inicialmente quando você executa o `up` comando, bem como quando as alterações são detectadas. Se houver diretórios adicionais ou diferentes que você gostaria de sincronizar com o espaço de desenvolvimento, você poderá alterar essa propriedade.

A propriedade *configurações. desenvolver. Container. iterate. buildCommands* especifica como compilar o aplicativo em um cenário de desenvolvimento. A propriedade *Configurations. desenvolver. Container. Command* fornece o comando para executar o aplicativo em um cenário de desenvolvimento. Talvez você queira atualizar qualquer uma dessas propriedades se houver sinalizadores ou parâmetros de tempo de execução ou de compilação adicionais que deseja usar durante o desenvolvimento.

As *configurações. desenvolver. Container. iterate. processesToKill* lista os processos a serem eliminados para interromper o aplicativo. Talvez você queira atualizar essa propriedade se desejar alterar o comportamento de reinicialização do seu aplicativo durante o desenvolvimento. Por exemplo, se você atualizou as propriedades *configurações. desenvolver. Container. iterate. buildCommands* ou *configurações. desenvolver. Container. Command* para alterar a forma como o aplicativo é criado ou iniciado, talvez seja necessário alterar quais processos são interrompidos.

Ao preparar seu código usando o `azds prep` comando, você tem a opção de adicionar o `--enable-ingress` sinalizador. A adição do `--enable-ingress` sinalizador cria uma URL acessível publicamente para seu aplicativo. Se você omitir esse sinalizador, o aplicativo só estará acessível no cluster ou usando o túnel localhost. Depois de executar o `azds prep` comando, você pode alterar essa configuração modificando a propriedade *entrada. Enabled* em `charts/APPNAME/values.yaml` :

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre rede e como as solicitações são roteadas em Azure Dev Spaces consulte [como o roteamento funciona com Azure dev Spaces][how-it-works-routing].

Para saber mais sobre a rápida iteração e o desenvolvimento com o kubernetes, consulte [como a ponte para o kubernetes funciona][how-it-works-bridge-to-kubernetes] e [como a depuração remota de seu código com Azure dev Spaces funciona][how-it-works-remote-debugging].


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md