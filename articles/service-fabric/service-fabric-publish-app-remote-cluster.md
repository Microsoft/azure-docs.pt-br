---
title: Publicar um aplicativo em um cluster remoto com o Visual Studio | Microsoft Docs
description: Saiba mais sobre como publicar um aplicativo em um cluster do Service Fabric remoto usando o Visual Studio.
services: service-fabric
documentationcenter: na
author: cawams
manager: timlt
editor: 
ms.assetid: faecd892-eb54-4d9c-8023-c67442afb8e8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/29/2016
ms.author: cawa
ms.openlocfilehash: c440c520d84fc503ff9e705555449e92555d4721
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-and-remove-applications-using-visual-studio"></a>Implantar e remover aplicativos usando o Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> * [APIs de FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

A extensão do Service Fabric do Azure para Visual Studio permite facilmente repetir e executar por script a publicação de um aplicativo em um cluster do Service Fabric.

## <a name="the-artifacts-required-for-publishing"></a>Os artefatos necessários para publicação
### <a name="deploy-fabricapplicationps1"></a>Deploy-FabricApplication.ps1
Este é um script do PowerShell que usa um caminho de perfil de publicação como um parâmetro para a publicação de aplicativos do Service Fabric. Como esse script faz parte do seu aplicativo, fique à vontade para modificá-lo como achar melhor para ele.

### <a name="publish-profiles"></a>Perfis de publicação
Uma pasta no projeto de aplicativo do Service Fabric chamada **PublishProfiles** contém arquivos XML que armazenam informações essenciais para a publicação de aplicativos, como:

* Parâmetros de conexão de cluster do Service Fabric
* Caminho para um arquivo de parâmetros de aplicativo
* Configurações de atualização

Por padrão, o aplicativo incluirá três perfis de publicação: Local.1Node.xml, Local.5Node.xml e Cloud.xml. Você pode adicionar mais perfis copiando e colando um dos arquivos padrão.

### <a name="application-parameter-files"></a>Arquivos de parâmetros de aplicativo
Uma pasta no projeto de aplicativo do Service Fabric chamada **ApplicationParameters** contém arquivos XML com valores de parâmetro do manifesto do aplicativo especificados pelo usuário. Os arquivos de manifesto do aplicativo podem ser parametrizados, de modo que você possa usar valores diferentes para configurações de implantação. Para saber mais sobre a parametrização de aplicativo, confira [Gerenciar vários ambientes no Service Fabric](service-fabric-manage-multiple-environment-app-configuration.md).

> [!NOTE]
> Para serviços de ator, você deve compilar o projeto antes de tentar editar o arquivo em um editor ou por meio da caixa de diálogo de publicação. Isso ocorre porque parte dos arquivos de manifesto serão gerados durante a compilação.

## <a name="to-publish-an-application-using-the-publish-service-fabric-application-dialog-box"></a>Para publicar um aplicativo usando a caixa de diálogo Publicar Aplicativo do Service Fabric
As etapas a seguir demonstram como publicar um aplicativo usando a caixa de diálogo **Publicar Aplicativo do Service Fabric** fornecida pelas Ferramentas do Service Fabric do Visual Studio.

1. No menu de atalho do projeto Aplicativo do Service Fabric, escolha **Publicar...** para exibir a caixa de diálogo **Publicar Aplicativo do Service Fabric**.
   
    ![A caixa de diálogo **Publicar Aplicativo do Service Fabric**][0]
   
    O arquivo selecionado na lista suspensa **Perfil de destino** é onde todas as configurações, exceto as **Versões do manifesto**, são salvas. Você pode reutilizar um perfil existente ou criar um novo escolhendo **<Gerenciar Perfis... >** na lista suspensa **Perfil de destino**. Quando você escolhe um perfil de publicação, seu conteúdo aparece nos campos correspondentes da caixa de diálogo. Para salvar suas alterações a qualquer momento, escolha o link **Salvar Perfil** .    
2. Na seção **Ponto de extremidade da conexão** , especifique um ponto de extremidade de publicação local ou remoto de cluster do Service Fabric. Para adicionar ou alterar o ponto de extremidade de conexão, clique na lista suspensa **Ponto de Extremidade de Conexão** . A lista mostra os pontos de extremidade de conexão do cluster do Service Fabric disponíveis nos quais você pode publicar com base em suas assinaturas do Azure. Você deve se conectar ao Visual Studio se já não estiver.
   
    Use a caixa de diálogo de seleção de cluster para escolher o conjunto de assinaturas e os clusters disponíveis.
   
    ![A caixa de diálogo **Selecionar Cluster do Service Fabric**][1]
   
   > [!NOTE]
   > Se você deseja publicar em um ponto de extremidade arbitrário (como um cluster de terceiros), confira a seção **Publicando em um ponto de extremidade de cluster arbitrário** abaixo.
   > 
   > 
   
    Depois de escolher um ponto de extremidade, o Visual Studio valida a conexão com o cluster do Service Fabric selecionado. Se o cluster não for seguro, o Visual Studio poderá conectar-se a ele imediatamente. No entanto, se o cluster estiver protegido, será necessário instalar um certificado em seu computador local antes de continuar. Confira [Como configurar conexões seguras](service-fabric-visualstudio-configure-secure-connections.md) para saber mais. Ao terminar, escolha o botão **OK** . O cluster selecionado aparece na caixa de diálogo **Publicar Aplicativo do Service Fabric** .
