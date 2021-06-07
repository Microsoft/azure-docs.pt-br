---
title: incluir arquivo
description: incluir arquivo
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: b34d3ed2481600cde7ffb3275d635b8a57926bd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "83665944"
---
As seguintes funções são fornecidas para colaboração:

|Função|Funcionalidades|Acesso à API|Permissões de API|
|--|--|--|--|
|Proprietário|Todos|Chave de autenticação|Todos|
|Colaborador|Tudo, exceto a capacidade de adicionar novos membros a funções|Chave de autenticação|Tudo, exceto a capacidade de adicionar novos membros a funções|
|QnA Maker leitura<br>leitura|Exportar/baixar<br>Teste|Token de portador|1. baixar a API do KB<br>2. listar KBs para a API do usuário<br>3. obter detalhes da base de dados de conhecimento<br>4. baixar alterações<br>Gerar resposta |
|Editor de QnA Maker<br>(leitura/gravação)|Exportar/baixar<br>Teste<br>Atualizar KB<br>Exportar KB<br>Importar KB<br>Substituir KB<br>Criar uma base de dados de conhecimento|Token de portador|1. criar a API do KB<br>2. atualizar a API do KB<br>3. substituir a API do KB<br>4. substituir as alterações<br>5. "treinar a API" [no novo modelo de serviço V5]|
|Usuário de serviço cognitiva<br>(leitura/gravação/publicação)|Todos|Token de portador|Todos|