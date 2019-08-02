---
title: Sobre o Azure Lab Services | Microsoft Docs
description: Saiba como serviços de laboratório podem facilitar a criação, o gerenciamento e a proteção de laboratórios com máquinas virtuais que podem ser usados por desenvolvedores, testadores, educadores, alunos e outros.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/13/2018
ms.author: spelluru
ms.openlocfilehash: 3c23cc4fc7647cffa98cff9310498f683fda6328
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275713"
---
# <a name="an-introduction-to-azure-lab-services"></a>Uma introdução ao Azure Lab Services
O Azure Lab Services permite que você configure rapidamente um ambiente para a sua equipe (por exemplo: ambiente de desenvolvimento, ambiente de teste, ambiente de laboratório de sala de aula) na nuvem. Um proprietário de laboratório cria um laboratório, provisiona máquinas virtuais do Windows ou do Linux, instala o software e as ferramentas necessárias e disponibiliza-os para os usuários do laboratório. Os usuários do laboratório se conectam às VMs (máquinas virtuais) do laboratório e as usam para o trabalho diário, para projetos de curto prazo ou para fazer exercícios em sala de aula. Depois que os usuários começam a usar recursos no laboratório, um administrador de laboratório pode analisar o custo e o uso em vários laboratórios e definir políticas abrangentes para otimizar os custos da equipe ou sua organização.

