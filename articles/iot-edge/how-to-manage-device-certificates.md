---
title: Gerenciar certificados de dispositivo-Azure IoT Edge | Microsoft Docs
description: Crie certificados de teste, instale e gerencie-os em um dispositivo Azure IoT Edge para se preparar para a implantação em produção.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e5c85d2c3049ea8718d0a9e0e574c13d0d99394c
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200269"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Gerenciar certificados em um dispositivo IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Todos os dispositivos IoT Edge usam certificados para criar conexões seguras entre o tempo de execução e os módulos em execução no dispositivo. IoT Edge dispositivos funcionando como gateways usam esses mesmos certificados para se conectar aos dispositivos downstream também.

## <a name="install-production-certificates"></a>Instalar certificados de produção

Quando você instala o IoT Edge e provisiona o dispositivo pela primeira vez, o dispositivo é configurado com certificados temporários para que você possa testar o serviço.
Esses certificados temporários expiram em 90 dias ou podem ser redefinidos reiniciando o computador.
Depois de se mover para um cenário de produção ou se desejar criar um dispositivo de gateway, você precisará fornecer seus próprios certificados.
Este artigo demonstra as etapas para instalar certificados em seus dispositivos IoT Edge.

Para saber mais sobre os diferentes tipos de certificados e suas funções, consulte [entender como o Azure IOT Edge usa certificados](iot-edge-certs.md).

>[!NOTE]
>O termo "autoridade de certificação raiz" usado em todo este artigo refere-se ao certificado público de autoridade superior da cadeia de certificados para sua solução de IoT. Você não precisa usar a raiz do certificado de uma autoridade de certificação agregada ou a raiz da autoridade de certificação de sua organização. Em muitos casos, é, na verdade, um certificado público de CA intermediário.

### <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo IoT Edge.

  Se você não tiver um dispositivo IoT Edge configurado, poderá criar um em uma máquina virtual do Azure. Siga as etapas em um dos artigos de início rápido para [criar um dispositivo Linux Virtual](quickstart-linux.md) ou [criar um dispositivo virtual do Windows](quickstart.md).

