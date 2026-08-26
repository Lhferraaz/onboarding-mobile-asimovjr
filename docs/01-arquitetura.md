# Arquitetura dos Projetos Mobile

Este documento apresenta a arquitetura utilizada como referência nos projetos da área de Mobile da Asimov Jr.

O objetivo é estabelecer uma separação clara de responsabilidades dentro da aplicação, tornando o código mais organizado, previsível e fácil de manter, especialmente em projetos desenvolvidos por vários membros e que podem ser continuados por outras equipes no futuro.

Atualmente, a área utiliza **MVVM (Model–View–ViewModel)** como arquitetura de referência, em conjunto com **Riverpod** para gerenciamento de estado e injeção de dependências.

> A arquitetura descrita neste guia deve ser entendida como uma base recomendada, e não como uma estrutura rígida. Projetos podem exigir adaptações de acordo com sua complexidade e necessidades específicas, desde que essas decisões possuam justificativa técnica.

---

## Visão geral

De forma simplificada, o fluxo da aplicação pode ser representado da seguinte maneira:

```text
View
  ↓
ViewModel
  ↓
Repository / Service
  ↓
Fonte de dados
```

## Entendendo a arquitetura na prática

Antes de pensar em nomes como View, ViewModel ou Repository, imagine uma situação simples:

> O usuário abre a tela de perfil e altera o próprio nome.

Para essa ação acontecer, diferentes partes da aplicação possuem responsabilidades diferentes.

```text
Usuário altera o nome
        ↓
A tela percebe a ação
        ↓
O ViewModel decide o que fazer
        ↓
O Repository salva a informação
        ↓
Firebase
        ↓
O resultado volta
        ↓
O ViewModel atualiza o estado
        ↓
A tela é atualizada

Cada camada possui uma responsabilidade diferente:

* **View:** apresenta a interface e recebe as interações do usuário;
* **ViewModel:** Basicamente estabelece a comunicação entre a view e o repository;
* **Repository:** Contém toda a lógica;
* **Service:** encapsula integrações com serviços externos quando necessário;
* **Model:** representa os dados utilizados pela aplicação.

Essa separação busca evitar que um único arquivo concentre interface, estado, regras da funcionalidade e acesso direto ao Firebase ou a outras fontes de dados.

---

## MVVM

MVVM significa:

```text
Model
View
ViewModel
```

A arquitetura separa a representação visual da aplicação da lógica responsável por controlar seus dados e estados.

Nos projetos Mobile da Asimov Jr., o MVVM pode ser combinado com camadas adicionais, como **Repositories** e **Services**, para evitar que o ViewModel conheça diretamente detalhes de implementação das fontes de dados.

---

## View

A **View** representa a interface apresentada ao usuário.

Ela pode ser composta por:

* páginas;
* widgets;
* formulários;
* componentes visuais;
* elementos de navegação;
* estados de carregamento;
* mensagens de erro;
* feedbacks de sucesso.

Sua principal responsabilidade é definir **como os dados são apresentados e como o usuário interage com a aplicação**.

Uma View pode, por exemplo:

* exibir informações de um usuário;
* apresentar um formulário;
* chamar uma ação do ViewModel ao pressionar um botão;
* observar o estado de uma funcionalidade;
* mostrar um indicador de carregamento;
* apresentar uma mensagem caso ocorra algum erro.

A View não deve concentrar acesso direto ao banco de dados ou regras complexas da funcionalidade.

### Exemplo

```dart
class ProfilePage extends ConsumerWidget {
  const ProfilePage({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final state = ref.watch(profileViewModelProvider);

    return Scaffold(
      body: state.when(
        loading: () => const Center(
          child: CircularProgressIndicator(),
        ),
        error: (error, stackTrace) => const Center(
          child: Text('Erro ao carregar perfil'),
        ),
        data: (user) => Center(
          child: Text(user.name),
        ),
      ),
    );
  }
}
```

Neste exemplo, a View observa o estado disponibilizado pelo ViewModel e decide apenas como representá-lo visualmente.

Ela não precisa saber de onde os dados vieram ou como foram obtidos.

---

## ViewModel

O **ViewModel** funciona como intermediário entre a interface e as demais camadas da funcionalidade.

Ele é responsável por controlar o estado utilizado pela View e coordenar as operações realizadas a partir das interações do usuário.

Entre suas responsabilidades podem estar:

* carregar dados;
* controlar estados de carregamento;
* atualizar o estado da funcionalidade;
* tratar erros;
* validar informações;
* chamar Repositories ou Services;
* coordenar ações iniciadas pela interface.

Nos projetos da área, o **Riverpod** é utilizado para disponibilizar e gerenciar esses estados.

### Exemplo

```dart
class ProfileViewModel extends AsyncNotifier<User> {
  @override
  Future<User> build() async {
    return ref.read(userRepositoryProvider).getCurrentUser();
  }

