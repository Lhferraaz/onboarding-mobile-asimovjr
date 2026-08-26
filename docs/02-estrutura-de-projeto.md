# Estrutura dos Projetos Mobile

Este documento apresenta a estrutura de diretórios utilizada como referência nos projetos da área de Mobile da Asimov Jr.

> Esta estrutura representa o padrão recomendado da área. Projetos podem realizar adaptações quando houver necessidade e justificativa técnica.

---

## Visão geral

A estrutura base de um projeto pode ser representada da seguinte forma:

```text
lib/
├── ui/
│   ├── core/
│   │   ├── shared/
│   │       ├── widgets/
│   │       └──  themes/
│   │
│   └── <nome_da_feature>/
│       ├── screens/
│       ├── view_models/
│       └── widgets/
│
├── data/
│   ├── models/
│   ├── repositories/
│   └── services/
│
├── config/
├── routing/
├── utils/
└── main.dart
```
É importante citar que: nem todos os projetos que serão desenvolvidos utilizarão toda essa estrutura de pasta. Criem as folders e as depêndencias conforme a necessidade.

A ideia principal é que, ao entrar em um projeto da área, um desenvolvedor consiga prever onde determinado código deve estar.

De maneira geral:

* `ui/` contém a interface e o estado das funcionalidades;
* `data/` concentra modelos, acesso aos dados e integrações;
* `config/` contém configurações gerais da aplicação;
* `routing/` centraliza a navegação;
* `utils/` contém utilitários realmente compartilhados;
* `test/` contém os testes automatizados;
* `testing/` contém recursos auxiliares utilizados pelos testes.

---

# `lib/`

A pasta `lib/` contém o código principal da aplicação.

```text
lib/
├── ui/
├── data/
├── config/
├── routing/
├── utils/
└── main.dart
```

A organização procura separar principalmente dois grupos de responsabilidades:

```text
Interface e estado
        ↓
       ui/

Acesso e representação dos dados
        ↓
      data/
```

---

# `ui/`

A pasta `ui/` contém tudo aquilo relacionado à interface da aplicação e ao estado utilizado por ela.

Sua organização é feita principalmente por **funcionalidades**.

```text
ui/
├── core/
├── auth/
├── home/
├── profile/
└── settings/
```

## Estrutura de uma feature

Uma funcionalidade pode ser organizada da seguinte maneira:

```text
ui/
└── <feature_name>/
    ├── screens/
    ├── view_models/
    └── widgets/
```

Cada uma dessas pastas possui uma responsabilidade diferente.

---

## `screens/`

A pasta `screens/` contém as telas completas pertencentes à funcionalidade.

```text
<feature_name>/
└── screens/
```

Uma screen normalmente representa uma página acessível através da navegação da aplicação.

Por exemplo, uma funcionalidade de autenticação pode possuir:

```text
auth/
├── screens/
├── view_models/
└── widgets/
```

Dentro de `screens/` poderiam existir as páginas de login, cadastro ou recuperação de senha.

A responsabilidade de uma screen é principalmente:

* estruturar a página;
* observar o estado disponibilizado pelo ViewModel;
* organizar os widgets necessários;
* encaminhar ações do usuário para o ViewModel.

A screen não deve concentrar acesso direto ao Firebase, consultas ao banco ou outras integrações externas.

---

## `view_models/`

A pasta `view_models/` contém os ViewModels responsáveis pelo estado e pelas ações da funcionalidade.

```text
<feature_name>/
└── view_models/
```

Entre suas responsabilidades podem estar:

* carregar informações;
* controlar estados de carregamento;
* receber ações da interface;
* tratar erros;
* validar informações relacionadas ao fluxo;
* chamar repositories;
* atualizar o estado observado pelas screens.

De maneira simplificada:

```text
Screen
   ↓
ViewModel
```

A screen informa que alguma ação aconteceu.

O ViewModel coordena o que precisa ser feito e atualiza o estado da funcionalidade.

---

## `widgets/`

A pasta `widgets/` contém componentes visuais utilizados dentro daquela funcionalidade.

```text
<feature_name>/
└── widgets/
```

Esses componentes não representam uma tela completa.

Eles podem ser utilizados para:

