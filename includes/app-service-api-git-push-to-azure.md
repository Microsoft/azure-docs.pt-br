Use a CLI do Azure para obter a URL de implantação remota do aplicativo de API. No comando a seguir, substitua *\<nome_do_aplicativo >* pelo nome do seu aplicativo Web.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Configure sua implantação local do Git para enviar por push ao remoto.

```bash
git remote add azure <URI from previous step>
```

Envie por push para o Azure remoto para implantar seu aplicativo. Você recebe uma solicitação pela senha criada anteriormente ao criar o usuário de implantação. Verifique se você inseriu a senha que você criou anteriormente no início rápido, não a senha usada para fazer logon no portal do Azure.

```bash
git push azure master
```
