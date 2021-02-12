---
title: Conector de Gerenciamento de Serviços de TI-exportação segura no Azure Monitor
description: Este artigo mostra como conectar seus produtos/serviços de ITSM com exportação segura em Azure Monitor para monitorar e gerenciar centralmente os itens de trabalho de ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: ee56d65452cb8535c5197e1b3524bd4e9c9ab9ea
ms.sourcegitcommit: 697638c20ceaf51ec4ebd8f929c719c1e630f06f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857402"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Conectar o Azure a ferramentas de ITSM usando a exportação segura

Este artigo mostra como configurar a conexão entre seu produto ou serviço de ITSM (gerenciamento de serviço de ti) usando a exportação segura.

A exportação segura é uma versão atualizada do [conector de gerenciamento de serviços de ti (ITSMC)](./itsmc-overview.md). Ambas as versões permitem que você crie itens de trabalho em uma ferramenta de ITSM quando Azure Monitor envia alertas. A funcionalidade inclui os alertas de métrica, log e log de atividades.

ITSMC usa credenciais de nome de usuário e senha. A exportação segura tem autenticação mais forte porque usa Azure Active Directory (Azure AD). O Azure AD é o serviço de gerenciamento de acesso e identidade baseado em nuvem da Microsoft. Ele ajuda os usuários a entrar e acessar recursos internos ou externos. Usar o Azure AD com o ITSM ajuda a identificar alertas do Azure (por meio da ID do aplicativo do Azure AD) que foram enviados para o sistema externo.

> [!NOTE]
> A capacidade de conectar o Azure às ferramentas de ITSM usando a exportação segura está em versão prévia.

## <a name="secure-export-architecture"></a>Arquitetura de exportação segura

A arquitetura de exportação segura apresenta os seguintes novos recursos:

* **Novo grupo de ações**: os alertas são enviados para a ferramenta de ITSM por meio do grupo de ação de webhook seguro, em vez do grupo de ações de ITSM que o ITSMC usa.
* **Autenticação do Azure ad**: a autenticação ocorre por meio do Azure AD em vez de credenciais de nome de usuário/senha.

## <a name="secure-export-data-flow"></a>Fluxo de dados de exportação segura

As etapas do fluxo de dados de exportação segura são:

1. Azure Monitor envia um alerta que está configurado para usar a exportação segura.
2. A carga do alerta é enviada por uma ação de webhook segura para a ferramenta ITSM.
3. O aplicativo ITSM verifica com o Azure AD se o alerta está autorizado a entrar na ferramenta de ITSM.
4. Se o alerta for autorizado, o aplicativo:
   
   1. Cria um item de trabalho (por exemplo, um incidente) na ferramenta de ITSM.
   2. Associa a ID do item de configuração (CI) ao banco de dados de gerenciamento do cliente (CMDB).

![Diagrama que mostra como a ferramenta de ITSM se comunica com o Azure A D, alertas do Azure e um grupo de ações.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>Benefícios da exportação segura

Os principais benefícios da integração são:

* **Autenticação melhor**: o Azure ad fornece autenticação mais segura sem os tempos limite que normalmente ocorrem no ITSMC.
* **Alertas resolvidos na ferramenta ITSM: os alertas de** métrica implementam os Estados "disparados" e "resolvidos". Quando a condição for atendida, o estado do alerta será "disparado". Quando a condição não for mais atendida, o estado do alerta será "resolvido". No ITSMC, os alertas não podem ser resolvidos automaticamente. Com a exportação segura, o estado resolvido flui para a ferramenta de ITSM e, portanto, é atualizado automaticamente.
* **[Esquema de alerta comum](./alerts-common-schema.md)**: em ITSMC, o esquema da carga de alerta difere com base no tipo de alerta. Na exportação segura, há um esquema comum para todos os tipos de alertas. Esse esquema comum contém o CI para todos os tipos de alerta. Todos os tipos de alertas poderão associar seu IC ao CMDB.

## <a name="next-steps"></a>Próximas etapas

* [Criar itens de trabalho de ITSM desde alertas do Azure](./itsmc-overview.md)
