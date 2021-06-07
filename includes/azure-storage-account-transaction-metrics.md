---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ac91ef5a56fe234cba47b430b78e74ce81c9d504
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96536946"
---
O Armazenamento do Azure fornece as seguintes métricas de transação no Azure Monitor.

| Métrica | Descrição |
| ------------------- | ----------------- |
| Transactions | O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: ResponseType, GeoType, ApiName e autenticação ([definição](#metrics-dimensions))<br/> Exemplo de valor: 1024 |
| Entrada | A quantidade de dados de entrada. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| Saída | A quantidade de dados de saída. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| SuccessServerLatency | O tempo médio usado para processar uma solicitação bem-sucedida pelo Armazenamento do Azure. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency. <br/><br/> Unidade: Milissegundos <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| SuccessE2ELatency | A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta. A diferença entre os valores SuccessE2ELatency e SuccessServerLatency é a latência provavelmente causada pela rede e pelo cliente.<br/><br/> Unidade: Milissegundos <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| Disponibilidade | O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor total de solicitações faturáveis e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada. <br/><br/> Unidade: Porcentagem <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 99,99 |
