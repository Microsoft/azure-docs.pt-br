1. Abra o Gerenciador de SDK do Android clicando no ícone na barra de ferramentas do Android Studio ou clicando em **Ferramentas** -> **Android** -> **Gerenciador de SDK** no menu. Localize a versão de destino do SDK do Android que é usada em seu projeto, abra-o clicando em **Mostrar Detalhes do Pacote** e escolha **APIs do Google**, se ainda não estiver instalado.
2. Clique na guia **Ferramentas do SDK** . Se você ainda não tiver instalado o Serviço do Google Play, clique em **Serviços do Google Play** , conforme mostrado abaixo. Em seguida, clique em **Aplicar** para instalar. 
   
    Anote o caminho do SDK, a ser usado em uma etapa posterior. 
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Abra o arquivo **build.gradle** no diretório do aplicativo.
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. Adicione esta linha em *dependências*: 
   
           compile 'com.google.android.gms:play-services-gcm:9.2.0'
5. Clique no botão **Sincronizar projetos com arquivos do Gradle** na barra de ferramentas.
6. Abra **Androidmanifest** e adicione esta marcação para a marca do *aplicativo* .
   
        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />

