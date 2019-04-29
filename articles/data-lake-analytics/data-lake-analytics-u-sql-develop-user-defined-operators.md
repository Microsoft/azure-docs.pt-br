---
title: Desenvolver UDOs (operadores U-SQL definidos pelo usuário) no Azure Data Lake Analytics
description: Saiba como desenvolver operadores definidos pelo usuário para serem usados e reutilizados em trabalhos do Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 122a4b6af78a22f74d5057da75767077f8d9b978
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813803"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Desenvolver UDOs (operadores definidos pelo usuário) do U-SQL
Este artigo descreve como desenvolver operadores definidos pelo usuário para processar dados em um trabalho do U-SQL.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definir e usar o operador definido pelo usuário no U-SQL
**Para criar e enviar um trabalho do U-SQL**

1. No Visual Studio, selecione **Arquivo > Novo > Projeto > Projeto U-SQL**.
2. Clique em **OK**. O Visual Studio cria uma solução com um arquivo Script.usql.
3. No **Gerenciador de Soluções**, expanda Script.usql e clique duas vezes em **Script.usql.cs**.
4. Cole o seguinte código no arquivo:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;

        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Suisse", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };

                public override IRow Process(IRow input, IUpdatableRow output)
                {

                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");

                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);

                    return output.AsReadOnly();
                }
            }
        }
6. Abra o **Script.usql** e cole o seguinte script U-SQL:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);

        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    

        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);
7. Especifique a conta da Análise do Data Lake, o Banco de Dados e o Esquema.
8. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Script.usql** e clique em **Criar Script**.
9. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Script.usql** e clique em **Enviar Script**.
10. Se você ainda não tiver se conectado à sua assinatura do Azure, será solicitado a inserir as credenciais de conta do Azure.
11. Clique em **Enviar**. Os resultados do envio e o link do trabalho estarão disponíveis na janela Resultados quando o envio for concluído.
12. Clique no botão **Atualizar** para ver o status do trabalho mais recente e atualizar a tela.

**Para ver a saída**

1. No **Gerenciador de Servidores**, expanda **Azure**, expanda **Data Lake Analytics**, expanda sua conta do Data Lake Analytics, expanda **Contas de Armazenamento**, clique com o botão direito do mouse no Armazenamento Padrão e clique em **Explorer**.
2. Expanda Exemplos, expanda Saídas e clique duas vezes em **Drivers.csv**.

## <a name="see-also"></a>Consulte também
* [Estendendo expressões U-SQL com o código de usuário](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Usar as Ferramentas do Data Lake para Visual Studio para desenvolver aplicativos do U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
