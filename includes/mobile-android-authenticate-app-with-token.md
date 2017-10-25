
O exemplo anterior mostrou um logon padrão, que requer que o cliente contate o provedor de identidade e o serviço de back-end do Azure sempre que o aplicativo for iniciado. Esse método é ineficiente e você pode ter problemas relacionados ao uso caso muitos clientes tentem iniciar o aplicativo simultaneamente. Uma abordagem melhor é armazenar em cache o token de autorização retornado pelo serviço do Azure e tentar usá-lo antes de usar um logon baseado em provedor.

> [!NOTE]
> Você pode armazenar em cache o token emitido pelo serviço de back-end do Azure usando tanto a autenticação gerenciada pelo cliente quanto a autenticação gerenciada pelo serviço. Este tutorial usa a autenticação gerenciada pelo serviço.
>
>

1. Abra o arquivo ToDoActivity.java e adicione as seguintes instruções de importação:

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;
2. Adicione os seguintes membros à classe `ToDoActivity` :

        public static final String SHAREDPREFFILE = "temp";    
        public static final String USERIDPREF = "uid";    
        public static final String TOKENPREF = "tkn";    
3. No arquivo ToDoActivity.java, adicione a seguinte definição para o método `cacheUserToken`.

        private void cacheUserToken(MobileServiceUser user)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            Editor editor = prefs.edit();
            editor.putString(USERIDPREF, user.getUserId());
            editor.putString(TOKENPREF, user.getAuthenticationToken());
            editor.commit();
        }    

    Este método armazena a ID e o token do usuário em um arquivo preferencial marcado como privado. Isto deve proteger o acesso ao cache para que os outros aplicativos do dispositivo não tenham acesso ao token. A preferência é uma área restrita para o aplicativo. No entanto, se alguém obtiver acesso ao dispositivo, é possível que a pessoa ganhe acesso ao cache de token de outras formas.

   > [!NOTE]
   > Você pode proteger ainda mais o token com criptografia se o acesso do token a seus dados for considerado altamente confidencial e alguém puder acessar o dispositivo. No entanto, uma solução completamente segura está além do escopo deste tutorial e depende dos seus requisitos de segurança.
   >
   >
4. No arquivo ToDoActivity.java, adicione a seguinte definição para o método `loadUserTokenCache`.

        private boolean loadUserTokenCache(MobileServiceClient client)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            String userId = prefs.getString(USERIDPREF, null);
            if (userId == null)
                return false;
            String token = prefs.getString(TOKENPREF, null);
            if (token == null)
                return false;

            MobileServiceUser user = new MobileServiceUser(userId);
            user.setAuthenticationToken(token);
            client.setCurrentUser(user);

            return true;
        }
5. No arquivo *ToDoActivity.java*, substitua o método `authenticate` pelo seguinte método, que usa um cache de token. Altere o provedor de logon se quiser usar uma conta que não seja do Google.

        private void authenticate() {
            // We first try to load a token cache if one exists.
            if (loadUserTokenCache(mClient))
            {
                createTable();
            }
            // If we failed to load a token cache, login and create a token cache
            else
            {
                // Login using the Google provider.    
                ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        createAndShowDialog("You must log in. Login Required", "Error");
                    }           
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        createAndShowDialog(String.format(
                                "You are now logged in - %1$2s",
                                user.getUserId()), "Success");
                        cacheUserToken(mClient.getCurrentUser());
                        createTable();    
                    }
                });
            }
        }
6. Compile o aplicativo e teste a autenticação usando uma conta válida. Execute pelo menos uma vez. Durante a primeira execução, você deverá ser solicitado a iniciar uma sessão e criar o cache do token. Depois disso, cada execução tentará carregar o cache de token para autenticação. Não deve ser solicitado que você inicie uma sessão.
