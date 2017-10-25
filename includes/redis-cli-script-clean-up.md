## <a name="clean-up-deployment"></a>Limpar implantação 

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos, instância do Cache Redis do Azure e todos os recursos relacionados no grupo de recursos.

```azurecli
az group delete --name contosoGroup
```