---
title: Funções RBAC do Synapse
description: Este artigo descreve as funções de RBAC Synapse internas
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 35f66732fa9cb48b94f80bab203534c9d04b7a7b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102114"
---
# <a name="synapse-rbac-roles"></a>Funções RBAC Synapse

O artigo descreve as funções de RBAC Synapse internas, as permissões que elas concedem e os escopos nos quais elas podem ser usadas.  

## <a name="whats-changed-since-the-preview"></a>O que mudou desde a versão prévia?
Para os usuários familiarizados com as funções RBAC Synapse fornecidas durante a visualização, as seguintes alterações se aplicam:
- O administrador do espaço de trabalho foi renomeado como **Administrator Synapse**
- Apache Spark administrador é renomeado como **Synapse Apache Spark administrador** e tem permissão para ver todos os artefatos de código publicados, incluindo scripts SQL.  Essa função não dá mais permissão para usar o MSI do espaço de trabalho, que requer a função de usuário de credencial Synapse.  Essa permissão é necessária para executar pipelines. 
- O administrador do SQL foi renomeado como **administrador do SQL Synapse** e tem permissão para ver todos os artefatos de código publicados, incluindo notebooks e trabalhos do Spark.  Essa função não dá mais permissão para usar o MSI do espaço de trabalho, que requer a função de usuário de credencial Synapse. Essa permissão é necessária para executar pipelines.
- **Novas funções RBAC de Synapse mais refinadas** são introduzidas que concentram-se no suporte às pessoas de desenvolvimento e operações em vez de tempos de execução de análise específicos.  
- **Novos escopos de nível inferior** são introduzidos para várias funções.  Esses escopos permitem que as funções sejam restritas a recursos ou objetos específicos.

>[!Note]
>As **novas funções RBAC Synapse e os escopos de nível inferior estão atualmente em visualização**.  Você é incentivado a usar essas novas funções e escopos, que têm suporte total e para fornecer comentários sobre seu uso.

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>Funções e escopos Synapse RBAC internos

A tabela a seguir descreve as funções internas e os escopos nos quais elas podem ser usadas.

>[!Note]
> Os usuários com qualquer função de RBAC Synapse em qualquer escopo têm automaticamente a função de usuário Synapse no escopo do espaço de trabalho. 

