---
title: Provisionar e implantar microsserviços previsíveis - Serviço de Aplicativo do Azure
description: Saiba como implantar um aplicativo composto por microsserviços no Serviço de Aplicativo do Azure como uma única unidade e de maneira previsível usando modelos de grupo de recursos JSON e scripts do PowerShell.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e6d18222e15f62f12592362827b6dbc4a3d7dfbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60766590"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Provisionar e implantar microsserviços previsíveis no Azure
Este tutorial mostra como provisionar e implantar um aplicativo composto por [microsserviços](https://en.wikipedia.org/wiki/Microservices) no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) como uma única unidade e de maneira previsível usando modelos do grupo de recursos JSON e scripts do PowerShell. 

Ao provisionar e implantar aplicativos em alta escala que são compostos por microsserviços totalmente separados, a repetitividade e a previsibilidade são essenciais para se obter êxito. [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) permite criar microsserviços que incluem aplicativos Web, back-ends móveis e aplicativos de API. O [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permite gerenciar todos os microsserviços como uma unidade, juntamente com as dependências de recurso, como as configurações de controle do código-fonte e banco de dados. Agora, você também pode implantar esse aplicativo usando modelos JSON e scripts simples do PowerShell. 

## <a name="what-you-will-do"></a>O que você fará
No tutorial, você implantará um aplicativo que inclui:

* Dois aplicativos do Serviço de Aplicativo (isto é, dois microsserviços)
* Um banco de dados SQL de back-end
* Configurações do aplicativo, cadeias de conexão e controle do código-fonte
* Application Insights, alertas, configurações de dimensionamento automático

## <a name="tools-you-will-use"></a>Ferramentas que você utilizará
Neste tutorial, você utilizará as ferramentas a seguir. Como não se trata de uma discussão abrangente sobre ferramentas, escolherei o cenário de ponta a ponta e oferecerei uma breve introdução a respeito cada uma, indicando também onde você pode encontrar mais informações sobre ela. 

### <a name="azure-resource-manager-templates-json"></a>Modelos do Gerenciador de Recursos do Azure (JSON)
Sempre que você cria um aplicativo no Serviço de Aplicativo do Azure, por exemplo, o Azure Resource Manager usa um modelo JSON para criar todo o grupo de recursos com os recursos do componente. Um modelo complexo do [Azure Marketplace](/azure/marketplace) pode incluir o banco de dados, contas de armazenamento, o Plano do Serviço de Aplicativo, o próprio aplicativo, regras de alerta, configurações do aplicativo, configurações de dimensionamento automático e muito mais, e todos esses modelos estão disponíveis para você por meio do PowerShell. Para obter mais informações sobre os modelos do Gerenciador de Recursos do Azure, consulte [Criação de Modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>SDK do Azure para Visual Studio 2.6
O SDK mais recente contém melhorias para o suporte a modelos do Gerenciador de Recursos no editor JSON. Você pode usar isto para criar rapidamente um modelo de grupo de recursos do zero ou abrir um modelo do JSON existente (como um modelo baixado da galeria) para modificação, preencher o arquivo de parâmetros e até mesmo implantar o grupo de recursos diretamente de uma solução de Grupo de Recursos do Azure.

Para obter mais informações, consulte [SDK do Azure para Visual Studio 2.6](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 ou posterior
A partir da versão 0.8.0, a instalação do Azure PowerShell inclui o módulo do Gerenciador de Recursos do Azure, além do módulo do Azure. Este novo módulo permite que você escreva script para implantação de grupos de recursos.

Para obter mais informações, consulte [Usando o PowerShell do Azure com o Gerenciador de Recursos do Azure](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Gerenciador de Recursos do Azure
Essa [ferramenta de visualização](https://resources.azure.com) permite que você explore as definições de JSON de todos os grupos de recursos em sua assinatura e os recursos individuais. Na ferramenta, você pode editar as definições de JSON de um recurso, excluir uma hierarquia inteira de recursos e criar novos recursos.  As informações prontamente disponíveis nessa ferramenta serão muito úteis para criação de modelos, porque mostram quais propriedades você precisa definir para um determinado tipo de recurso, os valores corretos, etc. Você pode até mesmo criar seu grupo de recursos no [Portal do Azure](https://portal.azure.com/) e, em seguida, inspecionar suas definições JSON no gerenciador para ajudá-lo a classificar o grupo de recursos segundo os modelos.

### <a name="deploy-to-azure-button"></a>Botão Implantar no Azure
Se você usa GitHub para controle do código-fonte, você pode colocar um [botão Implantar no Azure](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) em seu arquivo LEIAME.MD, que permite uma implantação pronta para uso da interface do usuário para Azure. Embora seja possível fazê-lo para qualquer aplicativo simples, você pode estender isso para habilitar a implantação de um grupo de recursos inteiro, colocando um arquivo azuredeploy.json na raiz do repositório. Esse arquivo JSON, que contém o modelo de grupo de recursos, será usado pelo botão Implantar no Azure para criar o grupo de recursos. Para obter um exemplo, consulte a amostra [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) , que você usará neste tutorial.

## <a name="get-the-sample-resource-group-template"></a>Obter o modelo de grupo de recursos de exemplo
Agora vamos direto ao ponto.

1. Navegue até o exemplo de Serviço de Aplicativo [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) .
2. Em readme.md, clique em **Implantar no Azure**.
3. Você é levado para o site [implantar-no-azure](https://deploy.azure.com) e solicitado a inserir parâmetros de implantação. Observe que a maioria dos campos é preenchida com o nome do repositório e algumas cadeias de caracteres aleatórias para você. Você pode alterar todos os campos se desejar, mas as únicas coisas que você precisa inserir são o logon administrativo do SQL Server e a senha; então, clique em **Próximo**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Em seguida, clique em **Implantar** para iniciar o processo de implantação. Quando o processo for concluído, clique no link http://todoapp*XXXX*.azurewebsites.net para procurar o aplicativo implantado. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   A interface do usuário deve estar um pouco lenta quando você navega pela primeira vez por ela porque os aplicativos estão sendo iniciados, mas esteja convencido de que se trata de um aplicativo totalmente funcional.
5. De volta à página Implantar, clique no link **Gerenciar** para ver o novo aplicativo no Portal do Azure.
6. Na lista suspensa **Essentials** , clique no link grupo de recursos. Observe também que o aplicativo já está conectado ao repositório GitHub em **Projeto Externo**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Na folha do grupo de recursos, observe que já existem dois aplicativos e um banco de dados SQL no grupo de recursos.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Tudo o que você acabou de ver em poucos minutos é um aplicativo de dois microsserviços totalmente implantado, com todos os componentes, dependências, configurações, banco de dados e publicação contínua, configurado por uma orquestração automatizada no Gerenciador de Recursos do Azure. Tudo isso foi feito por duas coisas:

* O botão Implantar no Azure
* O arquivo azuredeploy.json na raiz do repositório

Você pode implantar esse mesmo aplicativo dezenas, centenas ou milhares de vezes e ter sempre exatamente a mesma configuração. A capacidade de repetição e a previsibilidade dessa abordagem permitem que você implante aplicativos de grande escala com facilidade e confiança.

## <a name="examine-or-edit-azuredeployjson"></a>Examinar (ou editar) AZUREDEPLOY.JSON
Agora vamos examinar como o repositório GitHub foi configurado. Você usará o editor de JSON no SDK do .NET do Azure, portanto, se você ainda não tiver instalado o [SDK .NET do Azure 2.6](https://azure.microsoft.com/downloads/), faça isso agora.

1. Clone o repositório [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) usando sua ferramenta git favorita. Na captura de tela abaixo, faço isso no Team Explorer no Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Na raiz do repositório, abra azuredeploy.json no Visual Studio. Se você não vir o painel Estrutura de Tópicos JSON, precisará instalar o SDK .NET do Azure.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Não vou descrever todos os detalhes do formato JSON, mas a seção [Mais Recursos](#resources) contém links para aprender a linguagem de modelo de grupo de recursos. Aqui, eu vou mostrar a você recursos interessantes que podem ajudá-lo a começar a fazer seu próprio modelo personalizado para implantação do aplicativo.

### <a name="parameters"></a>parâmetros
Dê uma olhada na seção de parâmetros para ver que a maioria desses parâmetros são aquilo que o botão **Implantar no Azure** solicita que você insira. O site por trás do botão **Implantar no Azure** preenche a Interface do Usuário de entrada, usando os parâmetros definidos em azuredeploy.json. Esses parâmetros são usados em todas as definições de recurso, como nomes de recurso, valores de propriedade, etc.

### <a name="resources"></a>Recursos
No nó recursos, você pode ver que 4 recursos do nível mais alto estão definidos, incluindo uma instância do SQL Server, um plano do Serviço de Aplicativo e dois aplicativos. 

#### <a name="app-service-plan"></a>Plano do Serviço de Aplicativo
Vamos começar com um recurso simples de nível raiz em JSON. Na Estrutura de Tópicos JSON, clique no plano de Serviço de Aplicativo chamado **[hostingPlanName]** para realçar o código do JSON correspondente. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Observe que o elemento `type` especifica a cadeia de caracteres em um plano do Serviço de Aplicativo (isso era chamado de farm de servidores, há muito tempo atrás) enquanto outros elementos e propriedades são preenchidos com os parâmetros definidos no arquivo JSON, sendo que este recurso não tem nenhum recurso aninhado.

> [!NOTE]
> Observe também que o valor de `apiVersion` informa ao Azure com qual versão da API REST usar a definição de recurso JSON, e ele pode afetar como o recurso deve ser formatado dentro do `{}`. 
> 
> 

#### <a name="sql-server"></a>SQL Server
Em seguida, clique no recurso do SQL Server chamado **SQLServer** na Estrutura de Tópicos JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Observe o seguinte sobre o código JSON realçado:

* O uso de parâmetros garante que os recursos criados sejam nomeados e configurados de modo que torne-os consistentes entre si.
* O recurso SQLServer tem dois recursos aninhados, cada um com um valor diferente para `type`.
* Os recursos aninhados dentro de `“resources”: […]`, em que o banco de dados e as regras de firewall são definidas, têm um elemento `dependsOn` que especifica a ID de recurso do recurso SQLServer no nível de raiz. Isso informa ao Gerenciador de Recursos do Azure que, "antes de você criar esse recurso, o outro recurso já deve existir; e se o outro recurso estiver definido no modelo, crie-o primeiro".
  
  > [!NOTE]
  > Para obter informações detalhadas sobre como usar a função `resourceId()`, consulte [Funções do Modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid).
  > 
  > 
* O efeito do elemento `dependsOn` é que o Gerenciador de Recursos do Azure pode saber quais recursos podem ser criados em paralelo e quais recursos devem ser criados sequencialmente. 

#### <a name="app-service-app"></a>Aplicativo de Serviço de Aplicativo
Agora, vamos passar para os aplicativos em si, que são mais complicados. Clique no aplicativo [variables(‘apiSiteName’)] na estrutura de tópicos JSON para destacar seu código JSON. Você perceberá que as coisas estão ficando muito mais interessantes. Para esse fim, falarei sobre um dos recursos por vez:

##### <a name="root-resource"></a>Recurso raiz
O aplicativo depende de dois recursos diferentes. Isso significa que o Azure Resource Manager criará o aplicativo somente depois que ambos o plano do Serviço de Aplicativo e a instância do SQL Server forem criados.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Configurações do aplicativo
As configurações do aplicativo também serão definidas como um recurso aninhado.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

No elemento `properties` para `config/appsettings`, você tem duas configurações de aplicativo no formato `"<name>" : "<value>"`.

* `PROJECT` é uma [configuração KUDU](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) que, em uma solução do Visual Studio com vários projetos, informa à implantação do Azure qual projeto usar. Mostrarei posteriormente como o controle do código-fonte está configurado, mas já que o código ToDoApp está em uma solução do Visual Studio com vários projetos, precisamos dessa configuração.
* `clientUrl` é simplesmente um aplicativo de configuração que o código do aplicativo usa.

##### <a name="connection-strings"></a>Cadeias de conexão
As cadeias de conexão também serão definidas como um recurso aninhado.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

No elemento `properties` para `config/connectionstrings`, cada cadeia de conexão também é definida como um par nome:valor, com o formato específico de `"<name>" : {"value": "…", "type": "…"}`. Para o elemento `type`, os valores possíveis são `MySql`, `SQLServer`, `SQLAzure` e `Custom`.

> [!TIP]
> Para obter uma lista definitiva dos tipos de cadeia de caracteres de conexão, execute o seguinte comando no Azure PowerShell: \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Controle do código-fonte
As configurações do controle do código-fonte também serão definidas como um recurso aninhado. O Gerenciador de Recursos do Azure usa esse recurso para configurar a publicação contínua (consulte a advertência sobre `IsManualIntegration` posteriormente) e também para iniciar a implantação do código do aplicativo automaticamente durante o processamento do arquivo JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` e `branch` devem ser bastante intuitivos e devem apontar para o repositório Git e o nome da ramificação por meio da qual publicar. Mais uma vez, estes são definidos pelos parâmetros de entrada. 

Observe no elemento `dependsOn` que, além do recurso de aplicativo, `sourcecontrols/web` também depende de `config/appsettings` e `config/connectionstrings`. Isso ocorre porque quando `sourcecontrols/web` é configurado, o processo de implantação do Azure tentará automaticamente implantar, compilar e iniciar o código do aplicativo. Portanto, inserir essa dependência ajuda você a garantir que o aplicativo tenha acesso às configurações e cadeias de conexão necessárias do aplicativo antes de o código desse aplicativo ser executado. 

> [!NOTE]
> Observe também que `IsManualIntegration` é definido como `true`. Esta propriedade é necessária neste tutorial porque, na verdade, você não tem o repositório GitHub e, portanto, não pode efetivamente conceder permissão ao Azure para configurar a publicação contínua por meio do [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (ou seja, enviar atualizações automáticas de repositório por push ao Azure). Você pode usar o valor padrão `false` para o repositório especificado somente se tiver configurado as credenciais do proprietário para o GitHub no [Portal do Azure](https://portal.azure.com/) previamente. Em outras palavras, se você configurou o controle do código-fonte para GitHub ou BitBucket para qualquer aplicativo no [Portal do Azure](https://portal.azure.com/) previamente, usando suas credenciais de usuário, então o Azure lembrará dessas credenciais e as utilizará sempre que você implantar qualquer aplicativo do GitHub ou BitBucket no futuro. No entanto, se você ainda não tiver feito isso, a implantação do modelo JSON falhará quando o Azure Resource Manager tentar definir configurações de controle do código-fonte do aplicativo, porque ele não poderá fazer logon no GitHub ou BitBucket com as credenciais do proprietário do repositório.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Comparar o modelo JSON com grupo de recursos implantado
Aqui, você pode percorrer todas as folhas do aplicativo no [Portal do Azure](https://portal.azure.com/), mas há outra ferramenta que é tão útil quanto esta, ou mais. Vá para a ferramenta de visualização [Gerenciador de Recursos do Azure](https://resources.azure.com) , que oferece uma representação JSON de todos os grupos de recursos em suas assinaturas, como eles existem realmente no back-end do Azure. Você também pode ver como a hierarquia JSON do grupo de recursos no Azure corresponde à hierarquia no arquivo de modelo que é usado para criá-la.

Por exemplo, quando vou para a ferramenta [Gerenciador de Recursos do Azure](https://resources.azure.com) e expando os nós no gerenciador, posso ver o grupo de recursos e os recursos de nível raiz que são coletados em seus respectivos tipos de recurso.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Se você acessar hierarquias mais baixas de um aplicativo, você deve ser capaz de ver os detalhes de configuração de aplicativo como a captura de tela a seguir:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Novamente, os recursos aninhados devem ter uma hierarquia muito semelhante àquela no seu arquivo de modelo JSON, e você verá as configurações do aplicativo, cadeias de conexão, etc., adequadamente refletidas no painel JSON. A ausência de configurações pode indicar um problema com o arquivo JSON, e pode ajudá-lo a solucionar problemas no arquivo de modelo JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Implantar o modelo de grupo de recursos por conta própria
O botão **Implantar no Azure** é ótimo, mas só permite que você implante o modelo de grupo de recursos em azuredeploy.json se você já tiver enviado azuredeploy.json por push para o GitHub. O SDK .NET do Azure também fornece as ferramentas para implantar qualquer arquivo de modelo JSON diretamente do computador local. Para fazer isso, siga as etapas abaixo:

1. No Visual Studio, clique em **Arquivo** > **Novo** > **Projeto**.
2. Clique em **Visual C#** > **Nuvem** > **Grupo de Recursos do Azure**, em seguida, clique em **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. Em **Selecionar Modelo do Azure**, selecione **Modelo em Branco** e clique em **OK**.
4. Arraste azuredeploy.json para a pasta **Modelo** do novo projeto.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. No Gerenciador de Soluções, abra o azuredeploy.json copiado.
6. Apenas para fins de demonstração, vamos adicionar alguns recursos padrão do Application Insight em nosso arquivo JSON, clicando em **Adicionar Recurso**. Se estiver interessado apenas na implantação do arquivo JSON, vá direto para as etapas de implantação.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Selecione **Application Insights para Aplicativos Web**, certifique-se de que um aplicativo e um Plano do Serviço de Aplicativo existentes estão selecionados e, em seguida, clique em **Adicionar**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Você será capaz de ver vários recursos novos que, dependendo do recurso e do que ele faz, têm dependências no plano do Serviço de Aplicativo ou então no aplicativo. Esses recursos não estão habilitados segundo sua definição existente e você vai mudar isso.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. Na estrutura de tópicos JSON, clique em **appInsights AutoScale** para destacar seu código JSON. Essa é a configuração de dimensionamento para seu plano do Serviço de Aplicativo.
9. No código realçado JSON, localize as propriedades `location` e `enabled` e defina-as conforme mostrado abaixo.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. Na estrutura de tópicos JSON, clique em **CPUHigh appInsights** para destacar seu código JSON. Este é um alerta.
11. Localize as propriedades `location` e `isEnabled` e defina-as conforme mostrado abaixo. Faça o mesmo para os outros três alertas (lâmpadas roxas).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Agora, você está pronto para implantar. Clique no projeto com o botão direito do mouse e selecione **Implantar** > **New Implantarment**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Faça logon na conta do Azure se ainda não fez isso.
14. Selecione um grupo de recursos existente em sua assinatura ou crie um novo, selecione **azuredeploy.json** e, em seguida, clique em **Editar parâmetros**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Agora você poderá editar todos os parâmetros definidos no arquivo de modelo em uma boa tabela. Parâmetros que definem padrões já terão seus valores padrão, enquanto parâmetros que definem uma lista de valores permitidos serão mostrados como listas suspensas.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Preencha todos os parâmetros vazios e use o [Endereço de repositório do GitHub para ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) na **repoUrl**. Em seguida, clique em **Salvar**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > O dimensionamento automático é um recurso oferecido no patamar **Standard** ou mais alto, enquanto alertas de nível de plano são recursos oferecidos no patamar **Básico** ou superior; você precisará definir o parâmetro **sku** como **Standard** ou **Premium** para ver todos os seus novos recursos do App Insights se iluminarem.
    > 
    > 
16. Clique em **Implantar**. Se você selecionou **Salvar senhas**, a senha será salva no arquivo de parâmetros **em texto sem formatação**. Caso contrário, será solicitado que você insira a senha do banco de dados durante o processo de implantação.

É isso! Agora, basta ir para o [Portal do Azure](https://portal.azure.com/) e até a ferramenta [Gerenciador de Recursos do Azure](https://resources.azure.com) para ver os novos alertas e configurações de dimensionamento automático adicionados ao seu aplicativo JSON implantado.

As etapas cumpridas nesta seção realizaram principalmente o seguinte:

1. Prepararam o arquivo de modelo
2. Criaram um arquivo de parâmetros para acompanhar o arquivo de modelo
3. Implantaram o arquivo de modelo juntamente com o arquivo de parâmetros

A última etapa é facilmente realizada por um cmdlet do PowerShell. Para ver o que o Visual Studio fez quando implantou seu aplicativo, abra Scripts\Deploy AzureResourceGroup.ps1. Há muito código lá, mas vou destacar todo o código relevante que necessário para implantar o arquivo de modelo com o arquivo de parâmetros.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

O último cmdlet, `New-AzureResourceGroup`, é o que executa efetivamente a ação. Tudo isso deve demonstrar a você que, com a ajuda de ferramentas, é relativamente fácil implantar seu aplicativo em nuvem de modo previsível. Sempre que você executar o cmdlet no mesmo modelo com o mesmo arquivo de parâmetros, você obterá o mesmo resultado.

## <a name="summary"></a>Resumo
No DevOps, repetitividade e previsibilidade são essenciais para qualquer implantação bem-sucedida de um aplicativo composto por microsserviços de alta escala. Neste tutorial, você implantou um aplicativo de dois microsserviços no Azure como um único grupo de recursos usando o modelo do Gerenciador de Recursos do Azure. Espera-se que ele tenha dado a você o conhecimento necessário para iniciar a conversão de seu aplicativo do Azure em um modelo e possa ser provisionado e implantado de maneira previsível. 

<a name="resources"></a>

## <a name="more-resources"></a>Mais recursos
* [Idioma de modelo do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [Criando modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [Funções de modelo do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-template-functions.md)
* [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-template-deploy.md)
* [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Solucionando problemas de implantações de grupos de recursos no Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as propriedades e a sintaxe JSON de tipos de recursos implantados neste artigo, consulte:

* [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servers/databases](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servers/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)
* [Microsoft.Web/sites/slots](/azure/templates/microsoft.web/sites/slots)
* [Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)