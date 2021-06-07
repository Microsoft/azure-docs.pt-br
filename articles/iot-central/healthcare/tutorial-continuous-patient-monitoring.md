---
title: Tutorial – Criar um aplicativo de monitoramento contínuo de pacientes no Azure IoT Central | Microsoft Docs
description: Neste tutorial, você aprenderá criar um aplicativo de monitoramento contínuo de pacientes usando os modelos de aplicativo do Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 56ff43980aafc75d5936b86c6ba2cd400311c5fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101719093"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Tutorial: Implantar e explorar um modelo de aplicativo de monitoramento contínuo de pacientes

Este tutorial mostra como você, como criador de soluções, pode começar com a implantação de um modelo de aplicativo de monitoramento contínuo de pacientes IoT Central. Você aprenderá a implantar e usar o modelo.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um modelo de aplicativo
> * Explorar o modelo de aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do Azure é recomendada. Como alternativa, você pode usar uma avaliação gratuita de sete dias. Se você não tiver uma assinatura do Azure, poderá criar uma na [página de entrada do Azure](https://aka.ms/createazuresubscription).

## <a name="create-an-application-template"></a>Criar um modelo de aplicativo

Navegue até o [site do Gerenciador de Aplicativos do Azure IoT Central](https://apps.azureiotcentral.com/). Selecione **Criar** na barra de navegação à esquerda e selecione a guia **Assistência médica**.

:::image type="content" source="media/app-manager-health.png" alt-text="Modelo de aplicativo de assistência médica":::

Selecione o botão **Criar aplicativo** para começar a criar seu aplicativo e entre com uma conta pessoal, corporativa ou de estudante da Microsoft. Você será levado à página **Novo aplicativo**.

![Criar aplicativo de serviços de saúde](media/app-manager-health-create.png)

![Informações de cobrança de Criar aplicativo de serviços de saúde](media/app-manager-health-create-billinginfo.png)

Para criar seu aplicativo:

1. O Azure IoT Central sugere automaticamente um nome de aplicativo com base no modelo selecionado. Aceite esse nome ou insira seu próprio nome de aplicativo amigável, como **Monitoramento Contínuo de Pacientes**. O Azure IoT Central também gera um prefixo de URL exclusivo para você, com base no nome do aplicativo. Você terá a liberdade para alterar esse prefixo de URL para algo mais fácil de memorizar se desejar.

2. Escolha se deseja criar o aplicativo usando o plano de preços *Gratuito* ou um dos planos de preços *Standard*. Os aplicativos criados com o plano Gratuito são gratuitos por sete dias antes de expirarem e permitem o uso gratuito de até cinco dispositivos. Você pode mover um aplicativo do plano Gratuito para um plano de preços Standard a qualquer momento antes que ele expire. Se você escolher o plano Gratuito, precisará inserir suas informações de contato e escolher se deseja receber informações e dicas da Microsoft. Os aplicativos criados com um plano Standard dão suporte a até dois dispositivos gratuitos e exigem que você insira suas informações de assinatura do Azure para cobrança.

3. Selecione **Criar** no final da página para implantar o aplicativo.

## <a name="walk-through-the-application-template"></a>Explorar o modelo de aplicativo

### <a name="dashboards"></a>Painéis

Depois de implantar o modelo de aplicativo, primeiro, você aterrissará no **painel de monitoramento interno de pacientes da Lamna**. A Lamna Healthcare é um sistema hospitalar fictício que contém dois hospitais: o Woodgrove Hospital e o Burkville Hospital. No painel do operador do Woodgrove Hospital, você pode:

* Ver propriedades e dados de telemetria do dispositivo, como o **nível de bateria** ou o **status de conectividade**.

* Exibir a **planta** e a localização do dispositivo Smart Vitals Patch.

* **Provisionar novamente** o Smart Vitals Patch para um novo paciente.

* Ver um exemplo de **painel do provedor** que a equipe de atendimento de um hospital pode ver para controlar seus pacientes.

* Alterar o **status do paciente** de seu dispositivo para indicar se ele está sendo usado para um cenário interno no paciente ou remoto.

:::image type="content" source="media/lamna-in-patient.png" alt-text="Status interno do paciente":::

Você também pode selecionar **Ir para o painel de paciente remoto** para ver o painel do operador do Burkville Hospital. Esse painel contém um conjunto semelhante de ações, telemetria e informações. Você também pode ver vários dispositivos em uso e escolher **atualizar o firmware** em cada um.

:::image type="content" source="media/lamna-remote.png" alt-text="Painel do operador remoto":::

### <a name="device-templates"></a>Modelos de dispositivo

Se você selecionar **Modelos de dispositivo**, verá os dois tipos de dispositivo no modelo:

* **Smart Vitals Patch**: esse dispositivo representa um adesivo que mede diversos sinais vitais. Ele é usado para monitorar pacientes dentro e fora do hospital. Se você selecionar o modelo, verá que o adesivo envia dados do dispositivo, como o nível da bateria e a temperatura, e dados sobre a saúde do paciente, como a taxa respiratória e a pressão arterial.

* **Smart Knee Brace**: esse dispositivo representa uma joelheira que os pacientes usam ao se recuperarem de uma cirurgia de substituição do joelho. Se você selecionar esse modelo, verá funcionalidades como dados do dispositivo, amplitude de movimento e aceleração.

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="Modelo de adesivo inteligente":::

### <a name="device-groups"></a>Grupos de dispositivos

Use os grupos de dispositivos para agrupar logicamente um conjunto de dispositivos e executar operações ou consultas em massa neles.

Se você selecionar a guia grupos de dispositivos, verá um grupo padrão para cada modelo de dispositivo no aplicativo. Também criamos dois grupos de dispositivos de exemplo adicionais chamados **Provisionar dispositivos** e **Dispositivos com firmware desatualizado**. Você pode usar esses grupos de dispositivos de exemplo como entradas para executar alguns [Trabalhos](#jobs) no aplicativo.

### <a name="rules"></a>Regras

Se você selecionar **Regras**, verá as três regras no modelo:

* **Temperatura alta da cinta**: essa regra é disparada quando a temperatura do Smart Knee Brace é maior que 95&deg;F por um período de cinco minutos. Use essa regra para alertar o paciente e a equipe de atendimento e resfriar o dispositivo remotamente.

* **Detecção de queda**: essa regra será disparada se for detectada uma queda do paciente. Use essa regra para configurar uma ação de acionamento de uma equipe operacional para auxiliar o paciente que caiu.

* **Patch com pouca bateria**: essa regra é disparada quando o nível da bateria do dispositivo é inferior a 10%. Use essa regra para disparar uma notificação ao paciente para que ele carregue o dispositivo.

:::image type="content" source="media/brace-temp-rule.png" alt-text="Regras":::

### <a name="jobs"></a>Trabalhos

Os trabalhos permitem que você execute operações em massa em um conjunto de dispositivos, usando [grupos de dispositivos](#device-groups) como a entrada. O modelo de aplicativo tem dois trabalhos de exemplo que um operador pode executar:

* **Atualizar firmware da cinta de joelho**: esse trabalho encontra dispositivos no grupo **Dispositivos com firmware desatualizado** e executa um comando para atualizar esses dispositivos para a versão de firmware mais recente. Esse trabalho de exemplo pressupõe que os dispositivos podem lidar com um comando de **atualização** e buscar os arquivos de firmware na nuvem.  

* **Provisionar novamente os dispositivos**: você tem um conjunto de dispositivos que foram retornados recentemente para o hospital. Esse trabalho localiza dispositivos no grupo **Provisionar dispositivos** e executa um comando para provisioná-los novamente para o próximo conjunto de pacientes.

### <a name="devices"></a>Dispositivos

Selecione **Dispositivos** e selecione uma instância do **Smart Knee Brace**. Há três exibições para explorar informações sobre o dispositivo específico que você selecionou. Essas exibições são criadas e publicadas ao criar o modelo de dispositivo para seu dispositivo. Portanto, essas exibições serão consistentes em todos os dispositivos que você conectar ou simular.

A exibição de **Painel** fornece uma visão geral da telemetria e das propriedades do dispositivo orientadas pelo operador.

A guia **Propriedades** permite editar propriedades de nuvem e as propriedades de leitura/gravação do dispositivo.

A guia **Comandos** permite executar comandos no dispositivo.

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="Painel de cinta de joelho":::

### <a name="data-export"></a>Exportação de dados

A exportação de dados permite exportar seus dados do dispositivo continuamente para outros serviços do Azure, incluindo a [API do Azure para FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir).

## <a name="clean-up-resources"></a>Limpar os recursos

Caso não pretenda continuar usando esse aplicativo, exclua-o acessando **Administração > Configurações do aplicativo** e clicando em **Excluir**.

:::image type="content" source="media/admin-delete.png" alt-text="Recursos organizados":::

## <a name="next-steps"></a>Próximas etapas

Prossiga para o próximo artigo para saber como criar um painel de provedor que se conecta ao seu aplicativo do IoT Central.

> [!div class="nextstepaction"]
> [Criar um painel do provedor](tutorial-health-data-triage.md)