---
title: 'Tutorial: Criar um aplicativo de monitoramento da qualidade de água com o Azure IoT Central'
description: 'Tutorial: Saiba como criar um aplicativo monitoramento da qualidade da água usando os modelos de aplicativo do Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 3e55970b0603da9be9bf28a50d4c474e34a1cad3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017232"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Tutorial: Criar um aplicativo de monitoramento da qualidade de água no Azure IoT Central

Este tutorial orienta você pela criação de um aplicativo de monitoramento da qualidade da água no Azure IoT Central. Você cria o aplicativo com base no modelo de aplicativo **Monitoramento da Qualidade da Água** do Azure IoT Central.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Usar o modelo **Monitoramento da Qualidade da Água** para criar um aplicativo de monitoramento da qualidade da água.
> * Explorar e personalizar um painel do operador.
> * Explorar um modelo de dispositivo de monitoramento da qualidade da água.
> * Explorar dispositivos simulados.
> * Explorar e configurar regras.
> * Configurar trabalhos.
> * Personalizar a identidade visual do aplicativo usando white labeling.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você tenha uma assinatura do Azure para concluir este tutorial. Se você não tiver uma assinatura do Azure, poderá criar uma na [página de entrada do Azure](https://aka.ms/createazuresubscription).

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Criar um aplicativo de monitoramento da qualidade de água no Azure IoT Central

Nesta seção, você usa o modelo **Monitoramento da Qualidade da Água** do Azure IoT Central para criar um aplicativo de monitoramento da qualidade da água.

1. Acesse a [home page do Azure IoT Central](https://aka.ms/iotcentral).

    Se você tiver uma assinatura do Azure, entre com as credenciais usadas para acessá-la. Caso contrário, entre com uma conta Microsoft:

    ![Entre em sua conta da organização](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Selecione **Compilar** no painel da extrema esquerda no Azure IoT Central e selecione a guia **Governo**. O painel governamental mostra vários modelos de aplicativos governamentais.

    ![Modelos de aplicativos governamentais](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Selecione o modelo de aplicativo **Monitoramento da Qualidade da Água**. Este modelo de aplicativo inclui um modelo de dispositivo de qualidade da água, dispositivos simulados, um painel do operador e regras de monitoramento pré-configuradas.

1. Selecione **Criar aplicativo**. O painel **Novo aplicativo** é aberto e mostra os seguintes elementos:

    * **Nome do aplicativo**: por padrão, o nome do aplicativo é **Monitoramento da Qualidade da Água**, seguido por uma cadeia de caracteres de ID exclusiva gerada pelo Azure IoT Central. Se desejar, você poderá inserir um nome de exibição ou alterar o nome do aplicativo mais tarde.
    * **URL**: você pode inserir qualquer URL que desejar e pode alterar o valor da URL mais tarde.
    * Se tiver uma assinatura do Azure, insira os valores de **Diretório**, **Assinatura do Azure** e **Região**. Se não tiver uma assinatura, você poderá ativar a **Avaliação gratuita de sete dias** e preencher as informações de contato necessárias.

    Para obter mais informações sobre assinaturas e diretórios, consulte o início rápido [Criar um aplicativo](../core/quick-deploy-iot-central.md).

1. Selecione o botão **Criar** na parte inferior esquerda da página.

    ![A página de novo aplicativo do Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![As informações de cobrança do novo aplicativo do Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

Agora, você criou um aplicativo de monitoramento da qualidade da água usando o modelo **Monitoramento da Qualidade da Água** do Azure IoT Central.

Seu novo aplicativo vem com esses componentes pré-configurados:

* Painéis do operador
* Modelos de dispositivo de monitoramento da qualidade da água
* Dispositivos simulados de monitoramento da qualidade da água
* Regras e trabalhos
* Identidade visual que usa white labeling

Você pode modificar seu aplicativo a qualquer momento.

Em seguida, explore o aplicativo e faça algumas personalizações.

## <a name="explore-and-customize-the-operator-dashboard"></a>Explorar e personalizar o painel do operador

Após você criar o aplicativo, o **painel de qualidade da água da Wide World** é aberto.

   ![O painel de monitoramento da qualidade da água](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Como construtor, você pode criar e personalizar modos de exibição no painel para uso pelos operadores. Mas antes de tentar personalizar o painel, explore-o.

Todos os dados mostrados no painel são baseados em dados do dispositivo simulado, que são discutidos na próxima seção.

O painel inclui os seguintes tipos de blocos:

* **Bloco de imagem do sistema de tratamento de água Wide World**: o primeiro bloco no canto superior esquerdo do painel é uma imagem que mostra sistema de tratamento de água fictício chamado Wide World. Você pode personalizar o bloco para usar sua própria imagem ou pode removê-lo.

* **Blocos de KPI de pH médio**: blocos de KPI como **pH médio nos últimos 30 minutos** ficam na parte superior do painel. É possível personalizar os blocos de KPI e configurar cada um deles com tipos e intervalos de tempo diferentes.

* **Mapa da área de monitoramento da água**: o Azure IoT Central usa o Azure Mapas, que você pode configurar diretamente em seu aplicativo para mostrar a localização do dispositivo. Você também pode mapear informações de localização de seu aplicativo para seu dispositivo e, em seguida, usar o Azure Mapas para mostrar as informações em um mapa. Focalize o mapa e experimente os controles.

* **Gráfico com mapa de calor da distribuição do pH médio**: você pode selecionar diferentes gráficos de visualização para mostrar a telemetria do dispositivo da maneira mais adequada para seu aplicativo.

* **Gráfico de linhas dos indicadores críticos de qualidade**: você pode visualizar a telemetria do dispositivo plotada como um gráfico de linhas ao longo de um intervalo de tempo.  

* **Gráfico de barras de concentração de agentes químicos**: você pode visualizar a telemetria do dispositivo em um gráfico de barras.

* **Botão de ação**: o painel inclui um bloco com ações que um operador pode iniciar diretamente do painel de monitoramento. Por exemplo, redefinir as propriedades de um dispositivo.

* **Blocos de lista de propriedades**: o painel tem vários blocos de propriedades que representam informações de limite, informações sobre a integridade do dispositivo e informações de manutenção.

### <a name="customize-the-dashboard"></a>Personalizar o painel

Como construtor, você pode personalizar modos de exibição no painel para uso pelos operadores.

1. Selecione **Editar** para personalizar o **painel de qualidade da água da Wide World**. Você pode personalizar o painel selecionando comandos no menu **Editar**. Quando o painel está no modo de edição, você pode adicionar novos blocos ou configurar os arquivos existentes.

    ![Editar o painel](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Selecione **+ Novo** para criar um painel que você pode configurar. Você pode ter vários painéis e pode navegar entre eles no menu de painel.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Explorar um modelo de dispositivo de monitoramento da qualidade da água

Um modelo de dispositivo no Azure IoT Central define as funcionalidades de um dispositivo. As funcionalidades disponíveis são telemetria, propriedades e comandos. Como construtor, você pode definir modelos de dispositivo no Azure IoT Central que representam as funcionalidades dos dispositivos conectados. Você também pode criar dispositivos simulados para testar seu aplicativo e modelo de dispositivo.

O aplicativo de monitoramento da qualidade da água que você criou tem um modelo de dispositivo de monitoramento da qualidade da água.

Para exibir do modelo de dispositivo:

1. Selecione **Modelos de dispositivo** no painel à extrema esquerda do seu aplicativo no Azure IoT Central.
1. Na lista de modelos de dispositivo, selecione **Monitor de Qualidade da Água**. Esse modelo de dispositivo é aberto.

    ![O modelo de dispositivo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Personalizar o modelo de dispositivo

Pratique a personalização das seguintes configurações de modelo de dispositivo:

1. No menu de modelo de dispositivo, selecione **Personalizar**.
1. Vá para o tipo de telemetria **Temperatura**.
1. Altere o valor do **Nome de exibição** para **Temperatura relatada**.
1. Altere a unidade de medida ou configura o **Valor Mín.** e o **Valor Máx.** .
1. Clique em **Salvar**.

#### <a name="add-a-cloud-property"></a>Adicionar uma propriedade de nuvem

1. No menu de modelo de dispositivo, selecione **Propriedade de nuvem**.
1. Para adicionar uma nova propriedade de nuvem, selecione **+ Adicionar Propriedade de Nuvem**. No Azure IoT Central, você pode adicionar uma propriedade que é relevante para o dispositivo, mas que não se espera que seja enviada por ele. Um exemplo de tal propriedade é um limite de alerta específico para a área de instalação, informações sobre ativos ou informações de manutenção.
1. Clique em **Salvar**.

### <a name="explore-views"></a>Explorar exibições

O modelo de dispositivo de monitoramento da qualidade da água tem exibições predefinidas. As exibições definem como os operadores veem os dados do dispositivo e definem as propriedades da nuvem. Explore as exibições e pratique as alterações.

  ![Exibições do modelo de dispositivo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Se tiver feito alterações, selecione **Publicar** para publicar o modelo de dispositivo.

### <a name="create-a-new-device-template"></a>Criar um novo modelo de dispositivo

1. Selecione **+ Novo** para criar um modelo de dispositivo e siga o processo de criação.
1. Crie um modelo de dispositivo personalizado ou escolha um modelo no catálogo de dispositivos do Azure IoT.

## <a name="explore-simulated-devices"></a>Explorar dispositivos simulados

O aplicativo de monitoramento da qualidade da água que você criou com base no modelo de aplicativo tem dois dispositivos simulados. Esses dispositivos são mapeados para o modelo de dispositivo de monitoramento da qualidade da água.

### <a name="view-the-devices"></a>Exibir os dispositivos

1. Selecione **Dispositivos** no painel à extrema esquerda de seu aplicativo.

   ![Dispositivos](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Selecione um dispositivo simulado.

    ![Selecionar dispositivo 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. Na guia **Propriedades de Nuvem**, altere o valor do **Limite de acidez (pH)** de **8** para **9**.
1. Explore a guia **Propriedades do Dispositivo** e a guia **Painel do Dispositivo**.

> [!NOTE]
> Todas as guias foram configuradas das **Exibições de modelo de dispositivo**.

### <a name="add-new-devices"></a>Adicionar novos dispositivos

Na guia **Dispositivos**, selecione **+ Novo** para adicionar um novo dispositivo.

## <a name="explore-and-configure-rules"></a>Explorar e configurar regras

No Azure IoT Central, você pode criar regras que monitoram automaticamente a telemetria do dispositivo. Essas regras disparam uma ação quando qualquer uma de suas condições é atendida. Uma ação possível é enviar notificações por email. Outras possibilidades incluem uma ação do Power Automate ou um ação de webhook para enviar dados para outros serviços.

O aplicativo de monitoramento da qualidade da água criado tem duas regras pré-configuradas.

### <a name="view-rules"></a>Exibir regras

1. Selecione **Regras** no painel à extrema esquerda de seu aplicativo.

   ![Regras](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Selecione **Alerta de pH alto**, que é uma das regras pré-configuradas no aplicativo.

   ![A regra de alerta de pH alto](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   A regra **Alerta de pH alto** está configurada para verificar a condição da acidez (pH) acima de 8.

Em seguida, adicione uma ação de email à regra:

1. Selecione **+ Email**.
1. Na caixa **Nome de exibição**, digite **Alerta de pH alto**.
1. Na caixa **Para**, insira o endereço de email associado à sua conta do Azure IoT Central.
1. Opcionalmente, insira uma observação a ser incluída no texto do email.
1. Selecione **Concluído** para concluir a ação.
1. Selecione **Salvar** para salvar e ativar a nova regra.

Em alguns minutos, você deverá receber emails quando a condição configurada for atendida.

> [!NOTE]
> O aplicativo envia um email sempre que uma condição é atendida. Selecione **Desabilitar** para uma regra para parar de receber emails automatizados relacionados a ela.
  
Para criar uma regra, selecione **Regras** no painel na extrema esquerda do seu aplicativo e, em seguida, selecione **+Novo**.

## <a name="configure-jobs"></a>Configurar trabalhos

Com os trabalhos do Azure IoT Central, você pode disparar atualizações de propriedades do dispositivo ou da nuvem em vários dispositivos. Você também pode usar trabalhos para disparar comandos de dispositivo em vários dispositivos. O Azure IoT Central automatiza o fluxo de trabalho para você.

1. Selecione **Trabalhos** no painel à extrema esquerda de seu aplicativo.
1. Selecione **+Novo** e configure um ou mais trabalhos.

## <a name="customize-your-application"></a>Personalizar seu aplicativo

Como um criador, você pode alterar várias configurações para personalizar a experiência do usuário em seu aplicativo.

1. Selecione **Administração** > **Personalizar seu aplicativo**.
1. Em **Logotipo do aplicativo**, selecione **Alterar** para escolher a imagem a ser carregada como logotipo.
1. Em **Ícone do navegador**, selecione **Alterar** para escolher a imagem que aparece nas guias do navegador.
1. Em **Cores do navegador**, você pode substituir os valores padrão por códigos de cores hexadecimais HTML.
1. Selecione **Configurações** para alterar o valor do **Tema**.

   ![Personalizar seu aplicativo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>Atualizar a imagem do aplicativo

1. Selecione **Administração** > **Configurações do aplicativo**.

1. Use o botão **Selecionar imagem** para escolher uma imagem a ser carregada como a imagem do aplicativo.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser continuar usando o aplicativo, exclua-o seguindo estas etapas:

1. Abra a guia **Administração** no painel à extrema esquerda de seu aplicativo.
1. Selecione **Configurações do aplicativo** e selecione o botão **Excluir**.

    ![Excluir aplicativo](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre 

> [!div class="nextstepaction"]
> [conceitos de monitoramento da qualidade da água](./concepts-waterqualitymonitoring-architecture.md).