  Future<void> updateName(String name) async {
    state = const AsyncLoading();

    state = await AsyncValue.guard(() async {
      final repository = ref.read(userRepositoryProvider);

      await repository.updateName(name);

      return repository.getCurrentUser();
    });
  }
}
```

Nesse caso, o ViewModel controla o estado da tela de perfil e utiliza o Repository para realizar operações relacionadas aos dados do usuário.

A View não precisa conhecer os detalhes dessa implementação.

---

## Model

Os **Models** representam os dados utilizados pela aplicação.

Um Model pode representar, por exemplo:

* um usuário;
* um produto;
* um agendamento;
* uma mensagem;
* uma atividade;
* uma notificação;
* qualquer outra entidade relevante para o projeto.

### Exemplo

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

Além de armazenar os dados, os Models podem possuir métodos responsáveis por converter informações entre diferentes formatos quando necessário.

Por exemplo:

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

  Map<String, dynamic> toMap() {
    return {
      'id': id,
      'name': name,
      'email': email,
    };
  }

  factory User.fromMap(Map<String, dynamic> map) {
    return User(
      id: map['id'],
      name: map['name'],
      email: map['email'],
    );
  }
}
```

O Model não deve depender da interface da aplicação.

---

## Repository

O **Repository** cria uma camada de abstração entre a lógica da aplicação e suas fontes de dados.

Seu objetivo é impedir que o ViewModel precise conhecer detalhes como:

* qual banco de dados está sendo utilizado;
* como uma consulta ao Firestore é realizada;
* onde determinado dado está armazenado;
* como uma API externa é acessada;
* como os dados recebidos são convertidos.

O ViewModel solicita aquilo de que precisa ao Repository, enquanto o Repository decide como obter ou modificar aquela informação.

### Exemplo

```dart
class UserRepository {
  final FirebaseFirestore firestore;

  UserRepository(this.firestore);

  Future<User> getUser(String id) async {
    final document =
        await firestore.collection('users').doc(id).get();

    return User.fromMap(document.data()!);
  }

  Future<void> updateName(String id, String name) async {
    await firestore.collection('users').doc(id).update({
      'name': name,
    });
  }
}
```

Assim, o ViewModel pode trabalhar com operações como:

```dart
repository.getUser(id);
repository.updateName(id, name);
```

sem precisar conhecer os detalhes da implementação no Firebase.

---

## Services

Nem toda integração externa precisa necessariamente ser tratada diretamente por um Repository.

Quando uma funcionalidade depende de um serviço específico, pode ser útil criar uma classe responsável exclusivamente por essa integração.

Alguns exemplos são:

* autenticação;
* armazenamento de arquivos;
* notificações;
* localização;
* APIs externas;
* serviços específicos do dispositivo.

Exemplo:

```dart
class AuthService {
  final FirebaseAuth auth;

  AuthService(this.auth);

  Future<UserCredential> signIn({
    required String email,
    required String password,
  }) {
    return auth.signInWithEmailAndPassword(
      email: email,
      password: password,
    );
  }

  Future<void> signOut() {
    return auth.signOut();
  }
}
```

Um Repository pode utilizar um Service quando isso ajudar a manter responsabilidades mais claras.

---

## Fluxo de uma funcionalidade

Considere uma tela na qual o usuário altera seu nome.

O fluxo pode ocorrer da seguinte maneira:

```text
Usuário
  ↓
View
  ↓
ViewModel
  ↓
Repository
  ↓
Firebase
```

### 1. O usuário interage com a View

```dart
ElevatedButton(
  onPressed: () {
    ref
        .read(profileViewModelProvider.notifier)
        .updateName(nameController.text);
  },
  child: const Text('Salvar'),
);
```

### 2. O ViewModel recebe a ação

```dart
Future<void> updateName(String name) async {
  await ref
      .read(userRepositoryProvider)
      .updateName(name);
}
```

### 3. O Repository acessa a fonte de dados

```dart
Future<void> updateName(String name) async {
  await firestore.collection('users').doc(userId).update({
    'name': name,
  });
}
```

### 4. O estado é atualizado

Após a operação, o ViewModel atualiza seu estado.

### 5. A View reage ao novo estado

Como a View observa o provider correspondente, ela é reconstruída quando necessário.

---

## Riverpod na arquitetura

O Riverpod possui duas funções importantes dentro da arquitetura utilizada pela área:

1. **gerenciamento de estado**;
2. **injeção de dependências**.

Por meio dos Providers, diferentes partes da aplicação podem receber as dependências necessárias sem precisar instanciá-las diretamente.

Por exemplo:

```dart
final userRepositoryProvider = Provider<UserRepository>((ref) {
  return UserRepository(
    FirebaseFirestore.instance,
  );
});
```

O ViewModel pode acessar o Repository:

```dart
final repository = ref.read(userRepositoryProvider);
```

E a View pode acessar o ViewModel:

```dart
final state = ref.watch(profileViewModelProvider);
```

O fluxo de dependências fica semelhante a:

```text
View
  │
  └── observa → ViewModel
                  │
                  └── utiliza → Repository
                                  │
                                  └── utiliza → Firebase
```

Dessa forma, cada camada recebe somente as dependências necessárias para cumprir sua responsabilidade.

Ou seja, ViewModel é como se fosse o mediador entre a View e o Repository. Repare que: View nunca acessa diretamente Repository, e vice-versa.

---

## Separação por funcionalidades

Sempre que possível, a aplicação deve ser organizada considerando suas funcionalidades ou domínios.

Exemplo:

```text
features/
├── auth/
├── home/
├── profile/
├── appointments/
└── settings/
```

Cada funcionalidade pode conter os arquivos necessários para implementar seu próprio fluxo.

Por exemplo:

```text
profile/
├── models/
├── repositories/
├── viewmodels/
├── views/
└── widgets/
```

Isso permite que arquivos relacionados a uma mesma funcionalidade permaneçam próximos entre si.

A estrutura exata de diretórios será abordada separadamente na documentação de **estrutura de projetos**.

---

## Widgets compartilhados

Nem todo componente pertence exclusivamente a uma funcionalidade.

Elementos utilizados em várias partes da aplicação podem ser mantidos em uma área compartilhada.

Exemplos:

* botões padronizados;
* campos de texto;
* loaders;
* componentes de feedback;
* componentes de navegação;
* elementos visuais reutilizados entre diferentes funcionalidades.

De forma conceitual:

```text
lib/
├── features/
│   ├── auth/
│   ├── home/
│   └── profile/
│
└── shared/
    └── widgets/
```

Um widget deve ser movido para a área compartilhada quando existir uma necessidade real de reutilização.

Não é necessário transformar todo componente em um widget global antecipadamente.

---

## Responsabilidade de cada camada

| Camada         | Responsabilidade principal                               |
| -------------- | -------------------------------------------------------- |
| **View**       | Exibir informações e receber interações do usuário       |
| **ViewModel**  | Controlar estado e coordenar ações da funcionalidade     |
| **Model**      | Representar os dados utilizados pela aplicação           |
| **Repository** | Abstrair o acesso e a manipulação dos dados              |
| **Service**    | Encapsular integrações específicas com serviços externos |

Uma forma simples de identificar onde determinado código deve ficar é perguntar:

> **Qual é a responsabilidade desse código?**

Se ele define como algo aparece na tela, provavelmente pertence à View.

Se ele controla o estado apresentado pela tela, provavelmente pertence ao ViewModel.

Se ele representa uma entidade da aplicação, provavelmente pertence a um Model.

Se ele acessa ou persiste dados, provavelmente pertence a um Repository.

Se ele encapsula uma integração específica com uma ferramenta ou serviço externo, pode pertencer a um Service.

---

## O que evitar

A arquitetura existe para ajudar a separar responsabilidades. Por isso, alguns padrões devem ser evitados.

### Acesso direto ao Firebase dentro da View

Evite:

```dart
onPressed: () async {
  await FirebaseFirestore.instance
      .collection('users')
      .doc(userId)
      .update({
        'name': nameController.text,
      });
}
```

Prefira:

```text
View
  ↓
ViewModel
  ↓
Repository
  ↓
Firebase
```

---

### Arquivos responsáveis por muitas coisas

Uma página não deve concentrar:

```text
Interface
+
Validação
+
Estado
+
Consulta ao banco
+
Conversão de dados
+
Regras da funcionalidade
```

Quanto mais responsabilidades um arquivo acumula, mais difícil tende a ser sua manutenção e revisão.

---

### Abstrações sem necessidade

Separar responsabilidades não significa criar uma nova classe para cada operação existente.

A arquitetura deve facilitar o desenvolvimento, e não adicionar complexidade sem benefício.

Antes de criar uma nova camada ou abstração, deve existir uma responsabilidade clara que justifique sua existência.

---

## Princípio de flexibilidade

Esta arquitetura representa o **padrão recomendado da área de Mobile**, mas não deve ser tratada como uma regra imutável.

Projetos diferentes podem apresentar necessidades diferentes.

Uma aplicação pequena pode não exigir a mesma quantidade de abstrações de uma aplicação mais complexa. Da mesma forma, determinadas integrações podem exigir adaptações na organização apresentada neste documento.

Alterações são aceitáveis quando houver uma justificativa técnica válida.

O objetivo do padrão é garantir que exista uma base comum entre os projetos da área, permitindo que diferentes desenvolvedores consigam compreender e continuar um projeto com menor dependência de conhecimento informal.

Quando uma adaptação produzir resultados melhores e puder ser aplicada em outros projetos, ela também poderá motivar uma atualização deste guia.
