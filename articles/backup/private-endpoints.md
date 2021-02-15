---
title: Pontos de extremidade privados
description: Entenda o processo de criação de pontos de extremidade privados para o backup do Azure e os cenários em que o uso de pontos de extremidade privados ajuda a manter a segurança de seus recursos.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: a22da7341e3ebeff29bc784cfff0cc8aeb87fb9b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362430"
---
# <a name="private-endpoints-for-azure-backup"></a>Pontos de extremidade privados para o backup do Azure

O backup do Azure permite que você faça backup e restaure seus dados de seus cofres de serviços de recuperação usando [pontos de extremidade privados](../private-link/private-endpoint-overview.md). Os pontos de extremidade privados usam um ou mais endereços IP privados de sua VNet, colocando efetivamente o serviço em sua VNet.

Este artigo o ajudará a entender o processo de criação de pontos de extremidade privados para o backup do Azure e os cenários em que o uso de pontos de extremidade privados ajuda a manter a segurança de seus recursos.

## <a name="before-you-start"></a>Antes de começar

- Pontos de extremidade privados só podem ser criados para novos cofres de serviços de recuperação (que não têm nenhum item registrado no cofre). Portanto, os pontos de extremidade privados devem ser criados antes de tentar proteger os itens para o cofre.
- Uma rede virtual pode conter pontos de extremidade privados para vários cofres de serviços de recuperação. Além disso, um cofre dos serviços de recuperação pode ter pontos de extremidade privados para ele em várias redes virtuais. No entanto, o número máximo de pontos de extremidade privados que podem ser criados para um cofre é 12.
- Depois que um ponto de extremidade privado for criado para um cofre, o cofre será bloqueado. Ele não poderá ser acessado (para backups e restaurações) de redes que contêm um ponto de extremidade privado para o cofre. Se todos os pontos de extremidade privados para o cofre forem removidos, o cofre poderá ser acessado de todas as redes.
- Uma conexão de ponto de extremidade privada para backup usa um total de 11 IPs privados em sua sub-rede, incluindo aqueles usados pelo backup do Azure para armazenamento. Esse número pode ser maior (até 25) para determinadas regiões do Azure. Portanto, sugerimos que você tenha IPs privados suficientes disponíveis ao tentar criar pontos de extremidade privados para backup.
- Embora um cofre dos serviços de recuperação seja usado pelo (ambos) backup e Azure Site Recovery do Azure, este artigo aborda o uso de pontos de extremidade privados somente para o backup do Azure.
- No momento, o Azure Active Directory não dá suporte a pontos de extremidade privados. Portanto, os IPs e FQDNs necessários para Azure Active Directory trabalhar em uma região precisarão ter acesso de saída da rede protegida ao executar o backup de bancos de dados em VMs do Azure e fazer backup usando o agente MARS. Você também pode usar marcas NSG e marcas de firewall do Azure para permitir o acesso ao Azure AD, conforme aplicável.
- As redes virtuais com políticas de rede não têm suporte para pontos de extremidade privados. Você precisará desabilitar as políticas de rede antes de continuar.
- Você precisa registrar novamente o provedor de recursos dos serviços de recuperação com a assinatura se o tiver registrado antes de maio de 1 2020. Para registrar novamente o provedor, acesse sua assinatura no portal do Azure, navegue até provedor de **recursos** na barra de navegação à esquerda, selecione **Microsoft. recoveryservices** e selecione **registrar novamente**.
- Não há suporte para a [restauração entre regiões](backup-create-rs-vault.md#set-cross-region-restore) para SQL e SAP Hana backups de banco de dados se o cofre tiver pontos de extremidade privados habilitados.
- Ao mover um cofre dos serviços de recuperação que já usa pontos de extremidade privados para um novo locatário, você precisará atualizar o cofre dos serviços de recuperação para recriar e reconfigurar a identidade gerenciada do cofre e criar novos pontos de extremidade privados, conforme necessário (o que deve estar no novo locatário). Se isso não for feito, as operações de backup e restauração começarão a falhar. Além disso, qualquer permissão de RBAC (controle de acesso baseado em função) configurada na assinatura precisará ser reconfigurada.

## <a name="recommended-and-supported-scenarios"></a>Cenários recomendados e com suporte

Embora os pontos de extremidade privados estejam habilitados para o cofre, eles são usados para backup e restauração de cargas de trabalho do SQL e do SAP HANA somente em uma VM do Azure e no backup do agente MARS. Você também pode usar o cofre para backup de outras cargas de trabalho (eles não precisarão de pontos de extremidade privados). Além do backup de cargas de trabalho do SQL e do SAP HANA e do backup usando o agente MARS, os pontos de extremidade privados também são usados para executar a recuperação de arquivos para o backup de VM do Azure. Para obter mais informações, confira a tabela a seguir:

| Backup de cargas de trabalho na VM do Azure (SQL, SAP HANA), backup usando o agente MARS | O uso de pontos de extremidade privados é recomendado para permitir backup e restauração sem a necessidade de adicionar a uma lista de permissões quaisquer IPs/FQDNs para o backup do Azure ou o armazenamento do Azure de suas redes virtuais. Nesse cenário, verifique se as VMs que hospedam bancos de dados SQL podem alcançar IPs ou FQDNs do Azure AD. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Backup de VM do Azure**                                         | O backup da VM não exige que você permita o acesso a IPs ou FQDNs. Portanto, ele não requer pontos de extremidade privados para backup e restauração de discos.  <br><br>   No entanto, a recuperação de arquivo de um cofre que contém pontos de extremidade privados seria restrita a redes virtuais que contêm um ponto final privado para o cofre. <br><br>    Ao usar os discos não gerenciados do ACL'ed, verifique se a conta de armazenamento que contém os discos permite o acesso a **serviços confiáveis da Microsoft** se for ACL'ed. |
| **Backup de arquivos do Azure**                                      | Os backups de arquivos do Azure são armazenados na conta de armazenamento local. Portanto, ele não requer pontos de extremidade privados para backup e restauração. |

## <a name="get-started-with-creating-private-endpoints-for-backup"></a>Introdução à criação de pontos de extremidade privados para backup

As seções a seguir discutem as etapas envolvidas na criação e no uso de pontos de extremidade privados para o backup do Azure dentro de suas redes virtuais.

>[!IMPORTANT]
> É altamente recomendável que você siga as etapas na mesma sequência mencionada neste documento. Não fazer isso pode levar ao cofre que está sendo renderizado incompatível para usar pontos de extremidade privados e exigir que você reinicie o processo com um novo cofre.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Pontos de extremidade privados para backup podem ser criados somente para cofres de serviços de recuperação que não têm nenhum item protegido (ou que nenhum item tentou ser protegido ou registrado nele no passado). Portanto, sugerimos que você crie um novo cofre com o qual começar. Para obter mais informações sobre como criar um novo cofre, consulte  [criar e configurar um cofre dos serviços de recuperação](backup-create-rs-vault.md).

Consulte [esta seção](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) para saber como criar um cofre usando o cliente Azure Resource Manager. Isso cria um cofre com sua identidade gerenciada já habilitada.

## <a name="enable-managed-identity-for-your-vault"></a>Habilitar identidade gerenciada para seu cofre

Identidades gerenciadas permitem que o cofre crie e use pontos de extremidade privados. Esta seção fala sobre como habilitar a identidade gerenciada para seu cofre.

1. Vá para o cofre dos serviços de recuperação-> **identidade**.

    ![Alterar o status de identidade para ativado](./media/private-endpoints/identity-status-on.png)

1. Altere o **status** para **ativado** e selecione **salvar**.

1. Uma **ID de objeto** é gerada, que é a identidade gerenciada do cofre.

    >[!NOTE]
    >Uma vez habilitada, a identidade gerenciada **não** deve ser desabilitada (até mesmo temporariamente). Desabilitar a identidade gerenciada pode levar a um comportamento inconsistente.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Conceder permissões ao cofre para criar pontos de extremidade privados necessários

Para criar os pontos de extremidade privados necessários para o backup do Azure, o cofre (a identidade gerenciada do cofre) deve ter permissões para os seguintes grupos de recursos:

- O grupo de recursos que contém a VNet de destino
- O grupo de recursos em que os pontos de extremidade privados devem ser criados
- O grupo de recursos que contém as zonas de DNS privado, conforme discutido em detalhes [aqui](#create-private-endpoints-for-azure-backup)

Recomendamos que você conceda a função de **colaborador** para esses três grupos de recursos ao cofre (identidade gerenciada). As etapas a seguir descrevem como fazer isso para um grupo de recursos específico (isso precisa ser feito para cada um dos três grupos de recursos):

1. Vá para o grupo de recursos e navegue até **controle de acesso (iam)** na barra esquerda.
1. Uma vez no **controle de acesso**, vá para **Adicionar uma atribuição de função**.

    ![Adicionar uma atribuição de função](./media/private-endpoints/add-role-assignment.png)

1. No painel **Adicionar atribuição de função** , escolha **colaborador** como a **função** e use o **nome** do cofre como a entidade de **segurança**. Selecione seu cofre e selecione **salvar** quando terminar.

    ![Escolher função e entidade de segurança](./media/private-endpoints/choose-role-and-principal.png)

Para gerenciar permissões em um nível mais granular, consulte [criar funções e permissões manualmente](#create-roles-and-permissions-manually).

## <a name="create-private-endpoints-for-azure-backup"></a>Criar pontos de extremidade privados para o backup do Azure

Esta seção explica como criar um ponto de extremidade privado para seu cofre.

1. Navegue até o cofre criado acima e vá para **conexões de ponto de extremidade privado** na barra de navegação à esquerda. Selecione **+ ponto de extremidade privado** na parte superior para começar a criar um novo ponto de extremidade privado para este cofre.

    ![Criar novo ponto de extremidade privado](./media/private-endpoints/new-private-endpoint.png)

1. Uma vez no processo **criar ponto de extremidade privado** , será necessário especificar detalhes para criar sua conexão de ponto de extremidade privada.
  
    1. **Noções básicas**: Preencha os detalhes básicos para seus pontos de extremidade privados. A região deve ser a mesma do cofre e o recurso que está sendo submetido a backup.

        ![Preencher detalhes básicos](./media/private-endpoints/basics-tab.png)

    1. **Recurso**: essa guia exige que você selecione o recurso de PaaS para o qual você deseja criar a conexão. Selecione **Microsoft. recoveryservices/cofres** do tipo de recurso para sua assinatura desejada. Depois de concluído, escolha o nome do seu cofre de serviços de recuperação como o **recurso** e **AzureBackup** como o **subrecurso de destino**.

        ![Selecione o recurso para sua conexão](./media/private-endpoints/resource-tab.png)

    1. **Configuração**: em configuração, especifique a rede virtual e a sub-rede em que você deseja que o ponto de extremidade privado seja criado. Essa será a vnet em que a VM está presente.

        Para se conectar de forma privada, você precisa dos registros DNS necessários. Com base na sua configuração de rede, você pode escolher um dos seguintes:

          - Integre seu ponto de extremidade privado a uma zona DNS privada: selecione **Sim** se desejar integrá-lo.
          - Usar seu servidor DNS personalizado: selecione **não** se desejar usar seu próprio servidor DNS.

        O gerenciamento de registros DNS para ambos são [descritos posteriormente](#manage-dns-records).

          ![Especificar a rede virtual e a sub-rede](./media/private-endpoints/configuration-tab.png)

    1. Opcionalmente, você pode adicionar **marcas** para seu ponto de extremidade particular.
    1. Continue a **examinar + criar** depois de inserir detalhes. Quando a validação for concluída, selecione **criar** para criar o ponto de extremidade privado.

## <a name="approve-private-endpoints"></a>Aprovar pontos de extremidade privados

Se o usuário que cria o ponto de extremidade privado também for o proprietário do cofre dos serviços de recuperação, o ponto de extremidade privado criado acima será aprovado automaticamente. Caso contrário, o proprietário do cofre deve aprovar o ponto de extremidade privado antes de poder usá-lo. Esta seção aborda a aprovação manual de pontos de extremidade privados por meio do portal do Azure.

Consulte [aprovação manual de pontos de extremidade privados usando o cliente Azure Resource Manager](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) para usar o cliente Azure Resource Manager para aprovar pontos de extremidade privados.

1. No cofre dos serviços de recuperação, navegue até **conexões de ponto de extremidade privadas** na barra esquerda.
1. Selecione a conexão de ponto de extremidade privada que você deseja aprovar.
1. Selecione **aprovar** na barra superior. Você também pode selecionar **rejeitar** ou **remover** se quiser rejeitar ou excluir a conexão do ponto de extremidade.

    ![Aprovar pontos de extremidade privados](./media/private-endpoints/approve-private-endpoints.png)

## <a name="manage-dns-records"></a>Gerenciar registros DNS

Conforme descrito anteriormente, você precisa dos registros DNS necessários em suas zonas DNS particulares ou servidores para se conectar de forma privada. Você pode integrar seu ponto de extremidade privado diretamente com as zonas DNS privadas do Azure ou usar seus servidores DNS personalizados para conseguir isso, com base nas suas preferências de rede. Isso deverá ser feito para todos os três serviços: backup, BLOBs e filas.

### <a name="when-integrating-private-endpoints-with-azure-private-dns-zones"></a>Ao integrar pontos de extremidade privados com as zonas DNS privadas do Azure

Se você optar por integrar seu ponto de extremidade privado a zonas DNS privadas, o backup adicionará os registros DNS necessários. Você pode exibir as zonas DNS privadas que estão sendo usadas na **configuração DNS** do ponto de extremidade privado. Se essas zonas DNS não estiverem presentes, elas serão criadas automaticamente ao criar o ponto de extremidade privado. No entanto, você deve verificar se sua rede virtual (que contém os recursos de backup) está corretamente vinculada a todas as três zonas DNS privadas, conforme descrito abaixo.

![Configuração de DNS na zona DNS privada do Azure](./media/private-endpoints/dns-configuration.png)

#### <a name="validate-virtual-network-links-in-private-dns-zones"></a>Validar links de rede virtual em zonas DNS privadas

Para **cada zona DNS privada** listada acima (para backup, BLOBs e filas), faça o seguinte:

1. Navegue até a opção respectivos **links de rede virtual** na barra de navegação à esquerda.
1. Você deve ser capaz de ver uma entrada para a rede virtual para a qual você criou o ponto de extremidade privado, como aquele mostrado abaixo:

    ![Rede virtual para ponto de extremidade privado](./media/private-endpoints/virtual-network-links.png)

1. Se você não vir uma entrada, adicione um link de rede virtual a todas as zonas DNS que não as têm.

    ![Adicionar link de rede virtual](./media/private-endpoints/add-virtual-network-link.png)

### <a name="when-using-custom-dns-server-or-host-files"></a>Ao usar o servidor DNS personalizado ou arquivos de host

Se você estiver usando seus servidores DNS personalizados, será necessário criar as zonas DNS necessárias e adicionar os registros DNS necessários aos pontos de extremidade privados aos seus servidores DNS. Para BLOBs e filas, você também pode usar encaminhadores condicionais.

#### <a name="for-the-backup-service"></a>Para o serviço de backup

1. No servidor DNS, crie uma zona DNS para backup de acordo com a seguinte convenção de nomenclatura:

    |Zona |Serviço |
    |---------|---------|
    |`privatelink.<geo>.backup.windowsazure.com`   |  Backup        |

    >[!NOTE]
    > No texto acima, `<geo>` refere-se ao código de região (por exemplo, *eus* e *ne* para leste dos EUA e Europa setentrional respectivamente). Consulte as seguintes listas para códigos de regiões:
    >
    > - [Todas as nuvens públicas](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
    > - [China](https://docs.microsoft.com/azure/china/resources-developer-guide#check-endpoints-in-azure)
    > - [Alemanha](https://docs.microsoft.com/azure/germany/germany-developer-guide#endpoint-mapping)
    > - [US Gov](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide)

1. Em seguida, precisamos adicionar os registros DNS necessários. Para exibir os registros que precisam ser adicionados à zona DNS de backup, navegue até o ponto de extremidade privado que você criou acima e vá para a opção **configuração de DNS** na barra de navegação à esquerda.

    ![Configuração de DNS para o servidor DNS personalizado](./media/private-endpoints/custom-dns-configuration.png)

1. Adicione uma entrada para cada FQDN e IP exibido como um registro de tipo em sua zona DNS para backup. Se você estiver usando um arquivo de host para resolução de nomes, faça entradas correspondentes no arquivo de host para cada IP e FQDN de acordo com o seguinte formato:

    `<private ip><space><backup service privatelink FQDN>`

>[!NOTE]
>Conforme mostrado na captura de tela acima, os FQDNs representam `xxxxxxxx.<geo>.backup.windowsazure.com` e não `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` . Nesses casos, certifique-se de incluir (e, se necessário, adicionar) o de `.privatelink.` acordo com o formato declarado.

#### <a name="for-blob-and-queue-services"></a>Para serviços de BLOB e fila

Para BLOBs e filas, você pode usar encaminhadores condicionais ou criar zonas DNS no seu servidor DNS.

##### <a name="if-using-conditional-forwarders"></a>Se estiver usando encaminhadores condicionais

Se você estiver usando encaminhadores condicionais, adicione encaminhadores para FQDNs de BLOB e de fila da seguinte maneira:

|FQDN  |IP  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  168.63.129.16       |
|`privatelink.queue.core.windows.net`     | 168.63.129.16        |

##### <a name="if-using-private-dns-zones"></a>Se estiver usando zonas DNS privadas

Se você estiver usando zonas DNS para BLOBs e filas, precisará primeiro criar essas zonas DNS e, posteriormente, adicionar os registros A necessários.

|Zona |Serviço  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  Blob     |
|`privatelink.queue.core.windows.net`     | Fila        |

Neste momento, criaremos apenas as zonas para BLOBs e filas ao usar servidores DNS personalizados. A adição de registros DNS será feita posteriormente em duas etapas:

1. Quando você registra a primeira instância de backup, ou seja, quando você configura o backup pela primeira vez
1. Quando você executa o primeiro backup

Executaremos essas etapas nas seções a seguir.

## <a name="use-private-endpoints-for-backup"></a>Usar pontos de extremidade privados para backup

Depois que os pontos de extremidade privados criados para o cofre em sua VNet tiverem sido aprovados, você poderá começar a usá-los para executar backups e restaurações.

>[!IMPORTANT]
>Verifique se você concluiu todas as etapas mencionadas acima no documento com êxito antes de continuar. Para recapitular, você deve ter concluído as etapas na seguinte lista de verificação:
>
>1. Criado um (novo) cofre de serviços de recuperação
>2. Habilitou o cofre para usar a identidade gerenciada atribuída pelo sistema
>3. Permissões relevantes atribuídas à identidade gerenciada do cofre
>4. Criou um ponto de extremidade privado para seu cofre
>5. Aprovado o ponto de extremidade privado (se não aprovado automaticamente)
>6. Garantiu que todos os registros DNS sejam adicionados adequadamente (exceto registros de BLOB e fila para servidores personalizados, que serão discutidos nas seções a seguir)

### <a name="check-vm-connectivity"></a>Verificar conectividade da VM

Na VM na rede bloqueada, verifique o seguinte:

1. A VM deve ter acesso ao AAD.
2. Execute o **nslookup** na URL de backup ( `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` ) de sua VM, para garantir a conectividade. Isso deve retornar o IP privado atribuído em sua rede virtual.

### <a name="configure-backup"></a>Configurar backup

Depois de garantir que a lista de verificação e o acesso a foram concluídos com êxito, você pode continuar a configurar o backup de cargas de trabalho para o cofre. Se você estiver usando um servidor DNS personalizado, precisará adicionar entradas DNS para BLOBs e filas que estão disponíveis após a configuração do primeiro backup.

#### <a name="dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration"></a>Registros DNS para BLOBs e filas (somente para arquivos de host/servidores DNS personalizados) após o primeiro registro

Depois de configurar o backup para pelo menos um recurso em um cofre do ponto de extremidade privado habilitado, adicione os registros DNS necessários para BLOBs e filas, conforme descrito abaixo.

1. Navegue até o grupo de recursos e procure o ponto de extremidade privado que você criou.
1. Além do nome do ponto de extremidade privado fornecido por você, você verá dois pontos de extremidades mais privados sendo criados. Elas começam com `<the name of the private endpoint>_ecs` e são sufixadas `_blob` e, `_queue` respectivamente.

    ![Recursos de ponto de extremidade privados](./media/private-endpoints/private-endpoint-resources.png)

1. Navegue até cada um desses pontos de extremidade privados. Na opção configuração de DNS para cada um dos dois pontos de extremidade privados, você verá um registro com e um FQDN e um endereço IP. Adicione ambos ao seu servidor DNS personalizado, além daqueles descritos anteriormente.
Se você estiver usando um arquivo de host, faça entradas correspondentes no arquivo de host para cada IP/FQDN de acordo com o seguinte formato:

    `<private ip><space><blob service privatelink FQDN>`<br>
    `<private ip><space><queue service privatelink FQDN>`

    ![Configuração de DNS do blob](./media/private-endpoints/blob-dns-configuration.png)

Além do que está acima, há outra entrada necessária após o primeiro backup, que é discutido [posteriormente](#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup).

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-and-sap-hana"></a>Backup e restauração de cargas de trabalho na VM do Azure (SQL e SAP HANA)

Depois que o ponto de extremidade privado é criado e aprovado, nenhuma outra alteração é necessária do lado do cliente para usar o ponto de extremidade privado (a menos que você esteja usando grupos de disponibilidade do SQL, que discutiremos mais adiante nesta seção). Toda a comunicação e transferência de dados de sua rede segura para o cofre serão executadas por meio do ponto de extremidade privado. No entanto, se você remover pontos de extremidade privados para o cofre depois que um servidor (SQL ou SAP HANA) tiver sido registrado nele, você precisará registrar novamente o contêiner com o cofre. Você não precisa interromper a proteção para eles.

#### <a name="dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup"></a>Registros DNS para BLOBs (somente para arquivos de host/servidores DNS personalizados) após o primeiro backup

Depois de executar o primeiro backup e você estiver usando um servidor DNS personalizado (sem o encaminhamento condicional), é provável que o backup falhe. Se isso acontecer:

1. Navegue até o grupo de recursos e procure o ponto de extremidade privado que você criou.
1. Além dos três pontos de extremidade privados discutidos anteriormente, agora você verá um quarto ponto de extremidades privado com seu nome começando com `<the name of the private endpoint>_prot` e com o sufixo `_blob` .

    ![Endpoing particular com sufixo "Prot"](./media/private-endpoints/private-endpoint-prot.png)

1. Navegue até esse novo ponto de extremidade privado. Na opção configuração de DNS, você verá um registro com um FQDN e um endereço IP. Adicione-os ao seu servidor DNS privado, além daqueles descritos anteriormente.

    Se você estiver usando um arquivo de host, faça as entradas correspondentes no arquivo de host para cada IP e FQDN de acordo com o seguinte formato:

    `<private ip><space><blob service privatelink FQDN>`

>[!NOTE]
>Neste ponto, você deve ser capaz de executar o **nslookup** da VM e resolver para endereços IP privados quando terminar nas URLs de armazenamento e backup do cofre.

### <a name="when-using-sql-availability-groups"></a>Ao usar grupos de disponibilidade do SQL

Ao usar os grupos de disponibilidade do SQL (AG), você precisará provisionar o encaminhamento condicional no DNS do AG personalizado conforme descrito abaixo:

1. Entre no controlador de domínio.
1. No aplicativo DNS, adicione encaminhadores condicionais para todas as três zonas DNS (backup, BLOBs e filas) para o IP do host 168.63.129.16 ou o endereço IP do servidor DNS personalizado, conforme necessário. As capturas de tela a seguir mostram quando você está encaminhando para o IP do host do Azure. Se você estiver usando seu próprio servidor DNS, substitua pelo IP do seu servidor DNS.

    ![Encaminhadores condicionais no Gerenciador DNS](./media/private-endpoints/dns-manager.png)

    ![Novo encaminhador condicional](./media/private-endpoints/new-conditional-forwarder.png)

### <a name="backup-and-restore-through-mars-agent"></a>Backup e restauração por meio do agente MARS

Ao usar o agente MARS para fazer backup de seus recursos locais, verifique se sua rede local (contendo seus recursos de backup) está emparelhada com a VNet do Azure que contém um ponto de extremidade privado para o cofre, para que você possa usá-lo. Em seguida, você pode continuar a instalar o agente MARS e configurar o backup conforme detalhado aqui. No entanto, você deve garantir que toda a comunicação para o backup ocorra somente por meio da rede emparelhada.

Mas se você remover pontos de extremidade privados para o cofre depois que um agente MARS tiver sido registrado nele, você precisará registrar novamente o contêiner com o cofre. Você não precisa interromper a proteção para eles.

## <a name="additional-topics"></a>Tópicos adicionais

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Criar um cofre dos serviços de recuperação usando o cliente Azure Resource Manager

Você pode criar o cofre dos serviços de recuperação e habilitar sua identidade gerenciada (habilitar a identidade gerenciada é necessário, como veremos mais tarde) usando o cliente Azure Resource Manager. Um exemplo para fazer isso é compartilhado abaixo:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

O arquivo JSON acima deve ter o seguinte conteúdo:

JSON de solicitação:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

JSON de resposta:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>O cofre criado neste exemplo por meio do cliente Azure Resource Manager já foi criado com uma identidade gerenciada atribuída pelo sistema.

### <a name="managing-permissions-on-resource-groups"></a>Gerenciando permissões em grupos de recursos

A identidade gerenciada para o cofre precisa ter as seguintes permissões no grupo de recursos e na rede virtual em que os pontos de extremidade privados serão criados:

- `Microsoft.Network/privateEndpoints/*` Isso é necessário para executar o CRUD em pontos de extremidade privados no grupo de recursos. Ele deve ser atribuído no grupo de recursos.
- `Microsoft.Network/virtualNetworks/subnets/join/action` Isso é necessário na rede virtual em que o IP privado está sendo anexado com o ponto de extremidade privado.
- `Microsoft.Network/networkInterfaces/read` Isso é necessário no grupo de recursos para obter a interface de rede criada para o ponto de extremidade privado.
- Função de colaborador de zona DNS privado essa função já existe e pode ser usada para fornecer `Microsoft.Network/privateDnsZones/A/*` `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` permissões e.

Você pode usar um dos seguintes métodos para criar funções com as permissões necessárias:

#### <a name="create-roles-and-permissions-manually"></a>Criar funções e permissões manualmente

Crie os seguintes arquivos JSON e use o comando do PowerShell no final da seção para criar funções:

PrivateEndpointContributorRoleDef.jsem

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef.jsem

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef.jsem

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Usar um script

1. Inicie o **Cloud Shell** na portal do Azure e selecione **carregar arquivo** na janela do PowerShell.

    ![Selecione carregar arquivo na janela do PowerShell](./media/private-endpoints/upload-file-in-powershell.png)

1. Carregue o script a seguir: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Vá para sua pasta base (por exemplo: `cd /home/user` )

1. Execute o seguinte script:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Estes são os parâmetros:

    - **assinatura**: * * SubscriptionID que tem o grupo de recursos em que o ponto de extremidade privado para o cofre deve ser criado e a sub-rede em que o ponto de extremidade privado do cofre será anexado

    - **vaultPEResourceGroup**: grupo de recursos em que o ponto de extremidade privado para o cofre será criado

    - **vaultPESubnetResourceGroup**: grupo de recursos da sub-rede à qual o ponto de extremidade privado será Unido

    - **vaultMsiName**: nome do MSI do cofre, que é o mesmo que o **vaultname**

1. Conclua a autenticação e o script obterá o contexto da assinatura fornecida acima. Ele criará as funções apropriadas se elas estiverem ausentes do locatário e atribuirá funções ao MSI do cofre.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Criando pontos de extremidade privados usando o Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Pontos de extremidade privados aprovados automaticamente

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  

$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $VMResourceGroupName
$subnet = $vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq '<subnetName>'}


$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Aprovação manual de pontos de extremidade privados usando o cliente Azure Resource Manager

1. Use **getvault** para obter a ID de conexão de ponto de extremidade privada para seu ponto de extremidade particular.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Isso retornará a ID de conexão do ponto de extremidade privado. O nome da conexão pode ser recuperado usando a primeira parte da ID de conexão da seguinte maneira:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Obtenha a **ID de conexão do ponto de extremidade privado** (e o **nome do ponto de extremidade privado**, sempre que necessário) da resposta e substitua-o no URI de Azure Resource Manager e JSON a seguir e tente alterar o status para "aprovado/rejeitado/desconectado", conforme demonstrado no exemplo a seguir:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON:

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

## <a name="frequently-asked-questions"></a>Perguntas frequentes

Q. Posso criar um ponto de extremidade privado para um cofre de backup existente?<br>
a. Não, os pontos de extremidade privados só podem ser criados para novos cofres de backup. Portanto, o cofre não deve ter nenhum item protegido. Na verdade, nenhuma tentativa de proteger itens para o cofre pode ser feita antes da criação de pontos de extremidade privados.

Q. Tentei proteger um item ao meu cofre, mas ele falhou e o cofre ainda não contém itens protegidos. Posso criar pontos de extremidade privados para este cofre?<br>
a. Não, o cofre não deve ter nenhuma tentativa de proteger nenhum item para ele no passado.

Q. Tenho um cofre que está usando pontos de extremidade privados para backup e restauração. Posso adicionar ou remover pontos de extremidade particulares para este cofre, mesmo se eu tiver itens de backup protegidos?<br>
a. Sim. Se você já tiver criado pontos de extremidade privados para um cofre e itens de backup protegidos, será possível adicionar ou remover pontos de extremidade privados posteriormente, conforme necessário.

Q. O ponto de extremidade privado para o backup do Azure também pode ser usado para Azure Site Recovery?<br>
a. Não, o ponto de extremidade privado para backup só pode ser usado para o backup do Azure. Você precisará criar um novo ponto de extremidade privado para Azure Site Recovery, se ele tiver suporte do serviço.

Q. Eu perdi uma das etapas neste artigo e passei para proteger minha fonte de dados. Ainda posso usar pontos de extremidade privados?<br>
a. Não seguir as etapas no artigo e continuar a proteger os itens pode levar o cofre a não conseguir usar pontos de extremidade privados. Portanto, é recomendável que você consulte esta lista de verificação antes de continuar a proteger os itens.

Q. Posso usar meu próprio servidor DNS em vez de usar a zona DNS privada do Azure ou uma zona DNS privada integrada?<br>
a. Sim, você pode usar seus próprios servidores DNS. No entanto, verifique se todos os registros DNS necessários foram adicionados conforme sugerido nesta seção.

Q. É necessário executar qualquer etapa adicional no meu servidor depois de seguir o processo neste artigo?<br>
a. Depois de seguir o processo detalhado neste artigo, você não precisa fazer mais trabalho para usar pontos de extremidade privados para backup e restauração.

## <a name="next-steps"></a>Próximas etapas

- Leia sobre todos os [recursos de segurança no backup do Azure](security-overview.md)
