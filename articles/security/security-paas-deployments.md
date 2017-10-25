---
title: "Proteção de implantações de PaaS | Microsoft Docs"
description: " Entenda as vantagens de segurança do PaaS em comparação com outros modelos de serviço de nuvem e saiba mais sobre as melhores práticas para proteger a implantação de PaaS do Azure. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: techlake
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: terrylan
ms.openlocfilehash: f218fe7e59e46683b544fd83bfea505b7cbe2d59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="securing-paas-deployments"></a>Proteção de implantações de PaaS

Este artigo fornece informações que ajudam você a:

- Compreender as vantagens de segurança de hospedar aplicativos na nuvem
- Avaliar as vantagens de segurança de PaaS (plataforma como serviço) em comparação com outros modelos de serviço de nuvem
- Alterar o foco de segurança de uma abordagem de segurança de perímetro centrada na rede para uma centrada em identidade
- Implementar as recomendações de práticas recomendadas de segurança de PaaS gerais

## <a name="cloud-security-advantages"></a>Vantagens da segurança na nuvem
Há vantagens de segurança de estar na nuvem. Em um ambiente local, as organizações provavelmente têm responsabilidades não atendidas e recursos limitados disponíveis para investir em segurança, o que cria um ambiente em que os invasores podem explorar vulnerabilidades em todas as camadas.

![Vantagens de segurança da era da nuvem][1]

As empresas são capazes de aprimorar sua detecção de ameaças e tempos de resposta usando inteligência de nuvem e recursos de segurança baseados em nuvem do provedor.  Ao deslocar responsabilidades para o provedor de nuvem, as organizações podem obter mais cobertura de segurança, que permite que elas realocar recursos de segurança e orçamento para outras prioridades de negócios.

## <a name="division-of-responsibility"></a>Divisão de responsabilidade
É importante entender a divisão da responsabilidade entre você e a Microsoft. No local, você possui a pilha inteira, mas conforme muda para a nuvem, algumas responsabilidades são transferidas para a Microsoft. A matriz de responsabilidades a seguir mostra as áreas da pilha em uma implantação de SaaS, PaaS e IaaS pelas quais você é responsável e pelas quais a Microsoft é responsável.

![Zonas de responsabilidade][2]

Para todos os tipos de implantação de nuvem, você possui seus dados e identidades. Você é responsável por proteger a segurança de seus dados e identidades, dos recursos locais e dos componentes da nuvem que você controla (que varia por tipo de serviço).

As responsabilidades são sempre retidas por você, independentemente do tipo de implantação, são:

- Dados
- Pontos de extremidade
- Conta
- gerenciamento de acesso

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Vantagens de segurança de um modelo de serviço de nuvem PaaS
Usando a mesma matriz de responsabilidades, vamos analisar as vantagens de segurança de uma implantação de PaaS do Azure versus local.

![Vantagens de segurança do PaaS][3]

Iniciando na parte inferior da pilha, a infraestrutura física, a Microsoft reduz responsabilidades e riscos comuns. Como o Microsoft Cloud é monitorado continuamente pela Microsoft é difícil de atacar. Não faz sentido para um invasor buscar o Microsoft Cloud como um alvo. A menos que o invasor tenha muito dinheiro e recursos, é provável que ele mude para outro alvo.  

No meio da pilha, não há nenhuma diferença entre uma implantação de PaaS e local. Na camada de aplicativo e na camada de gerenciamento de acesso e de conta, você tem os riscos semelhantes. Na seção de próximas etapas desse artigo, guiaremos você para as práticas recomendadas para eliminar ou minimizar esses riscos.

Na parte superior da pilha, governança de dados e gerenciamento de direitos, você assume um risco que pode ser reduzido pelo gerenciamento de chaves. (O gerenciamento de chaves é abordado nas práticas recomendadas.) Embora o gerenciamento de chaves seja uma responsabilidade adicional, há áreas em uma implantação de PaaS que você não precisa mais gerenciar de modo que pode deslocar recursos para o gerenciamento de chaves.

A plataforma Azure também fornece uma forte proteção DDoS usando várias tecnologias baseadas em rede. No entanto, todos os tipos de métodos de proteção DDoS baseados em rede tem seus limites em uma base por link e por datacenter. Para ajudar a evitar o impacto de grandes ataques DDoS, você pode aproveitar a funcionalidade de nuvem central do Azure que lhe permite escalar horizontalmente de maneira rápida e automática para proteger contra os ataques DDoS. Entraremos em mais detalhes sobre como você pode fazer isso nos artigos de práticas recomendadas.

## <a name="modernizing-the-defenders-mindset"></a>Modernizando mentalidade do defensor
Juntamente com as implantações de PaaS vem uma mudança na abordagem geral da segurança. Você passa de precisar controlar tudo sozinho com compartilhar a responsabilidade com a Microsoft.

Outra diferença significativa entre as implantações locais tradicionais e de PaaS é uma nova exibição do que define o perímetro de segurança primário. Historicamente, o perímetro de segurança local primário foi sua rede e a maioria dos projetos de segurança local usam a rede como seu pivô de segurança primário. Para implantações de PaaS, você é melhor atendido considerando a identidade como o perímetro de segurança primário.

## <a name="identity-as-the-primary-security-perimeter"></a>Identidade como o perímetro de segurança primário
Uma das cinco características essenciais da computação em nuvem é amplo acesso à rede, que torna o pensamento centrado na rede menos relevante. A meta da maior parte da computação em nuvem é permitir que os usuários acessem recursos independentemente da localização. Para a maioria dos usuários, sua localização será em algum lugar na Internet.

