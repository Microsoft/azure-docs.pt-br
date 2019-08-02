---
title: Início Rápido para aprender a usar o Cache do Azure para Redis com aplicativos .NET | Microsoft Docs
description: Neste início rápido, aprenda como acessar o Cache do Azure para Redis de seus aplicativos .NET
services: cache,app-service
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: c502f74c-44de-4087-8303-1b1f43da12d5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 5930ac3834c0b697a4c03ce5b110dfeac105436a
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324443"
---
# <a name="quickstart-use-azure-cache-for-redis-with-a-net-framework-application"></a>Início Rápido: Usar o Cache do Azure para Redis com um aplicativo .NET Framework

Neste guia de início rápido, você incorporará o Cache do Azure para Redis a um aplicativo .NET Framework para ter acesso a um cache seguro e dedicado que pode ser acessado de qualquer aplicativo no Azure. Você especificamente usará o cliente [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) com o código C# em um aplicativo de console do .NET.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/)
- [.NET Framework 4 ou superior](https://www.microsoft.com/net/download/dotnet-framework-runtime), que é exigido pelo cliente StackExchange.Redis.

## <a name="create-a-cache"></a>Criar um cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Crie um arquivo no computador chamado *CacheSecrets.config* e coloque-o em um local em que ele não fará o check-in com o código-fonte do aplicativo de exemplo. Para este início rápido, o arquivo *CacheSecrets.config* está localizado aqui, em *C:\AppSecrets\CacheSecrets.config*.

Edite o arquivo *CacheSecrets.config* e adicione o seguinte conteúdo:

```xml
<appSettings>
    <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,password=<access-key>"/>
</appSettings>
```

Substitua `<cache-name>` pelo nome do host do cache.

Substitua `<access-key>` pela chave primária do cache.


## <a name="create-a-console-app"></a>Criar um aplicativo de console

No Visual Studio, clique em **Arquivo** > **Novo** > **Projeto**.

Em **Visual C#** , clique em **Área de Trabalho Clássica do Windows** e depois clique em **Aplicativo de Console**, e **OK** para criar um novo aplicativo de console.


<a name="configure-the-cache-clients"></a>

## <a name="configure-the-cache-client"></a>Configurar o cliente de cache

Nesta seção, você configurará o aplicativo de console para usar o cliente [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) para .NET.

No Visual Studio, clique em **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** e execute o comando a seguir na janela do Console do Gerenciador de Pacotes.

```powershell
Install-Package StackExchange.Redis
```

Depois que a instalação for concluída, o cliente de cache *StackExchange.Redis* está disponível para uso com o seu projeto.


## <a name="connect-to-the-cache"></a>Conectar-se ao cache

No Visual Studio, abra seu arquivo *App.config* e atualize-o para incluir um atributo `appSettings` `file` que referencia o arquivo *CacheSecrets.config*.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.1" />
    </startup>

    <appSettings file="C:\AppSecrets\CacheSecrets.config"></appSettings>  

</configuration>
```

No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** e clique em **Adicionar uma referência**. Adicionar uma referência ao assembly **System.Configuration**.

Adicione as instruções `using` a seguir ao *Program.cs*:

```csharp
using StackExchange.Redis;
using System.Configuration;
```

A conexão com o Cache do Azure para Redis é gerenciada pela classe `ConnectionMultiplexer`. Essa classe deve ser compartilhada e reutilizada em todo o seu aplicativo cliente. Não crie uma nova conexão para cada operação. 

Nunca armazene credenciais no código-fonte. Para manter essa amostra simples, estou usando apenas um arquivo de configuração de segredos externo. Uma abordagem melhor seria usar o [Azure Key Vault com certificados](https://docs.microsoft.com/rest/api/keyvault/certificate-scenarios).

Em *Program.cs*, adicione os seguintes membros à classe `Program` do seu aplicativo de console:

```csharp
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }
```


Uma abordagem para compartilhar uma instância do `ConnectionMultiplexer` em seu aplicativo usa uma propriedade estática que retorna uma instância conectada. O código oferece uma maneira segura ao thread de inicializar somente uma única instância conectada do `ConnectionMultiplexer`. `abortConnect` é definido como false, o que significa que a chamada é bem-sucedida mesmo que não seja possível estabelecer uma conexão com o Cache do Azure para Redis. Um recurso chave do `ConnectionMultiplexer` é que ele restaura automaticamente a conectividade com o cache assim que o problema de rede ou outras causas sejam resolvidos.

O valor do appSetting *CacheConnection* é usado para referenciar a cadeia de conexão do cache do portal do Azure como o parâmetro de senha.

## <a name="executing-cache-commands"></a>Executar comandos de cache

Adicione o seguinte código para o procedimento `Main` com a classe `Program` para seu aplicativo de console:

```csharp
        static void Main(string[] args)
        {
            // Connection refers to a property that returns a ConnectionMultiplexer
            // as shown in the previous example.
            IDatabase cache = lazyConnection.Value.GetDatabase();

            // Perform cache operations using the cache object...

            // Simple PING command
            string cacheCommand = "PING";
            Console.WriteLine("\nCache command  : " + cacheCommand);
            Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

            // Simple get and put of integral data types into the cache
            cacheCommand = "GET Message";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
            Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

            cacheCommand = "SET Message \"Hello! The cache is working from a .NET console app!\"";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
            Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET console app!").ToString());

            // Demonstrate "SET Message" executed as expected...
            cacheCommand = "GET Message";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
            Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

            // Get the client list, useful to see if connection list is growing...
            cacheCommand = "CLIENT LIST";
            Console.WriteLine("\nCache command  : " + cacheCommand);
            Console.WriteLine("Cache response : \n" + cache.Execute("CLIENT", "LIST").ToString().Replace("id=", "id="));

            lazyConnection.Value.Dispose();
        }
