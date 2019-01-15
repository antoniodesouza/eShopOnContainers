# Aplicação de referência baseada em arquitetura e recipientes de microsserviços ( estado BETA - compatível com ambientes Visual Studio 2017 e CLI)
Exemplo de aplicativo de referência .NET Core, desenvolvido pela Microsoft, baseado em uma arquitetura de microsserviços simplificada e contêineres Docker.

## ANOTAÇÕES IMPORTANTES!
**Você pode usar a versão mais recente do Visual Studio ou simplesmente o Docker CLI e o .NET CLI para Windows, Mac e Linux**. 

**Nota para Pedidos de Pull (PRs)**: Aceitamos pedidos de pull da comunidade. Ao fazê-lo, por favor, faça-o no **DEV branch** que é o ramo consolidado do trabalho em andamento. Não o solicite no ramo principal, se possível.

**NOTÍCIAS / ANÚNCIOS**
Você quer estar atualizado sobre as diretrizes de arquitetura .NET e aplicativos de referência como o eShopOnContainers? -> Assine por "WATCHING" este novo repositório do GitHub: https://github.com/dotnet-architecture/News 

## Atualizado para o .NET Core 2.2 "wave" de tecnologias
O eShopOnContainers é atualizado para o .NET Core 2.x (atualmente atualizado para 2.2) "wave" de tecnologias. Não apenas compilação, mas também novo código recomendado no EF Core, ASP.NET Core e outras novas versões relacionadas.

