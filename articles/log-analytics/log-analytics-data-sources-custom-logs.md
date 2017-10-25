---
title: Coletar logs personalizados no OMS Log Analytics | Microsoft Docs
description: "O Log Analytics pode coletar eventos de arquivos de texto em computadores com Windows e Linux.  Este artigo descreve como definir um novo log personalizado e os detalhes dos registros que ele cria no repositório do OMS."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/15/2017
ms.author: bwren
ms.openlocfilehash: b7f28868e3ffdf95dbe39872f382e7c97eae692c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="custom-logs-in-log-analytics"></a>Logs personalizados no Log Analytics
A fonte de dados de logs personalizados no Log Analytics permite que você colete eventos de arquivos de texto em computadores com Windows e Linux. Muitos aplicativos registram informações em arquivos de texto em vez de serviços de registro standard, como o log de eventos do Windows ou Syslog.  Depois de coletados, você pode analisar cada registro no log em campos individuais usando o recurso [Campos Personalizados](log-analytics-custom-fields.md) do Log Analytics.

![Coleta de log personalizado](media/log-analytics-data-sources-custom-logs/overview.png)

Os arquivos de log a serem coletados devem corresponder aos critérios a seguir.

- O log deve ter uma única entrada por linha ou usar um carimbo de data/hora correspondente a um dos formatos a seguir no início de cada entrada.

    AAAA-MM-DD HH:MM:SS <br>M/D/AAAA HH:MM:SS AM/PM <br>Mês DD,AAAA HH:MM:SS

- O arquivo de log não deve permitir atualizações circulares em que o arquivo é substituído por novas entradas.
- O arquivo de log deve usar a codificação ASCII ou UTF-8.  Não há suporte para outros formatos, como UTF-16.

>[!NOTE]
>Se houver entradas duplicadas no arquivo de log, análise de Log coletará-los.  No entanto, os resultados da pesquisa será inconsistente onde os resultados do filtro mostram mais eventos do que a contagem de resultados.  É importante que você valide o log para determinar se o aplicativo que cria está causando o problema e resolvê-lo se possível, antes de criar a definição de coleção de log personalizado.  
>
  
## <a name="defining-a-custom-log"></a>Definindo um log personalizado
Use o procedimento a seguir para definir um arquivo de log personalizado.  Role até o final deste artigo para encontrar um passo a passo de um exemplo de adição de um log personalizado.

### <a name="step-1-open-the-custom-log-wizard"></a>Etapa 1. Abrir o Custom Log Wizard (Assistente de Log Personalizado)
O Custom Log Wizard (Assistente de Log Personalizado) é executado no portal do OMS e permite que você defina um novo log personalizado para coletar.

1. No portal do OMS, vá para **Configurações**.
2. Clique em **Dados** e em **Custom logs**.
3. Por padrão, todas as alterações de configuração são automaticamente envidas por push para todos os agentes.  Para agentes do Linux, um arquivo de configuração é enviado para o coletor de dados Fluentd.  Se você quiser modificar esse arquivo manualmente em cada agente do Linux, desmarque a caixa *Apply below configuration to my Linux machines*(Aplicar as configurações abaixo aos computadores Linux).
4. Clique e **Adicionar+** para abrir o Custom Log Wizard (Assistente de Log Personalizado).

### <a name="step-2-upload-and-parse-a-sample-log"></a>Etapa 2. Carregar e analisar um log de exemplo
Inicie carregando um exemplo de log personalizado.  O assistente analisará e exibirá as entradas nesse arquivo para validação.  O Log Analytics usará o delimitador que você especificar para identificar cada registro.

**Nova Linha** é o delimitador padrão e é usado para arquivos de log que têm uma única entrada por linha.  Se a linha começar com uma data e hora em um dos formatos disponíveis, você poderá especificar um delimitador **Carimbo de data/hora** , que dá suporte a entradas que se estendem por mais de uma linha.

Se for usado um delimitador de carimbo de data/hora, a propriedade TimeGenerated de cada registro armazenado no OMS será populada com a data/hora especificada para a entrada no arquivo de log.  Se um delimitador de nova linha for usado, TimeGenerated será populada com a data e hora em que o Log Analytics coletou a entrada.

> [!NOTE]
> O Log Analytics atualmente trata a data/hora coletadas de um log usando um delimitador de carimbo de data/hora como UTC.  Isso em breve será alterado para usar o fuso horário no agente.
>
>

