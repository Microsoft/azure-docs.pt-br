---
title: Implantar um do Service Fabric em um cluster no Azure | Microsoft Docs
description: Saiba como implantar um aplicativo em um cluster no Visual Studio.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: d637b01eb9e4d1664def9eb537b810603eea18ea
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598818"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Tutorial: Implantar um aplicativo do Service Fabric em um cluster no Azure

Este tutorial é a parte dois de uma série. Ele mostra como implantar um aplicativo do Azure Service Fabric em um novo cluster no Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um cluster.
> * Implantar um aplicativo em um cluster remoto usando o Visual Studio.

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Criar um aplicativo do Service Fabric .NET](service-fabric-tutorial-create-dotnet-app.md).
> * Implantar o aplicativo em um cluster remoto.
> * [Adicionar um ponto de extremidade HTTPS a um serviço de front-end do ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md).
> * [Configure CI/CD usando o Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).
> * [Configurar monitoramento e diagnóstico para o aplicativo](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Instale o Visual Studio 2019](https://www.visualstudio.com/) e as cargas de trabalho **Desenvolvimento do Azure** e **Desenvolvimento para a Web e ASP.NET**.
* [Instalar o SDK do Service Fabric](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Baixar o aplicativo de exemplo Votação

Se você não tiver criado o aplicativo de exemplo de votação na [parte um desta série de tutoriais](service-fabric-tutorial-create-dotnet-app.md), poderá baixá-lo. Em uma janela Comando, execute o código a seguir para clonar o repositório de aplicativos de exemplo no computador local.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Abra o aplicativo no Visual Studio executando como administrador e crie-o.

## <a name="create-a-cluster"></a>Criar um cluster

Agora que o aplicativo está pronto, crie um cluster do Service Fabric e, em seguida, implante o aplicativo no cluster. Um [cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere) é um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados.

Neste tutorial, você criará um cluster de teste de três nós no IDE do Visual Studio e, em seguida, publicará o aplicativo nesse cluster. Confira o [tutorial Criar e gerenciar um cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) para obter informações sobre como criar um cluster de produção. Implante também o aplicativo em um cluster existente que você criou anteriormente por meio do [portal do Azure](https://portal.azure.com), usando scripts do [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) ou da [CLI do Azure](./scripts/cli-create-cluster.md) ou com base em um [modelo do Azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> O aplicativo de Votação e muitos outros aplicativos usam o proxy reverso do Service Fabric para se comunicar entre serviços. Os clusters criados no Visual Studio têm o proxy reverso habilitado por padrão. Se você estiver fazendo a implantação em um cluster existente, precisará [habilitar o proxy reverso no cluster](service-fabric-reverseproxy-setup.md) para que o aplicativo de Votação funcione.


### <a name="find-the-votingweb-service-endpoint"></a>Localizar o ponto de extremidade de serviço VotingWeb

O serviço Web front-end do aplicativo de Votação está escutando em uma porta específica (8080, se você seguiu as etapas na [parte 1 desta série de tutoriais](service-fabric-tutorial-create-dotnet-app.md). Quando o aplicativo for implantado para um cluster no Azure, o cluster e o aplicativo executam atrás de um balanceador de carga do Azure. A porta do aplicativo precisa ser aberta no Azure Load Balancer usando uma regra. A regra envia o tráfego de entrada por meio do balanceador de carga para o serviço Web. A porta pode ser encontrada no arquivo **VotingWeb/PackageRoot/ServiceManifest.xml** no elemento **Ponto de extremidade**. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Anote o ponto de extremidade de serviço, que será necessário em uma etapa posterior.  Se você estiver fazendo a implantação em um cluster existente, abra essa porta criando uma regra de balanceamento de carga e uma investigação no Azure Load Balancer usando um [script do PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) ou por meio do balanceador de carga desse cluster no [portal do Azure](https://portal.azure.com).

### <a name="create-a-test-cluster-in-azure"></a>Criar um cluster de teste no Azure
No Gerenciador de Soluções, clique com o botão direito do mouse em **Votação** e selecione **Publicar**.

No **Ponto de Extremidade de Conexão**, selecione **Criar Cluster**.  Se você estiver fazendo a implantação em um cluster existente, selecione o ponto de extremidade de cluster na lista.  A caixa de diálogo Criar Cluster do Service Fabric será aberta.

Na guia **Cluster**, insira o **Nome do cluster** (por exemplo, "mytestcluster"), selecione sua assinatura e uma região para o cluster (por exemplo, Centro-Sul dos EUA), insira o número de nós de cluster (recomendamos três nós para um cluster de teste) e insira um grupo de recursos (por exemplo, "mytestclustergroup"). Clique em **Próximo**.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

Na guia **Certificado**, insira a senha e o caminho de saída para o certificado de cluster. Um certificado autoassinado é criado como um arquivo PFX e salvo no caminho de saída especificado.  O certificado é usado para segurança de nó para nó e de cliente para nó.  Não use um certificado autoassinado para clusters de produção.  Esse certificado é usado pelo Visual Studio para se autenticar no cluster e implantar aplicativos. Selecione **Importar certificado** para instalar o PFX no repositório de certificados CurrentUser\My do computador.  Clique em **Próximo**.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

Na guia **Detalhes da VM**, insira o **Nome de usuário** e a **Senha** para a conta do administrador do cluster.  Selecione a **Imagem de máquina virtual** para os nós de cluster e o **Tamanho de máquina virtual** para cada nó de cluster.  Selecione a guia **Avançado** .

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

Em **Portas**, insira o ponto de extremidade de serviço VotingWeb da etapa anterior (por exemplo, 8080).  Quando o cluster é criado, essas portas do aplicativo são abertas no Azure Load Balancer para encaminhar o tráfego para o cluster.  Clique em **Criar** para criar o cluster, o que leva vários minutos.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>Publicar o aplicativo no cluster

Quando o novo cluster estiver pronto, você poderá implantar o aplicativo de Votação diretamente do Visual Studio.

No Gerenciador de Soluções, clique com o botão direito do mouse em **Votação** e selecione **Publicar**. A caixa de diálogo **Publicar** é exibida.

Em **Ponto de Extremidade de Conexão**, selecione o ponto de extremidade para o cluster criado na etapa anterior.  Por exemplo, "mytestcluster.southcentral.cloudapp.azure.com:19000". Se você selecionar **Parâmetros de Conexão Avançados**, as informações de certificado deverão ser preenchidas automaticamente.  
![Publicar um aplicativo do Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Selecione **Publicar**.

Depois que o aplicativo for implantado, abra um navegador e insira o endereço do cluster seguido por **:8080**. Ou insira outra porta, se houver alguma configurada. Um exemplo é `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. Você verá o aplicativo em execução no cluster no Azure. Na página da Web de votação, tente adicionar e excluir opções de votação e vote em uma ou mais dessas opções.

![Exemplo de votação do Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Próximas etapas
Nesta parte do tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um cluster.
> * Implantar um aplicativo em um cluster remoto usando o Visual Studio.

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Habilitar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
