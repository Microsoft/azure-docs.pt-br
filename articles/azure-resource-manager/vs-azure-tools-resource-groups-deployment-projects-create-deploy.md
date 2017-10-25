---
title: Projetos de grupo de recursos do Visual Studio Azure | Microsoft Docs
description: Use o Visual Studio para criar um projeto do grupo de recursos do Azure e implantar os recursos no Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 4bd084c8-0842-4a10-8460-080c6a085bec
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: tomfitz
ms.openlocfilehash: f82f59f363507b69a729580302c2d11202e93a87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Criação e implantação de grupos de recurso do Azure por meio do Visual Studio
Com o Visual Studio e o [SDK do Azure](https://azure.microsoft.com/downloads/), você pode criar um projeto que implementa sua infraestrutura e o código no Azure. Por exemplo, você pode definir o host da Web, o site da Web e o banco de dados para seu aplicativo, e implantar essa infraestrutura juntamente com o código. Ou pode definir uma Máquina Virtual, uma Rede Virtual e uma Conta de Armazenamento e implantar essa infraestrutura juntamente com um script que é executado na Máquina Virtual. O projeto de implantação **Grupo de Recursos do Azure** permite a você implantar todos os recursos necessários em uma única operação repetida. Para obter mais informações sobre como implantar e gerenciar seus recursos, confira [Visão geral do Azure Resource Manager](resource-group-overview.md).

Os projetos do Grupo de Recursos do Azure contêm modelos JSON do Azure Resource Manager que definem os recursos que você implanta no Azure. Para saber mais sobre os elementos do modelo do Gerenciador de Recursos, confira [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md). O Visual Studio permite editar esses modelos e fornece ferramentas que simplificam o trabalho com eles.

Neste artigo, você pode implantar um aplicativo Web e o Banco de Dados SQL. No entanto, as etapas são praticamente as mesmas para qualquer tipo de recurso. Você pode implantar facilmente uma Máquina Virtual e seus recursos relacionados. O Visual Studio fornece muitos modelos iniciais diferentes para implantar cenários comuns.

Este artigo mostra o Visual Studio 2017. Se você usar o Visual Studio 2015 Atualização 2 e o SDK do Microsoft Azure para .NET 2.9, ou o Visual Studio 2013 com o SDK do Azure 2.9, sua experiência será basicamente a mesma. Você pode usar as versões do SDK do Azure 2.6 ou posterior. No entanto, sua experiência da interface do usuário pode ser diferente da mostrada neste artigo. Recomendamos que você instale a versão mais recente do [SDK do Azure](https://azure.microsoft.com/downloads/) antes de iniciar as etapas. 

## <a name="create-azure-resource-group-project"></a>Criar um projeto do Grupo de Recursos do Azure
Neste procedimento, você cria um projeto do Grupo de Recursos do Azure com um modelo do **aplicativo Web + SQL** .

1. No Visual Studio, escolha **Arquivo**, **Novo Projeto**, escolha **C#** ou **Visual Basic**. Então escolha **Nuvem** e o projeto de **Grupo de Recursos do Azure**.
   
    ![Projeto do Cloud Deployment](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)
2. Escolha o modelo que você deseja implantar no Gerenciador de Recursos do Azure. Observe que há várias opções diferentes com base no tipo de projeto que você deseja implantar. Para este artigo, escolha o modelo do **aplicativo Web + SQL** .
   
    ![Selecionar um modelo](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)
   
    O modelo que você escolhe é apenas um ponto de partida. Você pode adicionar e remover os recursos para atender a seu cenário.
   
   > [!NOTE]
   > O Visual Studio recupera uma lista dos modelos disponíveis online. A lista pode mudar.
   > 
   > 
   
    O Visual Studio cria um projeto de implantação do grupo de recursos para o aplicativo Web e o banco de dados SQL.
3. Para ver o que você criou, examine os nós no projeto de implantação.
   
    ![mostrar nós](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)
   
    Como escolhemos o modelo do aplicativo Web + SQL para este exemplo, você vê os arquivos a seguir: 
   
   | Nome do arquivo | Descrição |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Um script do PowerShell que invoca comandos do PowerShell a implantar para o Gerenciador de Recursos do Azure.<br />**Observação** O Visual Studio usa esse script do PowerShell para implantar seu modelo. As alterações feitas no script afetam a implantação no Visual Studio. Portanto, tenha cuidado. |
   | WebSiteSQLDatabase.json |O modelo do Resource Manager que define a infraestrutura que você deseja implantar no Azure e os parâmetros que você pode fornecer durante a implantação. Também define as dependências entre os recursos para que o Resource Manager implante-os na ordem correta. |
   | WebSiteSQLDatabase.parameters.json |Um arquivo de parâmetros que contém os valores necessários ao modelo. Você passa os valores do parâmetro para personalizar cada implantação. |
   
    Todos os projetos de implantação do grupo de recursos do Azure contêm esses arquivos básicos. Outros projetos podem conter arquivos adicionais para dar suporte a outras funcionalidades.

## <a name="customize-the-resource-manager-template"></a>Personalizar o modelo do Gerenciador de Recursos
Você pode personalizar um projeto de implantação modificando os modelos JSON que descrevem os recursos que você deseja implantar. JSON significa JavaScript Object Notation e é um formato de dados serializados, com o qual é fácil de trabalhar. Os arquivos JSON usam um esquema que você referencia na parte superior de cada arquivo. Se você quiser entender o esquema, poderá baixá-lo e analisá-lo. O esquema define quais elementos são válidos, os tipos e formatos dos campos, os possíveis valores enumerados e assim por diante. Para saber mais sobre os elementos do modelo do Gerenciador de Recursos, confira [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).

Para trabalhar em seu modelo, abra o **WebSiteSQLDatabase.json**.

O editor do Visual Studio fornece ferramentas para ajudá-lo a editar o modelo do Resource Manager. A janela **Estrutura de Tópicos JSON** facilita ver os elementos definidos no modelo.

![mostrar estrutura de tópicos JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

Selecionar qualquer um dos elementos na estrutura de tópicos leva você para essa parte do modelo e destaca o JSON correspondente.

![navegar o JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Você pode adicionar um recurso selecionando o botão **Adicionar Recurso** na parte superior da janela Estrutura de Tópicos JSON ou clicando com o botão direito em **recursos** e selecionando **Adicionar Novo Recurso**.

![adicionar recurso](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

Para este tutorial, selecione **Conta de Armazenamento** e forneça um nome. Forneça um nome que não tenha mais de 11 caracteres e contenha apenas números e letras minúsculas.

![adicionar armazenamento](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Observe que não só foi adicionado o recurso, mas também um parâmetro para o tipo da conta de armazenamento e uma variável para o nome da conta de armazenamento.

![mostrar estrutura de tópicos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

O parâmetro **storageType** é predefinido com os tipos permitidos e um tipo padrão. Você pode deixar esses valores ou editá-los para seu cenário. Se você não quiser permitir que alguém implante uma conta de armazenamento **Premium_LRS** com esse modelo, remova-o dos tipos permitidos. 

```json
"storageType": {
  "type": "string",
  "defaultValue": "Standard_LRS",
  "allowedValues": [
    "Standard_LRS",
    "Standard_ZRS",
    "Standard_GRS",
    "Standard_RAGRS"
  ]
}
```

O Visual Studio também fornece o intellisense para ajudá-lo a entender quais propriedades estão disponíveis ao editar o modelo. Por exemplo, para editar as propriedades de seu plano do Serviço de Aplicativo, navegue até o recurso **HostingPlan** e adicione um valor para as **propriedades**. Observe que o intellisense mostra os valores disponíveis e fornece uma descrição desse valor.

![mostrar intellisense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Você pode definir **numberOfWorkers** para 1.

```json
"properties": {
  "name": "[parameters('hostingPlanName')]",
  "numberOfWorkers": 1
}
```

## <a name="deploy-the-resource-group-project-to-azure"></a>Implantar o projeto do Grupo de Recursos para o Azure
Agora, você está pronto para implantar seu projeto. Quando você implanta um projeto do Grupo de Recursos do Azure, implanta-o em um grupo de recursos do Azure. Um grupo de recursos é um agrupamento lógico de recursos que compartilham um ciclo de vida comum.

1. No menu de atalho do nó do projeto de implantação, escolha **Implantar** > **Novo**.
   
    ![Item de menu Implantar, Nova Implantação](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)
   
    A caixa de diálogo **Implantar no Grupo de Recursos** é exibida.
   
    ![Caixa de diálogo Implantar no Grupo de Recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)
2. Na caixa suspensa de **Grupo de recursos** , escolha um grupo de recursos existente ou crie um novo. Para criar um grupo de recursos, abra a caixa suspensa **Grupo de Recursos** e escolha **Criar Novo**.
   
    ![Caixa de diálogo Implantar no Grupo de Recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)
   
    A caixa de diálogo **Criar Grupo de Recursos** é exibida. Forneça ao grupo um nome e local, e selecione o botão **Criar** .
   
    ![Caixa de diálogo Criar Grupo de Recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
3. Edite os parâmetros da implantação escolhendo o botão **Editar Parâmetros** .
   
    ![Botão Editar Parâmetros](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/edit-parameters.png)
4. Forneça valores para os parâmetros vazios e selecione o botão **Salvar** . Os parâmetros vazios são **hostingPlanName**, **administratorLogin**, **administratorLoginPassword** e **databaseName**.
   
    **hostingPlanName** : especifica o nome do [plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) a ser criado. 
   
    **administratorLogin** especifica o nome de usuário do administrador do SQL Server. Não use nomes comuns de administração como **sa** ou **admin**. 
   
    O **administratorLoginPassword** especifica uma senha para o administrador do SQL Server. A opção **Salvar senhas como texto sem formatação no arquivo de parâmetros** não é segura; portanto, não selecione essa opção. Como a senha não é salva como texto sem formatação, você precisa fornecer essa senha novamente durante a implantação. 
   
    **databaseName** especifica um nome para o banco de dados a ser criado. 
   
    ![Caixa de diálogo Editar Parâmetros](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
5. Escolha o botão **Implantar** para implantar o projeto no Azure. Um console do PowerShell é aberto fora da instância do Visual Studio. Digite a senha de administrador do SQL Server no console do PowerShell quando for solicitado. **O console do PowerShell pode estar oculto por trás de outros itens ou minimizado na barra de tarefas.** Procure esse console e selecione-o para fornecer a senha.
   
   > [!NOTE]
   > O Visual Studio pode solicitar que você instale os cmdlets do Azure PowerShell. Você precisa dos cmdlets do Azure PowerShell para implantar com êxito os grupos de recursos. Se solicitado, instale-os.
   > 
   > 
6. A implantação pode demorar alguns minutos. Nas janelas de **Saída** , confira o status da implantação. Quando a implantação tiver sido concluída, a última mensagem indicará uma implantação bem-sucedida com algo semelhante a:
   
        ... 
        18:00:58 - Successfully deployed template 'websitesqldatabase.json' to resource group 'DemoSiteGroup'.
7. Em um navegador, abra o [Portal do Azure](https://portal.azure.com/) e entre em sua conta. Para ver o grupo de recursos, selecione **Grupos de recursos** e o grupo de recursos implantado.
   
    ![selecionar grupo](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)
8. Você verá todos os recursos implantados. Observe que o nome da conta de armazenamento não é exatamente o que você especificou ao adicionar esse recurso. A conta de armazenamento deve ser exclusiva. O modelo adiciona automaticamente uma cadeia de caracteres ao nome fornecido a fim de fornecer um nome exclusivo. 
   
    ![mostrar recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)
9. Se você fizer alterações e quiser reimplantar seu projeto, escolha o grupo de recursos existente no menu de atalho do projeto do grupo de recursos do Azure. No menu de atalho, escolha **Implantar**, então, escolha o grupo de recursos implantado.
   
    ![Grupo de recursos do Azure implantado](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## <a name="deploy-code-with-your-infrastructure"></a>Implantar o código com sua infraestrutura
Neste ponto, você implantou a infraestrutura de seu aplicativo, mas não há nenhum código real implantado com o projeto. Este artigo mostra como implantar um aplicativo Web e as tabelas do Banco de Dados SQL durante a implantação. Se você estiver implantando uma Máquina Virtual em vez de um aplicativo Web, desejará executar um código no computador como parte da implantação. O processo de implantação do código para um aplicativo Web ou configurar uma Máquina Virtual é quase o mesmo.

1. Adicione esse projeto à sua solução do Visual Studio. Clique com o botão direito na solução e selecione **Adicionar** > **Novo Projeto**.
   
    ![adicionar projeto](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)
2. Adicione um **Aplicativo Web ASP.NET**. 
   
    ![adicionar aplicativo Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
3. Selecione **MVC**.
   
    ![selecionar MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
4. Depois que o Visual Studio criar seu aplicativo Web, você poderá ver os dois projetos na solução.
   
    ![mostrar projetos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)
5. Agora, você precisa certificar-se de que seu projeto de grupo de recursos esteja ciente do novo projeto. Volte ao seu projeto de grupo de recursos (AzureResourceGroup1). Clique com botão direito do mouse em **Referências** e selecione **Adicionar Referência**.
   
    ![Adicionar Referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)
6. Selecione o projeto de aplicativo Web que você criou.
   
    ![Adicionar Referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
   
    Adicionando uma referência, você vincula o projeto do aplicativo Web ao projeto do grupo de recursos e define três propriedades principais automaticamente. Confira essas propriedades na janela **Propriedades** da referência.
   
      ![ver referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
   
    As propriedades são:
   
   * As **Propriedades Adicionais** contêm o local de preparação do pacote de implantação da Web que é enviado para o Armazenamento do Azure. Observe a pasta (ExampleApp) e o arquivo (package.zip). Você precisa saber esses valores porque os fornece como parâmetros ao implantar o aplicativo. 
   * A opção **Incluir Caminho do Arquivo** contém o caminho onde o pacote é criado. A opção **Incluir Destinos** contém o comando que a implantação executa. 
   * O valor padrão **Build;Package** permite que a implantação compile e crie um pacote de implantação da Web (package.zip).  
     
     Não é necessário um perfil de publicação quando a implantação obtém as informações necessárias nas propriedades para criar o pacote.
7. Vá para WebSiteSQLDatabase.json e adicione um recurso ao modelo.
   
    ![adicionar recurso](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)
8. Dessa vez, selecione **Implantação da Web para aplicativos Web**. 
   
    ![adicionar implantação da Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
9. Implante novamente o projeto do grupo de recursos para o grupo de recursos. Desta vez, há alguns parâmetros novos. Você não precisa fornecer valores para **_artifactsLocation** ou **_artifactsLocationSasToken** porque o Visual Studio gera automaticamente esses valores. No entanto, você deve definir o nome de pasta e do arquivo para o caminho que contém o pacote de implantação (mostrado como **ExampleAppPackageFolder** e **ExampleAppPackageFileName** na imagem a seguir). Forneça os valores que você viu anteriormente nas propriedades de referência (**ExampleApp** e **package.zip**).
   
    ![adicionar implantação da Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
   
    Para a **Conta de armazenamento do artefato**, selecione a conta implantada com esse grupo de recursos.
10. Após a conclusão da implantação, selecione seu aplicativo Web no portal. Clique na URL para navegar até o site.
    
     ![procurar no site](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)
11. Observe que você implantou o aplicativo ASP.NET padrão com êxito.
    
     ![mostrar aplicativo implantado](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como gerenciar seus recursos com o portal, consulte [Usando o Portal do Azure para gerenciar os recursos do Azure](resource-group-portal.md).
* Para saber mais sobre os modelos, confira [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).

