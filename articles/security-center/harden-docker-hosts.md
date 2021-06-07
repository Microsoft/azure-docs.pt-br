---
title: Use a central de segurança do Azure para proteger os hosts do Docker e proteger os contêineres
description: Como proteger os hosts do Docker e verificar se eles estão em conformidade com o parâmetro de comparação do Docker do CIS
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b30e08a2739000d2a7ec14a95742f2654e1d2ea1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98916227"
---
# <a name="harden-your-docker-hosts"></a>Proteger os hosts do Docker

A Central de Segurança do Azure identifica contêineres não gerenciados hospedados em VMs IaaS do Linux ou em outros computadores Linux que executam contêineres do Docker. A Central de Segurança avalia continuamente as configurações desses contêineres. Em seguida, ela as compara com o [benchmark do CIS (Center for Internet Security) do Docker](https://www.cisecurity.org/benchmark/docker/).

A Central de Segurança inclui todo o conjunto de regras do benchmark do CIS do Docker e alerta você se os contêineres não atendem a nenhum dos controles. Quando encontra configurações incorretas, a Central de Segurança gera recomendações de segurança. Use a **página recomendações** da Central de Segurança para exibir recomendações e corrigir problemas.

Quando as vulnerabilidades são encontradas, elas são agrupadas dentro de uma única recomendação.

>[!NOTE]
> Essas verificações de parâmetro de comparação de CIS não serão executadas em instâncias gerenciadas por AKS ou em VMs gerenciadas por databricks.

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|Requer [Azure Defender para Servidores](defender-for-servers-introduction.md)|
|Funções e permissões necessárias:|**Leitor** no espaço de trabalho ao qual o host se conecta|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>Identificar e corrigir vulnerabilidades de segurança na configuração do Docker

1. No menu da central de segurança, abra a página **recomendações** .

1. O filtro para as **vulnerabilidades de recomendação em configurações de segurança de contêiner deve ser corrigido** e selecionar a recomendação.

    A página recomendação mostra os recursos afetados (hosts do Docker). 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="Recomendação para corrigir vulnerabilidades em configurações de segurança de contêiner ":::

1. Para exibir e corrigir os controles de CIS que um host específico falhou, selecione o host que você deseja investigar. 

    > [!TIP]
    > Se você iniciou na página de inventário de ativos e chegou essa recomendação a partir daí, selec o botão **executar ação** na página recomendação.
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="Botão executar ação para iniciar Log Analytics":::

    Log Analytics é aberto com uma operação personalizada pronta para ser executada. A consulta personalizada padrão inclui uma lista de todas as regras com falha que foram avaliadas, juntamente com as diretrizes para ajudá-lo a resolver os problemas.

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="Log Analytics página com a consulta mostrando todos os controles de CIS com falha":::

1. Ajuste os parâmetros de consulta, se necessário.

1. Quando tiver certeza de que o comando é apropriado e pronto para o host, selecione **executar**.


## <a name="next-steps"></a>Próximas etapas

A proteção do Docker é apenas um aspecto dos recursos de segurança do contêiner da central de segurança. 

Saiba mais [segurança de contêiner na central de segurança](container-security.md).