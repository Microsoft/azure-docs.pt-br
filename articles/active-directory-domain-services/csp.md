---
title: Azure AD Domain Services para provedores de soluções na nuvem | Microsoft Docs
description: Saiba como habilitar e gerenciar Azure Active Directory Domain Services domínios gerenciados para provedores de soluções de nuvem do Azure
services: active-directory-ds
author: justinha
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: d8edafff9b6534e5f1ce1c4581595ee187dfd432
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619892"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Implantação e gerenciamento de Azure Active Directory Domain Services para provedores de soluções de nuvem do Azure

O CSP (provedores de soluções de nuvem) do Azure é um programa para parceiros da Microsoft e fornece um canal de licença para vários serviços de nuvem da Microsoft. O Azure CSP permite que os parceiros gerenciem vendas, tenham a propriedade da relação de cobrança, ofereçam suporte técnico e de cobrança e sejam o único ponto de contato dos clientes. Além disso, o Azure CSP fornece um conjunto completo de ferramentas, incluindo um portal de autoatendimento e APIs que o acompanha. Essas ferramentas permitem que os parceiros CSP facilmente provisionem e gerenciem recursos do Azure e forneçam a cobrança para os clientes e suas assinaturas.

O [portal do Partner Center](/partner-center/azure-plan-lp) é o ponto de entrada para todos os parceiros do Azure CSP e fornece recursos avançados de gerenciamento de clientes, processamento automatizado e muito mais. Parceiros Azure CSP podem usar recursos do Centro de Parceiros usando uma interface de usuário baseada na Web ou usando o PowerShell e várias chamadas de API.

O diagrama a seguir ilustra como o modelo CSP funciona em um alto nível. Aqui, a contoso tem um locatário Azure Active Directory (Azure AD). Eles têm uma parceria com um CSP, que implanta e gerencia os recursos na sua assinatura do Azure CSP. A Contoso também pode ter assinaturas regulares (diretas) do Azure, que são cobradas diretamente para a Contoso.

![Visão geral do modelo CSP](./media/csp/csp_model_overview.png)

O locatário do parceiro CSP tem três grupos especiais de agente – agentes de *Administração* , agentes de *assistência técnica* e agentes de *vendas* .

O grupo de agentes de *Administração* é atribuído à função de administrador de locatários no locatário do Azure ad da contoso. Como resultado, um usuário que pertence ao grupo de agentes de administração do parceiro CSP tem privilégios de administrador de locatário no locatário do Azure AD da contoso.

Quando o parceiro CSP provisiona uma assinatura do Azure CSP para a Contoso, seu grupo de agentes administradores é atribuído à função de proprietário para essa assinatura. Como resultado, os agentes administradores do parceiro CSP possuem os privilégios necessários para provisionar recursos do Azure como máquinas virtuais, redes virtuais e Azure Active Directory Domain Services em nome da Contoso.

Para saber mais, confira a [Visão geral do Azure CSP](/partner-center/azure-plan-lp)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Benefícios do uso do Azure AD DS em uma assinatura do Azure CSP

O Azure Active Directory Domain Services (Azure AD DS) fornece serviços de domínio gerenciados, como ingresso no domínio, diretiva de grupo, LDAP, autenticação Kerberos/NTLM que é totalmente compatível com o Windows Server Active Directory Domain Services. Por décadas, muitos aplicativos foram criados para funcionar no AD usando esses recursos. Muitos fornecedores de software independentes (ISVs) têm criado e implantado aplicativos nos locais de seus clientes. Esses aplicativos são difíceis de dar suporte, pois você geralmente requer acesso aos diferentes ambientes em que os aplicativos são implantados. Com assinaturas de Azure CSP, você tem uma alternativa mais simples com a escala e a flexibilidade do Azure.

O Azure AD DS dá suporte a assinaturas do Azure CSP. Você pode implantar seu aplicativo em uma assinatura do Azure CSP vinculada ao locatário do Azure AD do seu cliente. Como resultado, seus funcionários (equipe de suporte) podem gerenciar, administrar e atender às VMs nas quais seu aplicativo é implantado usando as credenciais corporativas da sua organização.

Você também pode implantar um domínio gerenciado do Azure AD DS no locatário do Azure AD do seu cliente. Seu aplicativo é então conectado ao domínio gerenciado do seu cliente. Os recursos em seu aplicativo que dependem do Kerberos/NTLM, LDAP ou da [API System. DirectoryServices](/dotnet/api/system.directoryservices) funcionam diretamente no domínio do cliente. Os clientes finais se beneficiam do consumo de seu aplicativo como serviço, sem a necessidade de se preocupar em manter a infraestrutura em que o aplicativo está implantado.

Toda a cobrança dos recursos do Azure que você consome nessa assinatura, incluindo o Azure AD DS, é cobrada de volta para você. Você mantém controle total sobre a relação com o cliente quando se trata de vendas, cobrança, suporte técnico etc. Com a flexibilidade da plataforma Azure CSP, uma pequena equipe de agentes de suporte pode atender a muitos clientes que têm instâncias do seu aplicativo implantadas.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Modelos de implantação do CSP para o Azure AD DS

Há duas maneiras pelas quais você pode usar o Azure AD DS com uma assinatura do Azure CSP. Escolha a correta com base nas considerações com segurança e simplicidade que seus clientes têm.