3. Na caixa de listagem suspensa **Arquivo de Parâmetros do Aplicativo** , navegue até um arquivo de parâmetros do aplicativo. Um arquivo de parâmetros do aplicativo contém valores especificados pelo usuário para os parâmetros no arquivo de manifesto do aplicativo. Para adicionar ou alterar um parâmetro, escolha o botão **Editar** . Insira ou altere o valor do parâmetro na grade **Parâmetros** . Quando tiver terminado, escolha o botão **Salvar** .
   
    ![A caixa de diálogo **Editar Parâmetros**][2]
4. Use a caixa de seleção **Atualizar o Aplicativo** para especificar se esta ação de publicação é uma atualização. Ações de publicação de atualização são diferentes das ações de publicação normais. Confira [Atualização de Aplicativo do Service Fabric](service-fabric-application-upgrade.md) para obter uma lista das diferenças. Para definir as configurações de atualização, escolha o link **Definir Configurações de Atualização** . O editor de parâmetros de atualização é exibido. Confira [Configurar a atualização de um aplicativo do Service Fabric](service-fabric-visualstudio-configure-upgrade.md) para saber mais sobre os parâmetros de atualização.
5. Escolha o botão **Versões do Manifesto...** para exibir a caixa de diálogo **Editar Versões**. Você precisa atualizar as versões do aplicativo e do serviço para que uma atualização ocorra. Confira o [Tutorial de atualização do aplicativo Service Fabric](service-fabric-application-upgrade-tutorial.md) para saber como as versões do aplicativo e do manifesto do serviço afetam um processo de atualização.
   
    ![A caixa de diálogo **Editar Versões**][3]
   
    Se o aplicativo e as versões de serviço usarem o controle de versão semântico, por exemplo, 1.0.0, ou valores numéricos no formato 1.0.0.0, selecione a opção **Atualizar automaticamente as versões do aplicativo e do serviço** . Quando você escolhe essa opção, os números de versão do serviço e do aplicativo são atualizados automaticamente sempre que um código, uma configuração ou a versão do pacote de dados é atualizado. Se você preferir editar as versões manualmente, desmarque a caixa de seleção para desativar esse recurso.
   
   > [!NOTE]
   > Para que todas as entradas do pacote apareçam em um projeto de ator, primeiro compile o projeto a fim de gerar as entradas nos arquivos de Manifesto do Serviço.
   > 
   > 
6. Quando você terminar de especificar todas as configurações necessárias, escolha o botão **Publicar** para publicar seu aplicativo no cluster do Service Fabric selecionado. As configurações especificadas por você são aplicadas ao processo de publicação.

## <a name="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters"></a>Publicar em um ponto de extremidade de cluster arbitrário (incluindo clusters de terceiros)
A experiência de publicação do Visual Studio é otimizada para publicação em clusters remotos associados a uma de suas assinaturas do Azure. No entanto, é possível publicar em pontos de extremidade arbitrários (como clusters de parte do Service Fabric) editando diretamente o XML do perfil de publicação. Como descrito acima, três perfis de publicação são fornecidos por padrão, **Local.1Node.xml**, **Local.5Node.xml** e **Cloud.xml**, mas você pode criar perfis adicionais para ambientes diferentes. Por exemplo, você pode querer criar um perfil de publicação em clusters de terceiros, talvez chamado **Party.xml**.

Se você está se conectando a um cluster não seguro, tudo de que precisa é o ponto de extremidade de conexão do cluster, assim como `partycluster1.eastus.cloudapp.azure.com:19000`. Nesse caso, o ponto de extremidade de conexão no perfil de publicação se assemelharia a este:

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Se você estiver se conectando a um cluster protegido, precisará também fornecer os detalhes do certificado de cliente do repositório local para ser usado na autenticação. Para obter mais detalhes, confira [Configurar conexões seguras para um cluster do Service Fabric](service-fabric-visualstudio-configure-secure-connections.md).

  Depois de configurar o seu perfil de publicação, você pode referenciá-lo na caixa de diálogo de publicação, conforme mostrado abaixo.

  ![Novo perfil de publicação na caixa de diálogo de publicação][4]

  Observe que, nesse caso, o novo perfil de publicação aponta para um dos arquivos de parâmetro do aplicativo padrão. Isso é ideal se você deseja publicar a mesma configuração de aplicativo em vários ambientes. Por outro lado, quando você deseja ter configurações diferentes para cada ambiente que quiser publicar, faz sentido criar um arquivo de parâmetros de aplicativo correspondente.

## <a name="next-steps"></a>Próximas etapas
Para saber como automatizar o processo de publicação em um ambiente de integração contínua, confira [Set up Service Fabric continuous integration](service-fabric-set-up-continuous-integration.md)(Configurar a integração contínua no Service Fabric).

[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png
