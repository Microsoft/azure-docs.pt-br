---
title: Conectar as contas da AWS à Central de Segurança do Azure
description: Como monitorar seus recursos da AWS por meio da Central de Segurança do Azure
author: memildin
ms.author: memildin
ms.date: 01/24/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2ad3ab8c39d9f9a29e74bd139fb5ddd1e5faeff9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755155"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>Conectar as contas da AWS à Central de Segurança do Azure

Com as cargas de trabalho de nuvem normalmente abrangendo plataformas de várias nuvens, os serviços de segurança de nuvem precisam fazer o mesmo.

A Central de Segurança do Azure protege as cargas de trabalho no Azure, na AWS (Amazon Web Services) e no GCP (Google Cloud Platform).

A integração das contas da AWS à Central de Segurança integra o Hub de Segurança da AWS e a Central de Segurança do Azure. Assim, a Central de Segurança fornece visibilidade e proteção em ambos os ambientes de nuvem para fornecer:

- Provisionamento automático de agente (a Central de Segurança usa o [Azure Arc](../azure-arc/servers/overview.md) para implantar o agente do Log Analytics nas instâncias da AWS)
- Gerenciamento de política
- Gerenciamento de vulnerabilidades
- Detecção e Resposta de Ponto de Extremidade Inserido (EDR)
- Detecção de configurações incorretas de segurança
- Uma exibição individual mostrando as recomendações da Central de Segurança e as conclusões do Hub de Segurança da AWS
- Incorporação dos recursos da AWS aos cálculos de classificação de segurança da Central de Segurança
- Avaliações de conformidade regulatória dos recursos da AWS

Na captura de tela abaixo, você pode ver as contas da AWS exibidas no dashboard de visão geral da Central de Segurança.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="Três projetos da GCP listados no painel de visão geral da Central de Segurança" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|Requer [Azure Defender para Servidores](defender-for-servers-introduction.md)|
|Funções e permissões necessárias:|**Proprietário** na assinatura relevante do Azure<br>O **Colaborador** também pode conectar uma conta da AWS se um proprietário fornecer os detalhes da entidade de serviço|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||



## <a name="connect-your-aws-account"></a>Conectar-se à sua conta da AWS

Siga as etapas abaixo para criar o conector de nuvem da AWS. 

### <a name="step-1-set-up-aws-security-hub"></a>Etapa 1. Configurar o Hub de Segurança da AWS:

1. Para exibir as recomendações de segurança para várias regiões, repita as etapas a seguir para cada região relevante.

    > [!IMPORTANT]
    > Se você estiver usando uma conta mestre da AWS, repita as três etapas a seguir para configurar a conta mestre e todas as contas membro conectadas em todas as regiões relevantes

    1. Habilite a [Configuração da AWS](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html).
    1. Habilite o [Hub de Segurança da AWS](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html).
    1. Verifique se há fluxo de dados para o Hub de segurança.

        Quando você habilita o Hub de Segurança pela primeira vez, pode levar várias horas para que os dados sejam disponibilizados.

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>Etapa 2. Configurar a autenticação para a Central de Segurança na AWS

Há duas maneiras de permitir que a Central de Segurança autentique na AWS:

- **Criar uma função IAM para a Central de Segurança** – Esse é o método mais seguro e recomendado
- **Usuário da AWS para a Central de Segurança** – Uma opção menos segura se você não tiver o IAM habilitado

#### <a name="create-an-iam-role-for-security-center"></a>Criar uma função IAM para a Central de Segurança
1. No console do Amazon Web Services, em **Segurança, Identidade e Conformidade**, selecione **IAM**.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="Serviços da AWS":::

1. Selecione **Funções** e **Criar função**.
1. Selecione **Outra conta da AWS**.
1. Insira os seguintes detalhes:

    - **ID da Conta** – Insira a ID da Conta Microsoft (**158177204117**), conforme mostrado na página do conector da AWS na Central de Segurança.
    - **Exigir ID Externa** – Deve estar selecionado
    - **ID Externa** – Insira a ID da assinatura, conforme mostrado na página do conector da AWS na Central de Segurança 

