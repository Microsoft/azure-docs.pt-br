---
title: Avaliar as VMs do VMware para migração para o Azure com as Migrações para Azure
description: Descreve como avaliar as VMs locais do VMware para migração para o Azure usando as Migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: hamusa
ms.openlocfilehash: 5f70037b1e6ce284b55ff5ff0ae38eb50c320122
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868670"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>Avalie as VMs do VMware com as Migrações para Azure: Avaliação de Servidor

Este artigo mostra como avaliar as VMs locais do VMware usando a ferramenta Migrações para Azure: Avaliação de Servidor.

As [Migrações para Azure](migrate-services-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros.



Este tutorial é o segundo de uma série que demonstra como avaliar e migrar VMs do VMware para o Azure. Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Configurar um projeto das Migrações para Azure.
> * Configurar um dispositivo das Migrações para Azure que é executado localmente para avaliar as VMs.
> * Iniciar a descoberta contínua de VMs locais. O dispositivo envia dados de desempenho e configuração para as VMs descobertas ao Azure.
> * Agrupar as VMs descobertas e avaliar o grupo de VMs.
> * Examinar a avaliação.



> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis. Para obter instruções detalhadas, examine os artigos de instruções.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

- [Conclua](tutorial-prepare-vmware.md) o primeiro tutorial desta série. Caso contrário, as instruções deste tutorial não funcionarão.
- Veja o que você deve ter feito no primeiro tutorial:
    - [Configurar as permissões do Azure](tutorial-prepare-vmware.md#prepare-azure) para as Migrações para Azure.
    - [Preparar o VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) para avaliação. As configurações do VMware devem ser verificadas, e você deve ter permissões para criar uma VM do VMware com um modelo OVA. Você também deve ter uma conta configurada para a descoberta de VM. As portas necessárias devem estar disponíveis, e você deve estar ciente das URLs necessárias para o acesso ao Azure.


## <a name="set-up-an-azure-migrate-project"></a>Configurar um projeto das Migrações para Azure

Configure um novo projeto das Migrações para Azure, conforme descrito a seguir.

1. No portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**.
2. Em **Serviços**, selecione **Migrações para Azure**.
3. Em **Visão Geral**, em **Descobrir, avaliar e migrar servidores**, clique em **Avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/tutorial-assess-vmware/assess-migrate.png)

4. Em **Introdução**, clique em **Adicionar ferramentas**.
5. Em **Migrar projeto**, selecione sua assinatura do Azure e crie um grupo de recursos, caso não tenha um.     
6. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia em que deseja criar o projeto. Há suporte para Ásia, Europa, Reino Unido e Estados Unidos.

    - A geografia do projeto é usada apenas para armazenar os metadados coletados das VMs locais.
    - Você pode selecionar qualquer região de destino ao executar uma migração.

    ![Criar um projeto das Migrações para Azure](./media/tutorial-assess-vmware/migrate-project.png)


7. Clique em **Próximo**.
8. Em **Selecionar ferramenta de avaliação**, selecione **Migrações para Azure: Avaliação de Servidor** > **Avançar**.

    ![Criar um projeto das Migrações para Azure](./media/tutorial-assess-vmware/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por enquanto** > **Avançar**.
10. Em **Examinar + adicionar ferramentas**, examine as configurações e clique em **Adicionar ferramentas**.
11. Aguarde alguns minutos até que o projeto das Migrações para Azure seja implantado. Você será levado para a página do projeto. Caso não veja o projeto, acesse-o em **Servidores** no painel das Migrações para Azure.


## <a name="set-up-the-appliance-vm"></a>Configurar a VM do dispositivo

Migrações para Azure: A Avaliação de Servidor executa um dispositivo leve de VM do VMware.

- Esse dispositivo executa a descoberta de VM e envia os metadados de VM e os dados de desempenho para a Avaliação de Servidor das Migrações para Azure.
- Para configurar o dispositivo:
    - Baixe um arquivo de modelo OVA e importe-o para o vCenter Server.
    - Crie o dispositivo e verifique se ele pode se conectar à Avaliação de Servidor das Migrações para Azure.
    - Configure o dispositivo pela primeira vez e registre-o com o projeto de Migrações para Azure.
- Você pode configurar vários dispositivos para um único projeto das Migrações para Azure. Em todos os dispositivos, há suporte para a descoberta de até 35.000 VMs. Um máximo de 10.000 servidores pode ser descoberto por dispositivo.

### <a name="download-the-ova-template"></a>Baixar o modelo OVA

1. Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique em **Descobrir**.
2. Em **Descobrir computadores** > **Os computadores estão virtualizados?** , clique em **Sim, com o hipervisor do VMware vSphere**.
3. Clique em **Baixar** para baixar o arquivo de modelo .OVA.

    ![Baixe o arquivo .ova](./media/tutorial-assess-vmware/download-ova.png)


### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo OVA é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Para a versão 1.19.06.27, o hash gerado deve corresponder a estes valores. 

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 605d208ac5f4173383f616913441144e
  SHA256 | 447d16bd55f20f945164a1189381ef6e98475b573d6d1c694f3e5c172cfc30d4


### <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie uma VM.

1. No console do cliente do vSphere, clique em **Arquivo** > **Implantar o Modelo de OVF**.

    ![Implantar o OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo OVA.
3. Em **Nome** e **Local**, especifique um nome amigável para a VM. Selecione o objeto de inventário no qual a VM será hospedada.
5. Em **Host/Cluster**, especifique o host ou o cluster no qual a VM será executada.
6. Em **Armazenamento**, especifique o destino de armazenamento para a VM.
7. Em **Formato de Disco**, especifique o tipo e o tamanho do disco.
8. Em **Mapeamento de Rede**, especifique a rede à qual a VM se conectará. A rede precisa ter conectividade com a Internet para enviar metadados para a Avaliação de Servidor das Migrações para Azure.
9. Revise e confirme as configurações e clique em **Concluir**.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às [URLs do Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


### <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo usando as etapas a seguir.

1. No console do cliente do vSphere, clique com botão direito do mouse na VM > **Abrir console**.
2. Forneça o idioma, o fuso horário e a senha do dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo clicando no atalho do aplicativo.
4. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
    - **Licença**: Aceite os termos de licença e leia as informações de terceiros.
    - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
        - Clique em **Configurações de proxy** e especifique o endereço proxy e a porta de escuta, no formato http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais caso o proxy exija autenticação.
        - Há suporte apenas para o proxy HTTP.
    - **Sincronização de horário**: o horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta funcione corretamente.
    - **Instalar as atualizações**: O dispositivo garante que as atualizações mais recentes serão instaladas.
    - **Instalar o VDDK**: O dispositivo verifica se o VDDK (Kit de Desenvolvimento de Disco Virtual) do VMware vSphere está instalado.
        - Migrações para Azure: A Migração de Servidor usa o VDDK para replicar computadores durante a migração para o Azure.
        - Baixe o VDDK 6.7 da VMware e extraia o conteúdo do zip baixado para a localização especificada no dispositivo.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Clique em **Fazer Logon**. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.
2. Na nova guia, entre usando suas credenciais do Azure.
    - Entre com seu nome de usuário e sua senha.
    - Não há suporte para a entrada com um PIN.
3. Depois de entrar com êxito, volte para o aplicativo Web.
2. Selecione a assinatura na qual o projeto das Migrações para Azure foi criado e, em seguida, selecione o projeto.
3. Especifique um nome para o dispositivo. O nome deve ser alfanumérico com 14 caracteres ou menos.
4. Clique em **Registrar**.


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Agora, conecte-se por meio do dispositivo ao vCenter Server e inicie a descoberta de VM.

1. Em **Especificar detalhes do vCenter Server**, especifique o nome (FQDN) ou o endereço IP do vCenter Server. Você pode manter a porta padrão ou especificar uma porta personalizada na qual o vCenter Server escutará.
2. Em **Nome de usuário** e **Senha**, especifique as credenciais de conta somente leitura que o dispositivo usará para descobrir VMs no vCenter Server. Verifique se a conta tem as [permissões necessárias para a descoberta](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). Você pode definir o escopo da descoberta limitando o acesso à conta do vCenter de forma adequada; saiba mais sobre como definir o escopo da descoberta [aqui](tutorial-assess-vmware.md#scoping-discovery).
3. Clique em **Validar conexão** para garantir que o dispositivo possa se conectar ao vCenter Server.
4. Depois que a conexão for estabelecida, clique em **Salvar e iniciar a descoberta**.

Isso iniciará a descoberta. São necessários cerca de 15 minutos para que os metadados das VMs descobertas sejam exibidos no portal.

### <a name="scoping-discovery"></a>Como definir o escopo da descoberta

A descoberta pode ser delimitada por meio da limitação do acesso da conta do vCenter usada para descoberta. Você pode definir o escopo para datacenters, clusters, pasta de clusters, hosts, pasta de hosts ou VMs individuais do vCenter Server. 

> [!NOTE]
> Hoje, a Avaliação de Servidor não poderá descobrir VMs se a conta do vCenter tiver acesso concedido no nível de pasta da VM do vCenter. Se você pretende definir o escopo da descoberta por pastas de VM, faça isso verificando se a conta do vCenter tem acesso somente leitura atribuído em um nível de VM.  Estas são as instruções sobre como você pode fazer isso:
>
> 1. Atribua permissões somente leitura em todas as VMs nas pastas de VM em que você deseja definir o escopo da descoberta. 
> 2. Permita acesso somente leitura a todos os objetos pai nos quais as VMs estão hospedadas. Todos os objetos pai – host, pasta de hosts, cluster, pasta de clusters – na hierarquia até o data center devem ser incluídos. Não é necessário propagar as permissões para todos os objetos filho.
> 3. Use as credenciais para a descoberta selecionando datacenter como *Escopo da Coleção*. A configuração do RBAC garante que o usuário correspondente do vCenter tenha acesso apenas às VMs específicas do locatário.
>
> Observe que há suporte para a pasta de hosts e os clusters.

### <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

Após a descoberta, você poderá verificar se as VMs são exibidas no portal do Azure.

1. Abra o painel das Migrações para Azure.
2. Na página **Migrações para Azure – Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique no ícone que exibe a contagem de **Servidores descobertos**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Há dois tipos de avaliações que podem ser criadas usando as Migrações para Azure: Avaliação de Servidor.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações com base nos dados de desempenho coletados | **Tamanho de VM recomendado**: com base nos dados de utilização da CPU e de memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado Standard ou Premium)** : com base na IOPS e na taxa de transferência dos discos locais.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de VM recomendado**: com base no tamanho da VM local<br/><br> **Tipo de disco recomendado**: com base na configuração de tipo de armazenamento selecionada para a avaliação.


### <a name="run-an-assessment"></a>Ler uma avaliação

Execute uma avaliação da seguinte maneira:

1. Examine as [melhores práticas](best-practices-assessment.md) para a criação de avaliações.
2. Na guia **Servidores**, no bloco **Migrações para Azure: Avaliação de Servidor**, clique em **Avaliar**.

    ![Avaliar](./media/tutorial-assess-vmware/assess.png)

2. Em **Avaliar servidores**, especifique um nome para a avaliação.
3. Clique em **Exibir tudo** para examinar as propriedades da avaliação.

    ![Propriedades de avaliação](./media/tutorial-assess-vmware/view-all.png)

3. Em **Selecionar ou criar um grupo**, selecione **Criar** e especifique um nome de grupo. Um grupo reúne uma ou mais VMs para avaliação.
4. Em **Adicionar computadores ao grupo**, selecione as VMs a serem adicionadas ao grupo.
5. Clique em **Criar Avaliação** para criar o grupo e execute a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-vmware/assessment-create.png)

6. Após a criação da avaliação, veja-a em **Servidores** > **Migrações para Azure: Avaliação de Servidor** > **Avaliações**.
7. Clique em **Exportar avaliação**, para baixá-la como um arquivo do Excel.



## <a name="review-an-assessment"></a>Examinar uma avaliação

Uma avaliação descreve:

- **Preparação para o Azure**: indica se as VMs são adequadas para a migração para o Azure.
- **Estimativa de custo mensal**: os custos mensais estimados de computação e armazenamento para execução das VMs no Azure.
- **Estimativa de custo de armazenamento mensal**: custos estimados para o armazenamento em disco após a migração.

### <a name="view-an-assessment"></a>Exibir uma avaliação

1. Em **Metas de migração** >  **Servidores**, clique em **Avaliações** em **Migrações para Azure: Avaliação de Servidor**.
2. Em **Avaliações**, clique em uma avaliação para abri-la.

    ![Resumo da avaliação](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examinar a Preparação para o Azure

1. Em **Preparação para o Azure**, verifique se as VMs estão prontas para a migração para o Azure.
2. Examine o status da VM:
    - **Pronto para o Azure**: as Migrações para Azure recomendam um tamanho de VM e estimativas de custo para as VMs na avaliação.
    - **Pronto com condições**: mostra os problemas e a correção sugerida.
    - **Não está pronto para o Azure**: mostra os problemas e a correção sugerida.
    - **Preparação desconhecida**: usado quando as Migrações para Azure não podem avaliar a preparação, devido a problemas de disponibilidade de dados.

2. Clique em um status de **Preparação para o Azure**. Você pode exibir os detalhes de preparação da VM e fazer uma busca detalhada para ver os detalhes da VM, incluindo as configurações de computação, armazenamento e rede.



### <a name="review-cost-details"></a>Examinar os detalhes de custo

Essa exibição mostra o custo estimado de computação e armazenamento da execução das VMs no Azure.

1. Examine os custos mensais de computação e armazenamento. Os custos são agregados para todas as VMs no grupo avaliado.

    - As estimativas de custo são baseadas nas recomendações de tamanho para um computador e em seus discos e propriedades.
    - Os custos mensais estimados de computação e armazenamento são mostrados.
    - A estimativa de custo refere-se à execução das VMs locais como VMs de IaaS. A Avaliação de Servidor das Migrações para Azure não considera os custos de PaaS ou SaaS.

2. Você pode examinar as estimativas de custo mensal de armazenamento. Essa exibição mostra os custos agregados de armazenamento para o grupo avaliado, divididos em diferentes tipos de discos de armazenamento.
3. Você pode fazer uma busca detalhada para ver os detalhes de VMs específicas.


### <a name="review-confidence-rating"></a>Revisar classificação de confiança

Quando você executa avaliações com base no desempenho, uma classificação de confiança é atribuída à avaliação.

![Classificação de confiança](./media/tutorial-assess-vmware/confidence-rating.png)

- Uma classificação de 1 estrela (mais baixa) a 5 estrelas (mais alta) é fornecida.
- A classificação de confiança ajuda você a estimar a confiabilidade das recomendações de tamanho fornecidas pela avaliação.
- A classificação de confiança é baseada na disponibilidade dos pontos de dados necessários para calcular a avaliação.

As classificações de confiança para uma avaliação são indicadas a seguir.

**Disponibilidade do ponto de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 estrela
21%-40% | 2 estrelas
41%-60% | 3 estrelas
61%-80% | 4 estrelas
81%-100% | 5 estrelas

[Saiba mais](best-practices-assessment.md#best-practices-for-confidence-ratings) sobre as melhores práticas de classificações de confiança.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você irá:

> [!div class="checklist"]
> * Configurou um dispositivo das Migrações para Azure
> * Criou e examinou uma avaliação

Continue e acesse o terceiro tutorial da série para saber como migrar VMs do VMware para o Azure com a Migração de Servidor das Migrações para Azure.

> [!div class="nextstepaction"]
> [Migrar VMs do VMware](./tutorial-migrate-vmware.md)
