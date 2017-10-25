---
title: "Implantar um aplicativo .NET em um contêiner do Azure Service Fabric | Microsoft Docs"
description: "Ensina você a empacotar um aplicativo .NET no Visual Studio em um Contêiner do Docker. Esse novo aplicativo de “contêiner” é então implantado em um cluster do Service Fabric."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: mikhegn
ms.openlocfilehash: 021c695a91ff46274b2a5174918711d04bcff239
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Implantar um aplicativo .NET em um contêiner do Windows no Azure Service Fabric

Este tutorial mostra como implantar um aplicativo ASP.NET existente em um contêiner do Windows no Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto do Docker no Visual Studio
> * Colocar um aplicativo existente em um contêiner
> * Configurar a integração contínua com o Visual Studio e o VSTS

## <a name="prerequisites"></a>Pré-requisitos

1. Instalar o [Docker CE para Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) de forma que você possa executar contêineres no Windows 10.
2. Familiarizar-se com o [início rápido dos Contêineres do Windows 10][link-container-quickstart].
3. Baixar o aplicativo de exemplo [Fabrikam Fiber CallCenter][link-fabrikam-github].
4. Instalar o [Azure PowerShell][link-azure-powershell-install]
5. Instalar a [extensão Ferramentas de Entrega Contínua para Visual Studio 2017][link-visualstudio-cd-extension]
6. Criar uma [assinatura do Azure][link-azure-subscription] e uma [conta do Visual Studio Team Services][link-vsts-account]. 
7. [Criar um cluster no Azure](service-fabric-tutorial-create-cluster-azure-ps.md)

## <a name="containerize-the-application"></a>Colocar o aplicativo em um contêiner

Agora que você tem um [cluster do Service Fabric em execução no Azure](service-fabric-tutorial-create-cluster-azure-ps.md), você está pronto para criar e implantar um aplicativo em contêineres. Para começar a executar o aplicativo em um contêiner, precisamos adicionar o **Suporte do Docker** ao projeto no Visual Studio. Quando você adiciona o **Suporte do Docker** ao aplicativo, duas coisas acontecem. Primeiro, um _arquivo do Docker_ é adicionado ao projeto. Esse novo arquivo descreve como a imagem de contêiner deve ser criada. Em segundo lugar, um novo projeto _docker-compose_ é adicionado à solução. O novo projeto contém alguns arquivos docker-compose. Os arquivos de composição do docker podem ser usados para descrever como o contêiner é executado.

Mais informações sobre como trabalhar com as [Ferramentas de Contêiner do Visual Studio][link-visualstudio-container-tools].

>[!NOTE]
>Se essa for a primeira vez que você estiver executando imagens de contêiner do Windows no computador, o Docker CE deverá obter as imagens base para os contêineres. As imagens usadas neste tutorial têm 14 GB. Vá em frente e execute o seguinte comando de terminal para efetuar pull das imagens base:
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

### <a name="add-docker-support"></a>Adicionar suporte ao Docker

Abra o arquivo [FabrikamFiber.CallCenter.sln][link-fabrikam-github] no Visual Studio.

Clique com o botão direito do mouse no projeto **FabrikamFiber.Web** > **Adicionar** > **Suporte do Docker**.

### <a name="add-support-for-sql"></a>Adicionar suporte para SQL

Esse aplicativo usa o SQL como o provedor de dados e, portanto, um SQL Server é necessário para executar o aplicativo. Referencie uma imagem de contêiner do SQL Server em nosso arquivo docker-compose.override.yml.

No Visual Studio, abra **Gerenciador de Soluções**, localize **docker-compose** e abra o arquivo **docker-compose.override.yml**.

Navegue para o nó `services:` e adicione um nó chamado `db:` que define a entrada do SQL Server para o contêiner.

```yml
  db:
    image: microsoft/mssql-server-windows-developer
    environment:
      sa_password: "Password1"
      ACCEPT_EULA: "Y"
    ports:
      - "1433"
    healthcheck:
      test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
      interval: 1s
      retries: 20
```

>[!NOTE]
>Você pode usar qualquer SQL Server que preferir para a depuração local, desde que ele seja acessível no host. No entanto, **localdb** não dá suporte à comunicação `container -> host`.

>[!WARNING]
>A execução do SQL Server em um contêiner não dá suporte a dados persistentes. Quando o contêiner é interrompido, os dados são apagados. Não use essa configuração para produção.

Navegue para o nó `fabrikamfiber.web:` e adicione um nó filho chamado `depends_on:`. Isso garante que o serviço `db` (o contêiner do SQL Server) é iniciado antes de nosso aplicativo Web (fabrikamfiber.web).

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>Atualizar a configuração da Web

Novamente no projeto **FabrikamFiber.Web**, atualize a cadeia de conexão no arquivo **web.config** para que ela aponte para o SQL Server no contêiner.

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>Se você desejar usar outro SQL Server ao criar um build de versão de seu aplicativo Web, adicione outra cadeia de conexão ao arquivo web.release.config.

### <a name="test-your-container"></a>Testar o contêiner

Pressione **F5** para executar e depurar o aplicativo no contêiner.

O Edge abre a página de inicialização definida do aplicativo usando o endereço IP do contêiner na rede NAT interna (geralmente, 172.x.x.x). Para saber mais sobre como depurar aplicativos em contêineres usando o Visual Studio 2017, consulte [este artigo][link-debug-container].

![exemplo de fabrikam em um contêiner][image-web-preview]

O contêiner agora está pronto para ser compilado e empacotado em um aplicativo do Service Fabric. Depois de compilar a imagem de contêiner no computador, você poderá enviá-la por push para qualquer registro de contêiner e efetuar pull dela em qualquer host para execução.

