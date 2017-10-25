---
title: Listar recursos de Armazenamento do Azure com a Biblioteca de Cliente de Armazenamento para C++ | Microsoft Docs
description: "Saiba como usar a APIs de listagem na Biblioteca de Cliente de Armazenamento do Microsoft Azure para C++ para enumerar contêineres, blobs, filas, tabelas e entidades."
documentationcenter: .net
services: storage
author: dineshmurthy
manager: jahogg
editor: tysonn
ms.assetid: 33563639-2945-4567-9254-bc4a7e80698f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: dineshm
ms.openlocfilehash: 9844412739f4f6f95416f81347f0f2eeeca62bea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="list-azure-storage-resources-in-c"></a>Listar recursos do Armazenamento do Azure no C++
As operações de listagem são fundamentais para muitos cenários de desenvolvimento com o Armazenamento do Azure. Este artigo descreve como enumerar os objetos no Armazenamento do Azure usando a listagem APIs fornecidas na Biblioteca de Cliente do Armazenamento do Microsoft Azure para C++ com mais eficiência.

> [!NOTE]
> Este guia destina-se à Biblioteca de Cliente do Armazenamento do Azure para C++ versão 2.x, que está disponível via [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).
> 
> 

A Biblioteca de Cliente do Armazenamento fornece uma variedade de métodos de consulta ou lista de objetos no Armazenamento do Azure. Este artigo aborda os seguintes cenários:

* Listar contêineres em uma conta
* Listar blobs em um contêiner ou diretório virtual de blob
* Listar filas em uma conta
* Lista de tabelas em uma conta
* Consultar entidades em uma tabela

Cada um desses métodos é mostrado usando sobrecargas diferentes para diferentes cenários.

