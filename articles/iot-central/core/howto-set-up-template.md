---
title: Definir um novo tipo de dispositivo IoT no Azure IoT Central | Microsoft Docs
description: Este artigo mostra, como um Solution Builder, como criar um novo modelo de dispositivo IoT do Azure em seu aplicativo de IoT Central do Azure. Você define a telemetria, estado, propriedades e comandos para o seu tipo.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperf-fy21q1
- device-developer
ms.openlocfilehash: 22e948a0100f23dbddef8fc138576bb4b9372c77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363195"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Definir um novo tipo de dispositivo IoT em seu aplicativo do Azure IoT Central

*Este artigo se aplica a construtores de solução e desenvolvedores de dispositivos.*

Um modelo de dispositivo é um plano gráfico que define as características e os comportamentos de um tipo de dispositivo que se conecta a um [aplicativo de IOT central do Azure](concepts-app-templates.md).

Por exemplo, um construtor pode criar um modelo de dispositivo para um ventilador conectado que tenha as seguintes características:

- Envia a telemetria de temperatura
- Envia a propriedade de localização
- Envia os eventos de erro do motor do ventilador
- Envia o estado operacional do ventilador
- Fornece uma propriedade de velocidade de ventilador gravável
- Fornece um comando para reiniciar o dispositivo
- Fornece uma exibição geral do dispositivo usando uma exibição

