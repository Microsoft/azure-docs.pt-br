---
title: Implantar rapidamente um aplicativo existente em um cluster do Azure Service Fabric
description: Use um cluster do Azure Service Fabric para hospedar um aplicativo Node.js existente com o Visual Studio.
services: service-fabric
documentationcenter: nodejs
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: aljo
ms.openlocfilehash: 63630f5c4799e9272601f431671abc78ec86a238
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622335"
---
# <a name="host-a-nodejs-application-on-azure-service-fabric"></a>Hospedar um aplicativo Node.js no Azure Service Fabric

Este guia de início rápido ajuda a implantar um aplicativo existente (Node.js neste exemplo) em um cluster do Service Fabric em execução no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você [configurou o ambiente de desenvolvimento](service-fabric-get-started.md). O que inclui instalar o SDK do Service Fabric e o Visual Studio 2015 ou 2017.

Você também precisa ter um aplicativo Node.js existente para a implantação. Este guia de início rápido usa um site Node.js simples que pode ser baixado [aqui][download-sample]. Extraia esse arquivo para a pasta `<path-to-project>\ApplicationPackageRoot\<package-name>\Code\` depois de criar o projeto na próxima etapa.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][create-account].

## <a name="create-the-service"></a>Criar o serviço

Inicie o Visual Studio como um **administrador**.

Criar um projeto com `CTRL`+`SHIFT`+`N`

Na caixa de diálogo **Novo Projeto**, escolha **Nuvem > Aplicativo do Service Fabric**.

Nomeie o aplicativo **MyGuestApp** e pressione **OK**.

>[!IMPORTANT]
>O Node.js pode interromper o limite de 260 caracteres para caminhos do Windows facilmente. Use um caminho curto para o projeto, como **c:\code\svc1**. Como opção, você pode seguir **[estas instruções](https://stackoverflow.com/a/41687101/1664231)** para habilitar caminhos de arquivo longos no Windows 10.
   
![Caixa de diálogo Novo projeto no Visual Studio][new-project]

Você pode criar qualquer tipo de serviço Service Fabric na próxima caixa de diálogo. Para este guia de início rápido, escolha **Executável Convidado**.

Nomeie o serviço **MyGuestService** e defina as opções à direita com os seguintes valores:

| Configuração                   | Value |
| ------------------------- | ------ |
| Pasta do Pacote de Código       | _&lt;a pasta com seu aplicativo Node.js&gt;_ |
| Comportamento do Pacote de Código     | Copiar o conteúdo da pasta para o projeto |
| Programa                   | node.exe |
| Argumentos                 | server.js |
| Pasta de trabalho            | CodePackage |

Pressione **OK**.

![Caixa de diálogo Novo serviço no Visual Studio][new-service]

O Visual Studio cria o projeto de aplicativo e o projeto de serviço ator e os exibe no Gerenciador de Soluções.

O projeto de aplicativo (**MyGuestApp**) não contém qualquer código diretamente. Em vez disso, ele faz referência a um conjunto de projetos de serviço. Além disso, ele contém três outros tipos de conteúdo:

* **Perfis de publicação**  
Preferências de ferramentas para ambientes diferentes.

* **Scripts**  
Script do PowerShell para a implantação/atualização de seu aplicativo.

* **Definição de aplicativo**  
Inclui o manifesto do aplicativo em *ApplicationPackageRoot*. Os arquivos de parâmetros do aplicativo associados estão em *ApplicationParameters*, que definem o aplicativo e permitem que você o configure especificamente para determinado ambiente.
    
Para obter uma visão geral do conteúdo do projeto de serviço, confira [Introdução aos Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="set-up-networking"></a>Configurar a rede

O exemplo de aplicativo Node.js que estamos implantando usa a porta **80**, e precisamos dizer ao Service Fabric que precisamos dessa porta exposta.

Abra o arquivo **ServiceManifest.xml** no projeto. Na parte inferior do manifesto, há `<Resources> \ <Endpoints>` com uma entrada já definida. Modifique a entrada para adicionar `Port`, `Protocol` e `Type`. 

```xml
  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyGuestAppServiceTypeEndpoint" Port="80" Protocol="http" Type="Input" />
    </Endpoints>
  </Resources>
```

## <a name="deploy-to-azure"></a>Implantar no Azure

Se você pressionar **F5** e executar o projeto, ele será implantado no cluster local. No entanto, vamos implantar no Azure.

Clique com botão direito do mouse no projeto e escolha **Publicar...**  para abrir um diálogo Publicar no Azure.

![Diálogo Publicar no Azure para um serviço Service Fabric][publish]

Selecione o perfil de destino **PublishProfiles\Cloud.xml**.

Se você já não fez isso, escolha uma conta do Azure para implantação. Se você ainda não tiver uma conta, [inscreva-se][create-account].

Em **Ponto de Extremidade de Conexão**, selecione o cluster do Service Fabric para implantação. Se você não tiver um, selecione **&lt;Criar Novo Cluster... &gt;**, o que abrirá a janela do navegador da Web no portal do Azure. Para saber mais, confira [Criar um cluster no portal](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal). 

Quando você cria o cluster do Service Fabric, defina a configuração **Pontos de extremidade personalizados** como **80**.

![Configuração de tipo de nó do Service Fabric com o ponto de extremidade personalizado][custom-endpoint]

A criação de um novo cluster do Service Fabric leva algum tempo para concluir. Depois que o cluster for criado, acesse o diálogo Publicar e selecione  **&lt;Atualizar&gt;**. O novo cluster está listado na caixa suspensa. Selecione-o.

Pressione **Publicar** e aguarde até que a implantação seja concluída.

Isso pode levar alguns minutos. Depois de concluído, pode levar alguns minutos para o aplicativo ficar totalmente disponível.

## <a name="test-the-website"></a>Testar o site

Depois que o serviço foi publicado, teste-o em um navegador da Web. 

Primeiro, abra o portal do Azure e localize seu serviço Service Fabric.

Verifique a folha de visão geral do endereço do serviço. Use o nome de domínio da propriedade _Ponto de extremidade de conexão de cliente_. Por exemplo, `http://mysvcfab1.westus2.cloudapp.azure.com`.

![Folha de visão geral do Service Fabric no portal do Azure][overview]

Navegue até esse endereço, onde você verá a resposta `HELLO WORLD`.

## <a name="delete-the-cluster"></a>Excluir o cluster

Não se esqueça de excluir todos os recursos que criou para este guia de início rápido, pois você será cobrado por esses recursos.

## <a name="next-steps"></a>Próximas etapas
Leia mais sobre [executáveis convidados](service-fabric-guest-executables-introduction.md).

<!-- Image References -->

[new-project]: ./media/quickstart-guest-app/new-project.png
[new-service]: ./media/quickstart-guest-app/template.png
[solution-exp]: ./media/quickstart-guest-app/solution-explorer.png
[publish]: ./media/quickstart-guest-app/publish.png
[overview]: ./media/quickstart-guest-app/overview.png
[custom-endpoint]: ./media/quickstart-guest-app/custom-endpoint.png

[download-sample]: https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/service-fabric-node-website.zip
[create-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