* dividir uma screen em partes menores;
* reduzir arquivos excessivamente grandes;
* reutilizar elementos dentro da própria feature;
* deixar a construção da interface mais legível.

Por exemplo:

```text
ui/
└── profile/
    ├── screens/
    ├── view_models/
    └── widgets/
```

Componentes utilizados apenas na funcionalidade de perfil devem permanecer dentro dela.

Caso um widget passe a ser utilizado por diferentes funcionalidades, pode fazer sentido movê-lo para:

```text
ui/core/shared/widgets/
```

---

# `ui/core/`

A pasta `ui/core/` contém elementos da interface que possuem responsabilidade global ou são compartilhados por diferentes funcionalidades.

```text
ui/
└── core/
    ├── shared/
       ├── widgets/
       └── themes/
```

---

## `ui/core/shared/widgets/`

A pasta:

```text
ui/core/shared/widgets/
```

contém widgets utilizados por diferentes funcionalidades da aplicação.

Por exemplo, se um mesmo componente for utilizado em:

```text
auth/
home/
profile/
settings/
```

ele pode ser considerado um componente compartilhado.

Por outro lado, se um widget for utilizado apenas dentro de:

```text
profile/
```

ele deve permanecer em:

```text
ui/profile/widgets/
```

> Widgets não devem ser colocados em `shared` apenas porque existe a possibilidade de serem reutilizados no futuro. A abstração deve surgir quando houver reutilização real ou uma responsabilidade claramente global.

---

## `ui/core/themes/`

A pasta:

```text
ui/core/themes/
```

centraliza as definições visuais globais da aplicação.

Ela pode concentrar responsabilidades relacionadas a:

* cores;
* tipografia;
* temas do Material;
* estilos globais;
* configurações visuais utilizadas em toda a aplicação.

O objetivo é evitar que decisões visuais importantes fiquem espalhadas pelas diferentes funcionalidades.

---

# Organização por funcionalidades

Uma aplicação com autenticação, home e perfil poderia possuir:

```text
lib/
└── ui/
    ├── core/
    │   ├── shared/
    │   │   └── widgets/
    │   └── themes/
    │
    ├── auth/
    │   ├── screens/
    │   ├── view_models/
    │   └── widgets/
    │
    ├── home/
    │   ├── screens/
    │   ├── view_models/
    │   └── widgets/
    │
    └── profile/
        ├── screens/
        ├── view_models/
        └── widgets/
```

Essa organização permite que os arquivos relacionados a uma mesma funcionalidade permaneçam próximos.

Ao trabalhar na funcionalidade de perfil, por exemplo, o desenvolvedor encontrará dentro de `profile/`:

```text
profile/
├── screens/
├── view_models/
└── widgets/
```

sem precisar procurar os arquivos daquela funcionalidade em diferentes partes do projeto.

---

# `data/`

A pasta `data/` contém os componentes responsáveis pela representação, obtenção, persistência e comunicação dos dados da aplicação.

```text
data/
├── models/
├── repositories/
└── services/
```

É nessa camada que normalmente ficam detalhes relacionados a:

* Firebase;
* APIs;
* armazenamento;
* autenticação;
* comunicação com serviços externos;
* transformação de dados.

---

## `data/models/`

A pasta:

```text
data/models/
```

contém os Models utilizados pela aplicação.

Models representam os dados manipulados pelo sistema.

Alguns exemplos conceituais poderiam ser:

```text
User
Appointment
Notification
Product
Message
```

Um Model define quais informações representam determinada entidade.

Exemplo:

```dart
class User {
  final String id;
  final String name;
  final String email;

  const User({
    required this.id,
    required this.name,
    required this.email,
  });
}
```

A separação entre `ui/` e `data/` é considerada suficiente para manter a organização sem adicionar camadas que não tragam benefício claro ao projeto.

Caso um projeto futuro apresente regras de negócio ou complexidade que justifiquem uma camada adicional, sua estrutura poderá ser adaptada.

---

# `data/repositories/`

Repositories representam a forma utilizada pelo restante da aplicação para consultar ou modificar informações.

```text
data/
└── repositories/
```

O Repository abstrai a origem dos dados.

Por exemplo, o ViewModel pode solicitar:

```dart
repository.getUser();
repository.updateUser();
```

