---
title: Cenários comuns de implantação para Azure AD Domain Services | Microsoft Docs
description: Saiba mais sobre alguns dos cenários comuns e casos de uso para Azure Active Directory Domain Services fornecer valor e atender às necessidades dos negócios.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: justinha
ms.openlocfilehash: d33698ed2f9ac53aae3c836acd54f19a4b72ceef
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619021"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Casos de uso comuns e cenários para Azure Active Directory Domain Services

O Azure AD DS (Azure Active Directory Domain Services) fornece serviços de domínio gerenciado, como ingresso no domínio, política de grupo, protocolo LDAP e autenticação Kerberos/NTLM. O Azure AD DS se integra ao locatário existente do Azure AD, o que permite aos usuários entrarem usando suas credenciais atuais. Você usa esses serviços de domínio sem a necessidade de implantar, gerenciar e aplicar patch nos controladores de domínio na nuvem, o que fornece um aumento mais suave dos recursos locais para o Azure.

Este artigo descreve alguns cenários de negócios comuns em que o Azure AD DS fornece valor e atende a essas necessidades.

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Maneiras comuns de fornecer soluções de identidade na nuvem

Ao migrar cargas de trabalho existentes para a nuvem, os aplicativos reconhecidos pelo diretório poderão usar o LDAP para acesso de leitura e gravação a um diretório local do AD DS. Aplicativos em execução no Windows Server normalmente são implantados em VMs (máquinas virtuais) ingressadas em domínio, para que possam ser gerenciados com segurança usando a Política de Grupo. Para autenticar usuários finais, os aplicativos também podem depender da autenticação integrada do Windows, como a autenticação Kerberos ou NTLM.

Geralmente, os administradores de TI usam uma das seguintes soluções para fornecer um serviço de identidade a aplicativos executados no Azure:

* Configurar uma conexão VPN site a site entre as cargas de trabalho executadas no Azure e o ambiente local do AD DS.
    * Os controladores de domínio locais, por sua vez, fornecem autenticação por meio da conexão VPN.
* Criar réplica dos controladores de domínio usando as VMs (máquinas virtuais) do Azure para estender o domínio/floresta do AD DS do local.
    * Os controladores de domínio executados em VMs do Azure fornecem autenticação e replicam informações de diretório entre o ambiente do AD DS local.
* Implantar um ambiente autônomo do AD DS no Azure usando controladores de domínio executados em VMs do Azure.
    * Os controladores de domínio executados em VMs do Azure fornecem autenticação, mas as informações de diretório não são replicadas entre o ambiente do AD DS local.

Com essas abordagens, as conexões VPN com o diretório local tornam os aplicativos vulneráveis a falhas de rede ou interrupções transitórias. Se você implantar controladores de domínio usando VMs no Azure, a equipe de TI precisará gerenciar as VMs e, então, proteger, aplicar patches, monitorar, fazer backup e solucionar os problemas delas.

O Azure AD DS oferece alternativas para a necessidade de criar conexões VPN de volta para um ambiente local do AD DS ou executar e gerenciar VMs no Azure para fornecer serviços de identidade. Como um serviço gerenciado, o Azure AD DS reduz a complexidade da criação de uma solução de identidade integrada para ambientes híbridos e somente em nuvem.

> [!div class="nextstepaction"]
> [Comparar o Azure AD DS com o Azure AD e o AD DS autogerenciado em VMs do Azure ou local][compare]

## <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure AD DS para organizações híbridas

Muitas organizações executam uma infraestrutura híbrida que inclui cargas de trabalho locais e de aplicativo. Aplicativos herdados migrados para o Azure como parte de uma estratégia de lift-and-shift podem usar conexões LDAP tradicionais para fornecer informações de identidade. Para apoiar essa infraestrutura híbrida, as informações de identidade de um ambiente local do AD DS podem ser sincronizadas com um locatário do Azure AD. Então, o Azure AD DS fornece esses aplicativos herdados no Azure com uma origem de identidade, sem a necessidade de configurar e gerenciar a conectividade do aplicativo com os serviços de diretório local.

