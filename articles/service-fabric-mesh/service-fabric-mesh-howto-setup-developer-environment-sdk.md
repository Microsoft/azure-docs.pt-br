---
title: Configurar um ambiente de desenvolvimento do Windows para Service Fabric malha
description: Configure seu ambiente de desenvolvimento do Windows para que você possa criar um aplicativo de malha do Service Fabric e implante-o na malha do Azure Service Fabric.
author: georgewallace
ms.author: gwallace
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: fc234f6954cf263423cc517bb3dda2ba2efa3358
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625728"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Configurar seu ambiente de desenvolvimento do Windows para compilar aplicativos de Malha do Service Fabric

> [!IMPORTANT]
> A visualização da malha de Service Fabric do Azure foi desativada. Novas implantações não serão mais permitidas por meio da API de malha Service Fabric. O suporte para implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, consulte desativação da [Visualização da malha de Service Fabric do Azure](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Para compilar e executar os aplicativos da Malha do Azure Service Fabric em seu computador de desenvolvimento do Windows, você precisará de:

* Docker
* Visual Studio 2017 ou posterior
* runtime da Malha do Service Fabric
* Ferramentas e SDK da Malha do Service Fabric.

E uma das seguintes versões do Windows:

* Windows 10 (Enterprise, Professional ou Education) versões 1709 (Fall Creators Update) ou 1803 (Atualização de abril de 2018 para o Windows 10)
* Windows Server versão 1709
* Windows Server versão 1803

As instruções a seguir o ajudarão a obter tudo instalado com base na versão do Windows que você está executando.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

O Visual Studio 2017 ou posterior é necessário para implantar Service Fabric aplicativos de malha. [Instale a versão 15.6.0][download-visual-studio] ou uma versão posterior e habilite as cargas de trabalho a seguir:

* Desenvolvimento Web e ASP.NET
* Desenvolvimento do Azure

## <a name="install-docker"></a>Instalar o Docker

Se você já tem o Docker instalado, verifique se você tem a versão mais recente. O Docker pode solicitar a você quando uma nova versão estiver fora, mas verificar manualmente para garantir que você tenha a versão mais recente.

#### <a name="install-docker-on-windows-10"></a>Instalar o Docker no Windows 10

Baixe e instale a versão mais recente do [Docker Community Edition para Windows][download-docker] para dar suporte a aplicativos do Service Fabric em contêineres usados pela Malha do Service Fabric.

Durante a instalação, selecione **Usar contêineres do Windows em vez de contêineres do Linux** quando for solicitado.

Se o Hyper-V não estiver habilitado no seu computador, o instalador do Docker oferecerá para habilitá-lo. Clique em **OK** para fazê-lo se solicitado.

#### <a name="install-docker-on-windows-server-2016"></a>Instalar o Docker no Windows Server 2016

Se a função do Hyper-V não estiver habilitada, abra o PowerShell como administrador e execute o seguinte comando para instalar o Hyper-V e reinicie o computador. Para saber mais, confira a [Edição Docker Enterprise para Windows Server][download-docker-server].

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Reinicie seu computador.

Abra o PowerShell como um administrador e execute os seguintes comandos para instalar o Docker:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK e ferramentas

Instale o runtime da Malha do Service Fabric, o SDK e as ferramentas na ordem a seguir.

1. Instale o [SDK da malha do Service Fabric][download-sdkmesh] usando o Web Platform Installer. Isso instalará também o runtime e o SDK do Microsoft Azure Service Fabric.
2. Instale a [extensão de Ferramentas da Malha do Service Fabric do Visual Studio (versão prévia)][download-tools] do Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Criar um cluster

> [!IMPORTANT]
> O Docker **deve** estar em execução antes de um cluster ser compilado.
> Teste se o Docker está em execução abrindo uma janela de terminal e executando `docker ps` para ver se ocorre um erro. Se a resposta não indicar um erro, o Docker está em execução e você está pronto para compilar um cluster.

> [!Note]
> Se estiver desenvolvendo em um computador com a atualização Windows Fall Creators Update (versão 1709), você só poderá usar imagens do docker do Windows versão 1709.
> Se estiver desenvolvendo no computador com a atualização de 10 de abril de 2018 do Windows (versão 1803), você poderá usar imagens do Docker do Windows versão 1709 ou 1803.

Se você estiver usando o Visual Studio, poderá ignorar esta seção porque o Visual Studio criará um cluster local para você, se você não tiver um.

Para obter o melhor desempenho de depuração quando você estiver criando e executando um único aplicativo de Service Fabric de cada vez, crie um cluster de desenvolvimento local de nó único. Se você estiver executando vários aplicativos por vez, crie um cluster de desenvolvimento local de cinco nós. O cluster precisa estar em execução sempre que você implanta ou depura um projeto de Malha do Service Fabric.

Depois de instalar o runtime, os SDKs, as ferramentas do Visual Studio, Docker e estar com o Docker funcionando, crie um cluster de desenvolvimento.

1. Feche a janela do PowerShell.
2. Abra uma janela nova, com privilégios elevados do PowerShell como administrador. Essa etapa é necessária para carregar os módulos do Service Fabric que foram instalados.
3. Digite o seguinte comando do PowerShell para criar um cluster de desenvolvimento:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. Para iniciar a ferramenta de gerenciamento do cluster, execute o comando do PowerShell a seguir:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. Depois que a ferramenta Gerenciador do cluster de serviço estiver funcionando (aparece na bandeja do sistema), clique nela com o botão direito do mouse e clique em **Iniciar o Cluster Local**.

![Figura 1 – Iniciar o cluster local](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Agora você está pronto para criar os aplicativos de malha do Service Fabric!

## <a name="next-steps"></a>Próximas etapas

Leia o tutorial [Criar um aplicativo do Azure Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md).

Encontre respostas para [dúvidas comuns e problemas conhecidos](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/
