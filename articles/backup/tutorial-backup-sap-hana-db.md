---
title: Tutorial – fazer backup de bancos de dados do SAP HANA em VMs do Azure
description: Neste tutorial, saiba o backup de bancos de dados SAP HANA executados em uma VM do Azure pode ser realizado no cofre dos Serviços de Recuperação do Backup do Azure.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: 00109de349c1fdfdbaff9de30d18f64d8b986a59
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587637"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Tutorial: Fazer backup de bancos de dados do SAP HANA em uma VM do Azure

Este tutorial mostra a você como fazer backup de bancos de dados do SAP HANA em execução em VMs do Azure em um cofre dos Serviços de Recuperação do Backup do Azure. Neste artigo, você aprenderá a:

> [!div class="checklist"]
>
> * Criar e configurar um cofre
> * Descobrir bancos de dados
> * Configurar backups

[Aqui](sap-hana-backup-support-matrix.md#scenario-support) estão todos os cenários aos quais damos suporte no momento.

>[!NOTE]
>Desde 1º de agosto de 2020, o backup do SAP HANA para RHEL (7.4, 7.6, 7.7 e 8.1) está em disponibilidade geral.

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar backups, verifique se você fez o seguinte:

* Identifique ou crie um [cofre dos Serviços de Recuperação](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) na mesma região e assinatura da VM que executa o SAP HANA.
* Permitir a conectividade da VM com a Internet para que ela possa acessar o Azure, conforme descrito no procedimento [configurar conectividade de rede](#set-up-network-connectivity) abaixo.
* Assegure que o comprimento combinado do nome da VM do Servidor SAP HANA e do nome do Grupo de Recursos não exceda 84 caracteres no caso de VMs do ARM (Azure Resource Manager) e 77 caracteres no caso de VMs clássicas. Essa limitação é porque alguns caracteres são reservados pelo serviço.
* Uma chave deve existir no **hdbuserstore** que atenda aos seguintes critérios:
  * Ela deve estar presente no **hdbuserstore** padrão. O padrão é a conta `<sid>adm` sob a qual o SAP HANA está instalado.
  * Para MDC, a chave deve apontar para a porta SQL de **NAMESERVER**. No caso do SDC, ele deve apontar para a porta SQL de **INDEXSERVER**
  * Ela deve ter credenciais para adicionar e excluir usuários
  * Observe que essa chave pode ser excluída após executar o script de pré-registro com êxito.
* Executar o script de configuração de backup do SAP HANA (script de pré-registro) na máquina virtual em que o HANA está instalado como o usuário raiz. [Esse script](https://aka.ms/scriptforpermsonhana) faz o sistema HANA ficar pronto para backup. Veja a seção [O que o script de pré-registro faz](#what-the-pre-registration-script-does) para entender mais sobre o script de pré-registro.
* Se a instalação do HANA usar pontos de extremidade privados, execute o [script de pré-registro](https://aka.ms/scriptforpermsonhana) com o parâmetro *-sn* ou *--skip-network-checks*.

>[!NOTE]
>O script de pré-registro instala o **compat-unixODBC234** para cargas de trabalho de SAP HANA em execução no RHEL (7.4, 7.6 e 7.7) e **unixODBC** para RHEL 8.1. [Esse pacote está localizado no repositório de RPMs (Serviços de Atualização para Soluções SAP) do RHEL for SAP HANA (para RHEL 7 Server)](https://access.redhat.com/solutions/5094721).  Para obter uma imagem do RHEL do Azure Marketplace, o repositório será **rhui-rhel-sap-hana-for-rhel-7-server-rhui-e4s-rpms**.

## <a name="set-up-network-connectivity"></a>Configurar a conectividade de rede

Para todas as operações, um banco de dados SAP HANA em execução em uma VM do Azure requer conectividade com o serviço de Backup do Azure, o Armazenamento do Azure e o Azure Active Directory. Isso pode ser feito usando pontos de extremidade privados ou concedendo acesso aos endereços IP públicos ou FQDNs necessários. Não permitir a conectividade adequada com os serviços do Azure necessários pode levar a uma falha em operações como descoberta de banco de dados, configuração de backup, realização de backups e restauração de dados.

A seguinte tabela lista as várias alternativas que você pode usar para estabelecer a conectividade:

| **Opção**                        | **Vantagens**                                               | **Desvantagens**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pontos de extremidade privados                 | Permitir backups por IPs privados dentro da rede virtual  <br><br>   Fornecer controle granular na rede e no lado do cofre | Incorre em [custos](https://azure.microsoft.com/pricing/details/private-link/) de ponto de extremidade privado padrão |
| Marcas de serviço do NSG                  | Mais fácil de ser gerenciada, pois as alterações de intervalo são mescladas automaticamente   <br><br>   Sem custos adicionais | Pode ser usada somente com NSGs  <br><br>    Fornece acesso a todo o serviço |
| Marcas de FQDN do Firewall do Azure          | Mais fácil de ser gerenciada, pois os FQDNs necessários são gerenciados automaticamente | Pode ser usada somente com o Firewall do Azure                         |
| Permitir o acesso a FQDNs/IPs de serviço | Sem custos adicionais   <br><br>  Funciona com todos os dispositivos e firewalls de segurança de rede | Pode ser solicitado acesso a um amplo conjunto de IPs ou FQDNs   |
| Usar um proxy HTTP                 | Único ponto de acesso à Internet para VMs                       | Custos adicionais para execução de uma VM com o software de proxy         |

Mais detalhes sobre como usar essas opções são compartilhados abaixo:

### <a name="private-endpoints"></a>Pontos de extremidade privados

Os pontos de extremidade privados permitem que você se conecte com segurança de servidores dentro de uma rede virtual ao seu cofre dos Serviços de Recuperação. O ponto de extremidade privado usa um IP do espaço de endereço da VNET para o cofre. O tráfego de rede entre seus recursos dentro da rede virtual e o cofre passa pela rede virtual e por um link privado na rede de backbone da Microsoft. Isso elimina a exposição à Internet pública. Leia mais sobre pontos de extremidade privados para o Backup do Azure [aqui](./private-endpoints.md).

### <a name="nsg-tags"></a>Marcas do NSG

Se você usar o NSG (grupo de segurança de rede), use a tag de serviço *AzureBackup* para permitir o acesso de saída ao Backup do Azure. Além da marca do Backup do Azure, você também precisará permitir a conectividade para autenticação e transferência de dados criando [regras de NSG](../virtual-network/network-security-groups-overview.md#service-tags) semelhantes para o Azure AD (*AzureActiveDirectory*) e o Armazenamento do Azure (*Armazenamento*). As seguintes etapas descrevem o processo para criar uma regra para a tag do Backup do Azure:

1. Em **Todos os Serviços**, acesse **Grupos de segurança de rede** e selecione o grupo de segurança de rede.

1. Selecione **Regras de segurança de saída** em **Configurações**.

1. Selecione **Adicionar**. Insira todos os detalhes necessários para criar uma regra, conforme descrito em [Configurações da regra de segurança](../virtual-network/manage-network-security-group.md#security-rule-settings). Verifique se a opção **Destino** está definida como *Tag de Serviço* e se **Marca de serviço de destino** está definida como *AzureBackup*.

1. Selecione **Adicionar** para salvar a regra de segurança de saída recém-criada.

De maneira semelhante, é possível criar [regras de segurança de saída de NSG](../virtual-network/network-security-groups-overview.md#service-tags) para o Armazenamento do Azure e o Azure AD. Para obter mais informações sobre as marcas de serviço, confira este [artigo](../virtual-network/service-tags-overview.md).

### <a name="azure-firewall-tags"></a>Marcas do Firewall do Azure

Se estiver usando o Firewall do Azure, crie uma regra de aplicativo usando a [tag de FQDN do Firewall do Azure](../firewall/fqdn-tags.md) *AzureBackup*. Isso permite todo o acesso de saída ao Backup do Azure.

### <a name="allow-access-to-service-ip-ranges"></a>Permitir o acesso aos intervalos de IP de serviço

Se você optar por permitir o acesso a IPs de serviço, confira os intervalos de IP no arquivo JSON disponível [aqui](https://www.microsoft.com/download/confirmation.aspx?id=56519). Será necessário permitir o acesso a IPs correspondentes ao Backup do Azure, ao Armazenamento do Azure e ao Azure Active Directory.

### <a name="allow-access-to-service-fqdns"></a>Permitir o acesso a FQDNs de serviço

Também é possível usar os seguintes FQDNs para permitir o acesso aos serviços necessários de seus servidores:

| Serviço    | Nomes de domínio a serem acessados                             |
| -------------- | ------------------------------------------------------------ |
| Backup do Azure  | `*.backup.windowsazure.com`                             |
| Armazenamento do Azure | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Permitir acesso a FQDNs nas seções 56 e 59 de acordo com [este artigo](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

### <a name="use-an-http-proxy-server-to-route-traffic"></a>Usar um servidor proxy HTTP para rotear o tráfego

Quando você faz backup de um banco de dados SAP HANA em execução em uma VM do Azure, a extensão de backup na VM usa as APIs HTTPS para enviar comandos de gerenciamento ao Backup do Azure e dados ao Armazenamento do Azure. A extensão de backup também usa o Azure AD para autenticação. Roteie o tráfego de extensão de backup para esses três serviços por meio do proxy HTTP. Use a lista de IPs e FQDNs mencionados acima para permitir o acesso aos serviços necessários. Não há suporte para os servidores proxy autenticados.

## <a name="understanding-backup-and-restore-throughput-performance"></a>Entendendo o desempenho da taxa de transferência de backup e restauração

Os backups (log e não log) nas VMs do Azure do SAP HANA fornecidas por meio do Backint são fluxos para cofres de Serviços de Recuperação do Azure e, portanto, é importante entender essa metodologia de streaming.

O componente Backint do HANA fornece os "pipes" (um pipe do qual ler e um pipe no qual gravar) conectados a discos subjacentes nos quais os arquivos de banco de dados residem, que são lidos pelo serviço de Backup do Azure e transportados para o cofre dos Serviços de Recuperação do Azure. O serviço de Backup do Azure também executa uma soma de verificação para validar os fluxos, além das verificações de validação nativos de backint. Essas validações garantem que os dados presentes no cofre dos Serviços de Recuperação do Azure sejam realmente confiáveis e recuperáveis.

Como os fluxos lidam principalmente com discos, você precisa entender o desempenho do disco para medir o desempenho de backup e restauração. Confira [este artigo](../virtual-machines/disks-performance.md) para ter uma compreensão detalhada da taxa de transferência e do desempenho do disco nas VMs do Azure. Eles também são aplicáveis ao desempenho de backup e restauração.

**O serviço de Backup do Azure tenta atingir até 420 MBps para backups que não são de log (como completo, diferencial e incremental) e até 100 MBps para backups de log para o HANA**. Conforme mencionado acima, essas velocidades não são garantidas e dependem dos seguintes fatores:

* Taxa de transferência máxima do disco não armazenado em cache da VM
* Tipo de disco subjacente e sua taxa de transferência
* O número de processos que estão tentando ler e gravar no mesmo disco ao mesmo tempo.

> [!IMPORTANT]
> Em VMs menores, em que a taxa de transferência do disco não armazenado em cache é muito próxima ou menor que 400 MBps, você pode estar preocupado que toda a IOPS de disco seja consumida pelo serviço de backup, o que pode afetar as operações do SAP HANA relacionadas à leitura/gravação dos discos. Nesse caso, se você quiser restringir ou limitar o consumo do serviço de backup ao limite máximo, poderá consultar a próxima seção.

### <a name="limiting-backup-throughput-performance"></a>Limitando o desempenho da taxa de transferência de backup

Se você quiser restringir o consumo de IOPS de disco do serviço de backup ao valor máximo, execute as etapas a seguir.

1. Vá até a pasta "opt/msawb/bin"
2. Crie um arquivo JSON chamado "ExtensionSettingOverrides.JSON"
3. Adicione um par chave-valor ao arquivo JSON da seguinte maneira:

    ```json
    {
    "MaxUsableVMThroughputInMBPS": 200
    }
    ```

4. Altere as permissões e a propriedade do arquivo da seguinte maneira:
    
    ```bash
    chmod 750 ExtensionSettingsOverrides.json
    chown root:msawb ExtensionSettingsOverrides.json
    ```

5. Não é necessário reiniciar nenhum serviço. O serviço de Backup do Azure tentará limitar o desempenho da taxa de transferência conforme mencionado neste arquivo.

## <a name="what-the-pre-registration-script-does"></a>O que o script de pré-registro faz

O script de pré-registro executa as seguintes funções:

* Com base em sua distribuição do Linux, o script instala ou atualiza todos os pacotes necessários exigidos pelo agente do Backup do Azure.
* Executa verificações de conectividade de rede de saída com servidores de Backup do Azure e serviços dependentes como Azure Active Directory e o Armazenamento do Azure.
* Ele faz logon em seu sistema HANA usando a chave de usuário listada como parte dos [pré-requisitos](#prerequisites). A chave do usuário é usada para criar um usuário de backup (AZUREWLBACKUPHANAUSER) no sistema HANA e **pode ser excluída após a execução bem-sucedida do script de pré-registro**.
* Estas funções e permissões necessárias são atribuídas a AZUREWLBACKUPHANAUSER:
  * Para MDC: ADMINISTRADOR DE BANCO DE DADOS e ADMINISTRADOR DE BACKUP (no caso do HANA 2.0 SPS05 em diante): para criar bancos de dados durante a restauração.
  * Para SDC: ADMIN DO BANCO DE DADOS: para criar bancos de dados durante a restauração.
  * LEITURA DO CATÁLOGO: para ler o catálogo de backup.
  * SAP_INTERNAL_HANA_SUPPORT: para acessar algumas tabelas privadas. Necessário apenas para versões SDC e MDC anteriores ao HANA 2.0 SPS04 Rev 46. Isso não é necessário para o HANA 2.0 SPS04 Rev 46 e versões posteriores, já que estamos obtendo as informações necessárias de tabelas públicas agora com a correção da equipe do HANA.
* O script adiciona uma chave a **hdbuserstore** para AZUREWLBACKUPHANAUSER para o plug-in de backup do HANA lidar com todas as operações (consultas de banco de dados, operações de restauração, configuração e execução de backup).

>[!NOTE]
> Você pode passar explicitamente a chave de usuário listada como parte dos [pré-requisitos](#prerequisites) como um parâmetro para o script de pré-registro: `-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Para saber quais outros parâmetros são aceitos pelo script, use o comando `bash msawb-plugin-config-com-sap-hana.sh --help`

Para confirmar a criação da chave, execute o comando HDBSQL no computador HANA com credenciais de SIDADM:

```hdbsql
hdbuserstore list
```

A saída do comando deve exibir a chave {SID}{DBNAME}, com o usuário mostrado como AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Verifique se você tem um conjunto exclusivo de arquivos SSFS em `/usr/sap/{SID}/home/.hdb/`. Deve haver apenas uma pasta neste caminho.

Aqui está um resumo das etapas necessárias para concluir a execução do script de pré-registro.

|Quem  |De  |O que executar  |Comentários  |
|---------|---------|---------|---------|
|```<sid>```adm (SO)     |  SO HANA       |   Leia o tutorial e baixar o script de pré-registro      |   Leia os [pré-requisitos acima](#prerequisites)    Baixe o script de pré-registro [aqui](https://aka.ms/scriptforpermsonhana)  |
|```<sid>```adm (SO) e usuário do sistema (HANA)    |      SO HANA   |   Executar o comando hdbuserstore Set      |   Por exemplo, hdbuserstore Set SYSTEM hostname>:3```<Instance#>```13 SYSTEM ```<password>``` **Observação:** lembre-se de usar o nome do host em vez do endereço IP ou do FQDN      |
|```<sid>```adm (SO)    |   SO HANA      |  Executar o comando hdbuserstore List       |   Verifique se o resultado inclui o armazenamento padrão, como abaixo: ```KEY SYSTEM  ENV : <hostname>:3<Instance#>13  USER: SYSTEM```      |
|Raiz (SO)     |   SO HANA        |    Executar script de pré-registro do Backup do Azure para HANA      |    ```./msawb-plugin-config-com-sap-hana.sh -a --sid <SID> -n <Instance#> --system-key SYSTEM```     |
|```<sid>```adm (SO)    |  SO HANA       |   Executar o comando hdbuserstore List      |    Verifique se o resultado inclui novas linhas, como mostrado abaixo: ```KEY AZUREWLBACKUPHANAUSER  ENV : localhost: 3<Instance#>13   USER: AZUREWLBACKUPHANAUSER```     |

Depois de executar o script de pré-registro com êxito e verificar, você pode continuar a verificar [os requisitos de conectividade](#set-up-network-connectivity) e, depois, [configurar o backup](#discover-the-databases) do cofre dos Serviços de Recuperação

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um cofre dos Serviços de Recuperação é uma entidade que armazena os backups e os pontos de recuperação criados ao longo do tempo. O cofre dos Serviços de Recuperação também contém as políticas de backup associadas às máquinas virtuais protegidas.

Para criar um cofre de Serviços de Recuperação:

1. Entre na sua assinatura no [Portal do Azure](https://portal.azure.com/).

2. No menu esquerdo, selecione **Todos os serviços**

   ![Selecionar Todos os serviços](./media/tutorial-backup-sap-hana-db/all-services.png)

3. Na caixa de diálogo **Todos os serviços**, insira **Serviços de Recuperação**. A lista de recurso filtra de acordo com sua entrada. Na lista de recursos, selecione **Cofres dos Serviços de Recuperação**.

   ![Selecione Cofres de Serviços de Recuperação](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. No painel de cofres dos **Serviços de Recuperação**, selecione **Adicionar**.

   ![Adicionar um cofre dos Serviços de Recuperação](./media/tutorial-backup-sap-hana-db/add-vault.png)

   A caixa de diálogo **Cofre dos Serviços de Recuperação** é aberta. Fornecer valores para **Nome, Assinatura, Grupo de recursos** e **Localização**

   ![Criar cofre de Serviços de Recuperação](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Name**: O nome é usado para identificar o cofre dos Serviços de Recuperação e precisa ser exclusivo para a assinatura do Azure. Especifique um nome que tenha pelo menos dois, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hifens. Para este tutorial, usamos o nome **SAPHanaVault**.
   * **Assinatura**: Escolha a assinatura a ser usada. Se você for um membro de apenas uma assinatura, verá esse nome. Se você não tem certeza de qual assinatura usar, utilize a assinatura padrão (sugerida). Só haverá múltiplas opções se a sua conta corporativa ou de estudante estiver associada a várias assinaturas do Azure. Aqui, usamos a **assinatura do laboratório de solução do SAP HANA**.
   * **Grupo de recursos**: Use um grupo de recursos existente ou crie um novo. Aqui, usamos **SAPHANADemo**.<br>
   Para ver a lista de grupos de recursos disponíveis em sua assinatura, selecione **Usar existente** e, em seguida, selecione um recurso na caixa de listagem suspensa. Para criar um novo grupo de recursos, selecione **Criar novo** e insira o nome. Para obter informações completas sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md).
   * **Localização**: Selecione a região geográfica para o cofre. O cofre precisa estar na mesma região que a máquina virtual que executa o SAP HANA. Nós usamos **Leste dos EUA 2**.

5. Selecione **Examinar + criar**.

   ![Selecione Examinar + Criar](./media/tutorial-backup-sap-hana-db/review-create.png)

O cofre dos Serviços de Recuperação agora está criado.

## <a name="discover-the-databases"></a>Descobrir os bancos de dados

1. No cofre, em **Introdução**, selecione **Backup**. Em **Em que local sua carga de trabalho é executada?** , selecione **SAP HANA em VM do Azure**.
2. Selecione **Iniciar Descoberta**. Isso inicia a descoberta de VMs do Linux desprotegidas na região do cofre. Você verá a VM do Azure que deseja proteger.
3. Em **Selecionar Máquinas Virtuais**, selecione o link para baixar o script que fornece permissões para que o serviço de Backup do Azure acesse as VMs SAP HANA para descoberta de banco de dados.
4. Execute o script na VM que hospeda os bancos de dados do SAP HANA dos quais você deseja fazer backup.
5. Depois de executar o script na VM, em **Selecionar Máquinas Virtuais**, selecione a VM. Em seguida, selecione **Descobrir BDs**.
6. O Backup do Azure descobre todos os bancos de dados do SAP HANA na VM. Durante a descoberta, o Backup do Azure registra a VM com o cofre e instala uma extensão na VM. Nenhum agente é instalado no banco de dados.

   ![Descobrir os bancos de dados](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Configurar backup

Agora que os bancos de dados dos quais desejamos fazer backup foram descobertos, vamos habilitar o backup.

1. Selecione **Configurar Backup**.

   ![Configurar backup](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. Em **Selecionar os itens para fazer backup**, selecione um ou mais bancos de dados que você deseja proteger e selecione **OK**.

   ![Selecionar itens para fazer backup](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. Em **Política de Backup > Escolher política de backup**, crie uma política de backup para os bancos de dados, de acordo com as instruções na próxima seção.

   ![Escolher política de backup](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Depois de criar a política, no menu **Backup**, selecione **Habilitar backup**.

   ![Selecione Habilitar backup.](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Acompanhe o progresso da configuração de backup na área de **Notificações** do portal.

## <a name="creating-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são feitos e por quanto tempo eles são mantidos.

* Uma política é criada no nível do cofre.
* Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.

Especifique as configurações de política da seguinte maneira:

1. Em **Nome da política**, insira um nome para a nova política. Nesse caso, digite **SAPHANA**.

   ![Inserir um nome para a nova política](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. Em **Política de backup completo**, selecione uma **Frequência de Backup**. Você pode escolher **Diário** ou **Semanal**. Para este tutorial, escolhemos o backup **Diário**.

   ![Selecionar uma frequência de backup](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. Em **Período de Retenção**, defina as configurações de retenção para o backup completo.
   * Por padrão, todas as opções são selecionadas. Desmarque os limites de período de retenção que você não deseja usar e marque os que você deseja.
   * O período de retenção mínimo para qualquer tipo de backup (completo/diferencial/log) é de sete dias.
   * Os pontos de recuperação são marcados para retenção com base em seu intervalo de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado a cada dia.
   * O backup para um dia específico é marcado e mantido com base na configuração e no período de retenção semanal.
   * Os intervalos de retenção mensal e anual comportam-se de maneira semelhante.
4. No menu de **política de Backup Completo**, clique em **OK** para aceitar as configurações.
5. Em seguida, selecione **Backup Diferencial** para adicionar uma política diferencial.
6. Em **Política de Backup Diferencial**, selecione **Habilitar** para abrir os controles de retenção e frequência. Habilitamos um backup diferencial a cada **Domingo** às **02:00**, que é mantido por **30 dias**.

   ![Política de backup diferencial](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Você pode escolher um diferencial ou um incremental como um backup diário, mas não ambos.

7. Em **Política de Backup Incremental**, selecione **Habilitar** para abrir os controles de retenção e frequência.
    * No máximo, você pode disparar um backup incremental por dia.
    * Backups incrementais podem ser retidos por até 180 dias. Se você precisar de retenção mais longa, deverá usar os backups completos.

    ![Política de backup incremental](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

8. Selecione **OK** para salvar a política e retornar para o menu principal da **Política de backup**.
9. Selecione **Backup de Log** para adicionar uma política de backup de log transacional.
   * **Backup de Log** é definido por padrão como **Habilitar**. Isso não pode ser desabilitado, pois o SAP HANA gerencia todos os backups de log.
   * Definimos **duas horas** como a agenda de backup e **15 dias** de período de retenção.

    ![Política de backup de log](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > Os backups de log só começam a fluir depois que um backup completo bem-sucedido é concluído.
   >

10. Selecione **OK** para salvar a política e retornar para o menu principal da **Política de backup**.
11. Depois de terminar de definir a política de backup, selecione **OK**.

Você configurou com êxito os backups para seus bancos de dados do SAP HANA.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [executar backups sob demanda em bancos de dados do SAP HANA em execução em VMs do Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Saiba como [restaurar bancos de dados do SAP HANA em execução em VMs do Azure](sap-hana-db-restore.md)
* Saiba como [gerenciar bancos de dados do SAP HANA cujo backup é feito usando o Backup do Azure](sap-hana-db-manage.md)
* Saiba como [solucionar problemas comuns ao fazer backup de bancos de dados do SAP HANA](backup-azure-sap-hana-database-troubleshoot.md)