A figura a seguir mostra como o perímetro de segurança evoluiu de um perímetro de rede para um perímetro de identidade. A segurança se torna menos sobre defender a rede e mais sobre como defender seus dados, bem como gerenciar a segurança de seus aplicativos e usuários. A principal diferença é que você deseja avançar a segurança para mais perto do que é importante para sua empresa.

![Identidade como novo perímetro de segurança][4]

Inicialmente, os serviços de PaaS do Azure (por exemplo, funções Web e SQL do Azure) forneciam pouca ou nenhuma defesa de perímetro de rede tradicional. Entendia-se que o objetivo do elemento era ser exposto na Internet (função web) e que a autenticação fornece o novo perímetro (por exemplo, Blob ou SQL do Azure).

As práticas de segurança modernas pressupõem que o adversário violou o perímetro da rede. Portanto, as práticas de defesa modernas passaram para a identidade. As organizações devem estabelecer um perímetro de segurança com base em identidade com forte higiene de autenticação e autorização (práticas recomendadas).

## <a name="recommendations-for-managing-the-identity-perimeter"></a>Recomendações para o gerenciamento do perímetro de identidade

Os princípios e padrões do perímetro de rede estiveram disponíveis por décadas. Por outro lado, o segmento tem relativamente menos experiência com o uso de identidade como o perímetro de segurança primário. Dito isso, acumulamos experiência suficiente para fornecer algumas recomendações gerais comprovadas no campo e que se aplicam a quase todos os serviços de PaaS.

Veja a seguir um resumo das práticas recomendadas gerais para gerenciar seu perímetro de identidade.

- **Não perca suas chaves ou credenciais** proteger as chaves e credenciais é essencial para proteger as implantações de PaaS. Perder chaves e credenciais é um problema comum. Uma boa solução é usar uma solução centralizada em que as chaves e segredos podem ser armazenados em HSMs (módulos de segurança de hardware). O Azure fornece um HSM na nuvem com o [Azure Key Vault](../key-vault/key-vault-whatis.md).
- **Não coloque as credenciais e outros segredos no código-fonte ou no GitHub** a única coisa pior do que perder suas chaves e credenciais é uma pessoa não autorizada obter acesso a elas. Os invasores são capazes de aproveitar tecnologias de bot para encontrar chaves e segredos armazenados em repositórios de código como o GitHub. Não coloque a chave e segredos nesses repositórios de código-fonte públicos.
- **Proteja suas interfaces de gerenciamento de VM nos serviços de PaaS e IaaS híbridos** os serviços de IaaS e PaaS são executados em VMs (máquinas virtuais). Dependendo do tipo de serviço, há várias interfaces de gerenciamento disponíveis que permitem que você gerencie remotamente essas VMs diretamente. Protocolos de gerenciamento remoto, como o [protocolo SSH (Secure Shell)](https://en.wikipedia.org/wiki/Secure_Shell), o [protocolo RDP](https://support.microsoft.com/kb/186607) e o [PowerShell Remoto](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting), podem ser usados. Em geral, é recomendável que você não habilite o acesso remoto direto às VMs da Internet. Se estiver disponível, você deverá usar abordagens alternativas como usar rede virtual privada em uma rede virtual do Azure. Se não houver abordagens alternativas disponíveis, certifique-se de usar senhas complexas e quando estiver disponível, a autenticação de dois fatores (como [Autenticação Multifator do Azure](../multi-factor-authentication/multi-factor-authentication.md)).
- **Use plataformas de autorização e autenticação fortes**

  - Use identidades federadas no Azure AD, em vez de repositórios de usuário personalizados. Quando você usa identidades federadas, aproveita uma abordagem baseada em plataforma e delega o gerenciamento de identidades autorizadas para seus parceiros. Uma abordagem de identidade federada é especialmente importante em cenários quando ocorre a demissão de funcionários e essa informação precisa ser refletida em vários sistemas de autorização e identidade.
  - Use os mecanismos de autenticação e autorização fornecidos da plataforma em vez de código personalizado. O motivo é que o desenvolvimento de código de autenticação personalizado pode ser propenso a erros. A maioria dos desenvolvedores não é especialista em segurança e provavelmente não está ciente das sutilezas e dos desenvolvimentos mais recentes em autenticação e autorização. O código comercial (por exemplo, da Microsoft) frequentemente é extensivamente revisado quanto à segurança.
  - Use a autenticação multifator. A autenticação multifator é o padrão atual para autenticação e autorização porque evita as vulnerabilidades de segurança inerentes em tipos de usuário e senha de autenticação. O acesso às interfaces de gerenciamento do Azure (portal/PowerShell remoto) e aos serviços voltados para o cliente deve ser projetado e configurado para usar o [MFA (Autenticação Multifator do Azure)](../multi-factor-authentication/multi-factor-authentication.md).
  - Use protocolos de autenticação padrão, como OAuth2 e Kerberos. Esses protocolos foram extensivamente revisados em pares e provavelmente são implementados como parte das bibliotecas da plataforma para autenticação e autorização.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, nos concentramos nas vantagens de segurança de uma implantação de PaaS do Azure. Em seguida, aprenda as práticas recomendadas para proteger suas soluções móveis e Web de PaaS. Começaremos com o Serviço de Aplicativo do Azure, o Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure. Conforme os artigos sobre práticas recomendadas para outros serviços do Azure forem disponibilizados, os links serão fornecidos na lista a seguir:

- [Serviço de Aplicativo do Azure](security-paas-applications-using-app-services.md)
- [Banco de Dados SQL do Azure e SQL Data Warehouse do Azure](security-paas-applications-using-sql.md)
- Armazenamento do Azure
- Cache REDIS do Azure
- Barramento de Serviço do Azure
- Firewalls de aplicativo Web

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
