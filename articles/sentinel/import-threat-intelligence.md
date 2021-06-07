---
title: Importar inteligência contra ameaças para o Azure Sentinel | Microsoft Docs
description: Use feeds de inteligência contra ameaças no Azure Sentinel para analisar dados, detectar ameaças e gerar alertas e incidentes. Visualize informações de inteligência contra ameaças com pastas de trabalho.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: 0b2a6088effc735076d56ba83dd85135392147b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574991"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>Importar inteligência contra ameaças para o Azure Sentinel

## <a name="introduction-to-threat-intelligence"></a>Introdução à inteligência contra ameaças

A CTI (inteligência contra ameaças virtuais) é uma informação que descreve ameaças existentes ou potenciais conhecidas para sistemas e usuários. Esse tipo de informação tem muitas formas, desde relatórios escritos detalhando as motivações, a infraestrutura e as técnicas de um determinado ator de ameaças, até observações específicas de endereços IP, domínios e hashes de arquivo associados a ameaças cibernéticos. A CTI é usada pelas organizações para fornecer um contexto essencial a atividades incomuns, para que a equipe de segurança possa tomar medidas rapidamente para proteger suas pessoas e ativos. A CTI pode ser originada de vários lugares, como feeds de dados de software livre, comunidades de compartilhamento de inteligência contra ameaças, feeds de inteligência comercial e inteligência local coletados no decorrer de investigações de segurança em uma organização.

Em uma solução de SIEM (gerenciamento de informações e eventos de segurança) como o Azure Sentinel, a forma mais utilizada da CTI são indicadores de ameaça, muitas vezes conhecidos como indicadores de comprometimento ou IoCs. Os indicadores de ameaça são dados que associam observações como URLs, hashes de arquivo ou endereços IP com atividade de ameaça conhecida, como phishing, botnets ou malware. Essa forma de inteligência contra ameaças é geralmente chamada de inteligência de ameaças tática porque pode ser aplicada a produtos de segurança e automação em grande escala para proteger e detectar possíveis ameaças a uma organização. No Azure Sentinel, você pode usar indicadores de ameaça para ajudar a detectar atividades mal-intencionadas observadas em seu ambiente e fornecer contexto a investigadores de segurança para ajudar a informar decisões de resposta.

Você pode integrar a TI (inteligência contra ameaças) ao Azure Sentinel por meio das seguintes atividades:

- Use **conectores de dados** para várias plataformas de ti para [importar inteligência contra ameaças](./connect-threat-intelligence.md) para o Azure Sentinel.
- Exiba e gerencie a inteligência de ameaças importada em **logs** e na nova área de **inteligência contra ameaças** do Azure Sentinel.
- Use os modelos de regra de **análise** interna para gerar alertas de segurança e incidentes usando a inteligência de ameaças importada.
- Visualize as principais informações sobre sua inteligência contra ameaças no Azure Sentinel com a **pasta de trabalho de inteligência contra ameaças**.

A inteligência contra ameaças também fornece um contexto útil em outras experiências do Azure Sentinel, como **buscas** e **notebooks**, e, embora não seja abordada neste artigo, essas experiências são abordadas nesta [excelente postagem no blog, de Ian Hellen nos notebooks Jupyter no Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239), que abrange o uso de CTI dentro de notebooks.

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>Conectores de dados do Azure Sentinel para inteligência contra ameaças

Assim como todos os outros dados de evento no Azure Sentinel, os indicadores de ameaça são importados usando os conectores de dados. Há dois conectores de dados no Azure Sentinel fornecidos especificamente para indicadores de ameaça, **inteligência contra ameaças** e **plataformas de inteligência contra ameaças**. Você pode usar o conector de dados sozinho ou ambos os conectores, dependendo de onde sua organização cria os indicadores de ameaça. Vamos falar sobre cada um dos conectores de dados.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>Adicionando indicadores de ameaça ao Azure Sentinel com o conector de dados das plataformas de inteligência contra ameaças

