---
title: Como usar as políticas de alocação personalizadas com o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure | Microsoft Docs
description: Como usar as políticas de alocação personalizadas com o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 1e672e7bd43dcd05d048d22205939749c1d96579
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348067"
---
# <a name="how-to-use-custom-allocation-policies"></a>Como usar políticas de alocação personalizadas


Uma política de alocação personalizada proporciona a você mais controle sobre como os dispositivos são atribuídos a um Hub IoT. Isso é feito usando código personalizado em uma [função do Azure](../azure-functions/functions-overview.md) para atribuir dispositivos a um Hub IoT. O serviço de provisionamento de dispositivos chama o código de função do Azure, fornecendo todas as informações relevantes sobre o dispositivo e o registro. O código de função é executado e retorna as informações do Hub IoT para provisionar o dispositivo.

Por meio de políticas de alocação personalizadas, você define suas próprias políticas de alocação quando as políticas fornecidas pelo Serviço de Provisionamento de Dispositivos não atendem aos requisitos do seu cenário.

Por exemplo, talvez você queira examinar o certificado que um dispositivo está usando durante o provisionamento e atribuir o dispositivo a um Hub IoT com base em uma propriedade de certificado. Talvez você tenha informações armazenadas em um banco de dados para seus dispositivos e precise consultar o banco de dados para determinar a qual Hub IoT um dispositivo deve ser atribuído.


Este artigo demonstra uma política de alocação personalizada usando uma função do Azure escrita em C#. Dois novos Hubs IoT são criados, representando uma *Divisão de Resistências da Contoso* e uma *Divisão de Bombas Térmicas da Contoso*. Dispositivos solicitando provisionamento devem ter uma ID de registro com um dos sufixos a seguir para serem aceitos para o provisionamento:

- **-contoso-tstrsd-007**: Divisão de Resistências da Contoso
- **-contoso-hpsd-088**: Divisão de Bombas Térmicas da Contoso

Os dispositivos serão provisionados com base em um destes sufixos necessários na ID do registro. Esses dispositivos serão simulados usando uma amostra de provisionamento incluída no [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c). 

Neste artigo, você realizará as seguintes etapas:

* Usar a CLI do Azure para criar dois Hubs IoT da divisão da Contoso (**Divisão de Resistências da Contoso** e **Divisão de Bombas Térmicas da Contoso**)
* Criar um registro de grupo usando uma função do Azure para a política de alocação personalizada
* Crie chaves de dispositivo para duas simulações do dispositivo.
* Preparar o ambiente de desenvolvimento para o SDK de C do IoT do Azure
* Simular os dispositivos para assegurar que eles sejam provisionados de acordo com o código de exemplo da política de alocação personalizada


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do guia de início rápido [Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md).
* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 ou posterior com a carga de trabalho ["Desenvolvimento para desktop com C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) habilitada.
* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>Criar dois Hubs IoT divisionários

Nesta seção, você usará o Azure Cloud Shell para criar dois novos Hubs IoT representando a **Divisão de Resistências da Contoso** e **Divisão de Bombas Térmicas da Contoso**.

1. Use o Azure Cloud Shell para criar um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

    O exemplo a seguir cria um grupo de recursos chamado *contoso-us-resource-group* na região *eastus*. É recomendável que você use esse grupo para todos os recursos criados neste artigo. Essa abordagem facilitará a limpeza após você terminar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Use o Azure Cloud Shell para criar o Hub IoT **Divisão de Resistências da Contoso** com o comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). O Hub IoT será adicionado ao *contoso-us-resource-group*.

    O exemplo a seguir cria um Hub IoT chamado *contoso-toasters-hub-1098* no local *eastus*. Você deve usar seu próprio nome de hub exclusivo. Crie seu próprio sufixo no nome do hub no lugar de **1098**. O código de exemplo para a política de alocação personalizada requer `-toasters-` no nome do hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Esse comando pode demorar um pouco para ser concluído.

