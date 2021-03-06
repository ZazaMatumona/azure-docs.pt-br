---
title: Lista de verificação de criação de oferta de SaaS no Marketplace comercial da Microsoft
description: Os detalhes que você pode fornecer no processo de criação da oferta de SaaS no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: c72d4d4f77ecf0bcad2b521650fd8ff7612fb604
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815344"
---
# <a name="saas-offer-creation-checklist-in-partner-center"></a>Lista de verificação de criação de oferta SaaS no Partner Center

O processo de criação da oferta de SaaS leva você por várias páginas.  Este artigo descreve os detalhes que você pode fornecer em cada página, com links para saber mais sobre cada item.

> [!NOTE]
> Se você estiver criando uma oferta de SaaS transactável, certifique-se de implementar a integração com [APIs de cumprimento de SaaS](./pc-saas-fulfillment-apis.md).  A integração com as APIs é a única maneira para que a transação do mercado funcione corretamente. Você também precisa certificar-se de que seu aplicativo usa a autenticação do Azure AD com SSO (logon único). Consulte [Azure AD e ofertas de SaaS transactáveis no Marketplace comercial](../azure-ad-saas.md).

Os itens que você precisa fornecer ou especificar estão indicados abaixo.  Algumas áreas são opcionais ou têm valores padrão fornecidos, que você pode alterar conforme desejado.  Você não precisa trabalhar nessas seções na ordem listada aqui.

