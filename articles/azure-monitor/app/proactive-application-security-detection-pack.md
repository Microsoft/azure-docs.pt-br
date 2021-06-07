---
title: Pacote de detecção de segurança com informações do Aplicativo Azure
description: Monitore o aplicativo com informações de Aplicativo Azure e detecção inteligente para possíveis problemas de segurança.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 1ed304e903bb50591e61e294b6701f8268f9d8e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87081620"
---
# <a name="application-security-detection-pack-preview"></a>Pacote de detecção de segurança do aplicativo (visualização)

Application Insights analisa automaticamente a telemetria gerada pelo seu aplicativo e detecta possíveis problemas de segurança. Esse recurso permite que você identifique possíveis problemas de segurança e lide com eles, corrigindo o aplicativo ou adotando as medidas de segurança necessárias.

Este recurso não exige nenhuma configuração especial, diferente de [configurar seu aplicativo para enviar telemetria](./usage-overview.md).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando eu receberia este tipo de notificação de detecção inteligente?
Há três tipos de problemas de segurança que são detectados:
1. Acesso de URL não segura: uma URL no aplicativo que está sendo acessada via HTTP e HTTPS. Normalmente, uma URL que aceita solicitações HTTPS não deve aceitar as solicitações HTTP. Isso pode indicar um problema de segurança ou um erro em seu aplicativo.
2. Forma não segura: um formulário (ou outra solicitação de "POST") no aplicativo usa HTTP em vez de HTTPS. Usar HTTP pode comprometer os dados do usuário que são enviados pelo formulário.
3. Atividade de usuário suspeita: o aplicativo está sendo acessado de vários países/regiões pelo mesmo usuário praticamente ao mesmo tempo. Por exemplo, o mesmo usuário acessou o aplicativo da Espanha e Estados Unidos na mesma hora. Essa detecção indica uma tentativa de acesso possivelmente mal-intencionado ao seu aplicativo.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Meu aplicativo realmente tem um problema?
Não, uma notificação não significa que seu aplicativo realmente tem um problema. A detecção de qualquer um dos cenários acima pode, em muitos casos, indicar um problema de segurança. No entanto, a detecção pode ter uma justificativa comercial natural e pode ser ignorada.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Como corrigir a detecção de "Acesso à URL inseguro"?
1. **Triagem.** A notificação fornece o número de usuários que acessaram URLs inseguras e a URL que foi mais afetada por acesso inseguro. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Com.** Que porcentagem de usuários acessaram URLs inseguros? Quantos URLs foram afetados? Essas informações podem ser obtidas na notificação.
3. **Tect.** A detecção fornece a lista de solicitações inseguras e as listas de URLs e os usuários que foram afetados, para ajudá-lo a diagnosticar o problema.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Como corrigir a detecção de "Formulário inseguro"?
1. **Triagem.** A notificação fornece o número de formulários inseguros e o número de usuários cujos dados foram possivelmente comprometidos. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Com.** Qual forma estava envolvida no maior número de transmissões inseguros e qual é a distribuição de transmissões inseguras, ao longo do tempo? Essas informações podem ser obtidas na notificação.
3. **Tect.** A detecção fornece a lista de formulários inseguros e uma análise do número de transmissões inseguros para cada formulário, para ajudá-lo a diagnosticar o problema.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Como corrigir a detecção de "atividade de usuário suspeita"?
1. **Triagem.** A notificação fornece o número de usuários diferentes que exibiu o comportamento suspeito. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Com.** De quais países/regiões as solicitações suspeitas se originam? Qual o usuário foi mais suspeito? Essas informações podem ser obtidas na notificação.
3. **Tect.** A detecção fornece a lista de usuários suspeitos e a lista de países/regiões para cada usuário, para ajudá-lo a diagnosticar o problema.
