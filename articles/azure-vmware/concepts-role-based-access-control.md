---
title: Conceitos-controle de acesso baseado em função do vSphere (vSphere RBAC)
description: Saiba mais sobre os principais recursos do vSphere controle de acesso baseado em função para a solução do Azure VMware
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 9d7c724fba194d86c2d73bb8323439de7c83c170
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537011"
---
# <a name="vsphere-role-based-access-control-vsphere-rbac-for-azure-vmware-solution"></a>controle de acesso baseado em função do vSphere (vSphere RBAC) para a solução do Azure VMware

Na solução Azure VMware, o vCenter tem um usuário local interno chamado cloudadmin e atribuído à função CloudAdmin interna. O usuário cloudadmin local é usado para configurar usuários no AD. Em geral, a função CloudAdmin cria e gerencia cargas de trabalho em sua nuvem privada. Na solução do Azure VMware, a função CloudAdmin tem privilégios de vCenter que diferem de outras soluções de nuvem VMware.     

> [!NOTE]
> Atualmente, a solução Azure VMware não oferece funções personalizadas no vCenter ou no portal de solução do Azure VMware. 

Em uma implantação local do vCenter e ESXi, o administrador tem acesso à conta do vCenter administrator@vsphere.local . Eles também podem ter usuários/grupos de Active Directory adicionais (AD) atribuídos. 

Em uma implantação de solução do Azure VMware, o administrador não tem acesso à conta de usuário administrador. Mas eles podem atribuir usuários e grupos do AD à função CloudAdmin no vCenter.  

O usuário da nuvem privada não tem acesso ao e não pode configurar componentes de gerenciamento específicos com suporte e gerenciados pela Microsoft. Por exemplo, clusters, hosts, repositórios de armazenamento e comutadores virtuais distribuídos.

## <a name="azure-vmware-solution-cloudadmin-role-on-vcenter"></a>Função CloudAdmin da solução VMware do Azure no vCenter

Você pode exibir os privilégios concedidos à função CloudAdmin da solução VMware do Azure em sua nuvem privada da solução Azure VMware vCenter.

1. Faça logon no cliente SDDC vSphere e vá para o **menu**  >  **Administração**.
1. Em **controle de acesso**, selecione **funções**.
1. Na lista de funções, selecione **CloudAdmin** e, em seguida, selecione **privilégios**. 

   :::image type="content" source="media/role-based-access-control-cloudadmin-privileges.png" alt-text="Como exibir os privilégios da função CloudAdmin no cliente vSphere":::

A função CloudAdmin na solução VMware do Azure tem os seguintes privilégios no vCenter. Consulte a [documentação do produto VMware](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html) para obter uma explicação detalhada de cada privilégio.

