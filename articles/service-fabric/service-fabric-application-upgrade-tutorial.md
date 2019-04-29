---
title: Tutorial de atualização de aplicativo do Service Fabric | Microsoft Docs
description: Este artigo descreve a experiência de implantação de um Aplicativo do Service Fabric, a alteração do código e a distribuição de uma atualização usando o Visual Studio.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 8fe0bf9c8827b7248195f89377176fd834845e32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615224"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Tutorial de atualização do aplicativo Service Fabric usando o Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

O Azure Service Fabric simplifica o processo de atualização de aplicativos em nuvem, garantindo que apenas os serviços alterados sejam atualizados e que a integridade do aplicativo seja monitorada durante todo o processo de atualização. Além disso, ele reverte automaticamente o aplicativo para a versão anterior ao encontrar problemas. As atualizações de aplicativo do Service Fabric não apresentam *Nenhum Tempo de Inatividade*, pois o aplicativo pode ser atualizado sem qualquer tempo de inatividade. Este tutorial aborda como concluir uma atualização sem interrupção por meio do Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Etapa 1: Criar e publicar o exemplo de objetos visuais
Primeiro, baixe o aplicativo [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) do GitHub. Em seguida, crie e publique o aplicativo clicando com o botão direito do mouse no projeto do aplicativo, **VisualObjects** e selecionando o comando **Publicar** no item de menu Service Fabric.

![Menu de contexto para um aplicativo do Service Fabric][image1]

A seleção de **Publicar** exibe um pop-up, e você pode definir o **Perfil de destino** como **PublishProfiles\Local.xml**. A janela deve parecer com o seguinte antes de clicar em **Publicar**.

![Publicar um aplicativo do Service Fabric][image2]

Agora, clique em **Publicar** na caixa de diálogo. Você pode usar o [Gerenciador do Service Fabric para exibir o cluster e o aplicativo](service-fabric-visualizing-your-cluster.md). O aplicativo Visual Objects tem um serviço Web que pode ser acessado digitando [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/) na barra de endereços do navegador.  Você deve ver 10 objetos visuais flutuantes na tela.

**OBSERVAÇÃO:** Se implantando `Cloud.xml` perfil (Azure Service Fabric), o aplicativo deve estar disponível em **http://{ServiceFabricName}. { Region}.cloudapp.Azure.com:8081/visualobjects/**. Verifique se você tem `8081/TCP` configurado no Balanceador de Carga (localize o Balanceador de Carga no mesmo grupo de recursos que a instância do Service Fabric).

## <a name="step-2-update-the-visual-objects-sample"></a>Etapa 2: Atualizar o exemplo de Objetos Visuais
Você pode notar que a com a versão implantada na Etapa 1, os objetos visuais não giram. Vamos atualizar esse aplicativo para um onde os objetos visuais possam girar.

Selecione o projeto VisualObjects.ActorService na solução VisualObjects e abra o arquivo **VisualObjectActor.cs** . Nesse arquivo, acesse o método `MoveObject`, comente `visualObject.Move(false)` e remova a marca de comentário de `visualObject.Move(true)`. Essa alteração de código gira os objetos após a atualização do serviço.  **Agora você pode compilar (não recompilar) a solução**, que compilará os projetos modificados. Se você selecionar *Recompilar todos*, terá que atualizar as versões de todos os projetos.

Também precisamos definir a versão do nosso aplicativo. Você pode usar a opção **Editar Versões do Manifesto** do Visual Studio depois de clicar com o botão direito do mouse no projeto **VisualObjects** para fazer as alterações de versão. Selecionar esta opção abrirá a caixa de diálogo para edição de versões da seguinte maneira:

![Caixa de diálogo Controle de Versão][image3]

Atualize as versões dos projetos modificados e seus pacotes de código, juntamente com o aplicativo, para a versão 2.0.0. Depois que as alterações forem feitas, o manifesto deverá parecer com o seguinte (as partes em negrito mostram as alterações):

![Atualizando versões][image4]

As ferramentas do Visual Studio podem fazer rollups automáticos das versões ao selecionar **Atualizar automaticamente o aplicativo e as versões de serviço**. Se você usar [SemVer](http://www.semver.org), será necessário atualizar a versão do código e/ou apenas do pacote de configuração se essa opção for selecionada.

Salve as alterações e marque a caixa **Atualizar o Aplicativo** .

## <a name="step-3--upgrade-your-application"></a>Etapa 3:  Atualizar seu aplicativo
Familiarize-se com os [parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md) e o [processo de atualização](service-fabric-application-upgrade.md) para ter uma boa compreensão dos vários parâmetros de atualização, do critério de tempos limite e de integridade que podem ser aplicados. Neste passo a passo, o critério de avaliação de integridade do serviço é definido com o valor padrão (modo sem monitoramento). Você pode definir essas configurações selecionando **Definir Configurações de Atualização** e modificando os parâmetros conforme o desejado.

Agora, estamos prontos para iniciar a atualização do aplicativo selecionando **Publicar**. Essa opção atualiza o aplicativo para a versão 2.0.0 no qual os objetos giram. O Service Fabric atualiza um domínio de atualização por vez (alguns objetos são atualizados primeiro e logo na sequência os outros) e o serviço estará acessível durante a atualização. O acesso ao serviço pode ser verificado por meio de seu cliente (navegador).  

Agora, à medida que a atualização do aplicativo continua, você poderá monitorá-lo usando o Service Fabric Explorer, na guia **Atualizações em Andamento** nos aplicativos.

Em alguns minutos, todos os domínios de atualização devem estar atualizados (concluídos), e a janela de saída do Visual Studio também deverá indicar que a atualização foi concluída. E você deverá notar que *todos* os objetos visuais na janela do navegador estarão girando!

Convém tentar alterar as versões e mudar da versão 2.0.0 para a versão 3.0.0 como um exercício, ou até mesmo da versão 2.0.0 de volta para a versão 1.0.0. Teste as possibilidades com políticas de integridade e tempos limite para ficar familiarizado com eles. Ao implantar em um cluster do Azure em vez de um cluster local, os parâmetros usados podem ter diferentes. É recomendável que você defina os tempos limite forma prudente.

## <a name="next-steps"></a>Próximas etapas
[Atualização do aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você uma atualização de aplicativo usando o PowerShell.

Controle como seu aplicativo é atualizado usando [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Torne suas atualizações de aplicativo compatíveis aprendendo a usar a [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando os [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns em atualizações de aplicativo consultando as etapas em [Solução de problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