## <a name="asynchronous-versus-synchronous"></a>Assíncrono versus síncrono
Como a Biblioteca de Cliente de Armazenamento para C++ é construída sobre a [Biblioteca C++ REST](https://github.com/Microsoft/cpprestsdk), nós oferecemos inerentemente suporte a operações assíncronas usando [pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Por exemplo:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Operações síncronas quebram as operações assíncronas correspondentes:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Se você estiver trabalhando com vários aplicativos ou serviços de threading, recomendamos usar as APIs assíncronas diretamente, em vez de criar um thread para chamar a APIs de sincronização, o que afeta significativamente o desempenho.

## <a name="segmented-listing"></a>Listagem segmentada
A escala de armazenamento em nuvem requer uma listagem segmentada. Por exemplo, você pode ter em um milhão de blobs em um contêiner de blob do Azure ou mais de um bilhão de entidades em uma Tabela do Azure. Esses não são números teóricos, são casos reais de uso de clientes.

Portanto, é impraticável listar todos os objetos em uma única resposta. Em vez disso, você pode listar objetos usando a paginação. Cada lista de APIs tem uma sobrecarga *segmentada* .

A resposta para uma operação de listagem segmentada inclui:

* <i>_segment</i>, que contém o conjunto de resultados retornado para uma única chamada à API de listagem.
* *continuation_token*, que é passado para a próxima chamada para obter a próxima página de resultados. Quando não há mais nenhum resultados para retornar, o token de continuação é nulo.

Por exemplo, uma chamada típica para listar todos os blobs em um contêiner pode se parecer com o seguinte trecho de código. O código está disponível em nossos [exemplos](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_diretory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Observe que o número de resultados retornados em uma página pode ser controlado pelo parâmetro *max_results* na sobrecarga de cada API, por exemplo:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Se você não especificar o parâmetro *max_results*, o valor máximo padrão de até 5000 resultados será retornado em uma única página.

Observe também que uma consulta em relação ao Armazenamento de Tabelas do Azure pode retornar nenhum registro ou menos registros que o valor do parâmetro *max_results* especificado, mesmo se o token de continuação não estiver vazio. Uma razão pode ser que a consulta não conseguiu concluir em cinco segundos. Desde que o token de continuação não esteja vazio, a consulta deve continuar e seu código não deve presumir o tamanho dos resultados de segmento.

O padrão de codificação recomendado para a maioria dos cenários é a listagem segmentada, que fornece o progresso explícito de listagem ou consulta, e o modo como o serviço responde a cada solicitação. Especificamente para aplicativos C++ ou serviços, um controle de baixo nível do progresso da listagem pode ajudar a controlar a memória e o desempenho.

## <a name="greedy-listing"></a>Listagem greedy
As versões anteriores da Biblioteca de Cliente de Armazenamento do C++ (versões 0.5.0 Preview e anteriores) incluíam APIs de listagem não segmentadas para tabelas e filas, como no exemplo a seguir:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Esses métodos foram implementados como wrappers de APIs segmentadas. Para cada resposta de listagem segmentada, o código adicionava os resultados a um vetor e retornava todos os resultados depois que contêineres cheios eram examinados.

Essa abordagem pode funcionar quando a conta de armazenamento ou a tabela contém um pequeno número de objetos. No entanto, com o aumento do número de objetos, a memória necessária pode aumentar sem limite, porque todos os resultados permaneceram na memória. Uma operação de listagem pode levar muito tempo, durante o qual o chamador não tinha nenhuma informação sobre seu progresso.

Essas APIs de listagem greedy no SDK não existem em ambientes C#, Java ou JavaScript Node.j. Para evitar possíveis problemas de uso dessas APIs greedy, nós as removemos da versão 0.6.0 Preview.

Se o seu código estiver chamando essas APIs greedy:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Você deve modificar o código para usar as APIs de listagem segmentada:

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

Ao especificar o parâmetro *max_results* do segmento, você pode equilibrar entre os números de solicitações e uso de memória para atender às considerações de desempenho do seu aplicativo.

Além disso, se você estiver usando APIs de listagem segmentada, mas armazena os dados em uma coleção local em um estilo "greedy", também recomendamos refatorar seu código para manipular o armazenamento de dados em uma coleção local cuidadosamente em grande escala.

## <a name="lazy-listing"></a>Listagem lenta
Embora a listagem greedy gerasse possíveis problemas, ela é conveniente se não houver muitos objetos no contêiner.

Se também estiver usando SDKs C# ou Oracle Java, você deverá conhecer o modelo de programação Enumerável, que oferece uma listagem do estilo lazy, na qual os dados em um determinado deslocamento são buscados somente se for necessário. Em C++, o modelo com base em iterador também fornece uma abordagem semelhante.

Uma API típica de listagem lenta, que usa **list_blobs** por exemplo é semelhante ao seguinte:

```cpp
list_blob_item_iterator list_blobs() const;
```

Um trecho de código típico que usa o padrão de listagem lenta pode ter esta aparência:

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

Observe que a listagem lenta só está disponível no modo síncrono.

A listagem lenta, em comparação com a listagem greedy, busca dados somente quando necessário. Nos bastidores, ela busca dados do Armazenamento do Azure apenas quando o próximo iterador passa para o próximo segmento. Portanto, o uso de memória é controlado com um tamanho limitado e a operação é rápida.

As APIs de listagem lenta estão incluídas na Biblioteca de Cliente de Armazenamento do C++ na versão 2.2.0.

## <a name="conclusion"></a>Conclusão
Neste artigo, discutimos diferentes sobrecargas para listar as APIs de vários objetos na Biblioteca de Cliente de Armazenamento do C++. Resumidamente:

* As APIs assíncronas são altamente recomendadas em vários cenários de threads.
* A listagem segmentada é recomendada para a maioria dos cenários.
* A listagem lenta é fornecida na biblioteca como um wrapper conveniente em cenários síncronos.
* Listagem greedy não é recomendada e foi removida da biblioteca.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o Armazenamento do Azure e a Biblioteca de Cliente para C++, consulte os seguintes recursos.

* [Como usar o Armazenamento de Blobs do C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Como usar o Armazenamento de Tabelas do C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Como usar o Armazenamento de Filas do C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Documentação Biblioteca de Cliente de Armazenamento do Azure para API do C++.](http://azure.github.io/azure-storage-cpp/)
* [Blog da equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)

