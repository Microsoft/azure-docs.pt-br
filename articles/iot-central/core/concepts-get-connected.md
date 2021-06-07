---
title: Conectividade de dispositivo no Azure IoT Central | Microsoft Docs
description: Este artigo apresenta os principais conceitos relacionados à conectividade de dispositivo no Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 1/15/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: c365f367a090f1697b71c51f24679b9ea09561d0
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490005"
---
# <a name="get-connected-to-azure-iot-central"></a>Conectar-se ao Azure IoT Central

*Este artigo se aplica a operadores e desenvolvedores de dispositivos.*

Veja neste artigo como um dispositivo se conecta a um aplicativo do Azure IoT Central. Antes de um dispositivo poder trocar dados com o IoT Central, ele precisa:

- *Autenticar*. A autenticação com o aplicativo IoT Central usa um _token de SAS (Assinatura de Acesso Compartilhado)_ ou um _certificado X.509_. Os certificados X.509 são recomendados para ambientes de produção.
- *Registrar*. Os dispositivos precisam ser registrados com o aplicativo IoT Central. É possível ver os dispositivos registrados na página **Dispositivos** do aplicativo.
- *Associar a um modelo de dispositivo*. Em um aplicativo IoT Central, os modelos de dispositivo definem a interface do usuário que os operadores usam para ver e gerenciar os dispositivos conectados.

O IoT Central é compatível com os dois cenários de registro de dispositivo seguintes:

