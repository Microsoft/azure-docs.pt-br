No Cloud Shell, crie um [aplicativo Web](../articles/app-service/app-service-web-overview.md) no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [az webapp list-runtimes](/cli/azure/webapp#create). 

O aplicativo Web fornece um espaço de hospedagem para seu código e fornece uma URL para exibir o aplicativo implantado.

No comando a seguir, substitua *\<nome_do_aplicativo >* por um nome exclusivo (os caracteres válidos são `a-z`, `0-9` e `-`). Se `<app_name>` não for exclusivo, você obterá a mensagem de erro "O site com o nome <nome_do_aplicativo> fornecido já existe." A URL padrão do aplicativo Web é `https://<app_name>.azurewebsites.net`. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

Quando o aplicativo Web tiver sido criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```

Navegue até o site para ver seu Aplicativo Web recém-criado.

```bash
http://<app_name>.azurewebsites.net
```