Muitas organizações utilizam soluções de gorjeta (plataforma de inteligência contra ameaças) para agregar feeds de indicadores de ameaça de uma variedade de fontes, para organizar os dados na plataforma e escolher quais indicadores de ameaça aplicar a várias soluções de segurança, como dispositivos de rede, soluções avançadas de proteção contra ameaças ou SIEMs como o Azure Sentinel. Se sua organização utiliza uma solução de TIP integrada, como MISP, Anomaliai ThreatStream, ThreatConnect, plataforma EclecticIQ, plataforma de inteligência contra ameaças ThreatQ ou Palo Alto Networks, o **conector de dados das plataformas de inteligência contra ameaças** permite que você use sua dica para importar indicadores de ameaça para o Azure Sentinel. Como o conector funciona com a [API do Microsoft Graph Security tiIndicators](/graph/api/resources/tiindicator) para fazer isso, o conector também pode ser usado por qualquer plataforma de inteligência contra ameaças personalizada para aproveitar a API do tiIndicators para enviar indicadores para o Azure Sentinel (e outras soluções de segurança da Microsoft, como o defender ATP).

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="Caminho de importação de inteligência contra ameaças":::

Siga estas etapas para importar indicadores de ameaça para o Azure Sentinel de sua dica integrada ou solução personalizada de inteligência contra ameaças:

1. Obter uma ID do aplicativo e um segredo do cliente do seu Azure Active Directory

1. Inserir essas informações em sua solução TIP ou aplicativo personalizado

1. Habilitar o conector de dados das plataformas de inteligência contra ameaças no Azure Sentinel

Veja uma visão detalhada de cada uma dessas etapas.

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Obter uma ID do aplicativo e um segredo do cliente do seu Azure Active Directory

Se você estiver trabalhando com uma dica ou com uma solução personalizada, a API do tiIndicators exigirá algumas informações básicas para se conectar e enviar indicadores de ameaça. As três informações que você precisará obter são:
- ID do aplicativo (cliente)
- ID do diretório (locatário)
- Segredo do cliente

Essas informações sempre vêm do Azure Active Directory por meio de um processo chamado **registro de aplicativo** , que inclui as três etapas a seguir:
- registrar um aplicativo no Azure Active Directory
- Especifique as permissões exigidas pelo aplicativo para se conectar à API do Microsoft Graph tiIndicators e enviar indicadores de ameaça
- Obtenha consentimento da sua organização para conceder essas permissões a este aplicativo.  

**Registrar um aplicativo com o Azure Active Directory**

