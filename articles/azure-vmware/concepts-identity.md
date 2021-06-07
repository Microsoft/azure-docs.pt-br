---
title: Conceitos-identidade e acesso
description: Saiba mais sobre os conceitos de identidade e acesso da solução do Azure VMware
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 7b1880ccdee1d4c3ce25e264c004b1a949134a96
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026897"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Conceitos de identidade da solução VMware do Azure

As nuvens privadas da solução Azure VMware são provisionadas com um vCenter Server e o NSX-T Manager. Use o vCenter para gerenciar cargas de trabalho de máquina virtual (VM) e o Gerenciador de NSX-T para gerenciar e estender a nuvem privada. Gerenciamento de acesso e identidade use a função CloudAdmin para o vCenter e direitos de administrador restritos para o NSX-T Manager. 

Para obter mais informações, consulte o [artigo conceitos de atualizações de nuvem privada][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>acesso e identidade do vCenter

Na solução Azure VMware, o vCenter tem um usuário local interno chamado cloudadmin e atribuído à função CloudAdmin. O usuário local cloudadmin é usado para configurar usuários no Active Directory (AD). Em geral, a função CloudAdmin cria e gerencia cargas de trabalho em sua nuvem privada. Mas, na solução Azure VMware, a função CloudAdmin tem privilégios de vCenter que diferem de outras soluções de nuvem VMware.     

- Em uma implantação local do vCenter e ESXi, o administrador tem acesso à conta do administrador do vCenter \@ vSphere. local. Eles também podem ter mais usuários e grupos do AD atribuídos. 

- Em uma implantação de solução do Azure VMware, o administrador não tem acesso à conta de usuário administrador. No entanto, eles podem atribuir usuários e grupos do AD à função CloudAdmin no vCenter.  

O usuário da nuvem privada não tem acesso ao e não pode configurar componentes de gerenciamento específicos com suporte e gerenciados pela Microsoft. Por exemplo, clusters, hosts, repositórios de armazenamento e comutadores virtuais distribuídos.

> [!IMPORTANT]
> A solução Azure VMware oferece funções personalizadas no vCenter, mas atualmente não as oferece no portal de solução do Azure VMware. Para obter mais informações, consulte a seção [criar funções personalizadas no vCenter](#create-custom-roles-on-vcenter) posteriormente neste artigo. 

### <a name="view-the-vcenter-privileges"></a>Exibir os privilégios do vCenter

Você pode exibir os privilégios concedidos à função CloudAdmin da solução VMware do Azure em sua nuvem privada da solução Azure VMware vCenter.

1. Faça logon no cliente SDDC vSphere e vá para o **menu**  >  **Administração**.
1. Em **controle de acesso**, selecione **funções**.
1. Na lista de funções, selecione **CloudAdmin** e, em seguida, selecione **privilégios**. 

   :::image type="content" source="media/role-based-access-control-cloudadmin-privileges.png" alt-text="Como exibir os privilégios da função CloudAdmin no cliente vSphere":::

A função CloudAdmin na solução VMware do Azure tem os seguintes privilégios no vCenter. Para obter mais detalhes, consulte a [documentação do produto VMware](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html).

| Privilege | Descrição |
| --------- | ----------- |
| **Alarmes** | Reconhecer alerta<br />Criar alarme<br />Desabilitar ação de alarme<br />Modificar alarme<br />Remover alarme<br />Definir status do alarme |
| **Biblioteca de conteúdo** | Adicionar item de biblioteca<br />Criar uma assinatura para uma biblioteca publicada<br />Criar biblioteca local<br />Criar biblioteca inscrita<br />Excluir item de biblioteca<br />Excluir biblioteca local<br />Excluir biblioteca assinada<br />Excluir a assinatura de uma biblioteca publicada<br />Baixar arquivos<br />Remover itens de biblioteca<br />Remover biblioteca assinada<br />Importar armazenamento<br />Informações de assinatura de investigação<br />Publicar um item de biblioteca em seus assinantes<br />Publicar uma biblioteca em seus assinantes<br />Armazenamento de leitura<br />Sincronizar item de biblioteca<br />Sincronizar biblioteca assinada<br />Introspecção de tipo<br />Atualizar definições de configuração<br />Arquivos de atualização<br />Atualizar biblioteca<br />Atualizar item de biblioteca<br />Atualizar biblioteca local<br />Atualizar biblioteca assinada<br />Atualizar a assinatura de uma biblioteca publicada<br />Exibir definições de configuração |
| **Operações criptográficas** | Acesso direto |
| **Datastore** | Alocar espaço<br />Procurar no Repositório de Dados<br />Configurar repositório de armazenamento<br />Operações de arquivo de nível baixo<br />Remover arquivos<br />Atualizar metadados da máquina virtual |
| **Pasta** | Criar pasta<br />Excluir pasta<br />Mover pasta<br />Renomear pasta |
| **Global** | Cancelar tarefa<br />Marca global<br />Saúde<br />Evento de log<br />Gerenciar atributos personalizados<br />Gerenciadores de serviços<br />Definir atributo personalizado<br />Marca do sistema |
| **Host** | Replicação do vSphere<br />&#160;&#160;&#160;&#160;gerenciar a replicação |
| **Rede** | Assign network |
| **Permissões** | Modificar permissões<br />Modificar função |
| **Perfil** | Exibição de armazenamento controlada por perfil |
| **Recurso** | Aplicar recomendação<br />Atribuir vApp ao pool de recursos<br />Assign virtual machine to resource pool<br />Criar pool de recursos<br />Migrar máquina virtual desligada<br />Migrar máquina virtual ligada<br />Modificar pool de recursos<br />Mover pool de recursos<br />Consultar o vMotion<br />Remover pool de recursos<br />Renomear pool de recursos |
| **Tarefa agendada** | Criar tarefa<br />Modificar tarefa<br />Remover tarefa<br />Executar tarefa |
| **Sessões** | Mensagem<br />Validar sessão |
| **Exibição de armazenamento** | Visualizar |
| **vApp** | Adicionar máquina virtual<br />Atribuir pool de recursos<br />Atribuir vApp<br />Clone<br />Criar<br />Excluir<br />Exportação<br />Importar<br />Mover<br />Desligar<br />Ligue<br />Renomear<br />Suspend<br />Cancelar o registro <br />Exibir ambiente OVF<br />configuração do aplicativo vApp<br />configuração da instância de vApp<br />configuração do vApp managedBy<br />configuração do recurso vApp |
| **Máquina virtual** | Alterar Configuração<br />&#160;&#160;&#160;&#160;adquirir concessão de disco<br />&#160;&#160;&#160;&#160;adicionar disco existente<br />&#160;&#160;&#160;&#160;adicionar novo disco<br />&#160;&#160;&#160;&#160;adicionar ou remover dispositivo<br />Configuração avançada do &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;alterar contagem de CPU<br />&#160;&#160;&#160;&#160;alterar a memória<br />&#160;&#160;&#160;&#160;alterar configurações<br />&#160;&#160;&#160;&#160;alterar o posicionamento do Swapfile<br />&#160;&#160;&#160;&#160;alterar recurso<br />&#160;&#160;&#160;&#160;configurar o dispositivo USB do host<br />&#160;&#160;&#160;&#160;configurar o dispositivo bruto<br />&#160;&#160;&#160;&#160;configurar o managedBy<br />&#160;&#160;&#160;&#160;exibir configurações de conexão<br />Estender o disco virtual &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;modificar as configurações do dispositivo<br />Compatibilidade de tolerância a falhas de consulta &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;consultar arquivos sem proprietário<br />Recarregar &#160;&#160;&#160;&#160;de caminhos<br />&#160;&#160;&#160;&#160;remover disco<br />Renomear &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;redefinir informações de convidado<br />&#160;&#160;&#160;&#160;definir anotação<br />&#160;&#160;&#160;&#160;alternar o controle de alterações do disco<br />&#160;&#160;&#160;&#160;alternar bifurcação pai<br />&#160;&#160;&#160;&#160;atualizar a compatibilidade da máquina virtual<br />Editar inventário<br />&#160;&#160;&#160;&#160;criar a partir de existente<br />&#160;&#160;&#160;&#160;criar novo<br />&#160;&#160;&#160;&#160;mover<br />Registro de &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;remover<br />&#160;&#160;&#160;&#160;cancelar o registro<br />Operações de convidado<br />Modificação de alias de operação de convidado &#160;&#160;&#160;&#160;<br />Consulta de alias de operação de convidado &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;modificações de operação de convidado<br />&#160;&#160;&#160;&#160;execução do programa de operação de convidado<br />&#160;&#160;&#160;&#160;consultas de operação de convidado<br />Interação<br />&#160;&#160;&#160;&#160;pergunta de resposta<br />&#160;&#160;&#160;&#160;operação de backup na máquina virtual<br />&#160;&#160;&#160;&#160;configurar mídia de CD<br />&#160;&#160;&#160;&#160;configurar mídia de disquete<br />Dispositivos &#160;&#160;&#160;&#160;Connect<br />Interação do console do &#160;&#160;&#160;&#160;<br />Criar captura de tela &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;desfragmentar todos os discos<br />Arrastar e soltar &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;o gerenciamento do sistema operacional convidado pela API do VIX<br />&#160;&#160;&#160;&#160;injetar códigos de verificação de HID USB<br />&#160;&#160;&#160;&#160;instalar as ferramentas do VMware<br />&#160;&#160;&#160;&#160;pausar ou pausar<br />&#160;&#160;&#160;&#160;operações de apagamento ou redução<br />&#160;&#160;&#160;&#160;desligar<br />Ligar &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;sessão de registro na máquina virtual<br />&#160;&#160;&#160;&#160;a sessão de reprodução na máquina virtual<br />&#160;&#160;&#160;&#160;suspender<br />Tolerância a falhas de suspensão &#160;&#160;&#160;&#160;<br />Failover de teste do &#160;&#160;&#160;&#160;<br />VM secundária de reinício de teste de &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;desativar a tolerância a falhas<br />&#160;&#160;&#160;&#160;ativar a tolerância a falhas<br />Provisionamento<br />&#160;&#160;&#160;&#160;permitir acesso ao disco<br />&#160;&#160;&#160;&#160;permitir o acesso ao arquivo<br />&#160;&#160;&#160;&#160;permitir acesso a disco somente leitura<br />&#160;&#160;&#160;&#160;permitir download de máquina virtual<br />&#160;&#160;&#160;&#160;o modelo de clonagem<br />Clonar &#160;&#160;&#160;&#160;máquina virtual<br />&#160;&#160;&#160;&#160;criar modelo a partir da máquina virtual<br />&#160;&#160;&#160;&#160;personalizar o convidado<br />&#160;&#160;&#160;&#160;implantar modelo<br />&#160;&#160;&#160;&#160;marcar como modelo<br />&#160;&#160;&#160;&#160;modificar especificação de personalização<br />&#160;&#160;&#160;&#160;promover discos<br />&#160;&#160;&#160;&#160;ler especificações de personalização<br />Configuração de serviço<br />&#160;&#160;&#160;&#160;permitir notificações<br />&#160;&#160;&#160;&#160;permitir sondagem de notificações de eventos globais<br />&#160;&#160;&#160;&#160;gerenciar a configuração de serviço<br />&#160;&#160;&#160;&#160;modificar a configuração de serviço<br />&#160;&#160;&#160;&#160;configurações do serviço de consulta<br />&#160;&#160;&#160;&#160;configuração do serviço de leitura<br />Gerenciamento de instantâneo<br />&#160;&#160;&#160;&#160;criar instantâneo<br />&#160;&#160;&#160;&#160;remover instantâneo<br />Renomear instantâneo &#160;&#160;&#160;&#160;<br />Reverta o instantâneo &#160;&#160;&#160;&#160;<br />Replicação do vSphere<br />&#160;&#160;&#160;&#160;configurar a replicação<br />&#160;&#160;&#160;&#160;gerenciar a replicação<br />&#160;&#160;&#160;&#160;monitorar a replicação |
| **vService** | Criar dependência<br />Destruir dependência<br />Reconfigurar a configuração de dependência<br />Atualizar dependência |
| **marcação de vSphere** | Atribuir e cancelar a atribuição de marca vSphere<br />Criar marca vSphere<br />Criar categoria de marca vSphere<br />Excluir marca vSphere<br />Excluir categoria de marca vSphere<br />Editar marca vSphere<br />Editar categoria da marca vSphere<br />Modificar o campo UsedBy para a categoria<br />Modificar campo UsedBy para marca |

### <a name="create-custom-roles-on-vcenter"></a>Criar funções personalizadas no vCenter

A solução Azure VMware dá suporte ao uso de funções personalizadas com privilégios iguais ou menores que a função CloudAdmin. 

A função CloudAdmin pode criar, modificar ou excluir funções personalizadas que têm privilégios menores ou iguais à sua função atual. Você pode criar funções que têm privilégios maiores que CloudAdmin, mas não será possível atribuir a função a nenhum usuário ou grupo ou excluir a função.

Para evitar a criação de funções que não podem ser atribuídas ou excluídas, a solução Azure VMware recomenda a clonagem da função CloudAdmin como a base para a criação de novas funções personalizadas.

#### <a name="create-a-custom-role"></a>Criar uma função personalizada
1. Entre no vCenter com cloudadmin \@ vSphere. local ou um usuário com a função cloudadmin.
2. Navegue até a seção configuração de **funções** e selecione **menu**  >  **Administração**  >  funções de controle de **acesso**  >  .
3. Selecione a função **CloudAdmin** e selecione o ícone de **ação clonar função** .

   > [!NOTE] 
   > Não clone a função de **administrador** . Essa função não pode ser usada e a função personalizada criada não pode ser excluída por cloudadmin \@ vSphere. local.

4. Forneça o nome desejado para a função clonada.
5. Adicione ou remova privilégios para a função e selecione **OK**. A função clonada agora deve estar visível na lista de **funções** .


#### <a name="use-a-custom-role"></a>Usar uma função personalizada

1. Navegue até o objeto que requer a permissão adicional. Por exemplo, para aplicar a permissão a uma pasta, navegue até o **menu**  >  **VMs e modelos**  >  **nome da pasta**
1. Clique com o botão direito do mouse no objeto e selecione **adicionar permissão**.
1. Na janela **adicionar permissão** , selecione a origem da identidade no menu suspenso do **usuário** onde o grupo ou o usuário pode ser encontrado.
1. Procure o usuário ou grupo depois de selecionar a origem de identidade na seção **usuário** . 
1. Selecione a função que será aplicada ao usuário ou grupo.
1. Marque a **propagar para filhos** , se necessário, e selecione **OK**.
   A permissão adicionada é exibida na seção **permissões** do objeto.

## <a name="nsx-t-manager-access-and-identity"></a>Acesso e identidade do NSX-T Manager

Use a conta de *administrador* para acessar o Gerenciador de NSX-T. Ele tem privilégios totais e permite criar e gerenciar gateways de camada 1 (T1), segmentos (comutadores lógicos) e todos os serviços. Os privilégios fornecem acesso ao gateway do NSX-T Tier-0 (T0). Uma alteração no gateway de T0 pode resultar em desempenho de rede degradado ou sem acesso à nuvem privada. Abra uma solicitação de suporte no portal do Azure para solicitar qualquer alteração em seu gateway do NSX-T T0.

 
## <a name="next-steps"></a>Próximas etapas

Agora que você abordou os conceitos de acesso e identidade da solução Azure VMware, talvez queira saber mais sobre:

- [Conceitos de atualização da nuvem privada](concepts-upgrades.md)
- [Como habilitar o recurso da solução Azure VMware](enable-azure-vmware-solution.md)
- [Detalhes de cada privilégio](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html)
- [Como a solução Azure VMware monitora e repara nuvens privadas](concepts-monitor-repair-private-cloud.md)
- [Como habilitar o recurso da solução Azure VMware](enable-azure-vmware-solution.md)


<!-- LINKS - external-->
[VMware product documentation]: https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