```

O Cache Redis do Azure tem um número configurável de bancos de dados (padrão de 16) que podem ser usados para separar logicamente os dados em um Cache Redis do Azure. O código se conecta ao banco de dados padrão, DB 0. Para saber mais, veja [O que são os bancos de dados do Redis?](cache-faq.md#what-are-redis-databases) e [Configuração padrão do servidor Redis](cache-configure.md#default-redis-server-configuration).

Os itens de cache podem ser armazenados e recuperados usando os métodos `StringSet` e `StringGet`.

O Redis armazena mais dados como cadeias de caracteres Redis, mas essas cadeias de caracteres podem conter muitos tipos de dados, incluindo dados binários serializados, que podem ser usados ao armazenar objetos .NET no cache.

Pressione **Ctrl+F5** para compilar e executar o aplicativo de console.

No exemplo abaixo, você pode ver que a chave `Message` já tinha um valor armazenado em cache, que foi definido por meio do Console do Redis no portal do Azure. O aplicativo atualizou esse valor armazenado em cache. O aplicativo também executou os comandos `PING` e `CLIENT LIST`.

![Aplicativo de console parcial](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Trabalhar com objetos .NET no cache

O Cache do Azure para Redis pode armazenar objetos .NET e tipos de dados primitivos em cache, mas antes que um objeto .NET seja armazenado em cache, ele deve ser serializado. Essa serialização de objetos .NET é de responsabilidade do desenvolvedor do aplicativo e proporciona ao desenvolvedor a flexibilidade na escolha do serializador.

Uma maneira simples de serializar objetos é usar os métodos de serialização do `JsonConvert` no [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) e serializar para e do JSON. Nesta seção, você adicionará um objeto .NET ao cache.

No Visual Studio, clique em **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** e execute o comando a seguir na janela do Console do Gerenciador de Pacotes.

```powershell
Install-Package Newtonsoft.Json
```

Adicione a seguinte instrução `using` à parte superior do *Program.cs*:

```csharp
using Newtonsoft.Json;
```

Adicione a seguinte definição de classe `Employee` ao *Program.cs*:

```csharp
        class Employee
        {
            public string Id { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }

            public Employee(string EmployeeId, string Name, int Age)
            {
                this.Id = EmployeeId;
                this.Name = Name;
                this.Age = Age;
            }
        }
```

Na parte inferior do procedimento `Main()` no *Program.cs* e antes da chamada para `Dispose()`, adicione as seguintes linhas de código para o cache e recupere um objeto serializado do .NET:

```csharp
            // Store .NET object to cache
            Employee e007 = new Employee("007", "Davide Columbo", 100);
            Console.WriteLine("Cache response from storing Employee .NET object : " + 
                cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

            // Retrieve .NET object from cache
            Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
            Console.WriteLine("Deserialized Employee .NET object :\n");
            Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
            Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
            Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

Pressione **Ctrl + F5** para compilar e executar o aplicativo de console para testar a serialização de objetos .NET. 

![Aplicativo de console concluído](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Limpar recursos

Se você pretende continuar para o próximo tutorial, mantenha os recursos criados neste início rápido e reutilize-os.

Caso contrário, se você não for mais usar o aplicativo de exemplo do início rápido, exclua os recursos do Azure criados neste início rápido para evitar encargos. 

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível, e o grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. Se tiver criado os recursos para hospedar este exemplo dentro de um grupo de recursos existente que contém recursos que você quer manter, exclua cada recurso individualmente de suas respectivas folhas, em vez de excluir o grupo de recursos.
>

Entre no [portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...** , digite o nome do seu grupo de recursos. As instruções deste artigo usaram um grupo de recursos chamado *TestResources*. Em seu grupo de recursos, na lista de resultados, clique em **...** , depois em **Excluir grupo de recursos**.

![Excluir](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-delete-resource-group.png)

Você receberá uma solicitação para confirmar a exclusão do grupo de recursos. Digite o nome do grupo de recursos para confirmar e clique em **Excluir**.

Após alguns instantes, o grupo de recursos, e todos os recursos contidos nele, serão excluídos.



<a name="next-steps"></a>

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a usar o Cache do Azure para Redis de um aplicativo .NET. Continue para o próximo início rápido usar o Cache do Azure para Redis com um aplicativo Web ASP.NET.

> [!div class="nextstepaction"]
> [Crie um aplicativo Web ASP.NET que usa um Cache do Azure para Redis.](./cache-web-app-howto.md)


