---
title: Sobre o Azure Lab Services | Microsoft Docs
description: Saiba como serviços de laboratório podem facilitar a criação, o gerenciamento e a proteção de laboratórios com máquinas virtuais que podem ser usados por desenvolvedores, testadores, educadores, alunos e outros.
ms.topic: overview
ms.date: 09/16/2020
ms.openlocfilehash: ad17ebb3a803a15d1ac9ef8cb71cf8ca7976243b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333929"
---
# <a name="an-introduction-to-azure-lab-services"></a>Uma introdução ao Azure Lab Services
O **Azure Lab Services** permite criar laboratórios cuja infraestrutura é gerenciada pelo Azure. Atualmente, o laboratório de sala de aula é o único laboratório gerenciado com suporte do Azure Lab Services. O próprio serviço cuida de todo o gerenciamento da infraestrutura de um tipo de laboratório gerenciado, da criação de VMs ao tratamento de erros e dimensionamento da infraestrutura. Depois que um administrador de TI cria uma conta de laboratório no Azure Lab Services, um instrutor pode configurar rapidamente um laboratório para sua classe, especificar o número e o tipo de VMs necessárias para exercícios em classe e adicionar usuários à classe. Depois que um usuário se registra na classe, o usuário pode acessar a VM para fazer exercícios para a classe.  

## <a name="key-capabilities"></a>Principais recursos
O Azure Lab Services dá suporte aos seguintes recursos/funcionalidades principais:

- **Configuração rápida e flexível de um laboratório**. Usando o Azure Lab Services, os proprietários de laboratório podem rapidamente configurar um laboratório para suas necessidades. O serviço oferece a opção de cuidar de todo o trabalho de infraestrutura do Azure para tipos de laboratório gerenciados. O serviço fornece dimensionamento interno e a resiliência da infraestrutura para laboratórios que o serviço gerencia para você.
- **Simplificado experiência para os usuários do laboratório**. Os usuários do laboratório podem se registrar em um laboratório com um código de registro e acessar o laboratório a qualquer momento para usar seus recursos. 
- **Otimização e a análise de custo**. Um proprietário de laboratório pode definir programações para desligar e inicializar máquinas virtuais automaticamente. O proprietário do laboratório pode definir uma agenda para especificar os horários quando as máquinas virtuais do laboratório estarão acessíveis para os usuários e definir políticas de uso por usuário ou por laboratório para otimizar custos. 

Caso deseje apenas inserir o que precisa em um laboratório e deixar o serviço configurar e gerenciar a infraestrutura necessária para o laboratório, escolha um dos **tipos de laboratório gerenciado**. Atualmente, o **laboratório de sala de aula** é o único tipo de laboratório gerenciado que pode ser criado com o Azure Lab Services.

As seções a seguir fornecem mais detalhes sobre esses laboratórios. 

## <a name="managed-lab-types"></a>Tipos de laboratório gerenciado
O Azure Lab Services permite que você crie laboratórios cuja infraestrutura é gerenciada pelo Azure. Este artigo se refere a eles como tipos de laboratório gerenciado. Os tipos de laboratório gerenciado oferecem tipos diferentes de laboratórios de acordo com sua necessidade específica. Atualmente, o único tipo de laboratório gerenciado com suporte é o **laboratório de sala de aula**. 

Os tipos de laboratório gerenciado permitem o início imediato, com o mínimo de configuração. O próprio serviço administra todo o gerenciamento da infraestrutura do laboratório, desde a execução das VMs até o tratamento de erros e o dimensionamento da infraestrutura.  Para criar um tipo de laboratório gerenciado, como um laboratório de sala de aula, primeiro você precisa criar uma conta de laboratório para sua organização. A conta de laboratório serve como a conta central no qual todos os laboratórios na organização são gerenciados. 

Quando você cria e usa recursos do Azure nesses tipos de laboratório gerenciado, o serviço cria e gerencia os recursos em assinaturas internas da Microsoft. Eles não são criados em sua própria assinatura do Azure. O serviço mantém o controle de uso desses recursos em assinaturas internas da Microsoft. Esse uso é debitado na sua assinatura do Azure que contém a conta de laboratório.   

Estes são alguns dos **casos de uso dos tipos de laboratório gerenciado**: 

- Fornecer aos alunos um laboratório de máquinas virtuais configuradas exatamente com o necessário para uma aula. Ofereça a cada aluno um número limitado de horas para usar as VMs em suas tarefas escolares ou projetos pessoais.
- Configure um pool de VMs de alto desempenho para executar uma pesquisa com uso intenso de computação ou de elementos gráficos. Mover o laboratório de computadores físicos da escola para a nuvem. 
- Mova o laboratório de computadores físicos da escola para a nuvem. Dimensione automaticamente o número de VMs até o limite máximo de uso e de custo definido no laboratório.  
- Provisionar rapidamente um laboratório de máquinas virtuais para hospedar um hackathon. Exclua o laboratório com um único clique quando terminar. 

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes tutoriais para obter instruções passo a passo para criar uma conta de laboratório e um laboratório de sala de aula.

- [Tutorial: configurar uma conta de laboratório](tutorial-setup-lab-account.md)
- [Tutorial: criar um laboratório de sala de aula](tutorial-setup-classroom-lab.md)
