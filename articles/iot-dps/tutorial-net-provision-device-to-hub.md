---
title: Provisionar um dispositivo usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure (.NET) | Microsoft Docs
description: Provisionar o dispositivo para um único Hub IoT usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure (.NET)
author: wesmc7777
ms.author: wesmc
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 4a6a074c3f677023928fefa5c09eb305b5441dfe
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303985"
---
# <a name="enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>Registrar o dispositivo para um Hub IoT usando o Cliente do Serviço de Provisionamento do Hub IoT do Azure (.NET)

No tutorial anterior, você aprendeu como configurar um dispositivo para se conectar ao seu Serviço de Provisionamento de Dispositivos. Neste tutorial, você aprenderá a usar este serviço para provisionar seu dispositivo para um único Hub IoT usando **_Registro individuais_** e **_Grupos de registro_** . Este tutorial mostra como:

> [!div class="checklist"]
> * Registrar o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registrado

## <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, certifique-se de configurar seu dispositivo e o respectivo *Hardware Security Module*, conforme discutido no tutorial [Configurar um dispositivo para provisionar usando o Serviço de Provisionamento de Dispositivos no Azure Hub IoT](./tutorial-set-up-device.md).

* Visual Studio

> [!NOTE]
> O Visual Studio não é necessário. A instalação do [.NET](https://www.microsoft.com/net) é suficiente e os desenvolvedores podem usar seu editor preferido no Windows ou Linux.  

Este tutorial simula o período durante ou logo após o processo de fabricação do hardware, quando as informações deo dispositivo são adicionadas ao serviço de provisionamento. Este código normalmente é executado em um computador ou um dispositivo de fábrica que pode executar o código .NET e não deve ser adicionado aos dispositivos em si.


## <a name="enroll-the-device"></a>Registrar o dispositivo

Esta etapa envolve adicionar os artefatos de segurança exclusivos do dispositivo ao Serviço de Provisionamento de Dispositivos. Esses artefatos de segurança são os seguintes:

- Para dispositivos baseados em TPM:
    - A *chave de endosso* que é exclusiva para cada simulação ou chip do TPM. Leia [Compreender a chave de endosso do TPM](https://technet.microsoft.com/library/cc770443.aspx) para obter mais informações.
    - A *ID do registro* é usada para identificar exclusivamente um dispositivo no namespace/escopo. Isso pode ou não ser o mesmo que a ID do dispositivo. A ID é obrigatória para cada dispositivo. Para dispositivos baseados em TPM, a ID do registro pode ser derivada do TPM em si, por exemplo, um hash SHA-256 da chave de endosso do TPM.

- Para dispositivos baseados em X.509:
    - O [certificado X.509 emitido para o dispositivo](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx), na forma de um arquivo *.pem* ou *.cer*. Para o registro individual, você precisa usar o *certificado assinante* para seu sistema X.509, enquanto para grupos de registro, você precisa usar o *certificado raiz* ou um *certificado de signatário* equivalente.
    - A *ID do registro* é usada para identificar exclusivamente um dispositivo no namespace/escopo. Isso pode ou não ser o mesmo que a ID do dispositivo. A ID é obrigatória para cada dispositivo. Para dispositivos baseados no X.509, a ID do registro é derivada do nome comum (CN) do certificado. Para obter mais informações sobre esses requisitos, consulte [Conceitos de dispositivo](https://docs.microsoft.com/azure/iot-dps/concepts-device).

Há duas maneiras de registrar o dispositivo no Serviço de Provisionamento de Dispositivos:

- **Registros individuais** isso representa uma entrada para um único dispositivo que pode ser registrado no Serviço de Provisionamento de Dispositivos. Inscrições individuais podem usar tokens de certificados X.509 ou tokens SAS (em um TPM real ou virtual) como mecanismos de atestado. É recomendável usar inscrições individuais para dispositivos que exigem configurações iniciais exclusivas ou para dispositivos que só podem usar tokens SAS por meio do TPM como o mecanismo de atestado. Registros individuais podem ter a ID de dispositivo de Hub IoT desejada especificada.

- **Grupos de registro** Isso representa um grupo de dispositivos que compartilham um mecanismo de atestado específico. É recomendável usar um grupo de registro para um grande número de dispositivos que compartilham uma configuração inicial desejada ou para dispositivos que vão todos para o mesmo locatário. Os grupos de registro só são X.509 e todos compartilham um certificado de autenticação em sua cadeia de certificado X.509.

### <a name="enroll-the-device-using-individual-enrollments"></a>Registre o dispositivo usando Registros individuais

1. No Visual Studio, crie um projeto de Aplicativo do Console do Visual C# usando o modelo de projeto de **Aplicativo do Console**. . Nomeie o projeto como **DeviceProvisioning**.
    
1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **DeviceProvisioning** e, em seguida, clique em **Gerenciar Pacotes NuGet...** .

1. Na janela **Gerenciador de Pacotes NuGet**, selecione **Procurar** e pesquise por **microsoft.azure.devices.provisioning.service**. Selecione a entrada e clique em **Instalar** para instalar o pacote **Microsoft.Azure.Devices.Provisioning.Service** e aceite os termos de uso. Esse procedimento baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço de provisionamento de dispositivos do Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) e suas dependências.

1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. Adicione os seguintes campos à classe **Program** . Substitua o valor de espaço reservado pela cadeia de conexão do serviço de provisionamento de dispositivos que você anotou na seção anterior.
   
    ```csharp
    static readonly string ServiceConnectionString = "{Device Provisioning Service connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. Adicione o seguinte para implementar o registro no dispositivo:

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. Por fim, adicione o seguinte código ao método **Main** para abrir a conexão para o Hub IoT e iniciar o registro:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.ReadLine();
    ```
        
1. No Visual Studio, no Gerenciador de Soluções, clique com o botão direito na solução e clique em **Definir Projetos de Inicialização...** . Selecione **Único projeto de inicialização** e, em seguida, selecione o projeto **DeviceProvisioning** no menu suspenso.  

1. Execute o aplicativo de dispositivo .NET **DeviceProvisiong**. Ele deve configurar provisionamento do dispositivo: 

    ![Execução do registro individual](./media/tutorial-net-provision-device-to-hub/individual.png)

Quando o dispositivo for registrado com êxito, você o verá exibido no portal conforme demonstrado a seguir:

   ![Registro bem-sucedido no portal](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>Registre o dispositivo usando Grupos de registros

> [!NOTE]
> O exemplo de grupo de registros requer um certificado X.509.

1. No Gerenciador de Soluções do Visual Studio, abra o projeto **DeviceProvisioning** criado acima. 

1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. Adicione os seguintes campos à classe **Program** . Substitua o valor de espaço reservado pela localização do certificado X509.
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. Adicione o seguinte ao **Program.cs** para implementar o registro para o grupo:

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. Por fim, substitua o seguinte código no método **Main** para abrir a conexão para Hub IoT e iniciar o registro de grupo:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Execute o aplicativo de dispositivo .NET **DeviceProvisiong**. Ele deve configurar o provisionamento do grupo para o dispositivo: 

    ![Execução do registro de grupo](./media/tutorial-net-provision-device-to-hub/group.png)

    Quando o grupo de dispositivo for registrado com sucesso, você o verá exibido no portal conforme demonstrado a seguir:

   ![Registro de grupo bem-sucedido no portal](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>Iniciar o dispositivo

Neste ponto, a configuração a seguir está pronta para registro do dispositivo:

1. Seu dispositivo ou grupo de dispositivos são registrados no Serviço de Provisionamento de Dispositivos e 
2. O dispositivo está pronto com a segurança configurada e acessível por meio do aplicativo usando o SDK do cliente do Serviço de Provisionamento de Dispositivos.

Inicie o dispositivo para permitir que seu aplicativo cliente inicie o registro com o Serviço de Provisionamento de Dispositivos.  


## <a name="verify-the-device-is-registered"></a>Verificar se o dispositivo está registrado

Uma vez que o dispositivo é inicializado, as ações a seguir devem ocorrer. Veja o [Exemplo de cliente de dispositivo de provisionamento](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device) para obter mais detalhes. 

1. O dispositivo envia uma solicitação de registro para o Serviço de Provisionamento de Dispositivos.
2. Para dispositivos do TPM, o Serviço de Provisionamento de Dispositivos envia de volta um desafio de registro que é respondido pelo dispositivo. 
3. Após um registro bem-sucedido, o Serviço de Provisionamento de Dispositivos envia o URI do Hub IoT, a ID do dispositivo e a chave criptografada de volta para o dispositivo. 
4. O aplicativo cliente do Hub IoT no dispositivo se conecta então ao seu hub. 
5. Após a conexão bem-sucedida ao hub, você verá o dispositivo aparecer no **Device Explorer** do Hub IoT. 

    ![Conexão bem-sucedida ao hub no portal](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Registrar o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registrado

Avance para o próximo tutorial para aprender a provisionar vários dispositivos entre hubs com balanceamento de carga. 

> [!div class="nextstepaction"]
> [Provisionar dispositivos entre Hubs IoT com balanceamento de carga](./tutorial-provision-multiple-hubs.md)
