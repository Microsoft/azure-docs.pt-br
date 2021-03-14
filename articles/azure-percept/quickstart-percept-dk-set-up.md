---
title: Configurar o Azure Percept DK
description: Conectar o kit de desenvolvimento ao Azure e implantar seu primeiro modelo de IA
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/15/2021
ms.custom: template-quickstart
ms.openlocfilehash: 49bf89d38edef6a9186cbdb5bb89a763339385b4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175814"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Configurar o Azure Percept DK e implantar seu primeiro modelo de IA

Comece a usar o Azure Percept DK e o Azure Percept Studio usando a experiência de instalação do Azure Percept DK para conectar seu dispositivo ao Azure e implantar seu primeiro modelo de IA (inteligência artificial). Depois de verificar se a sua conta do Azure é compatível com o Azure Percept Studio, conclua a experiência de instalação a fim de garantir que o Azure Percept DK esteja configurado para criar provas de conceito de IA de borda.

Se você tiver problemas durante este Início Rápido, confira o guia de [solução de problemas](./troubleshoot-dev-kit.md) para obter possíveis soluções.

## <a name="prerequisites"></a>Pré-requisitos

- Um Azure Percept DK.
- Um computador host baseado em Windows, Linux ou OS X com a funcionalidade de Wi-Fi e um navegador da Web.
- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- A conta do Azure deve ter a função "proprietário" ou "colaborador" na assinatura. Saiba mais sobre [definições de função do Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles#azure-roles).

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

Para verificar se a sua conta do Azure é um "proprietário" ou "colaborador" na assinatura, siga estas etapas.

1. Acesse o portal do Azure e faça logon com a mesma conta do Azure que você pretende usar com o Azure Percept Studio. 

    > [!NOTE]
    > Se você tiver várias contas do Azure, seu navegador poderá armazenar em cache as credenciais de outra conta. Confira o guia de solução de problemas para obter mais informações sobre como garantir que você esteja conectado com a conta certa.

1. Expanda o menu principal no canto superior esquerdo da tela e clique em "Assinaturas" ou selecione "Assinaturas" no menu de ícones na home page. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-01-subscription.png" alt-text="supscription icon in Azure portal.":::
    --->
1. Selecione a assinatura na lista. Se não encontrar sua assinatura na lista, verifique se você está conectado com a conta do Azure correta. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-02-sub-list.png" alt-text="supscription list in Azure portal.":::
    --->
Se quiser criar uma assinatura, siga [estas etapas](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription).

1. No menu Assinatura, selecione "Controle de acesso (IAM)"
1. Clique no botão "Exibir meu acesso"
1. Verifique a função
    - Se for exibida a função de "Leitor" ou se você receber uma mensagem informando que não tem permissões para ver as funções, você precisará seguir o processo necessário em sua organização para que a função da conta seja elevada.
    - Se for exibida a função de "proprietário" ou "colaborador", sua conta funcionará com o Azure Percept Studio. 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Iniciar a experiência de instalação do Azure Percept DK

<!---
> [!NOTE]
> Connecting over ethernet? See [this how-to guide](<link needed>) for detailed instructions.
--->
1. Conecte o computador host diretamente ao ponto de acesso Wi-Fi do kit de desenvolvimento. Isso é feito da mesma forma que a conexão a qualquer outra rede Wi-Fi,
    - **nome da rede**: scz-xxxx (em que "xxxx" são os últimos quatro dígitos do endereço de rede MAC do kit de desenvolvimento)
    - **senha**: pode ser encontrada no Cartão de boas-vindas que acompanha o kit de desenvolvimento

    > [!WARNING]
    > Enquanto estiver conectado ao ponto de acesso Wi-Fi do Azure Percept DK, seu computador host perderá temporariamente a conexão com a Internet. Chamadas de conferência de vídeo ativas, streaming pela Web ou outras experiências baseadas em rede serão interrompidas até que a etapa 3 da experiência de instalação do Azure Percept DK seja concluída.

1. Uma vez conectado ao ponto de acesso Wi-Fi do kit de desenvolvimento, o dispositivo host iniciará automaticamente a experiência de instalação do Azure Percept DK em uma nova janela do navegador. Se ela não abrir automaticamente, você poderá iniciá-la de forma manual abrindo uma janela do navegador e navegando até [http://10.1.1.1](http://10.1.1.1). 

1. Agora que você iniciou a experiência de instalação do Azure Percept, já pode prosseguir com ela. 

    > [!NOTE]
    > O serviço Web de experiência de instalação do Azure Percept DK será encerrado após 30 minutos de inatividade e na conclusão da experiência de instalação. Quando isso acontece, é recomendável reiniciar o kit de desenvolvimento para evitar problemas de conectividade com o ponto de acesso Wi-Fi do kit.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Concluir a experiência de instalação do Azure Percept DK

1. Começar – Clique em **Avançar** na tela de boas-vindas.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Página de boas-vindas.":::

1. Conectar-se ao Wi-Fi – Na página Conexão de rede, clique em **Conectar-se a uma nova rede Wi-Fi** para conectar o kit de desenvolvimento à rede Wi-Fi.

    Se você tiver conectado anteriormente esse kit de desenvolvimento à rede Wi-Fi ou se você já estiver conectado à experiência de instalação do Azure Percept DK pela rede Wi-Fi, clique no link **Ignorar**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-02-connect-to-wi-fi.png" alt-text="Conectar ao Wi-Fi.":::

1. Selecione a rede Wi-Fi nas conexões disponíveis e conecte-se. (Isso exigirá sua senha de Wi-Fi local)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-03-select-wi-fi.png" alt-text="Selecionar rede Wi-Fi."::: 

1. Copiar o endereço IP – Depois que o kit de desenvolvimento estiver conectado com êxito à sua rede preferencial, anote o **endereço IPv4** exibido na página. **Esse endereço IP será necessário posteriormente neste guia de início rápido**. 

    > [!NOTE]
    > O endereço IP pode ser alterado em cada inicialização do dispositivo.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Copiar endereço IP.":::

1. Examinar e aceitar o Contrato de Licença – Leia o Contrato de Licença, selecione **Li e concordo com o Contrato de Licença** (é preciso rolar para a parte inferior do contrato) e clique em **Avançar**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="Aceitar EULA.":::

1. Criar sua conta de logon SSH – Configure o SSH para acesso remoto ao seu kit de desenvolvimento. Crie um nome de usuário e uma senha SSH. 

    > [!NOTE]
    > O SSH (Secure Shell) é um protocolo de rede usado para a comunicação segura entre o dispositivo host e o kit de desenvolvimento. Confira mais informações sobre SSH [neste artigo](https://en.wikipedia.org/wiki/SSH_(Secure_Shell)).

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-06-ssh-login.png" alt-text="Criar conta SSH."::: 

1. Iniciar o processo de conexão do kit de desenvolvimento – Na próxima tela, clique em **Conectar com um novo dispositivo** para iniciar o processo de conexão do kit de desenvolvimento ao Hub IoT do Azure. 

    <!---
    Connecting with an existing IoT Edge device connection string? See this [how-to guide](<link needed>) for reference.
    --->
    :::image type="content" source="./media/quickstart-percept-dk-setup/main-07-connect-device.png" alt-text="Conectar-se ao Azure."::: 

1. Copiar o código do dispositivo – Clique no link **Copiar** para copiar o código do dispositivo. Em seguida, clique em **Fazer logon no Azure**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Copiar o código do dispositivo."::: 

1. Colar o código do dispositivo – Uma nova guia do navegador será aberta com uma janela que solicita o código de dispositivo copiado. Cole o código na janela e clique em **Avançar**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Inserir o código do dispositivo."::: 

1. Entrar no Azure – A próxima janela solicita que você entre com a conta do Azure verificada no começo do Início Rápido. Insira essas credenciais de logon e clique em **Avançar**. 

    > [!NOTE]
    > Seu navegador pode armazenar outras credenciais em cache automaticamente. Verifique se você está entrando com a conta certa.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-10-azure-sign-in.png" alt-text="Entrar no Azure.":::
 
1. Não feche a guia do navegador da experiência de instalação nesta etapa – Depois de entrar, você verá uma tela confirmando que está conectado. Embora isso indique que você pode fechar a janela, **recomendamos que não feche nenhuma janela**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-11-sign-in-success.png" alt-text="Entrada bem-sucedida."::: 

1. Retorne à guia do navegador que hospeda a experiência de instalação.
1. Selecione sua opção de Hub IoT
    - Se você já tiver um Hub IoT e ele estiver listado nesta página, você poderá selecioná-lo e **ir para a etapa 17**.
    - Se você não tiver um Hub IoT ou desejar criar um, vá para a parte inferior da lista e clique em **Criar um Hub IoT do Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Selecionar um Hub IoT."::: 

1. Criar um Hub IoT – Preencha todos os campos nesta página. 
    - Selecione a assinatura do Azure que será usada com o Azure Percept Studio
    - Selecione um grupo de recursos existente. Se não houver um, clique em "Criar" e siga os prompts. 
    - Selecione uma região do Azure. 
    - Dê um nome ao novo Hub IoT
    - Selecione o tipo de preço (normalmente, ele corresponderá à assinatura)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-14-create-iot-hub.png" alt-text="Criar um Hub IoT."::: 

1. Aguardar até que o Hub IoT seja implantado – isso pode levar alguns minutos

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-15-iot-hub-deploy.png" alt-text="Implantar o Hub IoT."::: 

1. Registrar o kit de desenvolvimento – Quando a implantação for concluída, clique no botão **Registrar**

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="Hub IoT implantado com êxito."::: 

1. Dar um nome ao kit de desenvolvimento – Insira um nome de dispositivo para o kit de desenvolvimento e clique em **Avançar**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-17-device-name.png" alt-text="Dar nome ao dispositivo."::: 

1. Aguardar até que os modelos de IA padrão sejam baixados – isso levará alguns minutos

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="Finalizando a instalação."::: 

1. Conferir a IA do Vision em ação – O kit de desenvolvimento foi vinculado com êxito ao Hub IoT do Azure e recebeu o modelo padrão de detecção de objetos da IA do Vision. A câmera do Azure Percept Vision agora pode fazer a inferência de detecção de objetos sem uma conexão com a nuvem. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-2-preview-video-output.png" alt-text="Clicar em Visualizar Saída de Vídeo."::: 
       
    > [!NOTE]
    > O processo de integração e a conexão com o acesso Wi-Fi do dispositivo ao computador host são encerrados neste ponto, mas o kit de desenvolvimento permanecerá conectado à Internet.   Você pode reiniciar a experiência de integração com uma reinicialização do kit de desenvolvimento, o que permitirá voltar à integração e reconectar o dispositivo a um hub IoT diferente associado à mesma ou a outra assinatura do Azure.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Aviso de desconexão da experiência de instalação."::: 

1. Seguir para o portal do Azure – Volte para a janela de experiência de instalação e clique no botão **Continuar no portal do Azure** para começar a criar seus modelos de IA personalizados no Azure Percept Studio.

    > [!NOTE]
    > Verifique se o computador host não está mais conectado ao ponto de acesso do kit de desenvolvimento nas configurações de Wi-Fi e agora está reconectado ao Wi-Fi local.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Ir para o Azure Percept Studio."::: 

## <a name="view-your-device-in-the-azure-percept-studio-and-deploy-common-prebuilt-sample-apps"></a>Exiba seu dispositivo no Azure Percept Studio e implante aplicativos de exemplo predefinidos comuns

1. Exiba sua lista de dispositivos na página Visão geral do [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). A página Visão geral do Azure Percept é seu ponto de partida para acessar vários fluxos de trabalho diferentes para o Desenvolvimento de solução e modelo de borda de IA inicial e avançado

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Exibir a lista de dispositivos.":::
    
1. Verifique se o dispositivo que você acabou de criar está conectado e clique nele.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Selecione seu dispositivo.":::

1. Exiba o fluxo de dispositivos para ver o que a câmera do kit de desenvolvimento está capturando. Um modelo de detecção de objetos padrão já vem implantado e detecta vários objetos comuns.

    > [!NOTE]
    > Na primeira vez que você fizer isso em um novo dispositivo, receberá uma notificação de que um novo módulo está sendo implantado no canto superior direito.  Depois que isso for concluído, você poderá iniciar a janela com o fluxo de vídeo da câmera. 
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Exibir o fluxo de vídeo.":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Ver a detecção de objetos.":::

1. Explore os modelos de IA de exemplo predefinidos.   O Azure Precept Studio tem várias amostras predefinidas comuns que podem ser implantadas com um único clique.   Selecione "Implantar um modelo de exemplo" para exibi-lo e implantá-lo.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Explorar modelos predefinidos.":::
    
1. Implante uma nova amostra predefinida em seu dispositivo conectado. Selecione uma amostra da biblioteca e clique em "Implantar no Dispositivo"

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Ver a detecção de objetos em ação.":::

## <a name="next-steps"></a>Próximas etapas

Você pode seguir um fluxo semelhante para experimentar [modelos de fala predefinidos](./tutorial-no-code-speech.md).