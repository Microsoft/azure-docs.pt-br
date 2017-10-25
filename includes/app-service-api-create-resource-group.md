Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create).

[!INCLUDE [resource group intro text](resource-group.md)]

O seguinte exemplo cria um grupo de recursos chamado *myResourceGroup* no local *westeurope*.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Para ver os locais disponíveis, execute o comando `az appservice list-locations`. Geralmente, você cria recursos em uma região perto de você.
