---
title: Registrar um novo dispositivo-Azure IoT Edge | Microsoft Docs
description: Registrar um único dispositivo de IoT Edge no Hub IoT para o provisionamento manual com chaves simétricas ou certificados X. 509
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: d75f184a324a9d418b0af2e3cf5790205af0fa42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200711"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>Registrar um dispositivo de IoT Edge no Hub IoT

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Este artigo fornece as etapas para registrar um novo dispositivo IoT Edge no Hub IoT.

Cada dispositivo que se conecta a um hub IoT tem uma ID de dispositivo que é usada para controlar as comunicações da nuvem para o dispositivo ou do dispositivo para a nuvem. Você configura um dispositivo com suas informações de conexão, o que inclui o nome de host do Hub IoT, a ID do dispositivo e as informações que o dispositivo usa para autenticar no Hub IoT.

As etapas neste artigo percorrem um processo chamado provisionamento manual, no qual você conecta um único dispositivo ao seu hub IoT. Para o provisionamento manual, você tem duas opções para autenticar dispositivos IoT Edge:

* **Chave simétrica**: quando você cria uma nova identidade de dispositivo no Hub IOT, o serviço cria duas chaves. Você coloca uma das chaves no dispositivo e apresenta a chave para o Hub IoT durante a autenticação.

  Esse método de autenticação é mais rápido para começar, mas não tão seguro.

* **X. 509 auto-assinado**: você cria dois certificados de identidade x. 509 e os coloca no dispositivo. Ao criar uma nova identidade de dispositivo no Hub IoT, você fornece impressões digitais de ambos os certificados. Quando o dispositivo é autenticado no Hub IoT, ele apresenta um certificado e o Hub IoT verifica se o certificado corresponde à sua impressão digital.

  Esse método de autenticação é mais seguro e recomendado para cenários de produção.

Este artigo aborda os dois métodos de autenticação.

Se você tiver muitos dispositivos a serem configurados e não quiser provisionar manualmente cada um deles, use um dos artigos a seguir para saber como IoT Edge funciona com o serviço de provisionamento de dispositivos no Hub IoT:

* [Criar e provisionar dispositivos IoT Edge usando certificados X. 509](how-to-auto-provision-x509-certs.md)
* [Criar e provisionar dispositivos IoT Edge com um TPM](how-to-auto-provision-simulated-device-linux.md)
* [Criar e provisionar dispositivos IoT Edge usando chaves simétricas](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)

Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou padrão na assinatura do Azure.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou Standard em sua assinatura do Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas de IoT do Microsoft Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

