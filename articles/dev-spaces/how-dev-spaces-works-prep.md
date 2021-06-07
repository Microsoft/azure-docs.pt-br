---
title: Como a preparação de um projeto para Azure Dev Spaces funciona
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve como preparar seu projeto com o Azure Dev Spaces funciona
keywords: azds. YAML, Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres
ms.openlocfilehash: d2da69dd8a8c2683ff584dfd0ffc61cb023f2ece
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91968150"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Como a preparação de um projeto para Azure Dev Spaces funciona

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

O Azure Dev Spaces fornece várias maneiras de iterar e depurar rapidamente aplicativos Kubernetes e colaborar com sua equipe em um cluster do AKS (Serviço de Kubernetes do Azure). Os espaços de desenvolvimento podem gerar gráficos Dockerfiles e Helm para seu projeto. Os espaços de desenvolvimento também criam e usam um arquivo de configuração para implantar, executar e depurar seus aplicativos kubernetes no AKS. Todos esses arquivos residem com o código do aplicativo e podem ser adicionados ao seu sistema de controle de versão.

Este artigo descreve o que acontece quando você prepara seu projeto para ser executado no AKS com espaços de desenvolvimento.

## <a name="prepare-your-code"></a>Preparar seu código

Para executar seu aplicativo em um espaço de desenvolvimento, ele precisa estar em contêiner e você precisa definir como ele deve ser implantado no kubernetes. Para colocar seu aplicativo em contêiner, você precisa de um Dockerfile. Para definir como seu aplicativo é implantado no kubernetes, você precisa de um [gráfico do Helm](https://docs.helm.sh/). Para auxiliar na criação do gráfico Dockerfile e Helm para seu aplicativo, as ferramentas do lado do cliente fornecem o `prep` comando:

```cmd
azds prep --enable-ingress
```

O `prep` comando examinará os arquivos em seu projeto e tentará criar o gráfico Dockerfile e Helm para executar seu aplicativo no kubernetes. Atualmente, o `prep` comando irá gerar um gráfico Dockerfile e Helm com os seguintes idiomas:

* Java
* Node.js
* .NET Core

Você *deve* executar o `prep` comando de um diretório que contém o código-fonte. A execução do `prep` comando no diretório correto permite que as ferramentas do lado do cliente identifiquem o idioma e criem um Dockerfile apropriado para colocar seu aplicativo em contêiner. Você também pode executar o `prep` comando de um diretório que contém um arquivo *pom.xml* para projetos Java.

Se você executar o `prep` comando do diretório que não contém o código-fonte, as ferramentas do lado do cliente não gerarão um Dockerfile. Ele também exibirá um erro dizendo: *Dockerfile não pôde ser gerado devido a um idioma sem suporte*. Esse erro também ocorrerá se as ferramentas do lado do cliente não reconhecerem o tipo de projeto.

Ao executar o `prep` comando, você tem a opção de especificar o `--enable-ingress` sinalizador. Esse sinalizador informa o controlador para criar um ponto de extremidade acessível pela Internet para esse serviço. Se você não especificar esse sinalizador, o serviço só poderá ser acessado de dentro do cluster ou usando o túnel localhost criado pelas ferramentas do lado do cliente. Você pode habilitar ou desabilitar esse comportamento depois de executar o `prep` comando atualizando o gráfico Helm gerado.

O `prep` comando não substituirá nenhum gráfico Dockerfiles ou Helm existente que você tenha em seu projeto. Se um gráfico Dockerfile ou Helm existente usar a mesma convenção de nomenclatura que os arquivos gerados pelo `prep` comando, o `prep` comando ignorará a geração desses arquivos. Caso contrário, o `prep` comando irá gerar seu próprio gráfico Dockerfile ou Helm ao lado dos arquivos existentes.

> [!IMPORTANT]
> Azure Dev Spaces usa o gráfico Dockerfile e Helm para que seu projeto crie e execute seu código, mas você pode modificar esses arquivos se quiser alterar a forma como o projeto é compilado e executado.

O `prep` comando também irá gerar um `azds.yaml` arquivo na raiz do seu projeto. Azure Dev Spaces usa esse arquivo para compilar, instalar, configurar e executar seu aplicativo. Esse arquivo de configuração lista o local do seu gráfico Dockerfile e Helm e também fornece configuração adicional sobre esses artefatos.

Aqui está um exemplo de arquivo azds. YAML criado usando o [aplicativo de exemplo .NET Core](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
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
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
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
```

O `azds.yaml` arquivo gerado pelo `prep` comando destina-se a funcionar para um cenário simples de desenvolvimento de projeto único. Se o seu projeto específico tiver aumentado a complexidade, talvez seja necessário atualizar esse arquivo depois de executar o `prep` comando. Por exemplo, seu projeto pode exigir algumas alterações em seu processo de compilação ou inicialização com base nas suas necessidades de desenvolvimento ou de depuração. Você também pode ter vários aplicativos em seu projeto, que exigem vários processos de compilação ou um conteúdo de compilação diferente.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como executar seu código em seu espaço de desenvolvimento, confira [como executar seu código com Azure dev Spaces funciona][how-it-works-up].

[how-it-works-up]: how-dev-spaces-works-up.md