|Função |Permissões|Escopos|
|---|---|-----|
|Administrador do Azure Synapse  |Acesso completo a Synapse a pools SQL sem servidor, pools de Apache Spark e tempos de execução de integração.  Inclui criar, ler, atualizar e excluir o acesso a todos os artefatos de código publicados.  Inclui permissões de operador de computação, Gerenciador de Dados vinculadas e de usuário de credencial na credencial de identidade do sistema do espaço de trabalho.  Inclui a atribuição de funções RBAC Synapse. Além do administrador do Synapse, os proprietários do Azure também podem atribuir funções RBAC Synapse. As permissões do Azure são necessárias para criar, excluir e gerenciar recursos de computação. </br></br>_Pode ler e gravar artefatos </br> pode fazer todas as ações em atividades do Spark. </br> Pode exibir os logs do pool do Spark </br> pode exibir o bloco de anotações salvo e </br> a saída do pipeline pode usar os segredos armazenados por serviços vinculados ou as credenciais </br> podem se conectar a pontos de extremidade sem SQL Server com `db_datareader` as permissões SQL, `db_datawriter` , `connect` , e `grant` </br> podem atribuir e revogar funções RBAC Synapse no escopo atual_|Workspace </br> Pool do Spark<br/>runtime de integração </br>Serviço vinculado</br>Credencial |
|Administrador de Apache Spark de Synapse</br>|Acesso total de Synapse a pools de Apache Spark.  Crie, leia, atualize e exclua o acesso a definições de trabalho do Spark, blocos de anotações e suas saídas publicados, além de bibliotecas, serviços vinculados e credenciais.  Inclui acesso de leitura a todos os outros artefatos de código publicados. Não inclui permissão para usar credenciais e executar pipelines. Não inclui concessão de acesso. </br></br>_Pode fazer todas as ações em artefatos do Spark </br> pode fazer todas as ações em atividades do Spark_|Workspace</br>Pool do Spark|
|Administrador do SQL do Synapse|Acesso total a Synapse a pools SQL sem servidor.  Criar, ler, atualizar e excluir o acesso a scripts, credenciais e serviços vinculados do SQL publicados.  Inclui acesso de leitura a todos os outros artefatos de código publicados.  Não inclui permissão para usar credenciais e executar pipelines. Não inclui concessão de acesso. </br></br>*Pode fazer todas as ações em scripts SQL <br/> pode se conectar a pontos de extremidade sem SQL Server com `db_datareader` permissões SQL, `db_datawriter` , `connect` e `grant`*|Workspace|
|Colaborador de Synapse|Acesso total de Synapse a pools de Apache Spark e tempos de execução de integração. Inclui acesso de criação, leitura, atualização e exclusão a todos os artefatos de código publicados e suas saídas, incluindo credenciais e serviços vinculados.  Inclui permissões de operador de computação. Não inclui permissão para usar credenciais e executar pipelines. Não inclui concessão de acesso. </br></br>_Pode ler e gravar artefatos </br> pode exibir o bloco de anotações salvo e a saída </br> do pipeline pode fazer todas as ações em atividades do Spark </br> pode exibir logs do pool do Spark_|Workspace </br> Pool do Spark<br/> runtime de integração|
|Publicador de artefatos do Synapse|Crie, leia, atualize e exclua o acesso a artefatos de código publicados e suas saídas. Não inclui permissão para executar o código ou pipelines ou para conceder acesso. </br></br>_Pode ler artefatos publicados e publicar artefatos </br> pode exibir o bloco de anotações salvo, o trabalho do Spark e a saída do pipeline_|Workspace
|Usuário do artefato Synapse|Acesso de leitura a artefatos de código publicados e suas saídas. Pode criar novos artefatos, mas não pode publicar alterações ou executar código sem permissões adicionais.|Workspace
|Operador de computação Synapse |Envie trabalhos e blocos de anotações do Spark e exiba logs.  Inclui o cancelamento de trabalhos do Spark enviados por qualquer usuário. Requer permissões de credenciais de uso adicionais na identidade do sistema do espaço de trabalho para executar pipelines, exibir execuções de pipeline e saídas. </br></br>_Pode enviar e Cancelar trabalhos, incluindo trabalhos enviados por outras pessoas </br> podem exibir logs do pool do Spark_|Workspace</br>Pool do Spark</br>runtime de integração|
|Usuário de credencial Synapse|Tempo de execução e configuração do uso de segredos em credenciais e serviços vinculados em atividades como execuções de pipeline. Para executar pipelines, essa função é necessária, com escopo para a identidade do sistema do espaço de trabalho. </br></br>_Com escopo para uma credencial, permite o acesso aos dados por meio de um serviço vinculado que é protegido pela credencial (também requer a permissão de uso de computação) </br> permite a execução de pipelines protegidos pela credencial de identidade do sistema do espaço de trabalho (com permissão adicional de uso de computação)_|Workspace </br>Serviço vinculado</br>Credencial
|Gerenciador de Dados Synapse vinculados|Criação e gerenciamento de pontos de extremidade privados gerenciados, serviços vinculados e credenciais. Pode criar pontos de extremidade privados gerenciados que usam serviços vinculados protegidos por credenciais|Workspace|
|Usuário Synapse|Listar e exibir detalhes de pools do SQL, pools de Apache Spark, tempos de execução de integração e credenciais e serviços vinculados publicados. Não inclui outros artefatos de código publicados.  Pode criar novos artefatos, mas não pode executar ou publicar sem permissões adicionais.</br></br>_Pode listar e ler pools do Spark, tempos de execução de integração._|Espaço de trabalho, pool do Spark</br>Serviço vinculado </br>Credencial|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>Funções RBAC Synapse e as ações que elas permitem