1. Clique em **Procurar** e navegue até um arquivo de exemplo.  Observe que esse botão pode ser rotulado como **Escolher Arquivo** em alguns navegadores.
2. Clique em **Avançar**.
3. O Custom Log Wizard (Assistente de Log Personalizado) carregará o arquivo e listará os registros que identificar.
4. Altere o delimitador usado para identificar um novo registro e selecione o delimitador que melhor identifica os registros no arquivo de log.
5. Clique em **Avançar**.

### <a name="step-3-add-log-collection-paths"></a>Etapa 3. Adicionar caminhos de coleta de log
Você deve definir um ou mais caminhos no agente no qual ele pode localizar o log personalizado.  Você pode fornecer um caminho e um nome específicos para o arquivo de log ou pode especificar um caminho com um caractere curinga para o nome.  Isso dá suporte a aplicativos que criam um novo arquivo por dia ou quando um arquivo atinge um determinado tamanho.  Você também pode fornecer vários caminhos para um único arquivo de log.

Por exemplo, um aplicativo pode criar um arquivo de log por dia com a data incluída no nome, como log20100316.txt. Um padrão para tal log pode ser *log\*.txt*, que se aplica a qualquer arquivo de log após o esquema de nomenclatura do aplicativo.

A tabela a seguir fornece exemplos de padrões válidos para especificar diferentes arquivos de log.

| Descrição | Caminho |
|:--- |:--- |
| Todos os arquivos em *C:\Logs* com extensão .txt no agente do Windows |C:\Logs\\\*.txt |
| Todos os arquivos em *C:\Logs* cujo nome começa com log e uma extensão .txt no agente do Windows |C:\Logs\log\*.txt |
| Todos os arquivos em */var/log/audit* com extensão .txt no agente do Linux |/var/log/audit/*.txt |
| Todos os arquivos em */var/log/audit* cujo nome começa com log e uma extensão .txt no agente do Linux |/var/log/audit/log\*.txt |

1. Selecione Windows ou Linux para especificar qual formato de caminho você está adicionando.
2. Digite o caminho e clique no botão **+** .
3. Repita o processo para todos os caminhos adicionais.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Etapa 4. Fornecer um nome e descrição para o log
O nome especificado será usado para o tipo de log, conforme descrito acima.  Ele sempre terminará com _CL para distingui-lo como um log personalizado.

1. Digite um nome para o log.  O sufixo **\_CL** é fornecido automaticamente.
2. Adicione uma **Descrição**opcional.
3. Clique em **Próximo** para salvar a definição do log personalizado.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Etapa 5. Validar que os logs personalizados estão sendo coletados
Pode demorar até uma hora para os dados iniciais de um novo log personalizado aparecerem no Log Analytics.  Ele começará a coletar entradas dos logs encontrados no caminho especificado do ponto que você definiu o log personalizado.  Ele não manterá as entradas que você carregou durante a criação de log personalizado, mas coletará as entradas já existentes nos arquivos de log que localizar.

Depois que o Log Analytics iniciar a coleta de log personalizado, seus registros estarão disponíveis com uma Pesquisa de Log.  Use o nome que você atribuiu ao log personalizado como o **Tipo** em sua consulta.

> [!NOTE]
> Se a propriedade RawData estiver ausente da pesquisa, você precisará fechar e reabrir o navegador.
>
>

### <a name="step-6-parse-the-custom-log-entries"></a>Etapa 6. Analisar as entradas do log personalizado
A entrada de log inteira será armazenada em uma única propriedade chamada **RawData**.  Você provavelmente desejará separar as diferentes partes de informações em cada entrada em propriedades individuais armazenados no registro.  Você faz isso usando a funcionalidade [Campos Personalizados](log-analytics-custom-fields.md) do Log Analytics.

As etapas detalhadas para analisar a entrada de log personalizado não são fornecidas aqui.  Consulte a documentação dos [Campos Personalizados](log-analytics-custom-fields.md) para encontrar essas informações.

## <a name="disabling-a-custom-log"></a>Desabilitar um log personalizado
Você não pode remover uma definição de log personalizado depois de ele ter sido criado, mas pode desabilitá-lo, removendo todos os seus caminhos de coleção.

1. No portal do OMS, vá para **Configurações**.
2. Clique em **Dados** e em **Custom logs**.
3. Clique em **Detalhes**, ao lado da definição do log personalizado, para desabilitar.
4. Remova cada um dos caminhos de coleção para a definição do log personalizado.

