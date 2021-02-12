---
title: Implantar um aplicativo Web com um modelo – Azure Cosmos DB
description: Saiba como implantar uma conta do Azure Cosmos, Azure App aplicativos Web do serviço e um aplicativo Web de exemplo usando um modelo de Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 55d58a6c4724bd01325db029ed75d77ccc96d0f8
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333555"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>Implantar Azure Cosmos DB e Azure App serviço com um aplicativo Web do GitHub usando um modelo de Azure Resource Manager
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este tutorial mostra como fazer uma implantação "no touch" de um aplicativo Web que se conecta a Azure Cosmos DB na primeira execução sem precisar recortar e colar as informações de conexão de Azure Cosmos DB para `appsettings.json` ou para as configurações do aplicativo Azure app Services no portal do Azure. Todas essas ações são realizadas usando um modelo de Azure Resource Manager em uma única operação. Neste exemplo, vamos implantar o [Azure Cosmos DB todo o exemplo](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) de um [tutorial de aplicativo Web](sql-api-dotnet-application.md).

Os modelos do Resource Manager são bastante flexíveis e permitem compor implantações complexas em qualquer serviço no Azure. Isso inclui tarefas avançadas, como implantar aplicativos do GitHub e injetar informações de conexão em Azure App configurações de aplicativo do serviço no portal do Azure. Este tutorial mostrará como fazer as seguintes coisas usando um único modelo do Resource Manager.

* Implante uma conta do Azure Cosmos.
* Implantar um plano de hospedagem do serviço de Azure App.
* Implantar um serviço de Azure App.
* Injetar o ponto de extremidade e as chaves da conta do Azure Cosmos nas configurações do aplicativo do serviço de aplicativo na portal do Azure.
* Implante um aplicativo Web de um repositório GitHub para o serviço de aplicativo.

A implantação resultante tem um aplicativo Web totalmente funcional que pode se conectar a Azure Cosmos DB sem precisar recortar e colar a URL do ponto de extremidade Azure Cosmos DB ou as chaves de autenticação do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

> [!TIP]
> Embora este tutorial não suponha que você tenha uma experiência anterior com modelos do Azure Resource Manager ou do JSON, caso queira modificar os modelos referenciados ou as opções de implantação, é necessário ter conhecimento em cada uma dessas áreas.

## <a name="step-1-deploy-the-template"></a>Etapa 1: implantar o modelo

Primeiro, selecione o botão **implantar no Azure** abaixo para abrir o portal do Azure para criar uma implantação personalizada. Você também pode exibir o modelo de gerenciamento de recursos do Azure na [Galeria de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-webapp%2Fazuredeploy.json)

Na portal do Azure, selecione a assinatura para implantar e selecione ou crie um novo grupo de recursos. Em seguida, preencha os valores a seguir.

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="Captura de tela da interface do usuário da implantação do modelo":::

* **Região** -isso é exigido pelo Gerenciador de recursos. Insira a mesma região usada pelo parâmetro Location onde os recursos estão localizados.
* **Nome do aplicativo** -esse nome é usado por todos os recursos para esta implantação. Certifique-se de escolher um nome exclusivo para evitar conflitos com as contas de Azure Cosmos DB e serviço de aplicativo existentes.
* **Local** -a região onde os recursos são implantados.
* **Camada do plano do serviço de aplicativo** – tipo de preço do plano do serviço de aplicativo.
* **Instâncias do plano do serviço de aplicativo** – o número de trabalhadores para o plano do serviço de aplicativo.
* **URL do repositório** -o repositório para o aplicativo Web no github.
* **Branch** -a ramificação do repositório do github.
* **Nome do banco de dados** -o nome do banco de dados Cosmos do Azure.
* **Nome do contêiner** -o nome do contêiner Cosmos do Azure.

Depois de preencher os valores, selecione o botão **criar** para iniciar a implantação. Essa etapa deve levar entre 5 e 10 minutos para ser concluída.

> [!TIP]
> O modelo não valida se o nome do serviço de Azure App e o nome da conta do cosmos do Azure inseridos no modelo são válidos e estão disponíveis. É altamente recomendável que você verifique a disponibilidade dos nomes que planeja fornecer antes de enviar a implantação.


## <a name="step-2-explore-the-resources"></a>Etapa 2: explorar os recursos

### <a name="view-the-deployed-resources"></a>Exibir os recursos implantados

Depois que o modelo tiver implantado os recursos, agora você poderá ver cada um deles em seu grupo de recursos.

