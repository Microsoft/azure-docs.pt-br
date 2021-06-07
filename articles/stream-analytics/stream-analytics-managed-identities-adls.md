---
title: Autenticar Azure Stream Analytics para Azure Data Lake Storage Gen1
description: Este artigo descreve como usar identidades gerenciadas para autenticar seu trabalho do Azure Stream Analytics para a saída do Azure Data Lake Storage Gen1.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: seodec18
ms.openlocfilehash: d5b406d260b1c6518d9227c51d84ab7eb3754329
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598143"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Autenticar Stream Analytics para Azure Data Lake Storage Gen1 usando identidades gerenciadas

O Azure Stream Analytics dá suporte à autenticação de identidade gerenciada com a saída do ADLS (Azure Data Lake Storage) Gen1. A identidade é um aplicativo gerenciado registrado no Azure Active Directory que representa um determinado trabalho do Stream Analytics e pode ser usada para autenticar um recurso almejado. Identidades gerenciadas eliminam as limitações dos métodos de autenticação baseados em usuário, como a necessidade de autenticar novamente devido a alterações de senha ou expirações de token de usuário que ocorrem a cada 90 dias. Além disso, as identidades gerenciadas ajudam com a automação de implantações de trabalho do Stream Analytics que produzem saída para o Azure Data Lake Storage Gen1.

Este artigo mostra três maneiras de habilitar a identidade gerenciada para um trabalho do Azure Stream Analytics que gera saídas para um Azure Data Lake Storage Gen1 por meio do portal do Azure, da implantação de modelo do Azure Resource Manager e das ferramentas do Azure Stream Analytics para Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Portal do Azure

