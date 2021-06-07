---
title: incluir arquivo
description: incluir arquivo
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 7b5034f2163e8478d7ddb7b9271402b094a809d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95554816"
---
## <a name="generate-the-certificate-signing-request-file"></a>Gerar o arquivo de solicitação de assinatura de certificado

O APNs (Serviço de Notificação por Push da Apple) usa certificados para autenticar suas notificações por push. Siga estas instruções para criar o certificado de push necessário para enviar e receber notificações. Para obter mais informações sobre esses conceitos, consulte a documentação oficial do [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Gere o arquivo CSR (Solicitação de Assinatura de Certificado), usado pela Apple para gerar um certificado de push assinado.

1. Em seu Mac, execute a ferramenta Acesso do Conjunto de Chaves. Ela pode ser aberta na pasta **Utilitários** ou na pasta **Outros** no Launchpad.

1. Selecione **Acesso do Conjunto de Chaves**, expanda **Assistente de Certificado** e selecione **Solicitar um Certificado de uma Autoridade de Certificação**.

    ![Usar o Acesso de conjunto de chaves para solicitar um novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Por padrão, o Acesso às Chaves seleciona o primeiro item na lista. Isso pode ser um problema se você estiver na categoria **Certificados** e a **Autoridade de Certificação de Relacionamentos do Desenvolvedor Mundial da Apple** não for o primeiro item da lista. Verifique se você tem um item não chave ou se a chave **Autoridade de Certificação de Relacionamentos do Desenvolvedor Mundial da Apple** está selecionada antes de gerar o CSR (Solicitação de Assinatura de Certificado).

1. Selecione seu **Endereço de Email do Usuário**, insira o valor do seu **Nome Comum**, verifique se você especificou **Salvo em disco** e, em seguida, selecione **Continuar**. Deixe **Endereço de Email de AC** em branco, pois isso não é necessário.

    ![Informações de certificado necessárias](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Insira um nome para o arquivo CSR em **Salvar Como**, selecione o local em **Onde** e, em seguida, selecione **Salvar**.

    ![Escolher um nome de arquivo para o certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Essa ação salva o arquivo CSR no local selecionado. O local padrão é a **área de trabalho**. Lembre-se do local escolhido para o arquivo.

Em seguida, registre seu aplicativo na Apple, habilite as notificações por push e carregue o CSR exportado para criar um certificado de push.

## <a name="register-your-app-for-push-notifications"></a>Registrar seu aplicativo para notificações por push

Para enviar notificações por push para um aplicativo iOS, registre seu aplicativo na Apple e também registre-se para notificações por push.  

1. Se você ainda não tiver registrado seu aplicativo, navegue até o [Portal de provisionamento iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) no Apple Developer Center. Conecte-se ao portal com sua ID da Apple e selecione **Identificadores**. Em seguida, selecione **+** para registrar um novo aplicativo.

    ![Página de IDs do aplicativo do Portal de Provisionamento do iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Na tela **Registrar um Novo Identificador**, selecione o botão de opção **IDs do Aplicativo**. Depois selecione **Continuar**.

    ![Página de registro de nova ID no Portal de Provisionamento do iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Atualize os três valores a seguir para o novo aplicativo e selecione **Continuar**:

   * **Descrição**: Digite um nome descritivo para o seu aplicativo.

   * **ID do Pacote**: Digite uma ID do Pacote no formulário **Identificador da Organização.Nome do Produto** conforme mencionado no [Guia de Distribuição de Aplicativos](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Os valores *Identificador de Organização* e *Nome do Produto* deverão corresponder ao identificador da organização e o nome do produto usados quando você criar seu projeto Xcode. Na captura de tela abaixo, o valor **NotificationHubs** é usado como um identificador de organização e o valor **GetStarted** é usado como o nome do produto. Verifique se o valor de **Identificador de Pacote** corresponde ao valor em seu projeto do Xcode, para que o Xcode use o perfil de publicação correto.

      ![Página de registro da ID do aplicativo no Portal de Provisionamento do iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Notificações por Push**: Marque a opção **Notificações por Push** na seção **Funcionalidades**.

      ![Formulário para registrar uma nova ID de aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Essa ação gerará sua ID do Aplicativo e solicitará que você confirme as informações. Selecione **Continuar** e, em seguida, selecione **Registrar** para confirmar a nova ID do Aplicativo.

      ![Confirmar nova ID do Aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Depois de selecionar **Registrar**, você verá a nova ID do Aplicativo como um item de linha na página **Certificados, Identificadores e Perfis**.

4. Na página **Certificados, Identificadores e Perfis**, em **Identificadores**, localize o item de linha da ID do Aplicativo que você acabou de criar e selecione sua linha para exibir a tela **Editar sua Configuração de ID do Aplicativo**.

## <a name="creating-a-certificate-for-notification-hubs"></a>Criar um certificado para os Hubs de Notificação
Um certificado é necessário para habilitar que o hub de notificação funcione com **APNs**. Isso pode ser feito de duas maneiras:

1. Crie um **.p12** que possa ser carregado diretamente no Hub de Notificação.  
2. Crie um **.p8** que possa ser usado para a [autenticação baseada em token](../articles/notification-hubs/notification-hubs-push-notification-http2-token-authentication.md) (*abordagem mais recente*).

A abordagem mais recente tem vários benefícios (em comparação ao uso de certificados), conforme documentado em [Autenticação baseada em token (HTTP/2) para APNs](../articles/notification-hubs/notification-hubs-push-notification-http2-token-authentication.md). No entanto, foram fornecidas etapas para ambas as abordagens. 

### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>OPÇÃO UM: Crie um certificado push .p12 que possa ser carregado diretamente no Hub de Notificação

1. Role para baixo até a opção marcada **Notificações por Push** e, em seguida, selecione **Configurar** para criar o certificado.

    ![Página Editar ID do aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

2. A janela **Certificados SSL do Apple Push Notification service** é exibida. Selecione o botão **Criar Certificado** na seção **Certificado SSL de Desenvolvimento**.

    ![Botão Criar um certificado para aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    A tela **Criar um novo Certificado** é exibida.

    > [!NOTE]
    > Este tutorial usa um certificado de desenvolvimento, que seu aplicativo usa para gerar um token de dispositivo exclusivo. O mesmo processo é usado para registrar um certificado de produção. Use o mesmo tipo de certificado ao enviar notificações.

3. Selecione **Escolher Arquivo**, navegue até o local em que salvou o arquivo CSR da primeira tarefa e clique duas vezes no nome do certificado para carregá-lo. Depois selecione **Continuar**.

4. Depois que o portal cria o certificado, selecione o botão **Baixar**. Salve o certificado e lembre-se do local em que ele foi salvo.

    ![Página de download do certificado gerado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    O certificado é baixado e salvo no seu computador, na pasta **Downloads**.

    ![Localize o arquivo do certificado na pasta de Downloads](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Por padrão, o certificado de desenvolvimento baixado é denominado **aps_development.cer**.

5. Clique duas vezes no certificado de push baixado, **aps_development.cer**. Essa ação instala o novo certificado no conjunto de chaves, conforme mostrado na seguinte imagem:

    ![Lista de certificados de acesso ao conjunto de chaves mostrando o novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Embora o nome em seu certificado possa ser diferente, esse nome será prefixado com **Serviços de Notificação por Push do iOS para Desenvolvimento da Apple**.

6. No Acesso ao Conjunto de Chaves, clique com o botão direito do mouse no novo certificado push criado na categoria **Certificados** . Selecione **Exportar**, nomeie o arquivo, selecione o formato **.p12** e selecione **Salvar**.

    ![Exportar o certificado como formato p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Você pode optar por proteger o certificado com uma senha, mas isso é opcional. Clique em **OK** caso deseje ignorar a criação de senha. Anote o nome do arquivo e o local do certificado .p12 exportado. Eles são usados para habilitar a autenticação com APNs.

    > [!NOTE]
    > O nome e o local do arquivo. p12 podem ser diferentes do que foi mostrado neste tutorial.

### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>OPÇÃO DOIS: Crie um certificado .p8 que possa ser usado para a autenticação baseada em token

1. Anote os seguintes detalhes:

    - O **Prefixo da ID do Aplicativo** (é uma **ID da Equipe**)
    - **ID do Pacote**
    
2. Novamente em **Certificados, Identificadores e Perfis**, clique em **Chaves**.

   > [!NOTE]
   > Caso já tenha uma chave configurada para **APNs**, você pode usar novamente o certificado .p8 que você baixou logo depois de criá-lo. Nesse caso, você pode ignorar desde a etapa **3** até a **5**.

3. Clique no botão **+** (ou no botão **Criar uma chave**) para criar uma chave.
4. Forneça um valor de **Nome de Chave** adequado, marque a opção **APNs (Apple Push Notifications service)** e clique em **Continuar** e, em seguida, em **Registrar** na próxima tela.
5. Clique em **Baixar**, mova o arquivo **.p8** (prefixado com *AuthKey_* ) para um diretório local seguro e clique em **Concluído**.

   > [!NOTE] 
   > Verifique se o arquivo .p8 está em um local seguro (e salve um backup). Após o download da chave, não é possível baixá-la novamente, pois a cópia do servidor é removida.
  
6. Em **Chaves**, clique na chave que você acabou de criar (ou em uma já existente caso você tenha optado por usá-la em vez disso).
7. Anote o valor da **ID da Chave**.
8. Abra o certificado .p8 em um aplicativo de sua escolha, como o [**Visual Studio Code**](https://code.visualstudio.com), e anote o valor da chave. Esse é o valor entre **-----BEGIN PRIVATE KEY-----** e **-----END PRIVATE KEY-----** .

    ```
    -----BEGIN PRIVATE KEY-----
    <key_value>
    -----END PRIVATE KEY-----
    ```

    > [!NOTE]
    > Esse é o **valor do token** que será usado mais tarde para configurar o **Hub de Notificação**. 

No final dessas etapas, você deveria ter as seguintes informações para uso posterior em [Configurar o hub de notificação com informações de APNs](#configure-your-notification-hub-with-apns-information):

- A **ID da Equipe** (veja a etapa um)
- A **ID do Pacote** (veja a etapa um)
- A **ID da Chave** (veja a etapa sete)
- O **Valor do token**, ou seja, o valor da chave .p8 (veja a etapa oito)

## <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de provisionamento para o aplicativo

1. Retorne ao [Portal de Provisionamento do iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecione **Certificados, Identificadores e Perfis**, selecione **Perfis** no menu à esquerda e selecione **+** para criar um novo perfil. A tela **Registrar um Novo Perfil de Provisionamento** é exibida.

1. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e selecione **Continuar**.

    ![Lista de perfil de provisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Em seguida, selecione a ID do aplicativo que você criou na lista suspensa **ID do Aplicativo** e selecione **Continuar**.

    ![Selecionar a ID do aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Na janela **Selecionar certificados**, selecione o certificado de desenvolvimento que você usa para assinatura de código e selecione **Continuar**. Esse certificado não é o certificado push que você criou. Se não houver um, você deverá criá-lo. Se houver um certificado, pule para a próxima etapa. Para criar um certificado de desenvolvimento, caso não exista um:

    1. Se você vir **Nenhum certificado disponível**, selecione **Criar Certificado**.
    2. Na seção **Software**, selecione **Desenvolvimento da Apple**. Depois selecione **Continuar**.
    3. Na tela **Criar um Novo Certificado**, selecione **Escolher Arquivo**.
    4. Navegue até o certificado **Solicitação de Assinatura de Certificado** criado anteriormente, selecione-o e, em seguida, selecione **Abrir**.
    5. Selecione **Continuar**.
    6. Baixe o certificado de desenvolvimento e lembre-se do local em que ele foi salvo.

1. Retorne à página **Certificados, Identificadores e Perfis**, selecione **Perfis** no menu à esquerda e selecione **+** para criar um novo perfil. A tela **Registrar um Novo Perfil de Provisionamento** é exibida.

1. Na janela **Selecionar certificados**, selecione o certificado de desenvolvimento que você acabou de criar. Depois selecione **Continuar**.

1. Em seguida, selecione os dispositivos a serem usados para teste e selecione **Continuar**.

1. Por fim, escolha um nome para o perfil em **Nome do Perfil de Provisionamento** e selecione **Gerar**.

    ![Escolher um nome do perfil de provisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Quando o novo perfil de provisionamento for criado, selecione **Baixar**. Lembre-se do local em que ele foi salvo.

1. Navegue até o local do perfil de provisionamento e clique duas vezes nele para instalá-lo em seu computador de desenvolvimento do Xcode.

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

Nesta seção, você cria um hub de notificação e configura a autenticação com APNs usando ou o certificado push .p12 ou a autenticação baseada em token. Se você quiser usar um hub de notificação já criado, passe diretamente à etapa 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Configure seu hub de notificação com as informações de APNs

Em **Serviços de Notificação**, selecione **Apple (APNS)** e siga as etapas apropriadas com base na abordagem que você selecionou antes na seção [Criar um certificado para os Hubs de Notificação](#creating-a-certificate-for-notification-hubs).  

> [!NOTE]
> Se você criar seu aplicativo com a App Store ou o perfil de distribuição ad hoc, use **Produção** para o **Modo de Aplicativo**. Isso permitirá que seu dispositivo envie notificações por push a usuários que compraram seu aplicativo da loja.

### <a name="option-1-using-a-p12-push-certificate"></a>OPÇÃO UM: Usando um certificado push .p12

1. Selecione **Certificado**.

1. Selecione o ícone arquivo.

1. Selecione o arquivo .p12 que você exportou anteriormente e, em seguida, clique em **Abrir**.

1. Caso seja necessário, especifique a senha correta.

1. Selecione o modo **Sandbox**.

    ![Configurar certificação de APNs no portal do Azure](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Clique em **Salvar**.

### <a name="option-2-using-token-based-authentication"></a>OPÇÃO DOIS: Usando a autenticação baseada em token

1. Selecione **Token**.
1. Insira os seguintes valores que você adquiriu antes:

    - **ID da Chave**
    - **ID do Pacote**
    - **ID da Equipe**
    - **Token** 

1. Selecione **Área restrita**
1. Clique em **Salvar**. 

Agora você configurou seu hub de notificações com APNs. Você também tem as cadeias de conexão para registrar seu aplicativo e enviar notificações por push.