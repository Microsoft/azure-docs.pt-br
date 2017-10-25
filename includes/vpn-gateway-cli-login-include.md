Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela. Para obter mais informações sobre registro em log, consulte [Introdução à CLI do Azure 2.0](/cli/azure/get-started-with-azure-cli).

```azurecli
az login
```

Se tiver mais de uma assinatura do Azure, liste as assinaturas para a conta.

```azurecli
az account list --all
```

Especifique a assinatura que você quer usar.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```