3. Use o Azure Cloud Shell para criar o Hub IoT **Divisão de Bombas Térmicas da Contoso** com o comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). Este Hub IoT também será adicionado ao *contoso-us-resource-group*.

    O exemplo a seguir cria um Hub IoT chamado *contoso-heatpumps-hub-1098* no local *eastus*. Você deve usar seu próprio nome de hub exclusivo. Crie seu próprio sufixo no nome do hub no lugar de **1098**. O código de exemplo para a política de alocação personalizada requer `-heatpumps-` no nome do hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Esse comando também pode demorar um pouco para ser concluído.




## <a name="create-the-enrollment"></a>Criar o registro

Nesta seção, você criará um novo grupo de registro que usará a política de alocação personalizada. Para simplificar, este artigo usa o [Atestado de chave simétrica](concepts-symmetric-key-attestation.md) com o registro. Para uma solução mais segura, considere usar o [Atestado de certificado X.509](concepts-security.md#x509-certificates) com uma cadeia de confiança.

1. Entre no [portal do Azure](https://portal.azure.com) e abra a instância do Serviço de Provisionamento de Dispositivos.

2. Selecione a guia **Gerenciar registros** e clique no botão **Adicionar grupo de registros** na parte superior da página. 

3. Em **Adicionar Grupo de Registros**, insira as informações a seguir e clique no botão **Salvar**.

    **Nome do grupo**: Insira **contoso-custom-allocated-devices**.

    **Tipo de Atestado**: selecione **Chave Simétrica**.

    **Gerar Chaves Automaticamente**: esta caixa de seleção já deve estar marcada.

    **Selecione como deseja atribuir dispositivos aos hubs**: Selecione **Personalizado (Use o Azure Functions)** .

    ![Adicionar grupo de registros de alocação personalizado para atestado de chave simétrica](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)


4. Em **Adicionar grupo de registros**, clique em **Vincular um novo Hub IoT** para vincular ambos os seus novos Hubs IoT divisionários. 

    Você precisa executar esta etapa para ambos os Hubs IoT divisionários.

    **Assinatura**: Se você tem várias assinaturas, escolha a assinatura em que você criou os Hubs IoT divisionários.

    **Hub IoT**: Selecione um dos hubs divisionários que você criou.

    **Política de Acesso**: Escolha **iothubowner**.

    ![Vincular os Hubs IoT divisionários ao serviço de provisionamento](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)


5. Em **Adicionar grupo de registros**, depois que ambos os Hubs IoT divisionários foram vinculados, você deve selecioná-los como o grupo de Hubs IoT para o grupo de registro, conforme mostrado abaixo:

    ![Criar o grupo de hubs divisionários para o registro](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)


6. Em **Adicionar grupo de registros**, role para baixo até a seção **Selecionar função do Azure** e clique em **Criar um novo aplicativo de funções**.

7. Na página de criação de **Aplicativo de funções** que é aberta, insira as seguintes configurações para sua nova função e clique em **Criar**:

    **Nome do aplicativo**: Insira um nome de aplicativo de funções exclusivo. **contoso-function-app-1098** é mostrado como um exemplo.

    **Grupo de recursos**: Selecione **Usar existente** e **contoso-us-resource-group** para manter juntos todos os recursos criados neste artigo.

    **Application Insights**: Para este exercício, você pode desativar esse recurso.

    ![Crie o aplicativo de funções](./media/how-to-use-custom-allocation-policies/function-app-create.png)


8. Novamente na sua página **Adicionar grupo de registros**, verifique se seu novo aplicativo de funções está selecionado. Talvez você precise selecionar novamente a assinatura para atualizar a lista de aplicativos de funções.

    Depois que seu novo aplicativo de funções for selecionado, clique em **Criar uma nova função**.

    ![Crie o aplicativo de funções](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    Seu novo aplicativo de funções será aberto.

9. No aplicativo de funções, clique para criar uma função

    ![Crie o aplicativo de funções](./media/how-to-use-custom-allocation-policies/new-function.png)

    Para a nova função, use as configurações padrão para criar um **Webhook + API** usando a linguagem de programação **CSharp**. Clique em **Criar esta função**.

    Isso cria uma nova função C# denominada **HttpTriggerCSharp1**.

10. Substitua o código da nova função C# pelo código a seguir e clique em **Salvar**:    

    ```csharp
    #r "Newtonsoft.Json"
    using System.Net;
    using System.Text;
    using Newtonsoft.Json;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // Just some diagnostic logging
        log.Info("C# HTTP trigger function processed a request.");
        log.Info("Request.Content:...");    
        log.Info(req.Content.ReadAsStringAsync().Result);

        // Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.Info("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.Info("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.Info("Unknown device registration");
                }
            }
        }

        return (fail)
            ? req.CreateResponse(HttpStatusCode.BadRequest, message)
            : new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StringContent(JsonConvert.SerializeObject(obj, Formatting.Indented), Encoding.UTF8, "application/json")
            };
    }    

    public class DeviceTwinObj
    {
        public string deviceId {get; set;}
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public string IoTHub {get; set;}
        public DeviceTwinObj initialTwin {get; set;}
        public string[] linkedHubs {get; set;}
        public string enrollment {get; set;}
    }
    ```


11. Volte para a página **Adicionar grupo de registros** e verifique se a nova função está selecionada. Talvez você precise selecionar novamente o aplicativo de funções para atualizar a lista de funções.

    Depois que sua nova função estiver selecionada, clique em **Salvar** para salvar o grupo de registros.

    ![Por fim, salve o grupo de registros](./media/how-to-use-custom-allocation-policies/save-enrollment.png)


12. Após salvar o registro, abra-o novamente e anote a **Chave primária**. Para que as chaves sejam geradas, é preciso primeiro salvar o registro. Essa chave será usada mais tarde para gerar chaves de dispositivo exclusivas para dispositivos simulados.


## <a name="derive-unique-device-keys"></a>Derivar chaves de dispositivo exclusivas

Nesta seção, você criará duas chaves de dispositivo exclusivas. Uma chave será usada para um dispositivo de resistência simulado. A outra chave será usada para um dispositivo de bomba térmica simulado.

Para gerar a chave do dispositivo, use a **Chave primária** que você anotou anteriormente para computar o [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) da ID de registro de dispositivo para cada um dos dispositivos, depois converta o resultado no formato Base64. Para obter mais informações sobre como criar chaves de dispositivo derivadas com grupos de registro, veja a seção de registros de grupo de [Atestado de chave simétrica](concepts-symmetric-key-attestation.md).

Para o exemplo neste artigo, use as duas IDs de registro de dispositivo a seguir e compute uma chave de dispositivo para ambos os dispositivos. As duas IDs de registro têm um sufixo válido para trabalhar com o código de exemplo para a política de alocação personalizada:

- **breakroom499-contoso-tstrsd-007**
- **mainbuilding167-contoso-hpsd-088**

#### <a name="linux-workstations"></a>Estações de trabalho do Linux

Se estiver usando uma estação de trabalho do Linux, você poderá usar openssl para gerar suas chaves de dispositivo derivadas, conforme mostrado no exemplo a seguir.

1. Substitua o valor de **CHAVE** pela **Chave primária** que você anotou anteriormente.

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


#### <a name="windows-based-workstations"></a>Estações de trabalho baseadas em Windows

Se estiver usando uma estação de trabalho baseada no Windows, você poderá usar o PowerShell para gerar sua chave de dispositivo derivada, conforme mostrado no exemplo a seguir.

1. Substitua o valor de **CHAVE** pela **Chave primária** que você anotou anteriormente.

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


Os dispositivos simulados usarão as chaves do dispositivo derivadas com cada ID de registro para realizar o atestado de chave simétrica.




## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Preparar um ambiente de desenvolvimento do SDK de C do IoT do Azure

Nesta seção, você preparará um ambiente de desenvolvimento usado para criar o [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c). O SDK inclui o código de exemplo para o dispositivo simulado. Este dispositivo simulado tentará realizar provisionamento durante a sequência de inicialização do dispositivo.

Esta seção é voltada para uma estação de trabalho baseada em Windows. Para obter um exemplo de Linux, consulte a configuração das VMs em [Como provisionar para multilocação](how-to-provision-multitenant.md).

1. Baixe o [sistema de build CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho de "Desenvolvimento para Desktop com C++") estejam instalados em seu computador, **antes** da instalação de `CMake`. Após a instalação dos pré-requisitos e verificação do download, instale o sistema de compilação CMake.

2. Abra um prompt de comando ou o shell Bash do Git. Execute o seguinte comando para clonar o repositório GitHub do SDK de C do IoT do Azure:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Essa operação deve demorar alguns minutos.


3. Crie um subdiretório `cmake` no diretório raiz do repositório git e navegue até essa pasta. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Execute o comando a seguir, que cria uma versão do SDK específica para a plataforma cliente de desenvolvimento. Uma solução do Visual Studio para o dispositivo simulado será gerada no diretório `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Se `cmake` não encontrar o compilador do C++, você poderá obter erros de build ao executar o comando acima. Se isso acontecer, tente executar esse comando no [prompt de comando do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Após o sucesso da compilação, as últimas linhas de saída serão semelhantes à seguinte saída:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```




## <a name="simulate-the-devices"></a>Simular os dispositivos

Nesta seção, você atualizará um exemplo de provisionamento denominado **prov\_dev\_client\_sample** localizado no SDK de C do IoT do Azure configurado anteriormente. 

Esse código de exemplo simula uma sequência de inicialização do dispositivo que envia a solicitação de provisionamento à sua instância do Serviço de Provisionamento de Dispositivos. A sequência de inicialização fará com que o dispositivo de resistência seja reconhecido e atribuído ao Hub IoT usando a política de alocação personalizada.

1. No portal do Azure, selecione a guia **Visão Geral** de seu serviço de Provisionamento de Dispositivos e anote o valor de **_Escopo da ID_** .

    ![Extrair informações do ponto de extremidade do Serviço de Provisionamento de Dispositivo na folha do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. No Visual Studio, abra o arquivo de solução **azure_iot_sdks.sln**, que foi gerado pela execução de CMake anteriormente. O arquivo da solução deve estar no seguinte local:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Na janela *Gerenciador de Soluções* do Visual Studio, navegue até a pasta **Provisionar\_Exemplos**. Expanda o projeto de exemplo chamado **prov\_dev\_client\_sample**. Expanda **Arquivos de Origem** e abra **prov\_dev\_client\_sample.c**.

4. Localize a constante `id_scope` e substitua o valor pelo seu valor de **Escopo de ID** copiado anteriormente. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Encontre a definição da função `main()` no mesmo arquivo. Certifique-se de que a variável `hsm_type` está configurada para `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, conforme mostrado abaixo:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Clique com botão direito do mouse no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Inicialização**. 


#### <a name="simulate-the-contoso-toaster-device"></a>Simular o dispositivo de resistência da Contoso

1. Para simular o dispositivo de resistência, localize a chamada para `prov_dev_set_symmetric_key_info()` em **prov\_dev\_client\_sample.c** que é comentado.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Remova a marca de comentário da chamada de função e substitua os valores de espaço reservado (incluindo os colchetes angulares) pela ID de registro da resistência e a chave de dispositivo derivada gerada anteriormente. O valor da chave **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** mostrado abaixo é fornecido apenas como um exemplo.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```
   
    Salve o arquivo.

2. No menu do Visual Studio, selecione **Depurar** > **Iniciar sem depuração** para executar a solução. No prompt para recompilar o projeto, clique em **Sim** para recompilar o projeto antes da execução.

    A saída a seguir é um exemplo do dispositivo de resistência simulado inicializando com êxito e, em seguida, conectando-se à instância do serviço de provisionamento a ser atribuído ao Hub IoT de resistências pela política de alocação personalizada:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```


#### <a name="simulate-the-contoso-heat-pump-device"></a>Simular o dispositivo de bomba térmica da Contoso

1. Para simular o dispositivo de bomba térmica, atualize a chamada para `prov_dev_set_symmetric_key_info()` em **prov\_dev\_client\_sample.c** novamente com a ID de registro de bomba térmica e a chave do dispositivo derivada gerada anteriormente. O valor de chave **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** mostrado abaixo também é fornecido apenas como um exemplo.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```
   
    Salve o arquivo.

2. No menu do Visual Studio, selecione **Depurar** > **Iniciar sem depuração** para executar a solução. No prompt para recompilar o projeto, clique em **Sim** para recompilar o projeto antes da execução.

    A saída a seguir é um exemplo do dispositivo de bomba térmica simulado inicializando com êxito e, em seguida, conectando-se à instância do serviço de provisionamento a ser atribuído ao Hub IoT de bombas térmicas pela política de alocação personalizada:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```


## <a name="troubleshooting-custom-allocation-policies"></a>Solução de problemas em políticas de alocação personalizadas

A tabela a seguir mostra cenários esperados e os códigos de erro de resultados que podem ser encontrados. Use esta tabela para ajudar a solucionar problemas de falhas de política de alocação personalizada com o Azure Functions.


| Cenário | Resultado de registro do serviço de provisionamento | Resultados do SDK de provisionamento |
| -------- | --------------------------------------------- | ------------------------ |
| O webhook retorna 200 OK com 'iotHubHostName' definido como um nome de host do Hub IoT válido | Status do resultado: Atribuído  | O SDK retorna PROV_DEVICE_RESULT_OK juntamente com informações de hub |
| O webhook retorna 200 OK com 'iotHubHostName' presente na resposta, mas definido como uma cadeia de caracteres vazia ou nula | Status do resultado: Falhou<br><br> Código de erro: CustomAllocationIotHubNotSpecified (400208) | O SDK retorna PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| O webhook retorna 401 Não Autorizado | Status do resultado: Falhou<br><br>Código de erro: CustomAllocationUnauthorizedAccess (400209) | O SDK retorna PROV_DEVICE_RESULT_UNAUTHORIZED |
| Um registro individual foi criado para desabilitar o dispositivo | Status do resultado: Desabilitado | O SDK retorna PROV_DEVICE_RESULT_DISABLED |
| O webhook retorna o código de erro >= 429 | Orquestração do DPS tentará novamente várias vezes. A política de repetição atualmente é:<br><br>&nbsp;&nbsp;– Contagem de repetições: 10<br>&nbsp;&nbsp;– Intervalo inicial: 1s<br>&nbsp;&nbsp;– Incremento: 9s | O SDK ignorará o erro e enviará outra mensagem de obtenção de status no tempo especificado |
| O webhook retorna qualquer outro código de status | Status do resultado: Falhou<br><br>Código de erro: CustomAllocationFailed (400207) | O SDK retorna PROV_DEVICE_RESULT_DEV_AUTH_ERROR |


## <a name="clean-up-resources"></a>Limpar recursos

Caso planeje continuar a trabalhar com recursos criados neste artigo, você pode mantê-los. Caso contrário, use as etapas a seguir para excluir todos os recursos criados por este artigo, a fim de evitar alterações desnecessárias.

As etapas aqui supõem que você criou todos os recursos neste artigo, conforme instruído no mesmo grupo de recursos chamado **contoso-us-resource-group**.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. Caso tenha criado o Hub IoT dentro de um grupo de recursos existente que contém recursos que você deseja manter, exclua apenas o próprio recurso do Hub IoT em vez de excluir o grupo de recursos.
>

Para excluir o grupo de recursos por nome:

1. Entre no [portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

2. Na caixa de texto **Filtrar por nome...** , digite o nome do grupo de recursos que contém seus recursos, **contoso-us-resource-group**. 

3. À direita do seu grupo de recursos, na lista de resultados, clique em **...** , depois em **Excluir grupo de recursos**.

4. Você receberá uma solicitação para confirmar a exclusão do grupo de recursos. Digite o nome do grupo de recursos novamente para confirmar e clique em **Excluir**. Após alguns instantes, o grupo de recursos, e todos os recursos contidos nele, serão excluídos.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o reprovisionamento, consulte conceitos de reprovisionamento de [dispositivo do Hub IOT](concepts-device-reprovision.md) 
- Para saber mais sobre desprovisionamento, confira [como desprovisionar dispositivos que foram previamente provisionados automaticamente](how-to-unprovision-devices.md) 











