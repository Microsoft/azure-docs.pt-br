---
title: Usar o Gerenciador de Implantação do Azure com modelos do Resource Manager | Microsoft Docs
description: Use modelos do Resource Manager com o Gerenciador de Implantação do Azure para implantar recursos do Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a42ccb1c0e60f5bf1568ccea13392186577f2875
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205714"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Tutorial: Usar o Gerenciador de Implantação do Azure com modelos do Resource Manager (versão prévia pública)

Saiba como usar [Gerenciador de Implantação do Azure](./deployment-manager-overview.md) para implantar seus aplicativos em várias regiões. Se preferir uma abordagem mais rápida, o [início rápido do Gerenciador de Implantação do Azure](https://github.com/Azure-Samples/adm-quickstart) cria as configurações necessárias em sua assinatura e personaliza os artefatos para implantar um aplicativo em várias regiões. O guia de início rápido executa as mesmas tarefas, como neste tutorial.

Para usar o Gerenciador de Implantação, você precisa criar dois modelos:

* **Um modelo de topologia**: descreve os recursos do Azure que compõem seus aplicativos e em que lugar implantá-lo.
* **Um modelo de distribuição**: descreve as etapas a serem seguidas ao implantar seus aplicativos.

> [!IMPORTANT]
> Caso sua assinatura esteja marcada como Canário para testar novos recursos do Azure, você só poderá usar o Gerenciador de Implantação do Azure para implantação nas regiões do Canário. 

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Compreender o cenário
> * Baixar os arquivos do tutorial
> * Preparar os artefatos
> * Criar a identidade gerenciada definida pelo usuário
> * Criar o modelo de topologia de serviço
> * Criar o modelo de distribuição
> * Implantar os modelos
> * Verificar a implantação
> * Implantar a versão mais recente
> * Limpar recursos

Recursos adicionais:

* A [referência à API REST do Gerenciador de Implantação do Azure](https://docs.microsoft.com/rest/api/deploymentmanager/).
* [Tutorial: Usar a verificação de integridade no Gerenciador de Implantação do Azure](./deployment-manager-tutorial-health-check.md).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* Alguma experiência com o desenvolvimento de [modelos do Azure Resource Manager](./resource-group-overview.md).
* PowerShell do Azure. Para obter mais informações, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Cmdlets do Gerenciador de Implantação. Para instalar esses cmdlets de pré-lançamento, você precisa ter a versão mais recente do PowerShellGet. Para obter a versão mais recente, veja [Instalação do PowerShellGet](/powershell/gallery/installing-psget). Depois de instalar o PowerShellGet, feche a janela do PowerShell. Abra uma nova janela elevada do PowerShell e use o seguinte comando:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

* [Gerenciador do Armazenamento do Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/). O Gerenciador de Armazenamento do Azure não é necessário, mas facilita.

## <a name="understand-the-scenario"></a>Compreender o cenário

O modelo de topologia de serviço descreve os recursos do Azure que compõem seu serviço e em que lugar implantá-lo. A definição de topologia de serviço tem a seguinte hierarquia:

* Topologia de serviço
  * Serviços
    * Unidades de serviço

O diagrama a seguir ilustra a topologia de serviço usada neste tutorial:

![Gerenciador de Implantação do Azure, tutorial do diagrama de cenário](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Há dois serviços alocados nos locais Centro-Oeste dos EUA e Leste dos EUA.  Cada serviço tem duas unidades de serviço: um front-end do aplicativo Web e uma conta de armazenamento para o back-end. As definições de unidade do serviço contêm links para os arquivos de modelo e parâmetro para a criação de aplicativos Web e contas de armazenamento.

## <a name="download-the-tutorial-files"></a>Baixar os arquivos do tutorial

1. Baixe [os modelos e os artefatos](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) usados por este tutorial.
2. Descompacte os arquivos em seu computador local.

Na pasta raiz, há duas pastas:

* **ADMTemplates**: contém os modelos do Gerenciador de Implantação, incluindo:
  * CreateADMServiceTopology.json
  * CreateADMServiceTopology.Parameters.json
  * CreateADMRollout.json
  * CreateADMRollout.Parameters.json
* **ArtifactStore**: contém os artefatos de modelo e os artefatos binários. Ver [Preparar os artefatos](#prepare-the-artifacts).

Note que há dois conjuntos de modelos.  Um conjunto é composto pelos modelos do Gerenciador de Implantação usados para implantar a topologia de serviço e a distribuição; o outro conjunto é chamado das unidades de serviço para criar serviços Web e contas de armazenamento.

## <a name="prepare-the-artifacts"></a>Preparar os artefatos

A pasta ArtifactStore do download contém duas pastas:

![Tutorial do Gerenciador de Implantação do Azure, diagrama de origem do artefato](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* A pasta **modelos**: contém os artefatos de modelo. **1.0.0.0** e **1.0.0.1** representam as duas versões dos artefatos binários. Dentro de cada versão, há uma pasta para cada serviço (serviço Leste dos EUA e Serviço do Oeste dos EUA). Cada serviço tem um par de arquivos de modelo e de parâmetro para a criação de uma conta de armazenamento e outro par para a criação de um aplicativo Web. O modelo de aplicativo Web chama um pacote compactado, que contém os arquivos do aplicativo Web. O arquivo compactado é um artefato binário armazenado na pasta de binários.
* A pasta de **binários**: contém os artefatos binários. **1.0.0.0** e **1.0.0.1** representam as duas versões dos artefatos binários. Dentro de cada versão, há um arquivo zip para criar o aplicativo Web no local Oeste dos EUA e outro arquivo zip para criar o aplicativo Web no local Leste dos EUA.

As duas versões (1.0.0.0 e 1.0.0.1) são para [implantação de revisão](#deploy-the-revision). Mesmo que os artefatos de modelo e os artefatos binários tenham duas versões, somente os artefatos binários serão diferentes entre as duas versões. Na prática, os artefatos binários são atualizados comparando com mais frequência com artefatos de modelo.

1. Abra **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json** em um editor de texto. É um modelo básico para criar uma conta de armazenamento.
2. Abra **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json**.

    ![Gerenciador de Implantação do Azure, tutorial para criar modelo de aplicativo Web](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    O modelo chama um pacote de implantação, que contém os arquivos do aplicativo Web. Neste tutorial, o pacote compactado contém apenas um arquivo index.html.
3. Abra **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json**.

    ![Gerenciador de Implantação do Azure, tutorial para criar containerRoot de parâmetros de modelo de aplicativo Web](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    O valor de deployPackageUri é o caminho para o pacote de implantação. O parâmetro contém uma variável **$containerRoot**. O valor $containerRoot é fornecido no [modelo de distribuição](#create-the-rollout-template) concatenando o local de SAS de origem do artefato, a raiz de artefato e o deployPackageUri.
4. Abra **\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html**.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    O html mostra as informações de local e versão. O arquivo binário na pasta 1.0.0.1 mostra "Versão 1.0.0.1". Depois de implantar o serviço, você poderá procurar essas páginas.
5. Confira outros arquivos de artefato. Ajuda você a entender melhor o cenário.

Artefatos de modelo são usados pelo modelo de topologia de serviço e artefatos binários são usados pelo modelo de distribuição. O modelo de topologia e o modelo de distribuição definem uma recurso do Azure de fonte do artefato, que é um recurso usado para apontar o Resource Manager para os artefatos de modelo e binários usados na implantação. Para simplificar o tutorial, uma conta de armazenamento é usada para armazenar os artefatos de modelo e os artefatos binários. Ambas as fontes de artefato apontam para a mesma conta de armazenamento.

1. Crie uma conta de armazenamento do Azure. Para obter as instruções, confira [Início Rápido: carregar, baixar e listar blobs usando o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).
2. Crie um contêiner de blobs na conta de armazenamento.
3. Copie as duas pastas (binários e modelos) e o conteúdo das duas pastas para o contêiner de blobs. O [Gerenciador de Armazenamento do Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) é compatível com o recurso do tipo "arrastar e soltar".
4. Obtenha o local de SAS do contêiner usando as instruções a seguir:

    1. No Gerenciador de Armazenamento do Azure, navegue até o contêiner de blobs.
    2. Clique com o botão direito do mouse no contêiner de blobs no painel esquerdo e selecione **Obter Assinatura de Acesso Compartilhado**.
    3. Configure a **Hora de início** e o **Tempo de expiração**.
    4. Selecione **Criar**.
    5. Faça uma cópia da URL. Essa URL é necessária para preencher um campo em dois arquivos de parâmetro, [arquivo de parâmetros da topologia](#topology-parameters-file) e [arquivo de parâmetros de distribuição](#rollout-parameters-file).

## <a name="create-the-user-assigned-managed-identity"></a>Criar identidade gerenciada atribuída pelo usuário

Posteriormente no tutorial, você implantará uma distribuição. Uma identidade gerenciada atribuída pelo usuário é necessária para executar as ações de implantação (por exemplo, implantar os aplicativos Web e a conta de armazenamento). Essa identidade deve receber acesso à assinatura do Azure para a qual você está implantando o serviço e ter permissão suficiente para concluir a implantação de artefato.

Você precisa criar uma identidade gerenciada atribuída pelo usuário e configurar o controle de acesso para sua assinatura.

> [!IMPORTANT]
> A identidade gerenciada atribuída pelo usuário deve estar no mesmo local que a [distribuição](#create-the-rollout-template). Atualmente, os recursos do Gerenciador de Implantação, incluindo a distribuição, só podem ser criados no Centro dos EUA ou no Leste dos EUA 2. No entanto, isso vale apenas para os recursos do Gerenciador de Implantação (como a topologia de serviço, serviços, unidades de serviço, distribuição e etapas). Seus recursos de destino podem ser implantados em qualquer região do Azure com suporte. Neste tutorial, por exemplo, os recursos do Gerenciador de Implantação são implantados no Centro dos EUA, mas os serviços são implantados no Leste dos EUA e no Oeste dos EUA. Essa restrição será eliminada futuramente.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Crie uma [identidade gerenciada atribuída ao usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
3. No portal, selecione **Assinaturas** no menu à esquerda e, em seguida, selecione sua assinatura.
4. Selecione **Controle de Acesso (IAM)** e, em seguida, selecione **Adicionar atribuição de função**.
5. Digite ou selecione os valores a seguir:

    ![Gerenciador de Implantação do Azure, tutorial de controle de acesso de identidade gerenciada atribuída pelo usuário](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Função**: conceda permissão suficiente para concluir a implantação de artefato (os aplicativos Web e as contas de armazenamento). Selecione **Colaborador** neste tutorial. Na prática, você deseja restringir as permissões ao mínimo.
    * **Acesso atribuído a**: selecione **Identidade Gerenciada Atribuída ao Usuário**.
    * Selecione a identidade gerenciada atribuída pelo usuário criada anteriormente no tutorial.
6. Clique em **Salvar**.

## <a name="create-the-service-topology-template"></a>Criar o modelo de topologia de serviço

Abra **\ADMTemplates\CreateADMServiceTopology.json**.

### <a name="the-parameters"></a>Os parâmetros

O modelo contém os seguintes parâmetros:

![Gerenciador de Implantação do Azure, tutorial de parâmetros do modelo de topologia](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-parameters.png)

* **namePrefix**: Esse prefixo é usado para criar os nomes para os recursos do Gerenciador de Implantação. Por exemplo, o nome da topologia de serviço usando o prefixo "jdoe" é **jdoe**ServiceTopology.  Os nomes de recursos são definidos na seção de variáveis desse modelo.
* **azureResourcelocation**: Para simplificar o tutorial, todos os recursos compartilham essa localização, a menos que seja especificado o contrário. Atualmente, os recursos de Gerenciador de Implantação do Azure só podem ser criados em **EUA Central** ou **Leste dos EUA 2**.
* **artifactSourceSASLocation**: O URI de SAS para o contêiner de Blob em que os arquivos de parâmetros e de modelo de unidade de serviço são armazenados para implantação.  Ver [Preparar os artefatos](#prepare-the-artifacts).
* **templateArtifactRoot**: O caminho de deslocamento do contêiner de Blob em que os modelos e os parâmetros são armazenados. O valor padrão é **templates/1.0.0.0**. Não altere esse valor, a menos que deseje alterar a estrutura de pastas explicada em [Preparar os artefatos](#prepare-the-artifacts). Caminhos relativos são usados neste tutorial.  O caminho completo é construído por meio da concatenação de **artifactSourceSASLocation**, **templateArtifactRoot** e **templateArtifactSourceRelativePath** (ou **parametersArtifactSourceRelativePath**).
* **targetSubscriptionID**: A ID da assinatura para a qual os recursos do Gerenciador de Implantação serão implantados e cobrados. Use sua ID da assinatura neste tutorial.

### <a name="the-variables"></a>As variáveis

A seção de variáveis define os nomes dos recursos, as localizações do Azure para os dois serviços: **Serviço WUS** e **Serviço EUS** e os caminhos de artefato:

![Gerenciador de Implantação do Azure, tutorial de variáveis do modelo de topologia](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Compare os caminhos de artefato com a estrutura de pastas que você carregou para a conta de armazenamento. Observe que os caminhos de artefato são caminhos relativos. O caminho completo é construído por meio da concatenação de **artifactSourceSASLocation**, **templateArtifactRoot** e **templateArtifactSourceRelativePath** (ou **parametersArtifactSourceRelativePath**).

### <a name="the-resources"></a>Os recursos

Há dois recursos definidos no nível raiz: *uma origem do artefato* e *uma topologia de serviço*.

A definição de fonte do artefato é:

![Gerenciador de Implantação do Azure, tutorial de fonte de artefato de recursos do modelo de topologia](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

A captura de tela a seguir mostra apenas algumas partes da topologia de serviço, serviços e as definições de unidades do serviço:

![Gerenciador de Implantação do Azure, tutorial de topologia de serviço de recursos do modelo de topologia](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* **artifactSourceId** é usado para associar o recurso de origem do artefato ao recurso de topologia de serviço.
* **dependsOn**: Todos os recursos da topologia de serviço dependem do recurso de origem do artefato.
* **artefatos** apontam para os artefatos de modelo.  Caminhos relativos são usados aqui. O caminho completo é construído concatenando artifactSourceSASLocation (definido na fonte de artefato), artifactRoot (definido na fonte de artefato) e templateArtifactSourceRelativePath (ou parametersArtifactSourceRelativePath).

### <a name="topology-parameters-file"></a>Arquivo de parâmetros de topologia

Você cria um arquivo de parâmetros usado com o modelo de topologia.

1. Abra **\ADMTemplates\CreateADMServiceTopology.Parameters** no Visual Studio Code ou qualquer editor de texto.
2. Preencha os valores de parâmetro:

    * **namePrefix**: Insira uma cadeia de caracteres com 4 a 5 caracteres. Esse prefixo é usado para criar nomes exclusivos de recursos do Azure.
    * **azureResourceLocation**: Caso não esteja familiarizado com as localizações do Azure, use **centralus** neste tutorial.
    * **artifactSourceSASLocation**: Insira o URI de SAS do diretório raiz (o contêiner de Blob) em que os arquivos de parâmetros e de modelo de unidade de serviço são armazenados para implantação.  Ver [Preparar os artefatos](#prepare-the-artifacts).
    * **templateArtifactRoot**: A menos que você altere a estrutura de pasta dos artefatos, use **templates/1.0.0.0** neste tutorial.
    * **targetScriptionID**: Insira sua ID da assinatura do Azure.

> [!IMPORTANT]
> O modelo de topologia e o modelo de distribuição compartilham alguns parâmetros em comum. Esses parâmetros devem ter os mesmos valores. Esses parâmetros são: **namePrefix**, **azureResourceLocation** e **artifactSourceSASLocation** (ambas as fontes de artefato compartilham a mesma conta de armazenamento neste tutorial).

## <a name="create-the-rollout-template"></a>Criar o modelo de distribuição

Abra **\ADMTemplates\CreateADMRollout.json**.

### <a name="the-parameters"></a>Os parâmetros

O modelo contém os seguintes parâmetros:

![Gerenciador de Implantação do Azure, tutorial de parâmetros do modelo de distribuição](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* **namePrefix**: Esse prefixo é usado para criar os nomes para os recursos do Gerenciador de Implantação. Por exemplo, o nome de distribuição usando o prefixo "jdoe" é **jdoe**Rollout.  Os nomes são definidos na seção de variáveis do modelo.
* **azureResourcelocation**: Para simplificar o tutorial, todos os recursos do Gerenciador de Implantação compartilham essa localização, a menos que seja especificado o contrário. Atualmente, os recursos de Gerenciador de Implantação do Azure só podem ser criados em **EUA Central** ou **Leste dos EUA 2**.
* **artifactSourceSASLocation**: O URI de SAS do diretório raiz (o contêiner de Blob) em que os arquivos de parâmetros e de modelo de unidade de serviço são armazenados para implantação.  Ver [Preparar os artefatos](#prepare-the-artifacts).
* **binaryArtifactRoot**:  O valor padrão é **binaries/1.0.0.0**. Não altere esse valor, a menos que deseje alterar a estrutura de pastas explicada em [Preparar os artefatos](#prepare-the-artifacts). Caminhos relativos são usados neste tutorial.  O caminho completo é construído concatenando **artifactSourceSASLocation**, **binaryArtifactRoot** e o **deployPackageUri** especificado no CreateWebApplicationParameters.json.  Ver [Preparar os artefatos](#prepare-the-artifacts).
* **managedIdentityID**: A identidade gerenciada atribuída pelo usuário que executa as ações de implantação. Veja [Criar identidade gerenciada atribuída pelo usuário](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>As variáveis

A seção de variáveis define os nomes dos recursos. Verifique se o nome da topologia de serviço, os nomes de serviço e os nomes de unidade do serviço correspondem aos nomes definidos no [modelo de topologia](#create-the-service-topology-template).

![Gerenciador de Implantação do Azure, tutorial de variáveis de modelo de distribuição](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Os recursos

Há três recursos definidos no nível raiz: uma fonte de artefato, uma etapa e uma distribuição.

A definição de fonte do artefato é idêntica àquela definida no modelo de topologia.  Veja [Criar o modelo de topologia de serviço](#create-the-service-topology-template) para obter mais informações.

A captura de tela a seguir mostra a definição de etapa de espera:

![Gerenciador de Implantação do Azure, tutorial de etapa de espera dos recursos do modelo de distribuição](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

A duração é usando o [padrão ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (letras maiúsculas são necessárias) é um exemplo de uma espera de 1 minuto.

Captura de tela a seguir mostra apenas algumas partes da definição de distribuição:

![Gerenciador de Implantação do Azure, tutorial de distribuição de recursos do modelo de distribuição](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* **dependsOn**: O recurso de distribuição depende do recurso de origem do artefato e de uma das etapas definidas.
* **artifactSourceId**: usado para associar o recurso de origem do artefato ao recurso de distribuição.
* **targetServiceTopologyId**: usado para associar o recurso de topologia de serviço ao recurso de distribuição.
* **deploymentTargetId**: É a ID do recurso da unidade de serviço do recurso de topologia de serviço.
* **preDeploymentSteps** e **postDeploymentSteps**: contém as etapas de distribuição. No modelo, uma etapa de espera é chamada.
* **dependsOnStepGroups**: configurar as dependências entre os grupos de etapa.

### <a name="rollout-parameters-file"></a>Arquivo de parâmetros de distribuição

Você cria um arquivo de parâmetros usado com o modelo de distribuição.

1. Abra **\ADMTemplates\CreateADMRollout.Parameters** no Visual Studio Code ou qualquer editor de texto.
2. Preencha os valores de parâmetro:

    * **namePrefix**: Insira uma cadeia de caracteres com 4 a 5 caracteres. Esse prefixo é usado para criar nomes exclusivos de recursos do Azure.
    * **azureResourceLocation**: Atualmente, os recursos de Gerenciador de Implantação do Azure só podem ser criados em **EUA Central** ou **Leste dos EUA 2**.
    * **artifactSourceSASLocation**: Insira o URI de SAS do diretório raiz (o contêiner de Blob) em que os arquivos de parâmetros e de modelo de unidade de serviço são armazenados para implantação.  Ver [Preparar os artefatos](#prepare-the-artifacts).
    * **binaryArtifactRoot**: A menos que você altere a estrutura de pasta dos artefatos, use **binaries/1.0.0.0** neste tutorial.
    * **managedIdentityID**: Insira a identidade gerenciada atribuída pelo usuário. Veja [Criar identidade gerenciada atribuída pelo usuário](#create-the-user-assigned-managed-identity). A sintaxe do é:

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> O modelo de topologia e o modelo de distribuição compartilham alguns parâmetros em comum. Esses parâmetros devem ter os mesmos valores. Esses parâmetros são: **namePrefix**, **azureResourceLocation** e **artifactSourceSASLocation** (ambas as fontes de artefato compartilham a mesma conta de armazenamento neste tutorial).

## <a name="deploy-the-templates"></a>Implantar os modelos

O Azure PowerShell pode ser usado para implantar os modelos.

1. Execute o script para implantar a topologia de serviço.

    ```azurepowershell
    $resourceGroupName = "<Enter a Resource Group Name>"
    $location = "Central US"
    $filePath = "<Enter the File Path to the Downloaded Tutorial Files>"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location "$location"

    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    > [!NOTE]
    > `New-AzResourceGroupDeployment` é uma chamada assíncrona. A mensagem de êxito apenas significa que a implantação foi iniciada com êxito. Para verificar a implantação, confira as etapas 2 e 4 deste procedimento.

2. Verifique se a topologia de serviço e os recursos de sublinhado foram criados com êxito usando o portal do Azure:

    ![Gerenciador de Implantação do Azure, tutorial para recursos de topologia de serviço implantados](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    **Mostrar tipos ocultos** deve ser selecionado para ver os recursos.

3. <a id="deploy-the-rollout-template"></a>Implante o modelo de distribuição:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. Verifique o progresso de distribuição usando o seguinte script do PowerShell:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "<Enter the Rollout Name>" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Os cmdlets do PowerShell do Gerenciador de Implantação devem ser instalados antes de executar este cmdlet. Confira Pré-requisitos. O comutador detalhado pode ser usado para ver toda a saída.

    O exemplo a seguir mostra o status em execução:

    ```
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    Depois que a distribuição tiver sido implantada com êxito, você deverá ver mais dois grupos de recursos criados, um para cada serviço.

## <a name="verify-the-deployment"></a>Verificar a implantação

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Navegue até os aplicativos Web criados recentemente nos novos grupos de recursos criados pela implantação de distribuição.
3. Abra o aplicativo Web em um navegador da Web. Verifique o local e a versão no arquivo index.html.

## <a name="deploy-the-revision"></a>Implantar a revisão

Quando você tem uma nova versão (1.0.0.1) para o aplicativo Web. Você pode usar o procedimento a seguir para reimplantar o aplicativo Web.

1. Abra CreateADMRollout.Parameters.json.
2. Atualize **binaryArtifactRoot** para **binaries/1.0.0.1**.
3. Reimplante a distribuição conforme as instruções em [Implantar os modelos](#deploy-the-rollout-template).
4. Verifique a implantação conforme as instruções em [Verificar a implantação](#verify-the-deployment). A página da Web deverá mostrar a versão 1.0.0.1.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. Use o campo **Filtrar por nome** para restringir os grupos de recursos criados neste tutorial. Deve haver 3 a 4:

    * **&lt;namePrefix>rg**: contém os recursos do Gerenciador de Implantação.
    * **&lt;namePrefix>ServiceWUSrg**: contém os recursos definidos pelo ServiceWUS.
    * **&lt;namePrefix>ServiceEUSrg**: contém os recursos definidos pelo ServiceEUS.
    * O grupo de recursos para a identidade gerenciada definida pelo usuário.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.
5. Repita as duas últimas etapas para excluir outros grupos de recursos criados neste tutorial.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o Gerenciador de Implantação do Azure. Para integrar o monitoramento de integridade ao Gerenciador de Implantação do Azure, confira [Tutorial: Usar a verificação de integridade no Gerenciador de Implantação do Azure](./deployment-manager-tutorial-health-check.md).
