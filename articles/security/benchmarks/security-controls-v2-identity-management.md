---
title: Benchmark de segurança do Azure v2-gerenciamento de identidade
description: Azure Security benchmark v2 Identity Management
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 33f5dff65fa7ad8274051f784f2e61dc8366d389
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368844"
---
# <a name="security-control-v2-identity-management"></a>Controle de segurança v2: gerenciamento de identidade

O gerenciamento de identidades abrange controles para estabelecer uma identidade segura e controles de acesso usando Azure Active Directory. Isso inclui o uso de logon único, autenticações fortes, identidades gerenciadas (e princípios de serviço) para aplicativos, acesso condicional e monitoramento de anomalias de conta.

## <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: padronizar o Azure Active Directory como o sistema central de identidade e autenticação

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| MENSAGENS INSTANTÂNEAS-1 | 16,1, 16,2, 16,4, 16,5 | IA-2, IA-8, AC-2, AC-3 |

O Azure Active Directory (AD do Azure) é o serviço de gerenciamento de identidade e acesso padrão do Azure. Você deve padronizá-lo no Azure AD para controlar o gerenciamento de identidades e acesso da sua organização:
- Recursos de nuvem da Microsoft, como o portal do Azure, o Armazenamento do Azure, as Máquinas Virtuais do Azure (Linux e Windows), o Azure Key Vault e os aplicativos PaaS e SaaS.

- Os recursos da sua organização, como os aplicativos no Azure ou os recursos de rede corporativa.

A proteção do Azure AD deve ser uma prioridade alta na prática de segurança de nuvem da sua organização. O Azure AD fornece uma classificação de segurança de identidade para ajudar você a avaliar sua postura de segurança de identidade em relação às recomendações de melhores práticas da Microsoft. Use a classificação para medir o alinhamento da sua configuração com as recomendações de melhores práticas e fazer aprimoramentos na sua postura de segurança.

Observação: o Azure AD dá suporte a provedores de identidade externos, que permitem aos usuários sem uma conta Microsoft entrar nos respectivos aplicativos e recursos com a identidade externa.