| Privilege | Descrição |
| --------- | ----------- |
| **Alarmes** | Reconhecer alerta<br />Criar alarme<br />Desabilitar ação de alarme<br />Modificar alarme<br />Remover alarme<br />Definir status do alarme |
| **Permissões** | Modificar permissões |
| **Biblioteca de conteúdo** | Adicionar item de biblioteca<br />Criar uma assinatura para uma biblioteca publicada<br />Criar biblioteca local<br />Criar biblioteca inscrita<br />Excluir item de biblioteca<br />Excluir biblioteca local<br />Excluir biblioteca assinada<br />Excluir a assinatura de uma biblioteca publicada<br />Baixar arquivos<br />Remover itens de biblioteca<br />Remover biblioteca assinada<br />Importar armazenamento<br />Informações de assinatura de investigação<br />Publicar um item de biblioteca em seus assinantes<br />Publicar uma biblioteca em seus assinantes<br />Armazenamento de leitura<br />Sincronizar item de biblioteca<br />Sincronizar biblioteca assinada<br />Introspecção de tipo<br />Atualizar definições de configuração<br />Arquivos de atualização<br />Atualizar biblioteca<br />Atualizar item de biblioteca<br />Atualizar biblioteca local<br />Atualizar biblioteca assinada<br />Atualizar a assinatura de uma biblioteca publicada<br />Exibir definições de configuração |
| **Operações criptográficas** | Acesso direto |
| **Datastore** | Alocar espaço<br />Procurar no Repositório de Dados<br />Configurar repositório de armazenamento<br />Operações de arquivo de nível baixo<br />Remover arquivos<br />Atualizar metadados da máquina virtual |
| **Pasta** | Criar pasta<br />Excluir pasta<br />Mover pasta<br />Renomear pasta |
| **Global** | Cancelar tarefa<br />Marca global<br />Saúde<br />Evento de log<br />Gerenciar atributos personalizados<br />Gerenciadores de serviços<br />Definir atributo personalizado<br />Marca do sistema |
| **Host** | Replicação do vSphere<br />&#160;&#160;&#160;&#160;gerenciar a replicação |
| **marcação de vSphere** | Atribuir e cancelar a atribuição de marca vSphere<br />Criar marca vSphere<br />Criar categoria de marca vSphere<br />Excluir marca vSphere<br />Excluir categoria de marca vSphere<br />Editar marca vSphere<br />Editar categoria da marca vSphere<br />Modificar o campo UsedBy para a categoria<br />Modificar campo UsedBy para marca |
| **Rede** | Assign network |
| **Recurso** | Aplicar recomendação<br />Atribuir vApp ao pool de recursos<br />Assign virtual machine to resource pool<br />Criar pool de recursos<br />Migrar máquina virtual desligada<br />Migrar máquina virtual ligada<br />Modificar pool de recursos<br />Mover pool de recursos<br />Consultar o vMotion<br />Remover pool de recursos<br />Renomear pool de recursos |
| **Tarefa agendada** | Criar tarefa<br />Modificar tarefa<br />Remover tarefa<br />Executar tarefa |
| **Sessões** | Mensagem<br />Validar sessão |
| **Perfil** | Exibição de armazenamento controlada por perfil |
| **Exibição de armazenamento** | Visualizar |
| **vApp** | Adicionar máquina virtual<br />Atribuir pool de recursos<br />Atribuir vApp<br />Clone<br />Criar<br />Excluir<br />Exportação<br />Importar<br />Mover<br />Desligar<br />Ligue<br />Renomear<br />Suspend<br />Cancelar o registro <br />Exibir ambiente OVF<br />configuração do aplicativo vApp<br />configuração da instância de vApp<br />configuração do vApp managedBy<br />configuração do recurso vApp |
| **Máquina virtual** | Alterar Configuração<br />&#160;&#160;&#160;&#160;adquirir concessão de disco<br />&#160;&#160;&#160;&#160;adicionar disco existente<br />&#160;&#160;&#160;&#160;adicionar novo disco<br />&#160;&#160;&#160;&#160;adicionar ou remover dispositivo<br />Configuração avançada do &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;alterar contagem de CPU<br />&#160;&#160;&#160;&#160;alterar a memória<br />&#160;&#160;&#160;&#160;alterar configurações<br />&#160;&#160;&#160;&#160;alterar o posicionamento do Swapfile<br />&#160;&#160;&#160;&#160;alterar recurso<br />&#160;&#160;&#160;&#160;configurar o dispositivo USB do host<br />&#160;&#160;&#160;&#160;configurar o dispositivo bruto<br />&#160;&#160;&#160;&#160;configurar o managedBy<br />&#160;&#160;&#160;&#160;exibir configurações de conexão<br />Estender o disco virtual &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;modificar as configurações do dispositivo<br />Compatibilidade de tolerância a falhas de consulta &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;consultar arquivos sem proprietário<br />Recarregar &#160;&#160;&#160;&#160;de caminhos<br />&#160;&#160;&#160;&#160;remover disco<br />Renomear &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;redefinir informações de convidado<br />&#160;&#160;&#160;&#160;definir anotação<br />&#160;&#160;&#160;&#160;alternar o controle de alterações do disco<br />&#160;&#160;&#160;&#160;alternar bifurcação pai<br />&#160;&#160;&#160;&#160;atualizar a compatibilidade da máquina virtual<br />Editar inventário<br />&#160;&#160;&#160;&#160;criar a partir de existente<br />&#160;&#160;&#160;&#160;criar novo<br />&#160;&#160;&#160;&#160;mover<br />Registro de &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;remover<br />&#160;&#160;&#160;&#160;cancelar o registro<br />Operações de convidado<br />Modificação de alias de operação de convidado &#160;&#160;&#160;&#160;<br />Consulta de alias de operação de convidado &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;modificações de operação de convidado<br />&#160;&#160;&#160;&#160;execução do programa de operação de convidado<br />&#160;&#160;&#160;&#160;consultas de operação de convidado<br />Interação<br />&#160;&#160;&#160;&#160;pergunta de resposta<br />&#160;&#160;&#160;&#160;operação de backup na máquina virtual<br />&#160;&#160;&#160;&#160;configurar mídia de CD<br />&#160;&#160;&#160;&#160;configurar mídia de disquete<br />Dispositivos &#160;&#160;&#160;&#160;Connect<br />Interação do console do &#160;&#160;&#160;&#160;<br />Criar captura de tela &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;desfragmentar todos os discos<br />Arrastar e soltar &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;o gerenciamento do sistema operacional convidado pela API do VIX<br />&#160;&#160;&#160;&#160;injetar códigos de verificação de HID USB<br />&#160;&#160;&#160;&#160;instalar as ferramentas do VMware<br />&#160;&#160;&#160;&#160;pausar ou pausar<br />&#160;&#160;&#160;&#160;executar operações de apagamento ou redução<br />&#160;&#160;&#160;&#160;desligar<br />Ligar &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;sessão de registro na máquina virtual<br />&#160;&#160;&#160;&#160;a sessão de reprodução na máquina virtual<br />&#160;&#160;&#160;&#160;suspender<br />Tolerância a falhas de suspensão &#160;&#160;&#160;&#160;<br />Failover de teste do &#160;&#160;&#160;&#160;<br />VM secundária de reinício de teste de &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;desativar a tolerância a falhas<br />&#160;&#160;&#160;&#160;ativar a tolerância a falhas<br />Provisionamento<br />&#160;&#160;&#160;&#160;permitir acesso ao disco<br />&#160;&#160;&#160;&#160;permitir o acesso ao arquivo<br />&#160;&#160;&#160;&#160;permitir acesso a disco somente leitura<br />&#160;&#160;&#160;&#160;permitir download de máquina virtual<br />&#160;&#160;&#160;&#160;o modelo de clonagem<br />Clonar &#160;&#160;&#160;&#160;máquina virtual<br />&#160;&#160;&#160;&#160;criar modelo a partir da máquina virtual<br />&#160;&#160;&#160;&#160;personalizar o convidado<br />&#160;&#160;&#160;&#160;implantar modelo<br />&#160;&#160;&#160;&#160;marcar como modelo<br />&#160;&#160;&#160;&#160;modificar especificação de personalização<br />&#160;&#160;&#160;&#160;promover discos<br />&#160;&#160;&#160;&#160;ler especificações de personalização<br />Configuração de serviço<br />&#160;&#160;&#160;&#160;permitir notificações<br />&#160;&#160;&#160;&#160;permitir sondagem de notificações de eventos globais<br />&#160;&#160;&#160;&#160;gerenciar a configuração de serviço<br />&#160;&#160;&#160;&#160;modificar a configuração de serviço<br />&#160;&#160;&#160;&#160;configurações do serviço de consulta<br />&#160;&#160;&#160;&#160;configuração do serviço de leitura<br />Gerenciamento de instantâneo<br />&#160;&#160;&#160;&#160;criar instantâneo<br />&#160;&#160;&#160;&#160;remover instantâneo<br />Renomear instantâneo &#160;&#160;&#160;&#160;<br />Reverta o instantâneo &#160;&#160;&#160;&#160;<br />Replicação do vSphere<br />&#160;&#160;&#160;&#160;configurar a replicação<br />&#160;&#160;&#160;&#160;gerenciar a replicação<br />&#160;&#160;&#160;&#160;monitorar a replicação |
| **vService** | Criar dependência<br />Destruir dependência<br />Reconfigurar a configuração de dependência<br />Atualizar dependência |



## <a name="next-steps"></a>Próximas etapas

Agora que você já abordou as noções básicas do controle de acesso baseado em função vSphere para a solução do Azure VMware, talvez queira saber mais sobre:

- Os detalhes de cada privilégio na [documentação do produto VMware](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html).
- [Como a solução Azure VMware monitora e repara nuvens privadas](concepts-monitor-repair-private-cloud.md).
- [Como habilitar o recurso de solução VMware do Azure](enable-azure-vmware-solution.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VMware product documentation]: https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html

