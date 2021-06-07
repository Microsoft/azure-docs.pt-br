---
title: Diretrizes gerais de segurança da empresa no Azure HDInsight
description: Algumas práticas recomendadas que devem facilitar a implantação e o gerenciamento do Enterprise Security Package.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 92ad8362f75cdf0613d4ee95f39c23aa6d4819bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933573"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Informações gerais e diretrizes de segurança corporativa no Azure HDInsight

Ao implantar um cluster HDInsight seguro, há algumas práticas recomendadas que devem facilitar a implantação e o gerenciamento de cluster. Algumas informações gerais e diretrizes são discutidas aqui.

## <a name="use-of-secure-cluster"></a>Uso do cluster seguro

### <a name="recommended"></a>Recomendadas

* O cluster será usado por vários usuários ao mesmo tempo.
* Os usuários têm diferentes níveis de acesso aos mesmos dados.

### <a name="not-necessary"></a>Não é necessário

* Você vai executar somente trabalhos automatizados (como a conta de usuário único), um cluster padrão é bom o suficiente.
* Você pode fazer a importação de dados usando um cluster padrão e usar a mesma conta de armazenamento em um cluster seguro diferente em que os usuários podem executar trabalhos de análise.

## <a name="use-of-local-account"></a>Uso da conta local

* Se você usar uma conta de usuário compartilhada ou uma conta local, será difícil identificar quem usou a conta para alterar a configuração ou o serviço.
* O uso de contas locais é problemático quando os usuários não fazem mais parte da organização.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Políticas

* Por padrão, o Ranger usa **Deny** como a política.

* Quando o acesso a dados é feito por meio de um serviço onde a autorização está habilitada:
  * O plug-in de autorização do Ranger é invocado e recebe o contexto da solicitação.
  * O Ranger aplica as políticas configuradas para o serviço. Se as políticas de Ranger falharem, a verificação de acesso será adiada para o sistema de arquivos. Alguns serviços como o MapReduce verificam apenas se o arquivo/pasta pertence ao mesmo usuário que está enviando a solicitação. Serviços como o Hive, verifique se há correspondência de propriedade ou permissões de sistema de arquivos apropriadas ( `rwx` ).

* Para o Hive, além de ter as permissões para criar/atualizar/excluir permissões, o usuário deve ter `rwx` permissões no diretório no armazenamento e em todos os subdiretórios.

* As políticas podem ser aplicadas a grupos (preferível) em vez de indivíduos.

* O autorizador do Ranger avaliará todas as políticas do Ranger para esse serviço para cada solicitação. Essa avaliação pode ter um impacto no tempo necessário para aceitar o trabalho ou a consulta.

### <a name="storage-access"></a>Acesso de armazenamento

* Se o tipo de armazenamento for WASB, nenhum token OAuth estará envolvido.
* Se o Ranger tiver realizado a autorização, o acesso de armazenamento ocorrerá usando a identidade gerenciada.
* Se o Ranger não executou nenhuma autorização, o acesso de armazenamento acontece usando o token OAuth do usuário.

### <a name="hierarchical-name-space"></a>Espaço de nome hierárquico

Quando o espaço de nome hierárquico não está habilitado:

* Não há permissões herdadas.
* Somente a permissão FileSystem que funciona é **Storage Data xxxx** Azure role, a ser atribuída ao usuário diretamente no portal do Azure.

### <a name="default-hdfs-permissions"></a>Permissões de HDFS padrão

