---
title: Lista de verificação de criação - da oferta comercial Marketplace do Azure
description: Os detalhes que você pode fornecer no processo de criação de oferta. -Comercial Marketplace do Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: eb824eb67e84ec4bdb93bc355ac6a6afa844ceb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67701166"
---
# <a name="offer-creation-checklist"></a>Lista de verificação da criação de oferta

O processo de criação de oferta você será levado através de várias páginas. Aqui estão os detalhes que você pode fornecer em cada página, com links para saber mais sobre cada item.

Itens que é necessários para fornecer ou especificar são indicadas abaixo. Algumas áreas são opcionais ou ter valores padrão fornecido, que você pode alterar conforme desejado. Você não precisa trabalhar com essas seções na ordem listada aqui.

| **Item**    | **Finalidade**  |
| :---------- | :-------------------|
| [**Novo Modal de oferta**](#new-offer-modal) | Coleta informações de identidade da oferta.  |
| [Página de instalação da oferta](#offer-setup-page) | Permite que você optar por usar recursos-chave e escolha como vender a sua oferta por meio da Microsoft.  |
| [Página de propriedades](#properties-page) | Defina as categorias e setores usados para agrupar sua oferta no Marketplace, os contratos legais que dão suporte a sua oferta e sua versão do aplicativo. |
| [Página de listagem da oferta](#offer-listing-page) | Defina os detalhes da oferta a ser exibido no marketplace, incluindo descrições de sua oferta e ativos de marketing. |
| [Página de visualização](#preview-page) | Defina um público limitado de visualização para liberar sua oferta antes de publicar sua oferta ao vivo para o público-alvo mais amplo do marketplace. |
| [Página de configuração técnica da oferta](#technical-configuration-page)  | Disponível somente se você optar por vender a oferta por meio da Microsoft. Defina os detalhes técnicos (caminho da URL, webhook, ID de locatário e ID do aplicativo) usados para se conectar à sua oferta. |
| [**Novo Modal de plano**](#plan-identity-modal) | Coleta informações de identidade do plano.  |
| [Planejar página de listagem](#plan-listing-page)  | Disponível somente se você optar por vender a oferta por meio da Microsoft. Defina os detalhes usados para listar o plano no marketplace.  |
| [Preço do plano e da página de disponibilidade](#plan-pricing--availability-page)  | Disponível somente se você optar por vender a oferta por meio da Microsoft.  Coleta as características de negócios (modelo de preços), o público-alvo e o mercado de disponibilidade para cada plano (versão) de sua oferta.  |
| [Página de listagem de unidade de teste](#test-drive-listing-page)  | Disponível somente se você selecionar a oferecer um test drive para sua oferta. Defina os detalhes usados para o teste de unidade no marketplace de lista.  |
| Página de configuração de técnicas de unidade de teste  | Disponível somente se você selecionar a oferecer um test drive para sua oferta. Defina os detalhes técnicos para a demonstração (ou "unidade de teste") que permitem que os clientes testar sua oferta antes de confirmar para adquiri-lo.  |
| [Examine e página de publicação](#review-and-publish-page)  | Selecione as alterações que você deseja publicar, ver o status de cada página e forneça anotações para a equipe de certificação.  |


## <a name="new-offer-modal"></a>Nova oferta modal 

As primeira partes de informações que você será solicitado a fornecer são uma ID e um alias para sua oferta. 

| **Nome do campo**    | **Observações**   |  
| :---------------- | :-----------| 
| ID da oferta  | Obrigatório, não pode ser alterado após a criação. Número máximo de 50 caracteres e deve consistir apenas de caracteres em minúsculas, alfanuméricos, traços ou sublinhados. |
| Alias de oferta  | Obrigatório. |

## <a name="offer-setup-page"></a>Página de instalação da oferta

A página de configuração da oferta é onde você pode optar por canais diferentes e movimentos de vendas, bem como declarar que o uso de recursos importantes, como teste de unidade e o cliente leva. 

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------|  
| Você gostaria de venda por meio da Microsoft?  | Obrigatório. Padrão: Sim |
| Como você deseja obter clientes potenciais para interagir com a oferta de listagem? (Plano de ação)  | Necessário se não venda por meio da Microsoft. Padrão: Avaliação gratuita, opções: "Obter agora", "Avaliação gratuita", "Entrar em contato comigo." |
| URL da Avaliação  | Necessário se "Avaliação gratuita" for selecionada, como os forma como os clientes devem interagir com a listagem de oferta. |
| URL da oferta  | Necessário se "Obter agora" está selecionado, como os forma como os clientes devem interagir com a listagem de oferta |
| Canais  | Opcional. Padrão: Não é aceito para o canal CSP (revendedor).  |
| Test drive | Opcional. Padrão: Não há test-drive habilitado.  |
| Tipo de Test Drive | Necessário se habilitado um test drive. Padrão: Nenhum selecionado. Opções: O Azure Resource Manager, o Dynamics 365 para o Central de negócios, o Dynamics 365 for Customer Engagement, Dynamics 365 para operações de aplicativo lógico, Power BI.  |
| Gerenciamento de cliente potencial – conectar-se a um sistema CRM | Necessário se venda por meio da Microsoft, ou se a listagem oferece como "Entrar em contato comigo." Padrão: nenhum sistema CRM conectado. Opções de CRM: De tabelas do Azure, blob do Azure, Dynamics CRM online, dos ponto de extremidade HTTPs, Marketo, Salesforce  |

## <a name="properties-page"></a>Página Propriedades

A página de propriedades é onde você define as categorias e setores usados para agrupar sua oferta no Marketplace, os contratos legais que dão suporte a sua oferta e sua versão do aplicativo. Certifique-se de fornecer detalhes completas e precisas sobre sua oferta nessa página, para que ele tenha exibidos adequadamente e oferecido para o conjunto certo de clientes. 

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------|  
| Categoria e subcategoria | 3 de 1 e máximo necessário. Padrão: Nenhum selecionado. |
| Setores e subindustries | Opcional. máximo de setores de L1 2 e máximo de 2 subindustries dentro de cada setor de L1, padrão: Nada selecionado |
| Versão do aplicativo  | Opcional. Padrão: nenhuma. |
| Contrato padrão de uso  | Opcional. Padrão: não selecionado.  | |
| Termos de uso  | Necessário se o contrato padrão não está selecionado.  |

## <a name="offer-listing-page"></a>Página de listagem de oferta

A página de listagem é onde você fornece o texto e imagens que os clientes veem ao exibir a listagem da sua oferta no marketplace. 

| **Nome do campo**    | **Observações**   |
| :---------------- | :-----------| 
| Nome  | Obrigatório, máximo 50 caracteres. |
| Resumo  | Obrigatório, máximo 100 caracteres. | 
| DESCRIÇÃO  | Obrigatório, máximo de 3000 caracteres. |
| Instruções de introdução  | Obrigatório, máximo de 3000 caracteres. |
| Instruções de introdução  | Obrigatório, máximo de 3000 caracteres. |
| Palavras-chave para pesquisa  | Opcional, mas recomendado, palavras-3 chave max. |
| URL da política de privacidade  | Obrigatório. |
| URL de materiais de Marketing de programa CSP  | Opcional. |
| Links úteis título + URL  | Opcional. |
| Documentos de suporte título + arquivo  | Obrigatório, min 1 e máximo de 3. Deve ser o formato de arquivo PDF. |
| Capturas de tela  | Obrigatório, captura de tela de 1 de min e max 5; quatro ou mais recomendados. Deve ser no formato PNG 1280 X 720. |
| Store logotipos (pequeno, médio, grande, todo, Hero)  | Pequeno (48 X 48) e grande (216x216) necessária; outros tamanhos opcionais mas recomendado: Médio (90 x 90), todo (255 x 115), o herói (815 x 290). Deve estar no formato PNG. |
| Nome de vídeos + URL + miniatura  | Opcional, recomendado, máximo de 4 vídeos. A miniatura deve ser 1280 x 720 no formato PNG. Vídeo deve ser hospedado no YouTube ou Vimeo. |
| Contatos (programa CSP, engenharia, suporte)  | Engenharia e contato de suporte necessários (nome, email e número de telefone); Contato do programa CSP opcional mas recomendado. |
| URL do suporte  | Obrigatório. |

## <a name="preview-page"></a>Página de visualização

A página de visualização é onde você especifica o público-alvo para ter acesso à sua oferta de visualização, para verificar se a oferta atende a todos os seus requisitos antes de entrar no ar. 

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------| 
| Email do AAD/MSA + descrição | Obrigatório, 1 de min e max 10 se inserido manualmente ou até 20 se carregando um arquivo CSV. |

## <a name="technical-configuration-page"></a>Página de configurações técnicas 

A página de configuração técnico é onde você pode especificar os detalhes técnicos usados pela Microsoft para se conectar a sua oferta. Esta página não está visível para você, se você decidiu não vendidos por meio da Microsoft.

| **Nome do campo**    | **Observações**   |  
| :---------------- | :-----------| 
| URL da página de aterrissagem | Necessário se estivermos vendendo por meio da Microsoft. |
| Webhook de Conexão | Necessário se estivermos vendendo por meio da Microsoft. |
| ID do locatário do Azure AD | Necessário se estivermos vendendo por meio da Microsoft. |
| ID do aplicativo do Azure AD | Necessário se estivermos vendendo por meio da Microsoft. |

## <a name="plan-identity-modal"></a>Plano de identidade modal

As primeira partes de informações que você será solicitado a fornecer são um nome e uma ID para seu plano. Esta página não está visível para você, se você optou por não vendidos por meio da Microsoft.

| **Nome do campo**    | **Observações**   |  
| :---------------- | :-----------| 
| ID do plano  | Necessário se estivermos vendendo por meio da Microsoft. Ele não pode ser alterado após a criação. Número máximo de 50 caracteres e deve consistir apenas de caracteres em minúsculas, alfanuméricos, traços ou sublinhados. |
| Nome do plano  | Necessário se estivermos vendendo por meio da Microsoft. Deve ser exclusivo em todos os planos da oferta. Máx. 50 caracteres. |

## <a name="plan-listing-page"></a>Página de listagem de plano

O plano de página de listagem é onde você pode fornecer o texto para os clientes vejam ao exibir o plano no marketplace. Esta página não está visível para você, se você decidiu não vendidos por meio da Microsoft.

| **Nome do campo**    | **Observações**   |  
| :---------------- | :-----------| 
| Descrição do plano   | Necessário se estivermos vendendo por meio da Microsoft. Máximo de 500 caracteres. | |

## <a name="plan-pricing--availability-page"></a>Página de preços e disponibilidade do plano

A página de preços e disponibilidade do plano é onde você define as características de negócios, público-alvo e disponibilidade do mercado para cada plano (versão) de sua oferta. Esta página não está visível para você, se você decidiu não vendidos por meio da Microsoft.

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------| 
| Disponibilidade do mercado  | Necessário, 1 de min e max 141. |
| Modelo de preços  | Obrigatório. Padrão: Taxa fixa. Opções: Taxa fixa por usuário. |
| Mínimo e máximo estações  | Opcional, disponível somente se o modelo selecionado de preços com base em estação. |
| Termo de cobrança  | Obrigatório. Padrão: Mensal. Opções: Mensal, anual. |
| Preço  | Necessário USD por mês, se o termo selecionado; a cobrança mensal USD por ano se anual de cobrança ou termo selecionado. |
| Público-alvo do plano  | Opcional. Padrão: Plano público. Opções: Público, privado, por ID de locatário |
| Restrito plano público-alvo (ID de locatário + descrição)  | Necessário se o plano privado selecionado. 1 de min e max 10 IDs de locatário se inserido manualmente. Max 20000 se a importação do arquivo CSV. |

## <a name="test-drive-listing-page"></a>Página de listagem de unidade de teste

Disponível somente se você selecionar a oferecer um test drive para sua oferta. Defina os detalhes usados para o teste de unidade no marketplace de lista.

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------| 
| DESCRIÇÃO  | Obrigatório. |
| Nome de usuário Manual + arquivo  | 1 doc necessária, máx. Deve ser o formato PDF. |
| Nome do vídeo, URL + miniatura  | Opcional, mas recomendado. A miniatura deve ser 533, 324 no formato JPGP ou PNG. Vídeo deve ser hospedado no YouTube ou Vimeo. |

## <a name="review-and-publish-page"></a>Examine e página de publicação

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------| 
| Notas para certificação  | Opcional. |

## <a name="next-steps"></a>Próximas etapas

- [Criar uma nova oferta de SaaS](./create-new-saas-offer.md)
