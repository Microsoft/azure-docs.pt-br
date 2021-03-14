---
title: Provisionar automaticamente dispositivos com DPS usando certificados X. 509-Azure IoT Edge | Microsoft Docs
description: Usar certificados X. 509 para testar o provisionamento automático de dispositivos para Azure IoT Edge com o serviço de provisionamento de dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 44ea6546eb2099165071fd493ec8f890820c0688
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199828"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Criar e provisionar um dispositivo IoT Edge usando certificados X. 509

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Com o [DPS (serviço de provisionamento de dispositivos) do Hub IOT do Azure](../iot-dps/index.yml), você pode provisionar automaticamente IOT Edge dispositivos usando certificados X. 509. Se você não estiver familiarizado com o processo de provisionamento automático, examine a visão geral de [provisionamento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

Este artigo mostra como criar um registro de serviço de provisionamento de dispositivos usando certificados X. 509 em um dispositivo IoT Edge com as seguintes etapas:

* Gere certificados e chaves.
* Crie um registro individual para um dispositivo ou um registro de grupo para um conjunto de dispositivos.
* Instale o IoT Edge Runtime e registre o dispositivo no Hub IoT.

Usar certificados X.509 como um mecanismo de atestado é uma maneira excelente de escalar a produção e simplificar o provisionamento do dispositivo. Normalmente, os certificados X. 509 são organizados em uma cadeia de certificados de confiança. A partir de um certificado de raiz confiável ou autoassinado, cada certificado na cadeia assina o próximo certificado inferior. Esse padrão cria uma cadeia de confiança delegada a partir do certificado raiz por meio de cada certificado intermediário para o certificado "folha" final instalado em um dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

* Um Hub IoT ativo.
* Um dispositivo físico ou virtual para ser o dispositivo IoT Edge.
* A versão mais recente do [git](https://git-scm.com/download/) instalada.
* Uma instância do serviço de provisionamento de dispositivos do Hub IoT no Azure, vinculada ao seu hub IoT.
  * Se você não tiver uma instância do serviço de provisionamento de dispositivos, siga as instruções em [Configurar o DPS do Hub IOT](../iot-dps/quick-setup-auto-provision.md).
  * Depois de executar o Serviço de Provisionamento de Dispositivo, copie o valor do **Escopo de ID** da página de visão geral. Você usa esse valor ao configurar o runtime do Azure IoT Edge.

## <a name="generate-device-identity-certificates"></a>Gerar certificados de identidade do dispositivo

O certificado de identidade do dispositivo é um certificado de folha que se conecta por meio de uma cadeia de certificados de confiança ao certificado da autoridade de certificação X. 509 (CA). O certificado de identidade do dispositivo deve ter um CN (nome comum) definido como a ID do dispositivo que você deseja que o dispositivo tenha no Hub IoT.

Os certificados de identidade do dispositivo são usados apenas para provisionar o dispositivo IoT Edge e autenticar o dispositivo com o Hub IoT do Azure. Eles não estão assinando certificados, diferentemente dos certificados de autoridade de certificação que o dispositivo IoT Edge apresenta aos módulos ou dispositivos de folha para verificação. Para obter mais informações, consulte [Azure IOT Edge detalhes de uso do certificado](iot-edge-certs.md).

Depois de criar o certificado de identidade do dispositivo, você deve ter dois arquivos: um arquivo. cer ou. pem que contém a parte pública do certificado e um arquivo. cer ou. pem com a chave privada do certificado. Se você planeja usar o registro de grupo no DPS, também precisará da parte pública de um certificado de autoridade de certificação raiz ou intermediária na mesma cadeia de certificados de confiança.

Você precisa dos seguintes arquivos para configurar o provisionamento automático com X. 509:

* O certificado de identidade do dispositivo e seu certificado de chave privada. O certificado de identidade do dispositivo será carregado no DPS se você criar um registro individual. A chave privada é passada para o tempo de execução de IoT Edge.
* Um certificado de cadeia completo, que deve ter pelo menos a identidade do dispositivo e os certificados intermediários nele. O certificado de cadeia completo é passado para o tempo de execução de IoT Edge.
* Um certificado de autoridade de certificação raiz ou intermediária da cadeia de certificados de confiança. Esse certificado será carregado no DPS se você criar um registro de grupo.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
> [!NOTE]
> Atualmente, uma limitação no libiothsm impede o uso de certificados que expiram em 1º de janeiro de 2038.

:::moniker-end

### <a name="use-test-certificates-optional"></a>Usar certificados de teste (opcional)

Se você não tiver uma autoridade de certificação disponível para criar novos certificados de identidade e quiser experimentar esse cenário, o repositório Git do Azure IoT Edge conterá scripts que você pode usar para gerar certificados de teste. Esses certificados são projetados apenas para teste de desenvolvimento e não devem ser usados na produção.

Para criar certificados de teste, siga as etapas em [criar certificados de demonstração para testar IOT Edge recursos do dispositivo](how-to-create-test-certificates.md). Conclua as duas seções necessárias para configurar os scripts de geração de certificado e para criar um certificado de autoridade de certificação raiz. Em seguida, siga as etapas para criar um certificado de identidade do dispositivo. Quando tiver terminado, você deverá ter a cadeia de certificados e o par de chaves a seguir:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Você precisa desses dois certificados no dispositivo IoT Edge. Se você for usar o registro individual no DPS, você carregará o arquivo. cert. PEM. Se você pretende usar o registro de grupo no DPS, também precisará de um certificado de CA raiz ou intermediário na mesma cadeia de certificados de confiança a ser carregada. Se você estiver usando certificados de demonstração, use o `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` certificado para registro de grupo.

## <a name="create-a-dps-individual-enrollment"></a>Criar um registro individual de DPS

Use seus certificados e chaves gerados para criar um registro individual no DPS para um único dispositivo de IoT Edge. Os registros individuais usam a parte pública do certificado de identidade de um dispositivo e correspondem ao certificado no dispositivo.

Se você estiver procurando provisionar vários dispositivos IoT Edge, siga as etapas na próxima seção, [criar um registro de grupo de DPS](#create-a-dps-group-enrollment).

Ao criar uma inscrição no DPS, tem a oportunidade de declarar um **Estado inicial do dispositivo duplo**. No dispositivo gêmeo, você pode definir tags para agrupar dispositivos por qualquer métrica que precisar em sua solução, como região, ambiente, local ou tipo de dispositivo. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-at-scale.md).

Para obter mais informações sobre registros no serviço de provisionamento de dispositivos, consulte [como gerenciar registros de dispositivo](../iot-dps/how-to-manage-enrollments.md).

   > [!TIP]
   > No CLI do Azure, você pode criar um [registro](/cli/azure/ext/azure-iot/iot/dps/enrollment) ou um [grupo de registro](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) e usar o sinalizador **habilitado para borda** para especificar que um dispositivo ou grupo de dispositivos é um dispositivo IOT Edge.

1. Na [portal do Azure](https://portal.azure.com), navegue até sua instância do serviço de provisionamento de dispositivos do Hub IOT.

1. Em **Configurações**, selecione **Gerenciar registros**.

1. Selecione **adicionar registro individual**, em seguida, conclua as seguintes etapas para configurar o registro:  

   * **Mecanismo**: selecione **X. 509**.

   * **Arquivo. PEM ou. cer do certificado primário**: carregue o arquivo público do certificado de identidade do dispositivo. Se você usou os scripts para gerar um certificado de teste, escolha o seguinte arquivo:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **ID do dispositivo do Hub IOT**: forneça uma ID para seu dispositivo, se desejar. Você pode usar IDs de dispositivo para um dispositivo individual para a implantação do módulo de destino. Se você não fornecer uma ID de dispositivo, o nome comum (CN) no certificado X. 509 será usado.

   * **IOT Edge dispositivo**: selecione **true** para declarar que o registro é para um dispositivo IOT Edge.

   * **Selecione os hubs IOT aos quais este dispositivo pode ser atribuído**: escolha o Hub IOT vinculado ao qual você deseja conectar o dispositivo. Você pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de alocação selecionada.

   * **Estado inicial do dispositivo** r: Adicione um valor de marca a ser adicionado ao dispositivo "d" se desejar. Você pode usar marcas para direcionar grupos de dispositivos para implantação automática. Por exemplo:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Selecione **Salvar**.

Agora que um registro existe para esse dispositivo, o tempo de execução do IoT Edge pode provisionar automaticamente o dispositivo durante a instalação. Continue na seção [instalar o IOT Edge Runtime](#install-the-iot-edge-runtime) para configurar o dispositivo de IOT Edge.

## <a name="create-a-dps-group-enrollment"></a>Criar um registro de grupo de DPS

Use seus certificados e chaves gerados para criar um registro de grupo no DPS para vários dispositivos IoT Edge. Os registros de grupo usam um certificado de CA raiz ou intermediário da cadeia de certificados de confiança usada para gerar os certificados de identidade de dispositivo individual.

Se você estiver procurando provisionar um único dispositivo IoT Edge em vez disso, siga as etapas na seção anterior, [criar um registro individual de DPS](#create-a-dps-individual-enrollment).

Ao criar uma inscrição no DPS, tem a oportunidade de declarar um **Estado inicial do dispositivo duplo**. No dispositivo gêmeo, você pode definir tags para agrupar dispositivos por qualquer métrica que precisar em sua solução, como região, ambiente, local ou tipo de dispositivo. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-at-scale.md).

### <a name="verify-your-root-certificate"></a>Verifique seu certificado raiz

Ao criar um grupo de registros, você tem a opção de usar um certificado verificado. Você pode verificar um certificado com o DPS, comprovando que você tem a propriedade do certificado raiz. Para obter mais informações, consulte [como fazer a prova de posse para certificados de AC X. 509](../iot-dps/how-to-verify-certificates.md).

1. Na [portal do Azure](https://portal.azure.com), navegue até sua instância do serviço de provisionamento de dispositivos do Hub IOT.

1. Selecione **certificados** no menu à esquerda.

1. Selecione **Adicionar** para adicionar um novo certificado.

1. Insira um nome amigável para seu certificado e, em seguida, navegue até o arquivo. cer ou. pem que representa a parte pública do seu certificado X. 509.

   Se você estiver usando os certificados de demonstração, carregue o `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` certificado.

1. Selecione **Salvar**.

1. O certificado agora deve estar listado na página **certificados** . Selecione-o para abrir os detalhes do certificado.

1. Selecione **gerar código de verificação** e, em seguida, copie o código gerado.

1. Se você trouxer seu próprio certificado de autoridade de certificação ou estiver usando os certificados de demonstração, poderá usar a ferramenta de verificação fornecida no repositório IoT Edge para verificar a prova de posse. A ferramenta de verificação usa seu certificado de autoridade de certificação para assinar um novo certificado com o código de verificação fornecido como o nome da entidade.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Na mesma página de detalhes do certificado na portal do Azure, carregue o certificado de verificação gerado recentemente.

1. Selecione **Verificar**.

### <a name="create-enrollment-group"></a>Criar grupo de registros

Para obter mais informações sobre registros no serviço de provisionamento de dispositivos, consulte [como gerenciar registros de dispositivo](../iot-dps/how-to-manage-enrollments.md).

1. Na [portal do Azure](https://portal.azure.com), navegue até sua instância do serviço de provisionamento de dispositivos do Hub IOT.

1. Em **Configurações**, selecione **Gerenciar registros**.

1. Selecione **Adicionar grupo de registro** e conclua as seguintes etapas para configurar o registro:

   * **Nome do grupo**: forneça um nome fácil de memorizar para este registro de grupo.

   * **Tipo de atestado**: selecione **certificado**.

   * **IOT Edge dispositivo**: selecione **verdadeiro**. Para um registro de grupo, todos os dispositivos devem ser IoT Edge dispositivos ou nenhum deles pode ser.

   * **Tipo de certificado**: selecione **certificado de autoridade** de certificação se você tiver um certificado de autoridade de certificação verificado armazenado com DPS ou **certificado intermediário** se quiser carregar um novo arquivo apenas para esse registro.

   * **Certificado principal**: se você escolheu certificado de autoridade de certificação na última seção, escolha seu certificado na lista suspensa. Se você escolheu certificado intermediário, carregue o arquivo público de um certificado de autoridade de certificação na cadeia de certificados de confiança que foi usada para gerar os certificados de identidade do dispositivo.

   * **Selecione os hubs IOT aos quais este dispositivo pode ser atribuído**: escolha o Hub IOT vinculado ao qual você deseja conectar o dispositivo. Você pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de alocação selecionada.

   * **Estado inicial do dispositivo** r: Adicione um valor de marca a ser adicionado ao dispositivo "d" se desejar. Você pode usar marcas para direcionar grupos de dispositivos para implantação automática. Por exemplo:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Selecione **Salvar**.

Agora que um registro existe para esse dispositivo, o tempo de execução do IoT Edge pode provisionar automaticamente o dispositivo durante a instalação. Continue na próxima seção para configurar o dispositivo IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do Azure IoT Edge

O runtime do IoT Edge é implantado em todos os dispositivos IoT Edge. Seus componentes são executados em contêineres e permitem implantar contêineres adicionais no dispositivo para que você possa executar o código na borda.

Siga as etapas em [instalar o Azure IOT Edge Runtime](how-to-install-iot-edge.md)e, em seguida, retorne a este artigo para provisionar o dispositivo.

O provisionamento X. 509 com o DPS só tem suporte no IoT Edge versão 1.0.9 ou mais recente.

## <a name="configure-the-device-with-provisioning-information"></a>Configurar o dispositivo com informações de provisionamento

Depois que o tempo de execução for instalado em seu dispositivo, configure o dispositivo com as informações que ele usa para se conectar ao serviço de provisionamento de dispositivos e ao Hub IoT.

Tenha as seguintes informações prontas:

* O valor de **escopo da ID** de DPS. Você pode recuperar esse valor da página Visão geral da instância do DPS no portal do Azure.
* O arquivo de cadeia de certificados de identidade do dispositivo no dispositivo.
* O arquivo de chave de identidade do dispositivo no dispositivo.
* Uma ID de registro opcional. Se não for fornecido, a ID será extraída do nome comum no certificado de identidade do dispositivo.

### <a name="linux-device"></a>Dispositivo Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Abra o arquivo de configuração no dispositivo IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Localize a seção Configurações de provisionamento do arquivo. Remova os comentários das linhas para o provisionamento de certificado DPS X. 509 e verifique se todas as outras linhas de provisionamento foram comentadas.

   A `provisioning:` linha não deve ter nenhum espaço em branco anterior e itens aninhados devem ser recuados em dois espaços.

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Atualize os valores de `scope_id` , `identity_cert` e `identity_pk` com suas informações de dispositivo e DPS.

   Quando você adiciona o certificado X. 509 e as informações de chave ao arquivo config. YAML, os caminhos devem ser fornecidos como URIs de arquivo. Por exemplo:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. Opcionalmente, forneça um `registration_id` para o dispositivo. Caso contrário, deixe essa linha comentada para registrar o dispositivo com o nome CN do certificado de identidade.

1. Opcionalmente, use as `always_reprovision_on_startup` `dynamic_reprovisioning` linhas ou para configurar o comportamento de reprovisionamento do seu dispositivo. Se um dispositivo estiver configurado para reprovisionar na inicialização, ele sempre tentará provisionar com o DPS primeiro e, em seguida, retornará para o backup de provisionamento se isso falhar. Se um dispositivo estiver definido para reprovisionar dinamicamente, IoT Edge será reiniciado e reprovisionado se um evento de reprovisionamento for detectado. Para obter mais informações, consulte [conceitos de reprovisionamento de dispositivo do Hub IOT](../iot-dps/concepts-device-reprovision.md).

1. Salve e feche o arquivo config. YAML.

1. Reinicie o runtime do IoT Edge para que ele pega todas as alterações de configuração feitas no dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Crie um arquivo de configuração para seu dispositivo com base em um arquivo de modelo fornecido como parte da instalação do IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Abra o arquivo de configuração no dispositivo IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Localize a seção **Provisionamento** do arquivo. Remova a marca de comentário das linhas para o provisionamento de DPS com o certificado X. 509 e verifique se todas as outras linhas de provisionamento foram comentadas.

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   # registration_id = "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"

   identity_cert = "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

1. Atualize os valores de `id_scope` , `identity_cert` e `identity_pk` com suas informações de dispositivo e DPS.

   O valor do certificado de identidade pode ser fornecido como um URI de arquivo ou pode ser emitido dinamicamente usando a EST ou uma autoridade de certificação local. Remova a marca de comentário apenas de uma linha, com base no formato que você escolher para usar.

   O valor da chave privada de identidade pode ser fornecido como um URI de arquivo ou um URI PKCS # 11. Remova a marca de comentário apenas de uma linha, com base no formato que você escolher para usar.

   Se você usar qualquer URI do PKCS # 11, localize a seção **PKCS # 11** no arquivo de configuração e forneça informações sobre a configuração PKCS # 11.

1. Opcionalmente, forneça um `registration_id` para o dispositivo. Caso contrário, deixe essa linha comentada para registrar o dispositivo com o nome comum do certificado de identidade.

1. Salve e feche o arquivo.

1. Aplique as alterações de configuração feitas ao IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Dispositivo Windows

1. Abra uma janela do PowerShell no modo de administrador. Certifique-se de usar uma sessão AMD64 do PowerShell ao instalar o IoT Edge, não o PowerShell (x86).

1. O comando **Initialize-IoTEdge** configura o runtime do IoT Edge em seu computador. O comando usa como padrão o provisionamento manual com contêineres do Windows; portanto, use o `-DpsX509` sinalizador para usar o provisionamento automático com a autenticação de certificado X. 509.

   Substitua os valores de espaço reservado para `{scope_id}` , `{identity cert chain path}` e `{identity key path}` pelos valores apropriados de sua instância de DPS e os caminhos de arquivo em seu dispositivo.

   Adicione o `-RegistrationId {registration_id}` se você quiser definir a ID do dispositivo como algo diferente do nome CN do certificado de identidade.

   Adicione o `-ContainerOs Linux` parâmetro se você estiver usando contêineres do Linux no Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >O arquivo de configuração armazena suas informações de certificado e chave como URIs de arquivo. No entanto, o comando Initialize-IoTEdge manipula essa etapa de formatação para você, para que você possa fornecer o caminho absoluto para o certificado e os arquivos de chave em seu dispositivo.

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Se o runtime foi iniciado com êxito, você pode entrar em seu Hub IoT e iniciar a implantação de módulos do IoT Edge em seu dispositivo.

Você pode verificar se o registro individual criado no serviço de provisionamento de dispositivos foi usado. Navegue até a instância do serviço de provisionamento de dispositivos no portal do Azure. Abra os detalhes de registro para o registro individual que você criou. Observe que o status do registro é **atribuído** e a ID do dispositivo é listada.

Use os seguintes comandos em seu dispositivo para verificar o runtime instalado e iniciado com êxito.

### <a name="linux-device"></a>Dispositivo Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Verifique o status do serviço do IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine os logs de serviço.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Módulos de execução da lista.

```cmd/sh
iotedge list
```
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Verifique o status do serviço do IoT Edge.

```cmd/sh
sudo iotedge system status
```

Examine os logs de serviço.

```cmd/sh
sudo iotedge system logs
```

Módulos de execução da lista.

```cmd/sh
sudo iotedge list
```
:::moniker-end

### <a name="windows-device"></a>Dispositivo Windows

Verifique o status do serviço do IoT Edge.

```powershell
Get-Service iotedge
```

Examine os logs de serviço.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Módulos de execução da lista.

```powershell
iotedge list
```

## <a name="next-steps"></a>Próximas etapas

O processo de registro do serviço de provisionamento de dispositivo permite definir a ID do dispositivo e as marcas do dispositivo gêmeo ao mesmo tempo, como provisionar o novo dispositivo. Você pode usar esses valores para dispositivos individuais ou grupos de dispositivos usando o gerenciamento automático de dispositivo de destino. Saiba como [implantar e monitorar módulos IOT Edge em escala usando o portal do Azure](how-to-deploy-at-scale.md) ou [usando CLI do Azure](how-to-deploy-cli-at-scale.md).
