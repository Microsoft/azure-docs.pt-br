Na maioria das vezes, erros de autenticação são o resultado de definições de configuração incorretas ou inconsistentes. Aqui estão algumas sugestões específicas de itens a ser verificados.

* Verifique se você não esqueceu de pressionar o botão **Salvar** em algum lugar. Geralmente, isso é fácil de ser feito, mas se você não fizer isso, o resultado será a exibição dos valores corretos em uma página de portal, mas que, na verdade, não foram salvos no ambiente do Azure ou no aplicativo do Azure AD.
* Para obter as configurações definidas na folha **Configurações do Aplicativo** do portal do Azure, verifique se o aplicativo Web ou o aplicativo de API correto foi selecionado quando as configurações foram inseridas.  Além disso, certifique-se de que as configurações foram inseridas como **Configurações do aplicativo** e não como **Cadeias de conexão**, já que o formato das duas seções é semelhante.
* Para a autenticação em um front-end do JavaScript, baixe o manifesto novamente para verificar se `oauth2AllowImplicitFlow` foi alterado com êxito para `true`.
* Verifique se você usou HTTPS sempre ao configurar URLs:
  
  * No código do projeto
  * No CORS
  * Nas configurações do Aplicativo do ambiente do Azure para cada aplicativo de API e aplicativo Web
  * Nas configurações do aplicativo do Azure AD.
    
    Observe que, se você fizer uma cópia da URL de um aplicativo de API no portal, ela geralmente terá `http://` e você terá de alterá-la manualmente para `https://`.
* Certifique-se de que as alterações de código foram implantadas com êxito. Por exemplo, em uma solução de vários projetos, é possível alterar o código de um projeto e escolher acidentalmente um dos demais quando pretender implantar a alteração.
* Verifique se você está usando URLs HTTPS no navegador, não URLs HTTP. Por padrão, o Visual Studio cria perfis de publicação com URLs HTTP, e isso é o que será aberto no navegador após a implantação de um projeto.
* Para a autenticação em um front-end do JavaScript, certifique-se de que o CORS está configurado corretamente no aplicativo de API chamado pelo código JavaScript. Caso esteja em dúvida se o problema está relacionado ao CORS, tente usar "*" como a URL de origem permitida. 
* Para um front-end do JavaScript, abra a guia do Console das Ferramentas de Desenvolvimento do navegador para obter mais informações sobre erros e examine as solicitações HTTP na Rede. No entanto, mensagens de erro do Console podem ser confusas. Se você receber uma mensagem indicando um erro no CORS, o problema real poderá ser a autenticação. É possível verificar se esse é o caso executando o aplicativo com a autenticação temporariamente desabilitada.
* Para um aplicativo de API do .NET, certifique-se de que você obtém o máximo possível de informações nas mensagens de erro definindo o [modo customErrors como Desativado](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Para um aplicativo de API do .NET, inicie uma [sessão de depuração remota](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug) e examine os valores das variáveis que são transmitidos para o código que usa o ADAL para adquirir um token de portador ou o código que verifica as declarações em relação à ID da entidade de serviço esperada. Observe que o código pode selecionar valores de configuração de várias fontes diferentes. Dessa forma, é possível que ocorram surpresas. Por exemplo, se você digitar incorretamente `ida:ClientId` como `ida:ClientID` ao definir as configurações do Ambiente do Serviço de Aplicativo do Azure, o código poderá receber o valor `ida:ClientId` procurado no arquivo Web.config, ignorando a configuração do Serviço de Aplicativo do Azure. 
* Se as coisas não funcionarem em uma janela normal do Internet Explorer, um logon existente poderá estar interferindo; tente usar a navegação InPrivate no Chrome ou Firefox.