* Ter um certificado de AC (autoridade de certificação) raiz, autoassinado ou comprado de uma autoridade de certificação comercial confiável, como Baltimore, Verisign, DigiCert ou GlobalSign.

  Se você ainda não tiver uma autoridade de certificação raiz, mas quiser experimentar IoT Edge recursos que exigem certificados de produção (como cenários de gateway), poderá [criar certificados de demonstração para testar IOT Edge recursos de dispositivo](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Criar certificados de produção

Você deve usar sua própria autoridade de certificação para criar os seguintes arquivos:

* AC Raiz
* Certificado de autoridade de certificação de dispositivo
* Chave privada da AC do dispositivo

Neste artigo, o que chamamos de *CA raiz* não é a autoridade de certificação mais alta para uma organização. É a mais alta autoridade de certificação para o cenário de IoT Edge, que o IoT Edge módulo de Hub, os módulos de usuário e os dispositivos downstream usam para estabelecer a confiança entre si.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Atualmente, uma limitação no libiothsm impede o uso de certificados que expiram em 1º de janeiro de 2038.

:::moniker-end

Para ver um exemplo desses certificados, examine os scripts que criam certificados de demonstração em [gerenciando certificados de AC de teste para exemplos e tutoriais](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Instalar certificados no dispositivo

Instale a cadeia de certificados no dispositivo IoT Edge e configure o tempo de execução IoT Edge para fazer referência aos novos certificados.

Copie os três arquivos de certificado e de chave em seu dispositivo IoT Edge. Você pode usar um serviço como [Azure Key Vault](../key-vault/index.yml) ou uma função como [Protocolo de cópia segura](https://www.ssh.com/ssh/scp/) para mover os arquivos de certificado.  Se você tiver gerado os certificados no próprio dispositivo IoT Edge, poderá ignorar esta etapa e usar o caminho para o diretório de trabalho.

Por exemplo, se você usou os scripts de exemplo para [criar certificados de demonstração](how-to-create-test-certificates.md), copie os seguintes arquivos para o dispositivo IoT-Edge:

* Certificado de autoridade de certificação do dispositivo: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Chave privada da AC do dispositivo: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* AC raiz: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Abra o artigo de configuração do daemon de segurança do IoT Edge.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

1. Defina as propriedades do **certificado** em config. YAML como o caminho do URI do arquivo para o certificado e os arquivos de chave no dispositivo IOT Edge. Remova o `#` caractere antes das propriedades do certificado para remover os comentários das quatro linhas. Verifique se a linha **certificados:** não tem espaço em branco precedente e se os itens aninhados são recuados em dois espaços. Por exemplo:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///C:/<path>/<device CA cert>"
        device_ca_pk: "file:///C:/<path>/<device CA key>"
        trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///<path>/<device CA cert>"
        device_ca_pk: "file:///<path>/<device CA key>"
        trusted_ca_certs: "file:///<path>/<root CA cert>"
      ```

1. Em dispositivos Linux, verifique se o usuário **iotedge** tem permissões de leitura para o diretório que contém os certificados.

1. Se você tiver usado outros certificados para IoT Edge no dispositivo antes, exclua os arquivos nos dois diretórios a seguir antes de iniciar ou reiniciar IoT Edge:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` e `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` e `/var/lib/iotedge/hsm/cert_keys`
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Abra o arquivo de configuração do daemon de segurança do IoT Edge: `/etc/aziot/config.toml`

1. Localize o `trust_bundle_cert` parâmetro no início do arquivo. Remova a marca de comentário dessa linha e forneça o URI do arquivo para o certificado de autoridade de certificação raiz em seu dispositivo.

   ```toml
   trust_bundle_cert = "file:///<path>/<root CA cert>"
   ```

1. Localize a `[edge_ca]` seção no arquivo config. toml. Remova os comentários das linhas nesta seção e forneça os caminhos de URI de arquivo para o certificado e os arquivos de chave no dispositivo IoT Edge.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Certifique-se de que o usuário **iotedge** tenha permissões de leitura para o diretório que contém os certificados.

1. Se você tiver usado outros certificados para IoT Edge no dispositivo antes, exclua os arquivos nos dois diretórios a seguir antes de iniciar ou reiniciar IoT Edge:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1. -->
<!-- Temporarily, customizable certificate lifetime not available in 1.2. Update before GA. -->
:::moniker range="iotedge-2018-06"

## <a name="customize-certificate-lifetime"></a>Personalizar o tempo de vida do certificado

IoT Edge gera automaticamente certificados no dispositivo em vários casos, incluindo:

* Se você não fornecer seus próprios certificados de produção ao instalar e provisionar IoT Edge, o IoT Edge Security Manager gerará automaticamente um **certificado de autoridade de certificação do dispositivo**. Esse certificado autoassinado destina-se apenas a cenários de desenvolvimento e teste, não à produção. Este certificado expira após 90 dias.
* O IoT Edge Security Manager também gera um **certificado de CA de carga de trabalho** assinado pelo certificado de autoridade de certificação do dispositivo

Para obter mais informações sobre a função dos diferentes certificados em um dispositivo IoT Edge, consulte [entender como o Azure IOT Edge usa certificados](iot-edge-certs.md).

Para esses dois certificados gerados automaticamente, você tem a opção de definir o sinalizador de **auto_generated_ca_lifetime_days** no arquivo de configuração para configurar o número de dias para o tempo de vida dos certificados.

>[!NOTE]
>Há um terceiro certificado gerado automaticamente que o IoT Edge Security Manager cria, o certificado de **servidor de Hub de IOT Edge**. Esse certificado sempre tem um tempo de vida de 90 dias, mas é renovado automaticamente antes de expirar. O valor de **auto_generated_ca_lifetime_days** não afeta esse certificado.

Após a expiração após o número de dias especificado, IoT Edge precisa ser reiniciado para regenerar o certificado de autoridade de certificação do dispositivo. O certificado de autoridade de certificação do dispositivo não será renovado automaticamente.

1. Para configurar a expiração do certificado para algo diferente do padrão de 90 dias, adicione o valor em dias à seção **certificados** do arquivo de configuração.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > Atualmente, uma limitação no libiothsm impede o uso de certificados que expiram em 1º de janeiro de 2038.

1. Exclua o conteúdo da `hsm` pasta para remover todos os certificados gerados anteriormente.

   Windows: `C:\ProgramData\iotedge\hsm\certs` e `C:\ProgramData\iotedge\hsm\cert_keys` Linux: `/var/lib/iotedge/hsm/certs` e `/var/lib/iotedge/hsm/cert_keys`

1. Reinicie o serviço IoT Edge.

   Windows:

   ```powershell
   Restart-Service iotedge
   ```

   Linux:

   ```bash
   sudo systemctl restart iotedge
   ```

1. Confirme a configuração do tempo de vida.

   Windows:

   ```powershell
   iotedge check --verbose
   ```

   Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   Verifique a saída da **preparação de produção:** verificação de certificados, que lista o número de dias até que os certificados de autoridade de certificação do dispositivo gerados automaticamente expirem.

:::moniker-end
<!-- end 1.1 -->

<!-- 
<!-- 1.2 --
:::moniker range=">=iotedge-2020-11"

1. To configure the certificate expiration to something other than the default 90 days, add the value in days to the **certificates** section of the config file.

   ```toml
   [certificates]
   device_ca_cert = "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
   device_ca_pk = "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
   trusted_ca_certs = "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
   auto_generated_ca_lifetime_days = <value>
   ```

1. Delete the contents of the `certd` and `keyd` folders to remove any previously generated certificates: `/var/lib/aziot/certd/certs` `/var/lib/aziot/keyd/keys`

1. Restart IoT Edge.

   ```bash
   sudo iotedge system restart
   ```

1. Confirm the new lifetime setting.

   ```bash
   sudo iotedge check --verbose
   ```

   Check the output of the **production readiness: certificates** check, which lists the number of days until the automatically generated device CA certificates expire.
:::moniker-end
<!-- end 1.2 --
-->

## <a name="next-steps"></a>Próximas etapas

A instalação de certificados em um dispositivo IoT Edge é uma etapa necessária antes de implantar sua solução em produção. Saiba mais sobre como [se preparar para implantar sua solução de IOT Edge em produção](production-checklist.md).
