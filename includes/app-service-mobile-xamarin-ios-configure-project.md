#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Configurar o projeto iOS no Xamarin Studio
1. No Xamarin.Studio, abra **Info.plist** e atualize o **Identificador de Pacote** com a ID de Pacote criada anteriormente com a nova ID do Aplicativo.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Role para baixo até **Modos em Segundo Plano**. Selecione a caixa **Habilitar Modos em Segundo Plano** e a caixa **Notificações remotas**.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Clique duas vezes em seu projeto no Painel de Solução para abrir **Opções de Projeto**.
4. Em **Build**, escolha **Assinatura do Pacote iOS** e selecione a identidade e o perfil de provisionamento correspondentes que você acabou de configurar para este projeto.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Isso garantirá que o projeto use o novo perfil para a assinatura de código. Para obter a documentação oficial de provisionamento do dispositivo Xamarin, consulte [Provisionamento do dispositivo Xamarin].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Configurar o projeto iOS no Visual Studio
1. No Visual Studio, clique com o botão direito do mouse no projeto e clique em **Propriedades**.
2. Nas páginas de propriedades, clique na guia **Aplicativo iOS** e atualize o **Identificador** com a ID que você criou anteriormente.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. Na guia **Assinatura do Pacote iOS**, selecione a identidade e o perfil de provisionamento correspondentes que você acabou de configurar para este projeto.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Isso garantirá que o projeto use o novo perfil para a assinatura de código. Para obter a documentação oficial de provisionamento do dispositivo Xamarin, consulte [Provisionamento do dispositivo Xamarin].
4. Clique duas vezes em Info.plist para abri-lo e habilite **RemoteNotifications** em **Modos de Segundo Plano**.

[Provisionamento do dispositivo Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
