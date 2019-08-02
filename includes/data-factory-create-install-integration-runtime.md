---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: a2858ac73838b50c21a76db5860675171a306192
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172391"
---
## <a name="create-a-self-hosted-integration-runtime"></a>Criar um Integration Runtime auto-hospedado

Nesta seção, você cria um Integration Runtime auto-hospedado e o associa a um computador local com o banco de dados do SQL Server. O tempo de execução de integração auto-hospedado é o componente que copia dados do SQL Server em seu computador para o Armazenamento de Blobs do Azure. 

1. Crie uma variável para o nome do Integration Runtime. Use um nome exclusivo e anote-o. Você o usará posteriormente neste tutorial. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Criar um tempo de execução de integração auto-hospedado. 

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Veja o exemplo de saída:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
  
3. Para recuperar o status do Integration Runtime criado, execute o comando a seguir. Confirme se o valor da propriedade **Estado** está definido como **NeedRegistration**. 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Veja o exemplo de saída:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

4. Para recuperar as chaves de autenticação usadas para registrar o Integration Runtime auto-hospedado com o serviço Azure Data Factory na nuvem, execute o comando a seguir: 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Veja o exemplo de saída:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. Copie uma das chaves (excluindo as aspas) usadas para registrar o Integration Runtime auto-hospedado instalado em seu computador nas próximas etapas.  

## <a name="install-the-integration-runtime"></a>Instalar o Integration Runtime
1. Se você já tiver o Integration Runtime em seu computador, desinstale-o usando **Adicionar ou Remover Programas**. 

2. [Baixe](https://www.microsoft.com/download/details.aspx?id=39717) o tempo de execução de integração auto-hospedado em uma máquina local do Windows. Execute a instalação.

3. Na página **Bem-vindo à instalação do Microsoft Integration Runtime**, selecione **Avançar**.

4. Na página **Contrato de Licença do Usuário Final**, aceite os termos e o contrato de licença e selecione **Avançar**.

5. Na página **Pasta de Destino**, selecione **Avançar**.

6. Na página **Pronto para instalar o Microsoft Integration Runtime**, selecione **Instalar**.

7. Se você vir uma mensagem de aviso sobre a configuração do computador para entrar no modo de suspensão ou hibernação quando não estiver em uso, selecione **OK**.

8. Se você vir a página **Opções de Energia**, feche-a e vá para a página de instalação.

9. Na página **Instalação do Microsoft Integration Runtime concluída**, selecione **Concluir**.

10. Na página **Registrar Integration Runtime (auto-hospedado)** , cole a chave que você salvou na seção anterior e selecione **Registrar**. 

    ![Registrar o Integration Runtime](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

11. Você verá a seguinte mensagem quando o Integration Runtime auto-hospedado for registrado com êxito:

    ![Registrado com êxito](media/data-factory-create-install-integration-runtime/registered-successfully.png)

12. Na página **Novo nó do Integration Runtime (auto-hospedado)** , selecione **Avançar**. 

    ![Nova página do nó do Integration Runtime](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)

13. Na página **Canal de Comunicação da Intranet**, selecione **Ignorar**. Selecione uma certificação TLS/SSL para proteger a comunicação entre nós em um ambiente de Integration Runtime de vários nós. 

    ![Página de canal de comunicação de Intranet](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)

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

    g. Insira a senha para o nome de usuário.

    h. Selecione **Testar** para confirmar que esse tempo de execução de integração pode se conectar ao SQL Server. Você verá uma marca de seleção verde se a conexão tiver êxito. Você verá uma mensagem de erro se a conexão não tiver êxito. Corrija todos os problemas e verifique se o tempo de execução de integração pode se conectar ao SQL Server.    

    > [!NOTE]
    > Anote os valores de tipo de autenticação, servidor, banco de dados, usuário e senha. Você os usará posteriormente neste tutorial. 
    
