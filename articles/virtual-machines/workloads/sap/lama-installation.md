---
title: Conector SAP LaMa para o Azure | Microsoft Docs
description: Gerenciamento de sistemas SAP no Azure usando o SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.openlocfilehash: e3f541e28f47bb6456b441811d23baa9e020fde7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959145"
---
# <a name="sap-lama-connector-for-azure"></a>Conector SAP LaMa para o Azure

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[2815988]:https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Instrução de Suporte Geral: Sempre abra um incidente com o SAP no componente BC-VCM-LVM-Hyper-v se você precisar de suporte para o SAP LaMa ou o conector do Azure.

O SAP LaMa é usado por muitos clientes para operar e monitorar seu cenário SAP. Desde SP05 de 3.0 de LaMa SAP, ele é fornecido com um conector para o Azure por padrão. Você pode usar esse conector para desalocar e iniciar as máquinas virtuais, copiar e realocar discos gerenciados e excluir discos gerenciados. Com essas operações básicas, você pode realocar, copiar, clonar e atualizar os sistemas SAP usando o SAP LaMa.

Este guia descreve como configurar o conector do Azure para SAP LaMa, criar máquinas virtuais que podem ser usadas para instalar os sistemas SAP adaptativos e como configurá-las.

> [!NOTE]
> O conector só está disponível na edição Enterprise SAP LaMa

## <a name="resources"></a>Recursos

As seguintes Notas do SAP estão relacionadas ao tópico do SAP no Azure:

| Número da observação | Title |
| --- | --- |
| [2343511] |Conector do Microsoft Azure para gerenciamento de paisagem do SAP (LaMa) |
| [2350235] |SAP Landscape Management 3.0 - Edição Enterprise |

Leia também o [Portal de Ajuda do SAP para SAP LaMa](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Comentários gerais

* Verifique se você habilitou a *Criação Automática do Ponto de Montagem* em Instalação -> Configurações -> Mecanismo  
  Se o SAP LaMa montar os volumes usando as extensões de SAP adaptativo em uma máquina virtual, o ponto de montagem deve existir se essa configuração não estiver habilitada.

* Usar sub-rede separada e não o use os endereços IP dinâmicos para evitar que o endereço "roube" ao implantar novas VMs e instâncias do SAP que não estão preparadas  
  Se você usar a alocação dinâmica de endereços IP na sub-rede, que também é usada pelo SAP LaMa, preparar um sistema SAP com o SAP LaMa poderá falhar. Se um sistema SAP for despreparado, os endereços IP não são reservados e podem ficar alocados a outras máquinas virtuais.

* Se você entrar em hosts gerenciados, certifique-se de não bloquear os sistemas de arquivos de serem desmontados  
  Se você entrar em máquinas virtuais do Linux e alterar o diretório de trabalho para um diretório em um ponto de montagem, por exemplo,/usr/sap/AH1/ASCS00/exe, o volume não poderá ser desmontado e uma falha de reutilização ou despreparação.

* Certifique-se de desabilitar CLOUD_NETCONFIG_MANAGE em máquinas virtuais SUSE SLES Linux. Para obter mais detalhes, consulte [SuSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633).

## <a name="set-up-azure-connector-for-sap-lama"></a>Configurar o conector do Azure para o SAP LaMa

O conector do Azure é fornecido a partir do SAP LaMa 3.0 SP05. É recomendável sempre instalar o pacote de suporte mais recente e o patch para o SAP LaMa 3.0.

O conector do Azure usa a API Azure Resource Manager para gerenciar os recursos do Azure. O SAP LaMa pode usar uma entidade de serviço ou uma identidade gerenciada para se autenticar nessa API. Se o LaMa do SAP estiver em execução em uma VM do Azure, é recomendável usar uma identidade gerenciada, conforme descrito no capítulo [usar uma identidade gerenciada para obter acesso à API do Azure](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d). Se você quiser usar uma entidade de serviço, siga as etapas no capítulo [usar uma entidade de serviço para obter acesso à API do Azure](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e).

### <a name="use-a-service-principal-to-get-access-to-the-azure-api"></a><a name="913c222a-3754-487f-9c89-983c82da641e"></a>Usar uma entidade de serviço para obter acesso à API do Azure

O conector do Azure pode usar uma entidade de serviço para autorizar contra Microsoft Azure. Siga estas etapas para criar uma entidade de serviço para o Gerenciamento de Paisagem do SAP (LaMa).

1. Acesse https://portal.azure.com
1. Abra a folha Azure Active Directory
1. Clique em Registros do Aplicativo
1. Clique em novo registro
1. Insira um nome e clique em registrar
1. Selecione o novo aplicativo e clique em certificados & segredos na guia Configurações
1. Crie um novo segredo do cliente, insira uma descrição para uma nova chave, selecione quando o segredo deve expirar e clique em salvar
1. Anote o Valor. Ele é usado como senha da Entidade de Serviço
1. Anote a ID do Aplicativo. Ele é usado como nome de usuário da Entidade de Serviço

A Entidade de Serviço não tem permissões para acessar os recursos do Azure por padrão. Você precisa conceder as permissões da entidade de serviço para acessá-los.

1. Acesse https://portal.azure.com
1. Abra a folha Grupos de recursos
1. Selecione o grupo de recursos que você quer usar
1. Clique em Controle de acesso (IAM)
1. Clique em Adicionar atribuição de função
1. Selecione a função Colaborador
1. Digite o nome do aplicativo criado acima
1. Clique em Salvar
1. Repita as etapas 3 a 8 para todos os grupos de recursos que você deseja usar no SAP LaMa

### <a name="use-a-managed-identity-to-get-access-to-the-azure-api"></a><a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>Usar uma identidade gerenciada para obter acesso à API do Azure

Para poder usar uma identidade gerenciada, sua instância do SAP LaMa deve ser executada em uma VM do Azure que tenha uma identidade de sistema ou de usuário atribuída. Para obter mais informações sobre identidades gerenciadas, leia [o que são identidades gerenciadas para recursos do Azure?](../../../active-directory/managed-identities-azure-resources/overview.md) e [Configure as identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md).

A identidade gerenciada não tem permissões para acessar os recursos do Azure por padrão. Você precisa conceder a ele permissões para acessá-los.

1. Acesse https://portal.azure.com
1. Abra a folha Grupos de recursos
1. Selecione o grupo de recursos que você quer usar
1. Clique em Controle de acesso (IAM)
1. Clique em Adicionar > adicionar atribuição de função
1. Selecione a função Colaborador
1. Selecione ' máquina virtual ' para ' atribuir acesso a '
1. Selecione a máquina virtual na qual sua instância do SAP LaMa está sendo executada
1. Clique em Salvar
1. Repita as etapas para todos os grupos de recursos que você deseja usar no SAP LaMa

Na configuração do conector do Azure para SAP LaMa, selecione ' usar identidade gerenciada ' para habilitar o uso da identidade gerenciada. Se você quiser usar uma identidade atribuída pelo sistema, deixe o campo nome de usuário vazio. Se você quiser usar uma identidade atribuída pelo usuário, insira a ID de identidade atribuída pelo usuário no campo nome de usuário.

### <a name="create-a-new-connector-in-sap-lama"></a>Criar um novo conector no SAP LaMa

Abra o site de LaMa SAP e navegue até a infraestrutura. Vá para a guia Gerenciadores de Nuvem e clique em Adicionar. Selecione o Adaptador de Nuvem do Microsoft Azure e clique em Avançar. Insira as seguintes informações:

* Rótulo: Escolha um nome para a instância do conector
* Nome de usuário: ID do aplicativo da entidade de serviço ou ID da identidade atribuída ao usuário da máquina virtual. Consulte [usando um sistema ou uma identidade atribuída pelo usuário] para obter mais informações
* Senha: chave/senha da entidade de serviço. Você pode deixar esse campo vazio se usar um sistema ou uma identidade atribuída pelo usuário.
* URL: Manter o padrão `https://management.azure.com/`
* Monitoramento de Intervalo (Segundos): Deve ser pelo menos 300
* Usar identidade gerenciada: o SAP LaMa pode usar um sistema ou uma identidade atribuída pelo usuário para se autenticar na API do Azure. Consulte [o capítulo usar uma identidade gerenciada para obter acesso à API do Azure](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) neste guia.
* ID da assinatura: ID da assinatura do Azure
* ID do locatário do Microsoft Azure Active Directory: ID do locatário do Microsoft Azure Active Directory
* Host de proxy: nome do host do proxy se SAP LaMa precisar de um proxy para conectar à internet
* Porta do proxy: porta TCP do proxy
* Alterar tipo de armazenamento para economizar custos: habilite essa configuração se o adaptador do Azure precisar alterar o tipo de armazenamento do Managed Disks para economizar custos quando os discos não estiverem em uso. Para discos de dados que são referenciados em uma configuração de instância do SAP, o adaptador alterará o tipo de disco para o armazenamento padrão durante uma instância despreparar e de volta para o tipo de armazenamento original durante uma preparação de instância. Se você parar uma máquina virtual no SAP LaMa, o adaptador alterará o tipo de armazenamento de todos os discos anexados, incluindo o disco do sistema operacional para o armazenamento padrão. Se você iniciar uma máquina virtual no SAP LaMa, o adaptador irá alterar o tipo de armazenamento de volta para o tipo de armazenamento original.

Clique em Configuração de Teste para validar a sua entrada. Você deverá ver

Conexão bem-sucedida: Conexão bem-sucedida à nuvem da Microsoft. 7 grupos de recurso encontrado (apenas 10 grupos solicitados)

na parte inferior do site.

## <a name="provision-a-new-adaptive-sap-system"></a>Provisionar um novo sistema SAP adaptativo

Você pode implantar uma nova máquina virtual manualmente ou usar um dos modelos do Azure no [repositório de início rápido](https://github.com/Azure/azure-quickstart-templates). Ele contém modelos para [ASCS do SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [servidores de aplicativos SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)e o [banco de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Você também pode usar esses modelos para provisionar novos hosts como parte de um sistema cópia/clone etc.

É recomendável usar uma sub-rede separada para todas as máquinas virtuais que você deseja gerenciar com o SAP LaMa e não usar endereços IP dinâmicos para evitar que o IP endereço "roube" ao implantar novas máquinas virtuais e instâncias do SAP que não estão preparadas.

> [!NOTE]
> Se possível, remova todas as extensões de máquina virtual, pois elas podem causar longos runtimes para desanexar discos de uma máquina virtual.

Verifique se \<hanasid> \<sapsid> os SAPs do usuário ADM, ADM e grupo existem no computador de destino com a mesma ID e GID ou usam LDAP. Habilite e inicie o servidor NFS nas máquinas virtuais que deve ser usadas para executar o SAP NetWeaver (A)SCS.

### <a name="manual-deployment"></a>Implantação manual

O SAP LaMa se comunica com a máquina virtual usando o agente de Host do SAP. Se você implantar as máquinas virtuais manualmente ou não usar o modelo do Azure Resource Manager do repositório do guia de início rápido, certifique-se de instalar o agente de Host SAP mais recente e as Extensões de SAP Adaptativo. Para obter mais informações sobre os níveis de patch necessários para o Azure, consulte a Nota SAP [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Implantação manual de uma máquina Virtual Linux

Crie uma nova máquina virtual com um dos sistemas de operação com suporte listados na nota SAP [2343511]. Adicione outras configurações de IP para as instâncias do SAP. Cada instância precisa pelo menos no endereço IP e deve ser instalada usando um nome de host virtual.

A instância ASCS do SAP NetWeaver precisa de discos para/sapmnt/ \<SAPSID> ,/usr/SAP/ \<SAPSID> ,/usr/SAP/trans e/usr/SAP/ \<sapsid> ADM. Os servidores de aplicativos SAP NetWeaver não são necessários para discos adicionais. Tudo relacionado à instância do SAP deve ser armazenado em ASCS e podem ser exportado por meio de NFS. Caso contrário, no momento, não é possível adicionar servidores de aplicativos adicionais usando o SAP LaMa.

![ASCS do SAP NetWeaver no Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Implantação manual do SAP HANA

Crie uma nova máquina virtual com um dos sistemas operacionais para o SAP HANA com suporte listados na Nota SAP [2343511]. Adicione uma configuração de IP adicional para o SAP HANA e um por locatário do HANA.

O SAP HANA precisa de discos para hana/shared, /hana/backup, /hana/data, e /hana/log

![SAP HANA no Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Implantação manual para o Oracle Database no Linux

Crie uma nova máquina virtual com um dos sistemas operacionais para os banco de dados do Oracle com suporte listados na Nota SAP [2343511]. Adicione uma configuração de IP adicional para o banco de dados Oracle.

O banco de dados Oracle precisa de discos para /oracle, /home/oraod1, e /home/oracle

![Diagrama que mostra um banco de dados Oracle no Linux e os discos necessários.](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Implantação manual para o Microsoft SQL Server

Crie uma nova máquina virtual com um dos sistemas operacionais para Microsoft SQL Server com suporte listados na Nota SAP [2343511]. Adicione uma configuração de IP adicional para a instância do SQL Server.

O servidor de banco de dados do Microsoft SQL Server precisa de discos para o banco de dados e os arquivos de log e os discos para c:\usr\sap.

![Banco de dados Oracle no Linux](media/lama/sap-lama-db-sql.png)

Certifique-se de instalar um driver Microsoft ODBC da Microsoft com suporte para o SQL Server em uma máquina virtual que você deseja usar para realocar um servidor de aplicativos SAP NetWeaver ou como um destino de cópia/clone do sistema.

O SAP LaMa não pode realocar o próprio SQL Server, portanto, uma máquina virtual que você deseja usar para realocar uma instância de banco de dados ou como um destino de cópia/clone do sistema SQL Server pré-instalado.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Implantar uma Máquina Virtual do Azure usando um modelo do Azure

Faça o download dos arquivos a seguir mais recentes disponíveis do [Marketplace do Software SAP](https://support.sap.com/swdc) para o sistema operacional das máquinas virtuais:

1. SAPCAR 7.21
1. AGENTE DE HOST DO SAP 7.21
1. EXTENSÃO ADAPTATIVA DO SAP 1.0 EXT

Baixe também os seguintes componentes do [Centro de Download da Microsoft](https://www.microsoft.com/download)

1. Pacote redistribuível do Microsoft Visual C++ 2010 (x64) (somente Windows)
1. Microsoft ODBC Driver for SQL Server (apenas SQL Server)

Os componentes são necessários para implantar o modelo. A maneira mais fácil de torná-los disponíveis para o modelo é carregá-los para uma conta de armazenamento do Azure e criar uma Assinatura de Acesso Compartilhado (SAS).

Os modelos têm os seguintes parâmetros:

* sapSystemId: ID do sistema SAP. Ele é usado para criar o layout do disco (por exemplo,/usr/SAP/ \<sapsid> ).

* computerName: O nome do computador da nova máquina virtual. Esse parâmetro também é usado pelo SAP LaMa. Quando você usa esse modelo para provisionar uma nova máquina virtual como parte de uma cópia do sistema, o SAP LaMa aguarda até que o host com este nome de computador possa ser alcançado.

* osType: O tipo do sistema operacional que você deseja implantar.

* dbtype: O tipo do objeto de banco de dados. Esse parâmetro é usado para determinar quantas configurações de IP adicionais precisam ser adicionadas e como o layout deve parecer.

* sapSystemSize: O tamanho do sistema SAP que você deseja implantar. É usado para determinar o tipo de instância de máquina virtual e o tamanho.

* adminUsername: nome de usuário para a máquina virtual.

* adminPassword: Senha para a máquina virtual. Você também pode fornecer uma chave pública para o SSH.

* sshKeyData: chave pública SSH para as máquinas virtuais. Apenas sistemas operacionais Linux com suporte.

* subnetId: A ID da sub-rede que você deseja usar.

* deployEmptyTarget: você pode implantar um destino vazio se você quiser usar a máquina virtual como um destino para realocar uma instância ou semelhantes. Nesse caso, não há discos adicionais ou configurações de IP anexadas.

* sapcarLocation: O local para o aplicativo sapcar que corresponde ao sistema operacional que você implantar. SAPCAR é usado para extrair os arquivos fornecidos nos outros parâmetros.

* sapHostAgentArchiveLocation: O local do arquivo do Agente de Host do SAP. O Agente de Host do SAP é implantado como parte dessa implantação de modelo.

* sapacExtLocation: O local das Extensões Adaptativas do SAP. Nota SAP [2343511] lista o nível de patch mínimo necessário para o Azure.

* vcRedistLocation: O local do runtime VC que é necessário para instalar as extensões de SAP adaptativo. Esse parâmetro só é necessário para Windows.

* odbcDriverLocation: O local do driver ODBC que você deseja instalar. Apenas Driver ODBC Microsoft para SQL Server é compatível.

* sapadmPassword: A senha do usuário sapadm.

* sapadmId: A ID de usuário do Linux do usuário sapadm. Não é necessário para Windows.

* sapsysGid: ID do grupo do Linux do grupo sapsys. Não é necessário para Windows.

* _artifactslocation: o URI de base, onde se encontram os artefatos exigidos por esse modelo. Quando o modelo for implantado usando os scripts que acompanham este artigo, será usado um local privado na assinatura e esse valor será gerado automaticamente. Necessário somente se você não implanta o modelo do GitHub.

* _artifactslocationsastoken: o sasToken necessário para acessar _artifactsLocation. Quando o modelo é implantado usando os scripts que acompanham este artigo, um sasToken será gerado automaticamente. Necessário somente se você não implanta o modelo do GitHub.

### <a name="sap-hana"></a>SAP HANA

Nos exemplos a seguir, vamos supor que você instale o SAP HANA com o sistema HN1 da ID e o sistema SAP NetWeaver com AH1 de ID do sistema. Os nomes de host virtuais são hn1-db para a instância do HANA, ah1-db para o locatário HANA usado pelo sistema SAP NetWeaver, ah1-ascs para o ASCS do SAP NetWeaver e ah1-di-0 para o primeiro servidor de aplicativos SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Instalar o ASCS do SAP NetWeaver para SAP HANA usando o Azure Managed Disks

Antes de iniciar o SAP SWPM Software Provisioning Manager (SWPM), você precisará montar o endereço IP do nome de host virtual do ASCS. A maneira recomendada é usar sapacext. Se você montar o endereço IP usando sapacext, certifique-se de montar novamente o endereço IP após uma reinicialização.

![Logotipo do Linux.][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Logotipo do Windows.][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

Execute o SWPM e use *ah1-ascs* para o *Nome de Host de Instância do ASCS*.

![Logotipo do Linux.][Logo_Linux] Linux  
Adicione o seguinte parâmetro de perfil para o perfil de agente de Host do SAP, que está localizado em /usr/sap/hostctrl/exe/host_profile. Para saber mais, confira a Nota SAP [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Instalar o ASCS do SAP NetWeaver para SAP HANA no Azure NetAppFiles (seja) BETA

> [!NOTE]
> Essa funcionalidade ainda é nem GA. Para obter mais informações, consulte SAP Note [2815988] (visível somente para clientes de visualização).
Abra um incidente SAP no componente BC-VCM-LVM-HYPERV e solicite ingressar no adaptador de armazenamento LaMa para Azure NetApp Files visualização

O seja fornece NFS para Azure. No contexto do SAP LaMa, isso simplifica a criação de instâncias ASCS (ABAP central Services) e a instalação subsequente de servidores de aplicativos. Anteriormente, a instância ASCS tinha que agir como servidor NFS e o parâmetro acosprep/nfs_paths precisava ser adicionado à host_profile do SAP Hostagent.

#### <a name="anf-is-currently-available-in-these-regions"></a>O seja está disponível atualmente nestas regiões:

Leste da Austrália, EUA Central, leste dos EUA, leste dos EUA 2, Europa Setentrional, Sul EUA Central, Europa Ocidental e oeste dos EUA 2.

#### <a name="network-requirements"></a>Requisitos de Rede

O seja requer uma sub-rede delegada que deve fazer parte da mesma VNET que os servidores SAP. Aqui está um exemplo para essa configuração.
Esta tela mostra a criação da VNET e a primeira sub-rede:

![SAP LaMa criar rede virtual para seja do Azure ](media/lama/sap-lama-createvn-50.png)

A próxima etapa cria a sub-rede delegada para Microsoft. NetApp/volumes.

![LaMa do SAP-adicionar sub-rede delegada ](media/lama/sap-lama-addsubnet-50.png)

![Lista de sub-redes do SAP LaMa ](media/lama/sap-lama-subnets.png)

Agora, uma conta do NetApp precisa ser criada dentro do portal do Azure:

![SAP LaMa criar conta do NetApp ](media/lama/sap-lama-create-netappaccount-50.png)

![Conta do SAP LaMa NetApp criada ](media/lama/sap-lama-netappaccount.png)

Na conta do NetApp, o pool de capacidade especifica o tamanho e o tipo de discos para cada pool:

![SAP LaMa criar pool de capacidade do NetApp ](media/lama/sap-lama-capacitypool-50.png)

![Pool de capacidade do SAP LaMa NetApp criado ](media/lama/sap-lama-capacitypool-list.png)

Os volumes de NFS agora podem ser definidos. Como haverá volumes para vários sistemas em um pool, um esquema de nomenclatura autoexplicado deverá ser escolhido. Adicionar o SID ajuda a agrupar volumes relacionados juntos. Para o ASCS e a instância do as, são necessárias as seguintes montagens *: \<SID\> /sapmnt/*, */usr/SAP/ \<SID\>* e */Home/ \<sid\> ADM*. Opcionalmente, */usr/SAP/trans* é necessário para o diretório de transporte central, que é pelo menos usado por todos os sistemas de um cenário.

> [!NOTE]
> Durante a fase BETA, o nome dos volumes deve ser exclusivo na assinatura.

![SAP LaMa criar um volume 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP LaMa criar um volume 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP LaMa criar um volume 3 ](media/lama/sap-lama-createvolume3-80.png)

Essas etapas também precisam ser repetidas para os outros volumes.

![Lista de volumes criados do SAP LaMa ](media/lama/sap-lama-volumes.png)

Agora esses volumes precisam ser montados nos sistemas em que a instalação inicial com o SAP SWPM será executada.

Primeiro, os pontos de montagem precisam ser criados. Nesse caso, o SID é AN1 para que os seguintes comandos precisem ser executados:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
Em seguida, os volumes seja serão montados com os seguintes comandos:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
Os comandos mount também podem ser derivados do Portal. Os pontos de montagem locais precisam ser ajustados.

Use o comando df-h para verificar.

![Nível de sistema operacional dos pontos de montagem do SAP LaMa ](media/lama/sap-lama-mounts.png)

Agora, a instalação com SWPM deve ser executada.

As mesmas etapas devem ser executadas para pelo menos uma instância do AS.

Após a instalação bem-sucedida, o sistema deve ser descoberto no SAP LaMa.

Os pontos de montagem devem ser assim para o ASCS e a instância do as:

![Pontos de montagem do SAP LaMa no LaMa ](media/lama/sap-lama-ascs.png) (este é um exemplo. Os endereços IP e o caminho de exportação são diferentes dos usados antes)


#### <a name="install-sap-hana"></a>Instalar SAP HANA

Se você instalar o SAP HANA usando a ferramenta hdblcm da linha de comando, use o parâmetro – nome do host para fornecer um nome de host virtual. Você precisará adicionar o endereço IP do nome do host virtual do banco de dados para uma interface de rede. A maneira recomendada é usar sapacext. Se você montar o endereço IP usando sapacext, certifique-se de montar novamente o endereço IP após uma reinicialização.

Adicione outro nome de host virtual e endereço IP para o nome que é usado pelos servidores de aplicativo para se conectar ao locatário do HANA.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Execute a instalação da instância de banco de dados do SWPM na máquina virtual do servidor do aplicativo, não na máquina virtual do HANA. Use *db ah1* para o *Banco de Dados Host* na caixa de diálogo *Banco de Dados para o Sistema SAP*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Instale o servidor de aplicativos do SAP NetWeaver do SAP HANA

Antes de iniciar o SAP SWPM Software Provisioning Manager (SWPM), você precisará montar o endereço IP do nome de host virtual do servidor do aplicativo. A maneira recomendada é usar sapacext. Se você montar o endereço IP usando sapacext, certifique-se de montar novamente o endereço IP após uma reinicialização.

![Logotipo do Linux.][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Logotipo do Windows.][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

É recomendável usar o parâmetro do perfil SAP NetWeaver dbs/hdb/hdb_use_ident para definir a identidade que é usada para localizar a chave no userstore HDB. Você pode adicionar esse parâmetro manualmente após a instalação da instância de banco de dados com o SWPM ou executar SWPM com

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Se você defini-lo manualmente, você também precisará criar novas entradas de userstore HDB.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Use *ah1-di-0* para o *nome do Host de instância PAS* na caixa de diálogo *Instância Primária do Servidor de Aplicativo*.

#### <a name="post-installation-steps-for-sap-hana"></a>Etapas pós-instalação para o SAP HANA

Certifique-se de fazer backup de todos os bancos de dados do locatário e o SYSTEMDB antes de tentar fazer uma cópia do locatário, movimentação do locatário ou criar um sistema de replicação.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

Nos exemplos a seguir, vamos supor que você instale o SAP HANA com o sistema AS1 da ID e o sistema SAP NetWeaver com AH1 de ID do sistema. Os nomes de host virtuais são as1-db para a instância do Microsoft SQL Server, usado pelo sistema SAP NetWeaver, como 1-ascs para o ASC do SAP NetWeaver e as1-di-0 para o primeiro aplicativo do SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Instale o SAP NetWeaver ASCS para o SQL Server

Antes de iniciar o SAP SWPM Software Provisioning Manager (SWPM), você precisará montar o endereço IP do nome de host virtual do ASCS. A maneira recomendada é usar sapacext. Se você montar o endereço IP usando sapacext, certifique-se de montar novamente o endereço IP após uma reinicialização.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Execute o SWPM e use *as1-ascs* para o *Nome de Host de Instância do ASCS*.

#### <a name="install-sql-server"></a>Instale o SQL Server

Você precisará adicionar o endereço IP do nome do host virtual do banco de dados para uma interface de rede. A maneira recomendada é usar sapacext. Se você montar o endereço IP usando sapacext, certifique-se de montar novamente o endereço IP após uma reinicialização.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Execute a instalação da instância de banco de dados do SWPM na máquina de virtual do SQL server. Use o SAPINST_USE_HOSTNAME =*as1 db* para substituir o nome do host usado para se conectar ao SQL Server. Se você implantou a máquina virtual usando o modelo do Azure Resource Manager, certifique-se de definir o diretório usado para os arquivos de dados do banco de dados para *C:\sql\data* e o arquivo de log de banco de dados *C:\sql\log*.

Certifique-se de que o usuário *NT AUTHORITY\SYSTEM* tem acesso ao SQL Server e tem a função de servidor *sysadmin*. Para saber mais, confira a Nota SAP [1877727] e [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Instalar o Servidor de Aplicativos do SAP NetWeaver

Antes de iniciar o SAP SWPM Software Provisioning Manager (SWPM), você precisará montar o endereço IP do nome de host virtual do servidor do aplicativo. A maneira recomendada é usar sapacext. Se você montar o endereço IP usando sapacext, certifique-se de montar novamente o endereço IP após uma reinicialização.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Use *as1-di-0* para o *nome do Host de Instância PAS* na caixa de diálogo *Instância Primária do Servidor de Aplicativo*.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="errors-and-warnings-during-discover"></a>Erros e avisos durante a descoberta

* A permissão SELECT foi negada
  * [Microsoft][ODBC SQL Server Driver][SQL Server] A permissão SELECT foi negada no objeto 'log_shipping_primary_databases', 'msdb' banco de dados 'msdb', esquema 'dbo'. [SOAPFaultException]  
  A permissão SELECT foi negada no objeto 'log_shipping_primary_databases', 'msdb' banco de dados 'msdb', esquema 'dbo'.
  * Solução  
    Certifique-se de que *NT AUTHORITY\SYSTEM* pode acessar o SQL Server. Confira Nota do SAP [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Erros e aviso para a validação de instância

* Uma exceção foi gerada na validação de userstore HDB  
  * consulte o Visualizador de Log  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException: Exceção no validador com a ID 'RuntimeHDBConnectionValidator' (Validation: 'VALIDATION_HDB_USERSTORE'): Não foi possível recuperar o hdbuserstore  
    O userstore HANA não está no local correto
  * Solução  
    Certifique-se de que /usr/sap/AH1/hdbclient/install/installation.ini está correto

### <a name="errors-and-warnings-during-a-system-copy"></a>Erros e avisos durante uma Cópia do Sistema

* Ocorreu um erro ao validar o sistema de etapa de provisionamento
  * Causado por: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * Solução  
    Fazer backup de todos os bancos de dados na fonte de sistema do HANA

* Etapa de cópia do sistema *Iniciar* da instância de banco de dados
  * Operação do Agente de Host '000D3A282BC91EE8A1D76CF1F92E2944' (OperationException. FaultCode: '127', Mensagem: 'Falha na execução do comando. : [Microsoft][ODBC SQL Server Driver][SQL Server] O usuário não tem permissão para alterar o banco de dados 'AS2', o banco de dados não existe ou o banco de dados não está em um estado que permita verificações de acesso.')
  * Solução  
    Certifique-se de que *NT AUTHORITY\SYSTEM* pode acessar o SQL Server. Confira Nota do SAP [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Erros e avisos durante um Clone do Sistema

* Erro ao tentar registrar o agente de instância na etapa *Registro Forçado e Iniciar Agente de Instância* do servidor de aplicativos ou ASCS
  * Erro ao tentar registrar o agente de instância. (RemoteException: 'Falha ao carregar dados da instância do perfil'\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': não é possível acessar o perfil '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': arquivo ou diretório não existe.')
  * Solução  
   Certifique-se de que o compartilhamento de sapmnt no ASCS/SCS tenha acesso completo para SAP_AS1_GlobalAdmin

* Erro na etapa *Habilitar a proteção de inicialização para o Clone*
  * Falha ao abrir o arquivo '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' Causa: arquivo ou diretório inexistente
  * Solução  
    A conta de computador do servidor de aplicativos precisa de acesso de gravação ao perfil

### <a name="errors-and-warnings-during-create-system-replication"></a>Erros e Avisos durante a Replicação Criar Sistema

* Exceção ao clicar em criar Replicação Criar Sistema
  * Causado por: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException   Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * Solução  
    Teste se sapacext pode ser executado como `<hanasid`> adm

* Erro quando a cópia completa não estiver habilitada na Etapa de Armazenamento
  * Ocorreu um erro ao relatar uma mensagem de atributo de contexto para o caminho IStorageCopyData.storageVolumeCopyList:1 e campo targetStorageSystemId
  * Solução  
    Ignorar os Avisos na etapa e tentar novamente. Esse problema será corrigido em um novo pacote de suporte/patch do SAP LaMa.

### <a name="errors-and-warnings-during-relocate"></a>Erros e avisos durante a Realocação

* O caminho '/ usr/sap/AH1' não é permitido para reexports nfs.
  * Consulte a Nota SAP [2628497] para obter detalhes.
  * Solução  
    Adicione as exportações do ASCS para o perfil de agente de host do ASCS. Veja a Nota do SAP [2628497]

* Função não implementada ao realocar ASCS
  * Saída do Comando: exportfs: host: / usr/sap/AX1: Função não implementada
  * Solução  
    Certifique-se de que o serviço do servidor NFS está habilitado na máquina virtual de destino de realocação

### <a name="errors-and-warnings-during-application-server-installation"></a>Erros e Avisos durante a Instalação do Servidor de Aplicativo

* Erro ao executar a etapa de SAPinst: getProfileDir
  * ERRO: (último erro relatado pela etapa: ESAPinstException capturado na chamada de módulo: validador de etapa
'|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir' relatou um erro: Nó \\\as1-ascs\sapmnt\AS1\SYS\perfil não existe. Iniciar SAPinst no modo interativo para resolver esse problema)
  * Solução  
    Certifique-se de que o SWPM está em execução com um usuário que tenha acesso ao perfil. Este usuário pode ser configurado no assistente de Instalação do Servidor de Aplicativo

* Erro ao executar a etapa de SAPinst: askUnicode
  * ERRO: (último erro relatado pela etapa: ESAPinstException capturado na chamada de módulo: validador de etapa
'|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_getUnicode|ind|ind|ind|ind|unicode|0|askUnicode' reported an error: Start SAPinst em modo interativo para resolver este problema)
  * Solução  
    Se você usar um kernel do SAP recente, o SWPM não pode determinar se o sistema é um sistema unicode usando o servidor de mensagens do ASCS. Consulte a Nota SAP [2445033] para obter mais detalhes.  
    Esse problema será corrigido em um novo pacote de suporte/patch do SAP LaMa.  
    Defina o parâmetro de perfil OS_UNICODE=uc no perfil padrão do seu sistema SAP para contornar esse problema.

* Erro ao executar a etapa de SAPinst: dCheckGivenServer
  * Erro ao executar a etapa SAPinst: dCheckGivenServer "Version =" 1.0 "erro: (último erro relatado pela etapa: \<p> a instalação foi cancelada pelo usuário. \</p>
  * Solução  
    Certifique-se de que o SWPM está em execução com um usuário que tenha acesso ao perfil. Este usuário pode ser configurado no assistente de Instalação do Servidor de Aplicativo

* Erro ao executar a etapa SAPinst: checkClient
  * Erro ao executar a etapa SAPinst: checkClient "Version =" 1.0 "erro: (último erro relatado pela etapa: \<p> a instalação foi cancelada pelo usuário. \</p>)
  * Solução  
    Certifique-se de que o Microsoft ODBC driver para SQL Server está instalado na máquina virtual na qual você deseja instalar o servidor de aplicativos

* Erro ao executar a etapa de SAPinst: copyScripts
  * Último erro relatado pela etapa: Falha na chamada do sistema. DETALHES: Erro 13 (0x0000000d) (permissão negada) na execução de chamada do sistema ‘fopenU’ com parâmetro (\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), line (494) in file (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp), rastreamento de pilha:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream(args_t const& _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl(const CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Solução  
    Certifique-se de que o SWPM está em execução com um usuário que tenha acesso ao perfil. Este usuário pode ser configurado no assistente de Instalação do Servidor de Aplicativo

* Erro ao executar a etapa de SAPinst: askPasswords
  * Último erro relatado pela etapa: Falha na chamada do sistema. DETALHES: Erro 5 (0x00000005) (Acesso negado.) na execução de chamada do sistema 'NetValidatePasswordPolicy' com o parâmetro (...), linha (359) no arquivo (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp),rastreamento de pilha:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  CSiStepExecute.cpp: 764: CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp: 56: DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp: 85: DarkModeDialog::submit()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy(args_t const& _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,*****) const )
  * Solução  
    Make sure to add a Host rule in step *Isolation* to allow communication from the VM to the domain controller

## <a name="next-steps"></a>Próximas etapas
* [SAP HANA no guia de operações do Azure][hana-ops-guide]
* [Planejamento e implementação de Máquinas Virtuais do Azure para o SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação do DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
