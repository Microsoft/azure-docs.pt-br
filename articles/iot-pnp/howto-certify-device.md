---
title: Como certificar dispositivos de Plug and Play IoT | Microsoft Docs
description: Como um construtor de dispositivos, saiba como executar testes e enviar um dispositivo para certificação
author: konichi3
ms.author: koichih
ms.date: 08/21/2020
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b3ba007f03b3aa4883d9455dc43b2bc19676da59
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803962"
---
# <a name="how-to-certify-iot-plug-and-play-devices"></a>Como certificar dispositivos de IoT Plug and Play

O programa de certificação de dispositivo IoT Plug and Play inclui ferramentas para verificar se um dispositivo atende aos requisitos de certificação do IoT Plug and Play. As ferramentas também ajudam as organizações a impulsionar o reconhecimento da disponibilidade de seus dispositivos IoT Plug and Play. Esses dispositivos certificados são personalizados para soluções de IoT e ajudam a reduzir o tempo de colocação no mercado.

Este artigo mostra como:

- Instalar a extensão da ferramenta de linha de comando do Azure IoT para o CLI do Azure
- Executar os testes de Plug and Play de IoT para validar seu aplicativo de dispositivo durante a fase de desenvolvimento  
- Usar o portal do dispositivo do Azure Certified para validar o aplicativo do dispositivo

## <a name="prepare-your-device"></a>Preparar seu dispositivo

O código do aplicativo que é executado em seu Plug and Play de IoT deve:

- Conecte-se ao Hub IoT do Azure usando o [DPS (serviço de provisionamento de dispositivos)](../iot-dps/about-iot-dps.md).
- Siga as [convenções de plug-and-Play de IOT](concepts-convention.md) para implementar a telemetria, propriedades e comandos.

O aplicativo é um software que é instalado separadamente do sistema operacional ou é agrupado com o sistema operacional em uma imagem de firmware que é atualizada para o dispositivo.

