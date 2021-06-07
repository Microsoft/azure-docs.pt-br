---
title: Grade de eventos do Azure-guia de solução de problemas
description: Este artigo fornece uma lista de códigos de erro, mensagens de erro, descrições e ações recomendadas.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 79533918ccc6995f459b39f058de9e01091c0958
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94592984"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Solucionar erros de grade de eventos do Azure
Este guia de solução de problemas fornece as seguintes informações: 

- Códigos de erro da grade de eventos do Azure
- Mensagens de erro
- Descrições para os erros
- Ações recomendadas que você deve executar ao receber esses erros. 

## <a name="error-code-400"></a>Código de erro: 400
| Código do erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. BadRequest<br/>400 | O nome do tópico deve ter entre 3 e 50 caracteres de comprimento. | O comprimento do nome do tópico personalizado deve ter entre 3 e 50 caracteres de comprimento. Somente letras alfanuméricas, dígitos e o caractere '-' são permitidos no nome do tópico. Além disso, o nome não deve começar com as seguintes palavras reservadas: <ul><li>O</li><li>EventGrid</li><li>Sistema</li></ul> | Escolha um nome de tópico diferente que esteja de acordo com os requisitos de nome do tópico. |
| HttpStatusCode. BadRequest<br/>400 | O nome de domínio deve ter entre 3 e 50 caracteres de comprimento. | O comprimento do nome de domínio deve ter entre 3 e 50 caracteres de comprimento. Somente letras alfanuméricas, dígitos e o caractere '-' são permitidos no nome de domínio. Além disso, o nome não deve começar com as seguintes palavras reservadas:<ul><li>O</li><li>EventGrid</li><li>Sistema</li> | Escolha um nome de domínio diferente que atenda aos requisitos de nome de domínio. |
| HttpStatusCode. BadRequest<br/>400 | Tempo de expiração inválido. | O tempo de expiração da assinatura de evento determina quando a assinatura do evento será desativada. Esse valor deve ser um valor DateTime válido no futuro.| Verifique se o tempo de expiração da assinatura do evento está em um formato válido de DateTime e se está definido como no futuro. |

## <a name="error-code-409"></a>Código de erro: 409
| Código do erro | Mensagem de erro | Descrição | Ação recomendada |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. Conflict <br/>409 | Já existe um tópico com o nome especificado. Escolha um nome de tópico diferente.   | O nome do tópico personalizado deve ser exclusivo em uma única região do Azure para garantir uma operação de publicação correta. O mesmo nome pode ser usado em diferentes regiões do Azure. | Escolha um nome diferente para o tópico. |
| HttpStatusCode. Conflict <br/> 409 | Já existe um domínio com o especificado. Escolha um nome de domínio diferente. | O nome de domínio deve ser exclusivo em uma única região do Azure para garantir uma operação de publicação correta. O mesmo nome pode ser usado em diferentes regiões do Azure. | Escolha um nome diferente para o domínio. |
| HttpStatusCode. Conflict<br/>409 | Limite de cota atingido. Para obter mais informações sobre esses limites, consulte [limites de grade de eventos do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Cada assinatura do Azure tem um limite para o número de recursos da grade de eventos do Azure que ele pode usar. Algumas ou todas essas cotas foram excedidas e não foi possível criar mais recursos. |    Verifique o uso dos recursos atuais e exclua os que não forem necessários. Se você ainda precisar aumentar sua cota, envie um email para [aeg@microsoft.com](mailto:aeg@microsoft.com) com o número exato de recursos necessários. |

## <a name="error-code-403"></a>Código de erro: 403

| Código do erro | Mensagem de erro | Descrição | Ação recomendada |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode. proibido <br/>403 | A publicação em {topic/Domain} pelo cliente {IpAddress} foi rejeitada devido às regras de filtragem IpAddress. | O tópico ou domínio tem regras de firewall IP configuradas e o acesso é restrito apenas a endereços IP configurados. | Adicionar o endereço IP às regras de firewall de IP, consulte [Configurar o firewall de IP](configure-firewall.md) |
| HttpStatusCode. proibido <br/> 403 | A publicação em {topic/Domain} pelo cliente é rejeitada, pois a solicitação provém do ponto de extremidade privado e nenhuma conexão de ponto de extremidade privada correspondente foi encontrada para o recurso. | O tópico ou domínio tem pontos de extremidade privados e a solicitação de publicação veio de um ponto de extremidades privado que não está configurado ou aprovado. | Configure um ponto de extremidade privado para o tópico/domínio. [Configurar pontos de extremidade privados](configure-private-endpoints.md) |

Além disso, verifique se o webhook está atrás de um gateway de Aplicativo Azure ou de um firewall do aplicativo Web. Se for, desabilite as seguintes regras de firewall e faça um HTTP POST novamente:

- 920300 (solicitação faltando um cabeçalho de aceitação)
- 942430 (detecção de anomalias de caracteres SQL restritos (args): número de caracteres especiais excedido (12))
- 920230 (várias codificações de URL detectadas)
- 942130 (ataque de injeção de SQL: SQL tautology detectado.)
- 931130 (possível ataque de RFI (inclusão de arquivo remoto) = link/referência fora do domínio)



## <a name="next-steps"></a>Próximas etapas
Se precisar de mais ajuda, poste seu problema no [fórum do Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um [tíquete de suporte](https://azure.microsoft.com/support/options/). 
