No Cloud Shell, crie credenciais de implantação com o comando [az webapp deployment user set](/cli/azure/webapp/deployment/user#set).

Um usuário de implantação é necessário para a implantação do FTP e do Git local em um aplicativo Web. O nome de usuário e a senha estão no nível de conta. _Eles são diferentes das credenciais da sua assinatura do Azure._

No comando a seguir, substitua *\<nome de usuário>* e *\<senha >* por um novo nome de usuário e senha. O nome do usuário deve ser exclusivo. A senha deve ter pelo menos oito caracteres, com dois destes três elementos: letras, números, símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Se receber um erro ` 'Conflict'. Details: 409`, altere o nome de usuário. Se receber um erro ` 'Bad Request'. Details: 400`, use uma senha mais forte.

Você só cria esse usuário de implantação uma vez; é possível usá-lo para todas as implantações do Azure.

> [!NOTE]
> Registre um nome de usuário e uma senha. Você precisará delas para implantar o aplicativo Web mais tarde.
>
>