- [Locação no Azure AD](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [Como criar e configurar uma instância do Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Definir locatários do Azure AD](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [Usar provedores de identidade externos para um aplicativo](../../active-directory/external-identities/identity-providers.md)

- [O que é a classificação de segurança de identidade do Azure AD](../../active-directory/fundamentals/identity-secure-score.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gerenciamento de identidades e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: gerenciar identidades de aplicativo de maneira segura e automática

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| MENSAGENS INSTANTÂNEAS-2 | N/D | AC-2, AC-3, IA-2, IA-4, IA-9 |

Para contas não humanas, como serviços ou automação, use identidades gerenciadas do Azure, em vez de criar uma conta humana mais poderosa para acessar recursos ou executar código. As identidades gerenciadas do Azure podem ser autenticadas nos serviços e recursos do Azure que dão suporte à autenticação do Azure AD. A autenticação é habilitada por meio de regras de concessão de acesso predefinidas, evitando credenciais embutidas no código-fonte ou arquivos de configuração. 

Para serviços que não dão suporte a identidades gerenciadas, use o Azure AD para criar uma entidade de serviço com permissões restritas no nível de recurso.  É recomendável configurar entidades de serviço com credenciais de certificado e fazer fallback para os segredos do cliente. Em ambos os casos, Azure Key Vault pode ser usado em conjunto com as identidades gerenciadas do Azure, para que o ambiente de tempo de execução (como uma função do Azure) possa recuperar a credencial do cofre de chaves.

- [Identidades gerenciadas do Azure](../../active-directory/managed-identities-azure-resources/overview.md)

- [Serviços compatíveis com identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Entidade de serviço do Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Criar uma entidade de serviço com certificados](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

Usar Azure Key Vault para o registro de entidade de segurança: autenticação # autorizar-a-Security-principal-to-Access-Key-Vault

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gerenciamento de identidades e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: usar o SSO (logon único) do Azure AD para acesso ao aplicativo

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| MENSAGENS INSTANTÂNEAS-3 | 4.4 | IA-2, IA-4 |

O Azure AD fornece gerenciamento de acesso e identidade para recursos do Azure, aplicativos de nuvem e aplicativos locais. O gerenciamento de identidades e acesso se aplica a identidades corporativas, como funcionários, bem como identidades externas, como parceiros, fornecedores e fornecedores.

Use o SSO (logon único) do Azure AD para gerenciar e proteger o acesso aos dados e recursos da sua organização localmente e na nuvem. Conecte todos os seus usuários, aplicativos e dispositivos ao Azure AD para obter acesso contínuo, seguro e maior visibilidade e controle. 

- [Entender o SSO do aplicativo com o Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gerenciamento de identidades e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: usar controles de autenticação forte para todo o acesso baseado no Azure Active Directory

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| MENSAGENS INSTANTÂNEAS-4 | 4,2, 4,4 4,5, 11,5, 12,11, 16,3 | AC-2, AC-3, IA-2, IA-4 |

O Azure AD dá suporte a controles de autenticação fortes por meio da MFA (autenticação multifator) e de métodos fortes de senha.  
- Autenticação multifator: habilite o Azure AD MFA e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure para sua configuração de MFA. A MFA pode ser imposta em todos os usuários, Selecionar usuários ou no nível por usuário com base nas condições de entrada e nos fatores de risco. 

- Autenticação com senha: três opções de autenticação com senha estão disponíveis: Windows Hello para empresas, Microsoft Authenticator app e métodos de autenticação locais, como cartões inteligentes. 

Para administradores e usuários privilegiados, verifique se o nível mais alto do método de autenticação forte é usado, seguido da distribuição da política de autenticação forte apropriada para outros usuários.

Se a autenticação baseada em senha herdada ainda for usada para autenticação do Azure AD, lembre-se de que as contas somente em nuvem (contas de usuário criadas diretamente no Azure) têm uma política de senha de linha de base padrão. E contas híbridas (contas de usuário que vêm do Active Directory local) seguem as políticas de senha local. Ao usar a autenticação baseada em senha, o Azure AD fornece um recurso de proteção de senha que impede que os usuários definam senhas que são fáceis de adivinhar. A Microsoft fornece uma lista global de senhas banidas que são atualizadas com base na telemetria, e os clientes podem aumentar a lista com base em suas necessidades (por exemplo, identidade visual, referências culturais, etc.). Essa proteção por senha pode ser usada para contas híbridas e somente em nuvem. 

Observação: a autenticação baseada em credenciais de senha sozinhas é suscetível a métodos de ataque populares. Para maior segurança, use autenticação forte, como MFA e uma política de senha forte. Para aplicativos de terceiros e serviços do Marketplace que podem ter senhas padrão, você deve alterá-los durante a configuração inicial do serviço. 

- [Como habilitar a MFA no Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Introdução às opções de autenticação sem senha do Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Política de senha padrão do Azure AD](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminar senhas inadequadas usando a proteção de senha do Azure AD](../../active-directory/authentication/concept-password-ban-bad.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gerenciamento de identidades e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: monitorar anomalias nas contas e fornecer alertas sobre elas

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| MENSAGENS INSTANTÂNEAS-5 | 4,8, 4,9, 16,12, 16,13 | AC-2, AC-3, AC-7, AU-6 |

O Azure AD fornece as seguintes fontes de dados: 
-   Entradas – O relatório de entradas fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.

-   Logs de auditoria – fornece rastreamento por meio de logs para todas as alterações feitas por meio de vários recursos no Azure AD. Exemplos de logs de auditoria de alterações registradas incluem adicionar ou remover usuários, aplicativos, grupos, funções e políticas.

-   Entradas arriscadas - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário.

-   Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

Essas fontes de dados podem ser integradas ao Azure Monitor, ao Azure Sentinel ou a sistemas SIEM de terceiros.

A central de segurança do Azure também pode alertar sobre determinadas atividades suspeitas, como um número excessivo de tentativas de autenticação com falha e contas preteridas na assinatura. 

A ATP (proteção avançada contra ameaças) do Azure é uma solução de segurança que pode usar sinais de Active Directory locais para identificar, detectar e investigar ameaças avançadas, identidades comprometidas e ações de insider mal-intencionadas.

- [Relatórios de atividade de auditoria no Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Como exibir entradas suspeitas do Azure Active Directory](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../../security-center/security-center-identity-access.md)

- [Alertas do módulo de proteção da inteligência contra ameaças da Central de Segurança do Azure](../../security-center/alerts-reference.md)

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Conectar dados de Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md)

- [Proteção Avançada contra Ameaças do Azure](/azure-advanced-threat-protection/what-is-atp)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: restringir o acesso aos recursos do Azure com base em condições

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| MENSAGENS INSTANTÂNEAS-6 | N/D | AC-2, AC-3 |

Use o acesso condicional do Azure AD para obter um controle de acesso mais granular com base em condições definidas pelo usuário, como exigir logons de usuário de determinados intervalos de IP para usar a MFA. Um gerenciamento de sessão de autenticação granular também pode ser usado por meio da política de acesso condicional do Azure AD para diferentes casos de uso. 

- [Visão geral do acesso condicional do Azure](../../active-directory/conditional-access/overview.md)

- [Políticas de acesso condicional comuns](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Configurar o gerenciamento da sessão de autenticação com Acesso Condicional](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gerenciamento de identidades e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminar a exposição involuntária de credenciais

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| MENSAGENS INSTANTÂNEAS-7 | 18,1, 18,7 | IA-5 |

Implemente o verificador de credenciais do Azure DevOps para identificar as credenciais no código. O verificador de credenciais também incentiva a movimentação de credenciais descobertas para locais mais seguros, como Azure Key Vault.

Para o GitHub, você pode usar o recurso de verificação de segredo nativo para identificar as credenciais ou outra forma de segredos dentro do código.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Verificação de segredos do GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: proteger o acesso do usuário a aplicativos herdados

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| IM-8 | 14,6 | AC-2, AC-3, SC-11 |

Verifique se você tem controles de acesso modernos e monitoramento de sessão para aplicativos herdados e os dados que eles armazenam e processam. Embora as VPNs sejam comumente usadas para acessar aplicativos herdados, elas geralmente têm apenas controle de acesso básico e monitoramento de sessão limitado.

O Azure Proxy de Aplicativo do AD permite que você publique aplicativos locais herdados para usuários remotos com SSO (logon único) ao validar explicitamente a confiabilidade de usuários e dispositivos remotos com acesso condicional do Azure AD. 

Como alternativa, Microsoft Cloud App Security é um serviço CASB (agente de segurança de acesso de nuvem) que pode fornecer controles para monitorar as sessões de aplicativo de um usuário e ações de bloqueio (tanto para aplicativos locais herdados quanto para aplicativos SaaS (software como serviço) de nuvem). 

- [Proxy de Aplicativo do AD do Azure](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Práticas recomendadas de Microsoft Cloud App Security](/cloud-app-security/best-practices)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)