---
title: Usar o Power BI com o SQL Data Warehouse | Microsoft Docs
description: "Dicas para usar o Power BI com o SQL Data Warehouse do Azure para desenvolvimento de soluções."
services: sql-data-warehouse
documentationcenter: NA
author: mlee3gsd
manager: jhubbard
editor: 
ms.assetid: b12bee87-2268-40c2-81bf-ab27588b32e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: martinle;barbkess
ms.openlocfilehash: 4b7609fc5d6ce7bf0e3bd3ebf6d8f52e93a40a75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-power-bi-with-sql-data-warehouse"></a>Usar o Power BI com o SQL Data Warehouse
Assim como o Banco de Dados SQL do Azure, a Conexão Direta do SQL Data Warehouse permite ao usuário aproveitar a aplicação lógica ao longo dos recursos analíticos do Power BI.  Com a Conexão Direta, as consultas são enviadas de volta ao SQL Data Warehouse do Azure em tempo real enquanto você explora os dados.  Esse recurso, combinado com a escala do SQL Data Warehouse, permite aos usuários criar relatórios dinâmicos dos terabytes de dados em questão de minutos.  Além disso, a introdução do botão Abrir no Power BI permite que os usuários conectem o Power BI diretamente ao respectivo SQL Data Warehouse sem coletar informações de outras partes do Azure.

Ao usar a Conexão Direta:

* Especifique o nome de servidor totalmente qualificado ao conectar (veja mais detalhes abaixo)
* Certifique-se de que as regras de firewall estejam configuradas como “Permitir acesso aos serviços do Azure”.
* Cada ação, como selecionar uma coluna ou adicionar um filtro, consultará diretamente o data warehouse
* Os blocos são atualizados aproximadamente a cada 15 minutos (a atualização não precisa ser agendada)
* Perguntas e respostas não estão disponíveis para conjuntos de dados de Conexão Direta
* As alterações no esquema não são selecionadas automaticamente
* Todas as consultas de Conexão Direta atingirão o tempo limite após 2 minutos

Essas restrições e observações podem mudar à medida que aprimoramos as experiências. As etapas para conexão estão detalhadas abaixo.  

## <a name="using-the-open-in-power-bi-button"></a>Usando o botão ‘Abrir no Power BI’
A maneira mais fácil de mover entre o SQL Data Warehouse e o Power BI é usando o botão Abrir no Power BI. Esse botão permite que você comece a criar novos painéis diretamente no Power BI.  

1. Para começar, navegue até sua instância do SQL Data Warehouse no Portal clássico do Azure.
2. Clique no botão “Abrir no Power BI”.
3. Se não pudermos conectar você diretamente ou se você não tiver uma conta do Power BI, será preciso se conectar.  
4. Você será direcionado para a página de conexão do SQL Data Warehouse, com as informações do seu SQL Data Warehouse previamente populadas.
5. Depois de inserir suas credenciais, você será totalmente conectado ao SQL Data Warehouse.

## <a name="connecting-through-the-power-bi-portal"></a>Conectando-se pelo portal do Power BI
Além de usar o botão Abrir no Power BI, os usuários também podem se conectar ao respectivo SQL Data Warehouse pelo Portal do Power BI.

1. Clique em 'Obter dados' na parte inferior do painel de navegação.
2. Selecione 'Bancos de dados'.
3. Uma vez na página Bancos de dados, selecione 'SQL Data Warehouse do Azure' e, em seguida, clique em 'Conectar'.
4. Insira as informações de conexão necessárias.  O nome do servidor e o nome do banco de dados podem ser encontrados no Portal do Azure.
5. Você será direcionado para a página principal do Power BI e, depois que a conexão é feita, uma nova entrada em 'Conjuntos de dados' aparecerá com o nome da sua instância.  
6. Você pode clicar no novo conjunto de dados para explorar todas as tabelas e exibições no seu banco de dados. Selecionar uma coluna enviará uma consulta de volta à origem, criando dinamicamente seu visual. Esses visuais podem ser salvos em um novo relatório e fixados de volta no seu painel.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
