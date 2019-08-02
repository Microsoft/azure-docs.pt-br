---
title: Padrões de design de tabela de armazenamento do Azure | Microsoft Docs
description: Use padrões para soluções de serviço de tabela do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: 40f760ab054154a02bea9eb341bda33bb879d824
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249572"
---
# <a name="table-design-patterns"></a>Padrões de design de tabela
Este artigo descreve alguns padrões adequados para uso com soluções de serviço Tabela. Além disso, você verá como abordar praticamente alguns dos problemas e compensações discutidos em outros artigos de design de armazenamento de Tabela. O diagrama a seguir resume as relações entre os diferentes padrões:  

![para pesquisar dados relacionados](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


O mapa padrão acima destaca algumas relações entre padrões (azul) e antipadrões (laranja) documentados neste guia. Há muitos outros padrões que vale a pena considerar. Por exemplo, um dos principais cenários para o serviço Tabela é utilizar o [Padrão de Exibição Materializada](https://msdn.microsoft.com/library/azure/dn589782.aspx) do padrão [CQRS (Segregação de Responsabilidade da Consulta de Comando)](https://msdn.microsoft.com/library/azure/jj554200.aspx).  

## <a name="intra-partition-secondary-index-pattern"></a>Padrão de índice secundário intrapartição
Armazene várias cópias de cada entidade usando valores diferentes de **RowKey** (na mesma partição) para permitir pesquisas rápidas e eficientes, bem como ordens de classificação alternativas usando valores diferentes de **RowKey**. Atualizações entre as cópias podem ser mantidas consistentes usando EGTs.  

### <a name="context-and-problem"></a>Contexto e problema
O serviço Tabela indexa automaticamente as entidades usando os valores de **PartitionKey** e **RowKey**. Isso habilita um aplicativo cliente a recuperar uma entidade com eficiência usando esses valores. Por exemplo, usando a estrutura de tabela mostrada abaixo, um aplicativo cliente pode usar uma consulta de ponto para recuperar uma entidade de funcionário individual usando o nome do departamento e a ID do funcionário (os valores de **PartitionKey** e **RowKey**). Um cliente também pode recuperar entidades classificadas por ID de funcionário dentro de cada departamento.

![Image06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Se você quiser ser capaz de encontrar uma entidade funcionário com base no valor de outra propriedade, como o endereço de email, deve usar uma verificação de partição menos eficiente para localizar uma correspondência. Isso ocorre porque o serviço Tabela não fornece índices secundários. Além disso, não há opção para solicitar uma lista de funcionários classificados em uma ordem diferente da ordem **RowKey** .  

### <a name="solution"></a>Solução
Para solucionar a falta de índices secundários, armazene várias cópias de cada entidade com cada cópia, usando um valor diferente de **RowKey** . Se você armazenar uma entidade com as estruturas mostradas abaixo, poderá recuperar com eficiência entidades de funcionário com base na ID do funcionário ou endereço de email. Os valores de prefixo para **RowKey**, "empid_" e "email_" permitem a consulta de um único funcionário ou um intervalo de funcionários usando um intervalo de endereços de email ou IDs de funcionário.  

![Entidades de funcionário](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Os seguintes dois critérios de filtro (uma pesquisa por ID funcionário e uma por endereço de email) especificam consultas de ponto:  

* $filter=(PartitionKey eq 'Sales') and (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') e (RowKey eq 'email_jonesj@contoso.com')  

Ao consultar um intervalo de entidades de funcionário, você poderá, usando uma consulta às entidades com o prefixo apropriado em **RowKey**, especificar um intervalo classificado por ordem de ID de funcionário ou por ordem de endereços de email.  

* Para localizar todos os funcionários do departamento de vendas com uma ID de funcionário no intervalo 000100 a 000199, use: $filter=(PartitionKey eq 'Sales') e (RowKey ge 'empid_000100') e (RowKey le 'empid_000199')  
* Para localizar todos os funcionários do departamento de Vendas com um endereço de email que começa com a letra 'a', use: $filter=(PartitionKey eq 'Sales') and (RowKey ge 'email_a') and (RowKey lt 'email_b')  
  
  Observe que a sintaxe de filtro usada nos exemplos acima é proveniente da API REST do serviço Tabela. Para saber mais, confira [Query Entities](https://msdn.microsoft.com/library/azure/dd179421.aspx) (Consultar Entidades).  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Armazenamento de tabela é relativamente barato, portanto a sobrecarga de custos de armazenar dados duplicados não deve ser uma preocupação importante. No entanto, você deve sempre avaliar o custo de seu design com base nas necessidades de armazenamento previstas e só adicionar entidades duplicadas para dar suporte a consultas que seu aplicativo cliente executará.  
* Como as entidades de índice secundário são armazenadas na mesma partição que as entidades originais, você deve se certificar de não exceder as metas de escalabilidade para uma partição individual.  
* Você pode manter suas entidades duplicadas consistentes entre si usando EGTs para atualizar as duas cópias da entidade atomicamente. Isso significa que você deve armazenar todas as cópias de uma entidade na mesma partição. Para saber mais, confira a seção [Usando transações de grupo de entidades](table-storage-design.md#entity-group-transactions).  
* O valor usado para **RowKey** deve ser exclusivo para cada entidade. Considere o uso de valores de chave composta.  
* O preenchimento de valores numéricos na **RowKey** (por exemplo, a ID de funcionário 000223), permite classificação e filtragem corretas, com base em limites superiores e inferiores.  
* Você não precisa necessariamente duplicar todas as propriedades da entidade. Por exemplo, se as consultas que pesquisam as entidades usando o endereço de email em **RowKey** nunca precisarem da idade do funcionário, essas entidades poderão ter a seguinte estrutura:

   ![Estrutura de entidade de funcionário](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Normalmente é melhor armazenar dados duplicados e certificar-se de que você possa recuperar todos os dados que precisa com uma única consulta, do que usar uma consulta para localizar uma entidade e outra para pesquisar os dados necessários.  

### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando o aplicativo cliente precisar recuperar entidades usando uma variedade de chaves diferentes, quando o cliente precisar recuperar entidades em diferentes ordens de classificação, e onde é possível identificar cada entidade usando uma variedade de valores exclusivos. No entanto, você deve ter certeza de que não excederá os limites de escalabilidade da partição durante a execução de pesquisas da entidade, usando valores diferentes de **RowKey** .  

### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de índice secundário entre partições](#inter-partition-secondary-index-pattern)
* [Padrão de chave composta](#compound-key-pattern)
* Transações de Grupo de Entidades
* [Trabalhando com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Padrão de índice secundário entre partições
Armazene várias cópias de cada entidade usando valores diferentes de **RowKey** em partições ou tabelas separadas para permitir pesquisas rápidas e eficientes, bem como ordens de classificação alternativas usando valores diferentes de **RowKey**.  

### <a name="context-and-problem"></a>Contexto e problema
O serviço Tabela indexa automaticamente as entidades usando os valores de **PartitionKey** e **RowKey**. Isso habilita um aplicativo cliente a recuperar uma entidade com eficiência usando esses valores. Por exemplo, usando a estrutura de tabela mostrada abaixo, um aplicativo cliente pode usar uma consulta de ponto para recuperar uma entidade de funcionário individual usando o nome do departamento e a ID do funcionário (os valores de **PartitionKey** e **RowKey**). Um cliente também pode recuperar entidades classificadas por ID de funcionário dentro de cada departamento.  

![ID do funcionário](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Se você quiser ser capaz de encontrar uma entidade funcionário com base no valor de outra propriedade, como o endereço de email, deve usar uma verificação de partição menos eficiente para localizar uma correspondência. Isso ocorre porque o serviço Tabela não fornece índices secundários. Além disso, não há opção para solicitar uma lista de funcionários classificados em uma ordem diferente da ordem **RowKey** .  

Você está prevendo um volume muito alto de transações em relação a essas entidades e deseja minimizar o risco de o serviço Tabela limitar o seu cliente.  

### <a name="solution"></a>Solução
Para solucionar a falta de índices secundários, você pode armazenar várias cópias de cada entidade com cada cópia usando valores diferentes de **PartitionKey** e **RowKey**. Se você armazenar uma entidade com as estruturas mostradas abaixo, poderá recuperar com eficiência entidades de funcionário com base na ID do funcionário ou endereço de email. Os valores de prefixo para **PartitionKey**, "empid_" e "email_" permitem que você identifique o índice que deseja usar para uma consulta.  

![Índice primário e índice secundário](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Os seguintes dois critérios de filtro (uma pesquisa por ID funcionário e uma por endereço de email) especificam consultas de ponto:  

* $filter=(PartitionKey eq 'empid_Sales') and (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') e (RowKey eq 'jonesj@contoso.com')  

Ao consultar um intervalo de entidades de funcionário, você poderá, usando uma consulta às entidades com o prefixo apropriado em **RowKey**, especificar um intervalo classificado por ordem de ID de funcionário ou por ordem de endereços de email.  

* Para localizar todos os funcionários do departamento de vendas com uma ID de funcionário no intervalo de **000100** a **000199**, classificados por ordem de ID de funcionário, use: $filter=(PartitionKey eq 'empid_Sales') e (RowKey ge '000100') e (RowKey le '000199')  
* Para localizar todos os funcionários do departamento de vendas com um endereço de email que comece com 'a', classificados por ordem de endereço de email, use: $filter=(PartitionKey eq 'email_Sales') and (RowKey ge 'a') and (RowKey lt 'b')  

Observe que a sintaxe de filtro usada nos exemplos acima é proveniente da API REST do serviço Tabela. Para saber mais, confira [Query Entities](https://msdn.microsoft.com/library/azure/dd179421.aspx) (Consultar Entidades).  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Você pode manter suas entidades duplicadas eventualmente consistentes entre si usando o [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) para manter as entidades de índice primário e secundário.  
* Armazenamento de tabela é relativamente barato, portanto a sobrecarga de custos de armazenar dados duplicados não deve ser uma preocupação importante. No entanto, você deve sempre avaliar o custo de seu design com base nas necessidades de armazenamento previstas e só adicionar entidades duplicadas para dar suporte a consultas que seu aplicativo cliente executará.  
* O valor usado para **RowKey** deve ser exclusivo para cada entidade. Considere o uso de valores de chave composta.  
* O preenchimento de valores numéricos na **RowKey** (por exemplo, a ID de funcionário 000223), permite classificação e filtragem corretas, com base em limites superiores e inferiores.  
* Você não precisa necessariamente duplicar todas as propriedades da entidade. Por exemplo, se as consultas que pesquisam as entidades usando o endereço de email em **RowKey** nunca precisarem da idade do funcionário, essas entidades poderão ter a seguinte estrutura:
  
   ![Entidade de funcionário (índice secundário)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* É geralmente melhor armazenar dados duplicados e garantir que você possa recuperar todos os dados que precisa com uma única consulta do que usar uma consulta para localizar uma entidade usando o índice secundário e outra para pesquisar os dados necessários no índice primário.  

### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando o aplicativo cliente precisar recuperar entidades usando uma variedade de chaves diferentes, quando o cliente precisar recuperar entidades em diferentes ordens de classificação, e onde é possível identificar cada entidade usando uma variedade de valores exclusivos. Use esse padrão quando quiser evitar exceder os limites de escalabilidade da partição durante a execução de pesquisas de entidade usando os valores diferentes de **RowKey** .  

### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  
* [Padrão de índice secundário intrapartição](#intra-partition-secondary-index-pattern)  
* [Padrão de chave composta](#compound-key-pattern)  
* Transações de Grupo de Entidades  
* [Trabalhando com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Padrão de transações eventualmente consistentes
Habilite comportamento eventualmente consistente entre limites de partição ou limites do sistema de armazenamento usando filas do Azure.  

### <a name="context-and-problem"></a>Contexto e problema
EGTs habilitam transações atômicas entre várias entidades que compartilham a mesma chave de partição. Por motivos de escalabilidade e desempenho, você pode optar por armazenar entidades que têm requisitos de consistência em partições separadas ou em um sistema de armazenamento separado: nesse cenário, você não pode usar EGTs para manter a consistência. Por exemplo, você pode ter um requisito para manter a consistência eventual entre:  

* Entidades armazenadas em duas partições diferentes na mesma tabela, em tabelas diferentes ou em diferentes contas de armazenamento.  
* Uma entidade armazenada no serviço Tabela e um blob armazenado no serviço Blob.  
* Uma entidade armazenada no serviço Tabela e um arquivo em um sistema de arquivos.  
* Um repositório de entidades no serviço Tabela ainda indexado usando o serviço de Azure Search.  

### <a name="solution"></a>Solução
Ao usar as filas do Azure, você pode implementar uma solução que fornece consistência eventual em duas ou mais partições ou sistemas de armazenamento.
Para ilustrar essa abordagem, suponha que você tenha de ser capaz de arquivar entidades antigas de funcionário. Entidades antigas de funcionário raramente são consultadas e devem ser excluídas de todas as atividades que lidam com funcionários atuais. Para implementar esse requisito, armazene funcionários ativos na tabela **Atual** e funcionários antigos na tabela **Arquivo morto**. O arquivamento de um funcionário exige a exclusão da entidade da tabela **Atual** e a adição da entidade à tabela **Arquivo morto**, mas não é possível usar uma EGT para executar essas duas operações. Para evitar o risco de que uma falha faça com que uma entidade seja exibida nas tabelas ou em nenhuma, a operação de arquivamento deve ser eventualmente consistente. O diagrama de sequência a seguir descreve as etapas nesta operação. Mais detalhes são fornecidos para caminhos de exceção no seguinte texto.  

![Solução de filas do Azure](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Um cliente inicia a operação de arquivamento colocando uma mensagem em uma fila do Azure, neste exemplo para arquivar funcionário nº456. Uma função de trabalho controla a fila para novas mensagens; quando encontra uma, lê a mensagem e deixa uma cópia oculta na fila. A função de trabalho, em seguida, busca uma cópia da entidade na tabela **Atual**, insere uma cópia na tabela **Arquivo morto** e exclui o original da tabela **Atual**. Finalmente, se não houve erros das etapas anteriores, a função de trabalho exclui a mensagem oculta da fila.  

Neste exemplo, a etapa 4 insere o funcionário na tabela **Arquivo morto** . Ele pode adicionar o funcionário em um blob no serviço Blob ou um arquivo em um sistema de arquivos.  

### <a name="recovering-from-failures"></a>Recuperando de falhas
Caso a função de trabalho precise reiniciar a operação de arquivamento, é importante que as operações nas etapas **4** e **5** sejam *idempotentes*. Se estiver usando o serviço Tabela, na etapa **4**, você deverá usar uma operação "inserir ou substituir"; na etapa **5**, você deverá usar uma operação "excluir se existir" na biblioteca de cliente que está usando. Se você estiver usando outro sistema de armazenamento, deve usar uma operação idempotente apropriada.  

Se a função de trabalho nunca concluir a etapa **6**, após um tempo limite a mensagem reaparecerá na fila, pronta para ser reprocessada pela função de trabalho. A função de trabalho pode verificar quantas vezes uma mensagem na fila foi lida e, se necessário, sinalizá-la como uma mensagem "suspeita" para investigação, enviando-a para uma fila separada. Para saber mais sobre a leitura de mensagens da fila e verificar a contagem de remoção da fila, consulte [Obter mensagens](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Alguns erros dos serviços Tabela e Fila são erros transitórios e o aplicativo cliente deve incluir uma lógica de repetição adequada para lidar com eles.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Esta solução não fornece isolamento da transação. Por exemplo, um cliente pode ler as tabelas **Atual** e **Arquivo morto** quando a função de trabalho estiver entre as etapas **4** e **5** e ver uma exibição inconsistente dos dados. Observe que os dados serão consistentes eventualmente.  
* Você deve se certificar de que as etapas 4 e 5 sejam idempotentes para garantir a consistência eventual.  
* Você pode dimensionar a solução usando várias filas e instâncias de função de trabalho.  

### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando quiser garantir a consistência eventual entre entidades que existem nas tabelas ou partições diferentes. Você pode estender esse padrão para garantir a consistência eventual de operações entre o serviço Tabela e o serviço Blob e outras fontes de dados de armazenamento não Azure, como banco de dados ou sistema de arquivos.  

### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* Transações de Grupo de Entidades  
* [Mesclar ou substituir](#merge-or-replace)  

> [!NOTE]
> Se o isolamento da transação for importante para sua solução, você deve considerar a recriação das tabelas para poder usar as EGTs.  
> 
> 

## <a name="index-entities-pattern"></a>Padrão de entidades de índice
Mantenha entidades de índice para habilitar pesquisas eficientes que retornam listas de entidades.  

### <a name="context-and-problem"></a>Contexto e problema
O serviço Tabela indexa automaticamente as entidades usando os valores de **PartitionKey** e **RowKey**. Isso habilita um aplicativo cliente a recuperar uma entidade com eficiência usando uma consulta de ponto. Por exemplo, usando a estrutura de tabela mostrada abaixo, um aplicativo cliente pode recuperar de maneira eficiente uma entidade de funcionário individual pelo uso do nome do departamento e da ID do funcionário (**PartitionKey** e **RowKey**).  

![Entidade de funcionário](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Se você quiser ser capaz de recuperar uma lista de entidades de funcionário com base no valor de outra propriedade não exclusiva, como seu sobrenome, deve usar uma verificação de partição menos eficiente para localizar correspondências em vez de usar um índice para examiná-las diretamente. Isso ocorre porque o serviço Tabela não fornece índices secundários.  

### <a name="solution"></a>Solução
Para habilitar a pesquisa por sobrenome com a estrutura de entidade mostrada acima, você deve manter listas de ids de funcionário. Para recuperar entidades de funcionário com determinado sobrenome, como Dias, primeiro localize a lista de ids de funcionário para os funcionários com Dias como sobrenome e recupere essas entidades. Há três opções principais para armazenar listas de ids de funcionário:  

* Use o armazenamento de blobs.  
* Crie entidades de índice na mesma partição que as entidades do funcionário.  
* Crie entidades de índice em uma partição ou tabela separada.  

<u>Opção nº 1: Usar o Armazenamento de Blobs</u>  

Para a primeira opção, crie um blob para todos os sobrenomes exclusivos e em cada repositório de blobs uma lista de valores **PartitionKey** (departamento) e **RowKey** (ID do funcionário) para os funcionários com esse sobrenome. Quando você adiciona ou exclui um funcionário, deve garantir que o conteúdo do blob relevante seja eventualmente consistente com as entidades do funcionário.  

<u>Opção nº 2:</u> Criar entidades de índice na mesma partição  

Para a segunda opção, use as entidades de índice que armazenam os dados a seguir:  

![Entidade de índice do funcionário](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

A propriedade **EmployeeIDs** contém uma lista de IDs de funcionário para os funcionários com o sobrenome armazenado em **RowKey**.  

As etapas a seguir descrevem o processo que você deve seguir ao adicionar um novo funcionário, se você estiver usando a segunda opção. Neste exemplo, estamos adicionando um funcionário com ID 000152 e um sobrenome Jones no departamento de Vendas:  

1. Recupere a entidade de índice com um valor de **PartitionKey** igual a "Vendas" e valor de **RowKey** igual a "Dias". Salve o ETag dessa entidade para usar na etapa 2.  
2. Crie uma transação de grupo de entidades (ou seja, uma operação em lote) que insira a nova entidade de funcionário (valor de **PartitionKey** igual a "Vendas" e valor de **RowKey** igual a "000152") e atualize a entidade de índice (valor de **PartitionKey** igual a "Vendas" e valor de **RowKey** igual a "Dias"), adicionando a ID do novo funcionário à lista no campo EmployeeIDs. Para saber mais sobre transações de grupo de entidades, confira a seção Transações de grupo de entidades.  
3. Se a transação de grupo de entidades falhar devido a um erro de simultaneidade otimista (alguém modificou a entidade de índice), será necessário recomeçar na etapa 1.  

Você pode usar uma abordagem semelhante à exclusão de um funcionário se usar a segunda opção. Alterar o sobrenome do funcionário é um pouco mais complexo, pois você precisará executar uma transação de grupo de entidades que atualiza as três entidades: a entidade funcionário, a entidade de índice para o sobrenome antigo e a entidade de índice para o novo sobrenome. Você deve recuperar cada entidade antes de fazer alterações para recuperar os valores de ETag que depois pode usar para executar as atualizações usando a simultaneidade otimista.  

As etapas abaixo descrevem o processo que você deve seguir quando precisar procurar todos os funcionários com determinado sobrenome em um departamento, se estiver usando a segunda opção. Neste exemplo, estamos procurando todos os funcionários com o sobrenome Dias no departamento de Vendas:  

1. Recupere a entidade de índice com um valor de **PartitionKey** igual a "Vendas" e valor de **RowKey** igual a "Dias".  
2. Analise a lista de Ids no campo EmployeeIDs.  
3. Se precisar de informações adicionais sobre cada um desses funcionários (como endereços de email), recupere cada uma das entidades de funcionário usando o valor de **PartitionKey** igual a "Vendas" e os valores de **RowKey** da lista de funcionários obtida na etapa 2.  

<u>Opção nº 3:</u> Criar entidades de índice em uma partição ou tabela separada  

Para a terceira opção, use as entidades de índice que armazenam os dados a seguir:  

![Entidade de índice de funcionário em uma partição separada](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


A propriedade **EmployeeIDs** contém uma lista de IDs de funcionário para os funcionários com o sobrenome armazenado em **RowKey**.  

Com a terceira opção, você não pode usar EGTs para manter a consistência porque as entidades de índice estão em uma partição separada das entidades de funcionário. Certifique-se de que as entidades de índice sejam eventualmente consistentes com as entidades de funcionário.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Essa solução exige, pelo menos, duas consultas para recuperar entidades correspondentes: uma para consultar as entidades de índice para obter a lista de valores de **RowKey** e consultas para recuperar cada entidade na lista.  
* Considerando que uma entidade individual tem um tamanho máximo de 1 MB, as opções nº2 e 3 na solução supõem que a lista de ids de funcionário para determinado sobrenome nunca é maior que 1 MB. Se a lista de ids de funcionário é provavelmente maior que 1 MB em tamanho, use a opção nº1 e armazene os dados de índice no armazenamento de blob.  
* Se você usar a opção n. 2 (usando EGTs para controlar a adição e exclusão de funcionários e alterar o sobrenome do funcionário), você deverá avaliar se o volume de transações abordará os limites de escalabilidade em uma determinada partição. Se esse for o caso, você deve considerar uma solução eventualmente consistente (opção nº1 ou 3) que usa filas para manipular solicitações de atualização e permite que você armazene suas entidades de índice em uma partição separada das entidades de funcionário.  
* A opção nº2 nesta solução pressupõe que você deseja pesquisar por sobrenome dentro de um departamento: por exemplo, você quer recuperar uma lista de funcionários com um sobrenome Jones no departamento de Vendas. Para pesquisar todos os funcionários com um sobrenome Jones em toda a organização, use a opção nº1 ou 3.
* É possível implementar uma solução baseada em fila que fornece consistência eventual (veja [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) para obter mais detalhes).  

### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando quiser pesquisar um conjunto de entidades que compartilham um valor da propriedade comum, como todos os funcionários com o sobrenome Dias.  

### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  
* Transações de Grupo de Entidades  
* [Trabalhando com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Padrão de desnormalização
Combine dados relacionados juntos em uma única entidade para que você possa recuperar todos os dados que precisa com uma ponto único de consulta.  

### <a name="context-and-problem"></a>Contexto e problema
Em um banco de dados relacional, você geralmente normaliza dados para remover a duplicação, resultando em consultas que recuperam dados de várias tabelas. Se você normalizar dados em tabelas do Azure, terá de ir e voltar várias vezes do cliente ao servidor para recuperar os dados relacionados. Por exemplo, com a estrutura de tabela mostrada abaixo, você precisa de dois viagens de ida e volta para recuperar os detalhes de um departamento: uma para buscar a entidade do departamento que inclui a ID do gerente e outra solicitação para buscar detalhes do gerente em uma entidade de funcionário.  

![Entidade de departamento e funcionário](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Solução
Em vez de armazenar os dados em duas entidades separadas, desnormalize os dados e mantenha uma cópia dos detalhes do gerente na entidade de departamento. Por exemplo:  

![Entidade de departamento](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Com entidades de departamento armazenadas com essas propriedades, agora você pode recuperar todos os detalhes de que precisa sobre um departamento usando uma consulta de ponto.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Existe alguma sobrecarga de custo associada ao armazenar alguns dados duas vezes. O benefício de desempenho (resultante de menos solicitações ao serviço de armazenamento) normalmente supera o aumento marginal nos custos de armazenamento (e esse custo é parcialmente compensado por uma redução no número de transações que você precisa para obter detalhes de um departamento).  
* Você deve manter a consistência das duas entidades que armazenam informações sobre os gerentes. Você pode tratar o problema de consistência usando EGTs para atualizar várias entidades em uma única transação atômica: nesse caso, a entidade de departamento e a entidade de funcionário para o gerente do departamento são armazenadas na mesma partição.  

### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar pesquisar informações relacionadas com frequência. Esse padrão reduz o número de consultas que o cliente deve fazer para recuperar os dados necessários.  

### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* Transações de Grupo de Entidades  
* [Trabalhando com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Padrão de chave composta
Use valores **RowKey** compostos para permitir que um cliente pesquise dados relacionados a uma consulta de ponto único.  

### <a name="context-and-problem"></a>Contexto e problema
Em um banco de dados relacional, é bastante natural usar junções em consultas para retornar partes relacionadas de dados para o cliente em uma única consulta. Por exemplo, você pode usar a ID do funcionário para pesquisar uma lista de entidades relacionadas que contém o desempenho e analisar os dados desse funcionário.  

Suponhamos que você esteja armazenando entidades de funcionário no serviço Tabela usando a seguinte estrutura:  

![Estrutura de entidade de funcionário](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Você também precisa armazenar dados históricos relacionados a revisões e desempenho de cada ano que o funcionário trabalhou para a sua organização e precisa ser capaz de acessar essas informações por ano. Uma opção é criar outra tabela que armazena entidades com a seguinte estrutura:  

![Estrutura de entidade de funcionário alternativa](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Observe que com essa abordagem você poderá duplicar algumas informações (como nome e sobrenome) na nova entidade para recuperar seus dados com uma única solicitação. No entanto, não é possível manter uma coerência forte porque você não pode usar uma EGT para atualizar as duas entidades atomicamente.  

### <a name="solution"></a>Solução
Armazene um novo tipo de entidade em sua tabela original usando entidades com a seguinte estrutura:  

![Solução para estrutura de entidade de funcionário](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Observe como a **RowKey** agora é uma chave composta de ID de funcionário e o ano dos dados de revisão que permitem recuperar o desempenho do funcionário e analisar os dados com uma única solicitação para uma única entidade.  

O exemplo a seguir descreve como você pode recuperar todos os dados de revisão de um funcionário específico (por exemplo, um funcionário 000123 no departamento de Vendas):  

$filter=(PartitionKey eq 'Sales') and (RowKey ge 'empid_000123') and (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Você deve usar um caractere separador adequado que facilite a análise do valor de **RowKey**: por exemplo, **000123_2012**.  
* Você também está armazenando essa entidade na mesma partição que outras entidades que contêm dados relacionados para o mesmo funcionário, o que significa que você pode usar EGTs para manter a coerência forte.
* Você deve considerar com que frequência consultará os dados para determinar se esse padrão é apropriado.  Por exemplo, se você for acessar os dados de revisão com pouca frequência e os dados principais do funcionário com mais frequência, deve mantê-los como entidades separadas.  

### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar armazenar uma ou mais entidades relacionadas que você consulta com frequência.  

### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* Transações de Grupo de Entidades  
* [Trabalhando com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Padrão de rastro do log
Recupere as *n* entidades adicionadas recentemente em uma partição usando um valor de **RowKey** que classifica em ordem de data e hora inversa.  

### <a name="context-and-problem"></a>Contexto e problema
Um requisito comum é ser capaz de recuperar as entidades criadas mais recentemente, por exemplo, os dez reembolsos de despesa mais recentes enviados por um funcionário. As consultas de tabela dão suporte a uma operação de consulta **$top** para retornar as primeiras *n* entidades de consulta de um conjunto: não há uma operação de consulta equivalente para retornar as últimas n entidades de um conjunto.  

### <a name="solution"></a>Solução
Armazene as entidades usando uma **RowKey** que classifica naturalmente em ordem inversa de data/hora. Fazendo isso, a entrada mais recente será sempre a primeira na tabela.  

Por exemplo, para recuperar os dez reembolsos de despesa mais recentes enviadas por um funcionário, você pode usar um valor de escala inversa derivado de data/hora atual. O seguinte exemplo de código C# mostra uma maneira de criar um valor adequado de "escala invertida" para uma **RowKey** que classifica do mais recente ao mais antigo:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Você pode retornar ao valor de data/hora usando o seguinte código:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A consulta a tabela tem esta aparência:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Você precisa preencher o valor da escala invertida com zeros para garantir que o valor de cadeia de caracteres seja classificado conforme o esperado.  
* Você deve estar ciente das metas de escalabilidade no nível de uma partição. Cuidado não criar partições com ponto de acesso.  

### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar acessar entidades na ordem inversa de data/hora ou quando precisar acessar entidades adicionadas mais recentemente.  

### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Antipadrão prefixar/acrescentar](#prepend-append-anti-pattern)  
* [Recuperando entidades](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Padrão de exclusão de alto volume
Habilite a exclusão de um alto volume de entidades armazenando todas as entidades para exclusão simultânea em suas próprias tabelas separadas; você exclui as entidades excluindo a tabela.  

### <a name="context-and-problem"></a>Contexto e problema
Muitos aplicativos excluem dados antigos que não precisam mais estar disponíveis para um aplicativo cliente, ou que o aplicativo tenha arquivado em outro meio de armazenamento. Você geralmente identifica esses dados por uma data: por exemplo, você tem um requisição para excluir registros de todas as solicitações de logon com mais de 60 dias.  

Um design possível é usar a data e a hora da solicitação de logon na **RowKey**:  

![Data e hora da tentativa de logon](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Essa abordagem evita sobrecargas de partição porque o aplicativo pode inserir e excluir entidades de logon para cada usuário em uma partição separada. No entanto, essa abordagem pode ser cara e demorada se você tiver um grande número de entidades porque primeiro é necessário executar uma verificação de tabela para identificar todas as entidades a excluir e, em seguida, excluir todas as entidades antigas. Observe que você pode reduzir o número de viagens de ida e volta ao servidor, necessário para excluir as entidades antigas por envio em lote de várias solicitações de exclusão para as EGTs.  

### <a name="solution"></a>Solução
Use uma tabela separada para cada dia de tentativas de logon. Você pode usar o design de entidade acima para evitar pontos de acesso ao inserir entidades e excluir entidades antigas agora é simplesmente uma questão de excluir uma tabela a cada dia (uma única operação de armazenamento), em vez de localizar e excluir centenas de milhares de entidades de logon individuais todos os dias.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Seu design dá suporte a outras formas de uso dos dados pelo aplicativo, como pesquisa em entidades específicas, vinculação com outros dados ou geração de informações agregadas?  
* O design evita pontos de acesso quando você está inserindo novas entidades?  
* Espere um atraso, se você quiser reutilizar o mesmo nome de tabela após a exclusão. É melhor sempre usar nomes de tabela exclusivos.  
* Espere alguns limitação quando você usar pela primeira vez uma nova tabela enquanto o serviço Tabela aprende os padrões de acesso e distribui as partições entre os nós. Você deve considerar com que frequência precisa criar novas tabelas.  

### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando tiver um alto volume de entidades que devem ser excluídas ao mesmo tempo.  

### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* Transações de Grupo de Entidades
* [Modificando entidades](#modifying-entities)  

## <a name="data-series-pattern"></a>Padrão de série de dados
Armazene série de dados completa em uma única entidade para minimizar o número de solicitações que você faz.  

### <a name="context-and-problem"></a>Contexto e problema
Um cenário comum é um aplicativo armazenar uma série de dados que ele normalmente precisa recuperar uma só vez. Por exemplo, seu aplicativo pode registrar a quantidade de mensagens instantâneas que cada funcionário envia por hora e usar essas informações para avaliar quantas mensagens cada usuário enviou nas 24 horas anteriores. Um design pode ser armazenar 24 entidades para cada funcionário:  

![Armazenar 24 entidades para cada funcionário](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Com esse design, você pode facilmente localizar e atualizar a entidade a ser atualizada para cada funcionário sempre que o aplicativo precisar atualizar o valor de contagem das mensagem. No entanto, para recuperar as informações e traçar um gráfico de atividades das 24 horas anteriores, você deve recuperar 24 entidades.  

### <a name="solution"></a>Solução
Use o design a seguir com uma propriedade separada para armazenar a contagem de mensagens para cada hora:  

![Entidade de estatísticas de mensagem](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Com esse design, você pode usar uma operação de mesclagem para atualizar a contagem de mensagens de um funcionário, em uma hora específica. Agora, você pode recuperar todas as informações necessárias traçar o gráfico usando uma solicitação para uma única entidade.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Se a sua série de dados completa não couber em uma única entidade (uma entidade pode ter até 252 propriedades), use um repositório de dados alternativo, como um blob.  
* Caso você tenha vários clientes atualizando uma entidade simultaneamente, será necessário usar o **Etag** para implementar a simultaneidade otimista. Se você tiver muitos clientes, poderá enfrentar alta contenção.  

### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar atualizar e recuperar uma série de dados associados a uma entidade individual.  

### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de grandes entidades](#large-entities-pattern)  
* [Mesclar ou substituir](#merge-or-replace)  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) (se você estiver armazenando a série de dados em um blob)  

## <a name="wide-entities-pattern"></a>Padrão de entidades longas
Use várias entidades físicas para armazenar entidades lógicas com mais de 252 propriedades.  

### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual pode ter no máximo 252 propriedades (exceto as propriedades de sistema obrigatórias) e não pode armazenar mais de 1 MB de dados no total. Em um banco de dados relacional, você normalmente contornaria os limites de tamanho de uma linha adicionando uma nova tabela e impondo uma relação de 1 para 1 entre elas.  

### <a name="solution"></a>Solução
Usando o serviço Tabela, você pode armazenar várias entidades para representar um único objeto grande de negócios com mais de 252 propriedades. Por exemplo, para armazenar uma contagem do número de mensagens instantâneas enviadas por cada funcionário nos últimos 365 dias, você poderia usar o design a seguir que usa duas entidades com diferentes esquemas:  

![Múltiplas entidades](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Se você precisar fazer uma alteração que requer a atualização de ambas as entidades para mantê-las sincronizadas entre si, pode usar uma EGT. Caso contrário, você pode usar uma operação de mesclagem única para atualizar a contagem de mensagens de um dia específico. Para recuperar todos os dados de um funcionário individual, você deverá recuperar as duas entidades, o que pode ser feito com duas solicitações eficientes que usam um valor de **PartitionKey** e um de **RowKey**.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* A recuperação de uma entidade lógica completa envolve pelo menos duas transações de armazenamento: uma para recuperar cada entidade física.  

### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar armazenar entidades cujo tamanho ou número de propriedades excede os limites de uma entidade individual no serviço Tabela.  

### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* Transações de Grupo de Entidades
* [Mesclar ou substituir](#merge-or-replace)

## <a name="large-entities-pattern"></a>Padrão de entidades grandes
Use armazenamento de blobs para armazenar grandes valores de propriedade.  

### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não pode armazenar mais de 1 MB de dados no total. Se uma ou várias de suas propriedades armazenam valores que fazem com que o tamanho total da entidade exceda esse valor, você não pode armazenar a entidade inteira no serviço Tabela.  

### <a name="solution"></a>Solução
Se a entidade exceder 1 MB de tamanho porque uma ou mais propriedades contêm uma grande quantidade de dados, você poderá armazenar dados no serviço Blob e, em seguida, armazenar o endereço do blob em uma propriedade na entidade. Por exemplo, você pode armazenar a foto de um funcionário no armazenamento de blobs e armazenar um link para a foto na propriedade **Foto** da entidade funcionário:  

![Propriedade de foto](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Para manter a consistência eventual entre a entidade do serviço Tabela e os dados no serviço Blob, use o [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) para manter suas entidades.
* A recuperação de uma entidade completa envolve pelo menos duas transações de armazenamento: uma para recuperar a entidade e outra para recuperar os dados do blob.  

### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar armazenar entidades cujo tamanho excede os limites de uma entidade individual no serviço Tabela.  

### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  
* [Padrão de entidades longas](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Antipadrão prefixar/acrescentar
Aumente a escalabilidade quando tiver um alto volume de inserções, distribuindo as inserções em várias partições.  

### <a name="context-and-problem"></a>Contexto e problema
A prefixação ou o acréscimo de entidades às suas entidades armazenadas normalmente fazem com que o aplicativo adicione novas entidades à primeira ou última partição de uma sequência de partições. Nesse caso, todas as inserções em um determinado momento estão ocorrendo na mesma partição, criando um ponto de acesso que impede o serviço Tabela de carregar inserções de balanceamento em vários nós e possivelmente fazendo com que o aplicativo atinja as metas de escalabilidade para partição. Por exemplo, se você tiver um aplicativo que registre acesso a rede e recursos por funcionários, então uma estrutura de entidade, conforme mostrado a seguir, poderá fazer com que a partição de hora atual se transforme em um ponto de acesso, caso o volume de transações atinja a meta de escalabilidade de uma partição individual:  

![Estrutura de entidade](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Solução
A seguinte estrutura de entidade alternativa evita um ponto de acesso em qualquer partição específica quando o aplicativo registra eventos:  

![Estrutura de entidade alternativa](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Observe com este exemplo como **PartitionKey** e **RowKey** são chaves compostas. A **PartitionKey** usa a ID de funcionário e departamento para distribuir o registro em log entre várias partições.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* A estrutura de chave alternativa que evita a criação de partições ativas em inserções dá suporte eficiente a consultas que o aplicativo cliente faz?  
* O volume antecipado de transações significa que você tem probabilidade de atingir as metas de escalabilidade para uma partição individual e ser limitado pelo serviço de armazenamento?  

### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Evite o antipadrão prefixar/acrescentar quando o volume de transações tenha a probabilidade de resultar em limitação pelo serviço de armazenamento quando você acessar uma partição ativa.  

### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de final do log](#log-tail-pattern)  
* [Modificando entidades](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Antipadrão de dados de log
Normalmente, você deveria usar o serviço Blob em vez do serviço Tabela para armazenar dados de log.  

### <a name="context-and-problem"></a>Contexto e problema
Um caso de uso comum de dados de log é recuperar uma seleção de entradas de log para um intervalo de data/hora específico: por exemplo, você deseja localizar todos os erros e mensagens críticas que seu aplicativo registrou entre 15:04 e 15:06 em uma data específica. Você não deseja usar a data e hora da mensagem de log para determinar a partição em que você salvou as entidades de log: isso resulta em uma partição ativa porque, a qualquer momento, todas as entidades de log compartilharão o mesmo valor **PartitionKey** (confira a seção [Antipadrão de prefixação/acréscimo](#prepend-append-anti-pattern)). Por exemplo, o seguinte esquema de entidade para uma mensagem de log resulta em uma partição ativa porque o aplicativo grava todas as mensagens de log na partição para a data e hora atual:  

![Entidade de mensagem de log](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

Neste exemplo, a **RowKey** inclui a data e hora da mensagem de log para garantir que as mensagens de log sejam armazenadas classificadas em ordem de data/hora; inclui também uma ID de mensagem, caso várias mensagens de log compartilhem a mesma data e hora.  

Outra abordagem é usar uma **PartitionKey** que garanta que o aplicativo grave mensagens em uma variedade de partições. Por exemplo, se a origem da mensagem de log fornecer uma maneira de distribuir mensagens através de várias partições, você pode usar o seguinte esquema de entidade:  

![Entidade de mensagem de log alternativo](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

No entanto, o problema com esse esquema é que para recuperar todas as mensagens de log para um período de tempo específico você deve pesquisar cada partição na tabela.

### <a name="solution"></a>Solução
A seção anterior realçou o problema de uma tentativa de usar o serviço Tabela para armazenar entradas de log e sugeriu dois designs insatisfatórios. Uma solução resultou em uma partição ativa com o risco de mau desempenho ao gravar mensagens de log; a outra solução resultou em mau desempenho de consulta por causa do requisito de verificar cada partição da tabela para recuperar mensagens de log de um período específico. O Armazenamento de blob oferece uma solução melhor para esse tipo de cenário e é assim que o Azure Storage Analytics armazena os dados de log que coleta.  

Esta seção descreve como o Storage Analytics armazena dados de log no armazenamento de blobs como uma ilustração dessa abordagem de armazenamento de dados que você normalmente consulta por intervalo.  

O Storage Analytics armazena mensagens de log em um formato delimitado em vários blobs. O formato delimitado facilita para um aplicativo cliente analisar os dados na mensagem de log.  

O Storage Analytics usa uma convenção de nomenclatura para blobs que permite que você localize o blob (ou blobs) que contêm as mensagens de log que você está pesquisando. Por exemplo, um blob denominado "queue/2014/07/31/1800/000001.log" contém mensagens de log que se relacionam com o serviço Fila para a hora de início às 18:00 em 31 de julho de 2014. "000001" indica que este é o primeiro arquivo de log para esse período. O Storage Analytics também registra carimbos de data/hora das últimas e primeiras mensagens de log armazenadas no arquivo como parte dos metadados do blob. A API do armazenamento ativo permite localizar blobs em um contêiner com base em um prefixo de nome: para localizar todos os blobs que contêm dados de log na fila para o horário com início às 18:00, você pode usar o prefixo "queue/2014/07/31/1800."  

O Storage Analytics armazena em buffer as mensagens de log internamente, e periodicamente atualiza o blob apropriado ou cria um novo com o último lote de entradas de log. Isso reduz o número de gravações que ele deve realizar para o serviço Blob.  

Se você estiver implementando uma solução semelhante em seu próprio aplicativo, deve considerar como gerenciar a compensação entre a confiabilidade (gravar todas as entradas de log no armazenamento de blobs à medida que ocorre) e o custo e a escalabilidade (armazenar as atualizações em buffer em seu aplicativo e gravá-las no armazenamento de blobs em lotes).  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como armazenar dados de log:  

* Se você criar um design de tabela que evita potenciais partições ativas, verá que não pode acessar os dados do log com eficiência.  
* Para processar dados de log, um cliente normalmente precisa carregar vários registros.  
* Embora os dados de log sejam geralmente estruturados, o armazenamento de blobs pode ser uma solução melhor.  

## <a name="implementation-considerations"></a>Considerações sobre a implementação
Esta seção discute algumas das considerações a serem lembradas ao implementar os padrões descritos nas seções anteriores. Grande parte dessa seção usa exemplos escritos em c# que usam a Biblioteca de Cliente de Armazenamento (versão 4.3.0 no momento da redação).  

## <a name="retrieving-entities"></a>Recuperando entidades
Conforme discutido na seção Design para consulta, a consulta mais eficiente é uma consulta de ponto. Entretanto, em alguns cenários talvez seja necessário recuperar várias entidades. Esta seção descreve algumas abordagens comuns para recuperar entidades usando a Biblioteca de Cliente de Armazenamento.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Executando uma consulta de ponto usando a Biblioteca de Cliente de Armazenamento
A maneira mais fácil de executar uma consulta de ponto é usar a operação da tabela **Recuperar**, conforme mostrado no snippet de código de C# a seguir, que recupera uma entidade com uma **PartitionKey** de valor "Vendas" e uma **RowKey** de valor "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Observe como esse exemplo espera que a entidade recuperada seja do tipo **EmployeeEntity**.  

### <a name="retrieving-multiple-entities-using-linq"></a>Recuperando várias entidades usando LINQ
Você pode usar o LINQ para recuperar várias entidades do serviço tabela ao trabalhar com Microsoft Azure biblioteca padrão da tabela Cosmos. 

```cli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Para que os exemplos a seguir funcionem, você precisará incluir namespaces:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

EmployeeTable é um objeto cloudtable que implementa um método CreateQuery\<ITableEntity > (), que retorna um > ITableEntity\<TableQuery. Objetos desse tipo implementam um IQueryable e permitem o uso de expressões de consulta LINQ e a sintaxe de notação de ponto.

Recuperar várias entidades e ser obtido especificando uma consulta com uma cláusula **Where** . Para evitar uma verificação de tabela, você sempre deve incluir o valor de **PartitionKey** na cláusula where e, se possível, o valor de **RowKey** para evitar verificações de tabela e de partição. O serviço Tabela dá suporte a um conjunto limitado de operadores de comparação (maior que, maior que ou igual a, menor que, menor que ou igual a, igual a, e diferente de) para usar na cláusula where. 

O seguinte snippet de código em C# localiza todos os funcionários cujo sobrenome começa com "B" (supondo que **RowKey** armazena o sobrenome) no departamento de vendas (supondo que **PartitionKey** armazena o nome do departamento):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
            
var employees = query.Execute();  
```

Observe como a consulta especifica uma **RowKey** e também uma **PartitionKey** para garantir um melhor desempenho.  

O exemplo de código a seguir mostra a funcionalidade equivalente sem usar a sintaxe LINQ:  

```csharp
TableQuery<EmployeeEntity> employeeQuery = 
    new TableQuery<EmployeeEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B")),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")));
            
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> O exemplo aninha vários métodos **CombineFilters** para incluir as três condições de filtro.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Recuperando grande número de entidades de uma consulta
Uma consulta ideal retorna uma entidade individual com base em um valor de **PartitionKey** e um valor de **RowKey**. No entanto, em alguns cenários, você pode precisar retornar muitas entidades da mesma partição ou até mesmo de várias partições.  

Você deve sempre testar totalmente o desempenho do seu aplicativo nesses cenários.  

Uma consulta no serviço Tabela pode retornar um máximo de 1.000 entidades de uma só vez e ser executada por um máximo de cinco segundos. Se o conjunto de resultados contiver mais de 1.000 entidades, se a consulta não for concluída em até cinco segundos, ou se a consulta ultrapassar o limite da partição, o serviço Tabela retornará um token de continuação para habilitar o aplicativo cliente a solicitar o próximo conjunto de entidades. Para saber mais sobre como funcionam os tokens de continuação, confira [Tempo limite e paginação de consulta](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Se você estiver usando a Biblioteca de Cliente de Armazenamento, ela pode automaticamente controlar os tokens de continuação para você, à medida que retorna entidades do serviço Tabela. O seguinte exemplo de código C# usando a Biblioteca de cliente de armazenamento trata automaticamente os tokens de continuação, se o serviço Tabela retorná-los em uma resposta:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

O código c# a seguir trata os tokens de continuação explicitamente:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;
do
{
    var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
        // ...
    }
    
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Usando tokens de continuação explicitamente, você pode controlar quando o aplicativo recupera o próximo segmento de dados. Por exemplo, se seu aplicativo cliente habilitar os usuários a percorrer as entidades armazenadas em uma tabela, um usuário poderá decidir não percorrer todas as entidades recuperadas pela consulta para que seu aplicativo só use um token de continuação para recuperar o próximo segmento quando o usuário tive terminado a paginação de todas as entidades no segmento atual. Essa abordagem tem vários benefícios:  

* Ela permite que você limite a quantidade de dados a ser recuperada do serviço Tabela e mover pela rede.  
* Ela permite que você execute E/s assíncrona no .NET.  
* Ela permite que você serialize o token de acompanhamento para o armazenamento persistente para que você possa continuar caso um aplicativo falhe.  

> [!NOTE]
> Um token de continuação normalmente retorna um segmento que contém 1.000 entidades, embora possa ser menos. Este também será o caso se você limitar o número de entradas que uma consulta retorna, usando **Take** para retornar as primeiras n entidades que correspondem aos seus critérios de pesquisa: o serviço Tabela pode retornar um segmento contendo menos de n entidades, junto com um token de continuação para permitir que você recupere as entidades restantes.  
> 
> 

O código c# a seguir mostra como modificar o número de entidades retornadas dentro de um segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projeção do lado do servidor
Uma única entidade pode ter até 255 propriedades e até 1 MB de tamanho. Ao consultar a tabela e recuperar entidades, você talvez não precise de todas as propriedades e pode evitar a transferência desnecessária de dados (para ajudar a reduzir a latência e custo). Você pode usar a projeção do lado do servidor para transferir apenas as propriedades que precisa. O exemplo a seguir recupera apenas a propriedade **Email** (com **PartitionKey**, **RowKey**, **Timestamp** e **ETag**) das entidades selecionadas pela consulta.  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
    new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
    Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Observe como o valor **RowKey** fica disponível, mesmo que não tenha sido incluído na lista de propriedades para recuperação.  

## <a name="modifying-entities"></a>Modificando entidades
A Biblioteca de Cliente de Armazenamento permite que você modifique suas entidades armazenadas no serviço Tabela, inserindo, excluindo e atualizando entidades. Você pode usar EGTs para várias operações de inserção, atualização e exclusão para reduzir o número de viagens de ida e volta necessárias e melhorar o desempenho da solução.  

Observe que as exceções geradas quando a Biblioteca de Cliente de Armazenamento executa uma EGT normalmente incluem o índice da entidade que causou a falha no lote. Isso é útil quando você está depurando código que usa EGTs.  

Você também deve considerar como seu design afeta a forma de tratamento, por parte do cliente, das operações de simultaneidade e atualização.  

### <a name="managing-concurrency"></a>Gerenciando simultaneidade
Por padrão, o serviço Tabela implementa verificações de simultaneidade otimista no nível de entidades individuais para as operações **Inserir**, **Mesclar** e **Excluir**, embora um cliente possa forçar o serviço Tabela a ignorar essas verificações. Para obter mais informações sobre como o serviço Tabela gerencia a simultaneidade, confira [Gerenciando a simultaneidade no Armazenamento do Microsoft Azure](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Mesclar ou substituir
O método **Replace** da classe **TableOperation** sempre substitui a entidade completa no serviço Tabela. Se você não incluir uma propriedade na solicitação quando essa propriedade existe na entidade armazenada, a solicitação removerá a propriedade da entidade armazenada. A menos que você queira remover uma propriedade explicitamente de uma entidade armazenada, você deve incluir todas as propriedades na solicitação.  

Você pode usar o método **Merge** da classe **TableOperation** para reduzir a quantidade de dados enviados para o serviço Tabela quando quiser atualizar uma entidade. O método **Mesclar** substitui todas as propriedades na entidade armazenada por valores de propriedade da entidade incluída na solicitação, mas deixa intactas quaisquer eventuais propriedades na entidade armazenada que não estejam incluídas na solicitação. Isso é útil se você tiver grandes entidades e só precisar atualizar um pequeno número de propriedades em uma solicitação.  

> [!NOTE]
> Os métodos **Replace** e **Merge** falharão se a entidade não existir. Como alternativa, você pode usar os métodos **InsertOrReplace** e **InsertOrMerge**, que criam uma nova entidade se ela não existir.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Trabalhando com tipos de entidade heterogênea
O serviço Tabela é um armazenamento de tabela *sem esquema* , o que significa que uma única tabela pode armazenar entidades de vários tipos, fornecendo grande flexibilidade no design. O exemplo a seguir ilustra uma tabela que armazena as entidades funcionário e departamento:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Observe que cada entidade deve ter ainda os valores de **PartitionKey**, **RowKey** e **Timestamp**, mas pode ter qualquer conjunto de propriedades. Além disso, não há nada para indicar o tipo de uma entidade, a menos que você opte por armazenar essa informação em algum lugar. Há duas opções para identificar o tipo de entidade:  

* Prefixe o tipo de entidade à **RowKey** (ou possivelmente à **PartitionKey**). Por exemplo, **EMPLOYEE_000123** ou **DEPARTMENT_SALES** como valores de **RowKey**.  
* Use uma propriedade separada para registrar o tipo de entidade, conforme mostrado na tabela a seguir.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Funcionário</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Funcionário</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>department</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Funcionário</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

A primeira opção, prefixar o tipo de entidade a **RowKey**, é útil quando há uma possibilidade de que duas entidades de tipos diferentes possam ter o mesmo valor de chave. Ela também agrupa entidades do mesmo tipo juntas na partição.  

As técnicas discutidas nesta seção são importantes principalmente para a discussão sobre [Relações de herança](table-storage-design-modeling.md#inheritance-relationships), anteriormente neste guia, no artigo [Relações de modelagem](table-storage-design-modeling.md).  

> [!NOTE]
> Você deve considerar a inclusão do número de versão no valor do tipo de entidade para habilitar aplicativos clientes a desenvolverem objetos POCO e trabalhem com diferentes versões.  
> 
> 

O restante desta seção descreve alguns dos recursos na Biblioteca de Cliente de Armazenamento que facilitam o trabalho com vários tipos de entidade na mesma tabela.  

### <a name="retrieving-heterogeneous-entity-types"></a>Recuperando tipos de entidade heterogênea
Se você estiver usando a Biblioteca de Cliente de Armazenamento, tem três opções para trabalhar com vários tipos de entidade.  

Se souber o tipo de entidade armazenado com determinados valores de **RowKey** e **PartitionKey**, você poderá especificar o tipo de entidade ao recuperar a entidade, conforme mostrado nos dois exemplos anteriores que recuperam entidades do tipo **EmployeeEntity**: [Executar uma consulta de ponto usando a Biblioteca de Clientes de Armazenamento](#executing-a-point-query-using-the-storage-client-library) e [Recuperar várias entidades usando LINQ](#retrieving-multiple-entities-using-linq).  

A segunda opção é usar o tipo **DynamicTableEntity** (um recipiente de propriedades), em vez de um tipo concreto de entidade POCO (essa opção também pode melhorar o desempenho, porque não é necessário serializar e desserializar a entidade para tipos .NET). O código C# a seguir recupera potencialmente várias entidades de diferentes tipos de tabela, mas retorna todas as entidades como instâncias **DynamicTableEntity** . Ele usa a propriedade **EntityType** para determinar o tipo de cada entidade:  

```csharp
string filter =
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
        TableOperators.And,
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "F")));
        
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
    
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
    EntityProperty entityTypeProperty;
    if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
    {
        if (entityTypeProperty.StringValue == "Employee")
        {
            // use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Observe que para recuperar outras propriedades, você deve usar o método **TryGetValue** na propriedade **Properties** da classe **DynamicTableEntity**.  

Uma terceira opção é combinar o uso do tipo **DynamicTableEntity** e uma instância **EntityResolver**. Isso permite que você resolver para vários tipos POCO na mesma consulta. Neste exemplo, o **EntityResolver** delegado usa a propriedade **EntityType** para distinguir entre os dois tipos de entidade retornados pela consulta. O método **Resolve** usa o **resolvedor** delegado para resolver instâncias **DynamicTableEntity** para instâncias **TableEntity**.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{
    TableEntity resolvedEntity = null;
    if (props["EntityType"].StringValue == "Department")
    {
        resolvedEntity = new DepartmentEntity();
    }
    else if (props["EntityType"].StringValue == "Employee")
    {
        resolvedEntity = new EmployeeEntity();
    }
    else 
    {
        throw new ArgumentException("Unrecognized entity", "props");
    }

    resolvedEntity.PartitionKey = pk;
    resolvedEntity.RowKey = rk;
    resolvedEntity.Timestamp = ts;
    resolvedEntity.ETag = etag;
    resolvedEntity.ReadEntity(props, null);
    return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
        
TableQuery<DynamicTableEntity> entityQuery = new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
    if (e is DepartmentEntity)
    {
        // ...
    }
    else if (e is EmployeeEntity)
    {
        // ...
    }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Modificando tipos de entidade heterogênea
Você não precisa saber o tipo de uma entidade para excluí-la, e você sempre sabe o tipo de uma entidade quando a insere. No entanto, você pode usar o tipo **DynamicTableEntity** para atualizar uma entidade sem saber seu tipo e sem usar uma classe de entidade POCO. O exemplo de código a seguir recupera uma única entidade e verifica se a propriedade **EmployeeCount** existe antes de atualizá-la.  

```csharp
TableResult result = employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;
if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
    throw new InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}

countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));
```

## <a name="controlling-access-with-shared-access-signatures"></a>Controlando o acesso com assinaturas de acesso compartilhado
É possível usar tokens de SAS (Assinatura de Acesso Compartilhado) para permitir que aplicativos cliente modifiquem (e consultem) entidades de tabela sem necessidade de incluir a chave da conta de armazenamento no código. Normalmente, há três benefícios principais para usar SAS em seu aplicativo:  

* Você não precisa distribuir sua chave de conta de armazenamento para uma plataforma insegura (como um dispositivo móvel) para permitir que esse dispositivo acesse e modifique entidades no serviço Tabela.  
* Você pode descarregar parte do trabalho que as funções Web e de trabalho desempenham no gerenciamento de suas entidades em dispositivos clientes, como computadores de usuários finais e dispositivos móveis.  
* Você pode atribuir um conjunto de permissões restrito e de tempo limitado a um cliente (como acesso somente leitura a recursos específicos).  

Para obter mais informações sobre como usar tokens SAS com o serviço Tabela, consulte [Uso de SAS (Assinaturas de Acesso Compartilhado)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

No entanto, você ainda deve gerar os tokens SAS que concedem a um aplicativo cliente para as entidades no serviço Tabela: você deve fazer isso em um ambiente com acesso seguro às chaves de conta de armazenamento. Geralmente, você usa uma função de trabalho ou Web para gerar tokens SAS e enviá-los aos aplicativos clientes que precisam acessar suas entidades. Como ainda há uma sobrecarga envolvida na geração e fornecimento de tokens SAS aos clientes, você deve considerar a melhor maneira de reduzir essa sobrecarga, especialmente em cenários de alto volume.  

É possível gerar um token SAS que conceda acesso a um subconjunto de entidades em uma tabela. Por padrão, você cria um token SAS para uma tabela inteira, mas também é possível especificar que o token SAS conceda acesso a um intervalo de valores de **PartitionKey** ou a um intervalo de valores de **PartitionKey** e **RowKey**. Você pode optar por gerar tokens SAS para usuários individuais do sistema, de modo que o token SAS de cada usuário só permita acesso às suas próprias entidades no serviço Tabela.  

## <a name="asynchronous-and-parallel-operations"></a>Operações paralelas e assíncronas
Desde que você esteja distribuindo suas solicitações por várias partições, pode melhorar a capacidade de resposta do cliente e a taxa de transferência usando consultas assíncronas ou paralelas.
Por exemplo, você pode ter duas ou mais instâncias de função de trabalho acessando suas tabelas em paralelo. Você pode ter funções de trabalho individuais responsáveis por determinados conjuntos de partições ou simplesmente ter várias instâncias de função de trabalho, cada uma capaz de acessar todas as partições em uma tabela.  

Dentro de uma instância do cliente, você pode melhorar o desempenho executando operações de armazenamento de forma assíncrona. A Biblioteca de Cliente de Armazenamento facilita a gravação de consultas e modificações assíncronas. Por exemplo, você pode começar com o método síncrono que recupera todas as entidades em uma partição, como mostra o código c# a seguir:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

    TableContinuationToken continuationToken = null;
    do
    {
        var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
        
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

Você pode facilmente modificar esse código para que a consulta seja executada de forma assíncrona, da seguinte maneira:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);
    
    TableContinuationToken continuationToken = null;
    do
    {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
    
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

Neste exemplo assíncrono, você pode ver as seguintes alterações da versão síncrona:  

* A assinatura do método agora inclui o modificador **async** e retorna uma instância **Task**.  
* Em vez de chamar o método **ExecuteSegmented** para recuperar os resultados, agora o método chama o método **ExecuteSegmentedAsync** e usa o modificador **await** para recuperar resultados de forma assíncrona.  

O aplicativo cliente pode chamar esse método várias vezes (com valores diferentes para o parâmetro **department** ) e cada consulta será executada em um thread separado.  

Observe que não há qualquer versão assíncrona do método **Execute** na classe **TableQuery**, pois a interface **IEnumerable** não dá suporte à enumeração assíncrona.  

Você também pode inserir, atualizar e excluir entidades de forma assíncrona. O exemplo c# a seguir mostra um método síncrono simples para inserir ou substituir uma entidade funcionário:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Você pode modificar este código facilmente para que a atualização seja executada de forma assíncrona da seguinte maneira:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Neste exemplo assíncrono, você pode ver as seguintes alterações da versão síncrona:  

* A assinatura do método agora inclui o modificador **async** e retorna uma instância **Task**.  
* Em vez de chamar o método **Execute** para atualizar a entidade, agora o método chama o método **ExecuteAsync** e usa o modificador **await** para recuperar resultados de modo assíncrono.  

O aplicativo cliente pode chamar vários métodos assíncronos como esse, e cada invocação de método será executado em um thread separado.  

## <a name="next-steps"></a>Próximas etapas

- [Relações de modelagem](table-storage-design-modeling.md)
- [Design para consulta](table-storage-design-for-query.md)
- [Criptografando dados de tabela](table-storage-design-encrypt-data.md)
- [Design para modificação de dados](table-storage-design-for-modification.md)