| Item | Finalidade |
| ---------- | -------------------|
| [**Nova oferta modal**](#new-offer-modal) | Coleta informações de identidade da oferta.  |
| [Página de instalação da oferta](#offer-setup-page) | Permite aceitar o uso dos principais recursos e escolher como vender a oferta pela Microsoft.  |
| [Página de propriedades](#properties-page) | Defina as categorias e os setores usados para agrupar sua oferta nos marketplaces, sua versão do aplicativo e os contratos legais que dão suporte à sua oferta. |
| [Página de listagem da oferta](#offer-listing-page) | Defina os detalhes da oferta a serem exibidos no marketplace, incluindo descrições de sua oferta e de ativos de marketing.|
| [Página de visualização](#preview-page) | Defina um público-alvo de versão prévia limitado para o qual liberar sua oferta antes de publicá-la como ativa para o público-alvo do marketplace em geral.|
| [Página Configuração técnica](#technical-configuration-page)  |  Disponível somente se você optar por vender a oferta pela Microsoft.  Defina os detalhes técnicos (URL da página de aterrissagem, URL de webhook de conexão, ID do locatário do Azure AD e ID do aplicativo do Azure AD) usados pelo Marketplace para se conectar à sua oferta.  Esses parâmetros são necessários para integrar corretamente com o cumprimento de SaaS e as APIs de cobrança limitada do Marketplace.|
| [**Novo plano modal**](#plan-identity-modal) | Coleta as informações de identidade do plano.  |
| [Página de listagem do plano](#plan-listing-page)  | Disponível somente se você optar por vender a oferta pela Microsoft. Defina os detalhes usados para listar o Plano no marketplace.  |
| [Planejar a página de preços e disponibilidade](#plan-pricing-and-availability-page)  | Disponível somente se você optar por vender a oferta pela Microsoft.  Coleta as características comerciais (modelo de preço), o público-alvo e a disponibilidade do mercado para cada plano (versão) da oferta.  |
| [Página de listagem do Test Drive](#test-drive-listing-page)  | Disponível somente se você optar por oferecer um test drive para a oferta. Defina os detalhes usados para listar o test drive no marketplace.  |
| Página Configuração Técnica do Test Drive  | Disponível somente se você optar por oferecer um test drive para a oferta. Defina os detalhes técnicos para a demonstração (ou "test drive") que permitirá que os clientes experimentem a oferta antes de confirmar a compra.  |
| [Página revisar e publicar](#review-and-publish-page)  | Selecione as alterações que você deseja publicar, confira o status de cada página e forneça observações para a equipe de certificação.  |
|

## <a name="new-offer-modal"></a>Modal Nova oferta

As primeiras informações que você deve fornecer são uma ID e um alias para a oferta.

| Nome do campo | Observações |  
| ---------------- | -----------|
| ID da oferta  | Obrigatório, não pode ser alterado após a criação. No máximo, 50 caracteres e deve conter somente letras minúsculas, caracteres alfanuméricos, traços ou sublinhados. |
| Alias da oferta  | Obrigatório |
|

## <a name="offer-setup-page"></a>Página Configuração da oferta

A página Configuração da oferta é onde você pode aceitar diferentes canais e movimentos de venda, bem como declarar o uso dos principais recursos, como test drive e clientes potenciais.

| Nome do campo | Observações |
| ---------------- | -----------|  
| Você gostaria de vender pela Microsoft?  | Obrigatórios. Padrão: Sim. |
| Como você deseja que possíveis clientes interajam com a listagem de ofertas? (Plano de ação)  | Obrigatório, se não estiver vendendo pela Microsoft. Padrão: Avaliação Gratuita, Opções: "Obter agora", "Avaliação gratuita", "Entre em contato comigo". |
| URL da Avaliação  | Obrigatório, se a opção "Avaliação Gratuita" foi selecionada, como o modo como os clientes devem interagir com a listagem de ofertas. |
| URL da oferta  | Necessário se "obter agora" estiver selecionado, como o modo como os clientes devem interagir com a listagem de ofertas. |
| Canais  | Opcional. Padrão: Não aceito pelo canal do CSP (revendedor).  |
| Test drive | Opcional. Padrão: Nenhum test drive habilitado.  |
| Tipo de Test Drive | Obrigatório, se um test drive estiver habilitado. Padrão: Nenhum selecionado. Opções: Azure Resource Manager, Dynamics 365 for Business Central, Dynamics 365 for Customer Engagement, Dynamics 365 for Operations, aplicativo lógico, Power BI.  |
| Clientes potenciais - conectar-se a um sistema CRM | Obrigatório, se estiver vendendo pela Microsoft ou se estiver listando as ofertas como "Entre em contato comigo". Padrão: nenhum sistema CRM conectado. Opções de CRM: tabela do Azure, BLOB do Azure, Dynamics CRM Online, HTTPs ' ponto de extremidade, Marketo, Salesforce.  |
|

## <a name="properties-page"></a>Página Propriedades

A página Propriedades permite que você defina as categorias e os setores usados para agrupar sua oferta no marketplace, os contratos legais que dão suporte à oferta e a versão do aplicativo. Não deixe de fornecer detalhes completos e precisos sobre a oferta nesta página, para que seja exibida adequadamente e oferecida ao conjunto certo de clientes.

| Nome do campo | Observações |
| ---------------- | -----------|  
| Categoria e subcategoria | Obrigatório 1 e no máximo 3. Padrão: Nenhum selecionado. |
| Setores e sub-setores | Opcional. no máximo 2 setores L1 e no máximo 2 sub-setores em cada setor de L1, Padrão: Nenhum selecionado |
| Versão do aplicativo  | Opcional. Padrão: Nenhum. |
| Use Contrato Standard  | Opcional. Padrão: não selecionado.  |
| Termos de uso  | Obrigatório, se o Contrato Standard não estiver selecionado.  |
|

## <a name="offer-listing-page"></a>Página Listagem de ofertas

A página Listagem é onde você fornece o texto e as imagens que os clientes veem ao exibir a listagem de ofertas no marketplace.

| Nome do campo | Observações |
| ---------------- | -----------|
| Nome  | Obrigatório, no máximo 50 caracteres. |
| Resumo  | Obrigatório, no máximo 100 caracteres. |
| Descrição  | Obrigatório, no máximo 3.000 caracteres. |
| Instruções de introdução  | Obrigatório, no máximo 3.000 caracteres. |
| Instruções de introdução  | Obrigatório, no máximo 3.000 caracteres. |
| Palavras-chave para pesquisa  | Opcional, recomendado, no máximo 3 palavras-chave. |
| URL da política de privacidade  | Obrigatório |
| URL dos materiais de marketing do programa CSP  | Opcional |
| Título + URL dos links úteis  | Opcional |
| Título + arquivo dos documentos de suporte  | Obrigatório, no mínimo 1 e no máximo 3. Deve ser no formato de arquivo PDF. |
| Capturas de tela  | Obrigatório, no mínimo 1 captura de tela e no máximo 5, recomenda-se quatro ou mais. Deve ser 1280 X 720 no formato PNG. |
| Logotipos da loja (pequeno, médio, grande)  | É necessário o logotipo grande (de 216 x 216 a 350 x 350 px). O Partner Center usará isso para criar um logotipo pequeno (48 x 48 px) e médio (90 x 90 px). Opcionalmente, você pode substituí-los por imagens diferentes posteriormente. Os logotipos devem estar no formato PNG. |
| Nome + URL + miniatura dos vídeos  | Opcional, recomendado, no máximo 4 vídeos. A miniatura deve ser 1280 X 720 no formato PNG. O vídeo deve ser hospedado no YouTube ou no Vimeo. |
| Contatos (programa CSP, engenharia, suporte)  | Contato de engenharia e suporte obrigatório (nome, email e número de telefone); o contato do programa CSP é opcional, mas recomendado. |
| URL do suporte  | Obrigatório |
|

## <a name="preview-page"></a>Página Visualização

A página Visualização é onde você especifica o público-alvo para ter acesso à visualização da oferta, para verificar se a oferta atende a todos os requisitos, antes de entrar em tempo real.

| Nome do campo | Observações |
| ---------------- | -----------|
| Email + descrição AAD/MSA | Obrigatório, no mínimo 1 e no máximo 10, se inseridos manualmente, ou até 20 se estiver carregando um arquivo CSV. |
|

## <a name="technical-configuration-page"></a>Página Configuração técnica

A página Configuração técnica é onde você especifica os detalhes técnicos usados pela Microsoft para se conectar à oferta. Esta página não fica visível se você decidiu não vender pela Microsoft.

> [!NOTE]
> Para ofertas de transações, você deve criar uma página de aterrissagem e seu aplicativo deve usar a autenticação do Azure AD com SSO (logon único). Para obter mais informações, consulte [ofertas do Azure AD e SaaS transformadas no Marketplace comercial](../azure-ad-saas.md).

| Nome do campo | Observações |  
| ---------------- | -----------| 
| URL da página de aterrissagem | Obrigatório, se estiver vendendo pela Microsoft. |
| Webhook de conexão | Obrigatório, se estiver vendendo pela Microsoft. |
| ID do locatário do Azure AD | Obrigatório, se estiver vendendo pela Microsoft. |
| ID do aplicativo do Azure AD | Obrigatório, se estiver vendendo pela Microsoft. |
|

## <a name="plan-identity-modal"></a>Modal Identidade do plano

As primeiras informações que você deve fornecer são um nome e uma ID para o Plano. Esta página não fica visível se você decidiu não vender pela Microsoft.

| Nome do campo | Observações |  
| ---------------- | -----------|
| ID do plano  | Obrigatório, se estiver vendendo pela Microsoft. Não pode ser alterado após a criação. No máximo, 50 caracteres e deve conter somente letras minúsculas, caracteres alfanuméricos, traços ou sublinhados. |
| Nome do Plano  | Obrigatório, se estiver vendendo pela Microsoft. Deve ser exclusivo em todos os planos na oferta. No máximo 50 caracteres. |
|

## <a name="plan-listing-page"></a>Página Listagem de planos

A página Listagem de planos é onde você fornece o texto que os clientes veem ao exibir o plano no marketplace. Esta página não fica visível se você decidiu não vender pela Microsoft.

| Nome do campo | Observações |  
| ---------------- | -----------|
| Descrição do plano   | Obrigatório, se estiver vendendo pela Microsoft. No máximo 500 caracteres. |
|

## <a name="plan-pricing-and-availability-page"></a>Planejar a página de preços e disponibilidade

A página Preço e disponibilidade do plano é onde você define as características comerciais, o público-alvo e a disponibilidade do mercado para cada plano (versão) da oferta. Esta página não fica visível se você decidiu não vender pela Microsoft.

| Nome do campo | Observações |
| ---------------- | -----------|
| Disponibilidade do mercado  | Obrigatório, no mínimo 1 e no máximo 141. |
| Modelo de preços  | Obrigatórios. Padrão: Taxa fixa. Opções: Taxa fixa, por usuário. |
| Estações mínimas e máximas  | Opcional, disponível somente se o modelo de preço baseado em estações foi selecionado. |
| Prazo de cobrança  | Obrigatórios. Padrão: Mensalmente. Opções: Mensal, anual. |
| Price  | Obrigatório, USD por mês, se o prazo de cobrança mensal foi selecionado, ou USD por ano, se o prazo de cobrança anual foi selecionado. |
| Público-alvo do plano  | Opcional. Padrão: Plano público. Opções: Público, privado por ID do locatário |
| Público-alvo restrito do plano (ID do locatário + descrição)  | Obrigatório, se o plano privado foi selecionado. No mínimo 1 e no máximo 10 IDs do locatário, se inseridas manualmente. No máximo 20.000, no caso de importação do arquivo CSV. |
|

## <a name="test-drive-listing-page"></a>Página de listagem do Test Drive

Disponível somente se você optar por oferecer um test drive para a oferta. Defina os detalhes usados para listar o test drive no marketplace.

| Nome do campo | Observações |
| ---------------- | -----------|
| Descrição  | Obrigatório |
| Nome + arquivo do manual do usuário  | Obrigatório, no máximo 1 documento. Deve ser no formato de PDF. |
| Nome, URL + miniatura do vídeo  | Opcional, recomendado. A miniatura deve ser 533 X 324 no formato JPGP ou PNG. O vídeo deve ser hospedado no YouTube ou no Vimeo. |
|

## <a name="review-and-publish-page"></a>Página Examinar e publicar

| Nome do campo | Observações |
| ---------------- | -----------|
| Notas para certificação  | Opcional |
|

## <a name="next-steps"></a>Próximas etapas

- [Criar uma oferta de SaaS](./create-new-saas-offer.md)
