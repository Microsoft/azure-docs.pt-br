---
title: Provisionar a produtividade do Azure Cosmos DB | Microsoft Docs
description: "Saiba como definir a produtividade provisionada para contêineres, coleções, gráficos e tabelas do Azure Cosmos DB."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: mimig
ms.openlocfilehash: d541bb19ba7e5ecb44c9fe91b1e232d4d9c2170e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-throughput-for-azure-cosmos-db-containers"></a>Definir a produtividade de contêineres do Azure Cosmos DB

Você pode definir a produtividade dos contêineres do Azure Cosmos DB no portal do Azure ou usando os SDKs do cliente. 

A tabela a seguir lista a produtividade disponível para cada contêiner:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Contêiner de partição única</strong></p></td>
            <td valign="top"><p><strong>Contêiner particionado</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Produtividade mínima</p></td>
            <td valign="top"><p>400 unidades de solicitação por segundo</p></td>
            <td valign="top"><p>2.500 unidades de solicitação por segundo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Produtividade máxima</p></td>
            <td valign="top"><p>10.000 unidades de solicitação por segundo</p></td>
            <td valign="top"><p>Ilimitado</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Para definir a taxa de transferência usando o portal do Azure

1. Em uma nova janela, abra o [portal do Azure](https://portal.azure.com).
2. Na barra esquerda, clique em **Azure Cosmos DB** ou em **Mais Serviços** na parte inferior, role até **Bancos de dados** e, depois, clique em **Azure Cosmos DB**.
3. Selecione sua conta do Cosmos DB.
4. Na nova janela, clique em **Data Explorer (Versão prévia)** no menu de navegação.
5. Na nova janela, expanda o banco de dados e o contêiner e, em seguida, clique em **Dimensionamento e Configurações**.
6. Na nova janela, digite o novo valor de produtividade na caixa **Produtividade** e, em seguida, clique em **Salvar**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-documentdb-api-for-net"></a>Para definir a produtividade usando a API do DocumentDB do .NET

```C#
//Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

//Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

## <a name="throughput-faq"></a>Perguntas frequentes sobre taxa de transferência

**Posso definir minha taxa de transferência com valor menor que 400 RU/s?**

400 RU/s é a produtividade mínima disponível em coleções de partição única do Cosmos DB (2500 RU/s é o mínimo para coleções particionadas). Unidades de solicitação são definidas em intervalos de 100 RU/s, mas a taxa de transferência não pode ser definida como 100 RU/s ou qualquer valor menor que 400 RU/s. Se você estiver procurando um método econômico para desenvolver e testar o Cosmos DB, poderá usar o [Emulador do Azure Cosmos DB](local-emulator.md) gratuito, que pode ser implantado localmente sem custo adicional. 

**Como defino a produtividade usando a API do MongoDB?**

Não há nenhuma extensão de API do MongoDB para definir a produtividade. A recomendação é usar a API do DocumentDB, conforme mostrado em [Para definir a produtividade usando a API do DocumentDB para .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o provisionamento e atingir uma escala mundial com o Cosmos DB, consulte [Particionamento e escala com o Cosmos DB](partition-data.md).
