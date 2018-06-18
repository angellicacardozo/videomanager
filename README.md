# Video Manager

## Objetivo

Modelar a arquitetura de um elemento de interface que execute vídeos, em uma página web. O elemento de interface deve reagir à alguns eventos provocados pelo usuário final, tal como o scroll da página web. Nos casos em que o usuário realize o scroll sobre a página e o elemento de vídeo, o qual ele tenha iniciado uma execução, saia da perspectiva de visão, esse deve ser fixado em algum quadrante da tela.

![Figura 1 : Representação visual da proposta dos elementos de interface](images/fig1.png " Figura 1 : Representação visual da proposta dos elementos de interface")

![Figura 2 : Representação visual da gerência de vídeo em detecção de scroll](images/fig2.png " Figura 2 : Representação visual da gerência de vídeo em detecção de scroll")

## Formalização

Quando trabalhamos com aplicações em JavaScript, devemos ter em mente que o planejamento acerca da arquitetura costuma nos tomar mais tempo do que imaginamos, principalmente quando queremos modelar os itens mais relevantes.

Escolher e definir padrões de projeto nos permite dividir partes de nossa aplicação em pequenos módulos. No entanto, durante a manutenção e evolução de um projeto, devemos nos questionar sobre questões básicas:

- Eu estou evoluindo o código de maneira que seja reutilizável?
- Minhas alterações provocam algum tipo de acoplamento entre módulos ou funcionalidades?
- A minha aplicação é sensível à falhas causadas por novas funcionalidades?
- Meu sistema pode ser testedado em suas pequenas unidades?

Portanto, quando trabalhamos no desenvolvimento e manutenção de aplicações robustas em JavaScript, devemos tem em mente as seguintes qualidades para nosso código: sustentável, extensível, flexível e reutilizável.

## Metodologia

Nesta fase apresentamos alguns detalhes de uma arquitetura para os componentes de interface apresentados anteriormente em **Objetivo**, bem como as trocas de estados durante detecção de scroll.

### Angular

A organização lógica dos componentes nos permite visualizar os elementos potencialmente agrupáveis. Quando trabalhamos com o Angular, por exemplo, esses grupos podem ser reunidos em diretivas.

Primeiro, identificamos os elementos que são utilizados repetidas vezes em diferentes áreas. Isso significa que é desejável ter diversas instâncias desses elementos disponíveis em qualquer área do nosso sistema.

Ao inspeciornamos visualmente as figuras 1 e 2, podemos notar que as informações dos vídeos se repetem nas diferentes áreas do site. Possuímos uma lista para vídeos mais assistidos como também para os vídeos mais recentes.

A figura 2 mostra um outro elemento, dessa vez único na tela, que é o modal de um vídeo em execução cuja a finalidade é manter-se fixo em algum quadrante da tela, permitindo que o usuário *gerencie* o vídeo que se encontrava em execução, antes do evento de scroll.

Logo, nossos candidatos são:
- Componente individual para o vídeo com título, descrição e preview de conteúdo
- Componente de gerência de vídeo para vídeos fora da área de visão do usuário
- Componentes para agrupar vídeos em diferentes listas

Como o objetivo do nosso trabalho é exibir vídeos e gerenciar a execução daqueles que fogem do campo de visão de um usuário, vamos focar nos dois primeiros componentes.