> [!IMPORTANT]
> O **Azure DevTest Labs** está sendo expandido com novos tipos de laboratórios (Azure Lab Services)!
>  
> O Azure Lab Services permite criar tipos de laboratório gerenciado, como laboratórios de sala de aula. O próprio serviço cuida de todo o gerenciamento da infraestrutura de um tipo de laboratório gerenciado, da criação de VMs ao tratamento de erros e dimensionamento da infraestrutura. Por enquanto, o [DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) e o [Azure Lab Services](https://azure.microsoft.com/services/lab-services/) continuarão sendo serviços separados no portal do Azure. 

## <a name="key-capabilities"></a>Principais recursos

O Azure Lab Services dá suporte aos seguintes recursos/funcionalidades principais:

- **Configuração rápida e flexível de um laboratório**. Usando o Azure Lab Services, os proprietários de laboratório podem rapidamente configurar um laboratório para suas necessidades. O serviço oferece a opção para cuidar de todo o trabalho de infraestrutura do Azure de tipos de laboratório gerenciado ou permitir que os proprietários do laboratório gerenciem e personalizem a infraestrutura na assinatura do proprietário do laboratório. O serviço fornece dimensionamento interno e a resiliência da infraestrutura para laboratórios que o serviço gerencia para você.
- **Simplificado experiência para os usuários do laboratório**. Em um tipo de laboratório gerenciado, como um laboratório de sala de aula, os usuários do laboratório podem se registrar em um laboratório com um código de registro e acessar o laboratório a qualquer momento para usar os recursos dele. Em um laboratório criado no DevTest Labs, um proprietário de laboratório pode atribuir permissões para usuários do laboratório criarem e acessarem as máquinas virtuais, gerenciar e reutilizar os discos de dados e configurar segredos reutilizáveis.  
- **Otimização e análise de custo**. Um proprietário de laboratório pode definir programações para desligar e inicializar máquinas virtuais automaticamente. O proprietário do laboratório pode definir uma programação para especificar os horários quando as máquinas virtuais do laboratório estarão acessíveis para os usuários, definir políticas de uso por usuário ou por laboratório para otimizar custos e analisar as tendências de uso e atividade em um laboratório. Para tipos de laboratório gerenciado, como laboratórios de sala de aula, atualmente, um subconjunto menor de opções de análise e otimização de custo está disponível.
- **Segurança inserida**. O proprietário de um laboratório pode configurar uma rede virtual privada e uma sub-rede para um laboratório e habilitar um endereço IP público compartilhado. Os usuários do laboratório podem acessar recursos com segurança usando a rede virtual configurada com o ExpressRoute ou o VPN site a site. (atualmente disponível apenas no DevTest Labs)
- **Integração com seus fluxos de trabalho e suas ferramentas**. O Azure Lab Services permite integrar os laboratórios no site e nos sistemas de gerenciamento da sua organização. Você pode provisionar automaticamente ambientes de dentro de suas ferramentas de integração contínua/implantação contínua (CI/CD). (atualmente disponível apenas no DevTest Labs)

> [!NOTE]
> Atualmente, o Azure Lab Services dá suporte apenas às VMs criadas de imagens do Azure Marketplace. Se você quiser usar imagens personalizadas ou criar outros recursos de PaaS em um ambiente de laboratório, use o DevTest Labs. Para saber mais, confira [Criar uma imagem personalizada no DevTest Labs](devtest-lab-create-custom-image-from-vm-using-portal.md) e [Criar ambientes de laboratório usando modelos do Resource Manager](devtest-lab-create-environment-from-arm.md).

## <a name="scenarios"></a>Cenários

Aqui estão alguns dos cenários aos quais o Azure Lab Services dá suporte:

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Configure um laboratório de computação redimensionável na nuvem para sua sala de aula  

- Crie um laboratório de sala de aula gerenciada. Diga ao serviço exatamente o que você precisa, e ele criará e gerenciará a infraestrutura do laboratório para você, para que você possa se concentrar em dar a aula e não nos detalhes técnicos não de um laboratório.
- Forneça aos alunos um laboratório de máquinas virtuais configuradas exatamente com o necessário para uma aula. Dê a cada aluno um número limitado de horas para usar as VMs para trabalho em sala.  
- Mova o laboratório de computadores físicos da escola para a nuvem. Dimensione automaticamente o número de VMs até o limite máximo de uso e de custo definido no laboratório.
- Exclua o laboratório com um único clique quando terminar.

### <a name="use-devtest-labs-for-development-environments"></a>Usar o DevTest Labs para ambientes de desenvolvimento

O Azure DevTest Labs pode ser usado para implementar vários cenários importantes, mas um dos principais cenários envolve o uso DevTest Labs para hospedar computadores de desenvolvimento para desenvolvedores. Neste cenário, o DevTest Labs oferece estes benefícios:

- Permita que os desenvolvedores provisionem rapidamente seus computadores de desenvolvimento sob demanda.
- Provisione os ambientes do Windows e do Linux usando modelos e artefatos reutilizáveis.
- Os desenvolvedores podem personalizar facilmente seus computadores de desenvolvimento sempre que for necessário.
- Os administradores podem controlar os custos garantindo que os desenvolvedores não podem ter mais VMs que precisam para o desenvolvimento e as VMs sejam desligadas quando não estiverem em uso.

Para obter mais informações, consulte [Use DevTest Labs para desenvolvimento](devtest-lab-developer-lab.md).

### <a name="use-devtest-labs-for-test-environments"></a>Usar DevTest Labs para ambientes de teste

Use o Azure DevTest Labs para implementar vários cenários importantes. No entanto, um cenário principal envolve o uso do DevTest Labs para hospedar computadores para testadores. Neste cenário, o DevTest Labs oferece estes benefícios:

- Os testadores podem testar a última versão de seus aplicativos provisionando ambientes Windows e Linux rapidamente com modelos e artefatos reutilizáveis.
- Os testadores podem escalar verticalmente seu teste de carga provisionando vários agentes de teste.
- Os administradores podem controlar os custos garantindo que os testadores não podem ter mais VMs que precisam para o teste e as VMs sejam desligadas quando não estiverem em uso.

Para obter mais informações, consulte [Use DevTest Labs para teste](devtest-lab-test-env.md).

## <a name="types-of-labs"></a>Tipos de laboratórios
Você pode criar dois tipos de laboratórios: **tipos de laboratório gerenciado** com o Azure Lab Services e **laboratórios** com o Azure Lab Services. Caso deseje apenas inserir o que precisa em um laboratório e deixar o serviço configurar e gerenciar a infraestrutura necessária para o laboratório, escolha um dos **tipos de laboratório gerenciado**. Atualmente, o **laboratório de sala de aula** é o único tipo de laboratório gerenciado que pode ser criado com o Azure Lab Services. Caso deseje gerenciar sua própria infraestrutura, crie um laboratório usando o **Azure DevTest Labs**.

As seções a seguir fornecem mais detalhes sobre esses laboratórios. 

## <a name="managed-lab-types"></a>Tipos de laboratório gerenciado
O Azure Lab Services permite que você crie laboratórios cuja infraestrutura é gerenciada pelo Azure. Este artigo se refere a eles como tipos de laboratório gerenciado. Os tipos de laboratório gerenciado oferecem tipos diferentes de laboratórios de acordo com sua necessidade específica. Atualmente, o único tipo de laboratório gerenciado com suporte é o **laboratório de sala de aula**. 

Os tipos de laboratório gerenciado permitem o início imediato, com o mínimo de configuração. O próprio serviço administra todo o gerenciamento da infraestrutura do laboratório, desde a execução das VMs até o tratamento de erros e o dimensionamento da infraestrutura. Para criar um tipo de laboratório gerenciado, como um laboratório de sala de aula, primeiro você precisa criar uma conta de laboratório para sua organização. A conta de laboratório serve como a conta central no qual todos os laboratórios na organização são gerenciados. 

Quando você cria e usa recursos do Azure nesses tipos de laboratório gerenciado, o serviço cria e gerencia os recursos em assinaturas internas da Microsoft. Eles não são criados em sua própria assinatura do Azure. O serviço mantém o controle de uso desses recursos em assinaturas internas da Microsoft. Esse uso é debitado na sua assinatura do Azure que contém a conta de laboratório.   

Estes são alguns dos **casos de uso dos tipos de laboratório gerenciado**: 

- Fornecer aos alunos um laboratório de máquinas virtuais configuradas exatamente com o necessário para uma aula. Ofereça a cada aluno um número limitado de horas para usar as VMs em suas tarefas escolares ou projetos pessoais.
- Configure um pool de VMs de alto desempenho para executar uma pesquisa com uso intenso de computação ou de elementos gráficos. Mover o laboratório de computadores físicos da escola para a nuvem. 
- Mova o laboratório de computadores físicos da escola para a nuvem. Dimensione automaticamente o número de VMs até o limite máximo de uso e de custo definido no laboratório.  
- Provisionar rapidamente um laboratório de máquinas virtuais para hospedar um hackathon. Exclua o laboratório com um único clique quando terminar. 


## <a name="devtest-labs"></a>Laboratórios de Desenvolvimento/Teste
Pode haver situações nas quais você deseja gerenciar toda a infraestrutura e configuração por conta própria, dentro da sua própria assinatura. Para fazer isso, você pode criar um laboratório com o Azure DevTest Labs no portal do Azure. Para esses laboratórios, não é necessário criar uma conta de laboratório. Esses laboratórios não são exibidos na conta de laboratório (existente para os tipos de laboratório gerenciado).  

Aqui estão alguns dos **casos de uso dos DevTest Labs**: 

- Provisione rapidamente um laboratório de máquinas virtuais para hospedar um hackathon ou uma sessão prática em uma conferência. Exclua o laboratório com um único clique quando terminar. 
- Crie um pool de VMs configuradas com o seu aplicativo e permita que sua equipe use facilmente uma VM para correção de bugs.  
- Fornece aos desenvolvedores máquinas virtuais configuradas com todas as ferramentas necessárias. Agende inicialização e desligamento automáticos para minimizar os custos. 
- Crie repetidamente um laboratório de máquinas de teste como parte da sua implantação. Teste os bits mais recentes e limpe as máquinas de teste quando terminar. 
- Defina várias máquinas virtuais configuradas de forma diferente e vários agentes de teste para teste de desempenho e de escala. 
- Ofereça sessões de treinamento aos clientes com um laboratório configurado com a versão mais recente do produto. Dê a que cada cliente um número limitado de horas para uso do laboratório. 


## <a name="managed-lab-types-vs-devtest-labs"></a>Tipos de laboratório gerenciado versus Laboratórios de Desenvolvimento/Teste
A tabela a seguir compara dois tipos de laboratórios que têm suporte dos Azure Lab Services: 

| Recursos | Tipos de laboratório gerenciado | Laboratórios de Desenvolvimento/Teste |
| -------- | ----------------- | ---------- |
| Gerenciamento da infraestrutura do Azure no laboratório. |  Gerenciado automaticamente pelo serviço | Você gerencia a sua própria  |
| Resiliência interna para problemas de infraestrutura | Administrado automaticamente pelo serviço | Você gerencia a sua própria  |
| Gerenciamento de assinaturas | O serviço lida com a alocação de recursos nas assinaturas do Microsoft que dão apoio ao serviço. A escala é administrada automaticamente pelo serviço. | Você gerencia a sua própria em sua assinatura do Azure. Sem dimensionamento automático de assinaturas. |
| Implantação do Azure Resource Manager dentro do laboratório | Não disponível | Disponível |

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos: 

- [Sobre os laboratórios de sala de aula](./classroom-labs/classroom-labs-overview.md)
- [Sobre o DevTest Labs](devtest-lab-overview.md)
