---
title: "Saídas do Stream Analytics: opções de armazenamento e análise | Microsoft Docs"
description: "Saiba mais sobre opções de saídas de dados do Stream Analytics, incluindo Power BI para resultados da análise."
keywords: "transformação de dados, resultados da análise, opções de armazenamento de dados"
services: stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: ba6697ac-e90f-4be3-bafd-5cfcf4bd8f1f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 33d0b9aa37cc92dda27f1cf21f1d393b42b8c09b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="stream-analytics-outputs-options-for-storage-analysis"></a>Saídas do Stream Analytics: opções de armazenamento, análise
Ao criar um trabalho do Stream Analytics, considere como os dados resultantes serão consumidos. Como você exibirá os resultados do trabalho do Stream Analytics e onde os armazenará?

Para poder habilitar vários padrões de aplicativo, o Stream Analytics do Azure disponibiliza opções diferentes de armazenamento de saída e de exibição dos resultados da análise. Isso facilita a exibição da saída do trabalho e proporciona flexibilidade no consumo e armazenamento da saída do trabalho para data warehouse e outras finalidades. Qualquer saída configurada no trabalho deve existir antes do trabalho ser iniciado e dos eventos começarem a fluir. Por exemplo, se você usar o armazenamento de Blobs como uma saída, o trabalho não criará uma conta de armazenamento automaticamente. Ele precisa ser criado pelo usuário antes do trabalho ASA ser iniciado.

## <a name="azure-data-lake-store"></a>Repositório Azure Data Lake
O Stream Analytics dá suporte ao [Repositório Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/). Esse armazenamento permite que você armazene dados de qualquer tamanho, tipo e velocidade de ingestão para análises operacionais e exploratórias. Além disso, o Stream Analytics deve estar autorizado a acessar o Repositório Data Lake. Detalhes sobre a autorização e como se inscrever no Data Lake Store (se necessário) são abordados no [artigo sobre a saída do Data Lake](stream-analytics-data-lake-output.md).

### <a name="authorize-an-azure-data-lake-store"></a>Autorizar um Azure Data Lake Store
Quando o Data Lake Store é selecionado como uma saída no portal do Azure, você será solicitado a autorizar uma conexão com um Data Lake Store existente.  