Nesse modelo de dispositivo, um operador pode criar e conectar dispositivos de ventilador reais. Todos esses ventiladores têm medidas, propriedades e comandos que os operadores usam para monitorá-los e gerenciá-los. Os operadores usam [modos de exibição](#add-views) e formulários do dispositivo para interagir com os dispositivos do ventilador. Um desenvolvedor de dispositivos usa o modelo para entender como o dispositivo interage com o aplicativo. Para obter mais informações, consulte [telemetria, propriedade e cargas de comando](concepts-telemetry-properties-commands.md).

> [!NOTE]
> Somente construtores e administradores podem criar, editar e excluir modelos de dispositivo. Qualquer usuário pode criar dispositivos na página **Dispositivos** com base nos modelos de dispositivos existentes.

Em um aplicativo IoT Central, um modelo de dispositivo usa um modelo de dispositivo para descrever os recursos de um dispositivo. Como um criador, você tem várias opções para criar modelos de dispositivo:

- Crie o modelo de dispositivo no IoT Central e, em seguida, [implemente seu modelo de dispositivo no código do dispositivo](concepts-telemetry-properties-commands.md).
- Importe um modelo de dispositivo do [Catálogo de dispositivos certificado pelo Azure para IOT](https://aka.ms/iotdevcat). Personalize o modelo de dispositivo para seus requisitos no IoT Central.
> [!NOTE]
> IoT Central requer o modelo completo com todas as interfaces referenciadas no mesmo arquivo, quando você importa um modelo do repositório de modelos, use a palavra-chave "Expanded" para obter a versão completa.
Por exemplo. https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json

- Crie um modelo de dispositivo usando o [DTDL (digital gêmeos Definition Language)-versão 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). O Visual Studio Code tem uma extensão que dá suporte a criação de modelos DTDL. Para saber mais, confira [Instalar e usar as ferramentas de criação DTDL](../../iot-pnp/howto-use-dtdl-authoring-tools.md). Em seguida, publique o modelo no repositório de modelo público. Para saber mais, consulte [repositório de modelos de dispositivo](../../iot-pnp/concepts-model-repository.md). Implemente o código do dispositivo do modelo e conecte seu dispositivo real ao seu aplicativo IoT Central. IoT Central localiza e importa o modelo de dispositivo do repositório público para você e gera um modelo de dispositivo. Em seguida, você pode adicionar quaisquer propriedades de nuvem, personalizações e exibições que seu aplicativo IoT Central precisa para o modelo de dispositivo.
- Crie um modelo de dispositivo usando o DTDL. Implemente o código do dispositivo do modelo. Importe manualmente o modelo do dispositivo para seu aplicativo IoT Central e, em seguida, adicione as propriedades de nuvem, personalizações e exibições de que seu aplicativo IoT Central precisa.

> [!TIP]
> IoT Central requer o modelo completo com todas as interfaces referenciadas no mesmo arquivo. Quando você importa um modelo do repositório de modelos, use a palavra-chave *Expanded* para obter a versão completa.
> Por exemplo, [https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json](https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json).

Você também pode adicionar modelos de dispositivo a um aplicativo IoT Central usando a [API REST](/learn/modules/manage-iot-central-apps-with-rest-api/) ou a [CLI](howto-manage-iot-central-from-cli.md).

Alguns [modelos de aplicativos](concepts-app-templates.md) já incluem modelos de dispositivo que são úteis no cenário ao qual o modelo de aplicativo dá suporte. Por exemplo, consulte [arquitetura da análise na loja](../retail/store-analytics-architecture.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Criar um modelo de dispositivo com base no catálogo de dispositivos

Como um construtor, você pode começar rapidamente a criar sua solução usando um dispositivo certificado. Confira a lista no [Catálogo de Dispositivos IoT do Azure](https://catalog.azureiotsolutions.com/alldevices). O IoT Central integra-se com o catálogo do dispositivo para que você possa importar um modelo de dispositivo de qualquer um dos dispositivos certificados. Para criar um modelo de dispositivo com base em um desses dispositivos no IoT Central:

1. Vá para a página **modelos de dispositivo** em seu aplicativo IOT central.
1. Selecione **+ novo** e, em seguida, selecione qualquer um dos dispositivos certificados do catálogo. IoT Central cria um modelo de dispositivo com base nesse modelo de dispositivo.
1. Adicione propriedades de nuvem, personalizações ou exibições ao modelo de dispositivo.
1. Selecione **Publicar** para disponibilizar o modelo para os operadores para exibir e conectar dispositivos.

## <a name="create-a-device-template-from-scratch"></a>Criar um modelo de dispositivo do zero

Um modelo de dispositivo contém:

- Um _modelo de dispositivo_ que especifica a telemetria, as propriedades e os comandos que o dispositivo implementa. Esses recursos são organizados em um ou mais componentes.
- _Propriedades de nuvem_ que definem as informações que o aplicativo do IoT Central armazena sobre seus dispositivos. Por exemplo, uma propriedade de nuvem pode registrar a data da última manutenção de um dispositivo. Essas informações nunca são compartilhadas com o dispositivo.
- As _personalizações_ permitem que o Construtor substitua algumas das definições no modelo do dispositivo. Por exemplo, o construtor pode substituir o nome de uma propriedade de dispositivo. Os nomes de propriedade aparecem em IoT Central modos de exibição e formulários.
- _Exibições e formulários_ permitem que o Construtor crie uma interface do usuário que permite aos operadores monitorar e gerenciar os dispositivos conectados ao seu aplicativo.

Para criar um modelo de dispositivo no IoT Central:

1. Acesse a página **Modelos de Dispositivo** no aplicativo do IoT Central.
1. Selecione **+ novo**  >  **dispositivo IOT**. Em seguida, selecione **Avançar: Personalizar**.
1. Insira um nome para o modelo, como **termostato**. Em seguida, selecione **Avançar: revisão** e, em seguida, selecione **criar**.
1. IoT Central cria um modelo de dispositivo vazio e permite que você escolha criar um modelo personalizado do zero ou importar um modelo DTDL.

## <a name="manage-a-device-template"></a>Gerenciar um modelo de dispositivo

Renomeie ou exclua um modelo na página inicial do modelo.

Depois de adicionar um modelo de dispositivo ao seu modelo, você poderá publicá-lo. Antes de publicar o modelo, você não pode conectar um dispositivo com base nesse modelo para que os operadores o vejam na página **Dispositivos**.

## <a name="create-a-capability-model"></a>Criar um modelo de funcionalidade

Para criar um modelo de dispositivo, você pode:

- Usar o IoT Central para criar um modelo personalizado do zero.
- Importar um modelo DTDL de um arquivo JSON. Um construtor de dispositivos pode ter usado Visual Studio Code para criar um modelo de dispositivo para seu aplicativo.
- Selecionar um dos dispositivos no catálogo de dispositivos. Essa opção importa o modelo de dispositivo que o fabricante publicou para este dispositivo. Um modelo de dispositivo importado como esse é publicado automaticamente.

## <a name="manage-a-capability-model"></a>Gerenciar um modelo de funcionalidade

Depois de criar um modelo de dispositivo, você pode:

- Adicione componentes ao modelo. Um modelo deve ter pelo menos um componente.
- Editar os metadados do modelo, como ID, namespace e nome.
- Excluir o modelo.

## <a name="create-a-component"></a>Criar um componente

Um modelo de dispositivo deve ter pelo menos um componente padrão. Um componente é uma coleção reutilizável de recursos.

Para criar um componente:

1. Vá para o modelo do dispositivo e escolha **+ Adicionar componente**.

1. Na página **Adicionar uma interface de componente** , você pode:

    - Crie um componente personalizado do zero.
    - Importe um componente existente de um arquivo DTDL. Um construtor de dispositivos pode ter usado Visual Studio Code para criar uma interface de componente para seu dispositivo.

1. Depois de criar um componente, escolha **Editar identidade** para alterar o nome de exibição do componente.

1. Se você optar por criar um componente personalizado do zero, poderá adicionar os recursos do dispositivo. As funcionalidades do dispositivo são telemetria, propriedades e comandos.

### <a name="telemetry"></a>Telemetria

A telemetria é um fluxo de valores enviado do dispositivo, normalmente de um sensor. Por exemplo, um sensor pode relatar a temperatura ambiente.

A seguinte tabela mostra as definições de configuração para uma funcionalidade de telemetria:

| Campo | Descrição |
| ----- | ----------- |
| Nome de exibição | O nome de exibição do valor de telemetria usado em exibições e formulários. |
| Nome | O nome do campo na mensagem de telemetria. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. Este campo precisa ser alfanumérico. |
| Tipo de Funcionalidade | Telemetria. |
| Tipo Semântico | O tipo semântico da telemetria, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos campos a seguir estão disponíveis. |
| Esquema | O tipo de dados telemétricos, como duplo, cadeia de caracteres ou vetor. As opções disponíveis são determinadas pelo tipo semântico. O esquema não está disponível para os tipos semânticos de evento e estado. |
| Severidade | Disponível somente para o tipo semântico de evento. As severidades são **Erro**, **Informação** ou **Aviso**. |
| Valores de Estado | Disponível apenas para o tipo de estado semântico. Defina os valores de estado possíveis, cada um dos quais com o nome de exibição, o nome, o tipo de enumeração e o valor. |
| Unidade | Uma unidade para o valor de telemetria, como **mph**, **%** ou **&deg; C**. |
| Unidade de exibição | Uma unidade de exibição para uso em exibições e formulários. |
| Comentário | Comentários sobre a funcionalidade de telemetria. |
| Descrição | Uma descrição da funcionalidade de telemetria. |

### <a name="properties"></a>Propriedades

As propriedades representam valores de ponto no tempo. Por exemplo, um dispositivo pode usar uma propriedade para relatar a temperatura de destino que está tentando alcançar. Você pode definir propriedades graváveis de IoT Central.

A seguinte tabela mostra as definições de configuração para uma funcionalidade de propriedade:

| Campo | Descrição |
| ----- | ----------- |
| Nome de exibição | O nome de exibição do valor da propriedade usado em exibições e formulários. |
| Nome | O nome da propriedade. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. Este campo precisa ser alfanumérico. |
| Tipo de Funcionalidade | Propriedade. |
| Tipo Semântico | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos campos a seguir estão disponíveis. |
| Esquema | O tipo de dados de propriedade, como duplo, cadeia de caracteres ou vetor. As opções disponíveis são determinadas pelo tipo semântico. O esquema não está disponível para os tipos semânticos de evento e estado. |
| Gravável | Se a propriedade não for gravável, o dispositivo poderá relatar valores de propriedade para IoT Central. Se a propriedade for gravável, o dispositivo poderá relatar valores de propriedade para IoT Central e IoT Central poderá enviar atualizações de propriedade para o dispositivo.
| Severidade | Disponível somente para o tipo semântico de evento. As severidades são **Erro**, **Informação** ou **Aviso**. |
| Valores de Estado | Disponível apenas para o tipo de estado semântico. Defina os valores de estado possíveis, cada um dos quais com o nome de exibição, o nome, o tipo de enumeração e o valor. |
| Unidade | Uma unidade para o valor da propriedade, como **mph**, **%** ou **&deg; C**. |
| Unidade de exibição | Uma unidade de exibição para uso em exibições e formulários. |
| Comentário | Comentários sobre a funcionalidade de propriedade. |
| Descrição | Uma descrição da funcionalidade de propriedade. |

### <a name="commands"></a>Comandos

Você pode chamar comandos do dispositivo no IoT Central. Opcionalmente, os comandos passam parâmetros para o dispositivo e recebem uma resposta do dispositivo. Por exemplo, você pode chamar um comando para reinicializar um dispositivo em 10 segundos.

A seguinte tabela mostra as definições de configuração para uma funcionalidade de comando:

| Campo | Descrição |
| ----- | ----------- |
| Nome de exibição | O nome de exibição do comando usado em exibições e formulários. |
| Nome | O nome do comando. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. Este campo precisa ser alfanumérico. |
| Tipo de Funcionalidade | Comando. |
| Comentário | Comentários sobre a funcionalidade de comando. |
| Descrição | Uma descrição da funcionalidade de comando. |
| Solicitação | Se habilitada, uma definição do parâmetro de solicitação, incluindo nome, nome de exibição, esquema, unidade e unidade de exibição. |
| Resposta | Se habilitada, uma definição da resposta do comando, incluindo nome, nome de exibição, esquema, unidade e unidade de exibição. |

Para saber mais sobre como os dispositivos implementam comandos, consulte [cargas de telemetria, propriedade e comando > comandos e comandos de longa execução](concepts-telemetry-properties-commands.md#commands).

#### <a name="offline-commands"></a>Comandos offline

Você pode escolher os comandos de fila se um dispositivo estiver offline, habilitando a opção **fila se estiver offline** para um comando no modelo de dispositivo.

Essa opção usa mensagens da nuvem para o dispositivo do Hub IoT para enviar notificações para dispositivos. Para saber mais, confira o artigo do Hub IoT [enviar mensagens da nuvem para o dispositivo](../../iot-hub/iot-hub-devguide-messages-c2d.md).

Mensagens da nuvem para o dispositivo:

- São notificações unidirecionais para o dispositivo de sua solução.
- Garantia de entrega de mensagem pelo menos uma vez. O Hub IoT mantém as mensagens da nuvem para o dispositivo em filas por dispositivo, garantindo a resiliência contra falhas de conectividade e de dispositivo.
- Exigir que o dispositivo implemente um manipulador de mensagens para processar a mensagem da nuvem para o dispositivo.

> [!NOTE]
> Essa opção só está disponível na interface do usuário do IoT Central Web. Essa configuração não será incluída se você exportar um modelo ou componente do modelo de dispositivo.

## <a name="manage-a-component"></a>Gerenciar um componente

Se você ainda não publicou o componente, poderá editar os recursos definidos pelo componente. Depois de publicar o componente, se desejar fazer alterações, você deverá criar uma nova versão do modelo de dispositivo e [a versão do componente](howto-version-device-template.md). Você pode fazer alterações que não exigem controle de versão, como nomes de exibição ou unidades, na seção **Personalizar**.

Você também pode exportar o componente como um arquivo JSON se quiser reutilizá-lo em outro modelo de funcionalidade.

## <a name="add-cloud-properties"></a>Adicionar propriedades da nuvem

Use propriedades de nuvem para armazenar informações sobre dispositivos no IoT Central. As propriedades de nuvem nunca são enviadas a um dispositivo. Por exemplo, você pode usar propriedades de nuvem para armazenar o nome do cliente que instalou o dispositivo ou a data do último serviço do dispositivo.

A seguinte tabela mostra as definições de configuração para uma propriedade de nuvem:

| Campo | Descrição |
| ----- | ----------- |
| Nome de exibição | O nome de exibição para o valor da propriedade de nuvem usado em exibições e formulários. |
| Nome | O nome da propriedade de nuvem. O IoT Central gera um valor para esse campo com base no nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
| Tipo Semântico | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos campos a seguir estão disponíveis. |
| Esquema | O tipo de dados de propriedade de nuvem, como duplo, cadeia de caracteres ou vetor. As opções disponíveis são determinadas pelo tipo semântico. |

## <a name="add-customizations"></a>Adicionar personalizações

Use personalizações quando precisar modificar um componente importado ou adicionar recursos específicos de IoT Central a um recurso. Você só pode personalizar campos que não interrompem a compatibilidade de componentes. Por exemplo, você pode:

- Personalizar o nome de exibição e as unidades de uma funcionalidade.
- Adicionar uma cor padrão a ser usada quando o valor for exibido em um gráfico.
- Especificar os valores iniciais, mínimos e máximos, para uma propriedade.

Você não pode personalizar o nome da funcionalidade ou o tipo de funcionalidade. Se houver alterações que não podem ser feitas na seção **Personalizar** , você precisará criar o modelo de dispositivo e o componente para modificar o recurso.

### <a name="generate-default-views"></a>Gerar modos de exibição padrão

Gerar modos de exibição padrão é uma maneira rápida de visualizar as informações importantes do dispositivo. Você tem até três modos de exibição padrão geradas para seu modelo de dispositivo:

- **Comandos**: uma exibição com comandos de dispositivo e permite que seu operador os envie para seu dispositivo.
- **Visão geral**: uma exibição com telemetria do dispositivo, exibindo gráficos e métricas.
- **Sobre**: uma exibição com informações do dispositivo, exibindo as propriedades do dispositivo.

Depois de selecionar **gerar exibições padrão**, você verá que elas foram adicionadas automaticamente na seção **exibições** do modelo de dispositivo.

## <a name="add-views"></a>Adicionar exibições

Adicione exibições a um modelo de dispositivo para permitir que os operadores visualizem um dispositivo usando gráficos e métricas. Você pode ter várias exibições para um modelo de dispositivo.

Para adicionar uma exibição a um modelo de dispositivo:

1. Acesse o modelo de dispositivo e selecione **Exibições**.
1. Escolha **Como visualizar o Dispositivo**.
1. Insira um nome para o modo de exibição no **nome da exibição**.
1. Adicione blocos à sua exibição na lista de elementos estáticos, de propriedade, de nuvem, de telemetria e de comando. Arraste e solte os blocos que você deseja adicionar à sua exibição.
1. Para plotar vários valores de telemetria em um só bloco de gráfico, selecione os valores de telemetria e, em seguida, selecione **Combinar**.
1. Configure cada bloco que você adicionar para personalizar a forma como ele exibe os dados. Acesse essa opção selecionando o ícone de engrenagem ou selecionando **Alterar configuração** no bloco do gráfico.
1. Organize e redimensione os blocos no modo de exibição.
1. Salve as alterações.

### <a name="configure-preview-device-to-view"></a>Configurar o dispositivo de visualização para exibição

Para exibir e testar seu modo de exibição, selecione **Configurar dispositivo de visualização**. Esse recurso permite ver a exibição à medida que seu operador vê-a após sua publicação. Use esse recurso para validar que suas exibições mostram os dados corretos. É possível escolher um das seguintes opções:

- Nenhum dispositivo de visualização.
- O dispositivo de teste real que você configurou para seu modelo de dispositivo.
- Um dispositivo existente em seu aplicativo usando a ID do dispositivo.

## <a name="add-forms"></a>Adicionar formulários

Adicione formulários a um modelo de dispositivo para permitir que os operadores gerenciem um dispositivo exibindo e definindo propriedades. Os operadores só podem editar propriedades de nuvem e propriedades de dispositivo graváveis. Você pode ter vários formulários para um modelo de dispositivo.

Para adicionar um formulário a um modelo de dispositivo:

1. Acesse o modelo de dispositivo e selecione **Exibições**.
1. Escolha **Como editar dados de Dispositivo e de Nuvem**.
1. Insira um nome para o formulário em **Nome do Formulário**.
1. Selecione o número de colunas a ser usado para definir o layout do formulário.
1. Adicione propriedades a uma seção existente no formulário ou selecione propriedades e escolha **Adicionar Seção**. Use seções para agrupar propriedades no formulário. Você pode adicionar um título a uma seção.
1. Configure cada propriedade do formulário para personalizar o comportamento dela.
1. Organize as propriedades no formulário.
1. Salve as alterações.

## <a name="publish-a-device-template"></a>Publicar um modelo de dispositivo

Antes de poder conectar um dispositivo que implementa o modelo de dispositivo, você deve publicar o modelo de dispositivo.

Depois de publicar um modelo de dispositivo, você só pode fazer alterações limitadas no modelo do dispositivo. Para modificar um componente, você precisa [criar e publicar uma nova versão](./howto-version-device-template.md).

Para publicar um modelo de dispositivo, acesse o modelo de dispositivo e selecione **Publicar**.

Depois que você publicar um modelo de dispositivo, um operador poderá acessar a página **Dispositivos** e adicionar dispositivos reais ou simulados que usam o modelo de dispositivo. Você pode continuar a modificar e salvar o modelo de dispositivo enquanto estiver fazendo alterações. Quando desejar enviar essas alterações por push para o operador visualizar na página **Dispositivos**, selecione **Publicar** a cada vez.

## <a name="next-steps"></a>Próximas etapas

Se você é um desenvolvedor de dispositivos, uma próxima etapa sugerida é ler sobre o [controle de versão de modelo de dispositivo](./howto-version-device-template.md).
