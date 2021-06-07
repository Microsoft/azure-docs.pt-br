---
title: Lista de verificação de desempenho e escalabilidade do Armazenamento de tabela – Armazenamento do Azure
description: Uma lista de verificação de práticas comprovadas para uso com o Armazenamento de tabela ao desenvolver aplicativos de alto desempenho.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.subservice: tables
ms.custom: devx-track-csharp
ms.openlocfilehash: 71b1f3cfa1df86b417c468d56f67cd7fe8d71d73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004697"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Lista de verificação de desempenho e escalabilidade para Armazenamento de tabela

A Microsoft desenvolveu várias práticas comprovadas para desenvolver aplicativos de alto desempenho com o Armazenamento de Tabela. Essa lista de verificação identifica as principais práticas que os desenvolvedores podem seguir para otimizar o desempenho. Tenha essas práticas em mente ao criar seu aplicativo e durante todo o processo.

O Armazenamento do Azure tem metas de escalabilidade e desempenho para capacidade, taxa de transação e largura de banda. Para obter mais informações sobre as metas de escalabilidade do Armazenamento do Azure, confira [Metas de escalabilidade e desempenho das contas de Armazenamento Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json) e [Metas de escalabilidade e desempenho do Armazenamento de Tabelas](scalability-targets.md).

## <a name="checklist"></a>Lista de verificação

Este artigo organiza as práticas comprovadas de desempenho em uma lista de verificação que você pode seguir enquanto desenvolve seu aplicativo de Armazenamento de Tabela.

