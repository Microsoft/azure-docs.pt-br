---
title: Gerenciar seus aplicativos do Azure Service Fabric no Visual Studio | Microsoft Docs
description: Use o Visual Studio para criar, desenvolver, empacotar, implantar e depurar seus aplicativos e serviços do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: 4744858869e10094389be58ddd3960cb8cc2773a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720006"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Usar o Visual Studio para simplificar a escrita e o gerenciamento de seus aplicativos do Service Fabric
É possível gerenciar os serviços e aplicativos do Service Fabric do Azure por meio do Visual Studio. Depois de [configurar o ambiente de desenvolvimento](service-fabric-get-started.md),você pode usar o Visual Studio para criar aplicativos do Service Fabric, adicionar serviços, ou pacotes, registrar e implantar aplicativos no cluster de desenvolvimento local.

## <a name="deploy-your-service-fabric-application"></a>Implantar o aplicativo do Service Fabric
Por padrão, a implantação de um aplicativo combina as etapas a seguir em uma única operação:

1. Criar o pacote de aplicativo
2. Carregar o pacote de aplicativo no repositório de imagens
3. Registrar o tipo de aplicativo
4. Remover as instâncias de aplicativo em execução
5. Criando uma instância do aplicativo

No Visual Studio, pressionar **F5** também implanta seu aplicativo e anexa o depurador a todas as instâncias do aplicativo. Você pode usar **Ctrl + F5** para implantar um aplicativo sem depuração ou pode publicar um cluster local ou remoto usando o perfil de publicação.

### <a name="application-debug-mode"></a>Modo de Depuração do Aplicativo
O Visual Studio fornece uma propriedade chamada **Modo de Depuração de Aplicativo**, que controla como você deseja que o Visual Studio trate da implantação de aplicativo como parte da depuração.

#### <a name="to-set-the-application-debug-mode-property"></a>Para definir a propriedade Modo de Depuração do Aplicativo
1. No menu de atalho do projeto de aplicativo do Service Fabric (*.sfproj), escolha **Propriedades** (ou pressione a tecla **F4**).
2. Na janela **Propriedades**, defina a propriedade **Modo de Depuração do Aplicativo**.

![Definir a Propriedade Modo de Depuração do Aplicativo][debugmodeproperty]

#### <a name="application-debug-modes"></a>Modos de depuração do aplicativo

1. **Atualizar aplicativo** Esse modo permite que você altere e depurar seu código rapidamente e dá suporte à edição de arquivos da Web estáticos durante a depuração. Esse modo só funciona se o cluster de desenvolvimento local está no modo de 1 Nó. Esse é o modo de depuração do aplicativo padrão.
2. **Remover Aplicativo** faz com que o aplicativo seja removido quando a sessão de depuração termina.
3. **Atualização Automática**: o aplicativo continua em execução quando a sessão de depuração termina. A próxima sessão de depuração tratará a implantação como uma atualização. O processo de atualização preserva todos os dados inseridos em uma sessão de depuração anterior.
4. **Manter Aplicativo** O aplicativo é mantido em execução no cluster quando a sessão de depuração termina. No início da próxima sessão de depuração, o aplicativo será removido.

Na **Atualização Automática** , os dados são preservados com a aplicação dos recursos de atualização de aplicativo do Service Fabric. Para obter mais informações sobre como atualizar aplicativos e como executar uma atualização em um ambiente real, confira [Atualização de aplicativos do Service Fabric](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Adicione um serviço ao aplicativo da Malha de Serviços
Você pode adicionar novos serviços a seu aplicativo para estender sua funcionalidade. Para garantir que o serviço esteja incluído no seu pacote de aplicativos, adicione o serviço usando o item de menu **Novo Serviço de Malha...** .

![Adicionar um novo serviço Service Fabric][newservice]

Selecione um tipo de projeto da Malha do Serviço para adicionar ao aplicativo e especifique um nome para o serviço.  Confira [Como escolher uma estrutura para o serviço](service-fabric-choose-framework.md) para ajudar com a decisão de que tipo de serviço usar.

![Selecionar um tipo de projeto do serviço Service Fabric para adicionar ao aplicativo][addserviceproject]

O novo serviço é adicionado à solução e ao pacote de aplicativo existente. As referências de serviço e uma instância de serviço padrão serão adicionados ao manifesto do aplicativo, fazendo com que o serviço seja criado e iniciado na próxima vez em que você implantar o aplicativo.

![O novo serviço é adicionado ao manifesto do aplicativo][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Empacotar o aplicativo do Service Fabric
Para implantar o aplicativo e seu serviço em um cluster, você precisa criar um pacote de aplicativos.  O pacote organiza o manifesto do aplicativo, os manifestos do serviço e outros arquivos necessários em um layout específico.  O Visual Studio configura e gerencia o pacote na pasta do projeto do aplicativo, no diretório 'pkg'.  Clicar em **Pacote** no menu de contexto **Aplicativo** cria ou atualiza o pacote de aplicativos.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Remover aplicativos e tipos de aplicativo usando o Gerenciador de Nuvem
Você pode executar operações de gerenciamento de cluster básico no Visual Studio usando o Cloud Explorer, que pode ser iniciado pelo menu **Exibir** . Por exemplo, você pode excluir aplicativos e desprovisionar tipos de aplicativos em clusters locais ou remotos.

![Remover um aplicativo][removeapplication]

> [!TIP]
> Para uma funcionalidade de gerenciamento de cluster mais avançada, confira [Visualizando o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
* [Modelo de aplicativo da Malha do Serviço](service-fabric-application-model.md)
* [Implantação de aplicativo da Malha do Serviço](service-fabric-deploy-remove-applications.md)
* [Gerenciando parâmetros do aplicativo para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md)
* [Depurando o aplicativo da Malha do Serviço](service-fabric-debugging-your-application.md)
* [Visualização do cluster usando o Gerenciador do Service Fabric](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