>[!Note]
>- Todas as ações listadas nas tabelas abaixo são prefixadas, "Microsoft. Synapse/..."</br>
>- Todas as ações de leitura, gravação e exclusão de artefatos são relacionadas aos artefatos publicados no serviço ativo.  Essas permissões não afetam o acesso a artefatos em um repositório git conectado.  

A tabela a seguir lista as funções internas e as ações/as permissões que cada uma suporta.

Função|Ações
--|--
Administrador do Azure Synapse|espaços de trabalho/leitura</br>espaços de trabalho/roleAssignments/gravação, excluir</br>espaços de trabalho/managedPrivateEndpoint/gravação, excluir</br>espaços de trabalho/bigDataPools/useCompute/ação</br>espaços de trabalho/bigDataPools/viewLogs/ação</br>espaços de trabalho/integrationRuntimes/useCompute/ação</br>espaços de trabalho/artefatos/leitura</br>espaços de trabalho/blocos de anotações/gravação, excluir</br>espaços de trabalho/sparkJobDefinitions/gravação, excluir</br>espaços de trabalho/sqlscriptes/gravar, excluir</br>espaços de trabalho/Dataflows/gravação, exclusão</br>espaços de trabalho/pipelines/gravação, exclusão</br>espaços de trabalho/gatilhos/gravação, exclusão</br>espaços de trabalho/conjuntos de valores/gravação, exclusão</br>espaços de trabalho/bibliotecas/gravação, excluir</br>espaços de trabalho/linkedservices/gravar, excluir</br>espaços de trabalho/credenciais/gravação, excluir</br>espaços de trabalho/blocos de anotações/viewOutputs/ação</br>espaços de trabalho/pipelines/viewOutputs/ação</br>espaços de trabalho/linkservices/useSecret/ação</br>espaços de trabalho/credenciais/useSecret/ação|
|Administrador de Apache Spark de Synapse|espaços de trabalho/leitura</br>espaços de trabalho/bigDataPools/useCompute/ação</br>espaços de trabalho/bigDataPools/viewLogs/ação</br>espaços de trabalho/blocos de anotações/viewOutputs/ação</br>espaços de trabalho/artefatos/leitura</br>espaços de trabalho/blocos de anotações/gravação, excluir</br>espaços de trabalho/sparkJobDefinitions/gravação, excluir</br>espaços de trabalho/bibliotecas/gravação, excluir</br>espaços de trabalho/linkedservices/gravar, excluir</br>espaços de trabalho/credenciais/gravação, excluir|
|Administrador do SQL do Synapse|espaços de trabalho/leitura</br>espaços de trabalho/artefatos/leitura</br>espaços de trabalho/sqlscriptes/gravar, excluir</br>espaços de trabalho/linkedservices/gravar, excluir</br>espaços de trabalho/credenciais/gravação, excluir|
|Colaborador de Synapse|espaços de trabalho/leitura</br>espaços de trabalho/bigDataPools/useCompute/ação</br>espaços de trabalho/bigDataPools/viewLogs/ação</br>espaços de trabalho/integrationRuntimes/useCompute/ação</br>espaços de trabalho/integrationRuntimes/viewLogs/ação</br>espaços de trabalho/artefatos/leitura</br>espaços de trabalho/blocos de anotações/gravação, excluir</br>espaços de trabalho/sparkJobDefinitions/gravação, excluir</br>espaços de trabalho/sqlscriptes/gravar, excluir</br>espaços de trabalho/Dataflows/gravação, exclusão</br>espaços de trabalho/pipelines/gravação, exclusão</br>espaços de trabalho/gatilhos/gravação, exclusão</br>espaços de trabalho/conjuntos de valores/gravação, exclusão</br>espaços de trabalho/bibliotecas/gravação, excluir</br>espaços de trabalho/linkedservices/gravar, excluir</br>espaços de trabalho/credenciais/gravação, excluir</br>espaços de trabalho/blocos de anotações/viewOutputs/ação</br>espaços de trabalho/pipelines/viewOutputs/ação|
|Publicador de artefatos do Synapse|espaços de trabalho/leitura</br>espaços de trabalho/artefatos/leitura</br>espaços de trabalho/blocos de anotações/gravação, excluir</br>espaços de trabalho/sparkJobDefinitions/gravação, excluir</br>espaços de trabalho/sqlscriptes/gravar, excluir</br>espaços de trabalho/Dataflows/gravação, exclusão</br>espaços de trabalho/pipelines/gravação, exclusão</br>espaços de trabalho/gatilhos/gravação, exclusão</br>espaços de trabalho/conjuntos de valores/gravação, exclusão</br>espaços de trabalho/bibliotecas/gravação, excluir</br>espaços de trabalho/linkedservices/gravar, excluir</br>espaços de trabalho/credenciais/gravação, excluir</br>espaços de trabalho/blocos de anotações/viewOutputs/ação</br>espaços de trabalho/pipelines/viewOutputs/ação|
|Usuário do artefato Synapse|espaços de trabalho/leitura</br>espaços de trabalho/artefatos/leitura</br>espaços de trabalho/blocos de anotações/viewOutputs/ação</br>espaços de trabalho/pipelines/viewOutputs/ação|
|Operador de computação Synapse |espaços de trabalho/leitura</br>espaços de trabalho/bigDataPools/useCompute/ação</br>espaços de trabalho/bigDataPools/viewLogs/ação</br>espaços de trabalho/integrationRuntimes/useCompute/ação</br>espaços de trabalho/integrationRuntimes/viewLogs/ação|
|Usuário de credencial Synapse|espaços de trabalho/leitura</br>espaços de trabalho/linkservices/useSecret/ação</br>espaços de trabalho/credenciais/useSecret/ação|
|Gerenciador de Dados Synapse vinculados|espaços de trabalho/leitura</br>espaços de trabalho/managedPrivateEndpoint/gravação, excluir</br>espaços de trabalho/linkedservices/gravar, excluir</br>espaços de trabalho/credenciais/gravação, excluir|
|Usuário Synapse|espaços de trabalho/leitura|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Ações do RBAC Synapse e as funções que as permitem

