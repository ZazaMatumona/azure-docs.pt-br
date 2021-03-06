---
title: 'Início Rápido: Como criar seu primeiro aplicativo Web estático com os Aplicativos Web Estáticos do Azure'
description: Aprenda a criar uma instância dos Aplicativos Web Estáticos do Azure com sua estrutura de front-end preferida.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: bbc06b657525880f22bd5fb38e902f906d438c9c
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565903"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Início Rápido: Como criar seu primeiro aplicativo Web estático

Os Aplicativos Web Estáticos do Azure publicam sites em um ambiente de produção por meio da criação de aplicativos de um repositório do GitHub. Neste guia de início rápido, você cria um aplicativo Web usando sua estrutura de front-end preferida de um repositório GitHub.

Se você não tiver uma assinatura do Azure, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Pré-requisitos

- [GitHub](https://github.com)
- Conta do [Azure](https://portal.azure.com)

## <a name="create-a-repository"></a>Criar um repositório

Este artigo usa repositórios de modelo do GitHub para facilitar a criação de um novo repositório. Os modelos têm aplicativos iniciais criados com estruturas de front-end diferentes.

# <a name="angular"></a>[Angular](#tab/angular)

- Verifique se você está conectado ao GitHub e navegue até o local a seguir para criar um repositório
  - https://github.com/staticwebdev/angular-basic/generate
- Nomeie seu repositório **my-first-static-web-app**

# <a name="react"></a>[React](#tab/react)

- Verifique se você está conectado ao GitHub e navegue até o local a seguir para criar um repositório
  - https://github.com/staticwebdev/react-basic/generate
- Nomeie seu repositório **my-first-static-web-app**

# <a name="vue"></a>[Vue](#tab/vue)

- Verifique se você está conectado ao GitHub e navegue até o local a seguir para criar um repositório
  - https://github.com/staticwebdev/vue-basic/generate
- Nomeie seu repositório **my-first-static-web-app**

# <a name="no-framework"></a>[Nenhuma estrutura](#tab/vanilla-javascript)

- Verifique se você está conectado ao GitHub e navegue até o local a seguir para criar um repositório
  - https://github.com/staticwebdev/vanilla-basic/generate
- Nomeie seu repositório **my-first-static-web-app**

> [!NOTE]
> Os Aplicativos Web Estáticos do Azure requerem pelo menos um arquivo HTML para criar um aplicativo Web. O repositório criado nesta etapa inclui um único arquivo _index.html_.

---

Clique no botão **Criar repositório a partir do modelo**.

:::image type="content" source="media/getting-started/create-template.png" alt-text="Criar repositório a partir do modelo":::

## <a name="create-a-static-web-app"></a>Criar um aplicativo Web estático

Agora que o repositório foi criado, você pode criar um aplicativo Web estático no portal do Azure.

- Navegue até o [portal do Azure](https://portal.azure.com)
- Clique em **Criar um Recurso**
- Pesquise por **Aplicativos Web Estáticos**
- Clique em **Aplicativos Web Estáticos (Versão Prévia)**
- Clique em **Criar**

### <a name="basics"></a>Noções básicas

Para começar, configure seu novo aplicativo e vincule-o a um repositório GitHub.

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="Guia Básico":::

- Selecione sua _Assinatura do Azure_.
- Selecione ou crie um novo _Grupo de Recursos_
- Nomeie o aplicativo **my-first-static-web-app**.
  - Os caracteres válidos são `a-z` (não diferencia maiúsculas de minúsculas), `0-9` e `-`.
- Selecione a _Região_ mais próxima de você.
- Selecione o **SKU** _gratuito_
- Clique no botão **Entrar com o GitHub** e autentique-se com o GitHub.

Depois de entrar com o GitHub, insira as informações do repositório.

:::image type="content" source="media/getting-started/repository-details.png" alt-text="Detalhes do repositório":::

- Selecione sua _Organização_ de preferência.
- Selecione **my-first-web-static-app** na lista suspensa _Repositório_.
- Selecione **mestre** na lista suspensa _Branch_
- Clique em **Avançar: Build >** para editar a configuração do build

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="Botão Próxima compilação":::

> [!NOTE]
>  Caso você não veja nenhum repositório, talvez seja necessário autorizar Aplicativos Web Estáticos do Azure no GitHub. Navegue até a [home page do GitHub](https://github.com) e clique na imagem de sua conta para abrir o menu suspenso. Clique em **Configurações**, selecione **Aplicativos > Aplicativos OAuth Autorizados > Aplicativos Web Estáticos do Azure** e, por fim, selecione **Conceder**. Em repositórios corporativos, você precisa ser um proprietário da organização para conceder as permissões.

### <a name="build"></a>Build

Em seguida, adicione detalhes de configuração específicos à sua estrutura de front-end preferida.

# <a name="angular"></a>[Angular](#tab/angular)

- Insira **/** na caixa de _Locais do aplicativo_.
- Limpe o valor padrão na caixa _Local da API_.
- Digite **dist/angular-basic** na caixa _Local do artefato do aplicativo_.

# <a name="react"></a>[React](#tab/react)

- Insira **/** na caixa de _Locais do aplicativo_.
- Limpe o valor padrão na caixa _Local da API_.
- Insira **build** na caixa _Local do artefato do aplicativo_.

# <a name="vue"></a>[Vue](#tab/vue)

- Insira **/** na caixa de _Locais do aplicativo_.
- Limpe o valor padrão na caixa _Local da API_.
- Insira **dist** na caixa _Local do artefato do aplicativo_.

# <a name="no-framework"></a>[Nenhuma estrutura](#tab/vanilla-javascript)

- Insira **/** na caixa de _Locais do aplicativo_.
- Limpe o valor padrão na caixa _Local da API_.
- Limpe o valor padrão na caixa _Local do artefato do aplicativo_.

---

Selecione o botão **Revisar + Criar**.

:::image type="content" source="media/getting-started/review-create.png" alt-text="Botão Revisar Criar":::.

Para alterar esses valores após criar o aplicativo, edite o [arquivo de fluxo de trabalho](github-actions-workflow.md).

### <a name="review--create"></a>Examinar + criar

Depois que a solicitação for validada, você poderá continuar a criar o aplicativo.

Clique no botão **Criar**

:::image type="content" source="media/getting-started/create-button.png" alt-text="Botão Criar":::

Depois que o recurso for criado, clique no botão **Ir para o recurso**.

:::image type="content" source="media/getting-started/resource-button.png" alt-text="Botão Ir para o recurso":::.

## <a name="view-the-website"></a>Exibir o site

Há dois aspectos na implantação de um aplicativo estático. O primeiro provisiona os recursos subjacentes do Azure que compõem seu aplicativo. O segundo é um fluxo de trabalho do GitHub Actions que cria e publica seu aplicativo.

Antes de navegar até o novo site estático, primeiro a compilação de implantação deve concluir a execução.

A janela de visão geral de Aplicativos Web Estáticos exibe uma série de links que ajudam você a interagir com seu aplicativo Web.

:::image type="content" source="media/getting-started/overview-window.png" alt-text="Janela de visão geral":::

1. Clicar na faixa que diz "Clique aqui para verificar o status das suas execuções do GitHub Actions" leva você para o GitHub Actions em execução no repositório. Quando você verificar se o trabalho de implantação foi concluído, poderá navegar para seu site por meio da URL gerada.

2. Depois que o fluxo de trabalho do GitHub Actions for concluído, você poderá clicar no link da _URL_ para abrir o site na nova guia.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser continuar usando esse aplicativo, poderá excluir a instância do Aplicativo Web Estático do Azure com as seguintes etapas:

1. Abra o [portal do Azure](https://portal.azure.com)
1. Pesquise **my-first-web-static-app** na barra de pesquisa superior.
1. Clique no nome do aplicativo.
1. Clique no botão **Excluir**
1. Clique em **Sim** para confirmar a ação

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar uma API](add-api.md)
