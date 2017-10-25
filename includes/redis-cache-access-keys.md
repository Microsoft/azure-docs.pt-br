Para se conectar a uma instância de Cache Redis do Azure, os clientes de cache precisam do nome de host, das portas e das chaves do cache. Alguns clientes podem se referir a esses itens por nomes um pouco diferentes. Você pode recuperar essas informações no portal do Azure ou usando as ferramentas da linha de comando, como a CLI do Azure.

### <a name="retrieve-host-name-ports-and-access-keys-using-the-azure-portal"></a>Recuperar o nome de host, portas e chaves de acesso usando o Portal do Azure
Para recuperar o nome de host, portas e acesso chaves usando o Portal do Azure, [navegue](../articles/redis-cache/cache-configure.md#configure-redis-cache-settings) até o cache no [portal do Azure](https://portal.azure.com) e clique em **Chaves de acesso** e **Propriedades** no **menu Recurso**. 

![Configurações de Cache Redis](media/redis-cache-access-keys/redis-cache-hostname-ports-keys.png)

### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Recuperar o nome de host, portas e chaves de acesso usando a CLI do Azure
Para recuperar o nome de host e as portas usando a CLI do Azure 2.0, você pode chamar [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) e para recuperar as chaves, pode chamar [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys). O script a seguir chama esses dois comandos e repete o nome de host, portas e chaves no console.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Redis Cache instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Redis Cache instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Para obter mais informações sobre esse script, consulte [Obter o nome de host, portas e chaves para o Cache Redis do Azure](../articles/redis-cache/scripts/cache-keys-ports.md). Para obter mais informações sobre a CLI do Azure 2.0, consulte [Instalar a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) e [Começar com a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
