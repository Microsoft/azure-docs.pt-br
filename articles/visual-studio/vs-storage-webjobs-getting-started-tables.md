---
title: Introdução ao armazenamento do Azure e aos Serviços Conectados do Visual Studio (projetos WebJob)
description: Como começar a usar o armazenamento de Tabela do Azure em um projeto WebJobs no Visual Studio após a conexão a uma conta de armazenamento usando os serviços conectados do Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 481c2f1eaf20e317c8efa4f21f337f4073af898f
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68260755"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Introdução ao Armazenamento do Azure (Projetos WebJob do Azure)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Visão geral
Este artigo apresenta exemplos de código em C# para mostrar como usar o SDK do Azure WebJobs versão 1.x com o serviço de armazenamento de tabela do Azure. Os exemplos de código usam o [SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki) versão 1.x.

O serviço de armazenamento de Tabela do Azure armazena grandes quantidades de dados estruturados. O serviço é um repositório de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.  Consulte a [Introdução ao Armazenamento de Tabelas do Azure usando o .NET](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) para obter mais informações.

Alguns dos snippets de código mostram o atributo **Table** usado nas funções que são chamadas manualmente ou seja, que não usam um dos atributos de gatilho.

## <a name="how-to-add-entities-to-a-table"></a>Como adicionar entidades a uma tabela
Para adicionar entidades a uma tabela, use o atributo **Table** com um **parâmetro\<ICollector t >** ou **\<IAsyncCollector t >** em que **t** especifica o esquema das entidades que você deseja adicionar. O construtor de atributo tem um parâmetro de cadeia que especifica o nome da tabela.

O exemplo de código a seguir adiciona entidades **Person** a uma tabela denominada *Ingress*.

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() {
                        PartitionKey = "Test",
                        RowKey = i.ToString(),
                        Name = "Name" }
                    );
            }
        }

Normalmente, o tipo que você usa com **ICollector** deriva de **TableEntity** ou implementa **ITableEntity**, mas isso não é necessário. Qualquer uma das seguintes classes **Person** funciona com o código mostrado no método **Ingress** anterior.

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

Se quiser trabalhar diretamente com a API de armazenamento do Azure, você pode adicionar um parâmetro **CloudStorageAccount** à assinatura do método.

## <a name="real-time-monitoring"></a>Monitoramento em tempo real
Como as funções de entrada de dados geralmente processam grandes volumes de dados, o painel do SDK de Trabalhos Web fornece dados de monitoramento em tempo real. A seção **Log de Invocação** informa se a função ainda está em execução.

![Função de entrada em execução](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

A página **Detalhes de Invocação** relata o progresso da função (número de entidades gravadas) enquanto ela está em execução e lhe dá a oportunidade de anulá-la.

![Função de entrada em execução](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Quando a função é concluída, a página **Detalhes de Invocação** relata o número de linhas gravadas.

![Função de entrada concluída](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Como ler várias entidades de uma tabela
Para ler uma tabela, use o atributo **Table** com um **parâmetro\<IQueryable T >** em que o tipo **t** deriva de **TableEntity** ou implementa **ITableEntity**.

O seguinte exemplo de código lê e registra em log todas as linhas da tabela **Ingress** :

        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### <a name="how-to-read-a-single-entity-from-a-table"></a>Como ler uma única entidade de uma tabela
Há um construtor de atributo **Table** com dois parâmetros adicionais que permite especificar a chave de partição e a chave de linha quando você deseja associar a uma entidade de tabela única.

O seguinte exemplo de código lê uma linha de tabela para uma entidade **Person** com base nos valores de chave de partição e chave de linha recebidos em uma mensagem de fila:  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


A classe **Person** neste exemplo não precisa implementar **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Como usar a API de Armazenamento .NET diretamente para trabalhar com uma tabela
Você também pode usar o atributo **Table** com um objeto **CloudTable** para ter mais flexibilidade ao trabalhar com uma tabela.

O exemplo de código a seguir usa um objeto **CloudTable** para adicionar uma única entidade à tabela *Ingress* .

        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

Para saber mais sobre como usar o objeto **CloudTable** , consulte [Como usar o Armazenamento de Tabelas do Azure usando o .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Tópicos relacionados abordados no artigo de instruções sobre filas
Para obter informações sobre como lidar com o processamento de tabelas acionado por uma mensagem da fila ou para cenários do SDK de WebJobs não específicos do processamento de tabelas, consulte [Introdução ao Armazenamento de Filas do Azure e aos serviços conectados do Visual Studio (Projetos do WebJob)](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Próximas etapas
Este artigo forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com tabelas do Azure. Para obter mais informações sobre como usar o Azure WebJobs e o SDK do WebJobs, consulte [Recursos de documentação do Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