## <a name="get-the-application-ready-for-the-cloud"></a>Preparar o aplicativo para a nuvem

Para preparar o aplicativo para execução no Service Fabric no Azure, precisamos concluir duas etapas:

1. Expor a porta em que desejamos poder acessar o aplicativo Web no cluster do Service Fabric.
2. Fornecer um banco de dados SQL pronto para produção para nosso aplicativo.

### <a name="expose-the-port-for-the-app"></a>Expor a porta para o aplicativo
O cluster do Service Fabric que configuramos tem a porta *80* aberta por padrão no Azure Load Balancer, o que balanceia o tráfego de entrada para o cluster. É possível expor o contêiner nessa porta por meio do arquivo docker-compose.yml.

No Visual Studio, abra o **Gerenciador de Soluções**, localize **docker-compose** e abra o arquivo **docker-compose.yml**.

Modifique o nó `fabrikamfiber.web:` e adicione um nó filho chamado `ports:`.

Adicione uma entrada de cadeia de caracteres `- "80:80"`. Esta é a aparência do seu arquivo docker-compose.yml:

```yml
  version: '3'

  services:
    fabrikamfiber.web:
      image: fabrikamfiber.web
      build:
        context: .\FabrikamFiber.Web
        dockerfile: Dockerfile
      ports:
        - "80:80"
```

### <a name="use-a-production-sql-database"></a>Usar um banco de dados SQL de produção
Ao executar em produção, precisamos que os dados sejam persistidos em nosso banco de dados. Atualmente, não existe uma maneira de garantir a persistência de dados em um contêiner. Portanto, não é possível armazenar dados de produção no SQL Server em um contêiner.

Recomendamos o uso de um Banco de Dados SQL do Azure. Para configurar e executar um SQL Server gerenciado no Azure, acesse o artigo [Guias de início rápido do Banco de Dados SQL do Azure][link-azure-sql].

>[!NOTE]
>Lembre-se de alterar as cadeias de conexão para o SQL Server no arquivo **web.release.config** do projeto **FabrikamFiber.Web**.
>
>Esse aplicativo falha normalmente se nenhum banco de dados SQL está acessível. Você pode optar por implantar o aplicativo sem um servidor SQL.

## <a name="deploy-with-visual-studio-team-services"></a>Implantar com o Visual Studio Team Services

Para configurar a implantação usando o Visual Studio Team Services, você precisa instalar a [extensão Ferramentas de Entrega Contínua para Visual Studio 2017][link-visualstudio-cd-extension]. Essa extensão facilita a implantação no Azure configurando o Visual Studio Team Services e implanta seu aplicativo no cluster do Service Fabric.

Para começar, o código deve ser hospedado no controle do código-fonte. No restante desta seção, pressupomos que o **git** esteja sendo usado.

### <a name="set-up-a-vsts-repo"></a>Configurar um repositório do VSTS
No canto inferior direito do Visual Studio, clique em **Adicionar ao Controle de Código-Fonte** > **Git** (ou a opção que preferir).

![pressionar o botão de controle do código-fonte][image-source-control]

No painel _Team Explorer_, pressione **Publicar Repositório Git**.

Selecione o nome do repositório VSTS e pressione **Repositório**.

![publicar o repositório no VSTS][image-publish-repo]

Agora que seu código está sincronizado com um repositório de origem do VSTS, você pode configurar a integração e a entrega contínuas.

### <a name="setup-continuous-delivery"></a>Configurar a entrega contínua

No _Gerenciador de Soluções_, clique com o botão direito do mouse em **solução** > **Configurar a Entrega Contínua**.

Selecione a Assinatura do Azure.

Defina **Tipo de Host** como **Cluster do Service Fabric**.

Defina **Host de Destino** como o cluster do Service Fabric criado na seção anterior.

Escolha um **Registro de Contêiner** no qual o contêiner será publicado.

>[!TIP]
>Use o botão **Editar** para criar um registro de contêiner.

Pressione **OK**.

![configurar a integração contínua do service fabric][image-setup-ci]
   
   Depois que a configuração for concluída, o contêiner será implantado no Service Fabric. Sempre que você envia atualizações ao repositório, uma nova compilação e versão são executadas.
   
   >[!NOTE]
   >A compilação das imagens de contêiner leva aproximadamente 15 minutos.
   >A primeira implantação para o cluster do Service Fabric faz com que as imagens de contêiner do Windows Server Core sejam baixadas. O download leva 5 a 10 minutos adicionais para ser concluído.

Navegue até o aplicativo Fabrikam Call Center usando a url do cluster. Por exemplo, *http://mycluster.westeurope.cloudapp.azure.com*

Agora que você colocou a solução Fabrikam Call Center em um contêiner e a implantou, poderá abrir o [portal do Azure][link-azure-portal] e ver o aplicativo em execução no Service Fabric. Para experimentar o aplicativo, abra um navegador da Web e acesse a URL do cluster do Service Fabric.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um projeto do Docker no Visual Studio
> * Colocar um aplicativo existente em um contêiner
> * Configurar a integração contínua com o Visual Studio e o VSTS

Na próxima parte do tutorial, saiba como configurar o [monitoramento do seu contêiner](service-fabric-tutorial-monitoring-wincontainers.md).

<!--   NOTE SURE WHAT WE SHOULD DO YET HERE

Advance to the next tutorial to learn how to bind a custom SSL certificate to it.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

## Next steps

- [Container Tooling in Visual Studio][link-visualstudio-container-tools]
- [Get started with containers in Service Fabric][link-servicefabric-containers]
- [Creating Service Fabric applications][link-servicefabric-createapp]
-->

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