sem precisar saber se essas informações estão sendo obtidas através de:

* Firebase;
* uma API;
* armazenamento local;
* outro serviço.

De maneira simplificada:

```text
ViewModel
    ↓
Repository
```

O ViewModel informa **o que precisa**.

O Repository sabe **como obter ou modificar aquela informação**.

---

## Exemplo

```dart
class UserRepository {
  final FirestoreService service;

  UserRepository(this.service);

  Future<User> getUser(String id) async {
    final data = await service.getUser(id);

    return User.fromMap(data);
  }
}
```

O ViewModel não precisa conhecer os detalhes de implementação utilizados para acessar o Firestore.

---

# `data/services/`

A pasta:

```text
data/services/
```

contém classes responsáveis pela comunicação direta com serviços externos ou fontes de dados.

Um Service pode encapsular integrações relacionadas a:

* Cloud Firestore;
* Firebase Authentication;
* Firebase Storage;
* APIs REST;
* notificações;
* localização;
* armazenamento local;
* outros SDKs ou serviços externos.

O fluxo pode ser entendido da seguinte maneira:

```text
ViewModel
    ↓
Repository
    ↓
Service
    ↓
Firebase / API / SDK
```

A diferença entre Repository e Service pode ser resumida assim:

```text
Repository
"O que preciso fazer com os dados?"

Service
"Como me comunico com a fonte desses dados?"
```

---

## Exemplo de fluxo

Considere que o usuário queira alterar seu nome.

```text
Screen
   ↓
ViewModel
   ↓
Repository
   ↓
Service
   ↓
Firebase
```

### 1. A screen recebe a interação

```dart
onPressed: () {
  ref
      .read(profileViewModelProvider.notifier)
      .updateName(name);
}
```

### 2. O ViewModel coordena a ação

```dart
Future<void> updateName(String name) async {
  await ref
      .read(userRepositoryProvider)
      .updateName(name);
}
```

### 3. O Repository solicita a alteração

```dart
Future<void> updateName(String name) {
  return service.updateName(name);
}
```

### 4. O Service acessa a fonte de dados

```dart
Future<void> updateName(String name) {
  return firestore
      .collection('users')
      .doc(userId)
      .update({
        'name': name,
      });
}
```

Cada parte conhece apenas a responsabilidade necessária para realizar sua função.

---

# Fluxo geral da aplicação

A estrutura pode ser resumida desta maneira:

```text
Usuário
   ↓
Screen
   ↓
ViewModel
   ↓
Repository
   ↓
Service
   ↓
Firebase / API / SDK
```

Os Models são utilizados ao longo desse fluxo para representar os dados manipulados pela aplicação.

```text
                 Model
                   │
                   ↓
Screen → ViewModel → Repository → Service
```

A arquitetura completa não precisa necessariamente possuir todas essas partes em todas as funcionalidades.

Uma funcionalidade simples pode não precisar de um Service específico, por exemplo.

A estrutura deve acompanhar a necessidade do projeto.

---

# `routing/`

A pasta:

```text
routing/
```

centraliza a configuração de navegação da aplicação.

Ela pode conter responsabilidades relacionadas a:

* definição das rotas;
* navegação entre telas;
* parâmetros;
* redirecionamentos;
* regras de acesso;
* configuração da biblioteca de navegação utilizada.

A navegação principal da aplicação deve permanecer centralizada sempre que isso facilitar a compreensão do fluxo entre as telas.

---

# `config/`

A pasta:

```text
config/
```

contém configurações gerais utilizadas pela aplicação.

Ela pode ser utilizada para:

* configurações de ambiente;
* inicialização de serviços;
* configuração de dependências;
* configurações globais;
* endpoints;
* informações relacionadas à execução da aplicação.

Credenciais ou dados sensíveis não devem ser armazenados diretamente em arquivos versionados.

---

# `utils/`

A pasta:

```text
utils/
```

contém funções ou classes auxiliares que sejam realmente genéricas e compartilhadas por diferentes partes da aplicação.

Podem existir utilitários relacionados a:

* formatação;
* validações genéricas;
* extensions;
* manipulação de datas;
* conversões.

Essa pasta deve ser utilizada com cuidado.

> Um arquivo não deve ser colocado em `utils/` apenas porque não ficou claro onde ele deveria estar.

