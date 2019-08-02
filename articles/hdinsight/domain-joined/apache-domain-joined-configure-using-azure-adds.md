---
title: Configuração do Enterprise Security Package usando Azure Active Directory Domain Services - Azure HDInsight
description: Saiba como instalar e configurar um cluster do HDInsight Enterprise Security Package usando o Azure Active Directory Domain Services.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: seodec18
ms.date: 04/23/2019
ms.openlocfilehash: 1ad3c446df2f2ce62024dfdda589669653f65ef4
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488701"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Configurar um cluster HDInsight com o Enterprise Security Package usando o Azure Active Directory Domain Services DS

Clusters de Enterprise Security Package fornecem acesso de vários usuários em clusters de HDInsight do Azure. Clusters de HDInsight com ESP estão conectados a um domínio para que os usuários do domínio podem usar suas credenciais de domínio para autenticar com os clusters e executar trabalhos de big data.

Neste artigo, você aprenderá como configurar um cluster HDInsight com ESP usando o Azure Active Directory Domain Services (AD do Azure-DS).

> [!NOTE]  
> O ESP está geralmente disponível no HDInsight 3,6 e 4,0 para tipos de cluster: Apache Spark, interativo, Apache Hadoop e HBase. O ESP para Apache Kafka tipo de cluster está em versão prévia.

## <a name="enable-azure-ad-ds"></a>Habilitar o Microsoft Azure Active Directory DS

> [!NOTE]  
> Somente administradores de inquilinos têm os privilégios para habilitar o Azure Active Directory -DS. Se o armazenamento de cluster for Azure Data Lake Storage (ADLS) Gen1 ou Gen2, você deverá desabilitar a MFA (autenticação multifator) somente para os usuários que precisarem acessar o cluster usando as autenticações Kerberos básicas. Você pode usar [IPs confiáveis](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) ou [acesso condicional](../../active-directory/conditional-access/overview.md) para desabilitar a MFA para usuários específicos somente quando eles estiverem acessando o intervalo de IP de VNET do cluster HDInsight. Se você estiver usando o acesso condicional, verifique se o ponto de extremidade de serviço do AD está habilitado na VNET do HDInsight.
>
> Se o armazenamento de cluster for o Armazenamento de Blobs do Azure (WASB), não desabilite a MFA.

Habilitar o Azure AD-DS é um pré-requisito antes de criar um cluster HDInsight com ESP. Para obter mais informações, consulte [Enable Azure Active Directory Domain Services using the Azure portal](../../active-directory-domain-services/create-instance.md) (Habilitar o Azure Active Directory Domain Services usando o Portal do Azure). 

Quando o Azure AD-DS é habilitado, todos os usuários e objetos começam a ser sincronizados do AAD (Azure Active Directory) para o Azure AD-DS por padrão. A duração da operação de sincronização depende do número de objetos no Azure AD. A sincronização pode levar alguns dias para centenas de milhares de objetos. 

O nome de domínio que você usa com o Azure AD-DS deve ter 39 caracteres ou menos para trabalhar com o HDInsight.

Você pode optar por sincronizar apenas os grupos que precisam de acesso aos clusters HDInsight. Essa opção de sincronizar apenas determinados grupos é chamada de *sincronização com escopo*. Consulte [Configurar a sincronização com escopo do Microsoft Azure AD para um domínio gerenciado](../../active-directory-domain-services/scoped-synchronization.md) para ver as instruções.

Ao ativar o LDAP seguro, coloque o nome do domínio no nome do assunto e o nome alternativo do assunto no certificado. Por exemplo, se o nome do domínio for *contoso100.onmicrosoft.com*, verifique se o nome exato existe no nome do assunto do certificado e no nome alternativo do assunto. Para obter mais informações, consulte [Configurar LDAP seguro para um domínio gerenciado do Microsoft Azure Active Directory DS](../../active-directory-domain-services/configure-ldaps.md). Abaixo, há um exemplo de criação de um certificado autoassinado e o nome do domínio (*contoso100.onmicrosoft.com*) em Nome do assunto e nome do DNS (nome alternativo do assunto):

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Verificar o status de integridade do Azure AD-DS
Exiba o status de integridade dos Azure Active Directory Domain Services selecionando **Integridade** na categoria **Gerenciar**. Verifique se o status do Azure AD-DS está verde (em execução) e se a sincronização foi concluída.

