---
title: Tutorial – Provisionar dispositivos X.509 no Hub IoT do Azure usando um HSM (módulo de segurança de hardware) personalizado
description: Este tutorial usa grupos de registro. Neste tutorial, você aprenderá a provisionar dispositivos X.509 usando um HSM (módulo de segurança de hardware) personalizado e o SDK do dispositivo C para o DPS (Serviço de Provisionamento de Dispositivos no Hub IoT do Azure).
author: wesmc7777
ms.author: wesmc
ms.date: 11/18/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f6026680dd566bf7a13c83b37883341bff8b4570
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354717"
---
# <a name="tutorial-provision-multiple-x509-devices-using-enrollment-groups"></a>Tutorial: Provisionar vários dispositivos X.509 usando grupos de registro

Neste tutorial, você aprenderá a provisionar grupos de dispositivos IoT que usam certificados X.509 para autenticação. O código de exemplo do [SDK do IoT do Azure para C](https://github.com/Azure/azure-iot-sdk-c) será usado para provisionar seu computador de desenvolvimento como um dispositivo IoT. 

O Serviço de Provisionamento de Dispositivos de IoT do Azure dá suporte a dois tipos de registros:

* [Grupos de registros](concepts-service.md#enrollment-group): usados para inscrever vários dispositivos relacionados.
* [Registros individuais](concepts-service.md#individual-enrollment): usados para inscrever um único dispositivo.

Este tutorial é semelhante aos tutoriais anteriores que demonstram como usar grupos de registro para provisionar conjuntos de dispositivos. No entanto, os certificados X.509 serão usados neste tutorial em vez das chaves simétricas. Examine os tutoriais anteriores nesta seção para obter uma abordagem simples usando [chaves simétricas](./concepts-symmetric-key-attestation.md).

Este tutorial demonstrará o [exemplo de HSM personalizado](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/custom_hsm_example), que fornece uma implementação de stub para estabelecer a interface com o armazenamento seguro baseado em hardware. Um [HSM (módulo de segurança de hardware)](./concepts-service.md#hardware-security-module) é usado para o armazenamento seguro baseado em hardware dos segredos do dispositivo. Um HSM pode ser usado com uma chave simétrica, um certificado X.509 ou um atestado do TPM para fornecer armazenamento seguro para os segredos. O armazenamento baseado em hardware de segredos do dispositivo é altamente recomendável.

Se você não estiver familiarizado com o processo de provisionamento automático, examine a visão geral de [provisionamento](about-iot-dps.md#provisioning-process). Além disso, conclua as etapas em [Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT com o portal do Azure](quick-setup-auto-provision.md) antes de continuar com este tutorial. 


Neste tutorial, você concluirá os seguintes objetivos:

> [!div class="checklist"]
> * Criar uma cadeia de certificados de confiança para organizar um conjunto de dispositivos usando certificados X.509.
> * Concluir a prova de posse com um certificado de autenticação usado com a cadeia de certificados.
> * Criar um registro de grupo que usa a cadeia de certificados
> * Configurar o ambiente de desenvolvimento para provisionar um dispositivo usando o código do [SDK do IoT do Azure para C](https://github.com/Azure/azure-iot-sdk-c)
> * Provisionar um dispositivo usando a cadeia de certificados com o exemplo de HSM (módulo de segurança de hardware) personalizado no SDK.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos a seguir são para um ambiente de desenvolvimento do Windows. Para Linux ou macOS, confira a seção apropriada em [Preparar seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) na documentação do SDK.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) com a carga de trabalho ["Desenvolvimento para desktop com C++"](https://docs.microsoft.com/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) habilitada. Também há suporte para o Visual Studio 2015 e o Visual Studio 2017.

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Preparar o ambiente de desenvolvimento do SDK de C do IoT do Azure

Nesta seção, você preparará um ambiente de desenvolvimento usado para compilar o [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c). O SDK inclui o código de exemplo e as ferramentas usadas pelo provisionamento de dispositivos X.509 com o DPS.

1. Baixe o [sistema de build CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio ([Visual Studio](https://visualstudio.microsoft.com/vs/) e a carga de trabalho de ['Desenvolvimento para Desktop com C++'](https://docs.microsoft.com/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)) estejam instalados no computador, **antes** do início da instalação do `CMake`. Após a instalação dos pré-requisitos e verificação do download, instale o sistema de compilação CMake.

2. Localize o nome da tag para a [versão mais recente](https://github.com/Azure/azure-iot-sdk-c/releases/latest) do SDK de C do Azure IoT.

3. Abra um prompt de comando ou o shell Bash do Git. Execute os comandos a seguir para clonar a versão mais recente do repositório do GitHub do [SDK do Azure IoT C](https://github.com/Azure/azure-iot-sdk-c). Use a tag que você encontrou na etapa anterior como o valor para o parâmetro `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Essa operação deve demorar alguns minutos.

4. Crie um subdiretório `cmake` no diretório raiz do repositório git e navegue até essa pasta. 

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. O diretório `cmake` que você criou conterá o exemplo de HSM personalizado e o código de exemplo de provisionamento de dispositivos que usa o HSM personalizado para fornecer a autenticação X.509. 

    Execute o comando a seguir no diretório `cmake` para compilar uma versão do SDK específica da plataforma de desenvolvimento. O build incluirá uma referência ao exemplo de HSM personalizado. 

    Ao especificar o caminho usado com `-Dhsm_custom_lib` abaixo, lembre-se de usar o caminho relativo ao diretório `cmake` criado anteriormente. O caminho relativo mostrado abaixo é apenas um exemplo.

    ```cmd
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    ```

    Se `cmake` não encontrar o compilador do C++, você poderá obter erros de build ao executar o comando acima. Se isso acontecer, tente executar esse comando no [prompt de comando do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

    Depois que o build for realizado com sucesso, uma solução do Visual Studio será gerada no diretório `cmake`. As últimas linhas de saída são semelhantes à seguinte saída:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: D:/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-x509-certificate-chain"></a>Criar uma cadeia de certificados X.509

Nesta seção, você vai gerar uma cadeia de certificados X.509 de três certificados para teste com este tutorial. Os certificados terão a hierarquia a seguir.

![Cadeia de certificados do dispositivo do tutorial](./media/tutorial-custom-hsm-enrollment-group-x509/example-device-cert-chain.png#lightbox)

[Certificado raiz](concepts-x509-attestation.md#root-certificate): você concluirá a [prova de posse](how-to-verify-certificates.md) para verificar o certificado raiz. Essa verificação permitirá que o DPS confie nesse certificado e verifique os certificados assinados por ele.

[Certificado Intermediário](concepts-x509-attestation.md#intermediate-certificate): é comum que certificados intermediários sejam usados para agrupar dispositivos logicamente por linhas de produtos, divisões da empresa ou outros critérios. Este tutorial usará uma cadeia de certificados composta por um certificado intermediário. O certificado intermediário será assinado pelo certificado raiz. Esse certificado também será usado no grupo de registro criado no DPS para agrupar um conjunto de dispositivos logicamente. Essa configuração permite gerenciar um grupo inteiro de dispositivos que têm certificados de dispositivo assinados pelo mesmo certificado intermediário. Você pode criar grupos de registro para habilitar ou desabilitar um grupo de dispositivos. Para obter mais informações sobre como desabilitar um grupo de dispositivos, confira [Não permitir um Certificado de Autoridade de Certificação intermediário ou raiz X.509 usando um grupo de registro](how-to-revoke-device-access-portal.md#disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group)

[Certificado do dispositivo](concepts-x509-attestation.md#end-entity-leaf-certificate): o certificado do dispositivo (folha) será assinado pelo certificado intermediário e armazenado no dispositivo junto com a respectiva chave privada. O dispositivo apresentará esse certificado e a chave privada, juntamente com a cadeia de certificados ao tentar realizar o provisionamento. 

Para criar a cadeia de certificados:

1. Abra um prompt de comando do Git Bash. Conclua as etapas 1 e 2 usando as instruções do shell do Bash que estão localizadas em [Como gerenciar Certificados de Autoridade de Certificação de teste para obter exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md#managing-test-ca-certificates-for-samples-and-tutorials).

    Esta etapa cria um diretório de trabalho para os scripts de certificado e gera a raiz de exemplo e o certificado intermediário para a cadeia de certificados usando o OpenSSL. 

    Observe a saída, que mostra a localização do certificado raiz autoassinado. Esse certificado passará pela [prova de posse](how-to-verify-certificates.md) para verificar a propriedade mais tarde.

    ```output
    Creating the Root CA Certificate
    CA Root Certificate Generated At:
    ---------------------------------
        ./certs/azure-iot-test-only.root.ca.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                fc:cc:6b:ab:3b:9a:3e:fe
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:30 2020 GMT
                Not After : Nov 22 21:30:30 2020 GMT
            Subject: CN=Azure IoT Hub CA Cert Test Only
    ```        

    Observe a saída, que mostra a localização do certificado intermediário que é assinado/emitido pelo certificado raiz. Esse certificado será usado com o grupo de registro que você criará posteriormente.

    ```output
    Intermediate CA Certificate Generated At:
    -----------------------------------------
        ./certs/azure-iot-test-only.intermediate.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 1 (0x1)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:33 2020 GMT
                Not After : Nov 22 21:30:33 2020 GMT
            Subject: CN=Azure IoT Hub Intermediate Cert Test Only
    ```    

2. Em seguida, execute o comando a seguir para criar um certificado do dispositivo/folha com um nome de entidade fornecido como um parâmetro. Use o exemplo de nome da entidade fornecido para este tutorial, `custom-hsm-device-01`. Esse nome da entidade será a identificação do dispositivo IoT. 

    > [!WARNING]
    > Não use um nome de entidade com espaços. Esse nome da entidade é a identificação do dispositivo IoT que está sendo provisionado. Ele precisa seguir as regras de uma identificação do dispositivo. Para obter mais informações, confira [Propriedades da identidade do dispositivo](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).

    ```cmd
    ./certGen.sh create_device_certificate_from_intermediate "custom-hsm-device-01"
    ```

    Observe a saída a seguir, que mostra a localização do novo certificado do dispositivo. O certificado do dispositivo é assinado (emitido) pelo certificado intermediário.

    ```output
    -----------------------------------
    ./certs/new-device.cert.pem: OK
    Leaf Device Certificate Generated At:
    ----------------------------------------
        ./certs/new-device.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 9 (0x9)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub Intermediate Cert Test Only
            Validity
                Not Before: Nov 10 09:20:33 2020 GMT
                Not After : Dec 10 09:20:33 2020 GMT
            Subject: CN=custom-hsm-device-01
    ```    
    
3. Execute o comando a seguir para criar um arquivo .pem de cadeia de certificados completa que inclui o novo certificado do dispositivo.

    ```Bash
    cd ./certs && cat new-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-device-full-chain.cert.pem
    ```

    Use um editor de texto e abra o arquivo da cadeia de certificados, *./certs/new-device-full-chain.cert.pem*. O texto da cadeia de certificados contém a cadeia completa de todos os três certificados. Você usará esse texto como a cadeia de certificados com o código do HSM personalizado mais adiante neste tutorial.

    O texto da cadeia completa tem o seguinte formato:
 
    ```output 
    -----BEGIN CERTIFICATE-----
        <Text for the device certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the intermediate certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the root certificate includes public key>
    -----END CERTIFICATE-----
    ```

5. Observe que a chave privada do novo certificado do dispositivo é gravada em *./private/new-device.key.pem*. O texto dessa chave será necessário para o dispositivo durante o provisionamento. Ele será adicionado ao exemplo de HSM personalizado posteriormente.

    > [!WARNING]
    > O texto dos certificados contém apenas informações da chave pública. 
    >
    > No entanto, o dispositivo também precisa ter acesso à chave privada do certificado do dispositivo. Isso é necessário porque o dispositivo precisa executar a verificação usando essa chave em runtime ao tentar realizar o provisionamento. A confidencialidade dessa chave é um dos principais motivos pelos quais recomendamos usar o armazenamento baseado em hardware em um HSM real para ajudar a proteger as chaves privadas.



## <a name="configure-the-custom-hsm-stub-code"></a>Configurar o código de stub do HSM personalizado

As especificidades de interação com o armazenamento baseado em hardware seguro real variam conforme o hardware. Como resultado, a cadeia de certificados usada pelo dispositivo neste tutorial será embutida no código de stub do HSM personalizado. Em um cenário do mundo real, a cadeia de certificados é armazenada no hardware do HSM real a fim de fornecer melhor segurança para as informações confidenciais. Depois, métodos semelhantes aos métodos de stub mostrados neste exemplo são implementados para ler os segredos desse armazenamento baseado em hardware.

Para atualizar o código de stub do HSM personalizado deste tutorial:

1. Inicie o Visual Studio e abra o arquivo de solução criado no diretório `cmake` que você criou na raiz do repositório Git azure-iot-sdk-c. O arquivo de solução é chamado `azure_iot_sdks.sln`.

2. No Gerenciador de Soluções para Visual Studio, acesse **Provisioning_Samples > custom_hsm_example > Arquivos de Origem** e abra *custom_hsm_example.c*.

3. Atualize o valor de cadeia de caracteres da constante de cadeia de caracteres `COMMON_NAME` usando o nome comum usado ao gerar o certificado do dispositivo.

    ```c
    static const char* const COMMON_NAME = "custom-hsm-device-01";
    ```

4. No mesmo arquivo, atualize o valor da cadeia de caracteres constante `CERTIFICATE` usando o texto da cadeia de certificados salvo em *./certs/new-device-full-chain.cert.pem* depois de gerar os certificados.

    > [!IMPORTANT]
    > Ao copiar o texto no Visual Studio, você poderá observar que o texto é analisado e atualizado com o espaçamento de código etc. Nesse caso, você precisará remover esse espaçamento e a análise clicando em **CTRL + Z** uma vez.

    Atualize o texto do certificado, de modo que ele siga o padrão abaixo sem espaços extras nem a análise feita pelo Visual Studio:

    ```c
    // <Device/leaf cert>
    // <intermediates>
    // <root>
    static const char* const CERTIFICATE = "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB"
    "\n-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFPDCCAySgAwIBAgIBATANBgkqhkiG9w0BAQsFADAqMSgwJgYDVQQDDB9BenVy"
        ...
    "MTEyMjIxMzAzM1owNDEyMDAGA1UEAwwpQXp1cmUgSW9UIEh1YiBJbnRlcm1lZGlh"
    "\n-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB"
    "\n-----END CERTIFICATE-----";        
    ```

5. No mesmo arquivo, atualize o valor da cadeia de caracteres da `PRIVATE_KEY` cadeia de caracteres constante usando a chave privada do certificado do dispositivo.

    > [!IMPORTANT]
    > Ao copiar o texto no Visual Studio, você poderá observar que o texto é analisado e atualizado com o espaçamento de código etc. Nesse caso, você precisará remover esse espaçamento e a análise clicando em **CTRL + Z** uma vez.

    Atualize o texto da chave privada, de modo que ele siga o padrão abaixo sem espaços extras nem a análise feita pelo Visual Studio:

    ```c
    static const char* const PRIVATE_KEY = "-----BEGIN RSA PRIVATE KEY-----\n"
    "MIIJJwIBAAKCAgEAtjvKQjIhp0EE1PoADL1rfF/W6v4vlAzOSifKSQsaPeebqg8U"
        ...
    "X7fi9OZ26QpnkS5QjjPTYI/wwn0J9YAwNfKSlNeXTJDfJ+KpjXBcvaLxeBQbQhij"
    "\n-----END RSA PRIVATE KEY-----";
    ```

6. Salve *custom_hsm_example.c*.


## <a name="verify-ownership-of-the-root-certificate"></a>Verificar a propriedade do certificado raiz

1. Uso das instruções descritas em [Registrar a parte pública de um certificado X.509 e obter um código de verificação](how-to-verify-certificates.md#register-the-public-part-of-an-x509-certificate-and-get-a-verification-code), carregar o certificado raiz e obter um código de verificação do DPS.

2. Depois de obter um código de verificação do DPS para o certificado raiz, execute o comando a seguir no diretório de trabalho do script de certificado para gerar um certificado de verificação.
 
    O código de verificação fornecido aqui é apenas um exemplo. Use o código gerado por meio do DPS.    

    ```Bash
    ./certGen.sh create_verification_certificate 1B1F84DE79B9BD5F16D71E92709917C2A1CA19D5A156CB9F    
    ```    

    Esse script cria um certificado assinado pelo certificado raiz com o nome da entidade definido como o código de verificação. Esse certificado permite que o DPS verifique se você tem acesso à chave privada do certificado raiz. Observe a localização do certificado de verificação na saída do script.

    ```output
    Leaf Device PFX Certificate Generated At:
    --------------------------------------------
        ./certs/verification-code.cert.pfx
    ```

3. Conforme mencionado em [Carregar o certificado de verificação assinado](how-to-verify-certificates.md#upload-the-signed-verification-certificate), carregue o certificado de verificação e clique em **Verificar** no DPS para concluir a prova de posse do certificado raiz.


## <a name="update-the-certificate-store-on-windows-based-devices"></a>Atualizar o repositório de certificados em dispositivos baseados no Windows

Em dispositivos não Windows, você pode passar a cadeia de certificados por meio do código como o repositório de certificados.

Em dispositivos baseados no Windows, você precisará adicionar os certificados de autenticação (raiz e intermediário) a um [repositório de certificados](https://docs.microsoft.com/windows/win32/secauthn/certificate-stores) do Windows. Caso contrário, os certificados de autenticação não serão transportados para o DPS por um canal seguro com o protocolo TLS.

Para adicionar os certificados de autenticação ao repositório de certificados em dispositivos baseados no Windows:

1. Em um prompt do Git Bash, acesse o subdiretório `certs` que contém os certificados de autenticação e converta-os em `.pfx`, conforme descrito a seguir.

    certificado raiz:

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.root.ca.key.pem -in ./azure-iot-test-only.root.ca.cert.pem -export -out ./root.pfx
    ```
    
    certificado intermediário:   

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.intermediate.key.pem -in ./azure-iot-test-only.intermediate.cert.pem -export -out ./intermediate.pfx
    ```

2. Clique com o botão direito do mouse no botão **Iniciar** do Windows. Em seguida, clique com o botão esquerdo do mouse em **Executar**. Insira *certmgr.mcs* e clique em **OK** para iniciar o snap-in do MMC do gerenciador de certificados.

3. No gerenciador de certificados, em **Certificados – Usuário Atual**, clique em **Autoridades de Certificação Raiz Confiáveis**. Em seguida, no menu, clique em **Ação** > **Todas as Tarefas** > **Importar** para importar o `root.pfx`.

    * Lembre-se de pesquisar **Troca de Informações Pessoais (.pfx)**
    * Use `1234` como a senha.
    * Coloque o certificado no repositório de certificados **Autoridades de Certificação Raiz Confiáveis**.

4. No gerenciador de certificados, em **Certificados – Usuário Atual**, clique em **Autoridades de Certificação Intermediárias**. Em seguida, no menu, clique em **Ação** > **Todas as Tarefas** > **Importar** para importar o `intermediate.pfx`.

    * Lembre-se de pesquisar **Troca de Informações Pessoais (.pfx)**
    * Use `1234` como a senha.
    * Coloque o certificado no repositório de certificados **Autoridades de Certificação Intermediárias**.

Os certificados de autenticação agora são confiáveis no dispositivo baseado no Windows, e a cadeia completa pode ser transportada para o DPS.



## <a name="create-an-enrollment-group"></a>Criar um grupo de registro

1. Entre no portal do Azure, selecione o botão **Todos os recursos** no menu esquerdo e abra o serviço de Provisionamento de Dispositivos.

2. Selecione a guia **Gerenciar registros** e escolha o botão **Adicionar grupo de registro** na parte superior.

3. No painel **Adicionar Grupo de Registro**, insira as informações a seguir e clique no botão **Salvar**.

      ![Adicionar grupo de registro para o atestado de X.509 no portal](./media/tutorial-custom-hsm-enrollment-group-x509/custom-hsm-enrollment-group-x509.png#lightbox)

    | Campo        | Valor           |
    | :----------- | :-------------- |
    | **Nome do grupo** | Para este tutorial, insira **custom-hsm-x509-devices** |
    | **Tipo de Atestado** | Selecione **Certificado** |
    | **Dispositivo do IoT Edge** | Selecione **Falso** |
    | **Tipo de certificado** | Selecione **Certificado Intermediário** |
    | **Arquivo .pem ou .cer de certificado primário** | Acesse o intermediário criado anteriormente ( *./certs/azure-iot-test-only.intermediate.cert.pem*) |


## <a name="configure-the-provisioning-device-code"></a>Configurar o código do dispositivo de provisionamento

Nesta seção, atualize o código de exemplo para provisionar o dispositivo com a instância do Serviço de Provisionamento de Dispositivos. Se o dispositivo for autenticado, ele será atribuído a um hub IoT vinculado à instância do Serviço de Provisionamento de Dispositivos.

1. No portal do Azure, selecione a guia **Visão Geral** de seu serviço de Provisionamento de Dispositivos e anote o valor de **_Escopo da ID_** .

    ![Extrair informações do ponto de extremidade do Serviço de Provisionamento de Dispositivo na folha do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. No Gerenciador de Soluções para Visual Studio, acesse **Provisioning_Samples > prov_dev_client_sample > Arquivos de Origem** e abra *prov_dev_client_sample.c*.

3. Localize a constante `id_scope` e substitua o valor pelo seu valor de **Escopo de ID** copiado anteriormente. 

    ```c
    static const char* id_scope = "0ne00000A0A";
    ```

4. Encontre a definição da função `main()` no mesmo arquivo. Verifique se a variável `hsm_type` está definida como `SECURE_DEVICE_TYPE_X509`, conforme mostrado abaixo.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    //hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

5. Clique com botão direito do mouse no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Inicialização**.

6. No menu do Visual Studio, selecione **Depurar** > **Iniciar sem depuração** para executar a solução. Quando a recompilação do projeto for solicitada, selecione **Sim** para recompilar o projeto antes da execução.

    A saída a seguir é um exemplo do cliente de dispositivo de provisionamento inicializando com êxito e conectando-se ao serviço de provisionamento. O dispositivo foi atribuído a um hub IoT e registrado:

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-01
    Press enter key to exit:
    ```

7. No portal, navegue até o Hub IoT vinculado ao seu serviço de provisionamento e selecione a guia **Dispositivos IoT**. No provisionamento bem-sucedido do dispositivo X.509 no hub, a identificação do dispositivo será exibida na folha **Dispositivos IoT** com o *STATUS* como **habilitado**. Talvez seja necessário pressionar o botão **Atualizar** na parte superior. 

    ![O dispositivo HSM personalizado é registrado no hub IoT](./media/tutorial-custom-hsm-enrollment-group-x509/hub-provisioned-custom-hsm-x509-device.png) 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de testar e explorar este exemplo do cliente de dispositivo, use as etapas a seguir para excluir todos os recursos criados por este tutorial.

1. Feche a janela de saída de exemplo de dispositivo cliente em seu computador.
1. No menu à esquerda no portal do Azure, selecione **Todos os recursos** e selecione o serviço de Provisionamento de Dispositivos. Abra **Gerenciar Registros** do serviço e selecione a guia **Grupos de Registros**. Marque a caixa de seleção ao lado do *Nome do Grupo* do grupo de dispositivos criado neste tutorial e clique no botão **Excluir** na parte superior do painel. 
1. Clique em **Certificados** no DPS. Para cada certificado carregado e verificado neste tutorial, clique no certificado e no botão **Excluir** para removê-lo.
1. No menu à esquerda no portal do Azure, selecione **Todos os recursos** e seu Hub IoT. Abra **Dispositivos IoT** do hub. Marque a caixa de seleção ao lado da *IDENTIFICAÇÃO DO DISPOSITIVO* do dispositivo registrado neste tutorial. Clique no botão **Excluir** na parte superior do painel.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você provisionou um dispositivo X.509 usando um HSM personalizado no hub IoT. Para saber como provisionar dispositivos IoT em vários hubs, prossiga para o próximo tutorial. 

> [!div class="nextstepaction"]
> [Tutorial: Provisionar dispositivos em vários hubs IoT com balanceamento de carga](tutorial-provision-multiple-hubs.md)
