No Cloud Shell, crie um [aplicativo Web](../articles/app-service/containers/app-service-linux-intro.md) no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [az webapp list-runtimes](/cli/azure/webapp#create). Não se esqueça de substituir `<app name>` por um nome exclusivo do aplicativo.

O tempo de execução no comando a seguir é definido como `PHP|7.0`. Para ver todos os tempos de execução com suporte, execute [az webapp list-runtimes](/cli/azure/webapp#list-runtimes). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --runtime "PHP|7.0" --deployment-local-git
```

Quando o aplicativo Web tiver sido criado, a CLI do Azure mostrará saídas semelhantes ao exemplo a seguir:

```json
Local git is configured with url of 'https://<username>@<app name>.scm.azurewebsites.net/<app name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Você criou um novo aplicativo Web vazio em um contêiner do Linux com a implantação do Git habilitada.

> [!NOTE]
> A URL do Git remoto é mostrada na propriedade `deploymentLocalGitUrl` com o formato `https://<username>@<app name>.scm.azurewebsites.net/<app name>.git`. Salve essa URL pois você precisará dela mais tarde.
>
