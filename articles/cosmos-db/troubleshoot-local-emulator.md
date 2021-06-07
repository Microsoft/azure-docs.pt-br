---
title: Solucionar problemas ao usar o emulador de Azure Cosmos DB
description: Saiba como solucionar problemas de serviço indisponível, certificado, criptografia e controle de versão ao usar o emulador de Azure Cosmos DB.
ms.service: cosmos-db
ms.topic: troubleshooting
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: bb07adafbc68ff1e9b7d3ee49bb7631dc4395d77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97033452"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-db-emulator"></a>Solucionar problemas ao usar o emulador de Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Emulador do Azure Cosmos DB fornece um ambiente local que emula o serviço Azure Cosmos DB para fins de desenvolvimento. Use as dicas neste artigo para ajudar a solucionar problemas encontrados ao instalar ou usar o emulador de Azure Cosmos DB. 

Se você tiver instalado uma nova versão do emulador e se houver erros, redefina os dados. Você pode redefinir seus dados clicando com o botão direito do mouse no ícone do emulador de Azure Cosmos DB na bandeja do sistema e, em seguida, clicando em Redefinir dados.... Se isso não corrigir os erros, você poderá desinstalar o emulador e qualquer versão mais antiga do emulador, se for encontrado, remover *C:\Program programas\azure Cosmos db diretório do emulador* e reinstalar o emulador. Veja [Desinstalar o emulador local](local-emulator.md#uninstall) para obter instruções. Como alternativa, se a redefinição dos dados não funcionar, navegue até `%LOCALAPPDATA%\CosmosDBEmulator` o local e exclua a pasta.

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>Solucionar problemas de contadores de desempenho do Windows corrompidos

* Se o emulador de Azure Cosmos DB falhar, colete os arquivos de despejo da `%LOCALAPPDATA%\CrashDumps` pasta, compacte-os e abra um tíquete de suporte no [portal do Azure](https://portal.azure.com).

* Se você enfrentar falhas em `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`, isso pode ser um sintoma de onde os contadores de desempenho estão em um estado corrompido. Normalmente, executar o seguinte comando em um prompt de comando do administrador corrige o problema:

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>Solucionar problemas de conectividade

* Se encontrar um problema de conectividade, [colete os arquivos de rastreamento](#trace-files), compacte-os e abra um tíquete de suporte no [portal do Azure](https://portal.azure.com).

* Se você receber uma mensagem de **Serviço Indisponível**, o emulador poderá estar falhando em inicializar a pilha de rede. Verifique se você tem o cliente seguro Pulse ou o cliente de redes Juniper instalado, uma vez que seus drivers de filtro de rede podem causar o problema. Desinstalar os drivers de filtro de rede de terceiros geralmente corrige o problema. Como alternativa, inicie o emulador com /DisableRIO, que mudará a comunicação de rede do emulador para Winsock regular. 

* Se você encontrar **"proibido", "mensagem": "a solicitação está sendo feita com um protocolo de criptografia proibido em trânsito ou codificação. Verificar a configuração de protocolo mínimo permitido por SSL/TLS da conta...** problemas de conectividade, isso pode ser causado por alterações globais no sistema operacional (por exemplo, Insider Preview versão 20170) ou as configurações do navegador que habilitam o TLS 1,3 como padrão. Pode ocorrer um erro semelhante ao usar o SDK para executar uma solicitação no emulador Cosmos, como **Microsoft.Azure.Documents.DocumentClientException: a solicitação está sendo feita com uma criptografia proibida no protocolo de trânsito ou na codificação. Verifique a configuração de protocolo mínimo permitido por SSL/TLS**. Isso é esperado no momento, pois o emulador Cosmos só aceita e funciona com o protocolo TLS 1.2. A solução alternativa recomendada é alterar as configurações e padrão para TLS 1,2; por exemplo, no Gerenciador do IIS, navegue até "sites"-> "sites padrão" e localize as "ligações do site" para a porta 8081 e edite-as para desabilitar o TLS 1,3. Uma operação semelhante pode ser realizada para o navegador da Web por meio das opções de "Configurações".

* Enquanto o emulador é executado, se o computador entrar em modo de suspensão ou executar atualizações do sistema operacional, talvez você veja uma mensagem **O serviço não disponível no momento**. Reinicie os dados do emulador clicando com o botão direito do mouse no ícone que aparece na bandeja de notificação do Windows e selecione **Redefinir dados**.

## <a name="collect-trace-files"></a><a id="trace-files"></a>Coletar arquivos de rastreamento

Para coletar rastreamentos de depuração, execute os seguintes comandos em um prompt de comando administrativo:

1. Navegue até o caminho em que o emulador está instalado:

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. Desligue o emulador e observe a bandeja do sistema para verificar se o programa foi desligado. Pode levar um minuto para ser concluído. Você também pode selecionar **sair** na interface de usuário do emulador de Azure Cosmos DB.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. Inicie o registro com o seguinte comando:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. Iniciar o emulador

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. Reproduza o problema. Se o data Explorer não estiver funcionando, você precisará aguardar até que o navegador seja aberto por alguns segundos para detectar o erro.

1. Pare o registro com o seguinte comando:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. Navegue até `%ProgramFiles%\Azure Cosmos DB Emulator` o caminho e localize o arquivo *docdbemulator_000001. etl* .

1. Abra um tíquete de suporte no [portal do Azure](https://portal.azure.com) e inclua o arquivo .etl com as etapas de reprodução.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a depurar problemas com o emulador local. Agora você pode prosseguir para os próximos artigos:

* [Exportar os certificados do emulador Azure Cosmos DB para uso com aplicativos Java, Python e Node.js](local-emulator-export-ssl-certificates.md)
* [Usar parâmetros de linha de comando e comandos do PowerShell para controlar o emulador](emulator-command-line-parameters.md)