| Concluído | Categoria | Consideração de design |
| --- | --- | --- |
| &nbsp; |Metas de escalabilidade |[Você pode criar seu aplicativo para usar não mais do que o número máximo de contas de armazenamento?](#maximum-number-of-storage-accounts) |
| &nbsp; |Metas de escalabilidade |[Você está evitando se aproximar dos limites de capacidade e de transação?](#capacity-and-transaction-targets) |
| &nbsp; |Metas de escalabilidade |[Você leva as metas de escalabilidade em consideração para entidades por segundo?](#targets-for-data-operations) |
| &nbsp; |Rede |[Os dispositivos cliente têm largura de banda suficiente e baixa latência para alcançar o desempenho necessário?](#throughput) |
| &nbsp; |Rede |[Os dispositivos cliente têm um link de rede de alta qualidade?](#link-quality) |
| &nbsp; |Rede |[O aplicativo cliente está na mesma região que a conta de armazenamento?](#location) |
| &nbsp; |Acesso direto do cliente |[Você está usando SAS (assinaturas de acesso compartilhado) e CORS (compartilhamento de recursos entre origens) para habilitar o acesso direto ao Armazenamento do Azure?](#sas-and-cors) |
| &nbsp; |Envio em lote |[O aplicativo está enviando atualizações por lote usando transações de grupo de entidades?](#batch-transactions) |
| &nbsp; |Configuração do .NET |[Você está usando o .NET Core 2.1 ou posterior para obter um desempenho ideal?](#use-net-core) |
| &nbsp; |Configuração do .NET |[Você configurou seu cliente para usar uma quantidade suficiente de conexões simultâneas?](#increase-default-connection-limit) |
| &nbsp; |Configuração do .NET |[Para aplicativos .NET, você configurou o .NET para usar um número suficiente de threads?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelismo |[Você garantiu que o paralelismo está corretamente associado para não sobrecarregar as funcionalidades do seu cliente ou se aproximar das metas de escalabilidade?](#unbounded-parallelism) |
| &nbsp; |Ferramentas |[Você está usando as últimas versões das bibliotecas de cliente e ferramentas fornecidas pela Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Novas tentativas |[Você está usando uma política de repetição com uma retirada exponencial para limitar erros e tempos limite?](#timeout-and-server-busy-errors) |
| &nbsp; |Novas tentativas |[Seu aplicativo evita novas tentativas para erros que não admitem novas tentativas?](#non-retryable-errors) |
| &nbsp; |Configuração |[Você usa JSON para suas solicitações de tabela?](#use-json) |
| &nbsp; |Configuração |[Você desativou o algoritmo Nagle para melhorar o desempenho de pequenas solicitações?](#disable-nagle) |
| &nbsp; |Tabelas e partições |[Você particionou seus dados corretamente?](#schema) |
| &nbsp; |Partições mais acessadas |[Você evita padrões do tipo "somente anexar" e "somente incluir"?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Partições mais acessadas |[Suas inserções/atualizações valem para diversas partições?](#high-traffic-data) |
| &nbsp; |Escopo da consulta |[Você criou seu esquema para permitir o uso de consultas pontuais na maioria dos casos e de consultas de tabelas raramente?](#query-scope) |
| &nbsp; |Densidade de consulta |[As suas consultas geralmente verificam e indicam quais linhas o aplicativo usará?](#query-density) |
| &nbsp; |Limitar dados retornados |[Você usa a filtragem para evitar o retorno de entidades que não são necessárias?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Limitar dados retornados |[Você usa a projeção para evitar o retorno de propriedades que não são necessárias?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Desnormalização |[Você desnormalizou seus dados a fim de evitar consultas ineficientes sou diversas solicitações de leitura ao tentar obter dados?](#denormalization) |
| &nbsp; |Inserir, atualizar e excluir |[Você compila as solicitações que devem ser transacionais ou que podem ser executadas ao mesmo tempo para diminuir a quantidade de viagens de ida e volta?](#batching) |
| &nbsp; |Inserir, atualizar e excluir |[Você evita novas tentativas nas entidades apenas para determinar se a chamada deve ser inserida ou atualizada?](#upsert) |
| &nbsp; |Inserir, atualizar e excluir |[Você já pensou em armazenar séries de dados que serão recuperados em conjunto frequentemente, em uma única entidade e como propriedades, em vez de serem recuperados como diversas entidades?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Inserir, atualizar e excluir |[Você já pensou em usar blobs no lugar das tabelas para as entidades que serão recuperadas em conjunto e que podem ser escritas em lotes (por exemplo, dados de séries temporais)?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Metas de escalabilidade

Se o seu aplicativo se aproximar ou ultrapassar alguma das metas de escalabilidade, pode haver aumento da latência e restrição das transações. Quando o Armazenamento do Azure limita seu aplicativo, o serviço começa a retornar os códigos de erro 503 (Servidor ocupado) ou 500 (Tempo limite da operação). Evitar esses erros mantendo-se dentro dos limites dos destinos de escalabilidade é uma parte importante do aprimoramento do desempenho do seu aplicativo.

Para obter mais informações sobre as metas de escalabilidade do serviço Tabela, confira [Metas de escalabilidade e desempenho do Armazenamento de Tabelas](scalability-targets.md).

### <a name="maximum-number-of-storage-accounts"></a>Número máximo de contas de armazenamento

Se você está se aproximando do número máximo de contas de armazenamento permitidas para uma combinação de assinatura/região específica, você está usando várias contas de armazenamento para fragmentar a fim de aumentar a entrada, a saída, as IOPS (operações de E/S por segundo) ou a capacidade? Nesse cenário, a Microsoft recomenda que você use maiores limites para contas de armazenamento a fim de reduzir o número de contas de armazenamento necessárias para sua carga de trabalho, se possível. Entre em contato com o [Suporte do Azure](https://azure.microsoft.com/support/options/) para solicitar maiores limites para sua conta de armazenamento. Para obter mais informações, confira [Anunciar contas de armazenamento maiores e de maior escala](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Metas de capacidade e de transação

Se seu aplicativo estiver lidando com metas de escalabilidade de uma única conta de armazenamento, você pode adotar uma destas abordagens:  

- Repensar a carga de trabalho que faz com que o aplicativo se aproxime da meta de escalabilidade ou a ultrapasse. Você pode alterar o aplicativo para que ele use menos largura de banda, menos capacidade ou menos transações?
- Se o aplicativo ultrapassar uma das metas de escalabilidade propositalmente, crie diversas contas de armazenamento e particione os dados do seu aplicativo nessas contas. Se você usar esse padrão, crie o aplicativo de forma que seja possível adicionar mais contas de armazenamento posteriormente, para balancear a carga. O único custo das contas de armazenamento é o uso dos dados armazenados, das transações feitas ou dos dados transferidos.
- Se o aplicativo estiver atingindo as metas de largura de banda, considere compactar os dados no cliente para reduzir a largura de banda necessária para enviar os dados para o Armazenamento do Azure.
    Embora a compactação de dados possa economizar largura de banda e melhorar o desempenho de rede, ela também pode ter efeitos negativos sobre o desempenho. Avalie o impacto de desempenho dos requisitos de processamento adicionais para compactação e descompactação de dados no lado do cliente. Tenha em mente que armazenar dados compactados pode dificultar a solução de problemas, porque pode ser mais desafiador exibir os dados usando ferramentas padrão.
- Se o aplicativo estiver se aproximando das metas de escalabilidade, verifique se você está usando uma retirada exponencial para novas tentativas. É melhor tentar evitar alcançar as metas de escalabilidade implementando as recomendações descritas neste artigo. No entanto, usar uma retirada exponencial para novas tentativas impedirá que seu aplicativo tente novamente com rapidez, o que poderia piorar a limitação. Para obter mais informações, confira a seção intitulada [Erros de Tempo Limite e Servidor Ocupado](#timeout-and-server-busy-errors).

### <a name="targets-for-data-operations"></a>Destinos para operações de dados

O Armazenamento do Azure executa o balanceamento de carga conforme o tráfego para a sua conta de armazenamento aumenta, mas se o tráfego exibir picos repentinos, talvez você não conseguirá obter esse volume de taxa de transferência imediatamente. Espere ver uma limitação e/ou tempos limite durante a intermitência enquanto o Armazenamento do Azure balanceia a carga da tabela automaticamente. Os aumentos graduais geralmente apresentam melhores resultados, pois o sistema ganha tempo para balancear a carga corretamente.

#### <a name="entities-per-second-storage-account"></a>Entidades por segundo (conta de armazenamento)

O limite de escalabilidade para acessar tabelas é de até 20 mil entidades (1 KB para cada) por segundo em cada conta. Em geral, cada entidade inserida, atualizada, excluída ou verificada é computada nessa meta. Assim, uma inserção em lote com 100 entidades é computada como 100 entidades. Uma consulta que verifica 1000 entidades e retorna apenas 5 é computada como 1000 entidades.

#### <a name="entities-per-second-partition"></a>Entidades por segundo (partição)

Em uma única partição, a meta de escalabilidade para acessar tabelas é de 2000 entidades (1 KB para cada) por segundo, usando a mesma contagem descrita na seção anterior.

## <a name="networking"></a>Rede

As restrições físicas da rede do aplicativo podem ter um impacto considerável no desempenho. As seções a seguir descrevem algumas das limitações que os usuários podem enfrentar.  

### <a name="client-network-capability"></a>Funcionalidade da rede do cliente

A largura de banda e a qualidade do link da rede desempenham funções importantes no desempenho do aplicativo, conforme descrito nas seções a seguir.

#### <a name="throughput"></a>Produtividade

No caso da largura de banda, muitas vezes o problema está relacionado às funcionalidades do cliente. As instâncias maiores do Azure têm NICs com mais capacidade. Por isso, você deve usar uma instância maior ou mais VMs se precisar de limites de rede mais altos em um único computador. Se você estiver acessando o Armazenamento do Azure de um aplicativo local, a mesma regra se aplicará: compreender as funcionalidades de rede do dispositivo do cliente e a conectividade de rede com o local de armazenamento do Azure e aperfeiçoá-los conforme necessário ou criar seu aplicativo para trabalhar dentro de suas funcionalidades.

#### <a name="link-quality"></a>Qualidade do link

Como acontece com qualquer uso de rede, tenha em mente que as condições de rede que resultam em erros e a perda do pacote desacelerarão a taxa de transferência efetiva.  Usar WireShark ou NetMon pode ajudar a identificar esse problema.  

### <a name="location"></a>Location

Em todos os ambientes, colocar o cliente próximo ao servidor proporciona o melhor desempenho. Para acessar o armazenamento do Azure com o mínimo de latência, o melhor local para o cliente é a região na qual o Azure se encontra. Por exemplo, se tiver um aplicativo Web do Azure que usa o Armazenamento do Azure, localize-os dentro de uma única região, como o Oeste dos EUA ou o Sudeste Asiático. Colocalizar recursos reduz a latência e o custo, pois o uso de largura de banda em uma única região é gratuito.  

Se os aplicativos cliente acessarem o Armazenamento do Azure, mas não estiverem hospedados no Azure, como aplicativos de dispositivo móvel ou serviços corporativos locais, a localização da conta de armazenamento em uma região próxima a esses clientes poderá reduzir a latência. Se os clientes estiverem amplamente distribuídos (por exemplo, alguns na América do Norte e outros na Europa), considere usar uma conta de armazenamento por região. A implementação dessa solução é mais fácil se os dados armazenados pelo aplicativo são específicos aos usuários e se não é necessário replicar os dados entre as contas de armazenamento.

## <a name="sas-and-cors"></a>SAS e CORS

Suponha que você precise autorizar um código, como o JavaScript que está sendo executado no navegador da Web de um usuário ou em um aplicativo de celular, a acessar dados no armazenamento do Azure. Uma abordagem é criar um aplicativo de serviço que funcione como um proxy. O dispositivo do usuário é autenticado no serviço que, por sua vez, autoriza o acesso aos recursos do Armazenamento do Azure. Dessa forma, você pode evitar a exposição das chaves de conta de armazenamento em dispositivos que não são seguros. No entanto, essa abordagem gera uma sobrecarga significativa no aplicativo do serviço, porque todos os dados transferidos entre o dispositivo do usuário e o Armazenamento do Azure devem passar pelo aplicativo do serviço.

Você pode evitar usar um aplicativo de serviço como um proxy para o Armazenamento do Azure usando SAS (assinaturas de acesso compartilhado). Usando SAS, você pode permitir que o dispositivo do seu usuário faça solicitações diretamente ao Armazenamento do Azure usando um token de acesso limitado. Por exemplo, se um usuário desejar fazer upload de uma foto para seu aplicativo, seu aplicativo de serviço poderá gerar uma SAS e enviá-la ao dispositivo do usuário. O token SAS pode conceder permissão para gravar em um recurso do Armazenamento do Azure durante um intervalo especificado de tempo; depois disso, o token SAS expirará. Para obter mais informações sobre SAS, confira [Conceder acesso limitado a recursos de Armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../common/storage-sas-overview.md).  

Normalmente, um navegador da Web não permitirá que o JavaScript em uma página hospedada por um site em um domínio execute determinadas operações, como de gravação, em outro domínio. Conhecida como a política de mesma origem, essa política impede que um script mal-intencionado em uma página obtenha acesso a dados em outra página da Web. No entanto, a política de mesma origem pode ser uma limitação ao criar uma solução na nuvem. O CORS (compartilhamento de recurso entre origens) é um recurso do navegador que permite que o domínio de destino se comunique com o navegador ao qual ele confia as solicitações que se originam no domínio de origem.

Por exemplo, suponha que um aplicativo Web em execução no Azure faça uma solicitação para um recurso para uma conta do Armazenamento do Azure. O aplicativo Web é o domínio de origem e a conta de armazenamento é o domínio de destino. Você pode configurar o CORS para que qualquer um dos serviços do Armazenamento do Azure se comunique com o navegador da Web cujas solicitações do domínio de origem têm a confiança do Armazenamento do Azure. Para obter mais informações sobre o CORS, confira [Suporte ao CORS (compartilhamento de recurso entre origens) para o Armazenamento do Azure](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
SAS e CORS podem ajudar você a evitar uma carga desnecessária em seu aplicativo Web.  

## <a name="batch-transactions"></a>Transações em lote

O serviço Tabela dá suporte a transações em lote em entidades que estão na mesma tabela e pertencem ao mesmo grupo de partições. Para obter mais informações, confira [Executar transações de grupo de entidades](/rest/api/storageservices/performing-entity-group-transactions).

## <a name="net-configuration"></a>Configuração do .NET

Esta seção é útil para quem usa o .NET Framework, pois lista diversas configurações rápidas que você pode usar para fazer melhorias significativas no desempenho.  Se estiver usando outras linguagens, verifique se conceitos parecidos aplicam-se à linguagem escolhida.  

### <a name="use-net-core"></a>Usar o .NET Core

Desenvolva seus aplicativos do Armazenamento do Azure com o .NET Core 2.1 ou superior para aproveitar os aprimoramentos de desempenho. O uso do .NET Core 3.x é recomendado quando possível.

Para saber mais sobre os aprimoramentos de desempenho no .NET Core, confira as seguintes postagens no blog:

- [Aprimoramentos de desempenho no .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Aprimoramentos de desempenho no .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Aumentar limite de conexão padrão

No .NET, o código a seguir aumenta o limite de conexão de padrão (que geralmente é 2 em um ambiente de cliente ou 10 em um ambiente de servidor) a 100. Normalmente, você deve definir o valor para aproximadamente igual ao número de threads utilizados pelo seu aplicativo.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Defina o limite da conexão antes de abrir conexões.  

No caso de outras linguagens de programação, confira a documentação da linguagem para verificar como definir o limite da conexão.  

Para saber mais, confira a postagem no blog [Serviços Web: conexões simultâneas](/archive/blogs/darrenj/web-services-concurrent-connections).  

### <a name="increase-minimum-number-of-threads"></a>Aumentar o número mínimo de threads

Se você estiver usando chamadas síncronas juntamente com tarefas assíncronas, será conveniente aumentar o número de threads no pool:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Para obter mais informações, confira o método [ThreadPool.SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads).  

## <a name="unbounded-parallelism"></a>Paralelismo não associado

Embora o paralelismo possa ser ótimo para o desempenho, tenha cuidado ao usar o paralelismo não associado, que significa que não há nenhum limite imposto sobre o número de threads ou solicitações paralelas. Limite as solicitações paralelas para fazer upload ou baixar dados, para acessar várias partições na mesma conta de armazenamento ou acessar vários itens na mesma partição. Se o paralelismo não estiver associado, o aplicativo poderá ultrapassar as funcionalidades do dispositivo cliente ou as metas de escalabilidade da conta de armazenamento, o que resultará em limitação e latências mais longas.  

## <a name="client-libraries-and-tools"></a>Bibliotecas de cliente e ferramentas

Para obter o melhor desempenho, sempre use as bibliotecas de cliente e ferramentas mais recentes fornecidas pela Microsoft. As bibliotecas de cliente do Armazenamento do Azure estão disponíveis para várias linguagens. O Armazenamento do Azure também dá suporte ao PowerShell e à CLI do Azure. A Microsoft desenvolve ativamente essas ferramentas e bibliotecas de cliente pensando no desempenho, os mantém atualizados com as versões de serviço mais recentes e verifica se eles lidam com muitas práticas de desempenho comprovadas internamente.

## <a name="handle-service-errors"></a>Manipular erros de serviço

O Armazenamento do Azure retorna um erro quando o serviço não pode processar uma solicitação. Entender os erros que podem ser retornados pelo Armazenamento do Azure em um determinado cenário é útil para otimizar o desempenho.

### <a name="timeout-and-server-busy-errors"></a>Erros de Tempo Limite e Servidor Ocupado

O Armazenamento do Azure poderá limitar seu aplicativo se ele se aproximar dos limites de escalabilidade. Em alguns casos, o Armazenamento do Azure pode ser incapaz de manipular uma solicitação devido a algumas condições transitórias. Em ambos os casos, o serviço pode retornar um erro 503 (Servidor Ocupado) ou 500 (Tempo Limite). Esses erros também poderão ocorrer se o serviço estiver reequilibrando partições de dados para permitir uma taxa de transferência mais alta. Normalmente, o aplicativo cliente deve repetir a operação que gera um erro desses erros. No entanto, se o Armazenamento do Azure estiver limitando o aplicativo porque ele está ultrapassando as metas de escalabilidade ou porque o serviço não pôde atender à solicitação por algum motivo, novas tentativas poderão piorar o problema. O uso de uma política de repetição de retirada exponencial é recomendado e o padrão das bibliotecas de cliente é esse comportamento. Por exemplo, o aplicativo pode fazer uma nova tentativa em 2 segundos, 4 segundos, 10 segundos e 30 segundos para então abandonar o processo. Dessa forma, seu aplicativo reduz significativamente sua carga sobre o serviço, em vez de exacerbar o comportamento que poderia causar a limitação.  

Pode haver novas tentativas imediatas em caso de erros de conectividade porque esses erros não são resultado de limitações e devem ser temporários.  

### <a name="non-retryable-errors"></a>Erros sem nova tentativa

As bibliotecas de cliente manipulam as novas tentativas com um reconhecimento de quais erros podem ser repetidos e quais não. No entanto, se você estiver chamando a API REST do Armazenamento do Azure diretamente, haverá alguns erros que você não deverá repetir. Por exemplo, um erro 400 (Solicitação Inválida) indica que o aplicativo cliente envia uma solicitação que não pôde ser processada, porque ela não estava no formato esperado. O reenvio dessa solicitação sempre resulta na mesma resposta, por isso as novas tentativas não são úteis. Se você estiver chamando a API REST do Armazenamento do Azure diretamente, lembre-se de possíveis erros e se eles deverão ser repetidos.

Para obter mais informações sobre os códigos de erro do Armazenamento do Azure, confira [Status e códigos de erro](/rest/api/storageservices/status-and-error-codes2).

## <a name="configuration"></a>Configuração

Esta seção lista diversas configurações rápidas que você pode usar para fazer melhorias significativas no desempenho do serviço Tabela:

### <a name="use-json"></a>Usar JSON

Começando na versão 2013-08-15 do serviço de armazenamento, o serviço Tabela dá suporte ao uso de JSON, em vez do formato AtomPub baseado em XML para transferir dados de tabela. Usar JSON pode reduzir o tamanho do conteúdo em até 75% e melhorar consideravelmente o desempenho do seu aplicativo.

Para saber mais, veja a postagem [Tabelas do Microsoft Azure: introdução ao JSON](/archive/blogs/windowsazurestorage/windows-azure-tables-introducing-json) e [Formato de carga para operações do serviço Tabela](/rest/api/storageservices/Payload-Format-for-Table-Service-Operations).

### <a name="disable-nagle"></a>Desabilitar o Nagle

O algoritmo de Nagle é implementado largamente em redes TCP/IP como meio de melhorar o desempenho das redes. No entanto, ele não é ideal em todas as circunstâncias (como em ambientes altamente interativos). O algoritmo do Nagle tem um impacto negativo sobre o desempenho de solicitações para o serviço Tabela do Azure e você deve desabilite-o, se possível.

## <a name="schema"></a>Esquema

Como você representa e consulta os seus dados é o maior fator único que afeta o desempenho do serviço Tabela. Embora cada aplicativo seja único, esta seção descreve algumas práticas gerais comprovadas relacionadas:

- Ao design da tabela
- A consultas eficientes
- A atualizações de dados eficientes

### <a name="tables-and-partitions"></a>Tabelas e partições

As tabelas são divididas em partições. Cada entidade armazenada em uma partição compartilha a mesma chave de partição e tem uma chave de linha exclusiva que a identifica dentro da partição em questão. As partições trazem benefícios, mas também criam limites de escalabilidade.

- Benefícios: é possível atualizar as entidades na mesma partição em uma única transação atômica e em lote que contenha até 100 operações de armazenamento independentes (limite total de 4 MB). Partindo do pressuposto de que a mesma quantidade de entidades pode ser recuperada, você também pode consultar dados em uma única partição com mais eficiência do que a consulta a dados presentes em diversas partições (consulte as demais recomendações sobre consulta aos dados das tabelas).
- Limite de escalabilidade: o acesso às entidades armazenadas em uma única partição não pode passar por balanceamento de carga porque as partições dão suporte a transações atômicas em lote. Por esse motivo, a meta de escalabilidade de cada partição da tabela é menor do que a meta do serviço Tabela como um todo.

Devido a essas características, você deve adotar estes princípios de design:

- Localize os dados que seu aplicativo cliente frequentemente atualiza ou consulta na mesma unidade lógica de trabalho na mesma partição. Por exemplo, localize dados na mesma partição se o aplicativo estiver agregando gravações ou se você estiver executando operações de lote atômicas. Além disso, é possível consultar os dados em uma única partição com mais eficiência do que fazer a consulta em diversas partições.
- Localize os dados que seu aplicativo cliente não insere, atualiza ou consulta na mesma unidade lógica de trabalho (ou seja, em uma única consulta ou atualização em lote) em partições separadas. Tenha em mente que não há limite de chaves de partição para uma única tabela. Por isso, ter milhões de chaves de partição não representa um problema e não afetará o desempenho. Por exemplo, se seu aplicativo é um site popular com logon dos usuários, usar a ID do usuário como chave de partição pode ser uma boa opção.

#### <a name="hot-partitions"></a>Partições mais acessadas

As partições mais acessadas são aquelas que recebem um porcentual desproporcional do tráfego de uma conta e não podem passar pelo balanceamento de carga porque se trata de uma única partição. Em geral, essas partições são criadas de duas formas:

#### <a name="append-only-and-prepend-only-patterns"></a>Padrões Somente Acrescentar e Somente Incluir

No padrão "Somente Acrescentar", todo (ou praticamente todo) o tráfego atribuído a uma determinada chave de partição aumenta e diminui de acordo com o momento. Por exemplo, suponha que seu aplicativo use a data atual como uma chave de partição para dados de log. Esse design faz com que todas as inserções vão para a última partição em sua tabela e o sistema não possa balancear carga devidamente. Se o volume do tráfego para a partição em questão ultrapassar a meta de escalabilidade na partição, teremos restrições como resultado. É melhor garantir que o tráfego seja enviado para diversas partições, a fim de garantir o balanceamento da carga das solicitações na tabela.

#### <a name="high-traffic-data"></a>Dados de tráfego intenso

Se você estiver particionando resultados de esquemas em uma única partição que tem dados mais usados do os dados presentes nas demais partições, também pode haver restrições, pois a partição em questão aproxima-se da meta de escalabilidade de uma única partição. É melhor garantir que seu esquema de partição não faça com que nenhuma partição específica aproxime-se das metas de escalabilidade.

### <a name="querying"></a>Consultas

Esta seção descreve as práticas comprovadas para consultar o serviço Tabela.

#### <a name="query-scope"></a>Escopo da consulta

Há diversas maneiras de especificar quais entidades devem ser consultadas. A lista a seguir descreve cada opção para o escopo de consulta.

- **Consultas pontuais:**– uma consulta pontual recupera exatamente uma entidade especificando a chave de partição e a chave bruta da entidade a ser recuperada. Essas consultas são eficientes e você deve usá-las sempre que possível.
- **Consultas de partição:** Esse tipo de consulta recupera um conjunto de dados que tem uma chave de partição em comum. Geralmente, a consulta especifica diversos valores de chave de linha ou valores de propriedade de entidade, além de uma chave de partição. Essas consultas são menos eficientes do que as consultas pontuais e devem ser usados com critério.
- **Consultas de tabela:** Esse tipo de consulta recupera um conjunto de entidades que não tem uma chave de partição em comum. Essas consultas não são eficientes e você deve evitá-las sempre que possível.

Em geral, evite as verificações (consultas cujo escopo tem mais de uma entidade). No entanto, se a verificação for necessária, tente organizar os dados de modo que as verificações recuperem os dados necessários sem verificar ou retornar grandes quantidades de entidades desnecessárias.

#### <a name="query-density"></a>Densidade de consulta

Outro fator importante na eficiência da consulta é a quantidade de entidades retornadas, em comparação à quantidade de entidades verificadas para localizar o conjunto retornado. Se o aplicativo ficar uma consulta de tabela filtrando por um valor da propriedade comum a apenas 1% dos dados, a consulta verificará 100 entidades para cada entidade retornada. As metas de escalabilidade de tabela discutidas anteriormente se relacionam ao número de entidades verificado, e não ao número de entidades retornadas: uma densidade de consulta baixa pode facilmente fazer com que o serviço Tabela limite seu aplicativo porque ele deve verificar muitas entidades para recuperar a entidade que você está procurando. Para obter mais informações sobre como evitar a limitação, confira a seção intitulada [Desnormalização](#denormalization).

#### <a name="limiting-the-amount-of-data-returned"></a>Limitar a quantidade de dados retornados

Quando você sabe que uma consulta retornará entidades que você não precisa no aplicativo cliente, considere usar um filtro para reduzir o tamanho do conjunto retornado. Embora as entidades não retornadas para o cliente ainda sejam computadas nos limites de escalabilidade, o desempenho do aplicativo melhora devido à redução no tamanho da carga da rede e na quantidade de entidades que seu aplicativo cliente deve processar. Tenha em mente que as metas de escalabilidade se relacionam ao número de entidades verificado; portanto, uma consulta que filtra muitas entidades ainda poderá resultar em limitação, mesmo se poucas entidades forem retornadas. Para obter mais informações sobre tornar as consultas eficientes, confira a seção intitulada [Densidade da consulta](#query-density).

Se seu aplicativo clientes precisar apenas de um conjunto limitado de propriedades das entidades da sua tabela, você pode usar a projeção para limitar o tamanho do conjunto de dados retornado. Como no caso da filtragem, a projeção ajuda a reduzir a carga da rede e o processamento do cliente.

#### <a name="denormalization"></a>Desnormalização

Diferente do que acontece com os bancos de dados relacionados, as práticas recomentadas para consultar os dados da tabela com eficiência causam a desnormalização dos dados. Ou seja, a duplicação dos mesmos dados em diversas entidades (uma para cada chave, que você pode usar para localizar os dados) para minimizar a quantidade de entidades a serem verificadas por uma consulta para localizar os dados necessários para o cliente, em vez de precisar verificar muitas entidades para localizar os dados necessários para o aplicativo. Por exemplo, em um site de comércio eletrônico, você pode querer localizar um pedido pela ID do cliente (apresentar os pedidos de um determinado cliente) e por data (apresentar os pedidos de uma determinada data). No armazenamento de tabela, é melhor armazenar a entidade (ou fazer referência a ela) duas vezes, uma com o nome da tabela, PK e RK para facilitar a localização da ID do cliente e outra para facilitar a localização por data.  

### <a name="insert-update-and-delete"></a>Inserir, atualizar e excluir

Esta seção descreve as práticas comprovadas para modificar as entidades armazenadas no serviço Tabela.  

#### <a name="batching"></a>Envio em lote

As transações em lote são conhecidas como transações de grupo de entidades no Armazenamento do Azure. Todas as operações em uma transação de grupo de entidades devem estar em uma única partição em uma única tabela. Quando possível, use transações de grupo de entidades para executar inserções, atualizações e exclusões em lotes. O uso de transações de grupo de entidades reduz a quantidade de viagens de ida e volta entre o aplicativo cliente e o servidor, diminui a quantidade de transações faturáveis (uma transação de grupo de entidades conta como uma única transação para fins de cobrança e pode conter até 100 operações de armazenamento) e viabiliza atualizações atômicas (em uma transação de grupo de entidades, todas as operações têm êxito ou falham). Os ambientes com altos níveis de latência, como os dispositivos móveis, veem muitos benefícios no uso das transações de grupo de entidades.  

#### <a name="upsert"></a>Upsert

Use as operações de tabela **Upsert** sempre que possível. Há dois tipos de **Upsert** e os dois podem ser mais eficientes do que as operações tradicionais **Insert** e **Update**:  

- **InsertOrMerge**: use essa operação quando desejar fazer upload de um subconjunto de propriedades da entidade, mas não souber se a entidade já existe. Se a entidade existir, essa chamada atualiza as propriedades incluídas na operação **Upsert** e deixa todas as propriedades existente como elas se encontram. Se a entidade não existir, a operação insere a nova entidade. Isso se parece com o uso de projeção em uma consulta, pois você só precisa carregar as propriedades que estão mudando.
- **InsertOrReplace**: use essa operação quando desejar fazer upload de uma entidade completamente nova, mas não souber se a entidade já existe. Use essa operação quando souber que a entidade carregada está perfeita, pois ela substitui completamente a entidade anterior. Por exemplo, você quer atualizar a entidade que armazena um local atual do usuário independente de o aplicativo já ter armazenado ou não os dados de local do usuário; a nova entidade de localização está completa e você não precisa de informações de nenhuma entidade anterior.

#### <a name="storing-data-series-in-a-single-entity"></a>Armazenar séries de dados em uma única entidade

Às vezes, um aplicativo armazena uma série de dados que ele precisa recuperar uma só vez com frequência: por exemplo, um aplicativo pode controlar o uso da CPU ao longo do tempo para plotar um gráfico sem interrupção dos dados das últimas 24 horas. Uma abordagem é ter uma entidade de tabela por hora, com cada uma delas representando uma hora específica e armazenando o uso da CPU para a hora em questão. Para obter esses dados, o aplicativo precisa recuperar as entidades que contêm os dados das últimas 24 horas.  

Como alternativa, o aplicativo pode armazenar o uso da CPU para cada hora como uma propriedade separada de uma única entidade: para atualizar a cada hora, seu aplicativo pode usar uma única chamada de **InsertOrMerge Upsert** para atualizar o valor para a hora mais recente. Para plotar os dados, o aplicativo precisa recuperar apenas uma entidade, em vez de 24, resultando em uma consulta eficiente. Para obter mais informações sobre a eficiência da consulta, confira a seção intitulada [Escopo da consulta](#query-scope)).

#### <a name="storing-structured-data-in-blobs"></a>Armazenar dados estruturados em blobs

Se você estiver executando inserções em lotes e, em seguida, recuperando intervalos de entidades, considere o uso de blobs em vez de tabelas. Um bom exemplo é um arquivo de log. Você pode enviar vários minutos de logs por lote, inseri-los e, em seguida, recuperar vários minutos de logs de uma só vez. Nesse caso, o desempenho será melhor se você usar blobs em vez de tabelas, pois você poderá reduzir significativamente o número de objetos gravados ou lidos e também possivelmente o número de solicitações que devem ser feitas.  

## <a name="next-steps"></a>Próximas etapas

- [Metas de escalabilidade e desempenho do Armazenamento de Tabelas](scalability-targets.md)
- [Metas de escalabilidade e desempenho das contas de Armazenamento Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Status e códigos de erro](/rest/api/storageservices/Status-and-Error-Codes2)