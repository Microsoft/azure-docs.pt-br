---
title: Como provisionar dispositivos para multilocação no serviço de provisionamento de dispositivos no Hub IoT do Azure
description: Como provisionar dispositivos para multilocação com sua instância do serviço de provisionamento de dispositivos (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: eebcfb98d4e155ca965b9e4c68a862afa08a46ca
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727423"
---
# <a name="how-to-provision-for-multitenancy"></a>Como provisionar para multilocação 

Este artigo demonstra como provisionar com segurança vários dispositivos de chave simétrica para um grupo de hubs IoT usando uma [política de alocação](concepts-service.md#allocation-policy). As políticas de alocação definidas pelo serviço de provisionamento oferecem suporte a uma variedade de cenários de alocação. Os dois cenários mais comuns são:

* **Localização geográfica / GeoLatency**: uma vez que um dispositivo se move entre locais, a latência de rede é aprimorada fazendo com que o dispositivo provisionasse para o Hub do IoT mais perto de cada localização. Nesse cenário, um grupo de hubs de IoT, que se estendem entre regiões, são selecionados para os registros. A política de alocação de **menor latência** é selecionada para esses registros. Essa política faz com que o serviço de provisionamento de dispositivos avaliem a latência de dispositivo e determinar o hub IoT fora do grupo de hubs IoT armário. 

* **A multilocação**: dispositivos usados dentro de uma solução de IoT precisam ser atribuídos a um hub IoT específico ou um grupo de hubs IoT. A solução pode exigir todos os dispositivos para um locatário específico para se comunicarem com um grupo específico de hubs IoT. Em alguns casos, um locatário pode possuir hubs IoT e exigir que os dispositivos sejam atribuídos a seus hubs IoT.

É comum combinar esses dois cenários. Por exemplo, uma solução de IoT multilocatária comumente atribuirá os dispositivos de locatário usando um grupo de hubs IoT que estão espalhados entre regiões. Esses dispositivos de locatário podem ser atribuídos ao hub IoT no grupo, que tem a menor latência com base na localização geográfica.

Este artigo usa uma amostra de dispositivo simulado com o [SDK do Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) para demonstrar como provisionar dispositivos em um cenário de multilocatário entre regiões. Neste artigo, você realizará as seguintes etapas:

* Usar a CLI do Azure para criar dois hubs IoT regionais (**Oeste dos EUA** e **Leste dos EUA**)
* Criar um registro de multilocatário
* Usar a CLI do Azure para criar duas VMs do Linux regional para agir como dispositivos nas mesmas regiões (**Oeste dos EUA** e **Leste dos EUA**)
* Preparar o ambiente de desenvolvimento para o SDK de C do IoT do Azure em ambas VMs do Linux
* Simule os dispositivos para ver que eles são provisionados para o mesmo locatário na região mais próxima.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

- Conclusão do guia de início rápido [Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>Criar dois Hubs IoT regionais

Nesta seção, você usará o Azure Cloud Shell para criar dois novos IoT hubs regionais nas regiões **Oeste dos EUA** e **Leste dos EUA** para ter um locatário.


1. Use o Azure Cloud Shell para criar um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

    O exemplo a seguir cria um grupo de recursos chamado *contoso-us-resource-group* na região *eastus*. É recomendável que você use esse grupo para todos os recursos criados neste artigo. Isso facilitará a limpeza após você terminar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Use o Azure Cloud Shell para criar o Hub IoT na região **leste dos EUA** com o comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). O Hub IoT será adicionado ao *contoso-us-resource-group*.

    O exemplo a seguir cria um Hub IoT chamado *contoso-east-hub* no local *leste dos EUA*. Você deve usar seu próprio nome exclusivo de hub, em vez de **contoso-east-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Esse comando pode demorar um pouco para ser concluído.

3. Use o Azure Cloud Shell para criar o Hub IoT na região **oeste dos EUA** com o comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). Este Hub IoT também será adicionado ao *contoso-us-resource-group*.

    O exemplo a seguir cria um Hub IoT chamado *contoso-west-hub* no local *oeste dos EUA*. Você deve usar seu próprio nome exclusivo de hub, em vez de **contoso-west-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Esse comando pode demorar um pouco para ser concluído.



## <a name="create-the-multitenant-enrollment"></a>Criar o registro de multilocatário

Nesta seção, você criará um novo grupo de registro para os dispositivos de locatário.  

Para simplificar, este artigo usa o [Atestado de chave simétrica](concepts-symmetric-key-attestation.md) com o registro. Para uma solução mais segura, considere usar o [Atestado de certificado X.509](concepts-x509-attestation.md) com uma cadeia de confiança.

1. Entre no [portal do Azure](https://portal.azure.com) e abra a instância do Serviço de Provisionamento de Dispositivos.

2. Selecione a guia **Gerenciar registros** e clique no botão **Adicionar grupo de registros** na parte superior da página. 

3. Em **Adicionar Grupo de Registros**, insira as informações a seguir e clique no botão **Salvar**.

    **Nome do grupo**: Insira **contoso-us-devices**.

    **Tipo de atestado**: Selecione **Chave simétrica**.

    **Gerar chaves automaticamente**: esta caixa de seleção já deve estar marcada.

    **Selecione como você deseja atribuir dispositivos a hubs**: selecione **Menor latência**.

    ![Adicionar grupo de registros de multilocação para atestado de chave simétrica](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. Em **Adicionar Grupo de Registros**, clique em **Vincular um novo Hub IoT** para vincular ambos os seus novos Hubs IoT regionais.

    **Assinatura**: se você tem várias assinaturas, escolha a assinatura em que você criou os Hubs IoT regionais.

    **Hub IoT**: selecione um dos hubs regionais que você criou.

    **Política de acesso**: escolha **iothubowner**.

    ![Vincular os Hubs IoT regionais ao serviço de provisionamento](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Depois que ambos os Hubs IoT regionais foram vinculados, você deve selecioná-los como o grupo de Hubs IoT e clicar em **Salvar** para criar o grupo de hub de IoT regional para o registro.

    ![Criar o grupo de hubs regionais para o registro](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Após salvar o registro, abra-o novamente e anote a **Chave primária**. Para que as chaves sejam geradas, é preciso primeiro salvar o registro. Essa chave será usada mais tarde para gerar chaves de dispositivo exclusivas para dispositivos simulados.


## <a name="create-regional-linux-vms"></a>Criar VMs do Linux regionais

Nesta seção, você criará duas VMs regionais Linux (máquinas virtuais). Essas VMs executarão um exemplo de simulação do dispositivo de cada região para demonstrar o provisionamento de dispositivos para dispositivos de locatário de ambas as regiões.

Para fazer a limpeza, essas VMs serão adicionadas ao mesmo grupo de recursos que contém os hubs IoT que foram criados, *contoso-us-resource-group*. No entanto, as VMs serão executadas em regiões separadas (**Oeste dos EUA** e **Leste dos EUA**).

1. No Azure Cloud Shell, execute o seguinte comando para criar uma região **Leste dos EUA** de VM depois de fazer as seguintes alterações de parâmetro no comando:

    **--nome**: insira um nome exclusivo para seu dispositivo regional VM no **Leste dos EUA**. 

    **nome de usuário-admin-**: usar seu próprio nome de usuário administrador.

    **-- admin-password**: Use sua própria senha de administrador.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Esse comando vai demorar um pouco para ser concluído. Quando o comando for concluído, anote o valor **publicIpAddress** para a sua VM de região do Leste dos EUA.

1. No Azure Cloud Shell, execute o comando para criar uma região **Oeste dos EUA** de VM depois de fazer as seguintes alterações de parâmetro no comando:

    **--nome**: insira um nome exclusivo para seu dispositivo regional VM no **Oeste dos EUA**. 

    **nome de usuário-admin-**: usar seu próprio nome de usuário administrador.

    **-- admin-password**: Use sua própria senha de administrador.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Esse comando vai demorar um pouco para ser concluído. Quando o comando for concluído, anote o valor **publicIpAddress** para a sua VM de região do Oeste dos EUA.

1. Abra dois shells de linha de comando. Conecte-se para uma das VMs regionais em cada shell usando o SSH. 

    Passe o seu nome de usuário administrador e o endereço IP público que você anotou para a VM como parâmetros para o SSH. Insira a senha de administrador do servidor quando solicitado.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Preparar o ambiente de desenvolvimento do SDK de C do IoT do Azure

Nesta seção, você clona o SDK do Azure IoT C em cada VM. O SDK contém um exemplo que simulará a configuração de cada região do dispositivo de um locatário.

1. Para cada VM, instale **CMake**, **g + +**, **gcc** e [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) usando os seguintes comandos:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Localize o nome da tag para a [versão mais recente](https://github.com/Azure/azure-iot-sdk-c/releases/latest) do SDK.

1. Clone o [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) em ambas as VMs.  Use a tag que você encontrou na etapa anterior como o valor para o parâmetro `-b`:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Essa operação deve demorar alguns minutos.

1. Para ambas as VMs, crie uma nova pasta **cmake** dentro do repositório e mude para a pasta.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Para ambas as VMs, execute o comando a seguir que cria uma versão do SDK específica para a plataforma cliente de desenvolvimento. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Após o sucesso da compilação, as últimas linhas de saída serão semelhantes à seguinte saída:

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>Derivar chaves de dispositivo exclusivas

Ao usar o atestado de chave simétrica com registros em grupo, você não usa as chaves de registro de grupo diretamente. Em vez disso, você cria uma chave derivada e exclusiva para cada dispositivo e mencionada em [Registros de Grupo com chaves simétricas](concepts-symmetric-key-attestation.md#group-enrollments).

Para gerar a chave do dispositivo, use a chave mestra de grupo para computar o [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) da ID de registro de dispositivo único para cada um dos dispositivos, depois converta o resultado no formato Base64.

Não inclua a chave mestra de grupo em seu código de dispositivo.

Use o exemplo de Bash shell para criar uma chave do dispositivo derivada para cada dispositivo usando **openssl**.

- Substitua o valor de **CHAVE** pela **Chave primária** que você anotou anteriormente para seu registro.

- Substitua o valor de **REG_ID** com sua própria ID de registro exclusivos para cada dispositivo. Use apenas caracteres alfanuméricos minúsculos e traço ('-') para definir ambas IDs.

Geração de chave de dispositivo de exemplo para *contoso-simdevice-east*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Geração de chave de dispositivo de exemplo para *contoso-simdevice-west*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Os dispositivos de locatário cada usará sua chave do dispositivo derivada e a ID do registro para executar o atestado de chave simétrico com o grupo de registro durante o provisionamento para o locatário IoT hubs.




## <a name="simulate-the-devices-from-each-region"></a>Simular os dispositivos de cada região


Nesta seção, você atualizará um exemplo de provisionamento no SDK de C do IoT do Azure para ambas as VMs regionais. 

Esse código de exemplo simula uma sequência de inicialização do dispositivo que envia a solicitação de provisionamento à sua instância do Serviço de Provisionamento de Dispositivos. A sequência de inicialização fará com que o dispositivo seja reconhecido e atribuído ao Hub IoT que esteja mais próximo baseado em latência.

1. No portal do Azure, selecione a guia **Visão Geral** de seu serviço de Provisionamento de Dispositivos e anote o valor de **_Escopo da ID_**.

    ![Extrair informações do ponto de extremidade do Serviço de Provisionamento de Dispositivo na folha do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Abra **~/azure-iot-sdk-c/provisioning\_client/samples/prov\_dev\_client\_sample/prov\_dev\_client\_sample.c** para editar em ambas VMs.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Localize a constante `id_scope` e substitua o valor pelo seu valor de **Escopo de ID** copiado anteriormente. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Encontre a definição da função `main()` no mesmo arquivo. Verifique se a `hsm_type` variável é definida como `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` conforme mostrado abaixo para coincidir com o método de atestado de grupo de registro. 

    Salve suas alterações para os arquivos em ambas as VMs.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Em ambas as VMs, localize a chamada para `prov_dev_set_symmetric_key_info()` em **prov\_dev\_client\_sample.c** que é comentado.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Remova as marcas de comentário das chamadas de função e substitua os valores de espaço reservado (incluindo os colchetes angulares) pelas IDs de registro exclusivo e chaves de dispositivo derivadas para cada dispositivo. As chaves mostradas abaixo são apenas para fins de exemplo. Use as chaves que você gerou anteriormente.

    Leste dos EUA:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    Oeste dos EUA:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Salve os arquivos.

1. Em ambas as VMs, navegue até a pasta de exemplo mostrada abaixo e compilar o exemplo.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. A compilação for bem-sucedida, execute **prov\_dev\_cliente\_sample.exe** em ambas as VMs para simular um dispositivo de locatário de cada região. Observe que cada dispositivo é alocado para o locatário do hub IoT mais próximo de regiões do dispositivo simulado.

    Execute a simulação:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Exemplo de saída da VM do Leste dos EUA:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Exemplo de saída da VM do Oeste dos EUA:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



## <a name="clean-up-resources"></a>Limpar os recursos

Caso planeje continuar a trabalhar com recursos criados neste artigo, você pode mantê-los. Caso contrário, use as etapas a seguir para excluir todos os recursos criados por este artigo, a fim de evitar alterações desnecessárias.

As etapas aqui supõem que você criou todos os recursos neste artigo, conforme instruído no mesmo grupo de recursos chamado **contoso-us-resource-group**.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. Caso tenha criado o Hub IoT dentro de um grupo de recursos existente que contém recursos que você deseja manter, exclua apenas o próprio recurso do Hub IoT em vez de excluir o grupo de recursos.
>

Para excluir o grupo de recursos por nome:

1. Entre no [portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

2. Na caixa de texto **Filtrar por nome...**, digite o nome do grupo de recursos que contém seus recursos, **contoso-us-resource-group**. 

3. À direita do seu grupo de recursos, na lista de resultados, clique em **...**, depois em **Excluir grupo de recursos**.

4. Você receberá uma solicitação para confirmar a exclusão do grupo de recursos. Digite o nome do grupo de recursos novamente para confirmar e clique em **Excluir**. Após alguns instantes, o grupo de recursos, e todos os recursos contidos nele, serão excluídos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> Para saber mais sobre o reprovisionamento, consulte [conceitos de reprovisionamento de dispositivo do Hub IOT](concepts-device-reprovision.md)

> [!div class="nextstepaction"]
> Para saber mais sobre o desprovisionamento, consulte [como desprovisionar dispositivos que foram previamente provisionados automaticamente](how-to-unprovision-devices.md)