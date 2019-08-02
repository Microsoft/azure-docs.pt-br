---
title: Recomendações de segurança para o serviço de aplicativo do Azure
description: Recomendações de segurança para o serviço de aplicativo do Azure. Implementando essas recomendações ajuda a cumprir suas obrigações de segurança, conforme descrito em nosso modelo de responsabilidade compartilhada e melhorará a segurança geral para suas soluções de aplicativo web.
services: app-service
author: barclayn
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 53cd2b1dde1158a1c46f798e57911dad110dc87e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718255"
---
# <a name="security-recommendations-for-app-service"></a>Recomendações de segurança para o serviço de aplicativo

Este artigo contém recomendações de segurança para o serviço de aplicativo do Azure. Implementando essas recomendações ajuda a cumprir suas obrigações de segurança, conforme descrito em nosso modelo de responsabilidade compartilhada e melhorará a segurança geral para suas soluções de aplicativo Web. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades de provedor de serviço, leia [segurança de infraestrutura do Azure](../security/azure-security-infrastructure.md).

## <a name="general"></a>Geral

| Recomendações | Comentários |
|-|-|----|
| Mantenha-se atualizado | Use as versões mais recentes de plataformas com suporte, estruturas, protocolos e linguagens de programação. |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Recomendações | Comentários |
|-|----|
| Desabilitar o acesso anônimo | A menos que você precisa dar suporte a solicitações anônimas, desabilite o acesso anônimo. Para obter mais informações sobre as opções de autenticação do serviço de aplicativo do Azure, consulte [autenticação e autorização no serviço de aplicativo do Azure](overview-authentication-authorization.md).|
| Exigir autenticação | Sempre que possível, use o módulo de autenticação do serviço de aplicativo em vez de escrever código para lidar com autenticação e autorização. Ver [autenticação e autorização no serviço de aplicativo do Azure](overview-authentication-authorization.md). |
| Proteger os recursos de back-end com acesso autenticado | Você pode usar a identidade do usuário ou usar uma identidade de aplicativo para autenticar em um recurso de back-end. Quando você opta por usar uma identidade de aplicativo um [identidade gerenciada](overview-managed-identity.md).
| Exigir autenticação de certificado do cliente | Autenticação de certificado de cliente melhora a segurança, permitindo apenas conexões de clientes que podem autenticar usando certificados que você fornecer. |

## <a name="data-protection"></a>Proteção de dados

| Recomendações | Comentários |
|-|-|
| Redirecionar HTTP para HTTPs | Por padrão, clientes podem se conectar a aplicativos web usando HTTP ou HTTPS. Recomendamos o redirecionamento HTTP para HTTPs porque o HTTPS usa o protocolo SSL/TLS para fornecer uma conexão segura, que é tanto criptografadas e autenticadas. |
| Criptografar a comunicação para recursos do Azure | Quando seu aplicativo se conecta aos recursos do Azure, tais como [banco de dados SQL](https://azure.microsoft.com/services/sql-database/) ou [armazenamento do Azure](/azure/storage/), a conexão permanece no Azure. Uma vez que a conexão percorre o compartilhada de rede no Azure, você deve sempre criptografar toda a comunicação. |
| Requer a versão mais recente do TLS possíveis | Desde 2018 novos aplicativos de serviço de aplicativo do Azure usam o TLS 1.2. Versões mais recentes do TLS incluem aprimoramentos de segurança sobre as versões mais antigas do protocolo. |
| Use o FTPS | O Serviço de Aplicativo dá suporte ao FTP e FTPS para implantar os arquivos. Use o FTPS em vez de FTP quando possível. Quando um ou ambos os protocolos não estiverem em uso será necessário [desabilitá-los](deploy-ftp.md#enforce-ftps). |
| Proteger dados do aplicativo | Não armazene segredos do aplicativo, como credenciais de banco de dados, tokens de API ou chaves privadas em seus arquivos de configuração ou código. A abordagem comumente aceita é acessá-los como [variáveis de ambiente](https://wikipedia.org/wiki/Environment_variable) usando o padrão standard na linguagem de sua escolha. No serviço de aplicativo do Azure, você pode definir variáveis de ambiente por meio [configurações do aplicativo](web-sites-configure.md) e [cadeias de caracteres de conexão](web-sites-configure.md). Configurações do aplicativo e cadeias de caracteres de conexão são armazenadas criptografados no Azure. As configurações do aplicativo são descriptografadas somente antes seja injetado na memória do processo do aplicativo quando o aplicativo é iniciado. As chaves de criptografia são giradas regularmente. Como alternativa, você pode integrar seu aplicativo de serviço de aplicativo do Azure com o [Azure Key Vault](/azure/key-vault/) para gerenciamento de segredos avançada. Ao [acessar o Key Vault com uma identidade gerenciada](../key-vault/tutorial-web-application-keyvault.md), o aplicativo do Serviço de Aplicativo poderá acessar com segurança os segredos que você precisa. |

## <a name="networking"></a>Rede

| Recomendações | Comentários |
|-|-|
| Usar restrições de IP estático | Serviço de aplicativo do Azure no Windows permite que você defina uma lista de endereços IP que têm permissão para acessar seu aplicativo. A lista permitida pode incluir endereços IP individuais ou um intervalo de endereços IP definidos por uma máscara de sub-rede. Para obter mais informações, consulte [Restrições de IP estático do Serviço de Aplicativo do Azure](app-service-ip-restrictions.md).  |
| Use o tipo de preço isolado | Exceto para o tipo de preço isolado, todas as camadas executam seus aplicativos na infraestrutura de rede compartilhada no serviço de aplicativo do Azure. A camada isolada fornece isolamento de rede completa executando seus aplicativos dentro de um dedicado [ambiente de serviço de aplicativo](environment/intro.md). Um ambiente do Serviço de Aplicativo é executado na própria instância da [Rede Virtual do Microsoft Azure](/azure/virtual-network/).|
| Usar conexões seguras ao acessar recursos locais | Você pode usar [conexões híbridas](app-service-hybrid-connections.md), [integração da rede Virtual](web-sites-integrate-with-vnet.md), ou [do ambiente do serviço de aplicativo](environment/intro.md) para se conectar aos recursos locais. |
| Limitar a exposição ao tráfego de rede de entrada | Grupos de segurança de rede permitem que você restrinja o acesso de rede e controlar o número de pontos de extremidade expostos. Para obter mais informações, consulte [como para controlar tráfego de entrada para um ambiente de serviço de aplicativo](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Monitoramento

| Recomendações | Comentários |
|-|-|
|Camada standard da Central de segurança do uso do Azure | [A Central de segurança do Azure](../security-center/security-center-app-services.md) é integrado nativamente com o serviço de aplicativo do Azure. Ele pode executar avaliações e fornecer recomendações de segurança. |

## <a name="next-steps"></a>Próximas etapas

Verifique com seu provedor de aplicativo para ver se há requisitos adicionais de segurança. Para obter mais informações sobre como desenvolver aplicativos seguros, consulte [documentação de desenvolvimento seguro](../security/abstract-develop-secure-apps.md).