Antes de criar um utilitário global, verifique se o código pertence, na verdade, a alguma funcionalidade específica.

---

# `main.dart`

O arquivo:

```text
main.dart
```

representa o ponto de entrada principal da aplicação.

Ele normalmente é responsável por iniciar o Flutter e realizar as configurações essenciais antes da execução do aplicativo.

Exemplo:

```dart
void main() {
  runApp(
    const ProviderScope(
      child: App(),
    ),
  );
}
```

Projetos que possuírem necessidade de múltiplos ambientes podem utilizar pontos de entrada adicionais.

Por exemplo:

```text
main.dart
main_development.dart
main_staging.dart
```

Essa separação não deve ser criada obrigatoriamente em todos os projetos.

Ela deve existir apenas quando houver necessidade real de diferentes configurações de ambiente.

---

# Como decidir onde colocar um arquivo

Quando houver dúvida sobre onde determinado código deve ficar, identifique primeiro sua responsabilidade.

| Se o código...                                      | Provavelmente pertence a... |
| --------------------------------------------------- | --------------------------- |
| Representa uma tela completa                        | `ui/<feature>/screens/`     |
| Controla o estado de uma funcionalidade             | `ui/<feature>/view_models/` |
| É um componente específico de uma feature           | `ui/<feature>/widgets/`     |
| É um widget compartilhado entre diferentes features | `ui/core/shared/widgets/`   |
| Define estilos visuais globais                      | `ui/core/themes/`           |
| Representa dados da aplicação                       | `data/models/`              |
| Abstrai operações de acesso aos dados               | `data/repositories/`        |
| Comunica diretamente com Firebase, API ou SDK       | `data/services/`            |
| Define a navegação                                  | `routing/`                  |
| Define configurações globais                        | `config/`                   |
| É um auxiliar realmente genérico                    | `utils/`                    |

---

# Exemplo completo

Considere uma aplicação com autenticação, home e perfil.

```text
lib/
├── ui/
│   ├── core/
│   │   ├── shared/
│   │   │   └── widgets/
│   │   └── themes/
│   │
│   ├── auth/
│   │   ├── screens/
│   │   ├── view_models/
│   │   └── widgets/
│   │
│   ├── home/
│   │   ├── screens/
│   │   ├── view_models/
│   │   └── widgets/
│   │
│   └── profile/
│       ├── screens/
│       ├── view_models/
│       └── widgets/
│
├── data/
│   ├── models/
│   ├── repositories/
│   └── services/
│
├── config/
├── routing/
├── utils/
└── main.dart

test/
├── data/
├── ui/
└── utils/

testing/
├── fakes/
└── models/
```

Uma funcionalidade de perfil poderia utilizar o seguinte fluxo:

```text
ui/profile/screens/
        ↓
ui/profile/view_models/
        ↓
data/repositories/
        ↓
data/services/
        ↓
Firebase
```

Enquanto os dados utilizados nesse fluxo seriam representados por:

```text
data/models/
```

---

# Princípio de simplicidade

A estrutura apresentada neste documento existe para facilitar o desenvolvimento, e não para obrigar todos os projetos a possuir a maior quantidade possível de pastas e abstrações.

Uma pasta ou camada deve existir quando houver uma responsabilidade que justifique sua existência.

Por exemplo, se determinada feature não possuir widgets separados de sua screen, sua pasta `widgets/` pode permanecer vazia até que seja necessária.

Da mesma forma, uma funcionalidade pode não precisar de um Service próprio caso seu Repository já possua uma dependência adequada para realizar a operação.

> A arquitetura deve começar simples e evoluir conforme a complexidade real do projeto.

O objetivo é preservar uma base comum entre os projetos da área sem transformar a organização do código em burocracia.

---

# Regra geral

Ao criar ou modificar um arquivo, tente responder três perguntas:

1. **A qual funcionalidade esse código pertence?**
2. **Qual é a responsabilidade desse código?**
3. **Ele é específico daquela funcionalidade ou compartilhado pela aplicação?**

Essas respostas normalmente serão suficientes para determinar onde o arquivo deve ser colocado.

> Uma boa estrutura deve permitir que outro desenvolvedor encontre e compreenda o código sem depender de explicações do autor original.