Os arquivos **dockerfiles** da solução também foram atualizados e agora suportam o [**Docker Multi-Stage**](https://blogs.msdn.microsoft.com/stevelasker/2017/09/11/net-and-multistage-dockerfiles/) desde meados de dezembro de 2017.

>**POR FAVOR** Leia nosso [branch guide](./branch-guide.md) para saber sobre nossa política de branching

> ### AVISO LEGAL
> **IMPORTANTE:** O estado atual deste aplicativo de amostra é **BETA** , porque estamos constantemente evoluindo para tecnologias recém-lançadas. Portanto, muitas áreas podem ser melhoradas e alteradas de maneira significativa durante a recriação do código atual e a implementação de novos recursos. Feedback com melhorias e pedidos de pull da comunidade serão muito apreciados e aceitos
>
> Este aplicativo de referência propõe uma implementação simplificada da arquitetura orientada a microserviço para apresentar tecnologias como o .NET Core com contêineres Docker por meio de um aplicativo abrangente. O domínio escolhido é eShop / eCommerce, mas simplesmente porque é um domínio bem conhecido pela maioria das pessoas / desenvolvedores. No entanto, esse aplicativo de exemplo não deve ser considerado como um "modelo de referência de comércio eletrônico". O domínio de negócios implementado pode não ser ideal do ponto de vista de um negócio de comércio eletrônico. Não está tentando resolver todos os problemas em um sistema distribuído grande, escalável e de missão crítica. É apenas um bootstrap para que os desenvolvedores possam começar facilmente no mundo dos contêineres e microsserviços do Docker com o .NET Core.
> <p>Por exemplo, a próxima etapa depois de executar a solução no PC local e entender o desenvolvimento de contêineres e microsserviços do Docker com o .NET Core é selecionar um orquestrador / cluster de microsserviço como o Kubernetes no Azure (AKS) ou o Azure Service Fabric, ambos os ambientes testados e suportado por esta solução.
> Etapas adicionais seriam mover seus bancos de dados para serviços de nuvem HA (como o Banco de Dados SQL do Azure) ou alternar seu EventBus para usar o Barramento de Serviço do Azure (em vez do RabbitMQ simples) ou qualquer outro Barramento de Serviço pronto para produção no mercado.

![image](https://user-images.githubusercontent.com/1712635/40397331-059a7ec6-5de7-11e8-8542-a597eca16fef.png)

> Leia o <a href='https://github.com/dotnet/eShopOnContainers/wiki/01.-Roadmap-and-Milestones-for-future-releases'>o roteiro e os marcos planejados para futuras versões do eShopOnContainers</a> no Wiki para obter mais informações sobre possíveis novas implementações e fornecer feedback na <a href='https://github.com/dotnet/eShopOnContainers/issues'>seção ISSUES</a> se quiser ver qualquer cenário específico implementado ou aprimorado. Além disso, sinta-se à vontade para discutir sobre qualquer assunto atual.

### Visão geral da arquitetura 
Esse aplicativo de referência é multi-plataforma no lado do servidor e do cliente, graças aos serviços .NET Core capazes de rodar em containers Linux ou Windows dependendo do seu host Docker, e ao Xamarin para aplicativos móveis rodando em Android, iOS ou Windows / UWP qualquer navegador para os aplicativos da web do cliente. 
A arquitetura propõe uma implementação de arquitetura orientada a microsserviços com múltiplos microsserviços autônomos (cada um possuindo seus próprios dados / db) e implementando diferentes abordagens dentro de cada microsserviço (padrões simples CRUD vs. DDD / CQRS) usando Http como o protocolo de comunicação entre os aplicativos cliente e os microsserviços e oferece suporte à comunicação assíncrona para propagação de atualizações de dados em vários serviços com base em eventos de integração e um barramento de eventos (um media broker de mensagens, para escolher entre o RabbitMQ ou o Barramento de Serviço do Azure,<a href='https://github.com/dotnet/eShopOnContainers/wiki/01.-Roadmap-and-Milestones-for-future-releases'>roteiro</a>.
<p>
<img src="img/eshop_logo.png">
<img src="https://user-images.githubusercontent.com/1712635/38758862-d4b42498-3f27-11e8-8dad-db60b0fa05d3.png">
<p>

> ### Nota importante sobre gateways de API e APIs publicadas
> Desde abril de 2018, introduzimos a implementação do [padrão de Gateway de API](http://microservices.io/patterns/apigateway.html) e [Backend-For-Front-End (BFF) pattern](https://samnewman.io/patterns/architectural/bff/) na arquitetura eShopOnContainers, para que você possa filtrar e publicar APIs e URIs simplificados e aplicar segurança adicional nesse nível enquanto oculta/protege os microsserviços internos para os aplicativos clientes ou consumidores externos. Esses exemplos de Gateways de API em eShopOnContainers são baseados no [Ocelot](https://github.com/ThreeMammals/Ocelot), uma solução leve de Gateway de API do OSS explicada [aqui](http://threemammals.com/ocelot). Os API Gateways implantados são autônomos e podem ser implantados como seus próprios microsserviços / contêineres personalizados, como atualmente é feito em eShopOnContainers, para que você possa testá-lo em um ambiente de desenvolvimento simples com o mecanismo Docker ou implantá-lo em orquestradores como o Kubernetes no AKS ou Service Fabric. 

> Para sua arquitetura pronta para produção, você pode continuar usando o [Ocelot](https://github.com/ThreeMammals/Ocelot) que é simples e fácil de usar e usado na produção por empresas importantes, ou se você precisar de mais funcionalidades e um conjunto muito mais rico de recursos adequados para Gateways APIs comerciais e use o [Azure API Management](https://azure.microsoft.com/en-us/services/api-management/) ou qualquer outro API Gateway comercial, conforme mostrado na imagem a seguir.

<p>
<img src="img/eShopOnContainers-Architecture-With-Azure-API-Management.png">
<p>

> O código de exemplo neste repositório NÃO está usando o Gerenciamento de API do Azure para poder fornecer uma "experiência F5" no Visual Studio (ou CLI) da amostra sem dependências iniciais no Azure. Mas você pode avaliar as alternativas de Gateways de API ao criar para produção.

> ### Arquitetura interna e design dos microsserviços

> Os microsserviços são diferentes em tipo, o que significa diferentes abordagens de padrões internos de arquitetura, dependendo de sua finalidade, conforme mostrado na imagem abaixo.
<p>
<img src="img/eShopOnContainers_Types_Of_Microservices.png">
<p>
<p>

> ### Nota importante sobre servidores/contêineres de banco de dados
> Na configuração atual desta solução para um ambiente de desenvolvimento, os bancos de dados SQL são implantados automaticamente com dados de amostra em um único contêiner do SQL Server (um único contêiner Docker compartilhado para bancos de dados SQL) para que toda a solução esteja ativa e sem dependência de qualquer nuvem ou um servidor específico. Cada banco de dados também poderia ser implantado como um único contêiner Docker, mas você precisaria de mais de 8 GB de RAM atribuídos ao Docker em sua máquina de desenvolvimento para poder executar 3 contêineres do SQL Server Docker em seu host do Docker Linux em "Docker" para ambientes de desenvolvimento Windows "ou" Docker for Mac ".
> <p> Um caso semelhante é definido em relação ao cache Redis sendo executado como um contêiner para o ambiente de desenvolvimento. Ou um banco de dados No-SQL (MongoDB) em execução como um contêiner.
> <p> No entanto, em um ambiente de produção real, recomenda-se ter seus bancos de dados (SQL Server, Redis e banco de dados NO-SQL, nesse caso) em serviços de alta disponibilidade como o Banco de Dados SQL do Azure, Redis como serviço e Azure CosmosDB. em vez disso, o contêiner MongoDB (como ambos os sistemas compartilham o mesmo protocolo de acesso). Se você quiser mudar para uma configuração de produção, só precisará alterar as sequências de conexão depois de configurar os servidores em uma nuvem HA ou no local.

## Documentação e orientação relacionadas
Ao desenvolver este aplicativo de referência, estamos criando um <b>Guia/eBook</b> de referência focado na <b>arquitetura e desenvolvimento de aplicativos .NET baseados em microsserviços e conteinerizados</b> (link de download disponível abaixo) que explica detalhadamente como desenvolver esse tipo de estilo arquitetônico (microsserviços, Docker recipientes, Design dirigido por domínio para determinados microsserviços), além de outros estilos arquiteturais mais simples, como aplicativos monolíticos que também podem ser armazenados como contêineres do Docker.
<p>
Há também eBooks adicionais com foco no ciclo de vida Containers/Docker (DevOps, CI / CD, etc.) com o Microsoft Tools, já publicados, além de um eBook adicional com foco nos Enterprise Apps Patterns com Xamarin.Forms. Você pode baixá-los e começar a rever estes Guias/e-Livros aqui:
<p>

| Architecting & Developing | Containers Lifecycle & CI/CD | App patterns with Xamarin.Forms |
| ------------ | ------------|  ------------|
| <a href='https://aka.ms/microservicesebook'><img src="img/ebook_arch_dev_microservices_containers_cover.png"> </a> | <a href='https://aka.ms/dockerlifecycleebook'> <img src="img/ebook_containers_lifecycle.png"> </a> | <a href='https://aka.ms/xamarinpatternsebook'> <img src="img/xamarin-enterprise-patterns-ebook-cover-small.png"> </a> |
| <sup> <a href='https://aka.ms/microservicesebook'>**Download .PDF** (v2.2 Edition)</a> </sup>  | <sup> <a href='https://aka.ms/dockerlifecycleebook'>**Download** </a>  </sup> | <sup> <a href='https://aka.ms/xamarinpatternsebook'>**Download**  </a>  </sup> |

Download em outros formatos (**eReaders** como **MOBI**, **EPUB**) e outros eBooks em [.NET Architecture center](http://dot.net/architecture).

Envie seu comentário para [dotnet-architecture-ebooks-feedback@service.microsoft.com](dotnet-architecture-ebooks-feedback@service.microsoft.com)

No entanto, incentivamos você a fazer o download e revisar o [Architecting and Developing Microservices eBook](https://aka.ms/microservicesebook)  porque os estilos arquiteturais e padrões de arquitetura e tecnologias explicados no guia estão usando este aplicativo de referência ao explicar muitas implementações de padrão, para que você entenda muito melhor o contexto, design e decisões tomadas na arquitetura atual e projetos internos.


## Visão geral do código do aplicativo
Neste repositório, você pode encontrar um exemplo de aplicativo de referência que o ajudará a entender como implementar um aplicativo baseado na arquitetura de microsserviço usando o <b>.NET Core</b> e o <b>Docker</b>.

O exemplo de domínio ou cenário de negócios é baseado em um eShop ou eCommerce que é implementado como um aplicativo de vários contêineres. Cada contêiner é uma implantação de microsserviço (como o basket-microservice, catálogo-microservice, ordering-microservice e identity-microservice) que é desenvolvido usando o ASP.NET Core sendo executado no .NET Core para que eles possam ser executados em contêineres do Linux e contêineres do Windows. 
A captura de tela abaixo mostra a estrutura da VS Solution para os microservices/containers e aplicativos clientes.


- (*Recomendado quando começar*) Abra o <b>eShopOnContainers-ServicesAndWebApps.sln</b> para obter uma solução contendo apenas os projetos do lado do servidor relacionados aos microservices e aplicativos da web.
- Abra o <b>eShopOnContainers-MobileApps.sln</b> para obter uma solução contendo apenas os projetos de aplicativos móveis do cliente (somente aplicativos móveis Xamarin). Ele funciona de forma independente com base em mocks também.
- Abra o <b>eShopOnContainers.sln</b>  para obter uma solução contendo todos os projetos (todos os aplicativos e serviços do cliente).

<img src="img/vs-solution-structure.png">

Por fim, esses microsserviços são consumidos por vários aplicativos da Web e móveis para clientes, conforme descrito abaixo.
<br>
<b>*MVC Application (ASP.NET Core)*</b>: É um aplicativo MVC onde você pode encontrar cenários interessantes sobre como consumir microsserviços baseados em HTTP a partir de C # em execução no lado do servidor, pois é um típico aplicativo ASP.NET Core MVC. Como é um aplicativo do lado do servidor, o acesso a outros contêineres/microsserviços é feito dentro da rede interna do Docker Host com sua resolução interna de nomes.
<img src="img/eshop-webmvc-app-screenshot.png">
<br>
<b>*SPA (Aplicativo de Página Única)*</b>: Fornecer "funcionalidade de negócios do eShop" semelhante, mas desenvolvida com o Angular, o Typescript e usando um pouco o ASP.NET Core MVC. Essa é outra abordagem para aplicativos da Web do cliente serem usados ​​quando você deseja ter um comportamento de cliente mais moderno que não esteja se comportando com a típica viagem de ida e volta do navegador em cada ação, mas comportando-se como um Aplicativo de Página Única mais semelhante a um experiência de uso de aplicativos de desktop. O consumo dos microsserviços baseados em HTTP é feito a partir do TypeScript/JavaScript no navegador do cliente, de modo que as chamadas do cliente para os microsserviços saem da rede interna do Docker Host (como da sua rede ou até mesmo da Internet).
<img src="img/eshop-webspa-app-screenshot.png">
<br>
<b>*Xamarin Mobile App (para iOS, Android e Windows/UWP)*</b>: É um aplicativo móvel cliente que suporta as plataformas mais comuns de sistemas operacionais móveis (iOS, Android e Windows/UWP). Nesse caso, o consumo dos microsserviços é feito a partir do C #, mas executado nos dispositivos do cliente, portanto, fora da rede interna do Docker Host (como na sua rede ou até mesmo na Internet).

<img src="img/xamarin-mobile-App.png">

## Configurando seu ambiente de desenvolvimento para o eShopOnContainers
### Visual Studio 2017 e baseado em Windows
Esta é a maneira mais simples de começar:
https://github.com/dotnet-architecture/eShopOnContainers/wiki/02.-Setting-eShopOnContainers-in-a-Visual-Studio-2017-environment

### CLI e Windows
Para aqueles que preferem o CLI no Windows, usando o dotnet CLI, o docker CLI e o VS Code para Windows:
https://github.com/dotnet/eShopOnContainers/wiki/03.-Setting-the-eShopOnContainers-solution-up-in-a-Windows-CLI-environment-(dotnet-CLI,-Docker-CLI-and-VS-Code)

### CLI e Mac baseados
Para aqueles que preferem o CLI em um Mac, usando o dotnet CLI, o docker CLI e o VS Code para Mac:
https://github.com/dotnet-architecture/eShopOnContainers/wiki/04.-Setting-eShopOnContainer-solution-up-in-a-Mac,-VS-for-Mac-or-with-CLI-environment--(dotnet-CLI,-Docker-CLI-and-VS-Code)

## Orquestradores: Kubernetes e Service Fabric
Veja no [Wiki](https://github.com/dotnet-architecture/eShopOnContainers/wiki) os posts sobre configuração/instruções sobre como implantar no Kubernetes ou Service Fabric no Azure (embora você possa também implantar em qualquer outra nuvem ou local).

## Enviando feedback e solicitações de pull
Como mencionado, gostaríamos de receber seus comentários, melhorias e ideias. 
Você pode criar novos problemas na seção de problemas, fazer solicitações e/ou enviar emails para **eshop_feedback@service.microsoft.com**

## Questions
[PERGUNTA] Responda +1 se a solução estiver funcionando para você (Por meio do ambiente VS2017 ou CLI):
https://github.com/dotnet/eShopOnContainers/issues/107
