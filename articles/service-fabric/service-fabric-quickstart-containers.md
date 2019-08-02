---
title: Criar um aplicativo de contêiner Windows no Service Fabric no Azure | Microsoft Docs
description: Neste início rápido, você cria seu primeiro aplicativo de contêiner do Windows no Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: jpconnock
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 096a398b8fc4f7f42dcc42feb7fe00b182d7649b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599377"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Início Rápido: Implantar contêineres do Windows no Service Fabric

O Azure Service Fabric é uma plataforma de sistemas distribuídos para implantação e gerenciamento de contêineres e microsserviços escalonáveis e confiáveis.

Executar um aplicativo existente em um contêiner do Windows em um cluster do Service Fabric não requer alterações no seu aplicativo. Este guia de início rápido mostra como implantar uma imagem de contêiner do Docker predefinida em um aplicativo do Service Fabric. Quando você terminar, terá um contêiner do IIS e um Servidor do Windows Server Core 2016 em execução. Este início rápido descreve a implantação de um contêiner do Windows. Leia [este início rápido](service-fabric-quickstart-containers-linux.md) para implantar um contêiner do Linux.

![Página de Web do IIS padrão][iis-default]

Neste guia de início rápido, você aprende a:

* Empacotar um contêiner de imagem do Docker
* Configurar a comunicação
* Compilar e empacotar o aplicativo do Service Fabric
* Implantar o aplicativo de contêiner no Azure


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure (você pode criar um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* Um computador de desenvolvimento executando:
  * Visual Studio 2019 ou Windows 2019.
  * [Ferramentas e SDK do Service Fabric](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Empacotar um recipiente de imagem do Docker com o Visual Studio

As ferramentas e o SDK do Service Fabric oferecem um modelo de serviço para ajudar você a implantar um contêiner em um cluster do Service Fabric.

Inicie o Visual Studio como “Administrador”.  Selecione **Arquivo** > **Novo** > **Projeto**.

Selecione **Aplicativo do Service Fabric**, nomeie-o como "MyFirstContainer" e clique em **Criar**.

Selecione **Contêiner** nos modelos **Aplicativos e Contêineres Hospedados**.

Em **Nome da Imagem**, insira "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016", o [Servidor do Windows Server Core e a imagem base do IIS](https://hub.docker.com/_/microsoft-windows-servercore-iis).

Configure o mapeamento de portas porta a host do contêiner para que as solicitações de entrada para o serviço na porta 80 sejam mapeadas para a porta 80 no contêiner.  Defina a **Porta do Contêiner** como "80" e defina a **Porta do Host** como "80".  

Chame o serviço de "MyContainerService" e clique em **OK**.

![Caixa de diálogo Novo serviço][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>Especifique o build do sistema operacional para a imagem de contêiner

Contêineres criados com uma versão específica do Windows Server podem não ser executados em um host executando uma versão diferente do Windows Server. Por exemplo, contêineres criados usando o Windows Server 1709 não funcionam em hosts que estão executando o Windows Server 2016. Para obter mais informações, consulte [Compatibilidade do sistema operacional contêiner e do sistema operacional do host do Windows Server](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). 

Com a versão 6.1 do tempo de execução do Service Fabric e mais recente, você pode especificar várias imagens do sistema operacional por contêiner e marcar cada um com a versão do build do sistema operacional na que ele deve ser implantado. Isso ajuda a garantir que seu aplicativo será executado em hosts que executam diferentes versões do sistema operacional Windows. Para saber mais, consulte [Especifique a compilação do sistema operacional das imagens de contêiner específicas](service-fabric-get-started-containers.md#specify-os-build-specific-container-images). 

A Microsoft publica imagens diferentes para versões do IIS criadas em diferentes versões do Windows Server. Para certificar-se de que o Service Fabric implanta um contêiner compatível com a versão do Windows Server em execução em nós de cluster onde ele implanta o aplicativo, adicione as seguintes linhas ao arquivo *ApplicationManifest.xml*. A versão de compilação para o Windows Server 2016 é 14393 e a versão de compilação para a versão 1709 do Windows Server é 16299.

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1803" /> 
          <Image Name= "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016" Os="14393" /> 
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

O manifesto do serviço continua a especificar apenas uma imagem para o Nano Server, `mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016`.

Além disso, no arquivo *ApplicationManifest.xml*, altere **PasswordEncrypted** para **false**. A conta e a senha estão em branco para a imagem de contêiner pública que está no Hub do Docker; portanto, desligamos a criptografia, porque criptografar uma senha em branco gerará um erro de build.

```xml
<RepositoryCredentials AccountName="" Password="" PasswordEncrypted="false" />
```

## <a name="create-a-cluster"></a>Criar um cluster

O script de exemplo a seguir cria um cluster do Service Fabric de cinco nós protegido com um certificado X.509. O comando cria um certificado autoassinado e o carrega em um novo cofre de chaves. O certificado também é copiado para um diretório local. É possível saber mais sobre como criar um cluster usando esse script em [Criar um cluster do Service Fabric](scripts/service-fabric-powershell-create-secure-cluster-cert.md).

Se necessário, instale o Azure PowerShell usando as instruções no [Guia do Azure PowerShell](/powershell/azure/overview).

Antes de executar o script a seguir, no PowerShell, execute `Connect-AzAccount` para criar uma conexão com o Azure.

Copie o script a seguir para a área de transferência e abra o **ISE do Windows PowerShell**.  Cole o conteúdo na janela vazia Untitled1.ps1. Forneça valores para as variáveis no script: `subscriptionId`, `certpwd`, `certfolder`, `adminuser`, `adminpwd` etc.  O diretório especificado para `certfolder` deve existir antes da execução do script.

[!code-powershell[main](../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

Após fornecer seus valores para as variáveis, pressione **F5** para executar o script.

Após a execução do script e a criação do cluster, localize o `ClusterEndpoint` na saída. Por exemplo:

```powershell
...
ClusterEndpoint : https://southcentralus.servicefabric.azure.com/runtime/clusters/b76e757d-0b97-4037-a184-9046a7c818c0
```

### <a name="install-the-certificate-for-the-cluster"></a>Instalar o certificado do cluster

Agora, instalaremos o PFX no repositório de certificados *CurrentUser\My*. O arquivo PFX estará no diretório especificado usando a variável de ambiente `certfolder` no script do PowerShell acima.

Mude para esse diretório e execute o seguinte comando do PowerShell, substituindo o nome do arquivo PFX no diretório `certfolder` e a senha que você especificou na variável `certpwd`. Nesse exemplo, o diretório atual é definido como o diretório especificado pela variável `certfolder` no script do PowerShell. É aí que o comando `Import-PfxCertificate` é executado:

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysfclustergroup20190130193456.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString Password#1234 -AsPlainText -Force)
```

O comando retorna a Impressão digital:

```powershell
  ...
  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
0AC30A2FA770BEF566226CFCF75A6515D73FC686  CN=mysfcluster.SouthCentralUS.cloudapp.azure.com
```

Lembre-se do valor de impressão digital da uma etapa posterior.

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Implantar o aplicativo no Azure usando o Visual Studio

Agora que o aplicativo está pronto, você poderá implantá-lo no cluster diretamente por meio do Visual Studio.

Clique com botão direito do mouse em **MyFirstContainer** no Gerenciador de Soluções e escolha **Publicar**. A caixa de diálogo Publicar será exibida.

Copie o conteúdo a seguir **CN=** na janela do PowerShell quando você executou o comando `Import-PfxCertificate` acima e adicione a porta `19000` a ele. Por exemplo, `mysfcluster.SouthCentralUS.cloudapp.azure.com:19000`. Copie-o para o campo **Ponto de Extremidade de Conexão**. Lembre-se desse valor, porque você precisará dele em uma etapa futura.

Clique em **Parâmetros de Conexão Avançados** e verifique as informações de parâmetro de conexão.  Os valores *FindValue* e *ServerCertThumbprint* devem coincidir com a impressão digital do certificado instalado quando você executou o `Import-PfxCertificate` na etapa anterior.

![Caixa de diálogo Publicar](./media/service-fabric-quickstart-containers/publish-app.png)

Clique em **Publicar**.

Cada aplicativo no cluster deve ter um nome exclusivo. Se houver um conflito de nome, renomeie o projeto do Visual Studio e implante novamente.

Abra um navegador e navegue até o endereço que você colocou no campo **Ponto de Extremidade de Conexão** na etapa anterior. Como opção, preceda o identificador do esquema, `http://`, e anexe a porta, `:80`, para a URL. Por exemplo, http:\//mysfcluster.SouthCentralUS.cloudapp.azure.com:80.

 Você deve ver a página da Web do IIS padrão: ![Página da Web do IIS padrão][iis-default]

## <a name="clean-up"></a>Limpar

Você continuará incorrendo em encargos enquanto o cluster estiver em execução. Considere [excluir o cluster](service-fabric-cluster-delete.md).

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

* Empacotar um contêiner de imagem do Docker
* Configurar a comunicação
* Compilar e empacotar o aplicativo do Service Fabric
* Implantar o aplicativo de contêiner no Azure

Para saber mais sobre como trabalhar com os contêineres do Windows no Service Fabric, prossiga com o tutorial sobre aplicativos de contêiner do Windows.

> [!div class="nextstepaction"]
> [Criar um aplicativo de contêiner do Windows](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
