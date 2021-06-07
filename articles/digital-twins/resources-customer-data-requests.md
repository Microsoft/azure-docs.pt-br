---
title: Recursos de solicitação de dados do cliente para o gêmeos digital do Azure
titleSuffix: Azure Digital Twins
description: Este artigo mostra processos para exportar e excluir dados pessoais no Azure digital gêmeos.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: digital-twins
services: digital-twins
ms.openlocfilehash: 9942056e59a46c61b4e9da32aab191d4b72db968
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92461752"
---
# <a name="azure-digital-twins-customer-data-request-features"></a>Recursos de solicitação de dados do cliente do Azure digital gêmeos

O Azure digital gêmeos é uma plataforma de desenvolvedor para criar representações digitais seguras de um ambiente de negócios. As representações são controladas por dados de estado dinâmicos de fontes de dados selecionadas por usuários.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

As representações digitais chamadas *digital gêmeos* no Azure digital gêmeos representam entidades em ambientes do mundo real e são associadas a identificadores. A Microsoft não mantém nenhuma informação e não tem acesso aos dados que permitiriam que os identificadores fossem correlacionados aos usuários. 

Muitas das gêmeos digitais no Azure digital gêmeos não representam diretamente entidades pessoais – objetos típicos representados podem ser uma sala de reunião do Office ou um piso de fábrica. No entanto, os usuários podem considerar que algumas entidades sejam identificáveis pessoalmente e, a seu critério, podem manter seus próprios métodos de ativos ou de acompanhamento de estoque que unem gêmeos digitais a indivíduos. O Azure digital gêmeos gerencia e armazena todos os dados associados ao gêmeos digital como se fossem dados pessoais.

Para exibir, exportar e excluir dados pessoais que podem ser referenciados em uma solicitação de entidade de dados, um administrador de gêmeos digital do Azure pode usar o [**portal do Azure**](https://portal.azure.com/) para usuários e funções ou as [**APIs REST do Azure digital gêmeos**](/rest/api/azure-digitaltwins/) para gêmeos digital. As APIs portal do Azure e REST fornecem métodos diferentes para os usuários atenderem a tais solicitações de entidade de dados.

## <a name="identifying-customer-data"></a>Identificando os dados do cliente

O Azure digital gêmeos considera os *dados pessoais* associados aos seus administradores e usuários. 

O Azure digital gêmeos armazena a *ID de objeto* [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) de usuários com acesso ao ambiente. O gêmeos digital do Azure no portal do Azure exibe endereços de email do usuário, mas esses endereços de email não são armazenados no Azure digital gêmeos. Eles são pesquisados dinamicamente em Azure Active Directory, usando a ID de objeto de Azure Active Directory.

## <a name="deleting-customer-data"></a>Excluindo os dados do cliente

Os administradores do gêmeos digital do Azure podem usar o portal do Azure para excluir dados relacionados aos usuários. Também é possível executar operações de exclusão em gêmeos digitais individuais usando as APIs REST do Azure digital gêmeos. Para obter mais informações sobre as APIs disponíveis, consulte a [documentação de APIs REST do Azure digital gêmeos](/rest/api/azure-digitaltwins/).

## <a name="exporting-customer-data"></a>Exportando os dados do cliente

O Azure digital gêmeos armazena dados relacionados ao gêmeos digital. Os usuários podem recuperar e exibir esses dados por meio de APIs REST e exportar esses dados usando copiar e colar. 

Os dados do cliente, incluindo funções de usuário e atribuições de função, podem ser selecionados, copiados e colados no portal do Azure. 

## <a name="links-to-additional-documentation"></a>Links para documentação adicional

Para obter uma lista completa das APIs do serviço de gêmeos digital do Azure, consulte a [documentação de APIs REST do Azure digital gêmeos](/rest/api/azure-digitaltwins/).