Com base na orientação [(Style Y163)](https://github.com/johnpapa/angular-styleguide/tree/master/a1#style-y163) , propomos o módulo *videos* para agrupar os serviços, controladores e visões da *feature* de vídeos das figuras 1 e 2.

Em seguida, com base na orientação [(Style Y164)](https://github.com/johnpapa/angular-styleguide/tree/master/a1#style-y164) propomos uma diretiva global para a detecção dos eventos associados ao scroll, uma vez que este será um serviço comum aos diferentes itens de vídeos.

O diretório da aplicação deve ter uma estrutura similar a abaixo:

```
utils
├── directives
│   ├── scroll-detect.directive.js
│
videos
├── directives
│   ├── video-item.directive.js
│   ├── video-manager.directive.js
├── views
│   ├── template-video-item.html
│   ├── template-video-manager.html
```

**scroll-detect.directive.js** : diretiva que agrega eventos associados ao scroll de tela em um elemento. Com ela, é possível detectar se um elemento está visível ou não para o usuário.

**video-item.directive.js** : representação mais simples de um elemento de vídeo. Fornece em uma view dados como o título, descritção e preview.

**video-manager.directive.js** : diretiva responsável por reunir os elementos video-item em uma tela e coordenar o estado de visão daquele que estiver em execução de acordo com a detecção do scroll.

**template-video-item.html** : estrutura html para suporte à diretiva **video-item.directive.js** na visualização do item de vídeo.

**template-video-manager.html** : estrutura html para suporte à diretiva **video-manager.directive.js** na visualização do modal de vídeo, de acordo com deteção do scroll.

![](images/diagrama_01.png)
###### Figura 3 - Visão geral dos componentes envolvidos na proposta com Angular

**Video Manager Directive**

```
/**
 ** video-manager.directive.js
 **
 ** @description diretiva responsável por reunir os elementos video-item em uma
 tela e coordenar o estado de visão daquele que estiver em execução de acordo
 com a detecção do scroll.
 **/
(function() {
    'use strict';

    angular
        .module('app.videos')
        .directive('videoManager', videoManager);

    function videoManager() {

        var ddo = {
            scope: {
                /// ...
            },
            restrict: 'E',
            controller: VideoManagerController,
            replace: true,
            templateUrl: 'app/videos/views/template-video-manager.html'
        }

        return ddo;

        function VideoManagerController($scope, $attrs) {
            // recebe os eventos referentes aos videos de sairam do campo de visao do usuario
            // os exibe em view de gerenciamento
        }

        /// ... more code
    }
})();
```

**Video Item Directive**

```
/**
 ** video - item.directive.js
 **
 ** @description diretiva que agrega eventos associados ao scroll de tela em um elemento.
 Com ela, é possível detectar se um elemento está visível ou não para o usuário
 **/
(function() {
    'use strict';

    angular
        .module('app.videos')
        .directive('videoItem', videoItem);

    function videoItem() {

        var ddo = {
            scope: {
                video: '=video'
            },
            restrict: 'E',
            controller: VideoItemController,
            replace: true,
            templateUrl: 'app/videos/views/template-video-item.html'
        }

        return ddo;

        function VideoItemController($scope, $attrs) {

            // controla eventos de play / pause / stop

        }

        function _onDetectedScroll(out_of_view) {
            // emite evento ao gerenciador de video para que este exiba o vídeo fixado na tela
        }

        /// ... more code
    }
})();
```

**Scroll detect directive**

```
/**
 ** scroll-detect.directive.js
 **
 ** @description diretiva que agrega eventos associados ao scroll de tela em um elemento. Com ela, é possível detectar se um elemento está visível ou não para o usuário
 **/
(function() {
    'use strict';

    angular
        .module('app.utils')
        .directive('scrollDetect', scrollDetect);

    function scrollDetect() {

        var ddo = {
            scope: {
              onScroll: 'onScroll'
            },
            restrict: 'A',
            link: scrollDetectLink
        }

        return ddo;

        function scrollDetectLink(scope, element, attr) {
            element.addEventListener('scroll', function(e) {
                /// executa a chamada que o elemento que recebeu a diretiva atribuiu
                $scope.onScroll(reachTop, reachBottom);
            });
        }
    }
})();
```
### Abordagem em REACTJS
###### Sugestão
* ReactJS + Redux
    * Utilização da biblioteca ReactJS em conjunto com Redux para manutenção de estados;
    *  Utilização de Redux Saga, uma biblioteca (redux middleware), que visa cuidar, por exemplo, dos tratamentos assíncronos da aplicação, entre outras coisas. Com isso podemos obter um produto final que fique fácil de administrar dar manutenção, mais eficiente em sua execução, praticidade nos testes e melhor tratamento de erros;

![](https://cdn-images-1.medium.com/max/800/1*BcmtHcMHN6PT7IniIWniHg.png)
###### Figura 4 - Fluxo de dados React+Redux
<br />

### Exemplo de Arquitetura (Video Manager)
```
videoManager
├── Components
│   ├── VideoManager.jsx
│   └── VideoManager.test.jsx
├── Elements
│   ├── VideoIcon.jsx
│   └── VideoIcon.test.jsx
├── VideoContainer.jsx
├── VideoContainer.test.jsx
└── module
    ├── index.js
    ├── routes.js
    ├── selectors.js
    └── videos.module.test.js
```
**Module**: Módulo - (index.js) - contém a maior parte do código Redux.
Seletores são simples funções "get" usadas para selecionar dados do estado.
Rotas contém as configurações dos módulos React-Router que são consumidas no alto nível da aplicação.

**Container**: Classe com maior conhecimento, responsável por injetar todas as "props" do modulo, incluindo estado e criadores de ações. Qualquer lógica intermediária, como filtros, lógicas dos 'event handlers', ou lógica de construção, é feita nesse método.

**Components**: Classes com pouco conhecimento ou funções com estados imutáveis que renderizam partes específicas da UI, baseado em props vindo do container.

**Elements**: Funções com estados imutáveis e que são utilizadas para manter a apresentação sem redundâncias (DRY).

**Tests**: Unidade de teste Jest que pertence a cada módulo.

### Exemplo de Arquitetura (Single Video Widget)
```
Widgets
   ├── Videos
   │   ├── index.js
   │   ├── components
   │   ├── containers
   └── └── modules

```
