
Crie um aplicativo no `myAppServicePlan` plano do Serviço de Aplicativo com o comando [az webapp create](/cli/azure/appservice/web#create). 

O aplicativo Web fornece um espaço de hospedagem para sua API e fornece uma URL para exibir o aplicativo implantado.

No comando a seguir, substitua *\<nome_do_aplicativo >* por um nome exclusivo. Se `<app_name>` não for exclusivo, você obterá a mensagem de erro "O site com o nome <nome_do_aplicativo> fornecido já existe." A URL padrão do aplicativo Web é `https://<app_name>.azurewebsites.net`. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
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