---
title: Coletar logs personalizados com o agente de Log Analytics no Azure Monitor
description: O Azure Monitor pode coletar eventos de arquivos de texto em computadores com Windows e Linux.  Este artigo descreve como definir um novo log personalizado e os detalhes dos registros que ele cria no Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: b66011ff41e1dd4a7439105862ca61355a95bd71
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042701"
---
# <a name="collect-custom-logs-with-log-analytics-agent-in-azure-monitor"></a>Coletar logs personalizados com o agente de Log Analytics no Azure Monitor

A fonte de dados de logs personalizados para o agente de Log Analytics no Azure Monitor permite que você colete eventos de arquivos de texto em computadores Windows e Linux. Muitos aplicativos registram informações em arquivos de texto em vez de serviços de registro standard, como o log de eventos do Windows ou Syslog. Depois de coletados, você pode analisar os dados em campos individuais em suas consultas ou extrair os dados durante a coleta de campos individuais.

> [!IMPORTANT]
> Este artigo aborda a coleta de logs personalizados com o [agente de log Analytics](./log-analytics-agent.md) , que é um dos agentes usados pelo Azure monitor. Outros agentes coletam dados diferentes e são configurados de forma diferente. Consulte [visão geral dos agentes de Azure monitor](../agents/agents-overview.md) para obter uma lista dos agentes disponíveis e os dados que eles podem coletar.

![Coleta de log personalizado](media/data-sources-custom-logs/overview.png)

Os arquivos de log a serem coletados devem corresponder aos critérios a seguir.

- O log deve ter uma única entrada por linha ou usar um carimbo de data/hora correspondente a um dos formatos a seguir no início de cada entrada.

    AAAA-MM-DD HH:MM:SS <br>M/D/AAAA HH:MM:SS AM/PM<br>Seg DD, AAAA HH:MM:SS<br />aaMMdd hh<br />ddMMaa HH:mm:ss<br />aaMMdd hh<br />dd/MMM/aaaa:HH:mm:ss zzz<br />aaaa-MM-ddTHH:mm:ssK

- O arquivo de log não deve permitir log circular ou rotação de log, em que o arquivo é substituído por novas entradas.
- O arquivo de log deve usar a codificação ASCII ou UTF-8.  Não há suporte para outros formatos, como UTF-16.
- Para o Linux, não há suporte para conversão de fuso horário em carimbos de data/hora nos logs.

>[!NOTE]
> Se houver entradas duplicadas no arquivo de log, o Azure Monitor irá coletá-los. No entanto, os resultados da consulta serão inconsistentes onde os resultados do filtro mostram mais eventos do que a contagem de resultados. É importante que você valide o log para determinar se o aplicativo que cria está causando o problema e resolvê-lo se possível, antes de criar a definição de coleção de log personalizado.  
>

>[!NOTE]
> Um workspace do Log Analytics suporta os seguintes limites:
> 
> * Apenas 500 logs personalizados podem ser criados.
> * Uma tabela só dá suporte a até 500 colunas. 
> * O número máximo de caracteres para o nome da coluna é 500. 
>

>[!IMPORTANT]
>A coleta de log Personalizada requer que o aplicativo que está gravando o arquivo de log libere o conteúdo do log para o disco periodicamente. Isso ocorre porque a coleção de logs personalizada se baseia em notificações de alteração do sistema de arquivos para o arquivo de log que está sendo acompanhado.

## <a name="defining-a-custom-log"></a>Definindo um log personalizado
Use o procedimento a seguir para definir um arquivo de log personalizado.  Role até o final deste artigo para encontrar um passo a passo de um exemplo de adição de um log personalizado.

### <a name="step-1-open-the-custom-log-wizard"></a>Etapa 1. Abrir o Custom Log Wizard (Assistente de Log Personalizado)
O Assistente de Log Personalizado é executado no portal do Azure e permite que você defina um novo log personalizado para ser coletado.

1. No portal do Azure, escolha **workspaces do Log Analytics** > seu workspace > **Configurações Avançadas**.
2. Clique em **dados**  >  **logs personalizados**.
3. Por padrão, todas as alterações de configuração são automaticamente envidas por push para todos os agentes. Para agentes do Linux, um arquivo de configuração é enviado para o coletor de dados Fluentd.
4. Clique e **Adicionar+** para abrir o Custom Log Wizard (Assistente de Log Personalizado).

### <a name="step-2-upload-and-parse-a-sample-log"></a>Etapa 2. Carregar e analisar um log de exemplo
Inicie carregando um exemplo de log personalizado.  O assistente analisará e exibirá as entradas nesse arquivo para validação.  O Azure Monitor usará o delimitador que você especificar para identificar cada registro.