O processo de certificação verifica se o dispositivo é compatível com IoT Plug and Play, Validando que a implementação do dispositivo corresponde à telemetria, às propriedades e aos comandos definidos no modelo de dispositivo [DTDL (digital gêmeos Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) e que o modelo está disponível no [repositório de modelo público do Azure IOT](concepts-model-repository.md).

Para atender aos requisitos de certificação, seu dispositivo deve:

- Conecta-se ao Hub IoT do Azure usando o [DPS](../iot-dps/about-iot-dps.md).
- Implementação de telemetria, propriedades ou comandos após a Convenção de Plug and Play IoT.
- Descreva as interações de dispositivo com um modelo [DTDL v2](https://aka.ms/dtdl) .
- Publicar o modelo e todas as interfaces necessárias no repositório de modelo público do Azure IoT
- Envie a ID do modelo durante o [registro do DPS](./concepts-developer-guide-device.md#dps-payload) na carga de provisionamento do DPS.
- Anuncie a ID do modelo durante a [conexão MQTT](./concepts-developer-guide-device.md#model-id-announcement).
- Todos os modelos de dispositivo devem ser compatíveis com o [Azure IOT central](../iot-central/core/overview-iot-central-developer.md).

> [!NOTE]
> Atualmente, o Azure IoT Central não dá suporte completo à **matriz** e aos tipos de dados **geoespaciais** DTDL.

## <a name="test-with-the-azure-iot-extension-cli"></a>Testar com a CLI de extensão de IoT do Azure

A [extensão da CLI do Azure IOT](/cli/azure/ext/azure-iot/iot/product?preserve-view=true&view=azure-cli-latest) permite que você valide se a implementação do dispositivo corresponde ao modelo antes de enviar o dispositivo para certificação por meio do portal do dispositivo certificado pelo Azure.

As etapas a seguir mostram como preparar e executar os testes de certificação usando a CLI:

### <a name="install-the-azure-iot-extension-for-the-azure-cli"></a>Instalar a extensão do Azure IoT para o CLI do Azure

Consulte as instruções de instalação para configurar o [CLI do Azure](/cli/azure/?preserve-view=true&view=azure-cli-latest) em seu ambiente.

Para instalar a extensão do Azure IoT, execute o seguinte comando:

```azurecli
az extension add --name azure-iot
```

Para saber mais, confira [CLI do Azure para o Azure IOT](/cli/azure/azure-cli-reference-for-iot?preserve-view=true&view=azure-cli-latest).

### <a name="create-a-new-product-test"></a>Criar um novo teste de produto

O comando a seguir cria um teste usando o DPS com um método de atestado de chave simétrica:

- Cria um novo produto para testar e gera uma configuração de teste. A saída exibe as informações de DPS que o dispositivo deve usar para provisionamento: a chave primária, a ID do dispositivo e o escopo da ID.
- Especifica a pasta com os arquivos DTDL que descrevem seu modelo.

```azurecli
az iot product test create --badge-type Pnp --at SymmetricKey --device-type FinishedProduct --models {local folder name}
```

> [!NOTE]
> Você precisa [entrar](/cli/azure/authenticate-azure-cli?preserve-view=true&view=azure-cli-latest) em sua assinatura ao usar a CLI.

A saída JSON do comando contém o `primaryKey` , o `registrationId` e o `scopeID` para usar ao conectar seu dispositivo.

Saída esperada:

```json
"deviceType": "FinishedProduct",
"id": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
"provisioningConfiguration": {
  "symmetricKeyEnrollmentInformation": {
    "primaryKey":"Ci/Ghpqp0n4m8FD5PTicr6dEikIfM3AtVWzAhObU7yc=",
    "registrationId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
    "scopeId": "0ne000FFA42"
  }
}
```

### <a name="connect-your-device"></a>Conecte seu dispositivo

Use a saída de informações de DPS pelo comando anterior para conectar seu dispositivo à instância do Hub IoT de teste.

### <a name="manage-and-configure-the-product-tests"></a>Gerenciar e configurar os testes de produto

Quando o dispositivo estiver conectado e estiver pronto para interagir com o Hub IoT, gere um arquivo de configuração de teste de produto. Para criar o arquivo:

- Use o teste `id` da saída do comando anterior.
- Use o `--wait` parâmetro para obter o caso de teste.

```azurecli
az iot product test task create --type GenerateTestCases --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Saída esperada:

```json
{
  "deviceTestId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
  "error": null,
  "id": "526da38e-91fc-4e20-a761-4f04b392c42b",
  "resultLink": "/deviceTests/d45d53d9-656d-4be7-9bbf-140bc87e98dc/TestCases",
  "status": "Completed",
  "type": "GenerateTestCases"
}
```

Você pode usar o `az iot product test case update` comando para modificar o arquivo de configuração de teste.

### <a name="run-the-tests"></a>Executar os testes

Depois de gerar a configuração de teste, a próxima etapa é executar os testes. Use o mesmo `devicetestId` dos comandos anteriores como parâmetro para executar os testes. Verifique os resultados do teste para certificar-se de que todos os testes têm um status `Passed` .

```azurecli
az iot product test task create --type QueueTestRun --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Exemplo de saída de execução de teste

```json
      "validationTasks": [
        {
          "componentName": "Default component",
          "endTime": "2020-08-25T05:18:49.5224772+00:00",
          "interfaceId": "dtmi:com:example:TemperatureController;1",
          "logs": [
            {
              "message": "Waiting for telemetry from the device",
              "time": "2020-08-25T05:18:37.3862586+00:00"
            },
            {
              "message": "Validating PnP properties",
              "time": "2020-08-25T05:18:37.3875168+00:00"
            },
            {
              "message": "Validating PnP commands",
              "time": "2020-08-25T05:18:37.3894343+00:00"
            },
            {
              "message": "{\"propertyName\":\"serialNumber\",\"expectedSchemaType\":null,\"actualSchemaType\":null,\"message\":\"Property is successfully validated\",\"passed\":true,\"time\":\"2020-08-25T05:18:37.4205985+00:00\"}",
              "time": "2020-08-25T05:18:37.4205985+00:00"
            },
            {
              "message": "PnP interface properties validation passed",
              "time": "2020-08-25T05:18:37.4206964+00:00"
            },
```

## <a name="test-using-the-azure-certified-device-portal"></a>Testar usando o portal do Microsoft Azure Certified para IoT

As etapas a seguir mostram como usar o [portal do dispositivo certificado pelo Azure](https://aka.ms/acdp) para carregar, registrar detalhes do produto, enviar um guia de introdução e executar os testes de certificação.

### <a name="onboarding"></a>Integração

Para usar o [portal de certificação](https://aka.ms/acdp), você deve usar um Azure Active Directory do seu locatário corporativo ou de estudante.

Para publicar os modelos no [repositório de modelo público do Azure IOT](https://github.com/Azure/iot-plugandplay-models), sua conta deve ser membro da [Microsoft Partner Network](https://partner.microsoft.com). O sistema verifica se a ID de Microsoft Partner Network existe e se a conta está totalmente verificados antes de publicar no catálogo do dispositivo.

### <a name="company-profile"></a>Perfil da empresa

Você pode gerenciar o perfil de sua empresa no menu de navegação à esquerda. O perfil da empresa inclui a URL da empresa, o endereço de email e o logotipo da empresa. O contrato do programa deve ser aceito nesta página antes de executar qualquer operação de certificação.

As informações de perfil da empresa são usadas na descrição do dispositivo demonstrado no catálogo do dispositivo.

### <a name="create-new-project"></a>Criar um novo projeto

Para certificar um dispositivo, você deve primeiro criar um novo projeto.

Navegue até o [portal de certificação](https://aka.ms/acdp). Na página **projetos** , selecione *+ criar novo projeto*. Em seguida, insira um nome para o projeto, o nome do dispositivo e selecione uma classe de dispositivo.

As informações do produto que você fornece durante o processo de certificação se enquadram em quatro categorias:

- Informações do dispositivo. Coleta informações sobre o dispositivo, como nome, descrição, certificações e sistema operacional.
- O guia de **introdução** . Você deve enviar o guia como um documento PDF a ser aprovado pelo administrador do sistema antes de publicar o dispositivo.
- Detalhes de marketing. Forneça informações de marketing prontas para o cliente para seu dispositivo. As informações de marketing incluem descrição, foto e distribuidores.
- Certificações adicionais do setor. Essa seção opcional permite que você forneça informações adicionais sobre quaisquer outras certificações que o dispositivo tenha recebido.

### <a name="connect-and-test"></a>Conectar e testar

A etapa de conexão e teste verifica se o dispositivo atende aos requisitos de certificação de Plug and Play de IoT.

Há três etapas a serem concluídas:

1. Conectar e descobrir interfaces. O dispositivo deve se conectar ao serviço de certificação IoT do Azure por meio do DPS. Escolha o método de autenticação (certificado X. 509, chaves simétricas ou módulo de plataforma confiável) para usar e atualizar o aplicativo do dispositivo com as informações de DPS.
1. Examine as interfaces. Examine a interface e certifique-se de que cada uma delas tem entradas de carga que fazem sentido para teste.
1. Testar. O sistema testa cada modelo de dispositivo para verificar se a telemetria, as propriedades e os comandos descritos no modelo seguem as convenções de Plug and Play de IoT. Quando o teste for concluído, selecione o link **Exibir logs** para ver a telemetria do dispositivo e os dados brutos enviados às propriedades do dispositivo do Hub IOT.

### <a name="submit-and-publish"></a>Enviar e publicar

O estágio final necessário é enviar o projeto para revisão. Esta etapa notifica um membro da equipe de dispositivos certificados do Azure para revisar seu projeto para fins de integridade, incluindo os detalhes do dispositivo e de marketing e o guia de introdução. Um membro da equipe pode entrar em contato com você no endereço de email da empresa fornecido anteriormente com perguntas ou editar solicitações antes da aprovação.

Se seu dispositivo exigir validação manual adicional como parte da certificação, você receberá um aviso no momento.

Quando um dispositivo é certificado, você pode optar por publicar os detalhes do produto no catálogo de dispositivos certificado pelo Azure usando o recurso **publicar no catálogo** na página Resumo do produto.

## <a name="next-steps"></a>Próximas etapas

Agora que o envio do dispositivo está concluído, você pode entrar em contato com a equipe de certificação do dispositivo em [iotcert@microsoft.com](mailto:iotcert@microsoft.com) para continuar nas próximas etapas, que incluem validação de associação de Microsoft Partner Network e uma revisão dos guias de introdução. Quando todos os requisitos forem atendidos, você poderá optar por ter seu dispositivo incluído no [Catálogo de dispositivos do Azure IOT certificado](https://aka.ms/devicecatalog).