1. Abra o [portal do Azure](https://portal.azure.com/) e navegue até o serviço **Azure Active Directory** .

1. Selecione **registros do aplicativo** no menu e selecione **novo registro**.

1. Escolha um nome para o registro do aplicativo, selecione o botão de opção de **locatário único** e selecione **registrar**.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="Registrar um aplicativo":::

1. Na tela resultante, copie os valores de ID do **aplicativo (cliente)** e de **ID do diretório (locatário)** . Essas são as duas primeiras informações que você precisará mais tarde para configurar sua dica ou solução personalizada para enviar indicadores de ameaça para o Azure Sentinel.

**Especificar as permissões exigidas pelo aplicativo**

1. Abra o [portal do Azure](https://portal.azure.com/) e navegue até o serviço **Azure Active Directory** .

1. Selecione **registros de aplicativo** no menu e selecione seu aplicativo recentemente registrado.

1. Selecione **permissões de API** no menu e clique no botão **Adicionar uma permissão** .

1. Na página **selecionar uma API** , selecione **Microsoft Graph** para escolher em uma lista de permissões de Microsoft Graph.

1. Quando perguntado **que tipo de permissões seu aplicativo exige?** selecione **permissões de aplicativo**. Esse é o tipo de permissões usadas por aplicativos que se autenticam com a ID do aplicativo e segredos do aplicativo (chaves de API).

1. Selecione **ThreatIndicators. ReadWrite. OwnedBy** e selecione **adicionar permissões** para adicionar essa permissão à lista de permissões do seu aplicativo.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="Especificar permissões":::

**Obter consentimento da sua organização para conceder essas permissões**

1. Para conceder consentimento, você precisa de um administrador global Azure Active Directory para selecionar o botão **conceder consentimento de administrador para seu locatário** na página permissões de API do aplicativo. Se você não tiver a função de administrador global em sua conta, esse botão não estará disponível e você precisará solicitar a um administrador global da sua organização para executar essa etapa.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="Conceder consentimento":::

1. Depois que o consentimento tiver sido concedido ao seu aplicativo, você deverá ver uma marca de seleção verde em **status**.
 
Agora que seu aplicativo foi registrado e as permissões foram concedidas, você pode obter a última coisa na lista – um segredo do cliente para seu aplicativo.

1. Abra o [portal do Azure](https://portal.azure.com/) e navegue até o serviço **Azure Active Directory** .

1. Selecione **registros de aplicativo** no menu e selecione seu aplicativo recentemente registrado.

1. Selecione **certificados & segredos** no menu e clique no botão **novo segredo do cliente** para obter um segredo (chave de API) para seu aplicativo.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="Obter segredo do cliente":::

1. Clique no botão **Adicionar** e **certifique-se de copiar o segredo do cliente**, pois você não poderá recuperar esse segredo novamente se sair dessa página. Você precisará desse valor ao configurar sua dica ou solução personalizada.

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>Inserir essas informações em sua solução TIP ou aplicativo personalizado

Agora você tem todas as três informações necessárias para configurar sua dica ou solução personalizada para enviar indicadores de ameaça para o Azure Sentinel. 
- ID do aplicativo (cliente)
- ID do diretório (locatário)
- Segredo do cliente

Insira esses valores na configuração da sua dica integrada ou solução personalizada, quando necessário, e os indicadores de ameaça serão enviados por meio da API Microsoft Graph tiIndicators direcionada ao Azure Sentinel.

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>Habilitar o conector de dados das plataformas de inteligência contra ameaças no Azure Sentinel

A última etapa do processo de integração é habilitar o conector de dados das **plataformas de inteligência contra ameaças** no Azure Sentinel. Esta é a etapa que importa os indicadores de ameaça enviados de sua dica ou solução personalizada por meio da API Microsoft Graph tiIndicators para o Azure sentinela. Esses indicadores estarão disponíveis para todos os espaços de trabalho do Azure Sentinel para sua organização. Siga estas etapas para habilitar o conector de dados das plataformas de inteligência contra ameaças para cada espaço de trabalho:

1. Abra o [portal do Azure](https://portal.azure.com/) e navegue até o serviço **Sentinela do Azure** .

1. Escolha o **espaço de trabalho** para o qual você deseja importar os indicadores de ameaça enviados da sua dica ou solução personalizada.

1. Selecione **conectores de dados** no menu, selecione **plataformas de inteligência contra ameaças** na Galeria de conectores e clique no botão **abrir página do conector** .

1. Como você já concluiu o registro do aplicativo e configurou sua dica ou solução personalizada para enviar indicadores de ameaça, a única etapa à esquerda é clicar no botão **conectar** .

Em alguns minutos, os indicadores de ameaça devem começar a fluir para este espaço de trabalho do Azure Sentinel.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>Adicionando indicadores de ameaça ao Azure Sentinel com o conector de dados de inteligência contra ameaças-TÁXIi

O padrão do setor mais amplamente adotado para a transmissão de inteligência contra ameaças é uma [combinação do formato de dados Stix e do protocolo táxii](https://oasis-open.github.io/cti-documentation/). Se sua organização obtiver indicadores de ameaça de soluções que dão suporte à versão atual do STIX/táxi (2,0 ou 2,1), você poderá usar o conector de dados de **inteligência contra ameaças-táxii** para trazer seus indicadores de ameaça para o Azure Sentinel. O conector de dados de inteligência contra ameaças – TÁXIi permite que um cliente de TÁXIs interno no Azure Sentinel importe a inteligência contra ameaças de servidores TÁXIi 2. x.

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="Caminho de importação de TÁXIi":::
 
Siga estas etapas para importar os indicadores de ameaça formatados STIX para o Azure Sentinel de um servidor táxi:

1. Obter a raiz da API do servidor TÁXIi e a ID da coleção

1. Habilitar o conector de dados de inteligência contra ameaças-TÁXIi no Azure Sentinel

Agora, vamos dar uma olhada detalhada em cada uma dessas etapas.

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>Obter a raiz da API do servidor TÁXIi e a ID da coleção

Os servidores TÁXIi 2. x anunciam raízes de API, que são URLs que hospedam coleções de inteligência contra ameaças. Geralmente, a raiz da API pode ser obtida por meio da página de documentação do provedor de inteligência contra ameaças que hospeda o servidor de TÁXIi. No entanto, às vezes, a única informação anunciada é uma URL conhecida como um ponto de extremidade de descoberta. Se esse for o caso, é fácil encontrar a raiz da API usando o ponto de extremidade de descoberta. Se você já conhece a raiz da API do servidor de táxi e as IDs de coleção com as quais deseja trabalhar, fique à vontade para ir para a próxima seção, **habilitar o conector de dados de inteligência contra ameaças no Azure Sentinel**.

Vejamos um exemplo real de como usar um utilitário de linha de comando simples chamado [enrolamento](https://en.wikipedia.org/wiki/CURL), que é fornecido no Windows e na maioria das distribuições do Linux, para descobrir a raiz da API e procurar as coleções de um servidor de táxii somente a partir do ponto de extremidade de descoberta. Para este exemplo, usaremos o ponto de extremidade de descoberta do servidor de [limo](https://www.anomali.com/community/limo) THREATSTREAM de táxii 2,0.

1.  Em um navegador, navegue até o [ponto de extremidade de descoberta do servidor THREATSTREAM táxii 2,0](https://limo.anomali.com/taxii) para recuperar a raiz da API. Autentique com o usuário e a senha `guest` .

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  Use o utilitário de rotação e a raiz da API ( https://limo.anomali.com/api/v1/taxii2/feeds/) da etapa anterior para procurar a lista de IDs de coleção hospedada na raiz da API

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

Agora você tem todas as informações de que precisa para conectar o Azure Sentinel a uma ou mais coleções de servidor de TÁXIs fornecidas pelo limo de Anomaliai.

| **Raiz da API** (https://limo.anomali.com/api/v1/taxii2/feeds/) | ID da coleção |
| ------------------------------------------------------------ | ------------: |
| **Tanque de Phish**                                               | 107           |
| **IPs de ransomware Abuse.ch**                                  | 135           |
| **Domínios de ransomware Abuse.ch**                              | 136           |
| **IPs de verificação de DShield**                                     | 150           |
| **Lista de domínios de malware-Hotlist**                            | 200           |
| **Nós Blutmagie TOR**                                      | 209           |
| **Ameaças emergentes C&C Server**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **Crime cibernético**                                               |  41           |
| **Ameaças emergentes-comprometidas**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Habilitar o conector de dados de inteligência contra ameaças-TÁXIi no Azure Sentinel

Para importar indicadores de ameaça para o Azure Sentinel de um servidor de táxi, siga estas etapas:

1. Abra o [portal do Azure](https://portal.azure.com/) e navegue até o serviço **Sentinela do Azure** .

1. Escolha o **espaço de trabalho** para o qual você deseja importar indicadores de ameaça do servidor táxi.

1. Selecione **conectores de dados** no menu, selecione **inteligência contra ameaças-táxii** na Galeria de conectores e clique no botão **abrir página do conector** .

1. Digite um **nome** para essa coleção de servidores de táxii, **URL raiz da API**, **ID da coleção**, **nome de usuário** (se necessário) e **senha** (se necessário) e clique no botão **Adicionar** .

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="Configurar servidores de TÁXIi":::
 
Você deve receber a confirmação de que uma conexão com o servidor de TÁXIs foi estabelecida com êxito e pode repetir a etapa (4) acima quantas vezes desejar para se conectar a várias coleções do mesmo servidor ou de servidores de TÁXIs diferentes.

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Exibir seus indicadores de ameaça no Azure Sentinel

Agora que você importou com êxito os indicadores de ameaça para o Azure Sentinel usando as **plataformas de inteligência contra ameaças** e/ou o conector de dados de **inteligência contra ameaças-táxii** , você pode exibi-los na tabela **ThreatIntelligenceIndicator** em **logs** , onde todos os seus dados de evento do Azure Sentinel são armazenados. Esta tabela é a base para consultas executadas por outros recursos do Azure Sentinel, como análises e pastas de trabalho. Veja como localizar e exibir seus indicadores de ameaça na tabela **ThreatIntelligenceIndicator** .

1. Abra o [portal do Azure](https://portal.azure.com/) e navegue até o serviço **Sentinela do Azure** .

1. Escolha o **espaço de trabalho** para o qual você importou indicadores de ameaça usando o conector de dados de inteligência contra ameaças.

1. Selecione **logs** na seção **geral** do menu do Azure Sentinel.

1. A tabela **ThreatIntelligenceIndicator** está localizada no grupo **Sentinela do Azure** .

1. Selecione o ícone **Visualizar dados** (o olho) ao lado do nome da tabela e selecione o botão **ver no editor de consultas** para executar uma consulta que mostrará os registros desta tabela.

Os resultados devem ser semelhantes ao indicador de ameaça de exemplo mostrado abaixo:

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="Dados de consulta de exemplo":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>Gerencie seus indicadores de ameaça na nova área de inteligência contra ameaças do Azure Sentinel

Com a nova área de **inteligência contra ameaças** , acessível no menu do Azure Sentinel, você também pode exibir, classificar, filtrar e pesquisar seus indicadores de ameaça importados sem sequer escrever uma consulta de **logs** . Essa nova área também permite que você crie indicadores de ameaça diretamente na interface do Azure Sentinel, bem como Execute tarefas administrativas comuns de inteligência contra ameaças, como marcação de indicador e criação de novos indicadores relacionados a investigações de segurança.
Vamos examinar duas das tarefas mais comuns, criar novos indicadores de ameaça e indicadores de marcação para facilitar o agrupamento e a referência.

1. Abra o [portal do Azure](https://portal.azure.com/) e navegue até o serviço **Sentinela do Azure** .

1. Escolha o **espaço de trabalho** para o qual você importou indicadores de ameaça usando o conector de dados de inteligência contra ameaças.

1. Selecione **inteligência contra ameaças** na seção Gerenciamento de ameaças do menu do Azure Sentinel.

1. Selecione o botão **Adicionar novo** no menu superior da página.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="Adicionar um novo indicador de ameaça" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. Escolha o tipo de indicador e preencha os campos obrigatórios marcados com um asterisco vermelho (*) no **novo painel indicador** .

1. Escolha **Aplicar**. O indicador é adicionado à grade de indicadores e também é enviado para a tabela ThreatIntelligenceIndicator em **logs**.

A marcação de indicadores de ameaça é uma maneira fácil de agrupá-los para facilitar sua localização. Normalmente, você pode aplicar uma marca a indicadores relacionados a um incidente específico ou a indicadores que representam ameaças de um ator conhecido específico ou uma campanha de ataque bem conhecida. Você pode marcar os indicadores de ameaça individualmente ou indicadores de seleção múltipla e marcá-los todos de uma vez. Mostrado abaixo está um exemplo de marcação de vários indicadores com uma ID de incidente. Como a marcação é de forma livre, uma prática recomendada é criar convenções de nomenclatura padrão para as marcas de indicador de ameaça. Você pode aplicar várias marcas a cada indicador.

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="Aplicar marcas a indicadores de ameaça" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>A análise coloca seus indicadores de ameaça para trabalhar na detecção de possíveis ameaças

Você tem seus indicadores de ameaça inseridos no Azure Sentinel; Você viu como exibi-los e gerenciá-los; Agora, veja o que eles podem fazer por você. O caso de uso mais importante para indicadores de ameaça em soluções SIEM como o Azure Sentinel é a regras do Power Analytics.  Essas regras baseadas em indicador comparam eventos brutos de suas fontes de dados em relação aos indicadores de ameaça para detectar ameaças à segurança em sua organização. No Azure Sentinel **Analytics**, você cria regras de análise que são executadas de forma programada e gera alertas de segurança. As regras são controladas por consultas, juntamente com as configurações que determinam a frequência com que a regra deve ser executada, que tipo de resultados de consulta devem gerar alertas de segurança e quaisquer respostas automatizadas para disparar quando os alertas são gerados.

Embora você sempre possa criar novas regras de análise do zero, o Azure Sentinel fornece um conjunto de modelos de regras internos, criados por engenheiros de segurança da Microsoft, que você pode usar no estado em que se encontram ou modificar para atender às suas necessidades. Você pode identificar prontamente os modelos de regra que usam indicadores de ameaça, já que eles são todos intitulados começando com "**mapa de ti**...". Todos esses modelos de regra operam da mesma forma, com a única diferença de qual tipo de indicadores de ameaça são usados (domínio, email, hash de arquivo, endereço IP ou URL) e a qual tipo de evento corresponder. Cada modelo lista as fontes de dados necessárias para que a regra funcione, para que você possa ver rapidamente se os eventos necessários já foram importados no Azure Sentinel.

Vamos dar uma olhada em um desses modelos de regra e explicar como habilitar e configurar a regra para gerar alertas de segurança usando os indicadores de ameaça que você importou para o Azure Sentinel. Neste exemplo, usaremos o modelo de regra chamado **entidade de IP do mapa de ti como AzureActivity**. Essa regra corresponderá a qualquer indicador de ameaça de tipo de endereço IP com todos os seus eventos de atividade do Azure. Quando uma correspondência for encontrada, um **alerta** será gerado, bem como um **incidente** correspondente para investigação por sua equipe de operações de segurança. Esta regra de análise funcionará com êxito somente se você tiver habilitado um ou ambos os conectores de dados de **inteligência contra ameaças** (para importar indicadores de ameaça) e o conector de dados de **atividade do Azure** (para importar os eventos de nível de assinatura do Azure).

1. Abra o [portal do Azure](https://portal.azure.com/) e navegue até o serviço **Sentinela do Azure** .

1. Escolha o **espaço de trabalho** para o qual você importou indicadores de ameaça usando os conectores de dados de **inteligência contra ameaças** e dados de atividade do Azure usando o conector de dados de **atividade**

1. Selecione **análise** na seção **configuração** do menu Azure Sentinel.

1. Selecione a guia **modelos de regra** para ver a lista de modelos de regra de análise disponíveis.

1. Navegue até a regra denominada **entidade de IP do mapa de ti para AzureActivity** e verifique se você conectou todas as fontes de dados necessárias, conforme mostrado abaixo.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="Fontes de dados necessárias":::

1. Selecione essa regra e selecione o botão **criar regra** . Isso abre um assistente para configurar a regra. Conclua as configurações aqui e selecione o botão **Avançar: definir a lógica de regra >** .

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="Criar regra de análise":::

1. A parte lógica da regra do assistente contém:
    - A consulta que será usada na regra.

    - Mapeamentos de entidade, que dizem ao Azure Sentinel como reconhecer entidades como contas, endereços IP e URLs, para que **incidentes** e **investigações** entendam como trabalhar com os dados em qualquer alerta de segurança gerado por essa regra.

    - O agendamento para executar esta regra.

    - O número de resultados da consulta necessários antes que um alerta de segurança seja gerado.

    As configurações padrão no modelo são:
    - Executar uma vez por hora.

    - Corresponda qualquer indicador de ameaça de endereço IP da tabela **ThreatIntelligenceIndicator** com qualquer endereço IP encontrado na última hora de eventos da tabela **AzureActivity** .

    - Gerar um alerta de segurança se os resultados da consulta forem maiores que zero, o que significa se alguma correspondência for encontrada.

Você pode deixar as configurações padrão ou alterar qualquer uma delas para atender às suas necessidades. Quando tiver terminado, selecione o botão **Avançar: resposta automatizada >**

1. Esta etapa do assistente permite que você configure qualquer automação que você gostaria de disparar quando um alerta de segurança é gerado por meio desta regra de análise. A automação no Azure Sentinel é feita usando **Guias estratégicos**, da plataforma de aplicativos lógicos do Azure. Para saber mais, confira este [tutorial: configurar respostas de ameaças automatizadas no Azure Sentinel](./tutorial-respond-threats-playbook.md). Para este exemplo, selecionaremos o botão **Avançar: Revisar >** para continuar.

1. Essa última etapa valida as configurações em sua regra. Quando você estiver pronto para habilitar a regra, selecione o botão **criar** e você terminou.

Agora que você habilitou sua regra de análise, poderá encontrar sua regra habilitada na guia **regras ativas** da seção **análise** do Azure Sentinel. Você pode editar, habilitar, desabilitar, duplicar ou excluir a regra ativa a partir daí. A nova regra é executada imediatamente após a ativação e, a partir de então, em será executada em seu agendamento definido.

De acordo com as configurações padrão, cada vez que a regra é executada em seu agendamento, todos os resultados encontrados gerarão um alerta de segurança. Os alertas de segurança no Azure Sentinel podem ser exibidos na seção **logs** do Azure Sentinel, na tabela **SecurityAlert** no grupo **Sentinela do Azure** .

No Azure Sentinel, os alertas gerados por meio de regras de análise também geram incidentes de segurança que podem ser encontrados em **incidentes** em **Gerenciamento de ameaças** no menu do Azure Sentinel. Os incidentes são o que suas equipes de operações de segurança analisarão e investigarão para determinar as ações de resposta apropriadas. Você pode encontrar informações detalhadas neste [tutorial: investigar incidentes com o Azure Sentinel](./tutorial-investigate-cases.md).

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>As pastas de trabalho fornecem informações sobre sua inteligência contra ameaças

Por fim, você pode usar uma pasta de trabalho do Azure Sentinel para visualizar informações importantes sobre sua inteligência contra ameaças no Azure Sentinel, e você pode personalizar facilmente as pastas de trabalho de acordo com suas necessidades de negócios.
Vamos examinar como encontrar a pasta de trabalho de inteligência contra ameaças fornecida no Azure Sentinel, e também mostraremos como fazer edições na pasta de trabalho para personalizá-la.

1. Abra o [portal do Azure](https://portal.azure.com/) e navegue até o serviço **Sentinela do Azure** .

1. Escolha o **espaço de trabalho** para o qual você importou indicadores de ameaça usando o conector de dados de inteligência contra ameaças.

1. Selecione **pastas de trabalho** na seção **Gerenciamento de ameaças** do menu do Azure Sentinel.

1. Navegue até a pasta de trabalho intitulada **inteligência contra ameaças** e verifique se você tem dados na tabela **ThreatIntelligenceIndicator** , conforme mostrado abaixo.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="Verificar os dados":::
 
1. Selecione o botão **salvar** e escolha um local do Azure para armazenar a pasta de trabalho. Essa etapa será necessária se você for modificar a pasta de trabalho de qualquer forma e salvar suas alterações.

1. Agora, selecione o botão **Exibir pasta de trabalho salva** para abrir a pasta de trabalho para exibição e edição.

1. Agora você deve ver os gráficos padrão fornecidos pelo modelo. Agora, vamos fazer algumas alterações em um dos gráficos. Selecione o botão **Editar** na parte superior da página para entrar no modo de edição da pasta de trabalho.

1. Vamos adicionar um novo gráfico de indicadores de ameaça por tipo de ameaça. Role até a parte inferior da página e selecione Adicionar consulta.

1. Adicione o seguinte texto à caixa de texto **consulta de log do espaço de trabalho log Analytics** :
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. Na lista suspensa **Visualização** , selecione **gráfico de barras**.

1. Selecione o botão **edição concluído** . Você criou um novo gráfico para a pasta de trabalho.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="Gráfico de barras":::

As pastas de trabalho fornecem painéis interativos avançados que fornecem informações sobre todos os aspectos do Azure Sentinel. Há muito que você pode fazer com pastas de trabalho e, embora os modelos fornecidos sejam um ótimo ponto de partida, você provavelmente desejará se aprofundar e personalizar esses modelos ou criar novos painéis combinando várias fontes de dados diferentes, para que você possa visualizar seus dados de maneiras exclusivas. Como as pastas de trabalho do Azure Sentinel são baseadas em pastas de trabalho Azure Monitor, já existe uma ampla documentação disponível e muitos outros modelos. Um ótimo lugar para começar é o artigo sobre como [criar relatórios interativos com Azure monitor pastas de trabalho](../azure-monitor/visualize/workbooks-overview.md). 

Também há uma rica comunidade de [pastas de trabalho do Azure monitor no GitHub](https://github.com/microsoft/Application-Insights-Workbooks) , em que você pode baixar modelos adicionais e contribuir com seus próprios modelos.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu sobre os recursos de inteligência contra ameaças do Azure Sentinel e a nova folha de inteligência contra ameaças. Para obter orientações práticas sobre como usar os recursos de inteligência contra ameaças do Azure Sentinel, consulte os seguintes artigos:

- [Conecte os dados de inteligência contra ameaças](./connect-threat-intelligence.md) ao Azure Sentinel.
- Crie alertas [internos](./tutorial-detect-threats-built-in.md) ou [personalizados](./tutorial-detect-threats-custom.md) e [investigue](./tutorial-investigate-cases.md) incidentes, no Azure Sentinel.