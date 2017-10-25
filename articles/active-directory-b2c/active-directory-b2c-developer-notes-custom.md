---
title: "Azure Active Directory B2C: Notas do desenvolvedor sobre o uso de políticas personalizadas | Microsoft Docs"
description: "Observações para os desenvolvedores sobre configuração e manutenção do B2C do Azure AD com políticas personalizadas"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/05/2017
ms.author: joroja
ms.openlocfilehash: a5f222e5b11e05286152a9f1cc55d2c3fc27a9dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Notas de versão para a versão prévia da política personalizada do Azure Active Directory B2C
O conjunto de recursos de política personalizada agora está disponível para avaliação em versão prévia para todos os clientes do Azure Active Directory B2C(Azure AD B2C). Esse conjunto de recursos se destina a desenvolvedores avançados de identidade para criar as soluções de identidade mais complexas.  

Atualmente, esse conjunto de recursos requer que os desenvolvedores configurem a Identity Experience Framework diretamente por meio da edição do arquivo XML. Esse método de configuração é poderoso e complexo. Os desenvolvedores de identidade avançada que usam a Identity Experience Framework devem planejar investir um tempo concluindo passo a passos e lendo os documentos de referência. 

## <a name="features-included-in-this-public-preview"></a>Recursos incluídos nesta versão prévia
Com os novos recursos introduzidos na revisão pública, os desenvolvedores podem executar as seguintes tarefas:<br>

* Criar e carregar percursos do usuário de autenticação personalizada usando políticas personalizadas. 
   * Descrever percursos do usuário passo a passo como trocas entre provedores de declarações. 
   * Definir a ramificação condicional em percursos do usuário. 
* Integrar os serviços habilitados para API REST aos seus percursos do usuário de autenticação personalizada.  
* Adicionar a federação a provedores de identidade em conformidade com o padrão OpenIDConnect. <br>
* Adicionar a federação com provedores de identidade que seguem o protocolo SAML 2.0. 

## <a name="terms-of-the-public-preview"></a>Termos da versão prévia

* Incentivamos o uso de novos recursos somente para fins de avaliação.<br>
* Os novos recursos não se destinam ao uso em um ambiente de produção.<br>
* Os contratos de nível de serviço (SLAs) não se aplicam aos novos recursos. <br>
* As solicitações de suporte podem ser arquivadas por meio dos canais de suporte normais. <br>
* Não há nenhuma data prometida para disponibilidade geral.<br>
* A nosso critério, e por qualquer motivo, a Microsoft pode sinalizar e rejeitar ou restringir os cenários e percursos do usuário que excedam o escopo da lista de produtos Azure AD B2C para servir como uma plataforma de gerenciamento de identidade e acesso de cliente (CIAM).

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilidades dos desenvolvedores de conjunto de recursos de política personalizada
A configuração de política manual concede acesso de nível inferior para a plataforma subjacente do Azure AD B2C e resulta na criação de uma estrutura de confiança exclusiva e totalmente personalizável. As permutas possíveis de provedores de identidade personalizada, relações de confiança, integrações com serviços externos e fluxos de trabalho passo a passo criam demandas ainda maiores para que os desenvolvedores avançados as consumam.

Para se beneficiar totalmente da versão prévia, sugerimos que os desenvolvedores que utilizarem o conjunto de recursos de política personalizada sigam as seguintes diretrizes:
* Se familiarize com a linguagem de configuração do Mecanismo de Experiência de Identidade e o gerenciamento de chave/segredos.
* Apropriar-se de cenários e de integrações personalizadas.
* Executar testes de cenário metódicos.
* Siga as práticas recomendadas de desenvolvimento/preparo de software com no mínimo um ambiente de desenvolvimento e teste um ambiente de produção.
* Mantenha-se informado sobre novos desenvolvimentos de provedores de identidade e de serviços para integração. Por exemplo, acompanhe as alterações em segredos e alterações programadas e não programadas no serviço.
* Configure o monitoramento ativo e monitore a capacidade de resposta dos ambientes de produção.
* Mantenha atualizados os endereços de email de contato e permaneça responsivo aos emails da equipe de site ativo da Microsoft.
* Execute ação em tempo hábil quando for aconselhado a fazer isso pela equipe de site ativo da Microsoft. 


>[!NOTE]
>Esses recursos podem, eventualmente, ser incluídos em políticas internas do Azure AD, tornando-os mais acessíveis para todos os desenvolvedores.

## <a name="next-steps"></a>Próximas etapas
[Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).
