---
title: "Explicando Unidades de Computação no Banco de Dados do Azure para MySQL | Microsoft Docs"
description: "Banco de dados do Azure para MySQL: este artigo explica o conceito de Unidades de Computação e o que acontece quando sua carga de trabalho atinge o máximo de Unidades de Computação."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 2c4894ae9a4235f9ced4a8d9b991238543646f53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="explaining-compute-units-in-azure-database-for-mysql"></a>Explicando Unidades de Computação no Banco de Dados do Azure para MySQL
Este tópico explica o conceito de Unidades de Computação e o que acontece quando sua carga de trabalho atinge o máximo de Unidades de Computação.

## <a name="what-are-compute-units"></a>O que são unidades de computação?
Unidades de computação são uma medida de taxa de transferência de processamento da CPU que possuem disponibilidade garantida para um único Banco de Dados do Azure para o servidor MySQL. Uma unidade de computação é uma medida combinada de recursos de CPU e memória. Em geral, 50 Unidades de Computação equivalem a metade de um núcleo. Cem Unidades de Computação equivalem a um núcleo. 2.000 Unidades de Computação equivalem a 20 núcleos de produtividade de processamento garantida disponível para o servidor.

A quantidade de memória por Unidade de Computação é otimizada para os tipos de preço Basic e Standard. Dobrar as Unidades de Computação aumentando o nível de desempenho equivale a dobrar o conjunto de recursos disponíveis para esse Banco de Dados do Azure único para MySQL.

Por exemplo, um tipo Standard de 800 Unidades de Computação fornece 8 vezes mais taxa de transferência de CPU e memória que uma configuração Standard com 100 Unidades de Computação. No entanto, apesar das 100 Unidades de Computação do tipo Standard fornecerem a mesma taxa de transferência de CPU em comparação com 100 Unidades de Computação da tipo Basic, a quantidade de memória que é pré-configurada no tipo de preço Standard é o dobro da quantidade de memória configurada para o tipo de preço Basic. Portanto, o tipo de preço Standard fornece um melhor desempenho de carga de trabalho e menor latência de transação que o tipo de preço Basic com as mesmas Unidades de Computação selecionadas.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>Como posso determinar o número de unidades de computação necessárias para a minha carga de trabalho?
Se você quiser migrar um servidor MySQL local ou em uma máquina virtual, poderá determinar o número de Unidades de Computação estimando quantos núcleos de taxa de transferência de processamento são necessários para a sua carga de trabalho. 

Se o seu servidor local ou de máquina virtual existente estiver utilizando 4 núcleos (sem contar o hiperthread da CPU), comece configurando 400 Unidades de Computação para o seu Banco de Dados do Azure para o servidor MySQL. É possível aumentar ou diminuir as unidades de computação dinamicamente conforme as suas necessidades de carga de trabalho sem praticamente nenhum tempo de inatividade do aplicativo. 

Monitorar o gráfico de métricas no Portal do Azure ou gravar comandos de CLI do Azure para medir as Unidades de Computação. As métricas relevantes para monitorar são a porcentagem de Unidade de Computação e o limite de Unidade de Computação.

>[!IMPORTANT]
> Se você achar que não está usando a capacidade máxima do IOPS de armazenamento, monitore também a utilização de Unidades de Computação. Aumentar as Unidades de Computação pode permitir maior taxa de transferência de E/S, diminuindo o afunilamento de desempenho em virtude de limites de CPU ou memória.

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>O que acontece quando eu atinjo o máximo de Unidades de Computação?
Os níveis de desempenho são calibrados e controlados para fornecer os recursos para executar sua carga de trabalho de banco de dados até os limites máximos para o tipo de preços e o nível de desempenho selecionados. 

Se a sua carga de trabalho atingir os limites máximos de Unidades de Computação ou IOPS provisionado, você continuará a utilizar os recursos no nível máximo permitido, mas provavelmente suas consultas apresentarão latências maiores. Esses limites não resultam em erros, mas apenas em uma lentidão na carga de trabalho, a menos que a lentidão se torne tão grave que as consultas atinjam o tempo limite. 

Se sua carga de trabalho atingir os limites máximo no número de conexões, erros explícitos serão gerados. Para saber mais sobre os limites de recursos, veja [Limites no Banco de Dados do Azure para MySQL](concepts-limits.md).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre tipos de preço, confira [Tipos de preço do Banco de Dados do Azure para MySQL](./concepts-service-tiers.md).