![Autorizar o Repositório Data Lake](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

Em seguida, preencha as propriedades da saída do Data Lake Store, conforme mostrado abaixo:

![Autorizar o Repositório Data Lake](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

A tabela abaixo lista os nomes de propriedade e sua descrição para a criação de uma saída do Repositório Data Lake.

<table>
<tbody>
<tr>
<td><B>NOME DA PROPRIEDADE</B></td>
<td><B>DESCRIÇÃO</B></td>
</tr>
<tr>
<td>Alias de saída</td>
<td>Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para esse Repositório Data Lake.</td>
</tr>
<tr>
<td>Nome da conta</td>
<td>O nome da conta do Data Lake Store para a qual você está enviando a saída. Você verá uma lista suspensa de contas do Repositório Data Lake às quais o usuário conectado ao portal tem acesso.</td>
</tr>
<tr>
<td>Padrão de prefixo de caminho</td>
<td>A nomenclatura de arquivo seguirá a seguinte convenção: <BR>{Padrão de prefixo de caminho}/schemaHashcode_Guid_Number.extension <BR> <BR>Exemplo de arquivos de saída:<BR>Myoutput/20170901/00/45434_gguid_1.csv <BR>Myoutput/20170901/01/45434_gguid_1.csv <BR> <BR>Além disso, esta são as situações nas quais um novo arquivo é criado:<BR>1. Alteração no esquema de saída <BR>2. Reinicialização interna ou externa de um trabalho<BR><BR>Além disso, se o padrão de caminho do arquivo não contiver uma "/" à direita, o último padrão no caminho do arquivo será tratado como um prefixo de nome de arquivo.<BR><BR>Exemplo:<BR>Para o padrão de caminho: folder1/logs/HH, o arquivo gerado pode parecer com o seguinte: folder1/logs/02_134343_gguid_1.csv</td>
</tr>
<tr>
<td>Formato de data [<I>opcional</I>]</td>
<td>Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [<I>opcional</I>]</td>
<td>Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH.</td>
</tr>
<tr>
<td>Formato de serialização do evento</td>
<td>Formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro.</td>
</tr>
<tr>
<td>Codificação</td>
<td>Se o formato for CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, tab e barra vertical.</td>
</tr>
<tr>
<td>Formatar</td>
<td>Aplicável somente para serialização JSON. Uma linha separada especifica que a saída será formatada com cada objeto JSON separado por uma nova linha. Matriz especifica que a saída será formatada como uma matriz de objetos JSON. Essa matriz será fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar JSON separado por linha, já que não exige nenhuma manipulação especial enquanto o arquivo de saída ainda estiver sendo gravado.</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Renovar autorização do Repositório Data Lake
Você precisará autenticar novamente sua conta do Data Lake Store caso sua senha tenha sido alterada depois de seu trabalho ser criado ou autenticado pela última vez.

![Autorizar o Repositório Data Lake](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>Banco de Dados SQL
[banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) pode ser usado como saída para os dados que sejam relacionais por natureza ou para aplicativos que dependam de o conteúdo ser hospedado em um banco de dados relacional. Os trabalhos do Stream Analytics gravarão em uma tabela existente em um banco de dados SQL do Azure.  Observe que o esquema da tabela deve corresponder exatamente aos campos e seus tipos sendo a saída do seu trabalho. Um [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) também pode ser especificado como uma saída por meio da opção de saída do Banco de Dados SQL (esse é um recurso na fase de visualização). A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída de banco de dados SQL.

| Nome da Propriedade | Descrição |
| --- | --- |
| Alias de saída |Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para esse banco de dados. |
| Banco de dados |O nome do banco de dados para o qual você está enviando a saída |
| Nome do Servidor |Nome do servidor de banco de dados SQL do Azure |
| Nome de Usuário |O nome de usuário que tem acesso de gravação no banco de dados |
| Senha |A senha para se conectar ao banco de dados |
| Tabela |O nome da tabela em que a saída será gravada. O nome da tabela diferencia maiúsculas de minúsculas e o esquema da tabela deve corresponder exatamente ao número de campos e seus tipos sendo gerados por sua saída de trabalho. |

> [!NOTE]
> Atualmente, há suporte para a oferta do Banco de Dados SQL uma saída do trabalho do Stream Analytics. No entanto, não há suporte para a execução de uma Máquina Virtual do Azure que executa o SQL Server com um banco de dados anexado. Isso está sujeito à mudança em versões futuras.
> 
> 

## <a name="blob-storage"></a>Armazenamento de blob
O armazenamento de Blob oferece uma solução econômica e escalonável para armazenar grandes quantidades de dados não estruturados na nuvem.  Para obter uma introdução sobre o Armazenamento de blob do Azure e seu uso, confira a documentação em [Como usar blobs](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída de banco de blob.

<table>
<tbody>
<tr>
<td>Nome da Propriedade</td>
<td>Descrição</td>
</tr>
<tr>
<td>Alias de saída</td>
<td>Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de blob.</td>
</tr>
<tr>
<td>Conta de armazenamento</td>
<td>O nome da conta de armazenamento para o qual você está enviando a saída</td>
</tr>
<tr>
<td>Chave da conta de armazenamento</td>
<td>A chave secreta associada à conta de armazenamento.</td>
</tr>
<tr>
<td>Contêiner de armazenamento</td>
<td>Os contêineres fornecem um agrupamento lógico de blobs armazenados no serviço Blob do Microsoft Azure. Quando você carrega um blob no serviço Blob, você deve especificar um contêiner para aquele blob.</td>
</tr>
<tr>
<td>Padrão de prefixo do caminho [opcional]</td>
<td>O padrão do caminho do arquivo usado para gravar seus blobs no contêiner especificado. <BR> No padrão de caminho, você pode optar por usar uma ou mais instâncias das duas variáveis a seguir para especificar a frequência com a qual os blobs são gravados: <BR> {data}, {hora} <BR> Exemplo 1: cluster1/logs /{data}/{hora} <BR> Exemplo 2: cluster1/logs/{data} <BR> <BR> A nomenclatura de arquivo seguirá a seguinte convenção: <BR> {Padrão de prefixo de caminho}/schemaHashcode_Guid_Number.extension <BR> <BR> Exemplo de arquivos de saída: <BR> Myoutput/20170901/00/45434_gguid_1.csv <BR> Myoutput/20170901/01/45434_gguid_1.csv <BR> <BR> Além disso, esta são as situações nas quais um novo arquivo é criado: <BR> 1. O arquivo atual excede o número máximo permitido de blocos (atualmente 50.000) <BR> 2. Alteração no esquema de saída <BR> 3. Reinicialização interna ou externa de um trabalho  </td>
</tr>
<tr>
<td>Formato de data [opcional]</td>
<td>Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH.</td>
</tr>
<tr>
<td>Formato de serialização do evento</td>
<td>Formato de serialização para dados de saída.  Há suporte para JSON, CSV e Avro.</td>
</tr>
<tr>
<td>Codificação</td>
<td>Se o formato for CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, tab e barra vertical.</td>
</tr>
<tr>
<td>Formatar</td>
<td>Aplicável somente para serialização JSON. Uma linha separada especifica que a saída será formatada com cada objeto JSON separado por uma nova linha. Matriz especifica que a saída será formatada como uma matriz de objetos JSON. Essa matriz será fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar JSON separado por linha, já que não exige nenhuma manipulação especial enquanto o arquivo de saída ainda estiver sendo gravado.</td>
</tr>
</tbody>
</table>

## <a name="event-hub"></a>Hub de evento
[Hubs de Eventos](https://azure.microsoft.com/services/event-hubs/) são um ingestor de eventos altamente escalonável de publicação/assinatura. Ele pode coletar milhões de eventos por segundo.  Uma utilização de um Hub de Eventos como saída é quando a saída de um trabalho do Stream Analytics será a entrada de outro trabalho de streaming.

Há alguns parâmetros que são necessários para configurar fluxos de dados de Hub de Eventos como uma saída.

| Nome da Propriedade | Descrição |
| --- | --- |
| Alias de saída |Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para esse Hub de eventos. |
| Namespace do Barramento de Serviço |Um namespace Barramento de Serviço é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo Hub de Eventos, você também criou um namespace Barramento de Serviço. |
| Hub de evento |O nome da sua saída de Hub de eventos |
| Nome da política do Hub de Eventos. |A política de acesso compartilhada, que pode ser criada na guia Configurar o Hub de Eventos. Cada política de acesso compartilhado terá um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política do Hub de eventos |A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço |
| Coluna de chave de partição [opcional] |Esta coluna contém a chave de partição para saída do Hub de Eventos. |
| Formato de serialização do evento |Formato de serialização para dados de saída.  Há suporte para JSON, CSV e Avro. |
| Codificação |Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador |Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar |Aplicável somente para serialização JSON. Uma linha separada especifica que a saída será formatada com cada objeto JSON separado por uma nova linha. Matriz especifica que a saída será formatada como uma matriz de objetos JSON. Essa matriz será fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar JSON separado por linha, já que não exige nenhuma manipulação especial enquanto o arquivo de saída ainda estiver sendo gravado. |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) pode ser usado como saída de um trabalho do Stream Analytics para fornecer uma experiência rica de visualização dos resultados da análise. Essa funcionalidade pode ser usada para painéis operacionais, geração de relatórios e relatórios orientados por métricas.

### <a name="authorize-a-power-bi-account"></a>Autorizar uma conta do Power BI
1. Quando o Power BI é selecionado como uma saída no portal do Azure, você será solicitado a autorizar um usuário existente do Power BI ou criar uma nova conta do Power BI.  
   
   ![Autorizar usuário do Power BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  
2. Crie uma nova conta se você não ainda tiver uma e, em seguida, clique em Autorizar agora.  Uma tela como essa será apresentada.  
   
   ![Conta do Azure Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  
3. Nesta etapa, forneça a conta corporativa ou de estudante para autorizar a saída do Power BI. Se você não se inscreveu ainda no Power BI, escolha a opção Inscreva-se agora. A conta de trabalho ou escolar usada para o Power BI poderia ser diferente da conta de assinatura do Azure na qual você está conectado.

### <a name="configure-the-power-bi-output-properties"></a>Configurar as propriedades de saída do Power BI
Uma vez que a conta do Power BI foi autenticada, você pode configurar as propriedades de saída do Power BI. A tabela a seguir é a lista de nomes de propriedade e sua descrição para configurar a saída do Power BI.

| Nome da Propriedade | Descrição |
| --- | --- |
| Alias de saída |Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para essa saída do Power BI. |
| Agrupar o espaço de trabalho |Para permitir o compartilhamento de dados com outros usuários do Power BI, você pode selecionar grupos dentro de sua conta do Power BI ou escolher "Meu espaço de trabalho" se não quiser gravar em um grupo.  Atualizar um grupo existente requer a renovação da autenticação do Power BI. |
| Nome do conjunto de dados |Forneça um nome de conjunto de dados que você deseja que seja usada para a saída do Power BI |
| Nome da tabela |Forneça um nome de tabela sob o conjunto de dados da saída do Power BI. Atualmente, a saída do Power BI de trabalhos do Stream Analytics só podem ter uma tabela em um conjunto de dados. |

Para obter um passo a passo de configuração de uma saída e de um painel do Power BI, consulte o artigo [Stream Analytics do Azure e Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> Não crie explicitamente o conjunto de dados e a tabela no painel do Power BI. O conjunto de dados e a tabela serão preenchidos automaticamente quando o trabalho é iniciado e o trabalho começa a produzir a saída no Power BI. Observe que se a consulta de trabalho não gerar resultados, o conjunto de dados e a tabela não serão criados. Tenha em mente também que se o Power BI já tiver um conjunto de dados e uma tabela com o mesmo nome fornecido no trabalho do Stream Analytics, os dados existentes serão substituídos.
> 
> 

### <a name="schema-creation"></a>Criação de Esquema
O Stream Analytics do Azure cria um conjunto de dados do Power BI e uma tabela em nome do usuário, se eles ainda não existirem. Em todos os outros casos, a tabela é atualizada com os novos valores. No momento, há uma limitação de apenas uma tabela dentro de um conjunto de dados.

### <a name="data-type-conversion-from-asa-to-power-bi"></a>Conversão de tipo de dados do ASA para o Power BI
O Stream Analytics do Azure atualiza o modelo de dados dinamicamente no tempo de execução se o esquema de saída mudar. Alterações de nome de coluna, alterações de tipo de coluna e a adição ou remoção de colunas são controladas.

Esta tabela abrange as conversões de tipo de dados dos [Tipos de dados do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835065.aspx) para Power BIs [Tipos de EDM (Modelo de Dados de Entidade)](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) se um conjunto de dados do POWER BI e uma tabela não existirem.


Do Stream Analytics | Para o Power BI
-----|-----|------------
bigint | Int64
nvarchar(max) | Cadeia de caracteres
datetime | DateTime
flutuante | Duplo
Matriz de registro | Tipo de cadeia de caracteres, Valor da constante “IRecord” ou “IArray”

### <a name="schema-update"></a>Atualização do Esquema
o Stream Analytics infere o esquema do modelo de dados com base no primeiro conjunto de eventos na saída. Posteriormente, se necessário, o esquema do modelo de dados é atualizado para acomodar os eventos de entrada que não se encaixam no esquema original.

A consulta `SELECT *` deve ser evitada para impedir que o esquema dinâmico atualize as linhas. Além das possíveis implicações de desempenho, ela também pode resultar na indeterminação do tempo necessário para obter os resultados. Os campos exatos que precisam ser mostradas no painel do Power BI devem ser selecionados. Além disso, os valores de dados devem estar em conformidade com o tipo de dados escolhido.


Anterior/Atual | Int64 | Cadeia de caracteres | DateTime | Duplo
-----------------|-------|--------|----------|-------
Int64 | Int64 | Cadeia de caracteres | Cadeia de caracteres | Duplo
Duplo | Duplo | Cadeia de caracteres | Cadeia de caracteres | Duplo
Cadeia de caracteres | Cadeia de caracteres | Cadeia de caracteres | Cadeia de caracteres |  | Cadeia de caracteres | 
DateTime | Cadeia de caracteres | Cadeia de caracteres |  DateTime | Cadeia de caracteres


### <a name="renew-power-bi-authorization"></a>Renovar a autorização do Power BI
Você precisará autenticar novamente sua conta do Power BI caso sua senha tenha sido alterada depois de seu trabalho ser criado ou autenticado pela última vez. Se a MFA (Multi-Factor Authentication) estiver configurada no locatário do AAD (Azure Active Directory) também será necessário renovar a autorização do Power BI a cada 2 semanas. Um sintoma desse problema é nenhuma saída de trabalho e um "erro de Autenticar usuário" nos Logs de Operação:

  ![Erro de token de atualização do Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Para resolver esse problema, pare seu trabalho em execução e vá para a saída do Power BI.  Clique no link "Renovar autorização" e reinicie o trabalho a partir da Hora da Última Interrupção para evitar a perda de dados.

  ![Autorização de renovação do Power BI](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Armazenamento de tabela
O [armazenamento de Tabelas do Azure](../storage/common/storage-introduction.md) oferece armazenamento altamente disponível e altamente escalonável, para que um aplicativo possa ser escalado automaticamente para atender à demanda dos usuários. O Armazenamento de tabela é um repositório de chave/atributo NoSQL da Microsoft que pode ser utilizado por alguém com dados estruturados, com menos restrições no esquema. O armazenamento de Tabela do Azure pode ser usado para armazenar dados de persistência e para recuperação eficiente.

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída da tabela.

| Nome da Propriedade | Descrição |
| --- | --- |
| Alias de saída |Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de tabela. |
| Conta de armazenamento |O nome da conta de armazenamento para o qual você está enviando a saída |
| Chave da conta de armazenamento |A chave de acesso associada à conta de armazenamento. |
| Nome da tabela |O nome da tabela. A tabela será criada se ainda não existir. |
| Chave de partição |O nome da coluna de saída que contém a chave da partição. A chave de partição é um identificador exclusivo para a partição em uma determinada tabela que forma a primeira parte da chave primária da entidade. É um valor de cadeia de caracteres que pode ter até 1 KB em tamanho. |
| Chave de linha |O nome da coluna de saída que contém a chave de linha. A chave de linha é um identificador exclusivo para uma entidade em uma determinada partição. Ela forma a segunda parte da chave primária da entidade. A chave de linha é um valor de cadeia de caracteres que pode ter até 1 KB em tamanho. |
| Tamanho do lote |É o número de registros para uma operação em lote. Normalmente, o padrão é suficiente para a maioria dos trabalhos; consulte a [especificação da Operação em Lote de Tabela](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) para obter mais detalhes sobre como modificar essa configuração. |
 
## <a name="service-bus-queues"></a>Filas de barramento de serviço
[Filas do barramento de serviço](https://msdn.microsoft.com/library/azure/hh367516.aspx) oferecem entrega de mensagem do tipo PEPS (primeiro a entrar, primeiro a sair) para um ou mais consumidores concorrentes. Normalmente, espera-se que as mensagens sejam recebidas e processadas pelos receptores na ordem cronológica em que foram adicionadas à fila, sendo que cada mensagem é recebida e processada por apenas um consumidor de mensagem.

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída da fila.

| Nome da Propriedade | Descrição |
| --- | --- |
| Alias de saída |Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para essa fila de Barramento de Serviço. |
| Namespace do Barramento de Serviço |Um namespace Barramento de Serviço é um contêiner para um conjunto de entidades de mensagens. |
| Nome da fila |O nome da fila do Barramento de Serviço. |
| Nome da política da fila |Ao criar uma fila, você também pode criar políticas de acesso compartilhado na guia Configurar fila. Cada política de acesso compartilhado terá um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política de fila |A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço |
| Formato de serialização do evento |Formato de serialização para dados de saída.  Há suporte para JSON, CSV e Avro. |
| Codificação |Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador |Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar |Aplicável somente para o tipo JSON. Uma linha separada especifica que a saída será formatada com cada objeto JSON separado por uma nova linha. Matriz especifica que a saída será formatada como uma matriz de objetos JSON. |

## <a name="service-bus-topics"></a>Tópicos do Service Bus
Enquanto as Filas do Barramento de Serviço fornecem um método de comunicação de um-para-um do remetente para o receptor, os [Tópicos sobre o Barramento de Serviço](https://msdn.microsoft.com/library/azure/hh367516.aspx) fornecem uma forma de comunicação de um-para-muitos.

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída da tabela.

| Nome da Propriedade | Descrição |
| --- | --- |
| Alias de saída |Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para essa tópico de Barramento de Serviço. |
| Namespace do Barramento de Serviço |Um namespace Barramento de Serviço é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo Hub de Eventos, você também criou um namespace Barramento de Serviço. |
| Nome do tópico |Tópicos são entidades de envio de mensagens, semelhantes a filas e hubs de eventos. Eles são projetados para coletar fluxos de eventos de alguns dispositivos e serviços diferentes. Quando um tópico é criado, ele também recebe um nome específico. As mensagens enviadas para um tópico não estarão disponíveis a menos que uma assinatura seja criada, portanto, certifique-se de que há uma ou mais assinaturas sob o tópico |
| Nome da política de tópico |Ao criar um tópico, você também pode criar políticas de acesso compartilhado na guia Configurar tópico. Cada política de acesso compartilhado terá um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política do tópico |A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço |
| Formato de serialização do evento |Formato de serialização para dados de saída.  Há suporte para JSON, CSV e Avro. |
 | Codificação |Se o formato for CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador |Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, tab e barra vertical. |

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
O [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) é um serviço de multimodelo de banco de dados globalmente distribuído que oferece escala elástica ilimitada em todo o mundo, consulta avançada e indexação automática em modelos de dados independentes de esquema, latência baixa assegurada e SLAs abrangentes líderes do setor.

A lista abaixo fornece detalhes dos nomes de propriedade e sua descrição para a criação de um resultado do Azure Cosmos DB.

* **Alias de Saída** : um alias para se referir a essa saída em sua consulta ASA  
* **Nome da Conta** – o nome ou o URI do ponto de extremidade da conta do Cosmos DB.  
* **Chave de Conta** – a chave de acesso compartilhado da conta do Cosmos DB.  
* **Banco de Dados** – o nome do banco de dados do Cosmos DB.  
* **Padrão do Nome de Coleção** – O nome da coleção ou seu padrão que será usado para as coleções. O formato de nome da coleção pode ser construído com o token {partição} opcional, em que as partições começam em 0. A seguir estão as entradas válidas de exemplo:  
  1\) MyCollection – uma coleção denominada “MyCollection” deve existir.  
  2\) MyCollection{partition} – estas coleções devem existir – "MyCollection0”, “MyCollection1”, “MyCollection2” e assim por diante.  
* **Chave de Partição** — opcional. Isso só será necessário se você estiver usando um token {partition} no seu padrão de nome de coleção. O nome do campo nos eventos de saída usado para especificar a chave para o particionamento de saída em várias coleções. Para uma saída de coleção única, nenhuma coluna de saída arbitrária pode ser usada, por exemplo, PartitionId.  
* **ID do Documento** : opcional. O nome do campo em eventos de saída usado para especificar a chave primária que serve de base para as operações de inserção ou atualização.  

## <a name="azure-functions-in-preview"></a>Azure Functions (em versão prévia)
O Azure Functions é um serviço de computação sem servidor que lhe permite executar código sob demanda sem a necessidade de provisionar explicitamente ou gerenciar a infraestrutura. Ele permite que você implemente código que é disparado por eventos que ocorrem no Azure ou por serviços de terceiros.  Essa capacidade do Azure Functions de responder a gatilhos o torna uma saída natural para o Azure Stream Analytics. Este adaptador de saída permite aos usuários se conectar o Stream Analytics ao Azure Functions e executar um script ou trecho de código em resposta a vários eventos.

O Azure Stream Analytics chama o Azure Functions por meio de gatilhos de HTTP. O novo Adaptador de saída do Azure Functions está disponível com as seguintes propriedades configuráveis:

| Nome da Propriedade | Descrição |
| --- | --- |
| Aplicativo de Funções |Nome de seu aplicativo do Azure Functions |
| Função |Nome da função em seu aplicativo do Azure Functions |
| Tamanho Máximo do Lote |Essa propriedade pode ser usada para definir o tamanho máximo de cada lote de saída que será enviado ao seu Azure Functions. Por padrão, esse valor é 256 KB |
| Contagem Máxima do Lote  |Como o nome indica, essa propriedade permite que você especifique o número máximo de eventos em cada lote que serão enviados ao Azure Functions. O valor da contagem máxima de lote padrão é de 100 |
| Chave |Se você quiser usar um Azure Function de outra assinatura, você pode fazer isso fornecendo a chave para acessar sua função |

Observe que quando o Azure Stream Analytics recebe a exceção 413 (Entidade de Solicitação http muito grande) da função do Azure, ele reduz o tamanho dos lotes que envia para o Azure Functions. Em seu código de função do Azure, use essa exceção para certificar-se de que o Azure Stream Analytics não envie lotes muito grandes. Além disso, certifique-se de que os valores de contagem e tamanho máximo do lote usados na função sejam consistentes com os valores inseridos no portal do Stream Analytics. 

Além disso, em uma situação em que não há nenhum evento caindo em uma janela de tempo, nenhuma saída é gerada. Como resultado, a função computeResult não será chamada. Esse comportamento é consistente com as funções de agregação em janelas internas.


## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
Você foi apresentado ao Stream Analytics, um serviço gerenciado para análise de streaming em dados da Internet das coisas. Para saber mais sobre esse serviço, consulte:

* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