**Nova Linha** é o delimitador padrão e é usado para arquivos de log que têm uma única entrada por linha.  Se a linha começar com uma data e hora em um dos formatos disponíveis, você poderá especificar um delimitador **Carimbo de data/hora** , que dá suporte a entradas que se estendem por mais de uma linha.

Se um delimitador de carimbo de data/hora for usado, a propriedade TimeGenerated de cada registro armazenado no Azure Monitor será preenchido com a data/hora especificada para essa entrada no arquivo de log.  Se um delimitador de nova linha for usado, TimeGenerated será populada com a data e hora em que o Azure Monitor coletou a entrada.

1. Clique em **Procurar** e navegue até um arquivo de exemplo.  Observe que esse botão pode ser rotulado como **Escolher Arquivo** em alguns navegadores.
2. Clique em **Avançar**.
3. O Custom Log Wizard (Assistente de Log Personalizado) carregará o arquivo e listará os registros que identificar.
4. Altere o delimitador usado para identificar um novo registro e selecione o delimitador que melhor identifica os registros no arquivo de log.
5. Clique em **Avançar**.

### <a name="step-3-add-log-collection-paths"></a>Etapa 3. Adicionar caminhos de coleta de log
Você deve definir um ou mais caminhos no agente no qual ele pode localizar o log personalizado.  Você pode fornecer um caminho e um nome específicos para o arquivo de log ou pode especificar um caminho com um caractere curinga para o nome. Isso dá suporte a aplicativos que criam um novo arquivo por dia ou quando um arquivo atinge um determinado tamanho. Você também pode fornecer vários caminhos para um único arquivo de log.

Por exemplo, um aplicativo pode criar um arquivo de log por dia com a data incluída no nome, como log20100316.txt. Um padrão para tal log pode ser *log\*.txt*, que se aplica a qualquer arquivo de log após o esquema de nomenclatura do aplicativo.

A tabela a seguir fornece exemplos de padrões válidos para especificar diferentes arquivos de log.

| Descrição | Caminho |
|:--- |:--- |
| Todos os arquivos em *C:\Logs* com extensão .txt no agente do Windows |C:\Logs\\\*.txt |
| Todos os arquivos em *C:\Logs* cujo nome começa com log e uma extensão .txt no agente do Windows |C:\Logs\log\*.txt |
| Todos os arquivos em */var/log/audit* com extensão .txt no agente do Linux |/var/log/audit/*.txt |
| Todos os arquivos em */var/log/audit* cujo nome começa com log e uma extensão .txt no agente do Linux |/var/log/audit/log\*.txt |

1. Selecione Windows ou Linux para especificar qual formato de caminho você está adicionando.
2. Digite o caminho e clique no **+** botão.
3. Repita o processo para todos os caminhos adicionais.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Etapa 4. Fornecer um nome e descrição para o log
O nome especificado será usado para o tipo de log, conforme descrito acima.  Ele sempre terminará com _CL para distingui-lo como um log personalizado.

1. Digite um nome para o log.  O sufixo **\_ CL** é fornecido automaticamente.
2. Adicione uma **Descrição** opcional.
3. Clique em **Próximo** para salvar a definição do log personalizado.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Etapa 5. Validar que os logs personalizados estão sendo coletados
Pode demorar até uma hora para os dados iniciais de um novo log personalizado aparecerem no Azure Monitor.  Ele começará a coletar entradas dos logs encontrados no caminho especificado do ponto que você definiu o log personalizado.  Ele não manterá as entradas que você carregou durante a criação de log personalizado, mas coletará as entradas já existentes nos arquivos de log que localizar.

Depois que o Azure Monitor iniciar a coleta de log personalizado, seus registros estarão disponíveis com uma consulta de log.  Use o nome que você atribuiu ao log personalizado como o **Tipo** em sua consulta.

> [!NOTE]
> Se a propriedade RawData estiver ausente da busca, você precisará fechar e reabrir o navegador.

### <a name="step-6-parse-the-custom-log-entries"></a>Etapa 6. Analisar as entradas do log personalizado
A entrada de log inteira será armazenada em uma única propriedade chamada **RawData**.  Você provavelmente desejará separar as diferentes partes de informações em cada entrada em propriedades individuais armazenadas para cada registro. Consulte a [Analisar dados de texto no Azure Monitor](../logs/parse-text.md) para opções de análise **RawData** em várias propriedades.

