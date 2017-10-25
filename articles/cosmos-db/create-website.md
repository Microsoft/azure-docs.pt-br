---
title: "Implantar um aplicativo Web com um modelo – Azure Cosmos DB | Microsoft Docs"
description: "Saiba como implantar uma conta do Azure Cosmos DB, Aplicativos Web do Serviço de Aplicativo do Azure e um aplicativo Web de exemplo usando um modelo do Azure Resource Manager."
services: cosmos-db, app-service\web
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 087d8786-1155-42c7-924b-0eaba5a8b3e0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: mimig
ms.openlocfilehash: 67c11277142d6616fe77179e03b55ed08993b818
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Implantar o Azure Cosmos DB e Aplicativos Web do Serviço de Aplicativo do Azure usando um modelo do Azure Resource Manager
Este tutorial mostra como usar um modelo do Azure Resource Manager para implantar e integrar o [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), um aplicativo Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) e um aplicativo Web de exemplo.

Usando modelos do Azure Resource Manager, é possível automatizar com facilidade a implantação e a configuração de seus recursos do Azure.  Este tutorial mostra como implantar um aplicativo Web e configurar automaticamente as informações de conexão da conta do Azure Cosmos DB.

Depois de concluir este tutorial, você estará apto a responder às seguintes perguntas:  

* Como fazer para usar um modelo do Azure Resource Manager para implantar e integrar uma conta do Azure Cosmos DB e um aplicativo Web no Serviço de Aplicativo do Azure?
* Como fazer para usar um modelo do Azure Resource Manager para implantar e integrar uma conta do Azure Cosmos DB, um aplicativo Web nos Aplicativos Web do Serviço de Aplicativo e um aplicativo Webdeploy?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Pré-requisitos
> [!TIP]
> Embora este tutorial não suponha que você tenha uma experiência anterior com modelos do Azure Resource Manager ou do JSON, caso queira modificar os modelos referenciados ou as opções de implantação, será necessário ter conhecimento em cada uma dessas áreas.
> 
> 

Antes de seguir as instruções neste tutorial, certifique-se de ter o seguinte:

* Uma assinatura do Azure. O Azure é uma plataforma baseada em assinatura.  Para obter mais informações sobre como adquirir uma assinatura, confira [Opções de compra](https://azure.microsoft.com/pricing/purchase-options/), [Ofertas para membros](https://azure.microsoft.com/pricing/member-offers/) ou [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a id="CreateDB"></a>Etapa 1: Baixar os arquivos de modelo
Vamos começar baixando os arquivos de modelo que usaremos neste tutorial.

1. Baixe o modelo da [amostra Criar uma conta do Azure Cosmos DB, Aplicativos Web e implantar um aplicativo de demonstração](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) em uma pasta local (por exemplo, C:\Azure Cosmos DBTemplates). Esse modelo implantará uma conta do Azure Cosmos DB, um aplicativo Web do Serviço de Aplicativo e um aplicativo Web.  Ele também configurará automaticamente o aplicativo Web para se conectar à conta do Azure Cosmos DB.
2. Baixe o modelo da [amostra Criar uma conta do Azure Cosmos DB e Aplicativos Web](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) em uma pasta local (por exemplo, C:\Azure Cosmos DBTemplates). Esse modelo implantará uma conta do Azure Cosmos DB e um aplicativo Web do Serviço de Aplicativo e modificará as configurações de aplicativo do site para exibir com facilidade as informações de conexão do Azure Cosmos DB; no entanto, ele não inclui um aplicativo Web.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Etapa 2: Implantar a conta do Azure Cosmos DB, o aplicativo Web do Serviço de Aplicativo e uma amostra de aplicativo de demonstração
Agora, vamos implantar nosso primeiro modelo.

> [!TIP]
> O modelo não valida se os nomes do aplicativo Web e da conta do Azure Cosmos DB inseridos abaixo são a) válidos e b) disponíveis.  É altamente recomendável que você verifique a disponibilidade dos nomes que planeja fornecer antes de enviar a implantação.
> 
> 

