---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 3e4549a21ec32f1a2c1c869c3b2e0bd8c2e4204e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446556"
---
## <a name="generate-the-certificate-signing-request-file"></a>Gerar o arquivo de solicitação de assinatura de certificado

O APNs (Serviço de Notificação por Push da Apple) usa certificados para autenticar suas notificações por push. Siga estas instruções para criar o certificado de push necessário para enviar e receber notificações. Para obter mais informações sobre esses conceitos, consulte a documentação oficial do [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Gere o arquivo CSR (Solicitação de Assinatura de Certificado), usado pela Apple para gerar um certificado de push assinado.

1. Em seu Mac, execute a ferramenta Acesso do Conjunto de Chaves. Ela pode ser aberta na pasta **Utilitários** ou na pasta **Outros** no Launchpad.

1. Selecione **Acesso do Conjunto de Chaves**, expanda **Assistente de Certificado** e selecione **Solicitar um Certificado de uma Autoridade de Certificação**.

    ![Usar o Acesso de conjunto de chaves para solicitar um novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. Selecione seu **Endereço de Email do Usuário**, insira o valor do seu **Nome Comum**, verifique se você especificou **Salvo em disco** e, em seguida, selecione **Continuar**. Deixe **Endereço de Email de AC** em branco, pois isso não é necessário.

    ![Informações de certificado necessárias](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Insira um nome para o arquivo CSR em **Salvar Como**, selecione o local em **Onde** e, em seguida, selecione **Salvar**.

    ![Escolher um nome de arquivo para o certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Essa ação salva o arquivo CSR no local selecionado. O local padrão é a **área de trabalho**. Lembre-se do local escolhido para o arquivo.

Em seguida, registre seu aplicativo na Apple, habilite as notificações por push e carregue o CSR exportado para criar um certificado de push.

## <a name="register-your-app-for-push-notifications"></a>Registrar seu aplicativo para notificações por push

Para enviar notificações por push para um aplicativo iOS, registre seu aplicativo na Apple e também registre-se em notificações por push.  

1. Se você ainda não tiver registrado seu aplicativo, navegue até o [Portal de provisionamento iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) no Apple Developer Center. Depois disso, entre com sua Apple ID, selecione **Identificadores**, selecione **IDs do aplicativo** e, por fim, selecione **+** para registrar um novo aplicativo.

    ![Página de IDs do aplicativo do Portal de Provisionamento do iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. Atualize os três valores a seguir para o novo aplicativo e selecione **Continuar**:

   * **Nome**: Digite um nome descritivo para o aplicativo na caixa **Nome**, na seção **Descrição da ID do Aplicativo**.

   * **Identificador de Pacote**: Na seção **ID do Aplicativo Explícita**, digite um **Identificador de Pacote** no formato `<Organization Identifier>.<Product Name>`, como mencionado no [Guia de Distribuição de Aplicativo](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Os valores *Identificador de Organização* e *Nome do Produto* deverão corresponder ao identificador da organização e o nome do produto usados quando você criar seu projeto Xcode. Na captura de tela abaixo, o valor *NotificationHubs* é usado como um identificador de organização e o valor *GetStarted* é usado como o nome do produto. Verifique se o valor de **Identificador de Pacote** corresponde ao valor em seu projeto do Xcode, para que o Xcode use o perfil de publicação correto.

   * **Notificações por Push**: marque a opção **Notificações por Push** na seção **Serviços de Aplicativos**.

     ![Formulário para registrar uma nova ID de aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     Essa ação gerará sua ID do Aplicativo e solicitará que você confirme as informações. Selecione **Registrar** para confirmar a nova ID do Aplicativo.

     Depois de selecionar **Registrar**, você verá a tela **Registro concluído**, conforme mostrado na imagem a seguir. Selecione **Concluído**.

     ![Registro da ID do aplicativo concluído mostrando direitos](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

1. No Centro de Desenvolvedores, em **IDs de aplicativo**, localize a ID do aplicativo que você criou e selecione a respectiva linha.

    ![Lista de IDs do aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Selecione a ID do aplicativo para exibir os detalhes do aplicativo e, em seguida, selecione o botão **Editar** na parte inferior.

    ![Página Editar ID do aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Role até a parte inferior da tela e selecione o botão **Criar Certificado** na seção **Certificado SSL por Push para Desenvolvimento**.

    ![Botão Criar um certificado para aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    O assistente **Adicionar Certificado de iOS** é agora exibido.

    > [!NOTE]
    > Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Use o mesmo tipo de certificado ao enviar notificações.

1. Selecione **Escolher Arquivo**, navegue até o local em que salvou o arquivo CSR da primeira tarefa e selecione **Gerar**.

    ![Página de upload do certificado gerado CSR](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

1. Depois que o portal cria o certificado, selecione o botão **Baixar** e, em seguida, **Concluído**.

    ![Página de download do certificado gerado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    O certificado é baixado e salvo no seu computador, na pasta **Downloads**.

    ![Localize o arquivo do certificado na pasta de Downloads](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Por padrão, o certificado de desenvolvimento baixado é denominado **aps_development.cer**.

1. Selecione o certificado de push baixado, **aps_development.cer**.

    Essa ação instala o novo certificado no conjunto de chaves, conforme mostrado na seguinte imagem:

    ![Lista de certificados de acesso ao conjunto de chaves mostrando o novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Embora o nome em seu certificado possa ser diferente, esse nome será prefixado com **Serviços de Notificação por Push do iOS para Desenvolvimento da Apple**.

1. No Acesso ao Conjunto de Chaves, clique com o botão direito do mouse no novo certificado push criado na categoria **Certificados** . Selecione **Exportar**, nomeie o arquivo, selecione o formato **.p12** e selecione **Salvar**.

    ![Exportar o certificado como formato p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Anote o nome do arquivo e o local do certificado .p12 exportado. Eles são usados para habilitar a autenticação com APNs.

    > [!NOTE]
    > Este tutorial cria um arquivo chamado **QuickStart.p12**. O nome do arquivo e o local podem ser diferentes.

## <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de provisionamento para o aplicativo

1. No [Portal de Provisionamento do iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecione **Perfis de Provisionamento**, selecione **Tudo** e selecione **+** para criar um perfil. É exibido o assistente **Adicionar Perfil de Provisionamento do iOS**.

    ![Lista de perfil de provisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e selecione **Continuar**.

1. Em seguida, selecione a ID do aplicativo que você criou na lista suspensa **ID do Aplicativo** e selecione **Continuar**.

    ![Selecionar a ID do aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Na janela **Selecionar certificados**, selecione seu certificado de desenvolvimento normal usado por assinatura de código e selecione **Continuar**. Esse certificado não é o certificado push que você criou.

    ![Selecionar o certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

1. Em seguida, selecione os dispositivos a serem usados para teste e selecione **Continuar**.

    ![Selecionar os dispositivos](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

1. Por fim, escolha um nome para o perfil em **Nome do Perfil** e selecione **Gerar**.

    ![Escolher um nome do perfil de provisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Quando o novo perfil de provisionamento for criado, escolha baixá-lo e instalá-lo em seu computador de desenvolvimento do Xcode. Em seguida, selecione **Concluído**.

    ![Baixar o perfil de provisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

Nesta seção, você cria um hub de notificação e configura a autenticação com APNs usando o certificado push .p12 que você criou anteriormente. Se você quiser usar um hub de notificação já criado, passe diretamente à etapa 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Configure seu hub de notificação com as informações de APNs

1. Em **Serviços de Notificação**, selecione **Apple (APNS)** .

1. Selecione **Certificado**.

1. Selecione o ícone arquivo.

1. Selecione o arquivo .p12 que você exportou anteriormente.

1. Especifique a senha correta.

1. Selecione o modo **Sandbox**. Use o modo **Produção** apenas se quiser enviar notificações por push aos usuários que adquiriram seu aplicativo na loja.

    ![Configurar certificação de APNs no portal do Azure](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

Agora você configurou seu hub de notificações com APNs. Você também tem as cadeias de conexão para registrar seu aplicativo e enviar notificações por push.