## <a name="removing-a-custom-log"></a>Removendo um log personalizado
Use o seguinte processo no portal do Azure para remover um log personalizado que você definiu anteriormente.

1. No menu **Dados** nas **Configurações Avançadas** do workspace, selecione **Logs Personalizados** para listar todos os seus logs personalizados.
2. Clique em **Remover** ao lado do log personalizado a ser removido.

## <a name="data-collection"></a>Coleta de dados
O Azure Monitor coletará novas entradas de cada log personalizado aproximadamente a cada cinco minutos.  O agente registrará seu lugar em cada arquivo de log do qual ele realiza a coleta.  Se o agente ficar offline por um período de tempo, o Azure Monitor coletará entradas de onde ele parou, mesmo que as entradas tenham sido criadas enquanto o agente estava offline.

Todo o conteúdo da entrada de log é gravado em uma única propriedade chamada **RawData**.  Consulte [Analisar dados de texto no Azure Monitor](../logs/parse-text.md) para métodos para analisar cada entrada de log importada em várias propriedades.

## <a name="custom-log-record-properties"></a>Propriedades de registro do log personalizado
Os registros de log personalizado têm um tipo com o nome do log que você fornece e as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated |Data e hora em que o registro foi coletado pelo Azure Monitor.  Se o log usar um delimitador baseado na hora, essa será a hora coletada da entrada. |
| SourceSystem |Tipo de registro do qual os dados foram coletados. <br> OpsManager – agente do Windows, conexão direta ou System Center Operations Manager <br>  Linux: todos os agentes do Linux |
| RawData |Texto completo da entrada coletada. Você provavelmente desejará [analisar esses dados em propriedades individuais](../logs/parse-text.md). |
| ManagementGroupName |Nome do grupo de gerenciamento para agentes do System Center Operations Manager.  Para outros agentes, ele é AOI-\<workspace ID\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Passo a passo do exemplo de adição de um log personalizado
A seção a seguir explica passo a passo um exemplo de criação de um log personalizado.  O log de exemplo sendo coletado tem uma única entrada em cada linha que começa com uma data e hora e campos delimitados por vírgula para o código, status e mensagem.  Várias entradas de exemplo são mostradas abaixo.

```output
2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2019-08-27 01:38:22 302,Error,Application could not connect to database
2019-08-27 01:31:34 303,Error,Application lost connection to database
```

### <a name="upload-and-parse-a-sample-log"></a>Carregar e analisar um log de exemplo
Fornecemos um dos arquivos de log e podemos ver os eventos que ele coletará.  Nesse caso, Nova Linha é um delimitador suficiente.  Se uma única entrada de log pudesse abranger várias linhas, seria necessário usar um delimitador de carimbo de data/hora.

![Carregar e analisar um log de exemplo](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Adicionar caminhos de coleta de log
Os arquivos de log estarão localizados em *C:\MyApp\Logs*.  Será criado um novo arquivo por dia com um nome que inclui a data no padrão *appAAAAMMDD.log*.  Um padrão suficiente para esse log seria *C:\MyApp\Logs \\ \* . log*.

![Caminho da coleta do log](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Fornecer um nome e descrição para o log
Usamos um nome de *MyApp_CL* e digitamos uma **Descrição**.

![Nome do log](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Validar que os logs personalizados estão sendo coletados
Usamos uma consulta simples de *MyApp_CL* para retornar todos os registros do log coletado.

![Consulta de log sem campos personalizados](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Alternativas para logs personalizados
Embora os logs personalizados sejam úteis se seus dados se ajustarem aos critérios listados acima, há casos como os seguintes, onde você precisa de outra estratégia:

- Os dados não se ajustam à estrutura necessária, como ter o carimbo de data/hora em um formato diferente.
- O arquivo de log não está de acordo com os requisitos como a codificação do arquivo ou uma estrutura de pastas sem suporte.
- Os dados requerem pré-processamento ou filtragem antes da coleta. 

Nos casos em que seus dados não puderem ser coletados com logs personalizados, considere as seguintes estratégias alternativas:

- Usar um script personalizado ou outro método para gravar dados em [Eventos do Windows](data-sources-windows-events.md) ou [Syslog](data-sources-syslog.md) coletados pelo Azure Monitor. 
- Enviar os dados diretamente ao Azure Monitor usando a [API do coletor de dados HTTP](../logs/data-collector-api.md). 

## <a name="next-steps"></a>Próximas etapas
* Consulte [Analisar dados de texto no Azure Monitor](../logs/parse-text.md) para métodos para analisar cada entrada de log importada em várias propriedades.
* Saiba mais sobre [registrar consultas](../logs/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções.