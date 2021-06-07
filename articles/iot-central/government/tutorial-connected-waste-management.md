---
title: 'Tutorial: Criar um aplicativo de gestão de resíduos conectada com o Azure IoT Central'
description: 'Tutorial: Saiba como criar um aplicativo de gestão de resíduos conectado usando modelos de aplicativo do Azure IoT Central'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: ff7cfb8c7aa8469111d4531da17c7cd184920f9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727559"
---
# <a name="tutorial-create-a-connected-waste-management-app"></a>Tutorial: Criar um aplicativo de gerenciamento de resíduos conectado

Este tutorial mostra como usar o Azure IoT Central para criar um aplicativo de gestão de resíduos conectada. 

Especificamente, você aprenderá como: 

> [!div class="checklist"]
> * usar o modelo de *Gestão de resíduos conectada* do Azure IoT Central para criar seu aplicativo;
> * Explorar e personalizar o painel do operador. 
> * explorar um modelo de dispositivo de recipiente para resíduos conectado;
> * Explorar dispositivos simulados.
> * Explorar e configurar regras.
> * Configurar trabalhos.
> * personalizar a identidade visual do aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do Azure é recomendada. Como alternativa, você pode usar uma avaliação gratuita de sete dias. Se você não tiver uma assinatura do Azure, poderá criar uma na [página de entrada do Azure](https://aka.ms/createazuresubscription).

## <a name="create-your-app-in-azure-iot-central"></a>Criar o aplicativo no Azure IoT Central

Nessa seção, você usará o modelo de Gestão de resíduos conectada para criar o aplicativo no Azure IoT Central. Aqui está como:

1. Acesse o [Azure IoT Central](https://aka.ms/iotcentral).

    Se você tiver uma assinatura do Azure, entre com as credenciais usadas para acessá-la. Caso contrário, entre usando uma conta Microsoft:

    ![Captura de tela da entrada da Microsoft.](./media/tutorial-connectedwastemanagement/sign-in.png)

1. No painel esquerdo, selecione **Criar**. Em seguida, selecione a guia **Governo**. A página governamental exibe vários modelos de aplicativos governamentais.

    ![Captura de tela da página de criação do Azure IoT Central.](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Selecione o modelo de aplicativo **Gestão de resíduos conectada**. Este modelo inclui um exemplo de modelo de dispositivo de recipiente para resíduos conectado, um dispositivo simulado, um painel do operador e regras de monitoramento pré-configuradas.    

1. Selecione **Criar aplicativo**, que abre a caixa de diálogo **Novo aplicativo**. Preencha as informações para os seguintes campos:
    * **Nome do aplicativo**. Por padrão, o aplicativo usa o nome **Gestão de resíduos conectada** seguido por uma cadeia de caracteres de ID exclusiva gerada pelo Azure IoT Central. Opcionalmente, escolha um nome de aplicativo amigável. Você também pode alterar o nome do aplicativo mais tarde.
    * **URL**. Opcionalmente, escolha a URL desejada. Você também pode alterar a URL mais tarde. 
    * **Plano de preços**. Se você tiver uma assinatura do Azure, insira seu diretório, a assinatura do Azure e a região nos campos apropriados da caixa de diálogo **Informações de cobrança**. Caso não tenha uma assinatura, selecione **Gratuito** para habilitar a assinatura de avaliação gratuita de sete dias e preencha as informações de contato necessárias.  

    Para obter mais informações sobre assinaturas e diretórios, confira [Início Rápido: Criar um aplicativo do Azure IoT Central](../core/quick-deploy-iot-central.md).

1. Selecione **Criar** na parte inferior da página. 

    ![Captura de tela da caixa de diálogo Criar aplicativo do Azure IoT Central.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Captura de tela da caixa de diálogo Informações de cobrança do Azure IoT Central.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
Seu aplicativo recém-criado tem os seguintes itens pré-configurados:
* Exemplos de painéis do operador.
* exemplo de modelos de dispositivo de recipiente para resíduos conectado predefinido;
* dispositivos simulados conectados de recipiente para resíduos;
* Regras e trabalhos.
* identidade visual de exemplo. 

O aplicativo é seu, e você pode modificá-lo a qualquer momento. Agora, vamos explorar o aplicativo e fazer algumas personalizações.  

## <a name="explore-and-customize-the-operator-dashboard"></a>Explorar e personalizar o painel do operador 

Dê uma olhada no **Painel de gestão de resíduos da Wide World**, que você vê depois de criar o aplicativo.

   ![Captura de tela do painel de gestão de resíduos da Wide World.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Como construtor, você pode criar e personalizar modos de exibição no painel para uso dos operadores. Primeiro, vamos explorar o painel. 

>[!NOTE]
>Todos os dados mostrados no painel são baseados em dados do dispositivo simulado, que são discutidos na próxima seção. 

O painel é composto por diferentes blocos:

* **Bloco de imagem da concessionária Wide World Waste**: o primeiro bloco do painel é um bloco de imagem da concessionária de abastecimento de água fictícia, "Wide World Waste". Você pode personalizar o bloco e colocar sua própria imagem ou removê-la. 

* **Bloco de imagem do recipiente para resíduos**: você pode usar blocos de imagem e conteúdo para criar uma representação visual do dispositivo que está sendo monitorado, junto com uma descrição. 

* **Bloco KPI de nível de preenchimento**: o bloco exibe um valor relatado por um sensor de *nível de preenchimento* em um recipiente para resíduos. O sensor de nível de preenchimento e outros sensores em um recipiente para resíduos, como *medidor de odor* ou *peso*, podem ser monitorados remotamente. Um operador pode tomar medidas, como despachar um caminhão de coleta de lixo. 

* **Mapa da área de monitoramento de resíduos**: essa peça usa o Azure Mapas, que pode ser configurado diretamente no Azure IoT Central. A peça de mapa exibe a localização do dispositivo. Tente focalizar o mapa e experimente os controles sobre ele, como ampliar, reduzir ou expandir.

     ![Captura de tela do mapa do painel do modelo de Gestão de Resíduos Conectada.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* **Gráfico de barras de nível de preenchimento, odor e peso**: você pode visualizar um ou vários tipos de dados de telemetria de dispositivo em um gráfico de barras. Você também pode expandir o gráfico de barras.  

  ![Captura de tela do gráfico de barras do painel do modelo de Gestão de Resíduos Conectada.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Field Services**: o painel inclui um link para integração com o Dynamics 365 Field Services de seu aplicativo do Azure IoT Central. Por exemplo, você pode usar o Field Services para criar tíquetes para expedição de serviços de coleta de lixo. 


### <a name="customize-the-dashboard"></a>Personalizar o painel 

Personalize o painel selecionando o menu **Editar**. Em seguida, você pode adicionar novos blocos ou configurar os existentes. Veja a aparência do painel no modo de edição: 

![Captura de tela do painel do modelo de Gestão de Resíduos Conectada no modo de edição.](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

Você também pode selecionar **+ Novo** para criar um painel e configurá-lo do zero. Você pode ter vários painéis e pode alternar entre eles no menu de painel. 

## <a name="explore-the-device-template"></a>Explorar o modelo de dispositivo

Um modelo de dispositivo no Azure IoT Central define as funcionalidades de um dispositivo, que pode incluir telemetria, propriedades ou comandos. Como construtor, você pode definir modelos de dispositivo que representam as funcionalidades dos dispositivos que você conectará. 

O aplicativo de Gestão de resíduos conectada tem um exemplo de modelo de dispositivo de recipiente para resíduos conectado.

Para exibir do modelo de dispositivo:

1. No Azure IoT Central, no painel esquerdo do aplicativo, selecione **Modelos de dispositivo**. 

    ![Captura de tela mostrando a lista de modelos de dispositivo no aplicativo.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. Na lista **Modelos de dispositivo**, selecione **Recipiente para Resíduos Conectado**.

1. Examine as funcionalidades do modelo de dispositivo. Você pode ver que ele define sensores como **Nível de preenchimento**, **Medidor de odor**, **Peso** e **Localização**.

   ![Captura de tela mostrando os detalhes do modelo de dispositivo Recipiente para Resíduos Conectado.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


### <a name="customize-the-device-template"></a>Personalizar o modelo de dispositivo

Tente personalizar o seguinte:
1. No menu de modelo de dispositivo, selecione **Personalizar**.
1. Localize o tipo de telemetria **Medidor de odor**.
1. Atualize o **Nome de exibição** do **Medidor de odor** para **Nível de odor**.
1. Tente atualizar a unidade de medida ou defina o **Valor mín.** e o **Valor máx.** .
1. Clique em **Salvar**. 

### <a name="add-a-cloud-property"></a>Adicionar uma propriedade de nuvem 

Aqui está como:
1. No menu de modelo de dispositivo, selecione **Propriedade de nuvem**.
1. Selecione **+ Adicionar Propriedade de Nuvem**. No Azure IoT Central, você pode adicionar uma propriedade que é relevante para o dispositivo, mas que não se espera que seja enviada por ele. Por exemplo, uma propriedade de nuvem poderia ser um limite de alerta específico para a área de instalação, informações sobre ativos ou informações de manutenção. 
1. Clique em **Salvar**. 
 
### <a name="views"></a>Exibições 
O modelo de dispositivo de recipiente para resíduos conectado inclui exibições predefinidas. Explore as exibições e atualize-as se desejar. As exibições definem como os operadores veem os dados do dispositivo e definem as propriedades da nuvem. 

  ![Captura de tela das exibições de modelos de dispositivo do modelo de Gestão de Resíduos Conectada.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publicar 

Caso tenha feito alguma alteração, lembre-se de publicar o modelo de dispositivo. 

### <a name="create-a-new-device-template"></a>Criar um novo modelo de dispositivo 

Para criar um modelo de dispositivo, selecione **+ Novo** e siga as etapas. Você pode criar um modelo de dispositivo personalizado do zero ou escolher um modelo de dispositivo no catálogo de dispositivos do Azure. 

## <a name="explore-simulated-devices"></a>Explorar dispositivos simulados

No Azure IoT Central, você também pode criar dispositivos simulados para testar o aplicativo e o modelo de dispositivo. 

O aplicativo de Gestão de resíduos conectada tem dois dispositivos simulados associados com o modelo de dispositivo de recipiente para resíduos conectado. 

### <a name="view-the-devices"></a>Exibir os dispositivos

1. No painel esquerdo do Azure IoT Central, selecione **Dispositivo**. 

   ![Captura de tela dos dispositivos do modelo de Gestão de Resíduos Conectada.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. Selecione o dispositivo **Recipiente para Resíduos Conectado**.  

     ![Captura de tela das propriedades do dispositivo do modelo de Gestão de Resíduos Conectada.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

1. Acesse a guia **Propriedades de Nuvem**. Atualize o valor de **Limite de alerta de recipiente cheio** de **95** para **100**. 

Explore as guias **Propriedades do Dispositivo** e **Painel do Dispositivo**. 

> [!NOTE]
> Todas as guias foram configuradas nas exibições de modelo de dispositivo.

### <a name="add-new-devices"></a>Adicionar novos dispositivos

É possível adicionar novos dispositivos selecionando **+ Novo** na guia **Dispositivos**. 

## <a name="explore-and-configure-rules"></a>Explorar e configurar regras

No Azure IoT Central, você poderá criar regras para monitorar automaticamente a telemetria do dispositivo e disparar ações quando uma ou mais condições forem atendidas. As ações podem incluir o envio de notificações por email, o disparo de uma ação do Microsoft Power Automate ou o disparo de uma ação de webhook para enviar dados a outros serviços.

O aplicativo de Gestão de resíduos conectada tem quatro regras de exemplo.

### <a name="view-rules"></a>Exibir regras
1. No painel esquerdo do Azure IoT Central, selecione **Regras**.

   ![Captura de tela das regras do modelo de Gestão de Resíduos Conectada.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. Selecione **Alerta de recipiente cheio**.

     ![Captura de tela do Alerta de recipiente cheio.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 1. O **Alerta de recipiente cheio** verifica a seguinte condição: **O nível de preenchimento é maior ou igual ao Limite de alerta de recipiente cheio**.

    O **Limite de recipiente cheio** é uma propriedade de nuvem que é definida no modelo de dispositivo de recipiente para resíduos conectado. 

Agora, vamos criar uma ação de email.

### <a name="create-an-email-action"></a>Criar uma ação de email

É possível configurar uma ação de email na lista **Ações** da regra:
1. Selecione **+ Email**. 
1. Em **Nome de exibição**, insira **Alerta de pH alto**.
1. Em **Para**, insira o endereço de email associado à sua conta do Azure IoT Central. 
1. Opcionalmente, insira uma observação a ser incluída no texto do email.
1. Selecione **Concluído** > **Salvar**. 

Você receberá um email quando a condição configurada for atendida.

>[!NOTE]
>O aplicativo envia um email sempre que uma condição é atendida. Desabilite a regra para parar de receber emails da regra automatizada. 
  
Para criar uma regra, no painel esquerdo de **Regras**, selecione **+ Novo**.

## <a name="configure-jobs"></a>Configurar trabalhos

No Azure IoT Central, você pode disparar atualizações de propriedades do dispositivo ou da nuvem em vários dispositivos. Você também pode usar trabalhos para disparar comandos de dispositivo em vários dispositivos. O Azure IoT Central automatiza o fluxo de trabalho para você. 

1. No painel esquerdo do Azure IoT Central, selecione **Trabalhos**. 
1. Selecione **+ Novo** e configure um ou mais trabalhos. 

## <a name="customize-your-application"></a>Personalizar seu aplicativo 

Como um criador, você pode alterar várias configurações para personalizar a experiência do usuário em seu aplicativo.

### <a name="change-the-application-theme"></a>Alterar o tema do aplicativo

Aqui está como:
1. Acesse **Administração** > **Personalizar seu aplicativo**.
1. Use o botão **Alterar** para escolher uma imagem a ser carregada como o **Logotipo do aplicativo**.
1. Selecione **Alterar** para escolher uma imagem a ser carregada para o **ícone do Navegador** (uma imagem que aparecerá nas guias do navegador).
1. Você também pode substituir as cores padrão do navegador adicionando códigos de cores hexadecimais HTML. Use os campos **Cabeçalho** e **Ênfase** para essa finalidade.

   ![Captura de tela de Personalizar seu aplicativo no Modelo de Gestão de Resíduos conectado.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. Você também pode alterar as imagens do aplicativo. Selecione **Administração** > **Configurações do aplicativo** > **Selecionar imagem** para escolher uma imagem a ser carregada como a imagem do aplicativo.
1. Por fim, você também pode alterar o tema clicando em **Configurações** na manchete do aplicativo.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser continuar usando o aplicativo, exclua-o seguindo estas etapas:

1. Selecione **Administração** no painel esquerdo do seu aplicativo do Azure IoT Central.
1. Selecione **Configurações do aplicativo** > **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conceitos de gestão de resíduos conectada](./concepts-connectedwastemanagement-architecture.md)
