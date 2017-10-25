---
title: "Adicionando Serviços Móveis usando serviços conectados no Visual Studio | Microsoft Docs"
description: "Adicionar Serviços Móveis usando a caixa de diálogo Adicionar Serviços Conectados do Visual Studio"
services: visual-studio-online
documentationcenter: na
author: mlhoop
manager: douge
editor: 
ms.assetid: 75c3cb93-88e1-476d-a416-f34caa3608e3
ms.service: visual-studio-online
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: mobile
ms.date: 12/16/2015
ms.author: mlearned
ms.openlocfilehash: d185fdafebad56f8970e390b2a0672c3fb84df8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="adding-mobile-services-by-using-visual-studio-connected-services"></a>Adicionando Serviços Móveis usando os serviços conectados do Visual Studio
Com o Visual Studio 2015, você pode se conectar a Serviços Móveis do Azure usando a caixa de diálogo **Adicionar Serviço Conectado** . É possível se conectar de qualquer aplicativo cliente C#, qualquer aplicativo JavaScript ou aplicativo Cordova entre plataformas. Uma vez conectado, você criar e acessar dados, criar APIs personalizadas e trabalhos agendados, ou adicionar suporte para notificações por push.  A operação de serviços conectados adiciona todas as referências apropriadas e o código de conexão. Você também pode aproveitar o suporte interno para autenticação com uma variedade de esquemas conhecidos de identidade, como o AD do Azure, Facebook, Twitter e Contas da Microsoft.

## <a name="supported-project-types"></a>Tipos de projeto com suporte
> [!NOTE]
> No Visual Studio 2015, não há suporte para adição de Serviços Móveis do Azure a projetos Windows Universal (Windows 10) usando a caixa de diálogo Adicionar Serviços Conectados. É possível adicionar Serviços Móveis do Azure instalando os pacotes adequados usando o Gerenciador de Pacotes NuGet para seu projeto.
> 
> 

Você pode usar a caixa de diálogo Serviços Conectados para se conectar a Serviços Móveis do Azure nos tipos de projeto a seguir.

* Projetos de aplicativo Windows 8.1 Store, Phone e Universal para .NET
* Projetos de aplicativo Windows 8.1 Store, Phone e Universal para JavaScript
* Projetos criados usando o Visual Studio Tools para Apache Cordova

## <a name="connect-to-azure-mobile-services-using-the-add-connected-services-dialog"></a>Conectar-se aos Serviços Móveis do Azure usando a caixa de diálogo Adicionar Serviços Conectados
1. Verifique se você tem uma conta do Azure. Se não tiver uma conta do Azure, você poderá assinar uma versão de [avaliação gratuita](http://go.microsoft.com/fwlink/?LinkId=518146).
2. Abra a caixa de diálogo **Adicionar Serviços Conectados** .
   
   * Para aplicativos .NET, abra o projeto no Visual Studio, abra o menu de contexto do nó **Referências** no Gerenciador de Soluções e escolha **Adicionar Serviço Conectado**
     
        ![Conectar-se ao Serviço Móvel do Azure](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)
   * Para projetos de aplicativo Apache Cordova, abra o projeto no Visual Studio, abra o menu de contexto do nó do projeto no Gerenciador de Soluções e escolha **Adicionar Serviço Conectado**.
3. Na caixa de diálogo **Adicionar Serviço Conectado**, escolha **Serviços Móveis do Azure** e escolha o botão **Configurar**. Você será solicitado a fazer logon no Azure, se ainda não o fez.
   
    ![Adicionando um Serviço Móvel do Azure](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)
4. Na caixa de diálogo **Serviços Móveis do Azure** , escolha um serviço móvel existente, se houver. Se você precisar criar um novo serviço móvel do Azure, siga o procedimento abaixo para fazer isso. Caso contrário, avance para a próxima etapa.
   
    Para criar uma nova conta de serviço móvel:
   
   1. escolha o link **Criar serviço** na parte inferior da caixa de diálogo.
       ![Adicionar novo serviço conectado móvel](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)
   2. Na caixa de diálogo **Criar Serviço Móvel**, você pode escolher um serviço móvel de back-end JavaScript ou um serviço móvel de back-end .NET na lista suspensa **Tempo de Execução**. 
      
       ![Criando um serviço móvel](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)
      
       Um serviço de back-end JavaScript é simples e eficiente. Se você criar um serviço móvel de back-end JavaScript, o código JavaScript do servidor será armazenado na nuvem, mas você poderá editar scripts de servidor usando o Gerenciador de Servidores ou o Portal de Gerenciamento do Azure. 
      
       Um serviço móvel back-end do .NET fornece a toda a potência e a flexibilidade da API Web e do Entity Framework. Se você criar um serviço móvel back-end do .NET, um projeto será criado e adicionado à sua solução. 
   3. Escolha a **Região** onde deseja o serviço móvel e insira um nome de usuário e uma senha para o servidor.
   4. Depois de inserir todas as informações necessárias, escolha o botão **Criar** para criar o serviço móvel.
   5. O novo serviço móvel deve aparecer na lista de serviço, na caixa de diálogo **Serviços Móveis do Azure** . Escolha o novo serviço móvel na lista e escolha o botão **Adicionar** para adicionar o serviço ao seu projeto.
5. Examine a página de introdução que aparece e descubra como o projeto foi modificado. Uma página de introdução aparece no navegador sempre que você adiciona um serviço conectado. É possível examinar as próximas etapas sugeridas e os exemplos de código ou alternar para a página "O que aconteceu" para ver quais referências foram adicionadas ao projeto e como o código e os arquivos de configuração foram modificados.
6. Usando os exemplos de código como guia, comece a escrever o código para acessar seu serviço móvel!

## <a name="how-your-project-is-modified"></a>Como o projeto é modificado
Como o Visual Studio modifica seu projeto depende do tipo de projeto. Para aplicativos cliente C#, consulte [O que aconteceu — projetos C#](http://go.microsoft.com/fwlink/p/?LinkId=513119). Para aplicativos cliente JavaScript, consulte [O que aconteceu — projetos JavaScript](http://go.microsoft.com/fwlink/p/?LinkId=513120). Para aplicativos Cordova, consulte [O que aconteceu — projetos Cordova](http://go.microsoft.com/fwlink/p/?LinkId=513116).

## <a name="next-steps"></a>Próximas etapas
Faça perguntas e obtenha ajuda: 

* [Fórum do MSDN: Serviços Móveis do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)
* [Serviços Móveis do Azure no Blog da equipe do Microsoft Azure](https://azure.microsoft.com/blog/topics/mobile/)
* [Serviços Móveis do Azure em azure.microsoft.com](https://azure.microsoft.com/services/mobile-services/)
* [Documentação dos Serviços Móveis do Azure em azure.microsoft.com](https://azure.microsoft.com/documentation/services/mobile-services/)