## <a name="data-collection"></a>Coleta de dados
O Log Analytics coletará novas entradas de cada log personalizado aproximadamente a cada cinco minutos.  O agente registrará seu lugar em cada arquivo de log do qual ele realiza a coleta.  Se o agente ficar offline por um período de tempo, o Log Analytics coletará entradas de onde ele parou, mesmo que as entradas tenham sido criadas enquanto o agente estava offline.

Todo o conteúdo da entrada de log é gravado em uma única propriedade chamada **RawData**.  Você pode analisar isso em várias propriedades que podem ser analisadas e pesquisadas separadamente definindo [Campos Personalizados](log-analytics-custom-fields.md) depois de ter criado o log personalizado.

## <a name="custom-log-record-properties"></a>Propriedades de registro do log personalizado
Os registros de log personalizado têm um tipo com o nome do log que você fornece e as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated |Data e hora em que o registro foi coletado pelo Log Analytics.  Se o log usar um delimitador baseado na hora, essa será a hora coletada da entrada. |
| SourceSystem |Tipo de registro do qual os dados foram coletados. <br> OpsManager – agente do Windows, conexão direta ou System Center Operations Manager <br> Linux: todos os agentes do Linux |
| RawData |Texto completo da entrada coletada. |
| ManagementGroupName |Nome do grupo de gerenciamento para agentes do System Center Operations Manager.  Para outros agentes, ele é AOI-\<ID do espaço de trabalho\> |

## <a name="log-searches-with-custom-log-records"></a>Pesquisas de log com registros de log personalizados
Os registros de logs personalizados são armazenados no repositório do OMS, exatamente como registros de qualquer outra fonte de dados.  Eles terão um tipo correspondente ao nome que você fornecer ao definir o log, então você pode usar a propriedade Type em sua pesquisa para recuperar registros coletados de um log específico.

A tabela a seguir fornece diferentes exemplos de pesquisas de log que recuperam registros de logs personalizados.

| Consultar | Descrição |
|:--- |:--- |
| Type=MyApp_CL |Todos os eventos de um log personalizado chamado MyApp_CL. |
| Type=MyApp_CL Severity_CF=error |Todos os eventos de um log personalizado chamado MyApp_CL com um valor de *erro* em um campo personalizado chamado *Severity_CF*. |

>[!NOTE]
> Se o seu espaço de trabalho fosse atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), as consultas acima seriam alteradas para o demonstrado a seguir.

> | Consultar | Descrição |
|:--- |:--- |
| MyApp_CL |Todos os eventos de um log personalizado chamado MyApp_CL. |
| MyApp_CL &#124; where Severity_CF=="error" |Todos os eventos de um log personalizado chamado MyApp_CL com um valor de *erro* em um campo personalizado chamado *Severity_CF*. |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Passo a passo do exemplo de adição de um log personalizado
A seção a seguir explica passo a passo um exemplo de criação de um log personalizado.  O log de exemplo sendo coletado tem uma única entrada em cada linha que começa com uma data e hora e campos delimitados por vírgula para o código, status e mensagem.  Várias entradas de exemplo são mostradas abaixo.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Carregar e analisar um log de exemplo
Fornecemos um dos arquivos de log e podemos ver os eventos que ele coletará.  Nesse caso, Nova Linha é um delimitador suficiente.  Se uma única entrada de log pudesse abranger várias linhas, seria necessário usar um delimitador de carimbo de data/hora.

![Carregar e analisar um log de exemplo](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Adicionar caminhos de coleta de log
Os arquivos de log estarão localizados em *C:\MyApp\Logs*.  Será criado um novo arquivo por dia com um nome que inclui a data no padrão *appAAAAMMDD.log*.  Um padrão suficiente para esse log seria *C:\MyApp\Logs\\\\*.log*.

![Caminho da coleta do log](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Fornecer um nome e descrição para o log
Usamos um nome de *MyApp_CL* e digitamos uma **Descrição**.

![Nome do log](media/log-analytics-data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Validar que os logs personalizados estão sendo coletados
Usamos uma consulta de *Type=MyApp_CL* para retornar todos os registros do log coletado.

![Consulta de log sem campos personalizados](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Analisar as entradas do log personalizado
Usamos campos personalizados para definir os campos *EventTime*, *Code*, *Status* e *Message* e podemos ver a diferença nos registros que são retornados pela consulta.

![Consulta de log com campos personalizados](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>Próximas etapas
* Use [Campos Personalizados](log-analytics-custom-fields.md) para analisar as entradas no log personalizado em campos personalizados.
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções.