![Integridade do Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Criar e autorizar uma identidade gerenciada

Uma **identidade gerenciada atribuída pelo usuário** é usada para simplificar e proteger operações de serviços do domínio. Quando você atribui a função de Colaborador de serviços de domínio do HDInsight para identidade gerenciada, ele pode ler, criar, modificar e excluir operações de serviços de domínio. Determinadas operações de serviços de domínio, como a criação de UOs e entidades de serviço, são necessárias para o Enterprise Security Package do HDInsight. As identidades gerenciadas podem ser criadas em qualquer assinatura. Para obter mais informações sobre identidades gerenciadas em geral, consulte [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). Para obter mais informações sobre como as identidades gerenciadas funcionam no Azure HDInsight, consulte [identidades gerenciadas no Azure hdinsight](../hdinsight-managed-identities.md).

Para configurar clusters de ESP, crie uma identidade gerenciada atribuída pelo usuário, se você ainda não tiver uma. Confira [Criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) para obter instruções. Em seguida, atribua a função **Colaborador de serviços de domínio do HDInsight** à identidade gerenciada no controle de acesso do Azure AD-DS (os privilégios de administrador do AAD-DS são necessários para fazer essa atribuição de função).

![Controle de acesso do Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Atribuir a função **do Colaborador de Serviços de Domínio do HDInsight** garante que essa identidade tenha acesso adequado (em nome de) para executar operações de serviços do domínio, como a criação de UOs, exclusões de UOs etc. no domínio do AAD-DS.

Após a criação da identidade gerenciada e o recebimento da função correta, o administrador do AAD-DS pode definir quem pode usar essa identidade gerenciada. Para configurar usuários para a identidade gerenciada, o administrador deve selecionar a identidade gerenciada no portal e depois clicar em **Controle de Acesso (IAM)** em **Visão geral**. Em seguida, à direita, atribua a função **Operador de identidade gerenciada** aos usuários ou grupos que desejam criar clusters HDInsight ESP. Por exemplo, o administrador do AAD-DS pode atribuir essa função ao grupo **MarketingTeam** para a identidade gerenciada do **sjmsi** , conforme mostrado na imagem a seguir. Isso garantirá que as pessoas certas na organização tenham acesso para usar essa identidade gerenciada com o objetivo de criar clusters de ESP.

![Atribuição da Função de Operador de Identidade Gerenciada do HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Considerações de rede

> [!NOTE]  
> Azure AD DS deve ser implantado em uma vNET do Azure Resource Manager (ARM) com base. Redes virtuais clássicas não são suportadas pelo Azure AD-DS. Por favor, consulte [Ativar os Serviços de Domínio do Active Directory do Azure usando o portal do Azure](../../active-directory-domain-services/active-directory-ds-getting-started-network.md) para obter mais detalhes.

Depois de habilitar o Azure AD-DS, um servidor DNS (Serviço de Nomes de Domínio) local é executado nas VMs (máquinas virtuais) do AD. Configure sua VNET (rede virtual) do Azure AD-DS para usar esses servidores DNS personalizados. Para localizar os endereços IP corretos, selecione **Propriedades** na categoria **Gerenciar** e examine os Endereços IP listados abaixo de **Endereço IP na Rede Virtual**.

![Localizar endereços IP para servidores DNS locais](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Altere a configuração dos servidores DNS na VNET do Azure AD-DS para usar esses IPs personalizados selecionando **Servidores DNS** na categoria **Configurações**. Em seguida, clique no botão de opção ao lado de **Personalizado**, insira o primeiro endereço IP na caixa de texto abaixo e clique em **Salvar**. Adicione outros Endereços IP usando as mesmas etapas.

![Atualizar a configuração do VNET DNS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

É mais fácil colocar a instância do Microsoft Azure Active Directory DS e o cluster HDInsight na mesma rede virtual do Azure. Se você planeja usar VNETs diferentes, emparelhe essas redes virtuais para que o controlador de domínio fique visível às VMs de HDI. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../../virtual-network/virtual-network-peering-overview.md). 

Após o emparelhamento das VNETs, configure a VNET do HDInsight para usar um servidor DNS personalizado e insira os IPs privados do Azure AD-DS como os endereços de servidor DNS. Quando as duas VNETs usarem os mesmos servidores DNS, seu nome de domínio personalizado será resolvido para o IP correto e será acessível do HDInsight. Por exemplo, se o nome de `contoso.com` domínio for depois dessa etapa `ping contoso.com` , o deverá ser resolvido para o IP do Azure AD-DS correto.

![Configurar Servidores DNS Personalizados para VNET emparelhada](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Se você estiver usando regras de NSG (Grupo de Segurança de Rede) em sua sub-rede do HDInsight, deverá permitir os [IPs necessários](../hdinsight-management-ip-addresses.md) para o tráfego de entrada e de saída. 

**Para testar** se sua rede estiver configurada corretamente, adicione uma VM do Windows à Sub-rede/VNET do HDInsight e execute o ping no nome de domínio (que deve resolver para um IP), depois execute **ldp.exe** para acessar o domínio do Azure AD-DS. Em seguida, **adicione essa VM do Windows ao domínio para confirmar** que todas as chamadas RPC necessárias tenham êxito entre o cliente e o servidor. Você também pode usar **nslookup** para confirmar o acesso de rede à sua conta de armazenamento ou qualquer banco de dados externo que você possa usar (por exemplo, metastore externo do Hive ou banco de dados do Ranger DB).
Você deve certificar-se de que todas as [portas necessárias](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) estejam na lista de permissões nas regras do Grupo de Segurança de Rede da sub-rede AAD-DS, se o AAD-DS estiver protegido por um NSG. Se a junção de domínio dessa VM do Windows for bem-sucedida, você poderá prosseguir para a próxima etapa e criar clusters de ESP.

## <a name="create-a-hdinsight-cluster-with-esp"></a>Crie um cluster HDInsight com ESP

Após a configuração correta das etapas anteriores, a próxima etapa é criar o cluster HDInsight com ESP habilitado. Ao criar um cluster HDInsight, você pode habilitar o Enterprise Security Package na guia **personalizada**. Se você preferir usar um modelo do Azure Resource Manager para implantação, use a experiência do portal uma vez e baixe o modelo previamente preenchido na última página "Resumo" para reutilização futura.

> [!NOTE]  
> Os primeiros seis caracteres dos nomes de cluster do ESP devem ser exclusivos em seu ambiente. Por exemplo, se você tem vários clusters ESP em diferentes VNETs, escolha uma convenção de nomenclatura que garanta que os primeiros seis caracteres nos nomes de cluster sejam exclusivos.

![Validação de domínio do pacote de segurança empresarial do Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Depois de habilitar o ESP, configurações incorretas comuns relacionadas a Microsoft Azure Active Directory DS serão automaticamente detectadas e validadas. Depois de corrigir esses erros, você pode prosseguir com a próxima etapa: 

![Falha na validação de domínio do pacote de segurança empresarial do Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Quando você cria um cluster HDInsight ingressado no domínio, você deve fornecer os seguintes parâmetros:

- **Usuário administrador de cluster**: Escolha um administrador para o cluster do Azure AD-DS sincronizado. Essa conta de domínio já deve estar sincronizada e disponível no Azure Active Directory-DS.

- **Grupos de acesso de cluster**: Os grupos de segurança cujos usuários você deseja sincronizar e ter acesso ao cluster devem estar disponíveis no Azure AD-DS. Por exemplo, o grupo HiveUsers. Para obter mais informações, consulte [Criar um grupo e adicionar membros no Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **URL de LDAPS**: Um exemplo é `ldaps://contoso.com:636`.

A seguinte captura de tela mostra uma configuração bem-sucedida no portal do Azure:

![Configuração dos Serviços de Domínio do Active Directory do Azure HDInsight ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

A identidade gerenciada criada por você pode ser escolhida no menu suspenso de identidade gerenciada atribuída pelo usuário durante a criação de um novo cluster.

![Configuração dos Serviços de Domínio do Active Directory do Azure HDInsight ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).

## <a name="next-steps"></a>Próximas etapas

* Para configurar as políticas do Hive e executar as consultas do Apache Hive, consulte [Configurar políticas do Apache Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md).
* Para usar SSH para conectar clusters HDInsight com ESP, consulte [Usar SSH com Apache Hadoop baseado em Linux no HDInsight do Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
