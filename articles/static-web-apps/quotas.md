---
title: Cotas na Versão Prévia dos Aplicativos Web Estáticos do Azure
description: Saiba mais sobre as cotas associadas à Versão Prévia dos Aplicativos Web Estáticos do Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 915f8675ffda0d70347905d11c7d93975b9d4526
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132730"
---
# <a name="quotas-in-azure-static-web-apps-preview"></a>Cotas na Versão Prévia dos Aplicativos Web Estáticos do Azure

As seguintes cotas existem para Versão Prévia dos Aplicativos Web Estáticos do Azure.

> [!IMPORTANT]
> Os Aplicativos Web Estáticos do Azure estão na versão prévia pública e não se destinam ao uso em produção.

| Recurso                     | Plano gratuito        |
|-----------------------------|------------------|
| Largura de banda incluída          | 100 GB por mês |
| Largura de banda excedente           | Indisponível      |
| Aplicativos por assinatura do Azure | 10               |
| Tamanho do aplicativo                    | 250 MB           |
| Ambientes de pré-produção | 1                |
| Domínios personalizados              | 1                |
| Autorização<br><br>Com funções personalizadas e regras de roteamento | Máximo de 25 usuários finais convidados e funções atribuídas |
| Funções do Azure             | Disponível        |
| Contrato de Nível de Serviço                         | Nenhum             |

## <a name="github-storage"></a>Armazenamento do GitHub

As GitHub Actions e os Pacotes usam o Armazenamento do GitHub que tem seu próprio conjunto de cotas. Quando você cria um site de Aplicativos Web Estáticos do Azure, o GitHub armazena os artefatos para seu site mesmo após a implantação.

Veja os seguintes recursos para obter mais detalhes:

- [Gerenciando o espaço de armazenamento de Actions](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [Sobre a cobrança das GitHub Actions](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [Gerenciando seu limite de gastos das GitHub Actions](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>Próximas etapas

- [Visão geral](overview.md)