- *Registro automático*. O dispositivo é registrado automaticamente quando se conecta pela primeira vez. Nesse cenário, os OEMs fabricam dispositivos em massa que podem ser conectados sem precisar de um registro anterior. Um OEM gera credenciais de dispositivo adequadas e configura os dispositivos na fábrica. Outra opção é exigir que um operador aprove o dispositivo antes que ele comece a enviar dados. Nesse cenário, é preciso configurar um _registro de grupo_ X.509 ou SAS no aplicativo.
- *Registro manual*. Os operadores registram cada dispositivo na página **Dispositivos** ou [importam um arquivo CSV](howto-manage-devices.md#import-devices) para o registro em massa de dispositivos. Nesse cenário, é possível usar o _registro de grupo_ X.509 ou SAS ou o _registro individual_ X.509 ou SAS.

Os dispositivos que se conectam ao IoT Central devem seguir as *convenções do IoT Plug and Play*. Uma dessas convenções é que um dispositivo precisa enviar a _ID de modelo_ do dispositivo que ele implementa quando se conecta. A ID do modelo permite que o aplicativo IoT Central associe o dispositivo ao modelo de dispositivo correto.

O IoT Central usa o [DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT](../../iot-dps/about-iot-dps.md) para gerenciar o processo de conexão. Primeiro, o dispositivo se conecta a um ponto de extremidade do DPS para recuperar as informações necessárias para se conectar ao seu aplicativo. Internamente, seu aplicativo IoT Central usa um hub IoT para tratar da conectividade do dispositivo. O uso do DPS permite que:

- O IoT Central dê suporte à integração e à conexão de dispositivos em escala.
- Você gere credenciais de dispositivo e configure os dispositivos offline sem registrar os dispositivos por meio da interface do usuário do IoT Central.
- Você use as próprias identificações do dispositivo para registrar dispositivos no IoT Central. O uso das próprias identificações do dispositivo simplifica a integração aos sistemas de back-office existentes.
- Uma só maneira consistente de conectar dispositivos ao IoT Central.

Este artigo descreve as seguintes etapas de conexão de dispositivo:

- [Registro de grupo X.509](#x509-group-enrollment)
- [Registro de grupo SAS](#sas-group-enrollment)
- [Registro individual](#individual-enrollment)
- [Registro de dispositivos](#device-registration)
- [Associar um dispositivo a um modelo de dispositivo](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>Registro de grupo X.509

Em um ambiente de produção, o uso de certificados X.509 é o mecanismo de autenticação de dispositivos recomendado para o IoT Central. Para saber mais, consulte [Autenticação de dispositivo usando certificados de AC X.509](../../iot-hub/iot-hub-x509ca-overview.md).

Para conectar um dispositivo com um certificado X.509 ao aplicativo:

1. Crie um *grupo de registro* que usa o tipo de atestado **Certificados (X.509)** .
1. Adicione e verifique um certificado X.509 intermediário ou raiz no grupo de registro.
1. Gere um certificado secundário com base no certificado raiz ou intermediário no grupo de registro. Envie o certificado secundário do dispositivo quando ele se conectar ao aplicativo.

Para saber mais, confira [Como se conectar a dispositivos com certificados X.509](how-to-connect-devices-x509.md)

### <a name="for-testing-purposes-only"></a>Apenas para fins de teste

Apenas para testes, você pode usar os utilitários a seguir para gerar certificados raiz, intermediários e de dispositivo:

- [Ferramentas para o SDK do dispositivo de provisionamento de dispositivos IoT do Azure](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): uma coleção de ferramentas do Node.js que você pode usar para gerar e verificar certificados e chaves X.509.
- Se você estiver usando um dispositivo DevKit, essa [ferramenta de linha de comando](https://aka.ms/iotcentral-docs-dicetool) gerará um certificado de Autoridade de Certificação que você pode adicionar ao seu aplicativo IoT Central para verificar os certificados.
- [Gerenciar certificados de Autoridade de Certificação de teste para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): uma coleção de scripts do PowerShell e Bash para:
  - Criar uma cadeia de certificados.
  - Salve os certificados como arquivos .cer para carregar em seu aplicativo IoT Central.
  - Use o código de verificação do aplicativo IoT Central para gerar o certificado de verificação.
  - Crie certificados de folha para seus dispositivos usando suas IDs de dispositivo como um parâmetro para a ferramenta.

## <a name="sas-group-enrollment"></a>Registro de grupo SAS

Para conectar um dispositivo com a chave SAS do dispositivo ao aplicativo:

1. Crie um *grupo de registro* que usa o tipo de atestado **SAS (Assinatura de Acesso Compartilhado)** .
1. Copie, no grupo de registro, a chave primária ou secundária do grupo.
1. Use a CLI do Azure para gerar uma chave de dispositivo com base na chave de grupo:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Use a chave de dispositivo gerada quando o dispositivo se conectar ao aplicativo IoT Central.

## <a name="individual-enrollment"></a>Registro individual

Para clientes que conectam dispositivos com as próprias credenciais de autenticação, use registros individuais. Um registro individual é uma entrada para um só dispositivo que tem permissão para se conectar. Os registros individuais podem usar certificados X.509 de folha ou tokens SAS (de um Trusted Platform Module físico ou virtual) como mecanismos de certificação. Uma identificação do dispositivo pode conter letras, números e o caractere `-`. Para obter mais informações, consulte [Registro individual do DPS](../../iot-dps/concepts-service.md#individual-enrollment).

> [!NOTE]
> Ao criar um registro individual para um dispositivo, ele tem precedência sobre as opções de registro de grupo padrão em seu aplicativo IoT Central.

### <a name="create-individual-enrollments"></a>Criar registros individuais

O IoT Central tem suporte para os seguintes mecanismos de atestado de registros individuais:

- **Atestado de chave simétrica:** O atestado de chave simétrica é uma abordagem simples para autenticar o dispositivo com a instância do DPS. Para criar um registro individual que usa chaves simétricas, abra a página **Conexão de dispositivo**, selecione **Registro individual** como o método de conexão e **SAS (Assinatura de Acesso Compartilhado)** como o mecanismo. Insira as chaves primária e secundária codificadas da base64 e salve as alterações. Use o **escopo da ID**, a **ID do dispositivo** e a chave primária ou secundária para conectar seu dispositivo.

    > [!TIP]
    > Para fazer testes, você pode usar o **OpenSSL** para gerar chaves base64 codificadas: `openssl rand -base64 64`

- **Certificados X.509:** Para criar um registro individual com certificados X.509, abra a página **Conexão de Dispositivo**, selecione **Registro individual** como o método de conexão e **Certificados (X.509)** como o mecanismo. Os certificados de dispositivo usados com uma entrada de registro individual têm como requisito que o emissor e o certificado CN estejam definidos para a ID do dispositivo.

    > [!TIP]
    > Para fazer testes, você pode usar [Ferramentas do SDK do dispositivo de provisionamento de dispositivos IoT do Azure para Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) para gerar um certificado autoassinado: `node create_test_cert.js device "mytestdevice"`

- **Atestado TPM (Trusted Platform Module):** Um [TPM](../../iot-dps/concepts-tpm-attestation.md) é um tipo de módulo de segurança de hardware. Usar um TPM é uma das maneiras mais seguras de se conectar um dispositivo. Este artigo pressupõe que você está usando um TPM discreto, de firmware ou integrado. Os TPMs emulados para software são adequados para fazer protótipos ou teste, mas não oferecem o mesmo nível de segurança dos TPMs discretos, de firmware ou integrados. Não use TPMs de software na produção. Para criar um registro individual que use um TPM, abra a página **Conexão de Dispositivo**, selecione **Registro individual** como o método de conexão e **TPM** como o mecanismo. Insira a chave de endosso de TPM e salve as informações de conexão do dispositivo.

## <a name="device-registration"></a>Registro de dispositivos

Antes de se conectar a um aplicativo IoT Central, o dispositivo precisa ser registrado no aplicativo:

- Os dispositivos podem se registrar automaticamente na primeira conexão. Para usar essa opção, é preciso usar o [registro de grupo X.509](#x509-group-enrollment) ou o [registro de grupo SAS](#sas-group-enrollment).
- Um operador pode importar um arquivo CSV para registrar em massa uma lista de dispositivos no aplicativo.
- Um operador pode registrar manualmente um dispositivo individual na página **Dispositivos** no aplicativo.

O IoT Central habilita os OEMs a fabricarem em massa dispositivos que se registram automaticamente. Um OEM gera credenciais de dispositivo adequadas e configura os dispositivos na fábrica. Quando um cliente ativa um dispositivo pela primeira vez, ele se conecta ao DPS, o qual, por sua vez, conecta automaticamente o dispositivo ao aplicativo IoT Central correto. Outra opção é exigir que um operador aprove o dispositivo antes que ele comece a enviar dados ao aplicativo.

> [!TIP]
> Na página **Administração > Conexão do dispositivo**, a opção **Aprovar automaticamente** controla se o operador precisa aprovar o dispositivo manualmente antes que comece a enviar dados.

### <a name="automatically-register-devices-that-use-x509-certificates"></a>Registrar automaticamente os dispositivos que usam certificados X.509

1. Gere os certificados secundários para os dispositivos usando o certificado raiz ou intermediário que você adicionou ao [grupo de registro X.509](#x509-group-enrollment). Use as identificações do dispositivo como o `CNAME` nos certificados secundários. Uma identificação do dispositivo pode conter letras, números e o caractere `-`.

1. O OEM fornece a cada dispositivo uma identificação do dispositivo, um certificado X.509 secundário gerado e o valor do **escopo de ID** do aplicativo. O código do dispositivo também deve enviar a ID do modelo do dispositivo que ele implementa.

1. Ao ligar um dispositivo, primeiro ele se conecta ao DPS para recuperar suas informações de conexão do IoT Central.

1. O dispositivo usa as informações do DPS para se conectar e se registrar no seu aplicativo IoT Central.

O aplicativo IoT Central usa a ID do modelo enviada pelo dispositivo para [associar o dispositivo registrado a um modelo de dispositivo](#associate-a-device-with-a-device-template).

### <a name="automatically-register-devices-that-use-sas-tokens"></a>Registrar automaticamente os dispositivos que usam tokens SAS

1. Copie, no grupo de registro **SAS-IoT-Devices**, a chave primária do grupo:

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="Agrupar chave primária do grupo de registro SAS-IoT-Devices":::

1. Use o comando `az iot central device compute-device-key` para gerar as chaves SAS do dispositivo. Use a chave primária do grupo da etapa anterior. A identificação do dispositivo pode conter letras, números e o caractere `-`:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. O OEM fornece a cada dispositivo uma identificação do dispositivo, uma chave SAS de dispositivo gerada e o valor do **escopo de ID** do aplicativo. O código do dispositivo também deve enviar a ID do modelo do dispositivo que ele implementa.

1. Ao ligar um dispositivo, primeiro ele se conecta ao DPS para recuperar suas informações de registro do IoT Central.

1. O dispositivo usa as informações do DPS para se conectar e se registrar no seu aplicativo IoT Central.

O aplicativo IoT Central usa a ID do modelo enviada pelo dispositivo para [associar o dispositivo registrado a um modelo de dispositivo](#associate-a-device-with-a-device-template).

### <a name="bulk-register-devices-in-advance"></a>Registrar dispositivos em massa com antecedência

Para registrar um grande número de dispositivos com seu aplicativo IoT Central, use um arquivo CSV para [importar IDs e nomes de dispositivo](howto-manage-devices.md#import-devices).

Caso seus dispositivos usem tokens SAS para autenticar, [exporte um arquivo CSV do aplicativo IoT Central](howto-manage-devices.md#export-devices). O arquivo CSV exportado inclui as IDs de dispositivo e as chaves SAS.

Caso seus dispositivos usem certificados X. 509 para autenticar, gere certificados X. 509 secundários para os dispositivos usando o certificado raiz ou intermediário carregado no seu grupo de registro X.509. Use as identificações do dispositivo importadas como o valor `CNAME` nos certificados secundários.

Os dispositivos devem usar o valor de **escopo de ID** do seu aplicativo e enviar uma ID de modelo quando eles se conectarem.

> [!TIP]
> Encontre esse valor de **escopo de ID** em **Administração > Conexão do dispositivo**.

### <a name="register-a-single-device-in-advance"></a>Registrar um dispositivo com antecedência

Essa abordagem é útil quando você estiver experimentando o IoT Central ou dispositivos de teste. Selecione **+ Novo** na página **Dispositivos** para registrar um dispositivo individual. Use as chaves SAS de conexão do dispositivo para conectá-lo ao aplicativo IoT Central. Copie a _chave SAS do dispositivo_ das informações de conexão de um dispositivo registrado:

![Chaves SAS para um dispositivo individual](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>Associar um dispositivo a um modelo de dispositivo

O IoT Central associa automaticamente um dispositivo a um modelo de dispositivo quando o dispositivo se conecta. Um dispositivo envia uma [ID de modelo](../../iot-fundamentals/iot-glossary.md?toc=/azure/iot-central/toc.json&bc=/azure/iot-central/breadcrumb/toc.json#model-id) quando se conecta. O IoT Central usa a ID do modelo para identificar o modelo de dispositivo para esse modelo de dispositivo específico. O processo de descoberta funciona da seguinte maneira:

1. Caso modelo de dispositivo já esteja publicado no aplicativo IoT Central, o dispositivo será associado ao modelo de dispositivo.
1. Se o modelo de dispositivo ainda não estiver publicado no aplicativo IoT Central, o IoT Central procurará o modelo de dispositivo no [repositório de modelos públicos](https://github.com/Azure/iot-plugandplay-models). Se o IoT Central encontrar o modelo, ele o usará para gerar um modelo de dispositivo básico.
1. Se o IoT Central não encontrar o modelo no repositório de modelos públicos, o dispositivo será marcado como **Não associado**. Um operador pode criar um modelo de dispositivo e, depois, migrar o dispositivo não associado para o novo modelo de dispositivo.

A captura de tela a seguir mostra como ver a ID de um modelo de dispositivo no IoT Central. Em um modelo de dispositivo, selecione um componente e clique em **Exibir identidade**:

:::image type="content" source="media/concepts-get-connected/model-id.png" alt-text="Captura de tela mostrando a ID do modelo no modelo de dispositivo termostato.":::

É possível ver o [modelo termostato](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-1.json) no repositório de modelos públicos. A definição de ID do modelo é semelhante a:

```json
"@id": "dtmi:com:example:Thermostat;1"
```

## <a name="device-status-values"></a>Valores de status do dispositivo

Quando um dispositivo real se conectar ao seu aplicativo IoT Central, o status do dispositivo será alterado da seguinte maneira:

1. O primeiro status do dispositivo é **Registrado**. Esse status significa que o dispositivo foi criado no IoT Central e tem uma ID de dispositivo. Um dispositivo é registrado quando:
    - Um novo dispositivo real é adicionado na página **Dispositivos**.
    - Um conjunto de dispositivos é adicionado usando a opção **Importação** na página **Dispositivos**.

1. O status do dispositivo muda para **Provisionado** quando o dispositivo conectado ao seu aplicativo IoT Central com credenciais válidas conclui a etapa de provisionamento. Nesta etapa, o dispositivo usa o DPS para, automaticamente, recuperar uma cadeia de conexão do Hub IoT usado pelo seu aplicativo IoT Central. Agora o dispositivo pode se conectar ao IoT Central e começar a enviar dados.

1. Um operador pode bloquear um dispositivo. Quando um dispositivo é bloqueado, ele não pode enviar dados para o aplicativo IoT Central. Os dispositivos bloqueados apresentam o status **Bloqueado**. Um operador deve redefinir o dispositivo antes de poder retomar o envio de dados. Quando um operador desbloquear um dispositivo, o status retornará ao seu valor anterior: **Registrado** ou **Provisionado**.

1. Se o status do dispositivo for **Aguardando aprovação**, isso significa que a opção **Aprovação automática** está desabilitada. Um operador deve aprovar explicitamente um dispositivo antes que ele comece a enviar dados. Dispositivos que não estejam registrados manualmente na página **Dispositivos**, mas que estejam conectados com credenciais válidas apresentarão **Aguardando aprovação** como status de dispositivo. Os operadores podem aprovar esses dispositivos na página **Dispositivos** usando o botão **Aprovar**.

1. Caso o status do dispositivo for seja **Não associado**, isso significa que o dispositivo que está se conectando ao IoT Central não tem um modelo de dispositivo associado. Essa situação geralmente ocorre nos seguintes cenários:

    - Um conjunto de dispositivos é adicionado usando a opção **Importar** na página **Dispositivos** sem especificar o modelo do dispositivo.
    - Um dispositivo foi registrado manualmente na página **Dispositivos** sem especificar o modelo de dispositivo. Em seguida, o dispositivo foi conectado com as credenciais válidas.  

    O operador pode associar um dispositivo a um modelo de dispositivo da página **Dispositivos** usando o botão **Migrar**.

## <a name="sdk-support"></a>Suporte a SDK

Os SDKs de dispositivo do Azure fornecem a forma mais fácil de implementar o código do dispositivo. Estão disponíveis os SDKs do dispositivo a seguir:

- [SDK do IoT do Azure para C](https://github.com/azure/azure-iot-sdk-c)
- [SDK do IoT do Azure para Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK do IoT do Azure para Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK do IoT do Azure para Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK do IoT do Azure para .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Recursos de SDK e conectividade do Hub IoT

Toda a comunicação do dispositivo com o Hub IoT usa as opções de conectividade do Hub IoT a seguir:

- [Mensagens de dispositivo para nuvem](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Mensagens de nuvem para dispositivo](../../iot-hub/iot-hub-devguide-messages-c2d.md)
- [Dispositivos gêmeos](../../iot-hub/iot-hub-devguide-device-twins.md)

A tabela a seguir resume como os recursos do dispositivo Azure IoT Central são mapeados para os recursos do Hub IoT:

| Azure IoT Central | Hub IoT do Azure |
| ----------- | ------- |
| Telemetria | Mensagens do dispositivo para a nuvem |
| Comandos offline | Mensagens da nuvem para o dispositivo |
| Propriedade | Propriedades relatadas do dispositivo gêmeo |
| Propriedade (gravável) | Propriedades desejadas e relatadas do dispositivo gêmeo |
| Comando | Métodos diretos |

### <a name="protocols"></a>Protocolos

Os SDKs do Dispositivo dão suporte aos protocolos de rede a seguir para conectar um Hub IoT:

- MQTT
- AMQP
- HTTPS

Para obter informações sobre esses diferentes protocolos e as diretrizes sobre como escolher um deles, consulte [Escolher um protocolo de comunicação](../../iot-hub/iot-hub-devguide-protocols.md).

Se o dispositivo não puder usar nenhum dos protocolos compatíveis, use o Azure IoT Edge para fazer a conversão de protocolo. O IoT Edge é compatível com outros cenários de inteligência na borda para transferir o processamento o aplicativo Azure IoT Central.

## <a name="security"></a>Segurança

Todos os dados trocados entre dispositivos e o Azure IoT Central são criptografados. O Hub IoT autentica todas as solicitações de um dispositivo que conecta a qualquer ponto de extremidade do Hub IoT voltado para o dispositivo. Para evitar a troca de credenciais pela rede, um dispositivo usa tokens assinados para autenticar. Para obter mais informações, consulte [Controlar o acesso ao Hub IoT](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Próximas etapas

Se você for um desenvolvedor de dispositivos, algumas próximas etapas sugeridas são:

- Veja as [melhores práticas](concepts-best-practices.md) de desenvolvimento de dispositivos.
- Analise um exemplo de código que mostra como usar tokens SAS em [Tutorial: criar e conectar um aplicativo cliente ao aplicativo do Azure IoT Central](tutorial-connect-device.md)
- Saiba [Como conectar dispositivos com certificados X.509 usando o SDK do dispositivo Node.js para o aplicativo IoT Central](how-to-connect-devices-x509.md)
- Saiba como [Monitorar a conectividade do dispositivo usando o CLI do Azure](./howto-monitor-devices-azure-cli.md)
- Leia sobre [Dispositivos do Azure IoT Edge e do Azure IoT Central](./concepts-iot-edge.md)
