Configure a implantação local do Git para o aplicativo Web com o comando [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git).

O Serviço de Aplicativo oferece suporte a várias maneiras de implantar conteúdo em um aplicativo Web, como FTP, Git local, GitHub, Visual Studio Team Services e Bitbucket. Para este início rápido, implante usando o Git local. Isso significa que você implanta usando um comando Git para enviar por push de um repositório local para um repositório no Azure. 

No comando a seguir, substitua *\<nome_do_aplicativo >* pelo nome do seu aplicativo Web.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

A saída tem o seguinte formato:

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

O `<username>` é o [usuário de implantação](#configure-a-deployment-user) que você criou na etapa anterior.

Copie o URI exibido; você o usará na próxima etapa.