1. Selecione **Avançar**.
1. Na seção **Anexar políticas de permissão**, selecione as seguintes políticas:

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. Opcionalmente, adicione marcações. A adição de marcações ao usuário não afeta a conexão.
1. Selecione **Avançar**.

1. Na lista de funções, escolha a função que você criou

1. Salve o ARN (Amazon Resource Name) para mais tarde. 

#### <a name="create-an-aws-user-for-security-center"></a>Criar um usuário da AWS para a Central de Segurança 
1. Abra a guia **Usuários** e selecione **Adicionar usuário**.
1. Na etapa **Detalhes**, insira um nome de usuário para a Central de Segurança e verifique se selecionou **Acesso programático** no tipo de acesso da AWS. 
1. Selecione **Próximas Permissões**.
1. Selecione **Anexar as políticas existentes diretamente** e aplique as seguintes políticas:
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. Selecione **Avançar: Marcas**. Opcionalmente, adicione marcações. A adição de marcações ao usuário não afeta a conexão.
1. Selecione **Examinar**.
1. Salve para mais tarde a **ID da chave de acesso** gerada automaticamente e o arquivo CSV com a **chave de acesso secreta**.
1. Examine o resumo e clique em **Criar usuário**.


### <a name="step-3-configure-the-ssm-agent"></a>Etapa 3. Configurar o Agente SSM

O Gerenciador de Sistemas da AWS é necessário para automatizar tarefas nos recursos da AWS. Se as instâncias EC2 não tiverem o agente SSM, siga as instruções relevantes da Amazon:

- [Instalando e configurando o agente SSM em instâncias do Windows](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Instalando e configurando o agente SSM em instâncias do Amazon EC2 no Linux](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-complete-azure-arc-prerequisites"></a>Etapa 4. Concluir os pré-requisitos do Azure Arc
1. Verifique se os [provedores de recursos do Azure](../azure-arc/servers/agent-overview.md#register-azure-resource-providers) apropriados estão registrados:
    - Microsoft.HybridCompute
    - Microsoft.GuestConfiguration

1. Crie uma Entidade de Serviço para integração em escala. Como **Proprietário** da assinatura que você deseja usar para a integração, crie uma entidade de serviço para a integração do Azure Arc, conforme descrito em [Criar uma Entidade de Serviço para integração em escala](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale).


### <a name="step-5-connect-aws-to-security-center"></a>Etapa 5. Conectar a AWS à Central de Segurança

1. No menu da Central de Segurança, selecione **Vários conectores de nuvem**.
1. Selecione **Adicionar conta da AWS**.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Botão Adicionar conta da AWS na página de vários conectores de nuvem da Central de Segurança":::
1. Configure as opções na guia **Autenticação da AWS**:
    1. Insira um **Nome de exibição** para o conector.
    1. Confirme se a assinatura está a correta. É a assinatura que incluirá as recomendações do Hub de Segurança da AWS e do conector.
    1. Dependendo da opção de autenticação escolhida na [Etapa 2. Configurar a autenticação da Central de Segurança na AWS](#step-2-set-up-authentication-for-security-center-in-aws):
        - Selecione **Assumir Função** e cole o ARN de [Criar uma função do IAM para a Central de Segurança](#create-an-iam-role-for-security-center).
            :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Como colar o arquivo ARN no campo pertinente do assistente de conexão da AWS no portal do Azure":::

            OU

        - Selecione **Credenciais** e cole a **chave de acesso** e a **chave secreta** do arquivo .csv salvo em [Criar um usuário da AWS para a Central de Segurança](#create-an-aws-user-for-security-center).
1. Selecione **Avançar**.
1. Defina as opções na guia **Configuração do Azure Arc**:

    A Central de Segurança descobre as instâncias EC2 na conta da AWS conectada e usa o SSM para integrá-las ao Azure Arc. 

    > [!TIP]
    > Para obter a lista de sistemas operacionais compatíveis, consulte [Quais sistemas operacionais para minhas instâncias do EC2 são compatíveis?](#what-operating-systems-for-my-ec2-instances-are-supported) nas perguntas frequentes.

    1. Selecione o **Grupo de Recursos** e a **região do Azure** se os EC2s da AWS descobertos serão integrados à assinatura selecionada.
    1. Insira a **ID da Entidade de Serviço** e **o segredo do cliente da Entidade de Serviço** para o Azure Arc, conforme descrito aqui [Criar uma Entidade de Serviço para integração em escala](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. Se o computador estiver se conectando à Internet por meio de um servidor proxy, especifique o endereço IP do servidor proxy ou o nome e o número da porta que o computador usa para se comunicar com o servidor proxy. Insira o valor no formato ```http://<proxyURL>:<proxyport>```
    1. Selecione **Examinar + criar**.

        Examinar as informações de resumo

        As seções de Marcações listarão todas as Marcações do Azure que serão criadas automaticamente para cada EC2 integrada com seus próprios detalhes relevantes a fim de reconhecê-las com facilidade no Azure. 

        Saiba mais sobre as Marcações do Azure em [Usar marcações para organizar os recursos do Azure e a hierarquia de gerenciamento](../azure-resource-manager/management/tag-resources.md).

### <a name="step-6-confirmation"></a>Etapa 6. Confirmação

Quando o conector for criado com sucesso e o Hub de Segurança da AWS tiver sido configurado corretamente:

- A Central de Segurança verifica o ambiente em busca de instâncias EC2 da AWS, integrando-as ao Azure Arc, habilitando a instalação do agente do Log Analytics e fornecendo recomendações de segurança e proteção contra ameaças. 
- O serviço ASC verifica se há novas instâncias EC2 da AWS a cada 6 horas e as integra de acordo com a configuração.
- O padrão CIS da AWS será mostrado no dashboard de conformidade regulatória da Central de Segurança.
- Se a política do Hub de Segurança estiver habilitada, as recomendações serão exibidas no portal da Central de Segurança e no dashboard de conformidade regulatória, de 5 a 10 minutos após a conclusão da integração.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="Recursos e recomendações da AWS na página de recomendações da Central de Segurança":::



## <a name="monitoring-your-aws-resources"></a>Monitorando os recursos da AWS

Conforme mostrado acima, a página de recomendações de segurança da Central de Segurança do Azure exibe os recursos da AWS juntamente com os recursos do Azure e da GCP para uma verdadeira exibição em várias nuvens.

Para exibir todas as recomendações ativas dos seus recursos por tipo de recurso, use a página de estoque de ativos da Central de Segurança e filtre para o tipo de recurso da AWS no qual você está interessado:

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="Filtro do tipo de recurso da página de estoque de ativos mostrando as opções da AWS"::: 


## <a name="aws-in-security-center-faq"></a>Perguntas Frequentes sobre a AWS na Central de Segurança

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>Quais sistemas operacionais para minhas instâncias EC2 são compatíveis?

Sistema operacional compatível para integração automática ao Azure Arc para computadores da AWS

- Ubuntu 16.04 – O agente SSM é pré-instalado por padrão
- Ubuntu 18.04 – O agente SSM é pré-instalado por padrão
- Windows Server – O agente SSM é pré-instalado por padrão
- CentOS Linux 7 – o SSM deve ser instalado manualmente ou integrado separadamente
- SUSE Linux Enterprise Server (SLES) 15 (x64) – o SSM deve ser instalado manualmente ou integrado separadamente
- Red Hat Enterprise Linux (RHEL) 7 (x64) – o SSM deve ser instalado manualmente ou integrado separadamente


## <a name="next-steps"></a>Próximas etapas

Conectar sua conta da AWS faz parte da experiência de várias nuvens disponível na Central de Segurança do Azure. Para obter informações relacionadas, confira a seguinte página:

- [Conectar as contas do GCP à Central de Segurança do Azure](quickstart-onboard-gcp.md)
