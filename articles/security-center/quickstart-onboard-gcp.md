---
title: Conectar a conta da GCP à Central de Segurança do Azure
description: Monitorando seus recursos da GCP por meio da Central de Segurança do Azure
author: memildin
ms.author: memildin
ms.date: 02/08/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 94c7a800fc551faf6650b8e30fe7c2188f7d2dbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100008376"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>Conectar as contas da GCP à Central de Segurança do Azure

Com as cargas de trabalho de nuvem normalmente abrangendo plataformas de várias nuvens, os serviços de segurança de nuvem precisam fazer o mesmo.

A Central de Segurança do Azure protege as cargas de trabalho no Azure, na AWS (Amazon Web Services) e no GCP (Google Cloud Platform).

A integração das contas da GCP à Central de Segurança integra o Comando de Segurança da GCP e a Central de Segurança do Azure. Assim, a Central de Segurança fornece visibilidade e proteção em ambos os ambientes de nuvem para fornecer:

- Detecção de configurações incorretas de segurança
- Uma exibição individual mostrando as recomendações da Central de Segurança e as conclusões da Central de Comandos de Segurança da GCP
- Incorporação dos recursos da GCP aos cálculos de classificação de segurança da Central de Segurança
- Integração das recomendações da Central de Comandos de Segurança da GCP com base no padrão CIS no dashboard de conformidade regulatória da Central de Segurança

Na captura de tela abaixo, você pode ver os projetos da GCP exibidos no dashboard de visão geral da Central de Segurança.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="3 projetos da GCP listados no dashboard de visão geral da Central de Segurança" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|Requer [Azure Defender para Servidores](defender-for-servers-introduction.md)|
|Funções e permissões necessárias:|**Proprietário** ou **Colaborador** na Assinatura relevante do Azure|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||

## <a name="connect-your-gcp-account"></a>Conectar-se à sua conta da GCP

Crie um conector para cada organização que você deseja monitorar na Central de Segurança.

