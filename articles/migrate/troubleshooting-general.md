---
title: Solucionar problemas das Migrações para Azure | Microsoft Docs
description: Fornece uma visão geral dos problemas conhecidos no serviço de Migrações para Azure e dicas de solução de erros comuns.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 15d3809b9a028fd2495c504e9bf19251dd051520
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372590"
---
# <a name="troubleshoot-azure-migrate"></a>Solucionar problemas das Migrações para Azure

As migrações para [Azure](migrate-services-overview.md) fornecem um hub de ferramentas da Microsoft para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros. Este documento fornece ajuda sobre como solucionar erros com as migrações para Azure, migrações para Azure: Avaliação do servidor e migrações para Azure: Migração de Servidor.

## <a name="azure-migrate-project-issues"></a>Problemas do projeto de migrações para Azure

### <a name="i-am-unable-to-find-my-existing-azure-migrate-project"></a>Não consigo encontrar meu projeto existente de migrações para Azure.

Há [duas versões](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) de migrações para Azure. Dependendo da versão na qual você criou o projeto, siga as etapas abaixo para localizar o projeto:

1. Se você estiver procurando um projeto criado com a versão anterior (experiência antiga) das migrações para Azure, siga as etapas abaixo para localizar o projeto.

    1. Vá para [portal do Azure](https://portal.azure.com), procure migrações para **Azure**.
    2. No painel migrações para Azure, você verá uma faixa que fala sobre o acesso a projetos mais antigos. Você verá essa faixa somente se tiver criado um projeto com a experiência antiga. Clique na faixa.

    ![Acessar projetos existentes](./media/troubleshooting-general/access-existing-projects.png)

    3. Agora, você verá a lista de projetos existentes criados com a versão anterior do migrações para Azure.

2. Se você estiver procurando um projeto criado com a versão atual (nova experiência), siga as etapas abaixo para localizar o projeto.

    1. Vá para [portal do Azure](https://portal.azure.com), procure migrações para **Azure**.
    2. No painel migrações para Azure, vá para a página **servidores** no painel esquerdo e selecione **alterar** no canto superior direito:

    ![Alternar para um projeto atual de migrações para Azure](./media/troubleshooting-general/switch-project.png)

    3. Selecione a **assinatura** apropriada e **migre o projeto**.

### <a name="i-am-unable-to-create-a-second-azure-migrate-project"></a>Não consigo criar um segundo projeto de migrações para Azure.

Siga as etapas abaixo para criar um novo projeto de migrações para Azure.

1. Vá para [portal do Azure](https://portal.azure.com), procure migrações para **Azure**.
2. No painel migrações para Azure, vá para a página **servidores** no painel esquerdo e selecione **alterar** no canto superior direito:

   ![Alterar projeto de migrações para Azure](./media/troubleshooting-general/switch-project.png)

3. Para criar um novo projeto, selecione **clique aqui** , conforme mostrado na captura de tela abaixo:

   ![Criar um segundo projeto de migrações para Azure](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Em quais regiões geográficas do Azure há suporte das Migrações para Azure?

Você pode encontrar a lista para [VMware aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) e para o [Hyper-V aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Exclusão de projetos de Migrações para Azure e espaço de trabalho do Log Analytics associado

Quando você exclui um projeto de migrações para Azure, ele exclui o projeto de migração junto com os metadados sobre computadores descobertos. No entanto, se você anexou um espaço de trabalho Log Analytics à ferramenta de avaliação do servidor, ele não excluirá automaticamente o espaço de trabalho Log Analytics. Isso ocorre porque o mesmo espaço de trabalho do Log Analytics pode ser usado para vários casos de uso. Se você desejar excluir o espaço de trabalho do Log Analytics, precisará fazer isso manualmente.

1. Navegue até o espaço de trabalho do Log Analytics associado ao projeto.
     1. Se você ainda não tiver excluído o projeto de migração, poderá encontrar o link para o espaço de trabalho na página Visão geral da avaliação do servidor na seção Essentials.

     ![Workspace do LA](./media/troubleshooting-general/loganalytics-workspace.png)

     2. Se você já tiver excluído o projeto de migração, selecione **grupos de recursos** no painel esquerdo na portal do Azure. Vá para o grupo de recursos no qual o espaço de trabalho foi criado e navegue até ele.
2. Siga as instruções [neste artigo](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) para excluir o workspace.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Falha na criação do projeto de migração com o erro *As solicitações devem conter cabeçalhos de identidade do usuário*

Esse problema pode acontecer para usuários que não têm acesso ao locatário do Azure AD (Azure Active Directory) da organização. Quando um usuário é adicionado a um locatário do Azure AD pela primeira vez, ele recebe um convite por email para ingressar no locatário. Os usuários precisam ir até o email e aceitar o convite para serem adicionados com êxito ao locatário. Se não for possível ver o email, entre em contato com um usuário que já tenha acesso ao locatário e solicite que ele envie novamente o convite novamente para você usando as etapas especificadas [aqui](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Depois que o email de convite for recebido, você precisará abri-lo e clicar no link contido nele para aceitar o convite. Quando isso for feito, você precisará sair do portal do Azure e entrar novamente, a atualização do navegador não funcionará. Em seguida, você pode tentar criar o projeto de migração.

## <a name="appliance-issues"></a>Problemas de dispositivo

### <a name="deployment-of-azure-migrate-appliance-for-vmware-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>Falha na implantação do dispositivo de migração do Azure para VMware com o erro: O arquivo de manifesto fornecido é inválido: Entrada inválida de manifesto de OVF.

1. Verifique se o arquivo OVA do dispositivo de migração do Azure foi baixado corretamente verificando seu valor de hash. Consulte o [artigo](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware) para verificar o valor de hash. Se o valor de hash não corresponde, baixe o arquivo OVA novamente e repita a implantação.
2. Se ele ainda falhar e se você estiver usando o VMware vSphere Client para implantar o OVF, tente implantá-lo por meio do vSphere Web Client. Se ele ainda falhar, tente usar outro navegador da Web.
3. Se você estiver usando o cliente Web do vSphere e tentar implantá-lo no vCenter Server 6.5 ou 6.7, tente implantar o arquivo OVA diretamente no host ESXi, seguindo as etapas a seguir:
   - Conecte-se diretamente ao host ESXi (em vez de vCenter Server) usando o cliente Web (*endereço IP do host*do https://< >/UI).
   - Vá para**inventário** **doméstico** > .
   - Clique em **arquivo** > **implantar modelo de OVF**. Navegue até o OVA e conclua a implantação.
4. Se a implantação ainda falhar, entre em contato com o suporte do Migrações para Azure.

### <a name="appliance-is-not-able-to-connect-to-the-internet"></a>O dispositivo não é capaz de se conectar à Internet

Isso pode acontecer quando a máquina que você está usando estiver atrás de um proxy. Certifique-se de fornecer as credenciais de autorização se precisar de um proxy.
Se você estiver usando qualquer proxy de firewall baseado em URL para controlar a conectividade de saída, certifique-se de adicionar essas URLs necessárias a uma lista de permissões:

Cenário | Lista de URLs
--- | ---
Avaliação do servidor para VMware | [Aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
Avaliação do servidor para Hyper-V | [Aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
Migração de servidor para VMware (sem agente) | [Aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
Migração de servidor para VMware (baseada em agente) | [Aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
Migração de servidor para Hyper-V | [Aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Se você estiver usando um proxy de interceptação para se conectar à Internet, será necessário importar o certificado de proxy para a VM do dispositivo. Você pode importar o certificado de proxy usando as etapas detalhadas [aqui](https://docs.microsoft.com/azure/migrate/concepts-collector).

### <a name="error-802-date-and-time-synchronization-error"></a>Erro 802: erro de sincronização de data e hora

O relógio do servidor pode estar fora de sincronização com a hora atual em mais de cinco minutos. Altere a hora do relógio na VM do coletor para corresponder à hora atual da seguinte maneira:

1. Abra um prompt de comando de administrador na VM.
2. Para verificar o fuso horário, execute w32tm /tz.
3. Para sincronizar a hora, execute w32tm /resync.


### <a name="error-unabletoconnecttoserver"></a>Erro UnableToConnectToServer

Não é possível se conectar ao vCenter Server "Servername.com:9443" devido ao erro: "Não havia nenhum ponto de extremidade escutando em https://Servername.com:9443/sdk para aceitar a mensagem." Isso geralmente é causado por um endereço incorreto ou ação SOAP.

Verifique se está executando a versão mais recente do dispositivo do coletor, caso não esteja, atualize o dispositivo para a [versão mais recente](https://docs.microsoft.com/azure/migrate/concepts-collector).

Se o problema ainda ocorrer na versão mais recente, poderá ser porque o computador do coletor não consegue resolver o nome do vCenter Server especificado ou a porta especificada está errada. Por padrão, se a porta não for especificada, o coletor tentará se conectar ao número da porta 443.

1. Tente executar ping para Servername.com do computador do coletor.
2. Se a etapa 1 falhar, tente conectar o servidor vCenter no endereço IP.
3. Identifique o número correto da porta para conectar o vCenter.
4. Finalmente, verifique se o servidor vCenter está em execução.


### <a name="the-appliance-could-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>O dispositivo não pôde ser registrado com êxito no projeto de migrações para Azure (ID do erro: 60052)

Esse erro ocorre devido a permissões insuficientes na conta do Azure usadas para registrar o dispositivo. Verifique se a conta de usuário do Azure usada para registrar o dispositivo tem pelo menos o acesso ' colaborador ' na assinatura. [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sobre as funções e permissões do Azure necessárias.

### <a name="the-appliance-could-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>O dispositivo não pôde ser registrado com êxito no projeto de migrações para Azure (ID do erro: 60039)

O projeto de migrações para Azure selecionado por você para registrar o dispositivo não foi encontrado, fazendo com que o registro falhe. Vá para a portal do Azure e verifique se o projeto existe em seu grupo de recursos. Se o projeto não existir, crie um novo projeto de migrações para Azure em seu grupo de recursos e registre o dispositivo novamente. [Saiba mais](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) sobre como criar um novo projeto de migrações para Azure.

### <a name="azure-key-vault-management-operation-failed-error-id-60030-60031"></a>Falha na operação de gerenciamento do cofre de chaves do Azure (ID do erro: 60030, 60031)

Verifique se a conta de usuário do Azure usada para registrar o dispositivo tem pelo menos o acesso ' colaborador ' na assinatura. Além disso, verifique se a conta tem acesso ao Key Vault especificado na mensagem de erro e repita a operação. Se o problema persistir, contate o Suporte da Microsoft. [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sobre as funções e permissões do Azure necessárias.

### <a name="discovery-could-not-be-initiated-due-to-the-error-the-operation-failed-for-the-given-list-of-hosts-or-clusters-error-id-60028"></a>Não foi possível iniciar a descoberta devido ao erro. A operação falhou para a lista determinada de hosts ou clusters (ID do erro: 60028)

Não foi possível iniciar a descoberta nos hosts listados no erro devido a um problema no acesso ou recuperação de informações da VM; o restante dos hosts que você adicionou foi adicionado com êxito. Adicione novamente os hosts no erro usando a opção **Adicionar host** . Se houver um erro de validação, examine as diretrizes de correção para corrigir os erros e tente **salvar e iniciar a descoberta** novamente.

### <a name="azure-active-directory-aad-operation-failed-the-error-occurred-while-creating-or-updating-the-aad-application-error-id-60025"></a>Falha na operação do Azure Active Directory (AAD). O erro ocorreu ao criar ou atualizar o aplicativo AAD (ID do erro: 60025)

A conta de usuário do Azure usada para registrar o dispositivo não tem acesso ao aplicativo AAD especificado na mensagem de erro. Verifique se você é o proprietário do aplicativo do AAD. [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sobre as permissões de aplicativo do AAD.


## <a name="discovery-issues"></a>Problemas de descoberta

### <a name="i-started-discovery-but-i-dont-see-the-discovered-vms-on-azure-portal-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>Comecei a descoberta, mas não vejo as VMs descobertas em portal do Azure. Os blocos avaliação do servidor e migração do servidor mostram o status "descoberta em andamento"
Depois de iniciar a descoberta do dispositivo, aguarde algum tempo para que os computadores descobertos apareçam no portal do Azure. Leva cerca de 15 minutos para uma descoberta do VMware e cerca de 2 minutos por host adicionado para uma descoberta do Hyper-V. Se você continuar a ver "descoberta em andamento" mesmo após esse período, clique em **Atualizar** na guia **servidores** . Isso deve mostrar a contagem dos servidores descobertos nos blocos avaliação do servidor e migração do servidor.


### <a name="i-am-using-the-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>Estou usando o dispositivo que descobre continuamente o meu ambiente local, mas as VMs que são excluídas no meu ambiente local ainda estão sendo mostradas no Portal.

Leva até 30 minutos para que os dados de descoberta sejam coletados pelo dispositivo para refletir no Portal. Se você não vir informações atualizadas mesmo após 30 minutos, emita uma atualização dos dados usando as seguintes etapas:

1. Em servidores > migrações para Azure: Avaliação do servidor, clique em **visão geral**.
2. Em **gerenciar**, clique em **integridade do agente**
3. Clique na opção para **atualizar o agente**. Você verá essa opção abaixo da lista de agentes.
4. Aguarde a conclusão da operação de atualização. Verifique se você consegue ver informações atualizadas em suas VMs.

### <a name="i-dont-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Não as informações mais recentes sobre as VMs locais no portal

Leva até 30 minutos para que os dados de descoberta sejam coletados pelo dispositivo para refletir no Portal. Se você não vir informações atualizadas mesmo após 30 minutos, emita uma atualização dos dados usando as seguintes etapas:

1. Em servidores > migrações para Azure: Avaliação do servidor, clique em **visão geral**.
2. Em **gerenciar**, clique em **integridade do agente**
3. Clique na opção para **atualizar o agente**. Você verá essa opção abaixo da lista de agentes.
4. Aguarde a conclusão da operação de atualização. Agora você deve ver informações atualizadas em suas VMs.

### <a name="unable-to-connect-to-hosts-or-cluster-as-the-server-name-cannot-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Não é possível se conectar a host (s) ou cluster, pois o nome do servidor não pode ser resolvido. Código de erro WinRM: 0x803381B9 (ID do erro: 50004)
Esse erro ocorre se o DNS que atende o dispositivo não puder resolver o cluster ou o nome do host fornecido. Se você vir esse erro no cluster, tente fornecer o nome de domínio totalmente qualificado do cluster.

Você também poderá ver esse erro para hosts em um cluster. Nesse caso, o dispositivo pode se conectar ao cluster. Mas o cluster retornou os nomes de host que não são nomes de domínio totalmente qualificados.

Para resolver esse erro, atualize o arquivo de hosts no dispositivo adicionando um mapeamento do endereço IP e nomes de host.
1. Abra o bloco de notas como usuário administrador. Abra o arquivo C:\Windows\System32\Drivers\etc\hosts.
2. Adicione o endereço IP e o nome do host em uma linha. Repita para cada host ou cluster em que você vê esse erro.
3. Salve e feche o arquivo de hosts.
4. Você pode verificar se o dispositivo pode se conectar aos hosts usando o aplicativo de gerenciamento de dispositivo. Após 30 minutos, você poderá ver as informações mais recentes sobre esses hosts no portal do Azure.


## <a name="assessment-issues"></a>Problemas de avaliação

### <a name="azure-readiness-issues"></a>Problemas de preparação do Azure

Problema | Correção
--- | ---
Tipo de inicialização não compatível | O Azure não oferece suporte a VMs com o tipo de inicialização EFI. Recomendamos que você converta o tipo de inicialização para BIOS antes de executar uma migração. <br/><br/>Você pode usar a migração de servidor de migrações para Azure para fazer a migração dessas VMs, pois ela converterá o tipo de inicialização da VM no BIOS durante a migração.
SO Windows com suporte condicional | O sistema operacional atingiu o fim da data de suporte e precisa de um Contrato de Suporte Personalizado (CSA) para [suporte no Azure](https://aka.ms/WSosstatement), considere o upgrade do sistema operacional antes de migrar para o Azure.
SO Windows sem suporte | O Azure suporta apenas as [versões de SO Windows selecionadas](https://aka.ms/WSosstatement), considere atualizar o sistema operacional do computador antes de migrar para o Azure.
SO Linux condicionalmente endossado | O Azure endossa apenas as [versões de SO Linux selecionadas](../virtual-machines/linux/endorsed-distros.md), considere atualizar o sistema operacional do computador antes de migrar para o Azure.
SO Linux não endossado | O computador pode ser inicializado no Azure, mas nenhum suporte de sistema operacional é fornecido pelo Azure, considere atualizar o sistema operacional para uma [versão do Linux endossada](../virtual-machines/linux/endorsed-distros.md) antes de migrar para o Azure
Sistema operacional desconhecido | O sistema operacional da VM foi especificado como 'Outro' no vCenter Server. Por esse motivo, o Migrações para Azure não consegue identificar a prontidão do Azure da VM. Certifique-se de que o sistema operacional em execução na máquina seja [compatível](https://aka.ms/azureoslist) com o Azure antes de migrar a máquina.
Número de bit do sistema operacional sem suporte | VMs com sistemas operacionais de 32 bits podem ser inicializadas no Azure, mas é recomendável atualizar o sistema operacional da VM de 32 bits para 64 bits antes de migrar para o Azure.
Exige uma assinatura do Visual Studio. | O computador tem um sistema operacional cliente Windows em execução dentro dele, que tem suporte apenas na assinatura do Visual Studio.
A VM não foi encontrada para o desempenho de armazenamento necessário. | O desempenho do armazenamento (IOPS/taxa de transferência) necessário para a máquina excede o suporte de VM do Azure. Reduza os requisitos de armazenamento para a máquina antes da migração.
A VM não foi encontrada para o desempenho de rede necessário. | O desempenho de rede (entrada/saída) necessário para a máquina excede o suporte de VM do Azure. Reduza os requisitos de rede para a máquina.
A VM não foi encontrada no local especificado. | Use um local de destino diferente antes da migração.
Um ou mais discos inadequados. | Um ou mais discos anexados à VM não atendem aos requisitos do Azure. Para cada disco anexado à VM, certifique-se de que o tamanho do disco seja < 4 TB, caso contrário, reduza o tamanho de disco antes de migrar para o Azure. Certifique-se de que o desempenho (IOPS/taxa de transferência) necessário para cada disco seja compatível com os [discos de máquina virtual gerenciada](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits) do Azure.   
Um ou mais adaptadores de rede inadequados. | Remova os adaptadores de rede não utilizados do computador antes da migração.
A contagem de discos excede o limite | Remova os discos não utilizados do computador antes da migração.
O tamanho do disco excede o limite | O Azure oferece suporte a discos com tamanho de até 4 TB. Reduza discos para menos de 4 TB antes da migração.
O disco não está disponível no local especificado | Verifique se o disco está em seu local de destino antes de migrar.
O disco não está disponível para a redundância especificada | O disco deve usar o tipo de armazenamento de redundância definido nas configurações de avaliação (LRS por padrão).
Não foi possível determinar a adequação do disco devido a um erro interno | Tente criar uma nova avaliação para o grupo.
VM com núcleos necessários e memória não encontrada | O Azure não pôde encontrar um tipo adequado de VM. Reduza a memória e o número de núcleos da máquina local antes de migrar.
Não foi possível determinar a adequação da VM devido a um erro interno. | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação de um ou mais discos devido a um erro interno. | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação de um ou mais adaptadores de rede devido a um erro interno. | Tente criar uma nova avaliação para o grupo.

### <a name="i-am-unable-to-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Não consigo especificar Enterprise Agreement (EA) como uma oferta do Azure nas propriedades de avaliação?
Migrações para Azure: A Avaliação do Servidor atualmente não tem suporte para ofertas baseadas em EA (Enterprise Agreement). A solução alternativa é usar uma oferta de “Pagamento Conforme o Uso” como a oferta do Azure e usar a propriedade “Desconto” para especificar algum desconto personalizado que você tenha recebido. [Saiba mais sobre como é possível personalizar uma avaliação](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready-is-there-anything-i-need-to-do-to-fix-this"></a>Por que a avaliação do servidor marca minhas VMs Linux de forma condicional. Há algo que preciso fazer para corrigir isso?
Há uma lacuna conhecida na Avaliação de Servidor em que não é possível detectar a versão secundária do sistema operacional Linux instalado nas VMs locais (por exemplo, para o RHEL 6.10, atualmente, a Avaliação de Servidor detecta somente RHEL 6 como a versão do sistema operacional). Como o Azure endossa apenas versões específicas do Linux, as VMs do Linux estão marcadas atualmente como condicionalmente pronto na Avaliação do Servidor. Você pode garantir manualmente se o sistema operacional Linux em execução na VM local é endossado no Azure, revisando a [documentação de suporte do Linux do Azure.](https://aka.ms/migrate/selfhost/azureendorseddistros) Depois de verificar a distribuição endossada, ignore este aviso.

### <a name="the-vm-sku-recommended-by-server-assessment-has-more-number-of-cores-and-a-larger-memory-size-than-what-is-allocated-on-premises-why-is-that-so"></a>A SKU de VM recomendada pela avaliação do servidor tem mais de núcleos e um tamanho de memória maior do que o que é alocado localmente. Por que isso?
A recomendação de SKU de VM na Avaliação de Servidor depende das propriedades da avaliação. Você pode criar dois tipos de avaliações na Avaliação de Servidor, avaliações “baseada em desempenho” e “como no local”. Para avaliações baseadas em desempenho, a avaliação do servidor considera os dados de utilização das VMs locais (CPU, memória, disco e utilização de rede) para determinar o SKU de VM de destino correto para suas VMs locais. Além disso, para o dimensionamento com base no desempenho, o fator conforto é levado em conta para identificar a utilização eficiente. No dimensionamento local, os dados de desempenho não são considerados e uma SKU de destino é recomendada com base no que é alocado no local.

Por exemplo, digamos que haja uma VM local com 4 núcleos e 8 GB de memória com 50% de utilização da CPU e 50% de utilização da memória, e um fator de conforto de 1,3 seja especificado na avaliação. No entanto, se o critério de dimensionamento da avaliação for ' como local ', um SKU de VM do Azure com 4 núcleos e 8 GB de memória será recomendado, no entanto, se o critério de dimensionamento for baseado em desempenho, com base na utilização efetiva de CPU e memória (50% de 4 núcleos * 1,3 = 2,6 núcleos e 50% de 8 GB memória * 1,3 = 5,3-GB de memória), o SKU de VM mais barato de quatro núcleos (contagem de núcleos mais próximo com suporte) e o tamanho de memória de 8 GB (tamanho de memória mais próximo com suporte) seriam recomendados. [Saiba mais sobre como a Avaliação de Servidor executa o dimensionamento.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

### <a name="the-disk-sku-recommended-by-server-assessment-has-a-bigger-size-than-what-is-allocated-on-premises-why-is-that-so"></a>O SKU de disco recomendado pela avaliação do servidor tem um tamanho maior do que o que é alocado localmente. Por que isso?
O dimensionamento de disco na Avaliação de Servidor depende de duas propriedades de avaliação: tipo de armazenamento e critério de dimensionamento. Se o critério de dimensionamento for "baseado em desempenho" e o tipo de armazenamento estiver definido como "automático", os valores de IOPS e taxa de transferência do disco serão considerados para identificar o tipo de disco de destino (HDD Standard, SSD Standard ou discos Premium). Um disco de SKU no tipo de disco, em seguida, é recomendado considerando os requisitos de tamanho do disco local. Se o critério de dimensionamento for "baseado em desempenho" e o tipo de armazenamento for "Premium", um SKU de disco Premium no Azure será recomendado com base nos requisitos de IOPS, taxa de transferência e tamanho do disco local. A mesma lógica é usada para dimensionamento do disco quando o critério de dimensionamento é dimensionamento local e o tipo de armazenamento é “HDD Standard”, “SSD Standard” ou “Premium”.

Por exemplo, se você tiver um disco local com memória de 32 GB, mas o IOPS agregado de leitura e gravação para o disco for de 800 IOPS, a avaliação do servidor recomendará um tipo de disco Premium (devido aos requisitos de IOPS mais altos) e, em seguida, recomendará uma SKU de disco, que pode dar suporte ao IOPS e tamanho necessários. A correspondência mais próxima neste exemplo seria P15 (256 GB, IOPS de 1100). Portanto, mesmo que o tamanho necessário para o disco local fosse 32 GB, a Avaliação de Servidor recomendou um disco com tamanho maior devido à necessidade de IOPS alto do disco local.

### <a name="why-does-my-assessment-report-say-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>Por que meu relatório de avaliação diz "PercentageOfCoresUtilizedMissing" ou "PercentageOfMemoryUtilizedMissing" para algumas VMs?
Os problemas acima são listados quando o dispositivo de migrações para Azure não pode coletar dados de desempenho para as VMs locais. Isso pode acontecer se as VMs estiverem desligadas durante o período durante o qual você está criando a avaliação (último dia/uma semana/um mês), uma vez que o dispositivo não pode coletar dados de desempenho para uma VM, quando ela estiver desligada. Se apenas os contadores de memória estiverem ausentes e você estiver tentando avaliar as VMs do Hyper-V, verifique se você tem memória dinâmica habilitada nessas VMs. Há um problema conhecido atualmente devido ao qual o dispositivo de migração do Azure não pode coletar a utilização de memória para VMs que não têm memória dinâmica habilitada. Observe que o problema é apenas para VMs do Hyper-V e não para VMs do VMware. Se algum dos contadores de desempenho estiver ausente, migrações para Azure: A avaliação do servidor volta para os núcleos/memória alocados e recomenda um tamanho de VM de acordo.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>O custo de licença do sistema operacional da VM está incluído no custo de computação estimado pela avaliação do servidor?
A Avaliação do Servidor atualmente só considera o custo de licença do sistema operacional para computadores Windows; o custo de licença do sistema operacional para as máquinas Linux não é levado em conta no momento.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Que impacto que a utilização do percentil e o histórico de desempenho têm sobre as recomendações de tamanho?
Essas propriedades só são aplicáveis ao dimensionamento “Com base no desempenho”. A Avaliação de Servidor coleta continuamente os dados de desempenho dos computadores locais e o utiliza para recomendar a SKU da VM e a SKU de disco no Azure. Abaixo vemos como os dados de desempenho são coletados pela Avaliação do Servidor:
- O dispositivo de migração do Azure cria o perfil continuamente no ambiente local para coletar dados de utilização em tempo real a cada 20 segundos para VMs VMware e a cada 30 segundos para VMs do Hyper-V.
- O dispositivo acumula as amostras de 20/30 segundos para criar um único ponto de dados a cada 10 minutos. Para criar o ponto de dados único, o dispositivo seleciona o valor de pico de todas as amostras de 20/30 segundos e envia-o para o Azure.
- Quando você cria uma avaliação no Servidor de Avaliação com base na duração de desempenho e no valor de percentil do histórico de desempenho, o valor de utilização representativa é identificado. Por exemplo, se o histórico de desempenho for uma semana e a utilização do percentil for 95 º, as migrações para Azure classificarão todos os pontos de exemplo de 10 minutos da última semana em ordem crescente e, em seguida, selecionaremos o 95 º percentil como o valor representativo.
O valor do 95º percentil faz com que você ignore eventuais exceções que possam ser incluídos se você escolher o 99º percentil. Se você deseja escolher o pico de uso para o período e não quiser perder exceções, deve selecionar o 99 º percentil como utilização do percentil.

## <a name="dependency-visualization-issues"></a>Problemas de visualização de dependência

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Não consigo encontrar a funcionalidade de visualização de dependência para projetos do Azure Governamental.

As Migrações para Azure dependem do Mapa do Serviço para a funcionalidade de visualização de dependência e, uma vez que o Mapa do Serviço não está disponível no momento no Azure Governamental, essa funcionalidade não está disponível no Azure Governamental.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Instalei o MMA (Microsoft Monitoring Agent) e o Dependency Agent em minhas VMs locais, mas as dependências agora estão aparecendo no portal de Migrações para Azure.

Depois de instalar os agentes, as Migrações para Azure normalmente levam de 15 a 30 minutos para exibir as dependências no portal. Se você precisar esperar mais de 30 minutos, verifique se o agente MMA é capaz de comunicar-se com o workspace do OMS seguindo as etapas a seguir:

Para VM do Windows:
1. Vá para o **painel de controle** e inicie o **Microsoft Monitoring Agent**.
2. Vá para a guia **log Analytics do Azure (OMS)** no pop-up Propriedades de MMA.
3. Verifique se o **Status** do workspace está verde.
4. Se o status não estiver verde, tente remover o workspace e adicioná-lo novamente no MMA.
        ![Status do MMA](./media/troubleshooting-general/mma-status.png)

Para VM do Linux, verifique se os comandos de instalação do agente do MMA e do Dependency Agent foram executados com êxito.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Quais são os sistemas operacionais com suporte no MMA?

A lista de sistemas de operacionais Windows com suporte no MMA está [aqui](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
A lista de sistemas operacionais Linux com suporte no MMA está [aqui](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Quais são os sistemas operacionais com suporte no Dependency Agent?

A lista de sistemas de operacionais Windows com suporte no Dependency Agent está [aqui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
A lista de sistemas de operacionais Linux com suporte no Dependency Agent está [aqui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Não consigo visualizar as dependências nas Migrações para Azure por um período maior de uma hora?
As Migrações para Azure permitem que você visualize as dependências por um período de até uma hora. Embora as Migrações para Azure permitam que você volte para uma data específica no histórico de até um mês atrás, a duração máxima em que você pode visualizar as dependências é de até uma hora. Por exemplo, você pode usar a funcionalidade de duração de tempo no mapa de dependências para exibir as dependências de ontem, mas só pode exibi-las durante o período de uma hora. No entanto, você pode usar os logs de Azure Monitor para [consultar os dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) por uma duração maior.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Não consigo visualizar as dependências para grupos com mais de 10 VMs?
Você pode [visualizar as dependências para grupos](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) de até 10 VMs. No caso de um grupo com mais de 10 VMs, é recomendado dividir o grupo em grupos menores e visualizar as dependências.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Eu instalei agentes e usei a visualização de dependência para criar grupos. Agora, após o failover, as máquinas mostram a ação de "Instalar o agente" em vez de "Exibir dependências"
* O failover pós-planejado ou não planejado, as máquinas locais são desativadas e as máquinas equivalentes são criadas no Azure. Essas máquinas adquirem um endereço MAC diferente. Elas podem adquirir um endereço IP diferente com base em se o usuário optou por reter o endereço IP local ou não. Se os endereços IP e MAC forem diferentes, as Migrações para Azure não associarão as máquinas locais com os dados de dependência do Mapa do Serviço e solicitarão que o usuário instale os agentes em vez de exibir as dependências.
* Após o failover de teste, as máquinas locais permanecem ativadas conforme o esperado. As máquinas equivalentes criadas no Azure adquirem um endereço MAC diferente e podem adquirir um endereço IP diferente. A menos que o usuário bloqueie o tráfego de logs de Azure Monitor de saída desses computadores, as migrações para Azure não associam os computadores locais a qualquer Mapa do Serviço dados de dependência e solicitam que o usuário instale agentes em vez de exibir dependências.

## <a name="collect-azure-portal-logs"></a>Coletar logs de portal do Azure

**Como fazer coletar portal do Azure logs de tráfego de rede?**

1. Abra o navegador e navegue e entre no [portal](https://portal.azure.com).
2. Pressione F12 para iniciar as Ferramentas para Desenvolvedores. Se necessário, desmarque a configuração **Limpar entradas na navegação**.
3. Clique na guia **Rede** e inicie a captura do tráfego de rede:
   - No Chrome, selecione **Preservar log**. A gravação deve ser iniciada automaticamente. Um círculo vermelho indica que o tráfego está sendo capturado. Se ele não aparecer, clique no círculo preto para iniciar.
   - No Microsoft Edge/IE, a gravação deve ser iniciada automaticamente. Se não estiver, clique no botão verde para executar.
4. Tente reproduzir o erro.
5. Depois que você tiver encontrado o erro durante a gravação, interrompa a gravação e salve uma cópia da atividade registrada:
   - No Chrome, clique com o botão direito e clique em **Salvar como HAR com conteúdo**. Isso compacta e exporta os logs como um arquivo. Har.
   - No IE/Microsoft Edge, clique no ícone **Exportar tráfego capturado**. Isso compacta e exporta o log.
6. Navegue até a guia **Console** para verificar se há avisos ou erros. Para salvar o log do console:
   - No Chrome, clique com o botão direito em qualquer lugar no log do console. Selecione **salvar como**, para exportar e compactar o log.
   - No Microsoft Edge/IE, clique com o botão direito do mouse nos erros e selecione **Copiar tudo**.
7. Fechar as Ferramentas para Desenvolvedores.
