---
title: Tutorial – Provisionar um dispositivo X.509 simulado no Hub IoT do Azure usando o Java e grupos de registro
description: Neste tutorial, crie e provisione um dispositivo X.509 simulado usando o SDK de serviço e dispositivo do Java e grupos de registro para o DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 4cfbfe3e3e3ba620d8292767012c9bb866d8a878
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968087"
---
# <a name="tutorial-create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>Tutorial: Criar e provisionar um dispositivo X.509 simulado usando um dispositivo Java e um SDK de serviço e agrupar registros para o Serviço de Provisionamento de Dispositivos no Hub IoT

Estas etapas mostram como simular um dispositivo X.509 no computador de desenvolvimento que executa o sistema operacional Windows e usam um exemplo de código para conectar este dispositivo simulado com o Serviço de Provisionamento do Dispositivo e o Hub IoT usando grupos de registro. 

Conclua as etapas em [Configurar o Serviço de Provisionamento do Dispositivo Hub IoT com o Portal do Azure](./quick-setup-auto-provision.md) antes de continuar.


## <a name="prerequisites"></a>Pré-requisitos

1. Certifique-se de ter o [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) instalado no computador.

1. Baixe e instale o [Maven](https://maven.apache.org/install.html).

1. Verifique se o `git` está instalado em seu computador e é adicionado às variáveis de ambiente que podem ser acessadas pela janela de comando. Confira [ferramentas de cliente Git do Software Freedom Conservancy](https://git-scm.com/download/) para obter a versão mais recente das ferramentas `git` a serem instaladas, que inclui o **Git Bash**, o aplicativo de linha de comando que você pode usar para interagir com seu repositório Git local. 

1. Use a seguinte [Visão Geral de Certificados](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) para criar seus certificados de teste.

    > [!NOTE]
    > Esta etapa requer [OpenSSL](https://www.openssl.org/), que pode a ser criado e instalado da fonte ou baixada e instalada de [terceiros](https://wiki.openssl.org/index.php/Binaries) como [este](https://sourceforge.net/projects/openssl/). Se você já tiver criado seus certificados de _raiz_, _intermediário_ e _dispositivo_, você pode ignorar esta etapa.
    >

    1. Execute as etapas 1 e 2 para criar seus certificados _raiz_ e _intermediário_.

    1. Entre no portal do Azure, clique no botão **Todos os recursos** no menu esquerdo e abra o serviço de provisionamento.

        1. Na folha de resumo do Serviço de Provisionamento de Dispositivos, selecione **Certificados** e clique no botão **Adicionar** na parte superior.

        1. Em **Adicionar Certificado**, insira as seguintes informações:
            - Digite um nome de certificado exclusivo.
            - Selecione o arquivo **_RootCA.pem_** que você criou.
            - Uma vez concluído, clique no botão **Salvar**.

           ![Adicionar certificado](./media/tutorial-group-enrollments/add-certificate.png)

        1. Selecione o certificado recém-criado:
            - Clique em **Gerar Código de Verificação**. Copie o código gerado.
            - Execute a etapa de verificação. Insira o _código de verificação_ ou clique com o botão direito do mouse para colar na janela do PowerShell.  Pressione **Enter**.
            - Selecione o arquivo **_verifyCert4.pem_** recém-criados no portal do Azure. Clique em **Verificar**.

              ![Validar certificado](./media/tutorial-group-enrollments/validate-certificate.png)

    1. Conclua executando as etapas para criar seus certificados de dispositivo e os recursos de limpeza.

       > [!NOTE]
       > Ao criar certificados de dispositivo, certifique-se de usar somente caracteres alfanuméricos em letras minúsculas e hifens no seu nome de dispositivo.
       >


## <a name="create-a-device-enrollment-entry"></a>Criar uma entrada de registro de dispositivo

1. Abra um prompt de comando. Clone o repositório do GitHub para os exemplos de código do SDK do Java:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. No código-fonte baixado, navegue até a pasta de exemplo **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** . Abra o arquivo **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** em um editor de sua escolha e adicione os seguintes detalhes:

    1. Adicione a `[Provisioning Connection String]` do serviço de provisionamento pelo portal desta forma:

        1. Navegue até seu serviço de provisionamento no [Portal do Azure](https://portal.azure.com).

        1. Abra as **Políticas de acesso compartilhadas** e selecione uma política que tenha a permissão *EnrollmentWrite*.

        1. Copie a **Cadeia de conexão da chave primária**.

            ![Obter a cadeia de conexão de provisionamento do portal](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. No arquivo de código de exemplo **_ServiceEnrollmentGroupSample.java_** , substitua a `[Provisioning Connection String]` por uma **cadeia de conexão de chave primária**.

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. Abra o arquivo de certificado de assinatura intermediário em um editor de texto. Atualize o valor `PUBLIC_KEY_CERTIFICATE_STRING` com o valor de seu certificado de assinatura intermediário.

        Se você gerou seus certificados de dispositivo com o shell de Bash, *./certs/azure-iot-test-only.intermediate.cert.pem* contém a chave do certificado intermediário. Se seus certificados forem gerados com o PowerShell, *./Intermediate1.pem* será seu arquivo de certificado intermediário.

        ```java
        private static final String PUBLIC_KEY_CERTIFICATE_STRING =
                "-----BEGIN CERTIFICATE-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "-----END CERTIFICATE-----\n";
        ```

    1. Navegue até o hub IoT vinculado ao serviço de provisionamento no [Portal do Azure](https://portal.azure.com). Abra a guia **Visão geral** do hub e copie o **Nome do host**. Atribua este **Nome do host** ao parâmetro *IOTHUB_HOST_NAME*.

        ```java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

    1. Estude o exemplo de código. Ele cria, atualiza, consulta e exclui um registro de grupo de dispositivos X.509. Para verificar o registro bem-sucedido no portal, comente temporariamente as seguintes linhas de código no fim do arquivo _ServiceEnrollmentGroupSample.java_:

        ```java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    1. Salve o arquivo _ServiceEnrollmentGroupSample.java_.

1. Abra uma janela de comando e navegue até a pasta **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** .

1. Compile o código de exemplo usando este comando:

    ```cmd\sh
    mvn install -DskipTests
    ```

1. Execute o exemplo usando estes comandos na janela de comando:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

1. Observe se o registro foi bem-sucedido na janela de saída.

    ![Registro bem-sucedido](./media/tutorial-group-enrollments/enrollment.png) 

1. Navegue até seu serviço de provisionamento no Portal do Azure. Clique em **Gerenciar registros**. Observe que o grupo de dispositivos X.509 é exibido na guia **Grupos de registro**, com um *NOME DE GRUPO* gerado automaticamente.

## <a name="simulate-the-device"></a>Simular o dispositivo

1. Na folha de resumo do Serviço de Provisionamento de Dispositivos, selecione **Visão Geral** e anote o _Escopo da ID_ e o _Ponto de Extremidade Global do Serviço de Provisionamento_.

    ![Informações de serviço](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. Abra um prompt de comando. Navegue até a pasta de projeto de exemplo.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Edite `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` para incluir o _Escopo da ID_ e o _Ponto de Extremidade Global do Serviço de Provisionamento_ anotado anteriormente.

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    private static final int MAX_TIME_TO_WAIT_FOR_REGISTRATION = 10000; // in milli seconds
    private static final String leafPublicPem = "<Your Public PEM Certificate here>";
    private static final String leafPrivateKey = "<Your Private PEM Key here>";
    ```

1. Atualize as variáveis `leafPublicPem` e `leafPrivateKey` com os certificados de dispositivo público e privado.

    Se você gerou seus certificados de dispositivo com o PowerShell, os arquivos mydevice* contêm a chave pública, a chave privada e o PFX para o dispositivo.

    Se você gerou seus certificados de dispositivo com o shell Bash, /certs/new-device.cert.pem contém a chave pública. A chave privada do dispositivo estará no arquivo ./private/new-device.key.pem.

    Abra seu arquivo de chave pública e atualize a variável `leafPublicPem` com esse valor. Copie o texto de _-----BEGIN PRIVATE KEY-----_ a _-----END PRIVATE KEY-----_.

    ```java
    private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

    Abra seu arquivo de chave privada e atualize a variável `leafPrivatePem` com esse valor. Copie o texto de _-----BEGIN RSA PRIVATE KEY-----_ para _-----END RSA PRIVATE KEY-----_.

    ```java
    private static final String leafPrivateKey = "-----BEGIN RSA PRIVATE KEY-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END RSA PRIVATE KEY-----\n";
    ```

1. Adicione uma nova variável logo abaixo de `leafPrivateKey` para seu certificado intermediário. Dê um nome a essa nova variável `intermediateKey`. Dê a ela o valor do seu certificado de autenticação intermediário.

    Se você gerou seus certificados de dispositivo com o shell de Bash, *./certs/azure-iot-test-only.intermediate.cert.pem* contém a chave do certificado intermediário. Se seus certificados forem gerados com o PowerShell, *./Intermediate1.pem* será seu arquivo de certificado intermediário.

    ```java
    private static final String intermediateKey = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

1. Na função `main`, adicione a coleção `intermediateKey` para a coleção `signerCertificates` antes da inicialização de `securityProviderX509`.

    ```java
    public static void main(String[] args) throws Exception
    {
        ...

        try
        {
            ProvisioningStatus provisioningStatus = new ProvisioningStatus();

            // Add intermediate certificate as part of the certificate key chain.
            signerCertificates.add(intermediateKey);

            SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(leafPublicPem, leafPrivateKey, signerCertificates);
    ```

1. Salve suas alterações e compile o exemplo. Navegue até a pasta de destino e execute o arquivo jar criado.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

    ![Registro bem-sucedido](./media/tutorial-group-enrollments/registration.png)

1. No portal, navegue até o hub IoT vinculado ao seu serviço de provisionamento e abra a folha **Device Explorer**. No provisionamento bem-sucedido do dispositivo X.509 simulado para o hub, sua ID de dispositivo aparecerá na folha **Device Explorer** com o *STATUS* **habilitado**. Observe que talvez você precise clicar no botão **Atualizar** na parte superior, se você já tiver aberto a folha antes de executar o aplicativo de dispositivo de exemplo. 

    ![Dispositivo é registrado no Hub IoT](./media/tutorial-group-enrollments/hub-registration.png) 


## <a name="clean-up-resources"></a>Limpar os recursos

Se você planeja continuar a trabalhar e explorar o dispositivo cliente de exemplo, não limpe os recursos criados neste Guia de Início Rápido. Caso contrário, use as etapas a seguir para excluir todos os recursos criados por este Guia de Início Rápido.

1. Feche a janela de saída de exemplo de dispositivo cliente em seu computador.
1. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione o serviço de Provisionamento de Dispositivos. Abra a folha **Gerenciar Registros** de seu serviço e clique na guia **Registros Individuais**. Selecione *ID de REGISTRO* do dispositivo descrito no Guia de Início Rápido e clique no botão **Excluir** na parte superior. 
1. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione seu Hub IoT. Abra a folha **Dispositivos IoT** do hub, selecione *DEVICE ID* registrado nesse Guia de Início Rápido, e clique no botão **Excluir** na parte superior.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um dispositivo simulado X.509 no seu computador Windows e o provisionou no Hub IoT usando o Serviço de Provisionamento de Dispositivos do Hub IoT e grupos de registro. Para saber mais sobre o seu dispositivo X.509, continue para conceitos de dispositivo. 

> [!div class="nextstepaction"]
> [Conceitos do Serviço de Provisionamento de Dispositivos no Hub IoT](concepts-service.md)