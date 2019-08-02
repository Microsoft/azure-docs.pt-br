---
title: Comparação baseada em recursos dos níveis de gerenciamento de API do Azure | Microsoft Docs
description: Este artigo compara os níveis de gerenciamento de API com base nos recursos que eles oferecem.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: a57f8e44d19432f82abe4fa5e7bafce900db3394
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448003"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Comparação baseada em recursos dos níveis de gerenciamento de API do Azure

Cada [tipo de preço](https://aka.ms/apimpricing) de Gerenciamento de API oferece um conjunto distinto de recursos e por unidade de [capacidade](api-management-capacity.md). A tabela a seguir resume os principais recursos disponíveis em cada um dos níveis. Alguns recursos podem funcionar de maneira diferente ou ter recursos diferentes dependendo da camada. Em tais casos, as diferenças são mencionadas nos artigos de documentação que descrevem esses recursos individuais.

| Recurso                                                                                      | Consumo | Desenvolvedores      | Básico          | Standard       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Integração do Azure AD<sup>1</sup>                                                             | Não                            | Sim            | Não             | sim            | Sim            |
| Suporte de rede virtual (VNet)                                                               | Não                            | Sim            | Não             | Não             | Sim            |
| Implantação em várias regiões                                                                      | Não                            | Não             | Não             | Não             | Sim            |
| Vários nomes de domínio personalizado                                                                 | Não                            | Não             | Não             | Não             | Sim            |
| Portal do desenvolvedor<sup>2</sup>                                                                 | Não                            | sim            | sim            | sim            | Sim            |
| Cache embutido                                                                               | Não                            | sim            | sim            | sim            | Sim            |
| Análise integrada                                                                           | Não                            | sim            | sim            | sim            | Sim            |
| [Configurações de SSL](api-management-howto-manage-protocols-ciphers.md)                             | Sim                            | sim            | sim            | sim            | Sim            |
| [Cache externo](https://aka.ms/apimbyoc)                                                    | Sim                           | sim            | sim            | sim            | Sim            |
| [Autenticação de certificado de cliente](api-management-howto-mutual-certificates-for-clients.md) | Sim                | sim            | sim            | sim            | Sim            |
| [Backup e restauração](api-management-howto-disaster-recovery-backup-restore.md)               | Não                            | sim            | sim            | sim            | Sim            |
| [Gerenciamento via Git](api-management-configuration-repository-git.md)                        | Não                            | sim            | sim            | sim            | Sim            |
| API de Gerenciamento                                                                        | Não                            | sim            | sim            | sim            | Sim            |
| Métricas e logs do Azure Monitor                                                               | Não                | sim            | sim            | sim            | Sim            |
| IP Estático                                                               | Não                | sim            | sim            | sim            | Sim            |

<sup>1</sup> permite o uso do Azure AD (e do Azure AD B2C) como uma identidade de provedor para o usuário é entrar no portal do desenvolvedor.<br/>
<sup>2</sup> Incluindo funcionalidade relacionada, por ex. usuários, grupos, problemas, aplicativos e modelos de email e notificações.<br/>
