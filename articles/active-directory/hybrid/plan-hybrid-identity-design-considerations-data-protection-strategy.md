---
title: Design de identidade híbrida - estratégia de proteção de dados do Azure | Microsoft Docs
description: Você definirá uma estratégia de proteção de dados para sua solução de identidade híbrida a fim de atender aos requisitos de negócios que definiu.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: bac3f53def6db1038a6dd7e45d7933daa22df9f0
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703845"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definir estratégia de proteção de dados para sua solução de identidade híbrida
Nesta tarefa, você definirá uma estratégia de proteção de dados para sua solução de identidade híbrida a fim de atender aos requisitos de negócios definidos em:

* [Determinar os requisitos para proteção de dados](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Determinar requisitos de gerenciamento de conteúdo](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Determinar requisitos de controle de acesso](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Determinar requisitos de resposta a incidentes](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definir opções de proteção de dados
Como foi explicado em [Determinar requisitos de sincronização de diretório](plan-hybrid-identity-design-considerations-directory-sync-requirements.md), o Microsoft Azure AD pode ser sincronizado com os Serviços de Domínio do Active Directory local (AD DS). Essa integração permite que as organizações usem o Azure Active Directory para verificar as credenciais dos usuários quando eles estiverem tentando acessar os recursos corporativos. Isso pode ser feito para dois cenários: dados em repouso no local e na nuvem. O acesso a dados no AD do Azure requer autenticação de usuário por meio de um STS (serviço de token de segurança).

Uma vez autenticado, o nome UPN pode ser lido do token de autenticação. Em seguida, o sistema de autorização determina a partição replicada e o contêiner correspondente ao domínio do usuário. As informações sobre a existência do usuário, o estado habilitado e a função ajudam o sistema de autorização a determinar se o acesso ao locatário de destino está autorizado para o usuário nessa sessão. Determinadas ações autorizadas (especificamente, criar usuários e redefinir senha) criam uma trilha de auditoria que um administrador locatário utiliza para gerenciar os esforços ou investigações de conformidade.

Mover os dados do seu datacenter local no Armazenamento do Azure por uma conexão com a Internet nem sempre pode ser possível devido ao volume de dados, à disponibilidade da largura de banda ou a outras considerações. O [Serviço de Importação/Exportação do Armazenamento do Azure](../../import-export/storage-import-export-service.md) fornece uma opção baseada em hardware para colocar/recuperar grandes volumes de dados no armazenamento de blob. Ele permite a você enviar discos rígidos [criptografados pelo BitLocker](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn306081(v=ws.11)#BKMK_BL2012R2) diretamente a um datacenter do Azure, onde os operadores de nuvem carregarão o conteúdo para a conta de armazenamento, ou eles podem fazer o download de seus dados do Azure nas unidades a serem retornadas para você. Somente discos criptografados são aceitos nesse processo (usando uma chave do BitLocker gerada pelo serviço durante a configuração do trabalho). A chave do BitLocker é fornecida ao Azure separadamente, oferecendo compartilhamento de chave fora da banda.

Uma vez que os dados em trânsito podem ocorrer em diferentes cenários, também é relevante saber que o Microsoft Azure usa uma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para isolar o tráfego dos locatários uns dos outros, empregando medidas como firewalls no nível de convidado e host, filtragem de pacote IP, bloqueio de porta e pontos de extremidade HTTPS. No entanto, a maioria das comunicações internas do Azure, incluindo infraestrutura para infraestrutura e infraestrutura para cliente (local), também é criptografada. Outro cenário importante é a comunicação em datacenters do Azure. A Microsoft gerencia redes para garantir que nenhuma VM possa representar ou interceptar o endereço IP de outra. O TLS/SSL é usado no acesso ao Armazenamento do Azure ou Bancos de Dados SQL ou na conexão com os Serviços de Nuvem. Nesse caso, o administrador do cliente é responsável por obter um certificado TLS/SSL e implantá-lo em sua infraestrutura de locatário. O tráfego de dados que se movimenta entre máquinas virtuais na mesma implantação ou entre locatários em uma única implantação por meio da Rede Virtual do Microsoft Azure pode ser protegido por meio de protocolos de comunicação criptografada como HTTPS, SSL/TLS, entre outros.

Dependendo de como você tiver respondido às perguntas em [Determinar requisitos de proteção de dados](plan-hybrid-identity-design-considerations-dataprotection-requirements.md), será possível determinar como você quer proteger seus dados e como a solução de identidade híbrida poderá ajudá-lo nesse processo. A tabela a seguir mostra as opções com suporte no Azure que estão disponíveis para cada cenário de proteção de dados.

| Opções de proteção de dados | Em repouso na nuvem | Em repouso no local | Em trânsito |
| --- | --- | --- | --- |
| Criptografia de Unidade de Disco BitLocker |X |X | |
| SQL Server para criptografar bancos de dados |X |X | |
| Criptografia de VM para VM | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Leia [Conformidade por recurso](https://azure.microsoft.com/support/trust-center/services/) na [Central de Confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/) para saber mais sobre as certificações com as quais o serviço do Azure está em conformidade.
> Como as opções de proteção de dados usam uma abordagem multicamada, a comparação entre essas opções não é aplicável para esta tarefa. Certifique-se de que você esteja aproveitando todas as opções disponíveis para cada estado dos dados.
>
>

## <a name="define-content-management-options"></a>Definir opções de gerenciamento de conteúdo

Uma vantagem de usar o AD do Azure para gerenciar uma infraestrutura de identidade híbrida é a total transparência do processo da perspectiva do usuário final. O usuário tenta acessar um recurso compartilhado, o recurso exige autenticação, o usuário tem que enviar uma solicitação de autenticação ao Azure Active Directory para obter o token e acessar o recurso. Todo esse processo ocorre em segundo plano, sem interação do usuário. 

As organizações que se preocupam com a privacidade dos dados normalmente exigem a classificação de dados para sua solução. Se a infraestrutura local atual delas já estiver usando a classificação de dados, será possível usar o Azure Active Directory como o principal repositório para a identidade do usuário. Uma ferramenta comum que é usada no local para a classificação de dados é denominada [Kit de Ferramentas de Classificação de Dados](/previous-versions/tn-archive/hh204743(v=technet.10)) para Windows Server 2012 R2. Essa ferramenta pode ajudar a identificar, classificar e proteger dados em servidores de arquivos na nuvem privada. Também é possível usar a [Classificação Automática de Arquivos](/windows-server/identity/solution-guides/deploy-automatic-file-classification--demonstration-steps-) no Windows Server 2012 para realizar essa tarefa.

Se sua organização não tem classificação de dados definida, mas precisa proteger arquivos confidenciais sem adicionar novos servidores locais, elas podem usar o [Microsoft Azure Rights Management Service](/azure/information-protection/what-is-azure-rms).  O Azure RMS usa políticas de criptografia, identidade e autorização para ajudar a proteger arquivos e email, além de funcionar em vários dispositivos — PCs, tablets e telefones. Como o Azure RMS é um serviço de nuvem, não é necessário configurar explicitamente as relações de confiança com outras organizações para que você possa compartilhar conteúdo protegido com elas. Se eles já tiverem um Microsoft 365 ou um diretório do Azure AD, a colaboração entre organizações terá suporte automaticamente. Você também pode sincronizar apenas os atributos de diretório que Azure RMS precisa para oferecer suporte a uma identidade comum para suas contas de Active Directory locais, usando Azure Active Directory Azure AD Sync (serviços de sincronização) ou Azure AD Connect.

Uma parte essencial do gerenciamento de conteúdo é entender quem está acessando o recurso, portanto, um recurso de registro em log avançado é importante para a solução de gerenciamento de identidades. O AD do Azure fornece log por 30 dias, incluindo:

* Alterações na associação de função (por exemplo: usuário adicionado à função de Administrador Global)
* Atualizações de credencial (por exemplo: alterações de senha)
* Gerenciamento de domínio (por exemplo: verificar um domínio personalizado, remover um domínio)
* Adicionando ou removendo aplicativos
* Gerenciamento de usuários (por exemplo: adição, remoção, atualização de um usuário)
* Adicionando ou removendo licenças

> [!NOTE]
> Leia [Gerenciamento de logs de auditoria e segurança do Microsoft Azure](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) para saber mais sobre como registrar em log recursos no Azure.
> Dependendo de como você tiver respondido às perguntas em [Determinar requisitos de gerenciamento de conteúdo](plan-hybrid-identity-design-considerations-contentmgt-requirements.md), deverá ser capaz de determinar como deseja que o conteúdo seja gerenciado na sua solução de identidade híbrida. Embora todas as opções expostas na Tabela 6 sejam capazes de se integrar ao AD do Azure, é importante definir qual é mais apropriada para suas necessidades de negócios.
>
>

| Opções de gerenciamento de conteúdo | Vantagens | Desvantagens |
| --- | --- | --- |
| Centralizado no local (servidor do Active Directory Rights Management) |Controle total sobre a infraestrutura de servidor responsável por classificar os dados  <br> Recurso interno no Windows Server, não é necessária nenhuma licença nem assinatura adicional <br> Pode ser integrado ao AD do Azure em um cenário híbrido <br> Dá suporte aos recursos de gerenciamento de direitos de informação (IRM) no Microsoft Online Services, como o Exchange Online e o SharePoint Online, bem como Microsoft 365 <br>  Oferece suporte aos produtos de servidor Microsoft no local, como Exchange Server, SharePoint Server e servidores de arquivos que executam o Windows Server e a FCI (Infraestrutura de Classificação de Arquivos). |Manutenção de nível mais alto (acompanhamento de atualizações, configuração e possíveis upgrades), uma vez que a TI é proprietária do servidor <br> Requer uma infraestrutura de servidor local<br> Não aproveita nativamente os recursos do Azure |
| Centralizado na nuvem (Azure RMS) |Mais fácil de gerenciar em comparação com a solução local  <br> Pode ser integrado ao AD DS em um cenário híbrido <br>  Totalmente integrado ao AD do Azure <br> Não requer um servidor local para implantar o serviço <br> Oferece suporte aos produtos de servidor Microsoft no local, como Exchange Server, SharePoint Server e servidores de arquivos que executam o Windows Server e a FCI (Infraestrutura de Classificação de Arquivos) <br> A TI pode ter controle total da chave de seus locatários com o recurso BYOK. |Sua organização deve ter uma assinatura de nuvem que ofereça suporte ao RMS  <br>  Sua organização deve ter um diretório do AD do Azure para oferecer suporte à autenticação de usuário do RMS |
| Híbrido (Azure RMS integrado ao servidor do Active Directory Rights Management local) |Este cenário acumula as vantagens dos outros dois, centralizado no local e na nuvem. |Sua organização deve ter uma assinatura de nuvem que ofereça suporte ao RMS  <br> Sua organização deve ter um diretório do AD do Azure para oferecer suporte à autenticação de usuário do RMS <br>  Exige uma conexão entre o serviço de nuvem do Azure e a infraestrutura local |

## <a name="define-access-control-options"></a>Definir opções de controle de acesso
Ao aproveitar os recursos de autenticação, autorização e controle de acesso disponíveis no Azure Active Directory, você pode permitir que sua empresa use um repositório de identidade central ao mesmo tempo que permite aos usuários e parceiros usarem o SSO (logon único), como mostrado na figura a seguir:

![gerenciamento centralizado](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Gerenciamento centralizado e integração completa a outros diretórios

O Active Directory do Azure fornece logon único a milhares de aplicativos SaaS e aplicativos Web locais. Leia o artigo [Lista de compatibilidade de federação do Azure Active Directory: provedores de identidade terceirizados que podem ser usados para implementar o logon único](how-to-connect-fed-compatibility.md) para obter mais detalhes sobre o SSO de terceiros que foi testado pela Microsoft. Esse recurso permite que a organização implemente uma variedade de cenários B2B enquanto mantém o controle do gerenciamento de identidades e acesso. No entanto, durante o processo de design do B2B, é importante entender o método de autenticação usado pelo parceiro e confirmar se esse método conta com suporte do Azure. Atualmente, os métodos a seguir são suportados pelo Azure Active Directory:

* SAML (Security Assertion Markup Language)
* OAuth
* Kerberos
* Tokens
* Certificados

> [!NOTE]
> Leia [Protocolos de autenticação do Azure Active Directory](/previous-versions/azure/dn151124(v=azure.100)) para saber mais detalhes sobre cada protocolo e suas funcionalidades no Azure.
>
>

Usando o suporte do AD do Azure, os aplicativos de negócios móveis podem usar a mesma experiência fácil de autenticação dos Serviços Móveis para permitir que os funcionários entrem em seus aplicativos móveis com as respectivas credenciais corporativas do Active Directory. Com esse recurso, o Azure Active Directory tem suporte como um provedor de identidade nos Serviços Móveis ao lado de outros provedores de identidade já suportados (que incluem Contas da Microsoft, ID do Facebook, ID do Google e ID do Twitter). Se os aplicativos locais usarem a credencial do usuário localizada no AD DS da empresa, o acesso de parceiros e usuários pela nuvem deverá ser transparente. Você pode gerenciar o controle de acesso condicional do usuário para aplicativos Web (baseados em nuvem), API da Web, serviços de nuvem da Microsoft, aplicativos SaaS de terceiros e aplicativos cliente nativos (móveis) e ter os benefícios de segurança, auditoria e relatórios em um único lugar. No entanto, é recomendável validar a implementação em um ambiente que não seja de produção ou com uma quantidade limitada de usuários.

> [!TIP]
> É importante mencionar que o Azure AD não tem uma Política de Grupo como o AD DS. Para aplicar a política de dispositivos, você precisa de uma solução de gerenciamento de dispositivo móvel, como o [Microsoft Intune](/mem/intune/).
>
>

Quando o usuário é autenticado usando o Azure Active Directory, é importante avaliar o nível de acesso que o usuário tem. O nível de acesso que o usuário tem sobre um recurso pode variar. Embora o Azure Active Directory possa incluir uma camada adicional de segurança controlando o acesso a alguns recursos, lembre-se de que o recurso em si também pode ter sua própria lista de controle de acesso separadamente, como o controle de acesso para arquivos localizados em um Servidor de Arquivos. A figura abaixo resume os níveis de controle de acesso que você pode ter em um cenário híbrido:

![Controle de acesso](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Cada interação no diagrama mostrado na Figura X representa um cenário de controle de acesso que pode ser coberto pelo AD do Azure. Abaixo, você tem uma descrição de cada cenário:

1. Acesso Condicional a aplicativos que são hospedados localmente: é possível usar dispositivos registrados com políticas de acesso para aplicativos configurados para usar o AD FS com o Windows Server 2012 R2.

2. Controle de acesso à portal do Azure: o Azure também permite que você controle o acesso ao portal usando o controle de acesso baseado em função do Azure (RBAC do Azure)). Esse método permite à empresa restringir a quantidade de operações que um indivíduo pode realizar no Portal do Azure. Usando o RBAC do Azure para controlar o acesso ao portal, os administradores de ti podem delegar o acesso usando as seguintes abordagens de gerenciamento de acesso:

   - Atribuição de função com base em grupo: você pode atribuir acesso a grupos do AD do Azure que podem ser sincronizados do seu Active Directory local. Isso permite que você aproveite os investimentos existentes que sua organização já fez em ferramentas e processos para gerenciar os grupos. Também é possível usar o recurso de gerenciamento de grupos delegado do Azure AD Premium.
   - Usar funções internas do Azure: você pode usar três funções: Proprietário, Colaborador e Leitor para garantir que os usuários e grupos tenham permissão para realizar apenas as tarefas necessárias para concluir seus respectivos trabalhos.
   -  Acesso granular aos recursos: você pode atribuir funções a usuários e grupos de uma determinada assinatura, grupo de recursos ou recurso individual do Azure, como um site ou banco de dados. Dessa forma, é possível garantir que os usuários tenham acesso a todos os recursos necessários e nenhum acesso a recursos que não precisem gerenciar.

   > [!NOTE]
   > Se você está criando aplicativos e quer personalizar o controle de acesso para eles, também é possível usar as Funções de Aplicativo do Azure AD para autorização. Examine este [exemplo de WebApp-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) em como criar aplicativo para usar esse recurso.


3. Acesso condicional para aplicativos Microsoft 365 com Microsoft Intune: os administradores de ti podem provisionar políticas de dispositivo de acesso condicional para proteger recursos corporativos e, ao mesmo tempo, permitir que os operadores de informações em dispositivos em conformidade acessem os serviços. 
  
4. Acesso Condicional para aplicativos SaaS: [esse recurso](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) permite configurar regras de acesso da autenticação multifator por aplicativo e a capacidade de bloquear o acesso de usuários que não estão em uma rede confiável. Você pode aplicar as regras de autenticação multifator a todos os usuários atribuídos ao aplicativo ou apenas aos usuários nos grupos de segurança especificados. Os usuários podem ser excluídos do requisito de autenticação multifator se estiverem acessando o aplicativo de um endereço IP de dentro da rede da organização.

Como as opções de controle de acesso usam uma abordagem multicamada, a comparação entre essas opções não são aplicáveis para esta tarefa. Verifique se você está aproveitando todas as opções disponíveis para cada cenário que exige o controle de acesso aos recursos.

## <a name="define-incident-response-options"></a>Definir opções de resposta a incidentes
O Azure Active Directory pode ajudar a TI a identificar possíveis riscos de segurança no ambiente, monitorando a atividade do usuário. A TI pode usar os relatórios de uso e de acesso do Azure Active Directory para obter visibilidade quanto à integridade e segurança do diretório da sua organização. Com essas informações, um administrador de TI pode determinar melhor onde podem estar os possíveis riscos à segurança, de modo que pode fazer planos adequados para reduzi-los.  [assinatura do Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) tem um conjunto de relatórios de segurança que podem permitir à TI obter essas informações. Os [relatórios do Azure Active Directory](../reports-monitoring/overview-reports.md) são categorizados como mostrado abaixo:

* **Relatórios de anomalias**: contêm eventos de entrada que identificamos como anômalos. A meta é que você fique ciente dessas atividades e permitir que você tome uma decisão quanto a um evento ser suspeito ou não.
* **Relatórios de aplicativos integrados**: fornecem um panorama de como os aplicativos em nuvem estão sendo usados na sua organização. O Active Directory do Azure oferece integração com milhares de aplicativos em nuvem.
* **Relatórios de erros**: indicam erros que podem ocorrer ao provisionar contas para aplicativos externos.
* **Relatórios específicos do usuário**: exibem dados de atividade de entrada/dispositivo de um usuário específico.
* **Logs de atividades**: contêm um registro de todos os eventos auditados nas últimas 24 horas, nos últimos 7 dias ou últimos 30 dias, bem como alterações de atividades do grupo e atividades de registro e redefinição de senha.

> [!TIP]
> Outro relatório que também pode ajudar a equipe de Resposta a Incidentes a trabalhar em um caso é o relatório [Usuário com credenciais vazadas](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) . Esse relatório revela as correspondências entre a lista de credenciais vazadas e seu locatário.
>


Outros relatórios internos importantes no Azure Active Directory que podem ser usados durante uma investigação de resposta a incidentes são:

* **Atividade de redefinição de senha**: fornece ao administrador informações sobre como a redefinição de senha está sendo ativamente usada na organização.
* **Atividade de registro de redefinição de senha**: fornece informações sobre quais usuários registraram seus métodos para redefinição de senha e quais métodos eles selecionaram.
* **Atividade do grupo**: fornece um histórico das alterações no grupo (ex: os usuários adicionados ou removidos) que foram iniciadas no Painel de Acesso.

Além do recurso de relatórios principais do Azure Active Directory Premium que você pode usar durante um processo de investigação de Resposta a Incidentes, a TI também pode aproveitar o Relatório de Auditoria para obter informações, como:

* Alterações na associação da função (por exemplo, usuário adicionado à função de Administrador Global)
* Atualizações de credencial (por exemplo, alterações de senha)
* Gerenciamento de domínio (por exemplo, verificar um domínio personalizado, remover um domínio)
* Adicionando ou removendo aplicativos
* Gerenciamento de usuários (por exemplo, adição, remoção, atualização de um usuário)
* Adicionando ou removendo licenças

Como as opções de reposta a incidentes usam uma abordagem multicamada, a comparação entre essas opções não são aplicáveis para esta tarefa. Verifique se você está aproveitando todas as opções disponíveis para cada cenário que exija o uso do recurso de relatórios do AD do Azure como parte do processo de resposta a incidentes da empresa.

## <a name="next-steps"></a>Próximas etapas
[Determinar tarefas de gerenciamento de identidade híbrida](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Consulte Também
[Visão geral sobre as considerações de design](plan-hybrid-identity-design-considerations-overview.md)