A tabela a seguir lista as ações Synapse e as funções internas que permitem estas ações:

Ação|Função
--|--
espaços de trabalho/leitura|Administrador do Azure Synapse</br>Administrador de Apache Spark de Synapse</br>Administrador do SQL do Synapse</br>Colaborador de Synapse</br>Publicador de artefatos do Synapse</br>Usuário do artefato Synapse</br>Operador de computação Synapse </br>Usuário de credencial Synapse</br>Gerenciador de Dados Synapse vinculados</br>Usuário Synapse 
espaços de trabalho/roleAssignments/gravação, excluir|Administrador do Azure Synapse
espaços de trabalho/managedPrivateEndpoint/gravação, excluir|Administrador do Azure Synapse</br>Gerenciador de Dados Synapse vinculados
espaços de trabalho/bigDataPools/useCompute/ação|Administrador do Azure Synapse</br>Administrador de Apache Spark de Synapse</br>Colaborador de Synapse</br>Operador de computação Synapse 
espaços de trabalho/bigDataPools/viewLogs/ação|Administrador do Azure Synapse</br>Administrador de Apache Spark de Synapse</br>Colaborador de Synapse</br>Operador de computação Synapse 
espaços de trabalho/integrationRuntimes/useCompute/ação|Administrador do Azure Synapse</br>Colaborador de Synapse</br>Operador de computação Synapse 
espaços de trabalho/artefatos/leitura|Administrador do Azure Synapse</br>Administrador de Apache Spark de Synapse</br>Administrador do SQL do Synapse</br>Colaborador de Synapse</br>Publicador de artefatos do Synapse</br>Usuário do artefato Synapse
espaços de trabalho/blocos de anotações/gravação, excluir|Administrador do Azure Synapse</br>Administrador de Apache Spark de Synapse</br>Colaborador de Synapse</br>Publicador de artefatos do Synapse
espaços de trabalho/sparkJobDefinitions/gravação, excluir|Administrador do Azure Synapse</br>Administrador de Apache Spark de Synapse</br>Colaborador de Synapse</br>Publicador de artefatos do Synapse
espaços de trabalho/sqlscriptes/gravar, excluir|Administrador do Azure Synapse</br>Administrador do SQL do Synapse</br>Colaborador de Synapse</br>Publicador de artefatos do Synapse
espaços de trabalho/Dataflows/gravação, exclusão|Administrador do Azure Synapse</br>Colaborador de Synapse</br>Publicador de artefatos do Synapse
espaços de trabalho/pipelines/gravação, exclusão|Administrador do Azure Synapse</br>Colaborador de Synapse</br>Publicador de artefatos do Synapse
espaços de trabalho/gatilhos/gravação, exclusão|Administrador do Azure Synapse</br>Colaborador de Synapse</br>Publicador de artefatos do Synapse
espaços de trabalho/conjuntos de valores/gravação, exclusão|Administrador do Azure Synapse</br>Colaborador de Synapse</br>Publicador de artefatos do Synapse
espaços de trabalho/bibliotecas/gravação, excluir|Administrador do Azure Synapse</br>Administrador de Apache Spark de Synapse</br>Colaborador de Synapse</br>Publicador de artefatos do Synapse
espaços de trabalho/linkedservices/gravar, excluir|Administrador do Azure Synapse</br>Colaborador de Synapse</br>Publicador de artefatos do Synapse</br>Gerenciador de Dados Synapse vinculados
espaços de trabalho/credenciais/gravação, excluir|Administrador do Azure Synapse</br>Colaborador de Synapse</br>Publicador de artefatos do Synapse</br>Gerenciador de Dados Synapse vinculados
espaços de trabalho/blocos de anotações/viewOutputs/ação|Administrador do Azure Synapse</br>Administrador de Apache Spark de Synapse</br>Colaborador de Synapse</br>Publicador de artefatos do Synapse</br>Usuário do artefato Synapse
espaços de trabalho/pipelines/viewOutputs/ação|Administrador do Azure Synapse</br>Colaborador de Synapse</br>Publicador de artefatos do Synapse</br>Usuário do artefato Synapse
espaços de trabalho/linkservices/useSecret/ação|Administrador do Azure Synapse</br>Usuário de credencial Synapse
espaços de trabalho/credenciais/useSecret/ação|Administrador do Azure Synapse</br>Usuário de credencial Synapse

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Escopos de RBAC Synapse e suas funções com suporte

