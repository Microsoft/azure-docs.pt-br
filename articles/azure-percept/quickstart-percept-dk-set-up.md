---
title: Configurar o Azure Percept DK
description: Conectar o kit de desenvolvimento ao Azure e implantar seu primeiro modelo de IA
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 8cecd1b57395fa51fd95c824e88885d9c3ae3f09
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023292"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Configurar o Azure Percept DK e implantar seu primeiro modelo de IA

Conclua a experiência de instalação do Azure Percept DK para configurar seu kit de desenvolvimento e implantar seu primeiro modelo de IA. Depois de verificar se a sua conta do Azure é compatível com o Azure Percept, você vai:

- Conectar seu kit de desenvolvimento a uma rede Wi-Fi
- Configurar um logon SSH para acesso remoto ao kit de desenvolvimento
- Criar um Hub IoT para usá-lo com o Azure Percept
- Conectar o kit de desenvolvimento ao Hub IoT e à conta do Azure

Caso tenha problemas durante este guia de início rápido, veja o [guia de solução de problemas de instalação](./how-to-troubleshoot-setup.md) para obter as possíveis soluções.

## <a name="prerequisites"></a>Pré-requisitos

- Um Azure Percept DK (kit de desenvolvimento).
- Um computador host baseado em Windows, Linux ou OS X com a funcionalidade de Wi-Fi e um navegador da Web.
- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- A conta do Azure precisa ter a função **proprietário** ou **colaborador** na assinatura. Siga as etapas abaixo para verificar sua função da conta do Azure. Para obter mais informações sobre as definições de função do Azure, confira a [documentação do controle de acesso baseado em função do Azure](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).

    > [!CAUTION]
    > Se você tiver várias contas do Azure, seu navegador poderá armazenar em cache as credenciais de outra conta. Para evitar confusão, recomendamos que você feche todas as janelas não usadas do navegador e faça logon no [portal do Azure](https://portal.azure.com/) antes de iniciar a experiência de instalação. Confira o [guia de solução de problemas de instalação](./how-to-troubleshoot-setup.md) para obter mais informações sobre como verificar se está conectado com a conta correta.

### <a name="check-your-azure-account-role"></a>Verificar a função da conta do Azure

Para verificar se a sua conta do Azure é um “proprietário” ou “colaborador” na assinatura, siga estas etapas:

1. Acesse o [portal do Azure](https://portal.azure.com/) e faça logon com a mesma conta do Azure que pretende usar com o Azure Percept.

1. Clique no ícone **Assinaturas** (semelhante a uma chave amarela).

1. Selecione a assinatura na lista. Caso não encontre sua assinatura, verifique se está conectado com a conta do Azure correta. Se quiser criar uma assinatura, siga [estas etapas](../cost-management-billing/manage/create-subscription.md).

1. No menu Assinatura, selecione **Controle de acesso (IAM)** .
1. Clique em **Exibir meu acesso**.
1. Verifique a função:
    - Se a função estiver listada como **Leitor** ou se você receber uma mensagem informando que não tem permissões para ver as funções, você precisará seguir o processo necessário na sua organização para elevar a função da conta.
    - Se a sua função estiver listada como **proprietário** ou **colaborador**, sua conta funcionará com o Azure Percept e você poderá prosseguir com a experiência de instalação.

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Iniciar a experiência de instalação do Azure Percept DK

1. Conecte o computador host diretamente ao ponto de acesso Wi-Fi do kit de desenvolvimento. Assim como a conexão com qualquer outra rede Wi-Fi, abra as configurações de rede e Internet no computador, clique na seguinte rede e insira a senha da rede quando solicitado:

    - **Nome da rede**: dependendo da versão do sistema operacional do kit de desenvolvimento, o nome do ponto de acesso Wi-Fi é **scz-xxxx** ou **apd-xxxx** (em que "xxxx" são os últimos quatro dígitos do endereço MAC do kit de desenvolvimento)
    - **Senha**: pode ser encontrada no Cartão de Boas-vindas que acompanha o kit de desenvolvimento

    > [!WARNING]
    > Enquanto você estiver conectado ao ponto de acesso Wi-Fi do Azure Percept DK, o computador host perderá temporariamente a conexão com a Internet. As chamadas ativas de videoconferência, o streaming da Web ou outras experiências baseadas em rede serão interrompidas.

1. Depois de conectado ao ponto de acesso Wi-Fi do kit de desenvolvimento, o computador host iniciará automaticamente a experiência de instalação em uma nova janela do navegador com **seu.novo.dispositivo/** na barra de endereços. Se a guia não for aberta automaticamente, inicie a experiência de instalação acessando [http://10.1.1.1](http://10.1.1.1). Verifique se o navegador está conectado com as mesmas credenciais de conta do Azure que você pretende usar com o Azure Percept.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Página de boas-vindas.":::

    > [!CAUTION]
    > O serviço Web da experiência de instalação será desligado após 30 minutos de não uso. Se isso acontecer, reinicie o kit de desenvolvimento para evitar problemas de conectividade com o ponto de acesso Wi-Fi do kit.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Concluir a experiência de instalação do Azure Percept DK

1. Clique em **Avançar** na tela de **Boas-vindas**.

1. Na página **Conexão de rede**, clique em **Conectar-se a uma nova rede Wi-Fi**.

    Se você já conectou o kit de desenvolvimento à rede Wi-Fi, clique em **Ignorar**.

1. Selecione a rede Wi-Fi na lista de redes disponíveis e clique em **Conectar**. Insira a senha da rede quando solicitado.

1. Depois que o kit de desenvolvimento se conectar com sucesso à rede de sua escolha, a página mostrará o endereço IPv4 atribuído ao kit de desenvolvimento. **Anote o endereço IPv4 exibido na página.** Você precisará do endereço IP ao se conectar ao kit de desenvolvimento via SSH para solução de problemas e atualizações de dispositivo.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Copiar endereço IP.":::

    > [!NOTE]
    > O endereço IP pode ser alterado em cada inicialização do dispositivo.

1. Leia o Contrato de Licença na íntegra, selecione **Li e concordo com o Contrato de Licença** (é preciso rolar a página para a parte inferior do contrato) e clique em **Avançar**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="Aceitar EULA.":::

1. Insira um nome de conta SSH e uma senha e **anote suas informações de logon para uso posterior**.

    > [!NOTE]
    > O SSH (Secure Shell) é um protocolo de rede que permite que você se conecte ao kit de desenvolvimento remotamente por meio de um computador host.

1. Na próxima página, clique em **Configurar como um novo dispositivo** para criar um dispositivo na sua conta do Azure.

1. Clique em **Copiar** para copiar o código do dispositivo. Depois, clique em **Fazer logon no Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Copiar o código do dispositivo.":::

1. Uma nova guia do navegador será aberta com uma janela indicando **Inserir código**. Cole o código na janela e clique em **Avançar**. NÃO feche a guia **Boas-vindas** com a experiência de instalação.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Inserir o código do dispositivo.":::

1. Entre no Azure Percept usando as credenciais de conta do Azure que você usará com o kit de desenvolvimento. Mantenha a guia do navegador aberta ao concluir.

    > [!CAUTION]
    > Seu navegador pode armazenar outras credenciais em cache automaticamente. Verifique se você está entrando com a conta certa.

    Depois de entrar com sucesso no Azure Percept no dispositivo, volte à guia **Boas-vindas** para continuar a experiência de instalação.

1. Quando a página **Atribuir seu dispositivo ao Hub IoT do Azure** for exibida na guia **Boas-vindas**, execute uma das seguintes ações:

    - Se você já tiver um Hub IoT que deseja usar com o Azure Percept e ele estiver listado nessa página, selecione-o e vá para a etapa 15.
    - Se você não tiver um Hub IoT ou desejar criar um, clique em **Criar um Hub IoT do Azure**.

    > [!IMPORTANT]
    > Se você tiver um Hub IoT, mas ele não aparecer na lista, você poderá ter entrado no Azure Percept com as credenciais erradas. Confira o [guia de solução de problemas de instalação](./how-to-troubleshoot-setup.md) para obter ajuda.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Selecionar um Hub IoT.":::

1. Para criar um Hub IoT, preencha os seguintes campos:

    - Selecione a assinatura do Azure que será usada com o Azure Percept.
    - Selecione um grupo de recursos existente. Se não houver nenhum, clique em **Criar** e siga os avisos.
    - Selecione a região do Azure mais perto da sua localização física.
    - Dê um nome ao novo Hub IoT.
    - Selecione o tipo de preço S1 (Standard).

    > [!NOTE]
    > Se você acabar precisando de uma [taxa de transferência de mensagem](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling#message-throughput) mais alta para seus aplicativos de IA de borda, poderá [atualizar seu Hub IoT para uma camada Standard mais alta](https://docs.microsoft.com/azure/iot-hub/iot-hub-upgrade) no portal do Azure a qualquer momento. As camadas B e F NÃO dão suporte ao Azure Percept.

1. A implantação do Hub IoT pode levar alguns minutos. Quando a implantação for concluída, clique em **Registrar**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="Hub IoT implantado com êxito.":::

1. Insira um nome de dispositivo para o kit de desenvolvimento e clique em **Avançar**.

1. Aguarde até os módulos do dispositivo serem baixados. Isso levará alguns minutos.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="Finalizando a instalação.":::

1. Quando a página **Instalação do dispositivo concluída!** for exibida, o kit de desenvolvimento terá sido vinculado com sucesso ao Hub IoT e baixado o software necessário. Seu kit de desenvolvimento desconectará automaticamente do ponto de acesso Wi-Fi, resultando nestas duas notificações:

    <!---
    > [!NOTE]
    > The onboarding process and connection to the device Wifi access to your host computer shuts down at this point, but your dev kit will stay connected to the internet.   You can restart the onboarding experience with a dev kit reboot, which will allow you to go back through the onboarding and reconnect the device to a different IOT hub associated with the same or a different Azure Subscription..
    --->

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Aviso de desconexão da experiência de instalação.":::

1. Conecte o computador host à rede Wi-Fi à qual o kit de desenvolvimento se conectou na Etapa 2.

1. Clique em **Continuar para o portal do Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Ir para o Azure Percept Studio.":::

## <a name="view-your-dev-kit-video-stream-and-deploy-a-sample-model"></a>Exibir o fluxo de vídeo do kit de desenvolvimento e implantar um modelo de exemplo

1. A [página Visão geral do Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) é o seu ponto de partida para acessar muitos fluxos de trabalho diferentes para o desenvolvimento iniciante e avançado da solução de IA de borda. Para começar, clique em **Dispositivos** no menu à esquerda.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Exibir a lista de dispositivos.":::

1. Verifique se o kit de desenvolvimento está listado como **Conectado** e clique nele para ver a página do dispositivo.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Selecione seu dispositivo.":::

1. Clique em **Exibir o fluxo de dispositivos**. Se esta for a primeira vez que você vê o fluxo de vídeo do seu dispositivo, verá uma notificação no canto superior direito informando que um novo modelo está sendo implantado. Isso pode levar alguns minutos.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Exibir o fluxo de vídeo.":::

    Depois que o modelo for implantado, você receberá outra notificação com um link **Exibir fluxo**. Clique no link para ver o fluxo de vídeo da sua câmera do Azure Percept Vision em uma nova janela do navegador. O kit de desenvolvimento é pré-carregado com um modelo de IA que executa automaticamente a detecção de muitos objetos comuns.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Ver a detecção de objetos.":::

1. O Azure Percept Studio também tem vários modelos de IA de exemplo. Para implantar um modelo de exemplo no seu kit de desenvolvimento, volte à página do dispositivo e clique em **Implantar um modelo de exemplo**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Explorar modelos predefinidos.":::

1. Selecione um modelo de exemplo da biblioteca e clique em **Implantar no dispositivo**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Ver a detecção de objetos em ação.":::

1. Depois que o modelo for implantado com sucesso, você verá uma notificação com um link **Exibir fluxo** no canto superior direito da tela. Para ver a inferência de modelo em ação, clique no link da notificação ou volte à página do dispositivo e clique em **Exibir o fluxo de dispositivos**. Todos os modelos em execução anteriormente no kit de desenvolvimento agora serão substituídos pelo novo modelo.

## <a name="video-walkthrough"></a>Passo a passo em vídeo

Para obter um passo a passo visual das etapas descritas acima, assista ao seguinte vídeo (a experiência de instalação começa em 0:50):

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma solução de pesquisa visual sem código](./tutorial-nocode-vision.md)