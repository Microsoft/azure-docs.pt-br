---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: d71d52257b6e8cfa243207c9bfdb5c7de7d3dd37
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172603"
---
1. No arquivo de projeto MainPage.xaml.cs e adicione as seguintes instruções **using** :
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Substitua o método **AuthenticateAsync** pelo seguinte código:
   
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
   
            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;
   
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;
   
            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }
   
            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;
   
                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;
   
                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: https://aka.ms/jww5vp.
   
                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Sign in with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider, "{url_scheme_of_your_app}");
   
                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);
   
                    success = true;
                    message = string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must sign in. Sign-In Required";
                }
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
   
            return success;
        }
   
    Nesta versão do **AuthenticateAsync**, o aplicativo tenta usar as credenciais armazenadas no **PasswordVault** para acessar o serviço. Também é realizado um registro normal quando não há uma credencial armazenada.
   
   > [!NOTE]
   > Um token armazenado em cache pode estar expirado e a expiração do token pode ocorrer após a autenticação quando o aplicativo estiver em uso. Para saber como determinar se um token está expirado, consulte [Procurar tokens de autenticação expirados](https://aka.ms/jww5vp). Para solucionar como lidar com erros de autorização relacionados a tokens expirados, consulte a postagem [Armazenagem em cache e manipulação de tokens expirados no SDK gerenciado pelos Serviços Móveis do Azure](https://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 
   > 
   > 
3. Reiniciar o aplicativo.
   
    Observe que na primeira inicialização, o registro com o provedor é requerido novamente. Porém, na segunda inicialização são usadas as credenciais armazenadas em cache e o registro é desviado. 