A tabela a seguir lista os escopos de RBAC Synapse e as funções que podem ser atribuídas em cada escopo. 

>[!note]
>Para criar ou excluir um objeto, você deve ter permissões em um escopo de nível superior.

Escopo|Funções
--|--
Workspace |Administrador do Azure Synapse</br>Administrador de Apache Spark de Synapse</br>Administrador do SQL do Synapse</br>Colaborador de Synapse</br>Publicador de artefatos do Synapse</br>Usuário do artefato Synapse</br>Operador de computação Synapse </br>Usuário de credencial Synapse</br>Gerenciador de Dados Synapse vinculados</br>Usuário Synapse
Pool do Apache Spark | Administrador do Azure Synapse </br>Colaborador de Synapse </br> Operador de computação Synapse
runtime de integração | Administrador do Azure Synapse </br>Colaborador de Synapse </br> Operador de computação Synapse
Serviço vinculado |Administrador do Azure Synapse </br>Usuário de credencial Synapse
Credencial |Administrador do Azure Synapse </br>Usuário de credencial Synapse
 
>[!note]
>Todas as funções de artefato e ações são delimitadas no nível do espaço de trabalho. 

## <a name="next-steps"></a>Próximas etapas

Saiba [como revisar as atribuições de função do RBAC Synapse](./how-to-review-synapse-rbac-role-assignments.md) para um espaço de trabalho.

Saiba [como atribuir funções RBAC Synapse](./how-to-manage-synapse-rbac-role-assignments.md)
