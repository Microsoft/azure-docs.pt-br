---
title: Tutorial – Configurar dispositivo para o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure
description: Este tutorial mostra como você pode configurar o dispositivo para provisionar por meio do DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT durante o processo de fabricação do dispositivo
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: dd82b8ac3a510d1b16b0d2f42d3e50803162a119
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94959791"
---
# <a name="tutorial-set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Tutorial: Configurar um dispositivo para provisionar usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure

No tutorial anterior, você aprendeu como configurar o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure para provisionar automaticamente os dispositivos para o Hub IoT. Este tutorial mostra como configurar seu dispositivo durante o processo de fabricação, permitindo que ele seja provisionado automaticamente com o Hub IoT. Seu dispositivo é provisionado com base no [mecanismo de atestado](concepts-service.md#attestation-mechanism) após a primeira inicialização e conexão com o serviço de provisionamento. Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Criar o SDK de cliente dos Serviços de Provisionamento de Dispositivos específico da plataforma
> * Extrair os artefatos de segurança
> * Criar o software de registro do dispositivo

Este tutorial espera que você já tenha criado sua instância do Serviço de Provisionamento de Dispositivos e um Hub IoT usando as instruções mencionadas no tutorial anterior [Configurar recursos de nuvem](tutorial-set-up-cloud.md).

Este tutorial usa [SDKs do IoT do Azure e bibliotecas para repositório de C](https://github.com/Azure/azure-iot-sdk-c), que contêm o SDK do cliente do Serviço de Provisionamento de Dispositivo para C. O SDK atualmente oferece suporte a TPM e X.509 em dispositivos com implementações Windows ou Ubuntu. Este tutorial baseia-se no uso de um cliente de desenvolvimento do Windows, o que também assume o domínio básico do Visual Studio. 

Se você não estiver familiarizado com o processo de provisionamento automático, examine a visão geral de [provisionamento](about-iot-dps.md#provisioning-process) antes de continuar. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos a seguir são para um ambiente de desenvolvimento do Windows. Para Linux ou macOS, confira a seção apropriada em [Preparar seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) na documentação do SDK.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) com a carga de trabalho ["Desenvolvimento para desktop com C++"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) habilitada. Também há suporte para o Visual Studio 2015 e o Visual Studio 2017.

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

## <a name="build-a-platform-specific-version-of-the-sdk"></a>Criar uma versão do SDK específica da plataforma

O SDK de cliente do Serviço de Provisionamento de Dispositivos ajuda a implementar o software de registro do dispositivo. Mas antes de poder usá-lo, você criará uma versão do SDK específica para o mecanismo de atestado e a plataforma de desenvolvimento do cliente. Neste tutorial, você criará um SDK que usa o Visual Studio em uma plataforma de desenvolvimento Windows para um tipo de atestado com suporte:

1. Baixe o [sistema de build CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho de "Desenvolvimento para Desktop com C++") estejam instalados em seu computador, **antes** da instalação de `CMake`. Após a instalação dos pré-requisitos e verificação do download, instale o sistema de compilação CMake.

2. Localize o nome da tag para a [versão mais recente](https://github.com/Azure/azure-iot-sdk-c/releases/latest) do SDK.

3. Abra um prompt de comando ou o shell Bash do Git. Execute os comandos a seguir para clonar a versão mais recente do repositório do GitHub do [SDK do Azure IoT C](https://github.com/Azure/azure-iot-sdk-c). Use a tag que você encontrou na etapa anterior como o valor para o parâmetro `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Essa operação deve demorar alguns minutos.

4. Crie um subdiretório `cmake` no diretório raiz do repositório git e navegue até essa pasta. Execute os seguintes comandos do diretório `azure-iot-sdk-c`:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Crie o SDK para sua plataforma de desenvolvimento com base nos mecanismos de atestado que você usará. Use um dos comandos a seguir (observe também os dois caracteres de ponto à direita para cada comando). Após a conclusão, o CMake cria o subdiretório `/cmake` com conteúdo específico do dispositivo:
 
    - Para dispositivos que usam o simulador do TPM como atestado:

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - Para qualquer outro dispositivo (TPM/HSM/X.509 físico, ou um certificado X.509 simulado):

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```


Agora você está pronto para usar o SDK para criar seu código de registro do dispositivo. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>Extrair os artefatos de segurança 

A próxima etapa é extrair os artefatos de segurança para o mecanismo de atestado usado pelo dispositivo. 

### <a name="physical-devices"></a>Dispositivos físicos 

Dependendo se você criou o SDK para usar o atestado para um TPM/HSM físico ou está usando certificados X.509, a coleta dos artefatos de segurança é a seguinte:

- Para um dispositivo TPM, você precisa determinar a **Chave de Endosso** do fabricante do chip do TPM associada a ele. Você pode derivar uma única **ID de registro** para seu dispositivo TPM via hash da chave de endosso.  

- Para um dispositivo X.509, você precisará obter os certificados emitidos para seus dispositivos. O serviço de provisionamento expõe dois tipos de entradas de registro que controlam o acesso de dispositivos que usam o mecanismo de atestado X.509. Os certificados necessários dependem dos tipos de registro que você usará.

    - Registros individuais: Registro para um único dispositivo específico. Esse tipo de entrada de registro requer [entidade final, "folha", certificados](concepts-x509-attestation.md#end-entity-leaf-certificate).
    
    - Grupos de registro: Esse tipo de entrada de registro requer certificados raiz ou intermediários. Para obter mais informações, confira [Como controlar o acesso ao dispositivo para o serviço de provisionamento com certificados X.509](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

### <a name="simulated-devices"></a>Dispositivos simulados

Dependendo se você criou o SDK para usar o atestado para um dispositivo simulado usando certificados X.509 ou TPM, a coleta dos artefatos de segurança é a seguinte:

- Para um dispositivo do TPM simulado:

   1. Abra um Prompt de Comando do Windows, navegue até o subdiretório `azure-iot-sdk-c` e execute o simulador do TPM. Ele escuta em um soquete nas portas 2321 e 2322. Não feche essa janela de comando. Você precisará manter esse simulador em execução até o término do Início Rápido a seguir. 

      No subdiretório `azure-iot-sdk-c`, execute o comando a seguir para iniciar o simulador:

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

      > [!NOTE]
      > Se você usar o prompt de comando do Git Bash para esta etapa, será necessário alterar as barras invertidas para barras consecutivas, por exemplo: `./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe`.

   1. Usando o Visual Studio, abra a solução gerada na pasta *cmake* denominada `azure_iot_sdks.sln` e compile-a usando o comando "Compilar solução" no menu "Compilar".

   1. No painel *Gerenciador de Soluções* no Visual Studio, navegue até a pasta **Provisionar\_Ferramentas**. Clique com botão direito do mouse no projeto **tpm_device_provision** e selecione **Definir como Projeto de Inicialização**. 

   1. Execute a solução usando um dos comandos "Iniciar" no menu "Depurar". A janela de saída exibe a **_Chave de Endosso_** e a **_ID de Registro_** do TPM necessárias para a inscrição e o registro de dispositivos. Copie esses valores para uso posterior. Você pode fechar esta janela (com a ID de registro e a chave de endosso), mas deixe a janela do simulador do TPM que você iniciou na etapa #1 em execução.

- Para um dispositivo de X.509 simulado:

  1. Usando o Visual Studio, abra a solução gerada na pasta *cmake* denominada `azure_iot_sdks.sln` e compile-a usando o comando "Compilar solução" no menu "Compilar".

  1. No painel *Gerenciador de Soluções* no Visual Studio, navegue até a pasta **Provisionar\_Ferramentas**. Clique com o botão direito do mouse no projeto **dice\_device\_enrollment** e selecione **Definir como Projeto de Inicialização**. 
  
  1. Execute a solução usando um dos comandos "Iniciar" no menu "Depurar". Na janela de saída, insira **i** para registro individual quando solicitado. A janela de saída exibe um certificado X.509 gerado localmente para seu dispositivo simulado. Copie para a área de transferência a saída que começa em *-----BEGIN CERTIFICATE-----* e termina no primeiro *-----END CERTIFICATE-----* , não deixando de incluir ambas essas linhas também. Você precisa apenas do primeiro certificado da janela de saída.
 
  1. Crie um arquivo chamado **_X509testcert.pem_** , abra-o em um editor de texto de sua escolha e copie o conteúdo da área de transferência para o arquivo. Salve o arquivo, pois você o usará mais tarde para registrar o dispositivo. Quando o software de registro é executado, ele usa o mesmo certificado durante o provisionamento automático.    

Esses artefatos de segurança são necessários durante a inscrição para registrar seu dispositivo no Serviço de Provisionamento de Dispositivos. O serviço de provisionamento fica aguardando que um desses dispositivos inicialize e se conecte com ele mais tarde. Quando o dispositivo é inicializado pela primeira vez, a lógica do SDK de cliente interage com o chip (ou simulador) para extrair os artefatos de segurança do dispositivo e verifica o registro com o Serviço de Provisionamento de Dispositivos. 

## <a name="create-the-device-registration-software"></a>Criar o software de registro do dispositivo

A última etapa é codificar um aplicativo de registro que usa o SDK de cliente do Serviço de Provisionamento de Dispositivos para registrar o dispositivo no serviço Hub IoT. 

> [!NOTE]
> Nesta etapa, vamos pressupor o uso de um dispositivo simulado feito com a execução de um aplicativo de registro de exemplo do SDK na sua estação de trabalho. No entanto, os mesmos conceitos se aplicam se você estiver criando um aplicativo de registro para implantação em um dispositivo físico. 

1. No portal do Azure, selecione a folha **Visão Geral** do Serviço de Provisionamento de Dispositivos e anote o valor de **_Escopo da ID_** . O *escopo da ID* é gerado pelo serviço e assegura a exclusividade. É imutável e é usado para identificar exclusivamente as IDs de registro.

    ![Extrair informações do ponto de extremidade do Serviço de Provisionamento de Dispositivo na folha do portal](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

1. No *Gerenciador de Soluções* do Visual Studio em seu computador, navegue até a pasta **Provisionar\_Amostras**. Selecione o projeto de exemplo chamado **prov\_dev\_client\_sample** e abra o arquivo **prov\_dev\_client\_sample.c**.

1. Atribua o valor _Escopo da ID_ obtido na etapa 1 para a variável `id_scope` (removendo os colchetes à esquerda /`[` e à direita /`]`): 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    Para referência, a variável `global_prov_uri`, que permite à API de registro de cliente do Hub IoT `IoTHubClient_LL_CreateFromDeviceAuth` se conectar à instância do Serviço de Provisionamento de Dispositivos designada.

1. Na função **main()** do mesmo arquivo, comente/remova o comentário da variável `hsm_type` que corresponde ao mecanismo de atestado que está sendo usado pelo software de registro do dispositivo (TPM ou X.509): 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

1. Salve suas alterações e recrie o exemplo **prov\_dev\_client\_sample** selecionando "Compilar solução" no menu "Compilar". 

1. Clique com o botão direito do mouse no projeto **prov\_dev\_client\_sample** na pasta **Provision\_Samples** e selecione **Definir como Projeto de Inicialização**. NÃO execute o aplicativo de exemplo agora.

> [!IMPORTANT]
> Não execute/inicie o dispositivo agora! Você precisa concluir o processo de registro do dispositivo no Serviço de Provisionamento de Dispositivos antes de iniciar o dispositivo. A seção Próximas etapas abaixo irá guiá-lo até o próximo artigo.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>APIs do SDK usadas durante o registro (apenas para referência)

Para referência, o SDK fornece as APIs a seguir para uso pelo aplicativo durante o registro. Essas APIs ajudam seu dispositivo a se conectar e se registrar no Serviço de Provisionamento de Dispositivos quando ele é inicializado. Em troca, seu dispositivo recebe as informações necessárias para estabelecer uma conexão com a instância do Hub IoT:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Você também pode chegar à conclusão de que precisa refinar seu aplicativo de registro de cliente do Serviço de Provisionamento de Dispositivos usando primeiro um dispositivo simulado e uma configuração de serviço de teste. Depois que o aplicativo está funcionando no ambiente de teste, você pode compilá-lo para o dispositivo específico e copiar o executável para a imagem do dispositivo. 

## <a name="clean-up-resources"></a>Limpar os recursos

Neste ponto, você pode ter os serviços de Provisionamento de Dispositivos e Hub IoT em execução no portal. Se você quiser abandonar a configuração de provisionamento de dispositivos e/ou atrasar a conclusão desta série de tutoriais, recomendamos desligá-los para evitar incorrer em custos desnecessários.

1. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione o serviço de Provisionamento de Dispositivos. Na parte superior da folha **Todos os recursos**, clique em **Excluir**.  
1. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione seu Hub IoT. Na parte superior da folha **Todos os recursos**, clique em **Excluir**.  

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar o SDK de cliente do Serviço de Provisionamento de Dispositivos específico da plataforma
> * Extrair os artefatos de segurança
> * Criar o software de registro do dispositivo

Avance para o próximo tutorial para aprender a provisionar o dispositivo para o Hub IoT registrando-o no Serviço de Provisionamento de Dispositivos no Hub IoT do Azure para provisionamento automático.

> [!div class="nextstepaction"]
> [Provisionar o dispositivo para o Hub IoT](tutorial-provision-device-to-hub.md)