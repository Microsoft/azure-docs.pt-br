---
title: Recursos da Central de Segurança do Azure para recursos compatíveis do PaaS do Azure.
description: Esta página mostra a disponibilidade dos recursos da Central de Segurança do Azure para os recursos compatíveis do PaaS do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 4c56f8cba232220844a8a8f9c408a60c89c581cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91613621"
---
# <a name="feature-coverage-for-azure-paas-services"></a>Cobertura de recursos para os serviços do PaaS do Azure <a name="paas-services"></a>

A tabela abaixo mostra a disponibilidade dos recursos da Central de Segurança do Azure para os recursos compatíveis do PaaS do Azure.

|Serviço|Recomendações (Gratuito)|Alertas de segurança (Azure Defender)|Avaliação de vulnerabilidades (Azure Defender)|
|:----|:----:|:----:|:----:|
|Serviço de aplicativo do Azure|✔|✔|-|
|Conta de Automação do Azure|✔|-|-|
|Conta do Lote do Azure|✔|-|-|
|Armazenamento do Blobs do Azure|✔|✔|-|
|Cache Redis do Azure|✔|-|-|
|Serviços de nuvem do Azure|✔|-|-|
|Pesquisa Cognitiva do Azure|✔|-|-|
|Registro de Contêiner do Azure|-|-|✔|
|Azure Cosmos DB*|-|✔|-|
|Análise Azure Data Lake|✔|-|-|
|Armazenamento do Azure Data Lake|✔|✔|-|
|Banco de Dados do Azure para MySQL*|-|✔|-|
|Banco de Dados do Azure para PostgreSQL*|-|✔|-|
|Namespace dos Hubs de Eventos do Azure|✔|-|-|
|Aplicativo Azure Functions|✔|-|-|
|Cofre de Chave do Azure|✔|✔|-|
|Serviço de Kubernetes do Azure|✔|✔|-|
|Azure Load Balancer|✔|-|-|
|Aplicativos Lógicos do Azure|✔|-|-|
|Banco de Dados SQL do Azure|✔|✔|✔|
|Instância Gerenciada do Azure SQL|✔|✔|✔|
|Namespace do Barramento de Serviço do Azure|✔|-|-|
|Conta do Azure Service Fabric|✔|-|-|
|Contas de armazenamento do Azure|✔|✔|-|
|Stream Analytics do Azure|✔|-|-|
|Assinatura do Azure|✔ **|✔|-|
|Rede Virtual do Azure</br> (incluindo sub-redes, NICs e grupos de segurança de rede)|✔|-|-|

\* Atualmente, esses recursos são compatíveis na versão prévia.

\*\* As recomendações do Azure AD (Azure Active Directory) estão disponíveis somente para assinaturas com o Azure Defender habilitado.