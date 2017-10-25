
O código para todas as funções em um determinado aplicativo de funções reside em uma pasta raiz que contém um arquivo de configuração de host e uma ou mais subpastas, cada qual contendo o código para uma função distinta, como no exemplo a seguir:

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

O arquivo *host.json* contém uma configuração específica de tempo de execução e reside na pasta raiz do aplicativo de funções. Para obter informações sobre as configurações disponíveis, consulte a referência [host.json](../articles/azure-functions/functions-host-json.md).

Cada função tem uma pasta que contém arquivos de código, a configuração function.json e outras dependências.