1. Faça logon no [Portal do Azure](https://portal.azure.com), clique em Novo e procure "Implantação de modelo".
    ![Captura de tela da interface do usuário da implantação do modelo](./media/create-website/TemplateDeployment1.png)
2. Selecione o item Implantação de modelo e clique em **Criar** ![Captura de tela da interface do usuário de implantação de modelo](./media/create-website/TemplateDeployment2.png)
3. Clique em **Editar modelo**, cole o conteúdo do arquivo de modelo DocDBWebsiteTodo.json e clique em **Salvar**.
   ![Captura de tela da interface do usuário da implantação do modelo](./media/create-website/TemplateDeployment3.png)
4. Clique em **Editar parâmetros**, forneça valores para cada um dos parâmetros obrigatórios e clique em **OK**.  Os parâmetros são os seguintes:
   
   1. SITENAME: especifica o nome do aplicativo Web do Serviço de Aplicativo e é usado para construir a URL que será usada para acessar o aplicativo Web (por exemplo, se você especificar “mydemodocdbwebapp”, a URL pela qual você acessará o aplicativo Web será mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: especifica o nome do plano de hospedagem do Serviço de Aplicativo a ser criado.
   3. LOCATION: especifica a localização do Azure na qual serão criados os recursos do Azure Cosmos DB e do aplicativo Web.
   4. DATABASEACCOUNTNAME: especifica o nome da conta do Azure Cosmos DB a ser criada.   
      
      ![Captura de tela da interface do usuário da implantação do modelo](./media/create-website/TemplateDeployment4.png)
5. Escolha um Grupo de recursos existente ou forneça um nome para criar um novo grupo de recursos e escolha um local para ele.

    ![Captura de tela da interface do usuário da implantação do modelo](./media/create-website/TemplateDeployment5.png)
6. Clique em **Examinar termos legais**, **Comprar** e em **Criar** para iniciar a implantação.  Selecione **Fixar no painel**, para que a implantação resultante seja facilmente visível em sua home page do portal do Azure.
   ![Captura de tela da interface do usuário da implantação do modelo](./media/create-website/TemplateDeployment6.png)
7. Quando a implantação for concluída, a folha do Grupo de recursos será aberta.
   ![Captura de tela da folha Grupo de recursos](./media/create-website/TemplateDeployment7.png)  
8. Para usar o aplicativo, basta navegar para a URL do aplicativo Web (no exemplo acima, a URL seria http://mydemodocdbwebapp.azurewebsites.net).  Você verá o seguinte aplicativo Web:
   
   ![Aplicativo Todo de exemplo](./media/create-website/image2.png)
9. Vá em frente e crie algumas tarefas no aplicativo Web. Clique no recurso de conta do Azure Cosmos DB na lista de Recursos e, em seguida, clique em **Gerenciador de Consultas**.
    ![Captura de tela da lente Resumo com o aplicativo Web realçado](./media/create-website/TemplateDeployment8.png)  
10. Execute a consulta padrão, “SELECT * FROM c” e inspecione os resultados.  Observe que a consulta recuperou a representação JSON dos itens de tarefas que você criou na etapa 7 acima.  Fique à vontade para experimentar as consultas. Por exemplo, tente executar SELECT * FROM c WHERE c.isComplete = true para retornar todos os itens de tarefas pendentes que foram marcados como concluídos.
    
    ![Captura de tela das lâminas Gerenciador de Consultas e Resultados mostrando os resultados da consulta](./media/create-website/image5.png)
11. Fique à vontade para descobrir a experiência do portal do Azure Cosmos DB ou modificar o aplicativo de Tarefa Pendente de exemplo.  Quando você estiver pronto, vamos implantar outro modelo.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Etapa 3: Implantar a conta de documento e o exemplo de aplicativo Web
Agora, vamos implantar nosso segundo modelo.  Esse modelo é útil para mostrar como é possível injetar informações de conexão do Azure Cosmos DB, como ponto de extremidade da conta e chave mestra, em um aplicativo Web como configurações de aplicativo ou como uma cadeia de conexão personalizada. Por exemplo, talvez você tenha seu próprio aplicativo Web e deseje implantá-lo com uma conta do Azure Cosmos DB e popular as informações de conexão automaticamente durante a implantação.

> [!TIP]
> O modelo não valida se os nomes do aplicativo Web e da conta do Azure Cosmos DB inseridos abaixo são a) válidos e b) disponíveis.  É altamente recomendável que você verifique a disponibilidade dos nomes que planeja fornecer antes de enviar a implantação.
> 
> 

1. No [Portal do Azure](https://portal.azure.com), clique em Novo e procure "Implantação de modelo".
    ![Captura de tela da interface do usuário da implantação do modelo](./media/create-website/TemplateDeployment1.png)
2. Selecione o item Implantação de modelo e clique em **Criar** ![Captura de tela da interface do usuário de implantação de modelo](./media/create-website/TemplateDeployment2.png)
3. Clique em **Editar modelo**, cole o conteúdo do arquivo de modelo DocDBWebSite.json e clique em **Salvar**.
   ![Captura de tela da interface do usuário da implantação do modelo](./media/create-website/TemplateDeployment3.png)
4. Clique em **Editar parâmetros**, forneça valores para cada um dos parâmetros obrigatórios e clique em **OK**.  Os parâmetros são os seguintes:
   
   1. SITENAME: especifica o nome do aplicativo Web do Serviço de Aplicativo e é usado para construir a URL que será usada para acessar o aplicativo Web (por exemplo, se você especificar “mydemodocdbwebapp”, a URL pela qual você acessará o aplicativo Web será mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: especifica o nome do plano de hospedagem do Serviço de Aplicativo a ser criado.
   3. LOCATION: especifica a localização do Azure na qual serão criados os recursos do Azure Cosmos DB e do aplicativo Web.
   4. DATABASEACCOUNTNAME: especifica o nome da conta do Azure Cosmos DB a ser criada.   
      
      ![Captura de tela da interface do usuário da implantação do modelo](./media/create-website/TemplateDeployment4.png)
5. Escolha um Grupo de recursos existente ou forneça um nome para criar um novo grupo de recursos e escolha um local para ele.

    ![Captura de tela da interface do usuário da implantação do modelo](./media/create-website/TemplateDeployment5.png)
6. Clique em **Examinar termos legais**, **Comprar** e em **Criar** para iniciar a implantação.  Selecione **Fixar no painel**, para que a implantação resultante seja facilmente visível em sua home page do portal do Azure.
   ![Captura de tela da interface do usuário da implantação do modelo](./media/create-website/TemplateDeployment6.png)
7. Quando a implantação for concluída, a folha do Grupo de recursos será aberta.
   ![Captura de tela da folha Grupo de recursos](./media/create-website/TemplateDeployment7.png)  
8. Clique no recurso Aplicativo Web na lista Recursos e, em seguida, clique em **Configurações do Aplicativo** ![Captura de tela do grupo de recursos](./media/create-website/TemplateDeployment9.png)  
9. Observe como há configurações de aplicativo presentes para o ponto de extremidade do Azure Cosmos DB e para cada uma das chaves mestras do Azure Cosmos DB.

    ![Captura de tela das configurações do aplicativo](./media/create-website/TemplateDeployment10.png)  
10. Fique à vontade para continuar descobrindo o Portal do Azure ou seguir uma de nossas [amostras](http://go.microsoft.com/fwlink/?LinkID=402386) do Azure Cosmos DB para criar seu próprio aplicativo do Azure Cosmos DB.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Próximas etapas
Parabéns! Você implantou o Azure Cosmos DB, um aplicativo Web do Serviço de Aplicativo e um aplicativo Web de exemplo usando modelos do Azure Resource Manager.

* Para saber mais sobre o Azure Cosmos DB, clique [aqui](http://azure.com/docdb).
* Para saber mais sobre os aplicativos Web do Serviço de Aplicativo do Azure, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=325362).
* Para saber mais sobre modelos do Gerenciador de Recursos do Azure, clique [aqui](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, confira: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