Ao conectar suas contas do GCP a assinaturas específicas do Azure, considere a [hierarquia de recursos do Google Cloud](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#resource-hierarchy-detail) e estas diretrizes:

- Você pode conectar suas contas do GCP ao ASC no nível da *organização*
- Você pode conectar várias organizações a uma assinatura do Azure
- Você pode conectar várias organizações a várias assinaturas do Azure
- Quando você conecta uma organização, todos os *projetos* dentro dessa organização são adicionados à Central de Segurança

Siga as etapas abaixo para criar o conector de nuvem da GCP. 

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>Etapa 1. Configurar a Central de Comandos de Segurança da GCP com a Análise de Integridade da Segurança

Para todos os projetos da GCP na sua organização, você também deverá:

1. Configurar a **Central de Comandos de Segurança da GCP** usando [estas instruções da documentação da GCP](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup).
1. Habilitar a **Análise de Integridade da Segurança** usando [estas instruções da documentação da GCP](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics).
1. Verifique se há fluxo de dados para a Central de Comandos de Segurança.

As instruções para conectar seu ambiente da GCP para configuração de segurança seguem as recomendações do Google para o consumo de recomendações de configuração de segurança. A integração aproveita a Central de Comandos de Segurança do Google e consumirá recursos adicionais que podem afetar sua cobrança.

Quando você habilita a Análise de Integridade da Segurança pela primeira vez, pode levar várias horas para que os dados sejam disponibilizados.


### <a name="step-2-enable-gcp-security-command-center-api"></a>Etapa 2. Habilitar a API da Central de Comandos de Segurança da GCP

1. Na **Biblioteca da API do Console do Cloud** do Google, selecione os projeto na organização que deseja conectar à Central de Segurança do Azure.
1. Na Biblioteca da API, localize e selecione a **API da Central de Comandos de Segurança**.
1. Na página da API, selecione **HABILITAR**.

Saiba mais sobre a [API da Central de Comandos de Segurança](https://cloud.google.com/security-command-center/docs/reference/rest/).


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>Etapa 3. Criar uma conta de serviço dedicada para a integração da configuração de segurança

1. No **Console do GCP**, selecione um projeto da organização em que você está criando a conta de serviço requerida. 

    > [!NOTE]
    > Quando essa conta de serviço for adicionada no nível da organização, ela será usada para acessar os dados coletados pelo Centro de Comando de Segurança de todos os outros projetos habilitados na organização. 

1. No **Menu de navegação**, nas opções **IAM e administração**, selecione **contas de serviço**.
1. Selecione **CRIAR CONTA DE SERVIÇO**.
1. Insira um nome de conta e selecione **Criar**.
1. Especifique a **Função** como **Visualizador de Administração da Central de Segurança** e selecione **Continuar**.
1. A seção **Conceder aos usuários acesso a essa conta de serviço** é opcional. Selecione **Concluído**.
1. Copie o **valor do Email** da conta de serviço criada e salve-o para uso posterior.
1. No **Menu de navegação**, nas opções **IAM e administração**, selecione **IAM**
    1. Mude para o nível da organização.
    1. Selecione **AICIONAR**.
    1. No campo **Novos membros**, cole o **valor do Email** copiado anteriormente.
    1. Especifique a função como **Visualizador de Administração da Central de Segurança** e selecione **Salvar**.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="Configurando as permissões relevantes da GCP":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>Etapa 4. Criar uma chave privada para a conta de serviço dedicada
1. Mude para o nível de projeto.
1. No **Menu de navegação**, nas opções **IAM e administração**, selecione **contas de serviço**.
1. Abra a conta de serviço dedicada e selecione Editar.
1. Na seção **Chaves**, selecione **ADICIONAR CHAVE** e depois **Criar nova chave**.
1. Na tela Criar chave privada, selecione **JSON** e depois **CRIAR**.
1. Salve este arquivo JSON para uso posterior.


### <a name="step-5-connect-gcp-to-security-center"></a>Etapa 5. Conectar a GCP à Central de Segurança
1. No menu da Central de Segurança, selecione **Conectores de nuvem**.
1. Selecione adicionar conta da GCP.
1. Na página de integração, faça conforme mostrado a seguir e depois selecione **Próximo**.
    1. Valide a assinatura escolhida.
    1. No campo **Nome de exibição**, insira um nome de exibição para o conector.
    1. No campo **ID da organização**, insira a ID da organização. Se você não souber, confira [Criando e gerenciando as organizações](https://cloud.google.com/resource-manager/docs/creating-managing-organization).
    1. Na caixa de arquivo **Chave privada**, navegue até o arquivo JSON baixado na [Etapa 4. Criar uma chave privada para a conta de serviço dedicada](#step-4-create-a-private-key-for-the-dedicated-service-account).


### <a name="step-6-confirmation"></a>Etapa 6. Confirmação

Quando o conector for criado com sucesso e a Central de Comandos de Segurança da GCP tiver sido configurada corretamente:

- O padrão CIS da GCP será mostrado no dashboard de conformidade regulatória da Central de Segurança.
- Recomendações de segurança para os recursos da GCP serão exibidas no portal da Central de Segurança e no dashboard de conformidade regulatória, de 5 a 10 minutos após a conclusão da integração:   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="Recursos e recomendações da GCP na página de recomendações da Central de Segurança":::


## <a name="monitoring-your-gcp-resources"></a>Monitorando os recursos da GCP

Conforme mostrado acima, a página de recomendações de segurança da Central de Segurança do Azure exibe os recursos da GCP juntamente com os recursos do Azure e da AWS para uma verdadeira exibição em várias nuvens.

Para exibir todas as recomendações ativas dos seus recursos por tipo de recurso, use a página de estoque de ativos da Central de Segurança e filtre para o tipo de recurso da GCP no qual você está interessado:

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="Filtro do tipo de recurso da página de estoque de ativos mostrando as opções da GCP"::: 


## <a name="faq-for-connecting-gcp-accounts-to-azure-security-center"></a>Perguntas frequentes sobre como conectar as contas da GCP à Central de Segurança do Azure

### <a name="can-i-connect-multiple-gcp-organizations-to-security-center"></a>Posso conectar várias organizações do GCP à Central de Segurança?
Sim. O conector do GCP da Central de Segurança conecta seus recursos do Google Cloud no nível da *organização*. 

Crie um conector para cada organização do GCP que você deseja monitorar na Central de Segurança. Quando você conecta uma organização, todos os projetos dentro dessa organização são adicionados à Central de Segurança.

Saiba mais sobre a hierarquia de recursos do Google Cloud na [documentação online do Google](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy).


### <a name="is-there-an-api-for-connecting-my-gcp-resources-to-security-center"></a>Há uma API para conectar meus recursos da GCP à Central de Segurança?
Sim. Para criar, editar ou excluir conectores de nuvem da Central de Segurança com uma API REST, confira os detalhes da [API de conectores](/rest/api/securitycenter/connectors).

## <a name="next-steps"></a>Próximas etapas

Conectar sua conta da GCP faz parte da experiência de várias nuvens disponível na Central de Segurança do Azure. Para obter informações relacionadas, confira a seguinte página:

- [Conectar as contas da AWS à Central de Segurança do Azure](quickstart-onboard-aws.md)
- [Hierarquia de recursos do Google Cloud](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy) – Saiba mais sobre a hierarquia de recursos do Google Cloud na documentação online do Google