* Um [hub IoT](../iot-hub/iot-hub-create-using-cli.md) gratuito ou padrão na assinatura do Azure.
* [CLI do Azure](/cli/azure/install-azure-cli) em seu ambiente. No mínimo, sua versão de CLI do Azure deve ser 2.0.70 ou mais recente. Use `az --version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack.

---

## <a name="option-1-register-with-symmetric-keys"></a>Opção 1: registrar com chaves simétricas

Você pode usar várias ferramentas para registrar um novo dispositivo IoT Edge no Hub IoT e recuperar sua cadeia de conexão, dependendo de sua preferência.

# <a name="portal"></a>[Portal](#tab/azure-portal)

No Hub IoT na portal do Azure, os dispositivos IoT Edge são criados e gerenciados separadamente dos dispositivos IoT que não estão habilitados para a borda.

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.

1. No painel esquerdo, selecione **IOT Edge** no menu e, em seguida, selecione **Adicionar um dispositivo de IOT Edge**.

   ![Adicionar um dispositivo de IoT Edge da portal do Azure](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Na página **criar um dispositivo** , forneça as seguintes informações:

   * Crie uma ID de dispositivo descritiva.
   * Selecione **Chave simétrica** como o tipo de autenticação.
   * Use as configurações padrão para gerar automaticamente as chaves de autenticação e conectar o novo dispositivo ao hub.

1. Clique em **Salvar**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o Hub IoT

É possível usar as extensões do Azure IoT para Visual Studio Code para executar operações com o Hub IoT. Para que essas operações funcionem, você precisa entrar na sua conta do Azure e selecionar o Hub.

1. No Visual Studio Code, abra a exibição do **Explorer**.
1. Na parte inferior do Explorer, expanda a seção **Hub IoT**.

   ![Expanda a seção de Dispositivos do Azure Hub IoT](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Clique em **...** no cabeçalho da seção **Hub IoT**. Se você não visualizar as reticências, clique ou passe o mouse sobre o cabeçalho.
1. Escolha **Selecionar Hub IoT**.
1. Se não estiver conectado à conta do Azure, siga as instruções para fazer isso.
1. Selecione sua assinatura do Azure.
1. Selecione seu Hub IoT.

### <a name="register-a-new-device-with-visual-studio-code"></a>Registrar um novo dispositivo com o Visual Studio Code

1. No Visual Studio Code Explorer, expanda a seção **Hub IOT do Azure** .
1. Clique em **...** no cabeçalho da seção **Hub IoT**. Se você não visualizar as reticências, clique ou passe o mouse sobre o cabeçalho.
1. Selecione **Criar dispositivo IoT Edge**.
1. Na caixa de texto que abrir, dê a seu dispositivo um ID.

Na tela de saída, você verá o resultado do comando. A informação do dispositivo é impressa, o que inclui o **deviceId** que você forneceu e o **connectionString** que você consegue usar para conectar seu dispositivo físico ao Hub IoT.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) para criar uma nova identidade de dispositivo no hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Este comando inclui três parâmetros:

* `--device-id` ou `-d` : forneça um nome descritivo que seja exclusivo no Hub IOT.
* `--hub-name` ou `-n` : forneça o nome do seu hub IOT.
* `--edge-enabled` ou `--ee` : declare que o dispositivo é um dispositivo IOT Edge.

   ![Saída da criação de identidade do dispositivo de hub IoT do Azure](./media/how-to-register-device/create-edge-device-cli.png)

---

Agora que você tem um dispositivo registrado no Hub IoT, recupere a cadeia de conexão que você usa para concluir a instalação e o provisionamento do tempo de execução de IoT Edge. Siga as etapas mais adiante neste artigo para [exibir os dispositivos registrados e recuperar as cadeias de conexão](#view-registered-devices-and-retrieve-connection-strings).

## <a name="option-2-register-with-x509-certificates"></a>Opção 2: registrar com certificados X. 509

O provisionamento manual com certificados X. 509 requer IoT Edge versão 1.0.10 ou mais recente.

Para a autenticação de certificado X. 509, as informações de autenticação de cada dispositivo são fornecidas na forma de *impressões digitais* obtidas dos certificados de identidade do dispositivo. Essas impressões digitais são dadas ao Hub IoT no momento do registro do dispositivo para que o serviço possa reconhecer o dispositivo quando ele se conectar.

### <a name="create-certificates-and-thumbprints"></a>Criar certificados e impressões digitais

Ao provisionar um dispositivo IoT Edge com certificados X. 509, você usa o que é chamado de *certificado de identidade do dispositivo*. Esse certificado é usado apenas para provisionar um dispositivo IoT Edge e autenticar o dispositivo com o Hub IoT do Azure. É um certificado de folha que não assina outros certificados. O certificado de identidade do dispositivo é separado dos certificados de AC (autoridade de certificação) que o dispositivo IoT Edge apresenta aos módulos ou dispositivos downstream para verificação. Para obter mais informações sobre como os certificados de autoridade de certificação são usados em dispositivos IoT Edge, consulte [entender como o Azure IOT Edge usa certificados](iot-edge-certs.md).

Você precisa dos seguintes arquivos para o provisionamento manual com X. 509:

* Dois certificados de identidade de dispositivo com seus certificados de chave privada correspondentes nos formatos. cer ou. PEM.

  Um conjunto de arquivos de certificado/chave é fornecido para o tempo de execução de IoT Edge. Ao criar certificados de identidade do dispositivo, defina o nome comum do certificado (CN) com a ID do dispositivo que você deseja que o dispositivo tenha no Hub IoT.

* Impressões digitais obtidas de ambos os certificados de identidade do dispositivo.

  Os valores de impressão digital são caracteres 40-hex para hashes SHA-1 ou caracteres 64-hex para hashes SHA-256. As impressões digitais são fornecidas ao Hub IoT no momento do registro do dispositivo.

Se você não tiver certificados disponíveis, poderá [criar certificados de demonstração para testar IOT Edge recursos do dispositivo](how-to-create-test-certificates.md). Siga as instruções neste artigo para configurar scripts de criação de certificado, criar um certificado de autoridade de certificação raiz e, em seguida, criar dois IoT Edge certificados de identidade de dispositivo.

Uma maneira de recuperar a impressão digital de um certificado é com o seguinte comando openssl:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Registrar um novo dispositivo

Você pode usar várias ferramentas para registrar um novo dispositivo IoT Edge no Hub IoT e carregar suas impressões digitais de certificado.

# <a name="portal"></a>[Portal](#tab/azure-portal)

No Hub IoT na portal do Azure, os dispositivos IoT Edge são criados e gerenciados separadamente dos dispositivos IoT que não estão habilitados para a borda.

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.

1. No painel esquerdo, selecione **IOT Edge** no menu e, em seguida, selecione **Adicionar um dispositivo de IOT Edge**.

   ![Adicionar um dispositivo de IoT Edge da portal do Azure](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Na página **criar um dispositivo** , forneça as seguintes informações:

   * Crie uma ID de dispositivo descritiva. Anote essa ID do dispositivo, pois você a usará na próxima seção.
   * Selecione **X.509 autoassinado** como o tipo de autenticação.
   * Forneça as impressões digitais do certificado de identidade primário e secundário. Os valores de impressão digital são caracteres 40-hex para hashes SHA-1 ou caracteres 64-hex para hashes SHA-256.

1. Clique em **Salvar**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Atualmente, a extensão do Azure IoT para Visual Studio Code não dá suporte ao registro de dispositivo com certificados X. 509.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) para criar uma nova identidade de dispositivo no hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Esse comando inclui vários parâmetros:

* `--device-id` ou `-d` : forneça um nome descritivo que seja exclusivo para o Hub IOT. Anote essa ID do dispositivo, pois você a usará na próxima seção.
* `hub-name` ou `-n` : forneça o nome do seu hub IOT.
* `--edge-enabled` ou `--ee` : declare que o dispositivo é um dispositivo IOT Edge.
* `--auth-method` ou `--am` : declare o tipo de autorização que o dispositivo vai usar. Nesse caso, estamos usando as impressões digitais do certificado X. 509.
* `--primary-thumbprint` ou `--ptp` : forneça uma impressão digital do certificado X. 509 para usar como chave primária.
* `--secondary-thumbprint` ou `--stp` : forneça uma impressão digital do certificado X. 509 para usar como uma chave secundária.

---

Agora que você tem um dispositivo registrado no Hub IoT, você está pronto para instalar e provisionar o tempo de execução de IoT Edge em seu dispositivo. IoT Edge dispositivos que se autenticam com certificados X. 509 não usam cadeias de conexão, portanto, você pode continuar para a próxima etapa:

* [Instalar ou desinstalar o Azure IoT Edge para Linux](how-to-install-iot-edge.md)
* [Instalar ou desinstalar o Azure IoT Edge para Windows](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Exibir dispositivos registrados e recuperar cadeias de conexão

Os dispositivos que usam a autenticação de chave simétrica precisam de suas cadeias de conexão para concluir a instalação e o provisionamento do tempo de execução de IoT Edge.

Os dispositivos que usam a autenticação de certificado X. 509 não precisam de cadeias de conexão. Em vez disso, esses dispositivos precisam de seu nome de Hub IoT, seu nome de dispositivo e seus arquivos de certificado para concluir a instalação e o provisionamento do tempo de execução de IoT Edge.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Todos os habilitado borda dispositivos que se conectam ao seu hub IoT são listados no **IoT Edge** página.

![Usar o portal do Azure para exibir todos os dispositivos de IoT Edge em seu hub IoT](./media/how-to-register-device/portal-view-devices.png)

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT.

Os dispositivos que se autenticam com chaves simétricas têm suas cadeias de conexão disponíveis para cópia no Portal.

1. Na página **IoT Edge** no portal, clique na ID de dispositivo na lista de dispositivos do IoT Edge.
2. Copie o valor da **Cadeia de conexão primária** ou **Cadeia de conexão secundária**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Exibir dispositivos do IoT Edge com o Visual Studio Code

Todos os dispositivos que conectam ao seu Hub IoT estão listados na seção **Azure IoT Hub** do Visual Studio Code Explorer. Os dispositivos do IoT Edge são distinguidos dos dispositivos que não são de borda por um ícone diferente e pelo fato de que os módulos **$edgeAgent** e **$edgeHub** são implantados em cada dispositivo do IoT Edge.

![Usar VS Code para exibir todos os dispositivos de IoT Edge em seu hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recuperar a cadeia de conexão com o Visual Studio Code

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT.

1. Clique com o botão direito na ID do seu dispositivo na seção **Hub IoT**.
1. Selecione **Copiar cadeia de conexão de dispositivo**.

   A cadeia de conexão é copiada à sua área de transferência.

Você também pode selecionar **Pegar Informação do Dispositivo** do menu de botão direito para ver todas as informações do dispositivo, incluindo a cadeia de conexão, na janela de saída.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Exibir dispositivos do IoT Edge com a CLI do Azure

Use o comando [az iot hub device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) para exibir todos os dispositivos no hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Qualquer dispositivo registrado como um dispositivo do IoT Edge terá a propriedade **capabilities.iotEdge** definida para **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recuperar a cadeia de conexão com a CLI do Azure

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT. Use o comando [AZ IOT Hub Device-identidade Connection-String show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) para retornar a cadeia de conexão para um único dispositivo:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>O `connection-string show` comando foi introduzido na versão 0.9.8 da extensão do Azure IOT, substituindo o comando preterido `show-connection-string` . Se você receber um erro ao executar esse comando, verifique se a versão da extensão está atualizada para 0.9.8 ou posterior. Para obter mais informações e as atualizações mais recentes, consulte [Microsoft Azure extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

O valor para o parâmetro `device-id` diferencia maiúsculas de minúsculas.

Ao copiar a cadeia de conexão a ser usada em um dispositivo, não inclua as aspas em volta da cadeia de conexão.

---

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo registrado no Hub IoT, você está pronto para instalar e provisionar o tempo de execução de IoT Edge em seu dispositivo.

* [Instalar ou desinstalar o Azure IoT Edge para Linux](how-to-install-iot-edge.md)
* [Instalar ou desinstalar o Azure IoT Edge para Windows](how-to-install-iot-edge-windows-on-windows.md)