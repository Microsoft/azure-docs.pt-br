

## <a name="generate-the-certificate-signing-request-file"></a>Gerar o arquivo de Solicitação de Assinatura de Certificado
O APNS (Serviço de Notificação por Push da Apple) usa certificados para autenticar suas notificações por push. Siga estas instruções para criar o certificado de push necessário para enviar e receber notificações. Para obter mais informações sobre esses conceitos, consulte a documentação oficial do [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584) .

Gere o arquivo CSR (Solicitação de Assinatura de Certificado), usado pela Apple para gerar um certificado de push assinado.

1. Em seu Mac, execute a ferramenta Acesso do Conjunto de Chaves. Ela pode ser aberta da pasta **Utilitários** ou da pasta **Outros** no launch pad.
2. Clique em **Acesso do Conjunto de Chaves**, expanda **Assistente de Certificado** e clique em **Solicitar um Certificado de uma Autoridade de Certificação...**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Selecione seu **Endereço de Email de Usuário** e seu **Nome Comum**, verifique se **Salvo em disco** está selecionado e, em seguida, clique em **Continuar**. Deixe o campo **Endereço de Email de CA** em branco, pois ele não é necessário.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. Digite um nome para o arquivo CSR (Solicitação de Assinatura de Certificado) em **Salvar como**, selecione o local em **Onde** e, em seguida, clique em **Salvar**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      Isso salvará o arquivo CSR no local selecionado; o local padrão está situado na Área de Trabalho. Lembre-se do local escolhido para esse arquivo.

Em seguida, você registrará seu aplicativo na Apple, habilitará as notificações por push e carregará esse CSR exportado para criar um certificado de push.

## <a name="register-your-app-for-push-notifications"></a>Registrar seu aplicativo para notificações por push
Para poder enviar notificações por push para um aplicativo iOS, você deverá registrar seu aplicativo na Apple e também registrar-se em notificações por push.  

1. Se você ainda não registrou seu aplicativo, navegue até o <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a> no Apple Developer Center, faça logon com a sua Apple ID, clique em **Identificadores**, em seguida, clique em **IDs de Aplicativo** e, finalmente, clique no sinal de **+** para registrar um novo aplicativo.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)
      
2. Atualize os três campos a seguir para o novo aplicativo e clique em **Continuar**:
   
   * **Nome**: digite um nome descritivo para o aplicativo no campo **Nome**, na seção **Descrição de ID do Aplicativo**.
   * **Identificador de Pacote**: na seção **ID do Aplicativo Explícita**, digite um **Identificador de Pacote** no formato `<Organization Identifier>.<Product Name>`, como mencionado no [Guia de Distribuição de Aplicativo](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). O *Identificador de Organização* e *Nome do Produto* usados deverão corresponder ao identificador da organização e o nome do produto usados quando você criar seu projeto XCode. Na captura de tela abaixo, *NotificationHubs* é usado como um identificador de organização e *GetStarted* é usado como o nome do produto. Garantir que isso corresponda aos valores que você usará em seu projeto XCode permitirá que você use o perfil de publicação correto com XCode. 
   * **Notificações por Push**: marque a opção **Notificações por Push** na seção **Serviços de Aplicativos**.
     
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
     
      Isso gerará sua ID do Aplicativo e solicitará que você confirme as informações. Clique em **Registrar** para confirmar a nova ID do Aplicativo.
     
      Depois de clicar em **Registrar**, você verá a tela **Registro concluído**, conforme mostrado abaixo. Clique em **Concluído**.
      
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


1. Na Central de Desenvolvedores, em IDs de Aplicativo, localize a ID do aplicativo que você acabou de criar e clique na respectiva linha.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)
   
      Clicar na ID do aplicativo exibirá os detalhes do aplicativo. Clique no botão **Editar** na parte inferior.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)
      
2. Role até a parte inferior da tela e clique no botão **Criar Certificado...** na seção **Certificado SSL por Push para Desenvolvimento**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
      Isso exibirá o assistente "Adicionar Certificado de iOS".
   
   > [!NOTE]
   > Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Use o mesmo tipo de certificado ao enviar notificações.
   > 
   > 
3. Clique em **Escolher Arquivo**, navegue até o local em que salvou o arquivo CSR criado na primeira tarefa e clique em **Gerar**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
4. Depois que o certificado for criado pelo portal, clique no botão **Baixar** e, em seguida, clique em **Concluído**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
      Isso baixará e salvará esse certificado no seu computador na pasta Downloads.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > Por padrão, o arquivo baixado, um certificado de desenvolvimento, é denominado **aps_development.cer**.
   > 
   > 
5. Clique duas vezes no certificado de push baixado, **aps_development.cer**.
   
      Isso instalará o novo certificado no Conjunto de Chaves, conforme mostrado abaixo:
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > O nome em seu certificado pode ser diferente, mas ele será prefixado com **Serviços de Notificação por Push do iOS para Desenvolvimento da Apple:**.
   > 
   > 
6. No Acesso ao Conjunto de Chaves, clique com o botão direito do mouse no novo certificado push criado na categoria **Certificados** . Clique em **Exportar**, nomeie o arquivo, selecione o formato **.p12** e clique em **Salvar**.
   
    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)
   
    Anote o nome do arquivo e o local do certificado .p12 exportado. Ele será usado para habilitar a autenticação com APNS.
   
   > [!NOTE]
   > Este tutorial cria um arquivo QuickStart.p12. O nome do arquivo e o local podem ser diferentes.
   > 
   > 

## <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de provisionamento para o aplicativo
1. De volta ao <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a>, selecione **Perfis de Provisionamento**, escolha **Tudo** e clique no botão **+** para criar um novo perfil. Isso iniciará o Assistente **Adicionar Perfil de Provisionamento do iOS**
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e clique em **Continuar**. 
3. Em seguida, selecione a ID do aplicativo que você acabou de criar da lista suspensa **ID do Aplicativo** e clique em **Continuar**
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. Na tela **Selecionar certificados**, selecione seu certificado de desenvolvimento normal usado por assinatura de código e clique em **Continuar**. Esse não é o certificado de push que você acabou de criar.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. Em seguida, selecione os **Dispositivos** a serem usados no teste e clique em **Continuar**
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. Por fim, escolha um nome para o perfil em **Nome do Perfil** e clique em **Gerar**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
7. Quando o novo perfil de provisionamento for criado, clique para baixá-lo e instalá-lo em seu computador de desenvolvimento do Xcode. Em seguida, clique em **Concluído**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