:::image type="content" source="./media/create-website/resource-group.png" alt-text="Grupo de Recursos":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>Exibir Cosmos DB ponto de extremidade e chaves

Em seguida, abra a conta do Azure Cosmos no Portal. A captura de tela a seguir mostra o ponto de extremidade e as chaves para uma conta do Azure Cosmos.

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Chaves Cosmos":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>Exibir as chaves de Azure Cosmos DB nas configurações do aplicativo

Em seguida, navegue até o serviço de Azure App no grupo de recursos. Clique na guia configuração para exibir as configurações do aplicativo para o serviço de aplicativo. As configurações do aplicativo contêm a conta Cosmos DB e os valores de chave primária necessários para se conectar ao Cosmos DB, bem como os nomes do banco de dados e do contêiner que foram passados da implantação do modelo.

:::image type="content" source="./media/create-website/application-settings.png" alt-text="Configurações do aplicativo":::

### <a name="view-web-app-in-deployment-center"></a>Exibir aplicativo Web na central de implantação

Em seguida, vá para a central de implantação do serviço de aplicativo. Aqui, você verá pontos de repositório para o repositório GitHub passado para o modelo. Além disso, o status abaixo indica êxito (ativo), o que significa que o aplicativo foi implantado e iniciado com êxito.

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="Centro de Implantação":::

### <a name="run-the-web-application"></a>Executar o aplicativo Web

Clique em **procurar** na parte superior da central de implantação para abrir o aplicativo Web. O aplicativo Web será aberto até a tela inicial. Clique em **criar novo** e insira alguns dados nos campos e clique em salvar. A tela resultante mostra os dados salvos em Cosmos DB.

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="Tela de Início":::

## <a name="step-3-how-does-it-work"></a>Etapa 3: como funciona

Há três elementos necessários para que isso funcione.

### <a name="reading-app-settings-at-runtime"></a>Lendo as configurações do aplicativo em tempo de execução

Primeiro, o aplicativo precisa solicitar o ponto de extremidade Cosmos DB e a chave na `Startup` classe no aplicativo web ASP.NET MVC. O [Cosmos DB fazer o exemplo](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) pode ser executado localmente, onde você pode inserir as informações de conexão em appsettings.js. No entanto, quando implantado, esse arquivo é implantado com o aplicativo. Se essas linhas em vermelho não puderem acessar as configurações de appsettings.jsem, elas serão tentadas nas configurações do aplicativo no serviço Azure App.

:::image type="content" source="./media/create-website/startup.png" alt-text="Captura de tela mostra um método com várias variáveis de cadeia de caracteres marcadas em vermelho, incluindo databaseName, ContainerName, Account e Key.":::

### <a name="using-special-azure-resource-management-functions"></a>Usando funções especiais de gerenciamento de recursos do Azure

Para que esses valores estejam disponíveis para o aplicativo quando implantados, o modelo de Azure Resource Manager pode solicitar esses valores da conta de Cosmos DB usando funções especiais de gerenciamento de recursos do Azure, incluindo [referência](../azure-resource-manager/templates/template-functions-resource.md#reference) e [listKeys](../azure-resource-manager/templates/template-functions-resource.md#listkeys) que pegam os valores da conta de Cosmos DB e os inserem nos valores das configurações do aplicativo com nomes de chave que correspondem ao que é usado no aplicativo acima em um formato ' {section: Key} Por exemplo, `CosmosDb:Account`.

:::image type="content" source="./media/create-website/template-keys.png" alt-text="Chaves de modelo":::

### <a name="deploying-web-apps-from-github"></a>Implantando aplicativos Web do GitHub

Por fim, precisamos implantar o aplicativo Web do GitHub no serviço de aplicativo. Isso é feito usando o JSON abaixo. Duas coisas com cuidado são o tipo e o nome desse recurso. Os `"type": "sourcecontrols"` valores de propriedade e são embutidos em `"name": "web"` código e não devem ser alterados.

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="Implantar do GitHub":::

## <a name="next-steps"></a>Próximas etapas

Parabéns! Você implantou Azure Cosmos DB, Azure App Service e um aplicativo Web de exemplo que tem automaticamente as informações de conexão necessárias para se conectar ao Cosmos DB, tudo em uma única operação e sem precisar recortar e colar informações confidenciais. Usando esse modelo como ponto de partida, você pode modificá-lo para implantar seus próprios aplicativos Web da mesma maneira.

* Para o modelo de Azure Resource Manager para este exemplo, vá para a [Galeria de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)
* Para o código-fonte do aplicativo de exemplo, acesse [Cosmos DB para fazer o aplicativo no GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app).
