---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: c0a0b44b3de088539a070f1182248c4e6db7303f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97510270"
---
## <a name="create-a-self-hosted-integration-runtime"></a>Criar um Integration Runtime auto-hospedado

Nesta seção, você cria um Integration Runtime auto-hospedado e o associa a um computador local com o banco de dados do SQL Server. O runtime de integração auto-hospedada é o componente que copia os dados do SQL Server no computador para o Banco de Dados SQL do Azure. 

1. Crie uma variável para o nome do Integration Runtime. Use um nome exclusivo e anote-o. Você o usará posteriormente neste tutorial. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Criar um runtime de integração auto-hospedada. 

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Veja o exemplo de saída:

   ```console
    Name              : <Integration Runtime name>
    Type              : SelfHosted
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Description       : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DataFactory/factories/<DataFactoryName>/integrationruntimes/ADFTutorialIR
    ```
  
3. Para recuperar o status do Integration Runtime criado, execute o comando a seguir. Confirme se o valor da propriedade **Estado** está definido como **NeedRegistration**. 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Veja o exemplo de saída:

   ```console  
   State                     : NeedRegistration
   Version                   : 
   CreateTime                : 9/24/2019 6:00:00 AM
   AutoUpdate                : On
   ScheduledUpdateDate       : 
   UpdateDelayOffset         : 
   LocalTimeZoneOffset       : 
   InternalChannelEncryption : 
   Capabilities              : {}
   ServiceUrls               : {eu.frontend.clouddatahub.net}
   Nodes                     : {}
   Links                     : {}
   Name                      : ADFTutorialIR
   Type                      : SelfHosted
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Description               : 
   Id                        : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroup name>/providers/Microsoft.DataFactory/factories/<DataFactory name>/integrationruntimes/<Integration Runtime name>
   ```

4. Para recuperar as chaves de autenticação usadas para registrar o Integration Runtime auto-hospedado com o serviço Azure Data Factory na nuvem, execute o comando a seguir: 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Veja o exemplo de saída:

   ```json
   {
    "AuthKey1": "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
    "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. Copie uma das chaves (excluindo as aspas) usadas para registrar o Integration Runtime auto-hospedado instalado em seu computador nas próximas etapas.  

## <a name="install-the-integration-runtime-tool"></a>Instalar a ferramenta Integration Runtime

1. Se você já tiver o Integration Runtime em seu computador, desinstale-o usando **Adicionar ou Remover Programas**. 

2. [Baixe](https://www.microsoft.com/download/details.aspx?id=39717) o runtime de integração auto-hospedada em uma máquina local do Windows. Execute a instalação.

3. Na página **Bem-vindo à instalação do Microsoft Integration Runtime**, selecione **Avançar**.

4. Na página **Contrato de Licença do Usuário Final**, aceite os termos e o contrato de licença e selecione **Avançar**.

5. Na página **Pasta de Destino**, selecione **Avançar**.

6. Na página **Pronto para instalar o Microsoft Integration Runtime**, selecione **Instalar**.

7. Na página **Instalação do Microsoft Integration Runtime concluída**, selecione **Concluir**.

8. Na página **Registrar Integration Runtime (auto-hospedado)** , cole a chave que você salvou na seção anterior e selecione **Registrar**. 

    ![Registrar o Integration Runtime](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

9. Na página **Novo nó do Integration Runtime (auto-hospedado)** , selecione **Concluir**. 

10. Você verá a seguinte mensagem quando o Integration Runtime auto-hospedado for registrado com êxito:

    ![Registrado com êxito](media/data-factory-create-install-integration-runtime/registered-successfully.png)

14. Na página **Registrar Integration Runtime (auto-hospedado)** , selecione **Iniciar o Configuration Manager**.

15. Você verá a página a seguir quando o nó estiver conectado ao serviço de nuvem:

    ![Página O nó está conectado](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. Agora teste a conectividade com o seu banco de dados do SQL Server.

    ![Guia Diagnósticos](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. Na página **Gerenciador de Configurações**, vá para a guia **Diagnóstico**.

    b. Selecione **SqlServer** para o tipo da fonte de dados.

    c. Insira o nome do servidor.

    d. Insira o nome do banco de dados.

    e. Selecione o modo de autenticação.

    f. Insira o nome de usuário.

    g. Insira a senha associada ao nome de usuário.

    h. Selecione **Testar** para confirmar que esse runtime de integração pode se conectar ao SQL Server. Você verá uma marca de seleção verde se a conexão tiver êxito. Você verá uma mensagem de erro se a conexão não tiver êxito. Corrija todos os problemas e verifique se o runtime de integração pode se conectar ao SQL Server.    

    > [!NOTE]
    > Anote os valores de tipo de autenticação, servidor, banco de dados, usuário e senha. Você os usará posteriormente neste tutorial.