### <a name="direct-deployment-model"></a>Modelo de implantação direta

Nesse modelo de implantação, o AD DS do Azure é habilitado em uma rede virtual que pertence à assinatura do Azure CSP. Agentes administradores do parceiro CSP têm os privilégios a seguir:

* Privilégios de *administrador global* no locatário do Azure AD do cliente.
* Privilégios de *proprietário da assinatura* na assinatura do Azure CSP.

![Modelo de implantação direta](./media/csp/csp_direct_deployment_model.png)

Nesse modelo de implantação, os agentes administradores do provedor CSP podem administrar identidades para o cliente. Esses agentes admin podem executar tarefas como provisionar novos usuários ou grupos ou adicionar aplicativos no locatário do Azure AD do cliente.

Esse modelo de implantação pode ser adequado para organizações menores que não têm um administrador de identidade dedicado ou preferem que o parceiro CSP administre identidades em seu nome.

### <a name="peered-deployment-model"></a>Modelo de implantação emparelhada

Nesse modelo de implantação, o Azure AD DS está habilitado em uma rede virtual que pertence ao cliente – uma assinatura direta do Azure paga pelo cliente. O parceiro CSP pode implantar aplicativos em uma rede virtual que pertence à assinatura do CSP do cliente. As redes virtuais podem ser conectadas usando o emparelhamento de rede virtual do Azure.

Com essa implantação, as cargas de trabalho ou os aplicativos implantados pelo parceiro CSP na assinatura do Azure CSP podem se conectar ao domínio gerenciado do cliente provisionado na assinatura direta do Azure do cliente.

![Modelo de implantação emparelhada](./media/csp/csp_peered_deployment_model.png)

Esse modelo de implantação fornece uma separação de privilégios e permite que os agentes de suporte técnico do parceiro CSP administrem a assinatura do Azure e implantem e gerenciar recursos dentro dele. No entanto, os agentes de assistência técnica do parceiro CSP não precisam ter privilégios de administrador global no diretório do Azure AD do cliente. Os administradores de identidades do cliente podem continuar a gerenciar identidades para sua organização.

Esse modelo de implantação pode ser adequado para cenários em que um ISV fornece uma versão hospedada de seu aplicativo local, que também precisa se conectar ao Azure AD do cliente.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Administrar AD DS do Azure em assinaturas do CSP

As considerações importantes a seguir se aplicam ao administrar um domínio gerenciado em uma assinatura do Azure CSP:

* **Os agentes de administração do CSP podem provisionar um domínio gerenciado usando suas credenciais:** O Azure AD DS dá suporte a assinaturas do Azure CSP. Os usuários que pertencem ao grupo de agentes administradores de um parceiro CSP podem provisionar um novo domínio gerenciado.

* **Os CSPs podem criar scripts de novos domínios gerenciados para seus clientes usando o PowerShell:** Consulte [como habilitar o Azure AD DS usando o PowerShell](powershell-create-instance.md) para obter detalhes.

* **Os agentes de administração do CSP não podem executar tarefas de gerenciamento contínuas no domínio gerenciado usando suas credenciais:** Os usuários administradores do CSP não podem executar tarefas de gerenciamento de rotina dentro do domínio gerenciado usando suas credenciais. Esses usuários são externos ao locatário do Azure AD do cliente e suas credenciais não estão disponíveis no locatário do Azure AD do cliente. O Azure AD DS não tem acesso aos hashes de senha Kerberos e NTLM para esses usuários, para que os usuários não possam ser autenticados em domínios gerenciados.

  > [!WARNING]
  > Você deve criar uma conta de usuário no diretório do cliente para executar tarefas de administração contínuas no domínio gerenciado.
  >
  > Você não pode entrar no domínio gerenciado usando as credenciais de um usuário administrador do CSP. Use as credenciais de uma conta de usuário que pertença ao locatário do Azure AD do cliente para fazer isso. Você precisa dessas credenciais para tarefas como unir VMs ao domínio gerenciado, administrar o DNS ou administrar o Política de Grupo.

* **A conta de usuário criada para administração contínua deve ser adicionada ao grupo de *Administradores de DC do AAD* :** o grupo de administradores do *AAD DC* tem privilégios para executar determinadas tarefas de administração delegadas no domínio gerenciado. Essas tarefas incluem a configuração de DNS, a criação de unidades organizacionais e a administração da diretiva de grupo.
    
    Para que um parceiro CSP execute essas tarefas em um domínio gerenciado, uma conta de usuário deve ser criada no locatário do Azure AD do cliente. As credenciais para esta conta devem ser compartilhadas com agentes administradores do parceiro CSP. Além disso, essa conta de usuário deve ser adicionada ao grupo de *Administradores de DC do AAD* para permitir que as tarefas de configuração no domínio gerenciado sejam executadas usando essa conta de usuário.

## <a name="next-steps"></a>Próximas etapas

Para começar, registre-se [no programa CSP do Azure](/partner-center/enrolling-in-the-csp-program). Em seguida, você pode habilitar Azure AD Domain Services usando [o portal do Azure](tutorial-create-instance.md) ou [Azure PowerShell](powershell-create-instance.md).