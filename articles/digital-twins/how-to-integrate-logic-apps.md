---
title: Integrar aos Aplicativos Lógicos
titleSuffix: Azure Digital Twins
description: Veja como conectar aplicativos lógicos ao Azure digital gêmeos usando um conector personalizado
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 2fc2db54217756ba0f4f7d643b1bc12ad2668209
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88848700"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>Integrar com aplicativos lógicos usando um conector personalizado

O [aplicativo lógico do Azure](../logic-apps/logic-apps-overview.md) é um serviço de nuvem que ajuda a automatizar fluxos de trabalho entre aplicativos e serviços. Ao conectar aplicativos lógicos às APIs do gêmeos digital do Azure, você pode criar fluxos automatizados em todo o gêmeos digital do Azure e seus dados.

Atualmente, o Azure digital gêmeos não tem um conector certificado (pré-compilado) para aplicativos lógicos. Em vez disso, o processo atual para usar aplicativos lógicos com o Azure digital gêmeos é criar um [**conector de aplicativos lógicos personalizados**](../logic-apps/custom-connector-overview.md), usando um [arquivo Swagger personalizado do Azure digital gêmeos](https://github.com/Azure-Samples/digital-twins-custom-swaggers/blob/main/LogicApps/preview/2020-05-31-preview/digitaltwins.json) que foi modificado para funcionar com aplicativos lógicos.

Neste artigo, você usará o [portal do Azure](https://portal.azure.com) para **criar um conector personalizado** que pode ser usado para conectar aplicativos lógicos a uma instância do gêmeos digital do Azure. Em seguida, você **criará um aplicativo lógico** que usa essa conexão para um cenário de exemplo, no qual os eventos disparados por um temporizador atualizarão automaticamente uma cópia em sua instância do gêmeos digital do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, **crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** antes de começar.

Entre no [portal do Azure](https://portal.azure.com) com esta conta.

### <a name="set-up-azure-digital-twins-instance"></a>Configurar a instância do gêmeos digital do Azure

Para conectar uma instância do gêmeos digital do Azure a aplicativos lógicos neste artigo, você precisará ter a **instância do gêmeos digital do Azure** já configurada. 

Se você precisar configurar uma nova instância agora, a maneira mais simples de fazer isso é executar um exemplo de script de implantação automatizado. Siga as instruções em [*como: configurar uma instância e autenticação (com script)*](how-to-set-up-instance-scripted.md) para configurar uma nova instância e o registro de aplicativo do Azure ad necessário. As instruções também contêm etapas para confirmar se você concluiu cada etapa com êxito e está pronto para passar a usar sua nova instância.

Neste tutorial, você precisará dos valores a seguir quando configurar sua instância. Se você precisar reunir esses valores novamente, use os links abaixo para as seções correspondentes no artigo de instalação para localizá-los no [portal do Azure](https://portal.azure.com).
* A instância de Gêmeos Digitais do Azure **_nome do host_** ([localizar no portal](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Registro de aplicativo do Azure AD **_ID do aplicativo (cliente)_** ([localizar no portal](how-to-set-up-instance-portal.md#collect-important-values))
* O registro de aplicativo do Azure AD **_ID do diretório (locatário)_** ([localizar no portal](how-to-set-up-instance-portal.md#collect-important-values))

### <a name="get-app-registration-client-secret"></a>Obter segredo do cliente de registro de aplicativo

Você também precisará criar um **_segredo do cliente_** para o registro do aplicativo do Azure AD. Para fazer isso, navegue até a página de [registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) no portal do Azure (você pode usar este link ou procurá-lo na barra de pesquisa do Portal). Selecione seu registro na lista para abrir seus detalhes. 

Clique em *certificados e segredos* no menu do registro e selecione *+ novo segredo do cliente*.

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Exibição do portal de um registro de aplicativo do Azure AD. Há um realce sobre certificados e segredos no menu de recursos e um realce na página ao nosso novo segredo do cliente":::

Insira os valores que você deseja para descrição e expirar e clique em *Adicionar*.
O segredo será adicionado à lista de segredos do cliente na página *certificados e segredos* . Anote seu valor para usar posteriormente (você também pode copiá-lo para a área de transferência com o ícone de cópia).

### <a name="add-a-digital-twin"></a>Adicionar uma teledigital

Este artigo usa aplicativos lógicos para atualizar uma cópia em uma instância de gêmeos digital do Azure. Para continuar, você deve adicionar pelo menos um cópia em sua instância. 

Você pode adicionar gêmeos usando as [APIs do DigitalTwins](how-to-use-apis-sdks.md), o [SDK do .net (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)ou a [CLI do Azure digital gêmeos](how-to-use-cli.md). Para obter etapas detalhadas sobre como criar gêmeos usando esses métodos, consulte [*como gerenciar o digital gêmeos*](how-to-manage-twin.md).

Você precisará da **_ID_** de cópia de um matreme em sua instância que você criou.

## <a name="create-custom-logic-apps-connector"></a>Criar conector de aplicativos lógicos personalizados

Nesta etapa, você criará um [conector de aplicativos lógicos personalizados](../logic-apps/custom-connector-overview.md) para as APIs do Azure digital gêmeos. Depois de fazer isso, você poderá conectar o gêmeos digital do Azure ao criar um aplicativo lógico na próxima seção.

Navegue até a página do [conector personalizado dos aplicativos lógicos](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) na portal do Azure (você pode usar este link ou pesquisá-lo na barra de pesquisa do Portal). Clique em *+ Adicionar*.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="A página ' conector personalizado dos aplicativos lógicos ' no portal do Azure. Realçar ao contrário do botão ' Adicionar '":::

Na página *criar conector personalizado dos aplicativos lógicos* a seguir, selecione sua assinatura e o grupo de recursos e um nome e um local de implantação para o novo conector. Pressione *examinar + criar*. Isso levará você para a guia *revisar + criar* , na qual você pode clicar em *criar* na parte inferior para criar seu recurso.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="A guia ' revisar + criar ' da página ' criar conector personalizado de aplicativos lógicos ' no portal do Azure. Realçar ao contrário do botão ' criar '":::

Você será levado para a página de implantação do conector. Quando terminar a implantação, pressione o botão *ir para recurso* para exibir os detalhes do conector no Portal.

### <a name="configure-connector-for-azure-digital-twins"></a>Configurar o conector para o gêmeos digital do Azure

Em seguida, você configurará o conector que criou para alcançar o gêmeos digital do Azure.

Primeiro, baixe um Swagger personalizado do Azure digital gêmeos que foi modificado para funcionar com aplicativos lógicos. Baixe *digitaltwins.js* por meio [deste link](https://github.com/Azure-Samples/digital-twins-custom-swaggers/blob/main/LogicApps/preview/2020-05-31-preview/digitaltwins.json).

Em seguida, na página Visão geral do conector na portal do Azure, clique em *Editar*.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="A página ' visão geral ' do conector criado na etapa anterior. Realçar ao contrário do botão ' Editar '":::

Na página *Editar conector personalizado dos aplicativos lógicos* a seguir, configure estas informações:
* **Conectores personalizados**
    - Ponto de extremidade de API: REST (Mantenha o padrão)
    - Modo de importação: arquivo OpenAPI (Mantenha o padrão)
    - Arquivo: esse será o arquivo Swagger personalizado que você baixou anteriormente. Clique em *importar*, localize o arquivo em seu computador e clique em *abrir*.
* **Informações gerais**
    - Ícone, cor do plano de fundo do ícone, descrição: Preencha quaisquer valores que você desejar.
    - Esquema: HTTPS (Mantenha o padrão)
    - Host: o *nome do host* da instância de gêmeos digital do Azure.
    - URL base:/(Mantenha o padrão)

Em seguida, clique no botão *segurança* na parte inferior da janela para continuar na próxima etapa de configuração.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Captura de tela da parte inferior da página ' Editar conector personalizado de aplicativos lógicos '. Realce o botão para continuar a segurança":::

Na etapa segurança, clique em *Editar* e configure essas informações:
* **Tipo de autenticação**: OAuth 2,0
* **OAuth 2,0**:
    - Provedor de identidade: Azure Active Directory
    - ID do cliente: a *ID do aplicativo (cliente)* para o registro do aplicativo do Azure AD
    - Segredo do cliente: o *segredo do cliente* que você criou em [*pré-requisitos*](#prerequisites) para o registro do aplicativo do Azure AD
    - URL de logon: https://login.windows.net (Mantenha o padrão)
    - ID do locatário: a *ID do diretório (locatário)* para o registro do aplicativo do Azure AD
    - URL do recurso: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - Escopo: Directory. AccessAsUser. All
    - URL de redirecionamento: (Mantenha o padrão por enquanto)

Observe que o campo URL de redirecionamento diz *salvar o conector personalizado para gerar a URL de redirecionamento*. Faça isso agora pressionando o *conector de atualização* na parte superior do painel para confirmar as configurações do conector.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Captura de tela da parte superior da página ' Editar conector personalizado de aplicativos lógicos '. Realce ao contrário do botão ' atualizar conector '":::

<!-- Success message? didn't see one -->

Volte para o campo URL de redirecionamento e copie o valor que foi gerado. Você o usará na próxima etapa.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="O campo URL de redirecionamento na página ' Editar conector personalizado de aplicativos lógicos ' agora tem um valor de ' https://logic-apis-westus2.consent.azure-apim.net/redirect '. O botão para copiar o valor é realçado.":::

Essas são todas as informações necessárias para criar seu conector (não é necessário continuar a segurança passada para a etapa de definição). Você pode fechar o painel do *conector personalizado para editar aplicativos lógicos* .

>[!NOTE]
>De volta à página de visão geral do conector onde você pressionou *originalmente, observe que pressionar* *Editar* novamente reiniciará todo o processo de inserção de suas opções de configuração. Ele não preencherá seus valores desde a última vez que você passou por ele, portanto, se você quiser salvar uma configuração atualizada com qualquer valor alterado, deverá inserir novamente todos os outros valores e evitar que eles sejam substituídos pelos padrões.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Conceder permissões de conector no aplicativo do Azure AD

Em seguida, use o valor da *URL de redirecionamento* do conector personalizado que você copiou na última etapa para conceder as permissões do conector no registro do aplicativo do Azure AD.

Navegue até a página [registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) no portal do Azure e selecione seu registro na lista. 

Em *autenticação* no menu do registro, adicione um URI.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="A página de autenticação para o registro do aplicativo no portal do Azure. Autenticação no menu é realçado e, na página, o botão adicionar um URI é realçado."::: 

Insira a *URL de redirecionamento* do conector personalizado no novo campo e clique no ícone *salvar* .

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="A página de autenticação para o registro do aplicativo no portal do Azure. A nova URL de redirecionamento é realçada e o botão ' salvar ' da página.":::

Agora você concluiu a configuração de um conector personalizado que pode acessar as APIs do gêmeos digital do Azure. 

## <a name="create-logic-app"></a>Criar aplicativo lógico

Em seguida, você criará um aplicativo lógico que usará seu novo conector para automatizar as atualizações do Azure digital gêmeos.

Navegue até a página [aplicativos lógicos](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Logic%2Fworkflows) na portal do Azure (você pode usar este link ou procurá-lo na barra de pesquisa do Portal). Clique em *criar aplicativo lógico*.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="A página ' aplicativos lógicos ' no portal do Azure. Realce ao contrário do botão ' criar aplicativo lógico '":::

Na página do *aplicativo lógico* a seguir, selecione sua assinatura e grupo de recursos, e um nome e um local de implantação para seu novo aplicativo lógico. Pressione *examinar + criar*. Isso levará você para a guia *revisar + criar* , na qual você pode clicar em *criar* na parte inferior para criar seu recurso.

Você será levado para a página de implantação do aplicativo lógico. Quando terminar a implantação, pressione o botão *ir para recurso* para continuar com o designer de *aplicativos lógicos*, no qual você preencherá a lógica do fluxo de trabalho.

### <a name="design-workflow"></a>Fluxo de trabalho de design

No *Designer de aplicativos lógicos*, em *Iniciar com um gatilho comum*, selecione _**recorrência**_.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="A página ' designer de aplicativos lógicos ' no portal do Azure. Realce ao contrário do gatilho comum ' recorrência '":::

Na página *Designer de aplicativos lógicos* a seguir, altere a frequência de **recorrência** para *segundo*, para que o evento seja disparado a cada 3 segundos. Isso facilitará a visualização dos resultados mais tarde, sem a necessidade de esperar muito tempo.

Pressione *+ nova etapa*.

Isso abrirá uma caixa *escolher uma ação* . Alterne para a guia *personalizado* . Você deve ver seu conector personalizado do mais cedo na caixa superior.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Criar um fluxo no designer de aplicativos lógicos no portal do Azure. Na caixa ' escolher uma ação ', a guia ' personalizado ' é selecionada. O conector personalizado do usuário do anterior é mostrado na caixa, com um realce ao seu respeito.":::

Selecione-o para exibir a lista de APIs contidas nesse conector. Use a barra de pesquisa ou role a lista para selecionar **DigitalTwins_Add**. (Essa é a API usada neste artigo, mas você também pode selecionar qualquer outra API como uma opção válida para uma conexão de aplicativos lógicos).

Talvez seja solicitado que você entre com suas credenciais do Azure para se conectar ao conector. Se você receber uma caixa de diálogo de *permissões solicitadas* , siga os prompts para conceder consentimento para seu aplicativo e aceitar.

Na caixa novo *DigitalTwinsAdd* , preencha os campos da seguinte maneira:
* ID: Preencha a *ID* do cópia do cópia digital em sua instância que você deseja que o aplicativo lógico atualize.
* Item-1: Este campo é onde você inserirá o corpo que a solicitação de API escolhida requer. Para *DigitalTwinsUpdate*, esse corpo está na forma de código de patch JSON. Para obter mais informações sobre como estruturar um patch JSON para atualizar seu número de atualizações, consulte a seção [atualizar uma atualização digital](how-to-manage-twin.md#update-a-digital-twin) de informações de *como: gerenciar gêmeos digitais*.
* versão da API: na visualização pública atual, esse valor é *2020-05-31-Preview*

Clique em *salvar* no designer de aplicativos lógicos.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Concluída a exibição do aplicativo no conector do aplicativo lógico. A caixa DigitalTwinsAdd é preenchida com os valores descritos acima, incluindo um exemplo de corpo de patch JSON. O botão ' salvar ' da janela é realçado.":::

## <a name="query-twin-to-see-the-update"></a>Consultar o ' Atualizar ' para ver a atualização

Agora que seu aplicativo lógico foi criado, o evento de atualização do matremeing que você definiu no designer de aplicativos lógicos deve ocorrer em uma recorrência de cada três segundos. Isso significa que, após três segundos, você deve ser capaz de consultar seus valores de atualização e ver os novos valors de patches refletidos.

Você pode consultar seu matreme por meio do método de escolha (como um [aplicativo cliente personalizado](tutorial-command-line-app.md), o [aplicativo de exemplo do Azure digital gêmeos Explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/), [SDKs e APIs](how-to-use-apis-sdks.md)ou a [CLI](how-to-use-cli.md)). 

Para obter mais informações sobre como consultar sua instância do gêmeos digital do Azure, consulte [*como consultar o grafo de entrelaçamento*](how-to-query-graph.md).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um aplicativo lógico que atualiza regularmente um cópia em sua instância do gêmeos digital do Azure com um patch que você forneceu. Você pode experimentar a seleção de outras APIs no conector personalizado para criar aplicativos lógicos para uma variedade de ações em sua instância.

Para ler mais sobre as operações de APIs disponíveis e os detalhes necessários, visite [*como usar as APIs e os SDKs do Azure digital gêmeos*](how-to-use-apis-sdks.md).