* Por padrão, os usuários não têm acesso à **/** pasta no HDFS (eles precisam estar na função de proprietário do blob de armazenamento para que o acesso tenha sucesso).
* Para o diretório de preparo para o MapReduce e outros, um diretório específico do usuário é criado e recebe `sticky _wx` permissões. Os usuários podem criar arquivos e pastas abaixo, mas não podem olhar outros itens.

### <a name="url-auth"></a>Autenticação de URL

Se a autenticação de URL estiver habilitada:

* A configuração conterá os prefixos que serão abordados na autenticação de URL (como `adl://` ).
* Se o acesso for para essa URL, o Ranger verificará se o usuário está na lista de permissões.
* O Ranger não verificará nenhuma das políticas refinadas.

## <a name="resource-groups"></a>Grupos de recursos

Use um novo grupo de recursos para cada cluster para que você possa distinguir entre os recursos de cluster.

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSGs, firewalls e gateway interno

* Use NSGs (grupos de segurança de rede) para bloquear redes virtuais.
* Use o firewall para lidar com políticas de acesso de saída.
* Use o gateway interno que não está aberto para a Internet pública.

## <a name="azure-active-directory"></a>Azure Active Directory

O [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (AD do Azure) é o serviço de gerenciamento de identidade e acesso baseado em nuvem da Microsoft.

### <a name="policies"></a>Políticas

* Desabilite a política de acesso condicional usando a política baseada em endereço IP. Isso requer que os pontos de extremidade de serviço sejam habilitados no VNETs em que os clusters são implantados. Se você usar um serviço externo para MFA (algo diferente do AAD), a política baseada em endereço IP não funcionará

* `AllowCloudPasswordValidation` a política é necessária para usuários federados. Como o HDInsight usa o nome de usuário/senha diretamente para obter tokens do Azure AD, essa política deve ser habilitada para todos os usuários federados.

* Habilite pontos de extremidade de serviço se você precisar de bypass de acesso condicional usando IPs confiáveis.

### <a name="groups"></a>Grupos

* Sempre implante clusters com um grupo.
* Use o Azure AD para gerenciar associações de grupo (mais fácil do que tentar gerenciar os serviços individuais no cluster).

### <a name="user-accounts"></a>Contas de usuário

* Use uma conta de usuário exclusiva para cada cenário. Por exemplo, use uma conta para importação, use outra para consulta ou outros trabalhos de processamento.
* Use políticas de Ranger baseadas em grupo em vez de políticas individuais.
* Tenha um plano sobre como gerenciar usuários que não devem ter mais acesso a clusters.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

O [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) (AD DS do Azure) fornece serviços de domínio gerenciados, como ingresso no domínio, diretiva de grupo, LDAP (Lightweight Directory Access Protocol) e autenticação Kerberos/NTLM que é totalmente compatível com o Windows Server Active Directory.

O AD DS do Azure é necessário para clusters seguros para ingressar em um domínio.
O HDInsight não pode depender de controladores de domínio locais ou controladores de domínio personalizados, pois introduz um número excessivo de pontos de falha, compartilhamento de credenciais, permissões de DNS e assim por diante. Para obter mais informações, consulte [perguntas frequentes sobre o Azure AD DS](../../active-directory-domain-services/faqs.md).

### <a name="azure-ad-ds-instance"></a>Instância de AD DS do Azure

* Crie a instância com o `.onmicrosoft.com domain` . Dessa forma, não haverá vários servidores DNS servindo ao domínio.
* Crie um certificado autoassinado para os LDAPs e carregue-o no Azure AD DS.
* Usar uma rede virtual emparelhada para a implantação de clusters (quando você tiver várias equipes implantando clusters do HDInsight com o, isso será útil). Isso garante que você não precisa abrir portas (NSGs) na rede virtual com o controlador de domínio.
* Configure o DNS para a rede virtual corretamente (o nome de domínio AD DS do Azure deve ser resolvido sem nenhuma entrada de arquivo de hosts).
* Se você estiver restringindo o tráfego de saída, verifique se leu o [suporte de firewall no HDInsight](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Propriedades sincronizadas do Azure AD para o Azure AD DS

* O Azure AD Connect é sincronizado do local para o Azure AD.
* O Azure AD DS sincronizações do Azure AD.

O Azure AD DS sincroniza objetos do Azure AD periodicamente. A folha AD DS do Azure na portal do Azure exibe o status de sincronização. Durante cada estágio da sincronização, as propriedades exclusivas podem entrar em conflito e renomear. Preste atenção ao mapeamento de Propriedade do Azure AD para o Azure AD DS.

Para obter mais informações, consulte [população de userPrincipalName do Azure ad](../../active-directory/hybrid/plan-connect-userprincipalname.md)e [como funciona a sincronização de AD DS do Azure](../../active-directory-domain-services/synchronization.md).

### <a name="password-hash-sync"></a>Sincronização de hash de senha

* As senhas são sincronizadas de forma diferente de outros tipos de objeto. Somente os hashes de senha não reversíveis são sincronizados no Azure AD e no Azure AD DS
* O local para o Azure AD deve ser habilitado por meio do AD Connect
* O Azure AD para o Azure AD DS a sincronização é automático (as latências são inferiores a 20 minutos).
* Os hashes de senha são sincronizados somente quando há uma senha alterada. Quando você habilita a sincronização de hash de senha, todas as senhas existentes não são sincronizadas automaticamente à medida que são armazenadas irreversíveis. Quando você altera a senha, os hashes de senha são sincronizados.

### <a name="computer-objects-location"></a>Local dos objetos do computador

Cada cluster é associado a uma única UO. Um usuário interno é provisionado na UO. Todos os nós são ingressados no domínio na mesma UO.

### <a name="active-directory-administrative-tools"></a>Active Directory ferramentas administrativas

Para obter as etapas sobre como instalar as ferramentas administrativas do Active Directory em uma VM do Windows Server, consulte [instalar ferramentas de gerenciamento](../../active-directory-domain-services/tutorial-create-management-vm.md).

## <a name="troubleshooting"></a>Solução de problemas

### <a name="cluster-creation-fails-repeatedly"></a>A criação do cluster falha repetidamente

Motivos mais comuns:

* A configuração de DNS não está correta, ingresso no domínio de nós de cluster falha.
* NSGs são muito restritivos, impedindo o ingresso no domínio.
* A identidade gerenciada não tem permissões suficientes.
* O nome do cluster não é exclusivo nos seis primeiros caracteres (com outro cluster ao vivo ou com um cluster excluído).

## <a name="authentication-setup-and-configuration"></a>Instalação e configuração de autenticação

### <a name="user-principal-name-upn"></a>Nome Principal do Usuário (UPN)

* Use minúsculas para todos os serviços-os UPNs não diferenciam maiúsculas de minúsculas em clusters ESP, mas
* O prefixo UPN deve corresponder a SAMAccountName no Azure AD-DS. A correspondência com o campo de email não é necessária.

### <a name="ldap-properties-in-ambari-configuration"></a>Propriedades LDAP na configuração do Ambari

Para obter uma lista completa das propriedades Ambari que afetam a configuração do cluster HDInsight, consulte [configuração de autenticação LDAP do Ambari](https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap2-4.html).

## <a name="next-steps"></a>Próximas etapas

* [Enterprise Security Package configurações com Azure Active Directory Domain Services no HDInsight](./apache-domain-joined-configure-using-azure-adds.md)

* [Sincronizar Azure Active Directory usuários com um cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md).