Veja um exemplo da Litware Corporation, uma organização híbrida que executa no local e em recursos do Azure:

![Azure Active Directory Domain Services para uma organização híbrida que inclui sincronização local](./media/overview/synced-tenant.png)

* Os aplicativos e as cargas de trabalho de servidor que exigem serviços de domínio são implantados em uma rede virtual no Azure.
    * Isso pode incluir aplicativos herdados migrados para o Azure como parte de uma estratégia de lift-and-shift.
* A Litware Corporation implantou o [Azure AD Connect][azure-ad-connect] para sincronizar informações de identidade do diretório local para seu locatário do Azure AD.
    * As informações de identidade sincronizadas incluem contas de usuário e associações de grupo.
* A equipe de TI da Litware habilita o Azure AD DS para o locatário do Azure AD dessa forma ou em uma rede virtual emparelhada.
* Os aplicativos e as VMs implantados na rede virtual do Azure podem utilizar recursos do Azure AD DS como ingresso no domínio, leitura LDAP, associação LDAP, autenticação NTLM e Kerberos e Política de Grupo.

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes do AD DS locais. A instalação do Azure AD Connect em um domínio gerenciado a fim de sincronizar objetos com o Azure AD não é um procedimento compatível.

## <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure AD DS para organizações somente na nuvem

Um locatário somente em nuvem do Azure AD não tem uma origem de identidade local. Contas de usuário e associações de grupo, por exemplo, são criadas e gerenciadas diretamente no Azure AD.

Agora, vamos ver um exemplo da Contoso, uma organização somente em nuvem que usa o Azure AD para gerenciar identidades. Todas as identidades de usuário, suas credenciais e associações de grupo são criadas e gerenciadas no Azure AD. Não há outras configurações do Azure AD Connect para sincronizar informações de identidade de um diretório local.

![Azure Active Directory Domain Services para uma organização somente em nuvem sem sincronização local](./media/overview/cloud-only-tenant.png)

* Os aplicativos e as cargas de trabalho de servidor que exigem serviços de domínio são implantados em uma rede virtual no Azure.
* A equipe de TI da Contoso habilita o Azure AD DS para o locatário do Azure AD dessa forma ou em uma rede virtual emparelhada.
* Os aplicativos e as VMs implantados na rede virtual do Azure podem utilizar recursos do Azure AD DS como ingresso no domínio, leitura LDAP, associação LDAP, autenticação NTLM e Kerberos e Política de Grupo.

## <a name="secure-administration-of-azure-virtual-machines"></a>Administração segura de máquinas virtuais do Azure

Para permitir que você use um único conjunto de credenciais do AD, as VMs (máquinas virtuais) do Azure podem ser unidas a um domínio gerenciado do Azure AD DS. Essa abordagem reduz os problemas de gerenciamento de credenciais, como a manutenção de contas de administrador local em cada VM ou a contas e senhas separadas entre ambientes.

As VMs que ingressaram em um domínio gerenciado também podem ser administradas e protegidas usando a política de grupo. As linhas de base de segurança necessárias podem ser aplicadas às VMs para bloqueá-las de acordo com as diretrizes de segurança corporativa. Por exemplo, você pode usar recursos de gerenciamento de política de grupo para restringir os tipos de aplicativos que podem ser iniciados na VM.