1. Comece criando um novo trabalho do Stream Analytics ou abrindo um trabalho existente no portal do Azure. Na barra de menus localizada no lado esquerdo da tela, selecione **identidade gerenciada** localizada em **Configurar**.

   ![Configurar Stream Analytics identidade gerenciada](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Selecione **usar identidade gerenciada atribuída pelo sistema** na janela que aparece à direita. Clique em **salvar** em uma entidade de serviço para obter a identidade do trabalho de Stream Analytics no Azure Active Directory. O ciclo de vida da identidade que acaba de ser criada será gerenciado pelo Azure. Quando o trabalho do Stream Analytics é excluído, a identidade associada (ou seja, a entidade de serviço) é excluída automaticamente pelo Azure.

   Quando a configuração é salva, a OID (ID de objeto ) da entidade de serviço é listada como a ID da Entidade de Segurança conforme mostrado abaixo:

   ![ID da entidade de serviço do Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   A entidade de serviço tem o mesmo nome que o trabalho do Stream Analytics. Por exemplo, se o nome do seu trabalho for **MyASAJob**, o nome da entidade de serviço criada também será **MyASAJob**.

3. Na janela Propriedades de saída da ADLS Gen1 coletor de saída, clique na lista suspensa modo de autenticação e selecione **identidade gerenciada**.

4. Preencha o restante das propriedades. Para saber mais sobre como criar uma saída do ADLS, veja [Criar uma saída do Data Lake Storage com o Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md). Quando terminar, clique em **Salvar**.

   ![Configurar o Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Navegue para a página Visão Geral do seu ADLS Gen1 e clique em **Data Explorer**.

   ![Configurar a Visão Geral do Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. No painel do Data Explorer, selecione **Acessar** e clique em **Adicionar** no painel Acessar.

   ![Configurar acesso ao Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Na caixa de texto no painel **Selecionar usuário ou grupo**, digite o nome da entidade de serviço. Lembre-se de que o nome da entidade de serviço também é o nome do trabalho do Stream Analytics correspondente. Conforme você começa a digitar o nome da entidade de segurança, ele será exibido abaixo da caixa de texto. Escolha o nome da entidade de serviço desejado e clique em **Selecionar**.

   ![Selecionar um nome da entidade de serviço](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. No painel **Permissões**, verifique as permissões **Gravar** e **Executar** e as atribua a **Esta pasta e todos os filhos**. Em seguida, clique em **OK**.

   ![Selecione as permissões de gravação e execução](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. A entidade de serviço está listada em **Permissões Atribuídas** no painel **Acesso** conforme mostrado abaixo. Agora você pode voltar e iniciar o trabalho do Stream Analytics.

   ![Lista de acesso do Stream Analytics no portal](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Para saber mais sobre permissões de sistema de arquivos do Data Lake Storage Gen1, veja [Controle de acesso no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Ferramentas do Stream Analytics para Visual Studio

1. Em JobConfig.json, defina **Usar Identidade atribuída pelo Sistema** como **Verdadeiro**.

   ![Identidades gerenciadas de configuração de trabalho do Stream Analytics](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. Na janela Propriedades de saída da ADLS Gen1 coletor de saída, clique na lista suspensa modo de autenticação e selecione **identidade gerenciada**.

   ![Identidades gerenciadas de saída do ADLS](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Preencha o restante das propriedades e clique em **Salvar**.

4. Clique em **Enviar para o Azure** no editor de consultas.

   Quando você envia o trabalho, as ferramentas executam duas ações:

   * Criam automaticamente uma entidade de serviço para a identidade do trabalho do Stream Analytics no Azure Active Directory. O ciclo de vida da identidade que acaba de ser criada será gerenciado pelo Azure. Quando o trabalho do Stream Analytics é excluído, a identidade associada (ou seja, a entidade de serviço) é excluída automaticamente pelo Azure.

   * Definem automaticamente as permissões **Escrever** e **Executar** para o caminho de prefixo do ADLS Gen1 usado no trabalho e o atribui a essa pasta e todos os filhos.

5. Você pode gerar os modelos do Resource Manager com a seguinte propriedade usando [Stream Analytics pacote NuGet CI.CD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) versão 1.5.0 ou superior em um computador de compilação (fora do Visual Studio). Siga as etapas de implantação de modelo do Resource Manager na próxima seção para obter a entidade de serviço e permitir acesso a ela por meio do PowerShell.

## <a name="resource-manager-template-deployment"></a>Implantação de modelo do Resource Manager

1. Você pode criar um recurso *Microsoft.StreamAnalytics/streamingjobs* com uma identidade gerenciada incluindo a propriedade a seguir na seção de recursos do seu modelo do Resource Manager:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Essa propriedade instrui o Azure Resource Manager a criar e gerenciar a identidade para seu trabalho do Azure Stream Analytics.

   **Trabalho de exemplo**
   
   ```json
   {
     "Name": "AsaJobWithIdentity",
     "Type": "Microsoft.StreamAnalytics/streamingjobs",
     "Location": "West US",
     "Identity": {
       "Type": "SystemAssigned",
     },
     "properties": {
       "sku": {
         "name": "standard"
       },
       "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": "myDataLakeAccountName",
                 "filePathPrefix": "cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
             }
           }
         }
       }
     }
   }
   ```
  
   **Resposta do trabalho de exemplo**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
     }
   }
   ```

   Tome nota da ID da Entidade de Segurança da resposta de trabalho para permitir acesso ao recurso ADLS exigido.

   A **ID do locatário** é a ID do locatário do Azure Active Directory em que a entidade de serviço é criada. A entidade de serviço é criada no locatário do Azure em que a assinatura confia.

   O **Tipo** indica o tipo de identidade gerenciada, conforme explicado em tipos de identidades gerenciadas. Há suporte apenas para o tipo Atribuído pelo Sistema.

2. Forneça acesso à entidade de serviço usando o PowerShell. Para conceder acesso à entidade de serviço por meio do PowerShell, execute o seguinte comando:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   A **PrincipalId** é a ID de objeto da entidade de serviço e é listada na tela portal depois que a entidade de serviço é criada. Se você tiver criado o trabalho usando uma implantação de modelo do Resource Manager, a ID de Objeto será listada na propriedade Identidade da resposta de trabalho.

   **Exemplo**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Para saber mais sobre o comando do PowerShell acima, consulte a documentação do [set-AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) .

## <a name="remove-managed-identity"></a>Remover identidade gerenciada

A identidade gerenciada criada para um trabalho de Stream Analytics é excluída somente quando o trabalho é excluído. Não é possível excluir a identidade gerenciada sem excluir o trabalho. Se você não quiser mais usar a identidade gerenciada, poderá alterar o método de autenticação para a saída. A identidade gerenciada continuará existindo até que o trabalho seja excluído e será usado se você decidir usar a autenticação de identidade gerenciada novamente.

## <a name="limitations"></a>Limitações
Este recurso não oferece suporte ao seguinte:

1. **Acesso multilocatário**: a entidade de serviço criada para um determinado trabalho de Stream Analytics residirá no locatário de Azure Active Directory no qual o trabalho foi criado e não pode ser usada em um recurso que resida em um locatário de Azure Active Directory diferente. Portanto, você só pode usar o MSI em recursos do ADLS Gen 1 que estão dentro do mesmo locatário Azure Active Directory que o seu trabalho de Azure Stream Analytics. 

2. **[Identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/overview.md)**: não tem suporte. Isso significa que o usuário não é capaz de inserir sua própria entidade de serviço a ser usada por seus Stream Analytics trabalho. A entidade de serviço é gerada pelo Azure Stream Analytics.

## <a name="next-steps"></a>Próximas etapas

* [Criar uma saída do Data Lake Storage com o Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md)
* [Testar as consultas do Stream Analytics localmente com o Microsoft Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testar dados dinâmicos localmente usando as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-live-data-local-testing.md) 
