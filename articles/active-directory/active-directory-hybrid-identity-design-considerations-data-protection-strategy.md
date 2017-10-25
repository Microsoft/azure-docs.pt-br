---
title: "Considerações de design da identidade híbrida do Azure Active Directory — definir estratégia de proteção de dados| Microsoft Docs"
description: "Você definirá uma estratégia de proteção de dados para sua solução de identidade híbrida a fim de atender aos requisitos de negócios que definiu."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 6c9b7423fa56886104bc6060d25904277b75f30c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definir estratégia de proteção de dados para sua solução de identidade híbrida
Nesta tarefa, você definirá uma estratégia de proteção de dados para sua solução de identidade híbrida a fim de atender aos requisitos de negócios definidos em:

* [Determinar os requisitos para proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Determinar requisitos de gerenciamento de conteúdo](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Determinar requisitos de controle de acesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Determinar requisitos de resposta a incidentes](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definir opções de proteção de dados
Como foi explicado em [Determinar requisitos de sincronização de diretório](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md), o AD do Microsoft Azure pode ser sincronizado com o AD DS (Serviços de Domínio do Active Directory) local. Essa integração permite que as organizações aproveitem o AD do Azure para verificar as credenciais do usuário quando eles estão tentando acessar recursos corporativos. Isso pode ser feito para dois cenários: dados em repouso no local e na nuvem.  O acesso a dados no AD do Azure requer autenticação de usuário por meio de um STS (serviço de token de segurança).

Uma vez autenticado, o UPN (nome de usuário principal) é lido no token de autenticação e a partição replicada, assim como o contêiner correspondente ao domínio do usuário é determinado. As informações sobre a existência do usuário, o estado habilitado e a função são usadas pelo sistema de autorização para determinar se o acesso solicitado ao locatário de destino está autorizado para esse usuário nessa sessão. Determinadas ações autorizadas (especificamente, criar usuários, redefinir senha) criam uma trilha de auditoria que pode ser usada por um administrador locatário para gerenciar os esforços ou investigações de conformidade.

Mover os dados do seu datacenter local no Armazenamento do Azure por uma conexão com a Internet nem sempre pode ser possível devido ao volume de dados, à disponibilidade da largura de banda ou a outras considerações. O [Serviço de Importação/Exportação do Armazenamento do Azure](../storage/common/storage-import-export-service.md) fornece uma opção baseada em hardware para colocar/recuperar grandes volumes de dados no armazenamento de blob. Ele permite a você enviar discos rígidos [criptografados pelo BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) diretamente a um datacenter do Azure, onde os operadores de nuvem carregarão o conteúdo para a conta de armazenamento, ou eles podem baixar seus dados do Azure nas unidades a serem retornadas para você. Somente discos criptografados são aceitos nesse processo (usando uma chave do BitLocker gerada pelo serviço durante a configuração do trabalho). A chave do BitLocker é fornecida ao Azure separadamente, oferecendo compartilhamento de chave fora da banda.

Uma vez que os dados em trânsito podem ocorrer em diferentes cenários, também é relevante saber que o Microsoft Azure usa uma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para isolar o tráfego dos locatários uns dos outros, empregando medidas como firewalls no nível de convidado e host, filtragem de pacote IP, bloqueio de porta e pontos de extremidade HTTPS. No entanto, a maioria das comunicações internas do Azure, incluindo infraestrutura para infraestrutura e infraestrutura para cliente (local), também é criptografada. Outro cenário importante é a comunicação em datacenters do Azure. A Microsoft gerencia redes para garantir que nenhuma VM possa representar ou interceptar o endereço IP de outra. O TLS/SSL é usado no acesso ao Armazenamento do Azure ou Bancos de Dados SQL ou na conexão com os Serviços de Nuvem. Nesse caso, o administrador do cliente é responsável por obter um certificado TLS/SSL e implantá-lo em sua infraestrutura de locatário. O tráfego de dados que se movimenta entre máquinas virtuais na mesma implantação ou entre locatários em uma única implantação por meio da Rede Virtual do Microsoft Azure pode ser protegido por meio de protocolos de comunicação criptografada como HTTPS, SSL/TLS, entre outros.

Dependendo de como você tiver respondido às perguntas em [Determinar requisitos de proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md), será possível determinar como quer proteger seus dados e como a solução de identidade híbrida ajudará nisso. A tabela mostra as opções com suporte no Azure que estão disponíveis para cada cenário de proteção de dados.

| Opções de proteção de dados | Em repouso na nuvem | Em repouso no local | Em trânsito |
| --- | --- | --- | --- |
| Criptografia de Unidade BitLocker |X |X | |
| SQL Server para criptografar bancos de dados |X |X | |
| Criptografia de VM para VM | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Leia [Conformidade por recurso](https://azure.microsoft.com/support/trust-center/services/) na [Central de Confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/) para saber mais sobre as certificações com as quais o serviço do Azure está em conformidade.
> Como as opções de proteção de dados usam uma abordagem multicamada, a comparação entre essas opções não é aplicável para esta tarefa. Certifique-se de que você esteja aproveitando todas as opções disponíveis para cada estado em que os dados estarão.
>
>

## <a name="define-content-management-options"></a>Definir opções de gerenciamento de conteúdo
Uma vantagem de usar o AD do Azure para gerenciar uma infraestrutura de identidade híbrida é a total transparência do processo da perspectiva do usuário final. O usuário tentará acessar um recurso compartilhado, o recurso exigirá autenticação, o usuário terá que enviar uma solicitação de autenticação ao AD do Azure para obter o token e acessar o recurso. Todo esse processo ocorre em segundo plano, sem interação do usuário. Também é possível conceder permissão a um [grupo](active-directory-manage-groups.md#getting-started-with-access-management) de usuários para permitir que eles realizem determinadas ações comuns.

As organizações que se preocupam com a privacidade dos dados normalmente exigem a classificação de dados para sua solução. Se a infraestrutura local atual delas já estiver usando a classificação de dados, será possível aproveitar o AD do Azure como o principal repositório para a identidade do usuário. Uma ferramenta comum que é usada no local para a classificação de dados é denominada [Kit de Ferramentas de Classificação de Dados](https://msdn.microsoft.com/library/Hh204743.aspx) para Windows Server 2012 R2. Essa ferramenta pode ajudar a identificar, classificar e proteger dados em servidores de arquivos na nuvem privada. Também é possível aproveitar a [Classificação Automática de Arquivos](https://technet.microsoft.com/library/hh831672.aspx) no Windows Server 2012 para fazer isso.

Se sua organização não tem classificação de dados definida, mas precisa proteger arquivos confidenciais sem adicionar novos servidores locais, elas podem usar o [Microsoft Azure Rights Management Service](https://technet.microsoft.com/library/JJ585026.aspx).  O Azure RMS usa políticas de criptografia, identidade e autorização para ajudar a proteger arquivos e email, além de funcionar em vários dispositivos — PCs, tablets e telefones. Como o Azure RMS é um serviço de nuvem, não é necessário configurar explicitamente as relações de confiança com outras organizações para que você possa compartilhar conteúdo protegido com elas. Se elas já tiverem um Office 365 ou um diretório do AD do Azure, isso significa que há suporte automático para a colaboração entre organizações. Você também pode sincronizar apenas os atributos de diretório que o Azure RMS precisa para oferecer suporte a uma identidade comum para suas contas locais do Active Directory, usando o AAD Sync (Serviços de Sincronização do Active Directory do Azure) ou o Azure AD Connect.

Uma parte essencial do gerenciamento de conteúdo é entender quem está acessando o recurso, portanto, um recurso de registro em log avançado é importante para a solução de gerenciamento de identidades. O AD do Azure fornece log por 30 dias, incluindo:

* Alterações na associação de função (por exemplo: usuário adicionado à função de Administrador Global)
* Atualizações de credencial (por exemplo: alterações de senha)
* Gerenciamento de domínio (por exemplo: verificar um domínio personalizado, remover um domínio)
* Adicionando ou removendo aplicativos
* Gerenciamento de usuários (por exemplo: adição, remoção, atualização de um usuário)
* Adicionando ou removendo licenças

> [!NOTE]
> Leia [Gerenciamento de logs de auditoria e segurança do Microsoft Azure](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) para saber mais sobre como registrar em log recursos no Azure.
> Dependendo de como você tiver respondido às perguntas em [Determinar requisitos de gerenciamento de conteúdo](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md), deverá ser capaz de determinar como deseja que o conteúdo seja gerenciado na sua solução de identidade híbrida. Embora todas as opções expostas na Tabela 6 sejam capazes de se integrar ao AD do Azure, é importante definir qual é mais apropriada para suas necessidades de negócios.
>
>

| Opções de gerenciamento de conteúdo | Vantagens | Desvantagens |
| --- | --- | --- |
| Centralizado no local (servidor do Active Directory Rights Management) |Controle total sobre a infraestrutura de servidor responsável por classificar os dados  <br> Recurso interno no Windows Server, não é necessária nenhuma licença nem assinatura adicional <br> Pode ser integrado ao AD do Azure em um cenário híbrido <br> Oferece suporte a recursos de IRM (Gerenciamento de Direitos de Informação) no Microsoft Online Services, como o Exchange Online e o SharePoint Online, bem como o Office 365 <br> Oferece suporte aos produtos de servidor Microsoft no local, como Exchange Server, SharePoint Server e servidores de arquivos que executam o Windows Server e a FCI (Infraestrutura de Classificação de Arquivos). |Manutenção de nível mais alto (acompanhamento de atualizações, configuração e possíveis upgrades), uma vez que a TI é proprietária do servidor  <br> Requer uma infraestrutura de servidor local<br> Não aproveita originalmente os recursos do Azure |
| Centralizado na nuvem (Azure RMS) |Mais fácil de gerenciar em comparação com a solução local  <br> Pode ser integrado ao AD DS em um cenário híbrido <br>  Totalmente integrado ao AD do Azure <br> Não requer um servidor local para implantar o serviço <br> Oferece suporte aos produtos de servidor Microsoft no local, como Exchange Server, SharePoint Server e servidores de arquivos que executam o Windows Server e a FCI (Infraestrutura de Classificação de Arquivos) <br> A TI pode ter controle total da chave de seus locatários com o recurso BYOK. |Sua organização deve ter uma assinatura de nuvem que ofereça suporte ao RMS  <br> Sua organização deve ter um diretório do AD do Azure para oferecer suporte à autenticação de usuário do RMS |
| Híbrido (Azure RMS integrado ao servidor do Active Directory Rights Management local) |Este cenário acumula as vantagens dos outros dois, centralizado no local e na nuvem. |Sua organização deve ter uma assinatura de nuvem que ofereça suporte ao RMS  <br> Sua organização deve ter um diretório do AD do Azure para oferecer suporte à autenticação de usuário do RMS <br> Exige uma conexão entre o serviço de nuvem do Azure e a infraestrutura local |

## <a name="define-access-control-options"></a>Definir opções de controle de acesso
Ao aproveitar os recursos de autenticação, autorização e controle de acesso disponíveis no AD do Azure, você poderá permitir que sua empresa use um repositório de identidades central ao mesmo tempo que permite aos usuários e parceiros usar o SSO (logon único), como mostrado na figura abaixo:

![](./media/hybrid-id-design-considerations/centralized-management.png)

Gerenciamento centralizado e integração completa a outros diretórios

O Active Directory do Azure fornece logon único a milhares de aplicativos SaaS e aplicativos Web locais. Leia o artigo [Lista de compatibilidade de federação do Active Directory do Azure: provedores de identidade terceirizados que podem ser usados para implementar o logon único](https://msdn.microsoft.com/library/azure/jj679342.aspx) para obter mais detalhes sobre o SSO de terceiros que foi testado pela Microsoft. Esse recurso permite que a organização implemente uma variedade de cenários B2B enquanto mantém o controle do gerenciamento de identidades e acesso. No entanto, durante o processo de design do B2B, é importante entender o método de autenticação que será usado pelo parceiro e confirmar se esse método conta com suporte do Azure. Atualmente, estes são os métodos com suporte do AD do Azure:

* SAML (Security Assertion Markup Language)
* OAuth
* Kerberos
* Tokens
* Certificados

> [!NOTE]
> Leia [Protocolos de autenticação do Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) para saber mais detalhes sobre cada protocolo e suas funcionalidades no Azure.
>
>

Usando o suporte do AD do Azure, os aplicativos de negócios móveis podem usar a mesma experiência fácil de autenticação dos Serviços Móveis para permitir que os funcionários entrem em seus aplicativos móveis com as respectivas credenciais corporativas do Active Directory. Com esse recurso, o AD do Azure tem suporte como um provedor de identidade nos Serviços Móveis ao lado de outros provedores de identidade aos quais já oferecemos suporte (que incluem Contas da Microsoft, ID do Facebook, ID do Google e ID do Twitter). Se os aplicativos locais usarem a credencial do usuário localizada no AD DS da empresa, o acesso de parceiros e usuários pela nuvem deverá ser transparente. Você pode gerenciar o controle de acesso condicional do usuário para aplicativos Web (baseados em nuvem), API da Web, serviços de nuvem da Microsoft, aplicativos SaaS de terceiros e aplicativos cliente (móveis) nativos, e ter os benefícios de segurança, auditoria, relatórios, em um só lugar. No entanto, é recomendável validar isso em um ambiente que não seja de produção ou com uma quantidade limitada de usuários.

> [!TIP]
> É importante mencionar que o Azure AD não tem uma Política de Grupo como o AD DS. Para aplicar a política de dispositivos, você precisará de uma solução de gerenciamento de dispositivo móvel, como o [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).
>
>

Quando o usuário é autenticado usando o AD do Azure, é importante avaliar o nível de acesso que ele terá. O nível de acesso que o usuário terá em um recurso pode variar. Embora o AD do Azure possa incluir uma camada adicional de segurança controlando o acesso a alguns recursos, você também deve se lembrar de que o recurso em si também pode ter sua própria lista de controle de acesso separadamente, como o controle de acesso para arquivos localizados em um Servidor de Arquivos. A figura abaixo resume os níveis de controle de acesso que você pode ter em um cenário híbrido:

![](./media/hybrid-id-design-considerations/accesscontrol.png)

Cada interação no diagrama mostrado na Figura X representa um cenário de controle de acesso que pode ser coberto pelo AD do Azure. Abaixo, você tem uma descrição de cada cenário:

1. Acesso Condicional a aplicativos que são hospedados localmente: é possível usar dispositivos registrados com políticas de acesso para aplicativos configurados para usar o AD FS com o Windows Server 2012 R2. Para obter mais informações sobre como configurar o acesso condicional para local, consulte [Configurando o acesso condicional local usando o registro do dispositivo do Active Directory do Azure](active-directory-conditional-access.md).

2. Controle de acesso ao Portal do Azure: o Azure também permite controlar o acesso ao Portal usando o RBAC (Controle de Acesso Baseado em Função). Esse método permite à empresa restringir a quantidade de operações que um indivíduo pode realizar no Portal do Azure. Ao usar o RBAC para controlar o acesso ao portal, os administradores de TI podem delegar o acesso usando as seguintes abordagens de gerenciamento de acesso:

* Atribuição de função com base em grupo: você pode atribuir acesso a grupos do AD do Azure que podem ser sincronizados do seu Active Directory local. Isso permite aproveitar os investimentos existentes que sua organização já fez em ferramentas e processos para gerenciar os grupos. Também é possível usar o recurso de gerenciamento de grupos delegado do Azure AD Premium.
* Aproveitar funções internas do Azure: você pode usar três funções: Proprietário, Colaborador e Leitor para garantir que os usuários e grupos tenham permissão para fazer apenas as tarefas que precisam para concluírem os respectivos trabalhos.
* Acesso granular aos recursos: você pode atribuir funções a usuários e grupos de uma determinada assinatura, grupo de recursos ou recurso individual do Azure, como um site ou banco de dados. Dessa forma, é possível garantir que os usuários tenham acesso a todos os recursos necessários e nenhum acesso a recursos que não precisem gerenciar.

> [!NOTE]
> Se você está criando aplicativos e quer personalizar o controle de acesso para eles, também é possível usar as Funções de Aplicativo do Azure AD para autorização. Examine este [exemplo de WebApp-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) em como criar aplicativo para usar esse recurso.
>
>

3. Acesso condicional para aplicativos do Office 365 com o Microsoft Intune: os administradores de TI podem provisionar políticas de dispositivo de acesso condicional para proteger recursos corporativos e, simultaneamente, permitir que os operadores de informações em dispositivos em conformidade acessem os serviços. Para mais informações, consulte [Políticas de dispositivo de acesso condicional para serviços do Office 365](active-directory-conditional-access-device-policies.md).

4. Acesso Condicional para aplicativos SaaS: [esse recurso](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) permite configurar regras de acesso da autenticação multifator por aplicativo e a capacidade de bloquear o acesso de usuários que não estão em uma rede confiável. Você pode aplicar as regras de autenticação multifator a todos os usuários atribuídos ao aplicativo ou apenas aos usuários nos grupos de segurança especificados. Os usuários podem ser excluídos do requisito de autenticação multifator se estiverem acessando o aplicativo de um endereço IP de dentro da rede da organização.

Como as opções de controle de acesso usam uma abordagem multicamada, a comparação entre essas opções não são aplicáveis para esta tarefa. Verifique se você está aproveitando todas as opções disponíveis para cada cenário que exige o controle de acesso aos recursos.

## <a name="define-incident-response-options"></a>Definir opções de resposta a incidentes
O AD do Azure pode ajudar a TI a identificar possíveis riscos à segurança no ambiente monitorando a atividade do usuário; a TI pode aproveitar o recurso de relatório de Acesso e Uso do AD do Azure para ganhar visibilidade da integridade e da segurança do diretório da organização. Com essas informações, um administrador de TI pode determinar melhor onde podem estar os possíveis riscos à segurança, de modo que pode fazer planos adequados para reduzi-los.  [assinatura do Azure AD Premium](active-directory-get-started-premium.md) tem um conjunto de relatórios de segurança que podem permitir à TI obter essas informações. [relatórios do AD do Azure](active-directory-view-access-usage-reports.md) são categorizados como mostrado abaixo:

* **Relatórios de anomalias**: contêm eventos de entrada que nós identificamos como anômalos. Nosso objetivo é que você fique ciente dessas atividades e permitir que você possa tomar uma decisão quanto a um evento ser suspeito ou não.
* **Relatórios de aplicativos integrados**: fornecem um panorama de como os aplicativos em nuvem estão sendo usados na sua organização. O Active Directory do Azure oferece integração com milhares de aplicativos em nuvem.
* **Relatórios de erros**: indicam erros que podem ocorrer ao provisionar contas para aplicativos externos.
* **Relatórios específicos do usuário**: exibem dados de atividade de entrada/dispositivo de um usuário específico.
* **Logs de atividades**: contêm um registro de todos os eventos auditados nas últimas 24 horas, nos últimos 7 dias ou últimos 30 dias, bem como alterações de atividades do grupo e atividades de registro e redefinição de senha.

> [!TIP]
> Outro relatório que também pode ajudar a equipe de Resposta a Incidentes a trabalhar em um caso é o relatório [Usuário com credenciais vazadas](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx) .  Esse relatório revela as correspondências entre essa lista de credenciais vazadas e seu locatário.
>
>

Outros relatórios internos importantes no AD do Azure que podem ser usados durante uma investigação de resposta a incidentes são:

* **Atividade de redefinição de senha**: fornece ao administrador informações sobre como a redefinição de senha está sendo ativamente usada na organização.
* **Atividade de registro de redefinição de senha**: fornece informações sobre quais usuários registraram seus métodos para redefinição de senha e quais métodos eles selecionaram.
* **Atividade do grupo**: fornece um histórico das alterações no grupo (ex: os usuários adicionados ou removidos) que foram iniciadas no Painel de Acesso.

Além do recurso de relatórios principais disponível no Azure AD Premium que pode ser aproveitado durante um processo de investigação de Resposta a Incidentes, a TI também pode aproveitar o Relatório de Auditoria para obter informações como:

* Alterações na associação de função (por exemplo: usuário adicionado à função de Administrador Global)
* Atualizações de credencial (por exemplo: alterações de senha)
* Gerenciamento de domínio (por exemplo: verificar um domínio personalizado, remover um domínio)
* Adicionando ou removendo aplicativos
* Gerenciamento de usuários (por exemplo: adição, remoção, atualização de um usuário)
* Adicionando ou removendo licenças

Como as opções de reposta a incidentes usam uma abordagem multicamada, a comparação entre essas opções não são aplicáveis para esta tarefa. Verifique se você está aproveitando todas as opções disponíveis para cada cenário que exija o uso do recurso de relatórios do AD do Azure como parte do processo de resposta a incidentes da empresa.

## <a name="next-steps"></a>Próximas etapas
[Determinar tarefas de gerenciamento de identidade híbrida](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Consulte também
[Visão geral sobre as considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)
