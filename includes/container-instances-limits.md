---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 07/22/2020
ms.author: danlep
ms.openlocfilehash: 6878180ffedfaa53f25d2bdc6db72dcd7dd8b38b
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87384813"
---
| Recurso | Limite |
| --- | :--- |
| Grupos de contêineres do SKU padrão por região por assinatura | 100<sup>1</sup> |
| Grupos de contêineres do SKU dedicados por região por assinatura | 0<sup>1</sup> |
| Número de contêineres por grupo de contêineres | 60 |
| Número de volumes por grupo de contêineres | 20 |
| Núcleos de SKU padrão (CPUs) por região por assinatura | 10<sup>1.2</sup> | 
| Núcleos de SKU padrão (CPUs) para GPU K80 por região por assinatura | 18<sup>1.2</sup> |
| Núcleos de SKU padrão (CPUs) para GPU P100 ou V100 por região por assinatura | 0<sup>1.2</sup> |
| Portas por IP | 5 |
| Tamanho do log de instância de contêiner - instância em execução | 4 MB |
| Tamanho do log de instância de contêiner - instância parada | 16 KB ou 1.000 linhas |
| Criações de contêiner por hora |300<sup>1</sup> |
| Criações de contêiner a cada 5 minutos | 100<sup>1</sup> |
| Exclusões de contêiner por hora | 300<sup>1</sup> |
| Exclusões de contêiner a cada 5 minutos | 100<sup>1</sup> |


<sup>1</sup>Para solicitar um aumento de limite, crie uma [Solicitação de suporte do Azure][azure-support]. Assinaturas gratuitas, incluindo a [Conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) e o [Microsoft Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p/) não se qualificam para aumentos de cota nem de limite. Se tiver uma assinatura gratuita, você poderá [atualizar](../articles/cost-management-billing/manage/upgrade-azure-subscription.md) para uma assinatura com Pagamento Conforme o Uso.<br />
<sup>2</sup>Limite padrão para assinatura com [Pagamento Conforme o Uso](https://azure.microsoft.com/offers/ms-azr-0003p/). O limite pode ser diferente para outros tipos de categoria.<br/>

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
