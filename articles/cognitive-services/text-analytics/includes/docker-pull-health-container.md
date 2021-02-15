---
title: Pull do Docker para o contêiner de integridade
titleSuffix: Azure Cognitive Services
description: Comando de pull do Docker para Análise de Texto para o contêiner de integridade
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: a0b2c9548f9c1289ae0abd61a72d7146a3bbca29
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965130"
---
Preencha e envie o [formulário de solicitação de serviços cognitivas](https://aka.ms/csgate) para solicitar acesso ao análise de texto para visualização pública de integridade.  Esse aplicativo se aplica tanto ao contêiner quanto à visualização pública da API web hospedada.
O formulário solicita informações sobre você, sua empresa e o cenário do usuário para o qual você usará o contêiner. Depois de submetida, a equipe de Serviços Cognitivos do Azure revisa o formulário para garantir que você atenda aos critérios de acesso ao registro de contêiner privado.

> [!IMPORTANT]
> * No formulário, você deve usar um endereço de email associado a uma ID de assinatura do Azure.
> * O recurso do Azure que você usa para executar o contêiner deve ter sido criado com a ID de assinatura aprovada do Azure. 
> * Verifique seu email (pastas de caixa de entrada e lixo eletrônico) para obter atualizações sobre o status do seu aplicativo da Microsoft.

Depois de aprovado, você receberá um email com as credenciais para acessar o registro de contêiner privado.  Use o comando Docker login com credenciais fornecidas em seu email de integração para se conectar ao nosso registro de contêiner privado para contêineres de serviços cognitivas.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar essa imagem de contêiner do nosso registro de contêiner privado.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
