---
title: Noções básicas sobre o status do aplicativo no Azure Spring Cloud
description: Aprenda as categorias de status do aplicativo no Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: e3ef202a1a98b8193b55bcc4c2cb616d4a2000d8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037756"
---
# <a name="understanding-app-status-in-azure-spring-cloud"></a>Noções básicas sobre o status do aplicativo no Azure Spring Cloud

A interface do usuário do Azure Spring Cloud fornece informações sobre o status dos aplicativos em execução.  Há uma opção de **aplicativos** para cada grupo de recursos em uma assinatura que exibe o status geral dos tipos de aplicativos.  Para cada tipo de aplicativo, há uma exibição das **instâncias do aplicativo**.

## <a name="apps-status"></a>Status dos aplicativos
Para exibir o status geral de um tipo de aplicativo, selecione **aplicativos** no painel de navegação esquerdo de um grupo de recursos. O resultado exibe o status do aplicativo implantado:

* O **status de provisionamento** mostra o estado de provisionamento da implantação
* A **execução da instância** mostra quantas instâncias do aplicativo estão em execução/quantas instâncias do aplicativo são desejadas. Se o aplicativo for interrompido, essa coluna mostrará *parado*.
* A **instância registrada** mostra quantas instâncias de aplicativo são registradas para Eureka/quantas instância de aplicativo são desejadas. Se o aplicativo for interrompido, essa coluna mostrará *parado*.


 ![Status dos aplicativos](media/spring-cloud-concept-app-status/apps-ui-status.png)

**O status da implantação é relatado como um dos seguintes valores:**

| Enumeração | Definição |
|:--:|:----------------:|
| Em execução | A implantação deve estar em execução. |
| Parado | A implantação deve ser interrompida. |

**O estado de provisionamento é acessível somente da CLI.  Ele é relatado como um dos seguintes valores:**

| Enumeração | Definição |
|:--:|:----------------:|
| Criando | O recurso está sendo criado. |
| Atualizar | O recurso está sendo atualizado. |
| Com sucesso | Recursos fornecidos com êxito e implantação do binário. |
| Com falha | Falha ao obter a meta *bem-sucedida* . |
| Excluindo | O recurso está sendo excluído. Isso impede a operação e o recurso não está disponível nesse status. |

## <a name="app-instances-status"></a>Status de instâncias do aplicativo

Para exibir o status de uma instância específica de um aplicativo implantado, clique no **nome** do aplicativo na interface do usuário de **aplicativos** . Os resultados serão exibidos:
* **Status**: se a instância está em execução ou seu estado
* **DiscoveryStatus**: o status registrado da instância do aplicativo no servidor Eureka

 ![Status de instâncias do aplicativo](media/spring-cloud-concept-app-status/apps-ui-instance-status.png)

**O status da instância é relatado como um dos seguintes valores:**

| Enumeração | Definição |
|:--:|:----------------:|
| Iniciando | O binário é implantado com êxito na instância especificada. A inicialização da instância do arquivo JAR pode falhar porque o jar não pode ser executado corretamente. |
| Em execução | A instância funciona. |
| Com falha | A instância do aplicativo não pôde iniciar o binário do usuário após várias tentativas. |
| Final | A instância do aplicativo está sendo desligada. |

**O status de descoberta da instância é relatado como um dos seguintes valores:**

| Enumeração | Definição |
|:--:|:----------------:|
| UP | A instância do aplicativo está registrada em Eureka e pronta para receber tráfego |
| OUT_OF_SERVICE | A instância do aplicativo é registrada em Eureka e pode receber tráfego. Mas desliga o tráfego intencionalmente. |
| PARA BAIXO | A instância do aplicativo não está registrada no Eureka ou está registrada, mas não pode receber tráfego. |


## <a name="see-also"></a>Confira também
* [Preparar um aplicativo Spring Java para implantação no Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md)