![Administração simplificada de máquinas virtuais do Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Vejamos um cenário de exemplo comum. Como servidores e outras infra-estruturas atingem o fim da vida útil, a contoso deseja mover os aplicativos atualmente hospedados localmente para a nuvem. Seu padrão de ti atual exige que os servidores que hospedam aplicativos corporativos devem ser ingressados no domínio e gerenciados usando a diretiva de grupo.

O administrador de ti da Contoso prefere o ingresso no domínio de VMs implantadas no Azure para facilitar a administração, pois os usuários podem entrar usando suas credenciais corporativas. Quando ingressado no domínio, as VMs também podem ser configuradas para atender às linhas de base de segurança necessárias usando objetos de política de grupo (GPOs). A contoso prefere não implantar, monitorar e gerenciar seus próprios controladores de domínio no Azure.

O Azure AD DS é uma ótima opção para esse caso de uso. Um domínio gerenciado permite que você ingresse em VMs de domínio, use um único conjunto de credenciais e aplique a política de grupo. E, como é um domínio gerenciado, você não precisa configurar e manter os controladores de domínio por conta própria.

### <a name="deployment-notes"></a>Observações de implantação

As seguintes considerações de implantação se aplicam a este exemplo de caso de uso:

* Por padrão, os domínios gerenciados usam uma única estrutura de UO (unidade organizacional) simples. Todas as VMs ingressadas no domínio estão em uma única UO. Se desejar, você pode criar [UOs personalizadas][custom-ou].
* O Azure AD DS usa um GPO interno para os contêineres usuários e computadores. Para controle adicional, você pode [criar GPOs personalizados][create-gpo] e direcioná-los para UOs personalizadas.
* O Azure AD DS dá suporte ao esquema de objeto de computador do AD base. Você não pode estender o esquema do objeto do computador.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>Aplicativos locais de comparação de precisão e deslocamento que usam autenticação de associação LDAP

Como um cenário de exemplo, a contoso tem um aplicativo local que foi adquirido de um ISV há muitos anos. O aplicativo está atualmente no modo de manutenção pelo ISV e solicitar alterações ao aplicativo é extremamente caro. Esse aplicativo tem um front-end baseado na Web que coleta as credenciais do usuário usando um formulário da Web e autentica os usuários executando uma associação LDAP ao ambiente de AD DS local.

![Associação LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

A contoso gostaria de migrar esse aplicativo para o Azure. O aplicativo deve continuar funcionando no estado em que se encontra, sem necessidade de alterações. Além disso, os usuários devem ser capazes de autenticar usando suas credenciais corporativas existentes e sem treinamento adicional. Ele deve ser transparente para os usuários finais onde o aplicativo está em execução.

Para esse cenário, o Azure AD DS permite que os aplicativos executem associações LDAP como parte do processo de autenticação. Os aplicativos locais herdados podem se deslocar e mudar para o Azure e continuar a autenticar os usuários sem qualquer alteração na configuração ou na experiência do usuário.

### <a name="deployment-notes"></a>Observações de implantação

As seguintes considerações de implantação se aplicam a este exemplo de caso de uso:

* Certifique-se de que o aplicativo não precise modificar/gravar no diretório. Não há suporte para o acesso de gravação LDAP para um domínio gerenciado.
* Você não pode alterar as senhas diretamente em um domínio gerenciado. Os usuários finais podem alterar sua senha usando o [mecanismo de alteração de senha de autoatendimento do Azure ad][sspr] ou no diretório local. Essas alterações são sincronizadas automaticamente e disponíveis no domínio gerenciado.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Aplicativos locais de comparação de precisão e deslocamento que usam LDAP leitura para acessar o diretório

Como o cenário de exemplo anterior, vamos supor que a contoso tenha um aplicativo de linha de negócios (LOB) local que foi desenvolvido há quase uma década. Este aplicativo tem reconhecimento de diretório e foi projetado para usar LDAP para ler informações/atributos sobre os usuários de AD DS. O aplicativo não modifica atributos ou, de outra forma, grava no diretório.

A contoso deseja migrar esse aplicativo para o Azure e desativar o hardware local de envelhecimento que hospeda esse aplicativo no momento. O aplicativo não pode ser reescrito para usar APIs de diretório modernas, como a API de Microsoft Graph baseada em REST. Uma opção de comparação de precisão e deslocamento é desejada onde o aplicativo pode ser migrado para ser executado na nuvem, sem modificar o código ou reescrever o aplicativo.

Para ajudar nesse cenário, o Azure AD DS permite que os aplicativos executem leituras LDAP no domínio gerenciado para obter as informações de atributo de que precisa. O aplicativo não precisa ser reescrito, portanto, um deslocamento e uma mudança no Azure permitem que os usuários continuem a usar o aplicativo sem perceber que há uma alteração no local em que é executado.

### <a name="deployment-notes"></a>Observações de implantação

As seguintes considerações de implantação se aplicam a este exemplo de caso de uso:

* Certifique-se de que o aplicativo não precise modificar/gravar no diretório. Não há suporte para o acesso de gravação LDAP para um domínio gerenciado.
* Verifique se o aplicativo não precisa de um esquema de Active Directory personalizado/estendido. Não há suporte para extensões de esquema no AD DS do Azure.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Migrar um aplicativo de serviço ou daemon local para o Azure

Alguns aplicativos incluem várias camadas, em que uma das camadas precisa executar chamadas autenticadas para uma camada de back-end, como um banco de dados. As contas de serviço do AD são comumente usadas nesses cenários. Ao migrar e deslocar aplicativos para o Azure, o Azure AD DS permite que você continue a usar contas de serviço da mesma maneira. Você pode optar por usar a mesma conta de serviço que é sincronizada do seu diretório local para o Azure AD ou criar uma UO personalizada e, em seguida, criar uma conta de serviço separada nessa UO. Com qualquer abordagem, os aplicativos continuam a funcionar da mesma maneira de fazer chamadas autenticadas para outras camadas e serviços.

![Conta de serviço usando WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Neste cenário de exemplo, a contoso tem um aplicativo de cofre de software personalizado que inclui um front-end da Web, um SQL Server e um servidor FTP de back-end. A autenticação integrada do Windows usando contas de serviço autentica o front-end da Web para o servidor FTP. O front-end da Web é configurado para execução como uma conta de serviço. O servidor back-end está configurado para autorizar o acesso da conta de serviço para o front-end da Web. A contoso não deseja implantar e gerenciar suas próprias VMs do controlador de domínio na nuvem para mover esse aplicativo para o Azure.

Para esse cenário, os servidores que hospedam o front-end da Web, o SQL Server e o servidor FTP podem ser migrados para VMs do Azure e ingressados em um domínio gerenciado. As VMs podem usar a mesma conta de serviço em seu diretório local para fins de autenticação do aplicativo, que é sincronizado por meio do Azure AD usando o Azure AD Connect.

### <a name="deployment-notes"></a>Observações de implantação

As seguintes considerações de implantação se aplicam a este exemplo de caso de uso:

* Certifique-se de que os aplicativos usem um nome de usuário e senha para autenticação. Não há suporte para autenticação com base em certificado ou cartão inteligente no Azure AD DS.
* Você não pode alterar as senhas diretamente em um domínio gerenciado. Os usuários finais podem alterar sua senha usando o [mecanismo de alteração de senha de autoatendimento do Azure ad][sspr] ou no diretório local. Essas alterações são sincronizadas automaticamente e disponíveis no domínio gerenciado.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Implantações de serviços de área de trabalho remota do Windows Server no Azure

Você pode usar o Azure AD DS para fornecer serviços de domínio gerenciado para servidores de área de trabalho remota implantados no Azure.

Para obter mais informações sobre esse cenário de implantação, consulte [como integrar o Azure AD Domain Services com sua implantação do RDS][windows-rds].

## <a name="domain-joined-hdinsight-clusters"></a>Clusters HDInsight ingressados no domínio

Você pode configurar um cluster do Azure HDInsight que tenha ingressado em um domínio gerenciado com o Apache Ranger habilitado. Você pode criar e aplicar políticas de Hive por meio do Apache Ranger e permitir que usuários, como cientistas de dados, se conectem ao hive usando ferramentas baseadas em ODBC como Excel ou tableau. Continuamos a trabalhar para adicionar outras cargas de trabalho, como HBase, Spark e Storm ao HDInsight ingressado no domínio.

Para obter mais informações sobre esse cenário de implantação, consulte [como configurar clusters HDInsight ingressados no domínio][hdinsight]

## <a name="next-steps"></a>Próximas etapas

Para começar, [crie e configure um Azure Active Directory Domain Services domínio gerenciado][tutorial-create-instance].

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[tutorial-create-instance]: tutorial-create-instance.md
[custom-ou]: create-ou.md
[create-gpo]: manage-group-policy.md
[sspr]: ../active-directory/authentication/overview-authentication.md#self-service-password-reset
[compare]: compare-identity-solutions.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds