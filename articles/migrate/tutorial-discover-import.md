---
title: Avaliar servidores locais por meio de um arquivo CSV importado com a Avaliação de Servidor das Migrações para Azure
description: Descreve como descobrir servidores locais para a migração para o Azure usando um arquivo CSV importado na Avaliação de Servidor das Migrações para Azure.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: dfa7ee941e2c373b02fe5fb2f2a648a60a677670
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753100"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>Tutorial: acessar servidores usando um arquivo CSV importado

Como parte de sua jornada de migração para o Azure, descubra seu inventário local e suas cargas de trabalho. 

Este tutorial mostra como avaliar computadores locais com a ferramenta Migrações para Azure: Avaliação de Servidor, usando um arquivo CSV (valores separados por vírgula) importado. 

Se você usar um arquivo CSV, não será preciso configurar o dispositivo das Migrações para Azure a fim de descobrir e avaliar servidores. Você pode controlar os dados que compartilha no arquivo, e grande parte dos dados é opcional. Esse método será útil se:

- Você quiser criar uma avaliação inicial rápida antes de implantar o dispositivo.
- Você não conseguir implantar o dispositivo das Migrações para Azure em sua organização.
- Não for possível compartilhar credenciais que permitam o acesso a servidores locais.
- As restrições de segurança impedem que você colete e envie os dados coletados pelo dispositivo ao Azure.

> [!NOTE]
> Não é possível migrar servidores importados usando um arquivo CSV.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Configurar uma conta do Azure
> * Configurar um projeto das Migrações para Azure
> * Preparar um arquivo CSV
> * Importar o arquivo
> * Avaliar servidores

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam as opções padrão sempre que possível. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Você pode adicionar até 20.000 servidores em um único arquivo CSV e em um projeto das Migrações para Azure. 
- Os nomes de sistema operacional especificados no arquivo CSV devem conter e corresponder a [nomes compatíveis](#supported-operating-system-names).


## <a name="prepare-an-azure-user-account"></a>Preparar uma conta de usuário do Azure

Para criar um projeto das Migrações para Azure, você precisará de uma conta com:
- Permissões de Colaborador ou Proprietário em uma assinatura do Azure.
- Permissões para registrar aplicativos do Azure Active Directory.

Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura. Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir as permissões da seguinte maneira:

1. No portal do Azure, pesquise por "assinaturas" e, em **Serviços**, selecione **Assinaturas**.

    ![Caixa de pesquisa para pesquisar a assinatura do Azure](./media/tutorial-discover-import/search-subscription.png)

2. Na página **Assinaturas**, selecione a assinatura na qual você deseja criar um projeto das Migrações para Azure. 
3. Na assinatura, selecione **Controle de acesso (IAM)**  > **Verificar o acesso**.
4. Em **Verificar o acesso**, procure a conta de usuário relevante.
5. Em **Adicionar uma atribuição de função**, clique em **Adicionar**.

    ![Pesquisar uma conta de usuário para verificar o acesso e atribuir uma função](./media/tutorial-discover-import/azure-account-access.png)

6. Em **Adicionar atribuição de função**, selecione a função Colaborador ou Proprietário e selecione a conta (azmigrateuser em nosso exemplo). Em seguida, clique em **Salvar**.

    ![Abre a página Adicionar atribuição de função para atribuir uma função à conta](./media/tutorial-discover-import/assign-role.png)

7. No portal, pesquise por usuários e, em **Serviços**, selecione **Usuários**.
8. Em **Configurações de usuário**, verifique se os usuários do Azure AD podem registrar aplicativos (definido como **Sim** por padrão).

    ![Verificar nas Configurações de Usuário se os usuários podem registrar aplicativos do Active Directory](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>Configurar um projeto

Configure um novo projeto das Migrações para Azure caso não tenha um.

1. No portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**.
2. Em **Serviços**, selecione **Migrações para Azure**.
3. Em **Visão geral**, selecione **Criar projeto**.
5. Em **Criar projeto**, selecione sua assinatura do Azure e o grupo de recursos. Crie um grupo de recursos, caso ainda não tenha um.
6. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia em que deseja criá-lo. Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Caixas para nome e região do projeto](./media/tutorial-discover-import/new-project.png)

7. Selecione **Criar**.
8. Aguarde alguns minutos até que o projeto das Migrações para Azure seja implantado.

A ferramenta **Migrações para Azure: Avaliação de Servidor** é adicionada por padrão ao novo projeto.

![Página mostrando a ferramenta de Avaliação de Servidor adicionada por padrão](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>Preparar o arquivo CSV

Baixe o modelo do CSV e adicione informações do servidor a ele.

### <a name="download-the-template"></a>Baixe o modelo

1. Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, selecione **Descobrir**.
2. Em **Descobrir computadores**, escolha **Importar usando CSV**.
3. Selecione **Baixar** para baixar o modelo CSV. Se preferir, também poderá [baixá-lo diretamente](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Baixar o modelo CSV](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>Adicionar informações do servidor

Colete dados do servidor e adicione-os ao arquivo CSV.

- Para coletar dados, você pode exportá-los das ferramentas usadas no gerenciamento do servidor local, como o VMware vSphere ou o CMDB (banco de dados de gerenciamento de configuração).
- Para analisar os dados de exemplo, baixe nosso [arquivo de exemplo](https://go.microsoft.com/fwlink/?linkid=2108405).

A tabela a seguir resume os campos de arquivo a serem preenchidos:

**Nome do campo** | **Obrigatório** | **Detalhes**
--- | --- | ---
**Nome do servidor** | Sim | Recomendamos especificar o FQDN (nome de domínio totalmente qualificado).
**Endereço IP** | Não | Endereço do servidor.
**Núcleos** | Sim | O número de núcleos do processador alocados para o servidor.
**Memória** | Sim | Total de RAM (MB) alocado para o servidor.
**Nome do SO** | Sim | Sistema operacional do servidor. <br/> Nomes de sistema operacional que correspondem ou contêm os nomes [desta](#supported-operating-system-names) lista são reconhecidos pela avaliação.
**Versão do SO** | Não | Versão do sistema operacional do servidor.
**Arquitetura do SO** | Não | Arquitetura do SO do servidor <br/> Os valores válidos são: x64, x86, amd64, 32 bits ou 64 bits
**Número de discos** | Não | Não é necessário se forem fornecidos detalhes de disco individuais.
**Tamanho do Disco 1**  | Não | Tamanho máximo do disco (GB).<br/>Para adicionar detalhes de outros discos, [adicione colunas](#add-multiple-disks) no modelo. Você pode adicionar até oito discos.
**Operações de leitura do Disco 1** | Não | Operações de leitura do disco por segundo.
**Operações de gravação do Disco 1** | Não | Operações de gravação do disco por segundo.
**Taxa de transferência de leitura do Disco 1** | Não | Dados lidos no disco por segundo (MB/s).
**Taxa de transferência de gravação do Disco 1** | Não | Dados gravados no disco por segundo (MB/s).
**Porcentagem de utilização de CPU** | Não | O percentual de CPU em uso.
**Porcentagem de utilização de memória** | Não | O percentual de RAM em uso.
**Total de operações de leitura de discos** | Não | Operações de leitura do disco por segundo.
**Total de operações de gravação de discos** | Não | Operações de gravação do disco por segundo.
**Taxa de transferência de leitura de discos** | Não | Dados lidos no disco (MB/s).
**Taxa de transferência do total de gravação de discos** | Não | Dados gravados no disco (MB/s).
**Taxa de transferência da entrada na rede** | Não | Dados recebidos pelo servidor (MB/s).
**Taxa de transferência da saída da rede** | Não | Dados transmitidos pelo servidor (MB/s).
**Tipo de firmware** | Não | Firmware do servidor. Os valores podem ser "BIOS" ou "UEFI".
**Endereço MAC**| Não | Endereço MAC do servidor.


### <a name="add-operating-systems"></a>Adicionar sistemas operacionais

A avaliação reconhece nomes de sistemas operacionais específicos. Qualquer nome especificado deve corresponder exatamente a uma das opções na [lista de nomes compatíveis](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Adicionar vários discos

O modelo fornece campos padrão para o primeiro disco. Você pode adicionar colunas semelhantes para até oito discos.

Por exemplo, para especificar todos os campos para um segundo disco, adicione estas colunas:

- Tamanho do disco 2
- Operações de leitura do disco 2
- Operações de gravação do disco 2
- Taxa de transferência de leitura do disco 2
- Taxa de transferência de gravação do disco 2


## <a name="import-the-server-information"></a>Importar as informações do servidor

Depois de adicionar informações ao modelo CSV, importe o arquivo CSV para a Avaliação de Servidor.

1. Nas Migrações para Azure, em **Descobrir computadores**, navegue até o modelo concluído.
2. Selecione **Importar**.
3. O status da importação é exibido.
    - Se surgirem avisos no status, corrija-os ou continue sem solucioná-los.
    - Para aprimorar a precisão da avaliação, aprimore as informações do servidor.
    - Para exibir e corrigir avisos, selecione **Baixar o .CSV de detalhes de avisos**. Esta operação baixa o CSV com avisos incluídos. Examine os avisos e corrija os problemas conforme necessário.
    - Se forem exibidos erros no status (o status da importação será mostrado como **Com falha**), você precisará corrigi-los para poder continuar com a importação:
        1. Baixe o CSV, que agora inclui os detalhes do erro.
        1. Examine os erros e solucione-os conforme necessário. 
        1. Carregue novamente o arquivo modificado novamente.
4. Se o status da importação for **Concluído**, as informações do servidor terão sido importadas. Atualize se o processo de importação não ter sido concluído.

## <a name="update-server-information"></a>Atualizar informações do servidor

Para atualizar as informações do servidor, importe os dados para o servidor novamente usando o mesmo **Nome do servidor**. O campo **Nome do servidor** não pode ser modificado. Atualmente, não há suporte para a exclusão de servidores.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Após a descoberta, verifique se os servidores são exibidos no portal do Azure:

1. Abra o painel das Migrações para Azure.
2. Na página **Migrações para Azure – Servidores** > **Migrações para Azure: Avaliação de Servidor**, selecione o ícone que exibe a contagem de **Servidores descobertos**.
3. Selecione a guia **Baseado em importação**.



## <a name="supported-operating-system-names"></a>Nomes de sistemas operacionais com suporte

Os nomes de sistema operacional fornecidos no CSV devem conter e corresponder aos desta lista. Do contrário, você não poderá avaliá-los. 

**A-H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Apple Mac OS X 10<br/>Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Criou um projeto das Migrações para Azure 
> * Descobriu servidores usando um arquivo CSV importado. Agora, execute uma avaliação para [Migração da VM do VMware para VMs do Azure](./tutorial-assess-vmware-azure-vm.md).