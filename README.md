<div align="center">
  <img
    src="assets/banner.png"
    alt="Banner do Onboarding Mobile da Asimov Jr."
    width="100%"
  />
</div>

# Onboarding Mobile

Este documento tem como objetivo apresentar aos membros recém-efetivados o funcionamento da área de Mobile da Asimov Jr., seus padrões técnicos e a forma como os projetos são desenvolvidos.

Além de apoiar a integração de novos membros, este material também servirá como referência técnica para toda a área, reunindo decisões, convenções e processos adotados pelo time de Mobile.

Inicialmente, o guia abordará:

- o funcionamento da área de Mobile;
- a stack utilizada nos projetos;
- a arquitetura adotada;
- as responsabilidades de cada camada da aplicação;
- os motivos que levaram a área a utilizar essa arquitetura.

Futuramente, o documento poderá incluir outros padrões da área, como requisitos para pull requests, critérios de revisão e Definition of Done.

## Visão geral da área de Mobile

A área de Mobile da Asimov Jr. é responsável pelo desenvolvimento de aplicações móveis sob medida, de acordo com as necessidades apresentadas por cada cliente.

Atualmente, os projetos da área são desenvolvidos exclusivamente com Flutter, permitindo a criação de aplicações multiplataforma a partir de uma única base de código.

Além da implementação das interfaces, os desenvolvedores também são responsáveis pela integração das funcionalidades com os serviços de backend utilizados no projeto.

---

## Tecnologias utilizadas

### Tecnologias principais

A área de Mobile utiliza atualmente:

- **Flutter:** framework utilizado para o desenvolvimento das aplicações móveis;
- **Dart:** linguagem utilizada pelo Flutter;
- **Riverpod:** solução adotada para gerenciamento de estado e injeção de dependências;
- **Firebase:** principal conjunto de serviços utilizado para autenticação, armazenamento de dados e outras funcionalidades de backend;
- **MVVM:** arquitetura adotada para organizar as responsabilidades e separar a interface, o estado da aplicação e o acesso aos dados.

### Ferramentas de apoio

Além das tecnologias principais, a área utiliza:

- **GitHub:** versionamento de código, armazenamento dos repositórios e colaboração entre os desenvolvedores;
- **Figma:** consulta aos protótipos e especificações visuais produzidos pela equipe responsável pela prototipagem.

A responsabilidade pela criação dos protótipos não pertence à área de Mobile. Os desenvolvedores utilizam o material produzido no Figma como referência para implementar as interfaces da aplicação.

---

## Como os projetos são desenvolvidos

O desenvolvimento de uma aplicação Mobile normalmente começa após a equipe de prototipagem concluir uma parcela suficiente das telas no Figma.

Isso permite que a equipe de desenvolvimento analise o projeto, identifique padrões visuais e comece a planejar a implementação da aplicação.

Antes da divisão das tarefas, são observados elementos que aparecem repetidamente em diferentes telas, como:

- botões;
- campos de texto;
- cabeçalhos;
- cards;
- barras de navegação;
- componentes de carregamento;
- elementos de feedback ao usuário.

Esses elementos podem ser transformados em widgets reutilizáveis, evitando duplicação de código e mantendo maior consistência visual entre as diferentes partes do aplicativo.

### Divisão por funcionalidades

A aplicação é dividida em funcionalidades ou domínios, como:

```text
Autenticação
Perfil do usuário
Página inicial
Agendamentos
Pagamentos
Configurações
```

Cada desenvolvedor fica responsável por uma ou mais funcionalidades do aplicativo.

Dentro da funcionalidade atribuída, o desenvolvedor normalmente realiza:

1. a implementação das interfaces;
2. a criação dos componentes necessários;
3. o gerenciamento do estado;
4. a integração com o Firebase ou outro serviço utilizado;
5. o tratamento dos dados;
6. a integração da funcionalidade com o restante da aplicação.

Geralmente, a implementação começa pela interface. Depois que as telas e seus comportamentos principais estão estruturados, o desenvolvedor realiza a integração com os serviços necessários.

Sempre que possível, o mesmo desenvolvedor que implementou a interface também realiza a integração dos dados daquela funcionalidade. Dessa forma, ele mantém maior domínio sobre o fluxo completo que está desenvolvendo.

Ao final, as diferentes funcionalidades são integradas para formar a aplicação completa.

---

## Papéis dentro de um projeto

### Gerente de projeto

O gerente de projeto é responsável por coordenar a execução do projeto.

Entre suas responsabilidades estão:

- organizar e distribuir as tarefas;
- acompanhar o andamento das funcionalidades;
- esclarecer os requisitos;
- alinhar prioridades;
- acompanhar prazos;
- apoiar os desenvolvedores;
- garantir a integração entre as diferentes partes da aplicação.

### Desenvolvedor

O desenvolvedor é responsável pela implementação das funcionalidades atribuídas a ele.

Isso pode envolver:

- implementação de interfaces;
- criação de widgets reutilizáveis;
- gerenciamento de estado;
- integração com o Firebase;
- tratamento de dados;
- correção de erros;
- integração com outras funcionalidades;
- documentação das decisões relevantes.

### Gerente da área de Mobile

O gerente da área possui uma responsabilidade mais ampla do que o gerenciamento de um único projeto.

Seu papel envolve:

- acompanhar a situação geral da área;
- apoiar os gerentes de projeto;
- promover a padronização técnica;
- definir e manter os processos da área;
- acompanhar o desenvolvimento dos membros;
- identificar necessidades de capacitação;
- preservar e compartilhar o conhecimento técnico da área.

---

## O que se espera de um membro efetivado

A efetivação não representa o fim do processo de aprendizagem. Ela marca o início da participação do membro em projetos reais da empresa.

Espera-se que o membro recém-efetivado:

- demonstre interesse pelos projetos;
- esteja disposto a aprender;
- seja proativo;
- comunique suas dificuldades;
- faça perguntas quando necessário;
- busque compreender as decisões técnicas;
- cumpra os compromissos assumidos;
- participe das discussões da equipe;
- compartilhe conhecimentos e descobertas relevantes;
- reconheça a oportunidade de desenvolvimento profissional oferecida pela empresa.

Não se espera que o membro saiba resolver tudo sozinho. Entretanto, espera-se que ele participe e tire suas dúvidas, sempre buscando o desenvolvimento mútuo entre o mesmo e a área.