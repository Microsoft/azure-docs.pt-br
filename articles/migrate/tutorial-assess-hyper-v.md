---
title: Avaliar VMs do Hyper-V para migração para o Azure usando as Migrações para Azure | Microsoft Docs
description: Descreve como avaliar as VMs locais do Hyper-V para migração para o Azure usando as Migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 83567a45980b29931f9b68bd6d60df0d427b09de
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813014"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Avaliar VMs do Hyper-V usando a avaliação de servidor das Migrações para Azure

Este artigo mostra como avaliar as VMs locais do Hyper-V usando a ferramenta Migrações para Azure: Avaliação de Servidor.

As [Migrações para Azure](migrate-services-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros.



Este tutorial é o segundo de uma série que demonstra como avaliar e migrar VMs do Hyper-V para o Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar um projeto das Migrações para Azure.
> * Configurar e registrar um dispositivo das Migrações para Azure.
> * Iniciar a descoberta contínua de VMs locais.
> * Agrupar as VMs descobertas e avaliar o grupo.
> * Examinar a avaliação.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis. Para obter instruções detalhadas, examine os artigos de instruções.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

- [Conclua](tutorial-prepare-hyper-v.md) o primeiro tutorial desta série. Caso contrário, as instruções deste tutorial não funcionarão.
- Veja o que você deve ter feito no primeiro tutorial:
    - [Configurar as permissões do Azure](tutorial-prepare-hyper-v.md#prepare-azure) para as Migrações para Azure.
    - [Preparar](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-assessment) os clusters, hosts e VMs do Hyper-V para avaliação.

## <a name="set-up-an-azure-migrate-project"></a>Configurar um projeto das Migrações para Azure

1. No portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**.
2. Nos resultados da pesquisa, selecione **Migrações para Azure**.
3. Em **Visão Geral**, em **Descobrir, avaliar e migrar servidores**, clique em **Avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. Em **Introdução**, clique em **Adicionar ferramentas**.
5. Na guia **Migrar projeto**, selecione sua assinatura do Azure e crie um grupo de recursos, caso não tenha um.
6. Em **Detalhes do Projeto**, especifique o nome do projeto e a região em que deseja criar o projeto.


    ![Criar um projeto das Migrações para Azure](./media/tutorial-assess-hyper-v/migrate-project.png)

    Você pode criar um projeto de Migrações para Azure nessas regiões.

    **Geografia** | **Região**
    --- | ---
    Ásia  | Sudeste Asiático
    Europa | Europa Setentrional ou Europa Ocidental
    Reino Unido |  Sul do Reino Unido ou Oeste do Reino Unido
    Estados Unidos | Leste dos EUA, Oeste dos EUA 2 ou Centro-oeste dos EUA

    - A região do projeto é usada apenas para armazenar os metadados coletados das VMs locais.
    - Você pode selecionar uma região de destino do Azure diferente ao migrar as VMs. Todas as regiões do Azure têm suporte como destino de migração.

7. Clique em **Próximo**.
8. Em **Selecionar ferramenta de avaliação**, selecione **Migrações para Azure: Avaliação de Servidor** > **Avançar**.

    ![Criar um projeto das Migrações para Azure](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por enquanto** > **Avançar**.
10. Em **Examinar + adicionar ferramentas**, examine as configurações e clique em **Adicionar ferramentas**.
11. Aguarde alguns minutos até que o projeto das Migrações para Azure seja implantado. Você será levado para a página do projeto. Caso não veja o projeto, acesse-o em **Servidores** no painel das Migrações para Azure.




## <a name="set-up-the-appliance-vm"></a>Configurar a VM do dispositivo

A Avaliação de Servidor das Migrações para Azure executa um dispositivo leve de VM do Hyper-V.

- Esse dispositivo executa a descoberta de VM e envia os metadados de VM e os dados de desempenho para as Migrações para Azure: Avaliação de Servidor.
- Para configurar o dispositivo:
    - Baixe um VHD compactado do Hyper-V no portal do Azure.
    - Crie o dispositivo e verifique se ele pode se conectar à Avaliação de Servidor das Migrações para Azure.
    - Configure o dispositivo pela primeira vez e registre-o com o projeto de Migrações para Azure.

### <a name="download-the-vhd"></a>Baixar o VHD

Baixe o modelo de VHD compactado para o dispositivo.

1. Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique em **Descobrir**.
2. Em **Descobrir computadores** > **Os computadores estão virtualizados?** , clique em **Sim, com o Hyper-V**.
3. Clique em **Download** para baixar o arquivo VHD.

    ![Baixar VM](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Para a versão 1.19.06.27 do dispositivo, o hash gerado deve corresponder a essas configurações.

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 3681f745fa2b0a0a6910707d85161ec5
  SHA256 | e6ca109afab9657bdcfb291c343b3e3abced9a273d25273059171f9954d25832



### <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie a VM.

1. Extraia o arquivo VHD compactado para uma pasta no host Hyper-V que hospedará a VM do dispositivo. Três pastas são extraídas.
2. Abra o Gerenciador do Hyper-V. Em **Ações**, clique em **Importar Máquina Virtual**.

    ![Implantar o VHD](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. No Assistente para Importar Máquina Virtual > **Antes de começar**, clique em **Avançar**.
3. Em **Localizar Pasta**, especifique a pasta que contém o VHD extraído. Em seguida, clique em **Próximo**.
1. Em **Selecionar Máquina Virtual**, clique em **Avançar**.
2. Em **Escolher Tipo de Importação**, clique em **Copiar a máquina virtual (criar uma nova ID exclusiva)** . Em seguida, clique em **Próximo**.
3. Em **Escolher Destino**, mantenha a configuração padrão. Clique em **Próximo**.
4. Em **Pastas de Armazenamento**, mantenha a configuração padrão. Clique em **Próximo**.
5. Em **Escolher Rede**, especifique o comutador virtual que será usado pela VM. O comutador precisa de conectividade com a Internet para enviar dados ao Azure.
6. Em **Resumo**, examine as configurações. Em seguida, clique em **Concluir**.
7. No Gerenciador do Hyper-V, > **Máquinas Virtuais**, inicie a máquina virtual.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às [URLs do Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

### <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

1. No Gerenciador do Hyper-V > **Máquinas Virtuais**, clique com o botão direito do mouse na VM > **Conectar**.
2. Forneça o idioma, o fuso horário e a senha do dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo clicando no atalho do aplicativo.
1. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
    - **Licença**: Aceite os termos de licença e leia as informações de terceiros.
    - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
        - Clique em **Configurações de proxy** e especifique o endereço proxy e a porta de escuta, no formato http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais caso o proxy exija autenticação.
        - Há suporte apenas para o proxy HTTP.
    - **Sincronização do horário**: o horário é verificado. o horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta da VM funcione corretamente.
    - **Instalar as atualizações**: a Avaliação de Servidor das Migrações para Azure verifica se o dispositivo tem as atualizações mais recentes instaladas.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Clique em **Fazer Logon**. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.
2. Na nova guia, entre usando suas credenciais do Azure.
    - Entre com seu nome de usuário e sua senha.
    - Não há suporte para a entrada com um PIN.
3. Depois de entrar com êxito, volte para o aplicativo Web.
4. Selecione a assinatura na qual o projeto das Migrações para Azure foi criado. Em seguida, selecione o projeto.
5. Especifique um nome para o dispositivo. O nome deve ser alfanumérico com 14 caracteres ou menos.
6. Clique em **Registrar**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegar credenciais para VHDs de SMB

Se estiver executando VHDs em SMBs, você precisará habilitar a delegação de credenciais do dispositivo para os hosts do Hyper-V. Se não tiver feito isso em cada host do [tutorial](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts) anterior, faça agora no dispositivo:

1. Na VM do dispositivo, execute este comando. HyperVHost1/HyperVHost2 são nomes de host de exemplo.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Como alternativa, faça isso no Editor de Política de Grupo Local no dispositivo:
    - Em **Política do Computador local** > **Configuração do Computador**, clique em **Modelos Administrativos** > **Sistemas** > **Delegação de Credenciais**.
    - Clique duas vezes em **Permitir delegação de novas credenciais** e selecione **Habilitado**.
    - Em **Opções**, clique em **Mostrar** e adicione cada host Hyper-V que você deseja descobrir à lista, com o prefixo **wsman/** .
    - Em **Delegação de Credenciais**, clique duas vezes em **Permitir delegação de novas credenciais com autenticação de servidor somente NTLM**. Mais uma vez, adicione cada host Hyper-V que você deseja descobrir à lista, com o prefixo **wsman/** .

## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Conecte-se do dispositivo a clusters ou hosts do Hyper-V e inicie a descoberta de VM.

1. Em **Nome de usuário** e **Senha**, especifique as credenciais de conta que o dispositivo usará para descobrir VMs. Especifique um nome amigável para as credenciais e clique em **Salvar detalhes**.
2. Clique em **Adicionar host** e especifique os detalhes do host/cluster do Hyper-V.
3. Clique em **Validar**. Após a validação, o número de VMs que podem ser descobertas em cada host/cluster é mostrado.
    - Se a validação falhar para um host, examine o erro passando o ponteiro do mouse sobre o ícone na coluna **Status**. Corrija os problemas e valide novamente.
    - Para remover hosts ou clusters, selecione > **Excluir**.
    - Não é possível remover um host específico de um cluster. Você só pode remover o cluster inteiro.
    - Você pode adicionar um cluster, mesmo que haja problemas com hosts específicos nele.
4. Após a validação, clique em **Salvar e iniciar descoberta** para iniciar o processo de descoberta.

Isso iniciará a descoberta. São necessários cerca de 15 minutos para que os metadados das VMs descobertas sejam exibidos no portal do Azure.

### <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

Após a descoberta terminar, você poderá verificar se as VMs são exibidas no portal.

1. Abra o painel das Migrações para Azure.
2. Na página **Migrações para Azure – Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique no ícone que exibe a contagem de **Servidores descobertos**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Há dois tipos de avaliações que podem ser executadas usando a Avaliação de Servidor das Migrações para Azure.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações com base nos dados de desempenho coletados | **Tamanho de VM recomendado**: com base nos dados de utilização da CPU e de memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado Standard ou Premium)** : com base na IOPS e na taxa de transferência dos discos locais.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de VM recomendado**: com base no tamanho da VM local<br/><br> **Tipo de disco recomendado**: com base na configuração de tipo de armazenamento selecionada para a avaliação.



### <a name="run-an-assessment"></a>Ler uma avaliação

Execute uma avaliação da seguinte maneira:

1. Examine as [melhores práticas](best-practices-assessment.md) para a criação de avaliações.
2. Em **Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique em **Avaliar**.

    ![Avaliar](./media/tutorial-assess-hyper-v/assess.png)

3. Em **Avaliar Servidores**, especifique um nome para a avaliação.
4. Clique em **Exibir tudo** para examinar as propriedades da avaliação.

    ![Propriedades de avaliação](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. Em **Selecionar ou criar um grupo**, selecione **Criar** e especifique um nome de grupo. Um grupo reúne uma ou mais VMs para avaliação.
4. Em **Adicionar computadores ao grupo**, selecione as VMs a serem adicionadas ao grupo.
5. Clique em **Criar Avaliação** para criar o grupo e execute a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Após a criação da avaliação, veja-a em **Servidores** > **Migrações para Azure: Avaliação de Servidor**.
7. Clique em **Exportar avaliação**, para baixá-la como um arquivo do Excel.


## <a name="review-an-assessment"></a>Examinar uma avaliação

Uma avaliação descreve:

- **Preparação para o Azure**: indica se as VMs são adequadas para a migração para o Azure.
- **Estimativa de custo mensal**: os custos mensais estimados de computação e armazenamento para execução das VMs no Azure.
- **Estimativa de custo de armazenamento mensal**: custos estimados para o armazenamento em disco após a migração.


### <a name="view-an-assessment"></a>Exibir uma avaliação

1. Em **Metas de migração** >  **Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique em **Avaliações**.
2. Em **Avaliações**, clique em uma avaliação para abri-la.

    ![Resumo da avaliação](./media/tutorial-assess-hyper-v/assessment-summary.png)


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

![Classificação de confiança](./media/tutorial-assess-hyper-v/confidence-rating.png)

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

Continue e acesse o terceiro tutorial da série para saber como migrar VMs do Hyper-V para o Azure com a Migração de Servidor das Migrações para Azure.

> [!div class="nextstepaction"]
> [Migrar VMs do Hyper-V](./tutorial-migrate-hyper-v.md)
