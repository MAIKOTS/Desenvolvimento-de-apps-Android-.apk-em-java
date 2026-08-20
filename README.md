# Curso Completo: Desenvolvimento de Apps Android (.apk) com Java e Gradle

## Sobre este curso

Este curso ensina a criar aplicativos Android nativos do zero, usando **Java** como linguagem e **Gradle** como sistema de build, até gerar e assinar um arquivo **.apk** final. É voltado para quem já tem alguma noção de programação, mas não é obrigatório.

**Carga estimada:** 40–60 horas de estudo
**Pré-requisitos:** Lógica de programação básica (ajuda, mas o Módulo 2 cobre o essencial de Java)

---

## Índice

1. Preparando o ambiente
2. Fundamentos de Java para Android
3. Estrutura de um projeto Android e o Gradle
4. Activities e o ciclo de vida
5. Layouts e Views (XML)
6. Componentes de UI e eventos
7. Intents e navegação entre telas
8. RecyclerView e Adapters (listas)
9. Fragments
10. Persistência de dados (SharedPreferences, SQLite, Room)
11. Consumindo APIs (Retrofit)
12. Permissões e Services
13. Testes automatizados
14. Gerando e assinando o APK com Gradle
15. Publicando na Google Play
16. Projeto final sugerido

---

## Módulo 1 — Preparando o Ambiente

### 1.1 O que você vai instalar
- **JDK (Java Development Kit)** 17 — Android Studio moderno já recomenda essa versão
- **Android Studio** (inclui o Android SDK, emulador e o Gradle embutido)
- Um dispositivo Android físico (opcional, mas recomendado) com **Depuração USB** ativada

### 1.2 Passo a passo
1. Baixe o Android Studio em `developer.android.com/studio`
2. Instale seguindo o assistente (ele já baixa o SDK padrão)
3. Abra o Android Studio → **More Actions → SDK Manager** e confirme que está instalada pelo menos a API 34 (Android 14)
4. Crie um emulador em **Device Manager → Create Device**

### 1.3 Criando o primeiro projeto
- **File → New Project → Empty Views Activity**
- Language: **Java** (não Kotlin!)
- Minimum SDK: API 24 (Android 7.0) é uma boa base de compatibilidade

### 1.4 Estrutura inicial gerada
```
MeuApp/
├── app/
│   ├── src/main/java/...      → seu código Java
│   ├── src/main/res/          → layouts, imagens, strings
│   └── AndroidManifest.xml    → configuração do app
├── build.gradle (Project)
├── build.gradle (Module :app)
└── settings.gradle
```

**Exercício 1:** Crie o projeto, rode no emulador e confirme que aparece "Hello World".

---

## Módulo 2 — Fundamentos de Java para Android

Se você já sabe Java, pode pular para o Módulo 3. Aqui vai o essencial usado o tempo todo em Android:

### 2.1 Classes, objetos e métodos
```java
public class Pessoa {
    private String nome;

    public Pessoa(String nome) {
        this.nome = nome;
    }

    public String getNome() {
        return nome;
    }
}
```

### 2.2 Interfaces e classes anônimas (muito usado em listeners)
```java
public interface OnCliqueListener {
    void aoClicar();
}

// Uso comum em Android:
botao.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Toast.makeText(context, "Cliquei!", Toast.LENGTH_SHORT).show();
    }
});
```

### 2.3 Herança e classes abstratas
```java
public abstract class Animal {
    public abstract void fazerSom();
}

public class Cachorro extends Animal {
    @Override
    public void fazerSom() {
        System.out.println("Au au");
    }
}
```

### 2.4 Coleções (List, ArrayList, Map)
```java
List<String> nomes = new ArrayList<>();
nomes.add("Ana");
nomes.add("Bruno");

Map<String, Integer> idades = new HashMap<>();
idades.put("Ana", 30);
```

### 2.5 Expressões Lambda (Java 8+, funciona bem em Android)
```java
botao.setOnClickListener(v -> {
    Toast.makeText(this, "Cliquei!", Toast.LENGTH_SHORT).show();
});
```

**Exercício 2:** Crie uma classe `Produto` com nome e preço, uma lista de produtos e imprima o total no `Logcat` usando `Log.d()`.

---

## Módulo 3 — Estrutura de Projeto e o Gradle

### 3.1 O que é o Gradle
Gradle é o sistema que **compila** seu código Java/XML, resolve **dependências** (bibliotecas externas) e **empacota** tudo em um `.apk` (ou `.aab` para a Play Store).

### 3.2 `build.gradle` do módulo `app` (o mais importante)
```groovy
plugins {
    id 'com.android.application'
}

android {
    namespace 'com.exemplo.meuapp'
    compileSdk 34

    defaultConfig {
        applicationId "com.exemplo.meuapp"
        minSdk 24
        targetSdk 34
        versionCode 1
        versionName "1.0"
    }

    buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
        debug {
            minifyEnabled false
        }
    }

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_17
        targetCompatibility JavaVersion.VERSION_17
    }
}

dependencies {
    implementation 'androidx.appcompat:appcompat:1.7.0'
    implementation 'com.google.android.material:material:1.12.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
}
```

### 3.3 Conceitos-chave
| Termo | O que é |
|---|---|
| `applicationId` | Identificador único do app na Play Store |
| `minSdk` | Versão mínima do Android suportada |
| `targetSdk` | Versão que você testou/otimizou o app |
| `versionCode` / `versionName` | Controle de versão interno / visível ao usuário |
| `dependencies` | Bibliotecas externas que o Gradle baixa automaticamente |
| `buildTypes` | Perfis de build: `debug` (desenvolvimento) e `release` (produção) |

### 3.4 Comandos Gradle úteis (terminal)
```bash
./gradlew assembleDebug      # gera um APK de debug
./gradlew assembleRelease    # gera um APK de release
./gradlew clean              # limpa builds anteriores
./gradlew test               # roda testes unitários
```

**Exercício 3:** Adicione a dependência do Retrofit ao `build.gradle` (vamos usá-la no Módulo 11) e sincronize o projeto (botão "Sync Now").

---

## Módulo 4 — Activities e Ciclo de Vida

Uma **Activity** representa uma tela do app.

### 4.1 Criando uma Activity
```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
}
```

Toda Activity precisa ser declarada no `AndroidManifest.xml`:
```xml
<activity
    android:name=".MainActivity"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

### 4.2 Ciclo de vida
```
onCreate() → onStart() → onResume() → [app em uso]
   → onPause() → onStop() → onDestroy()
```

- `onCreate`: tela é criada (infla o layout)
- `onResume`: tela ficou visível e interativa
- `onPause`: usuário está saindo da tela (salve dados leves aqui)
- `onDestroy`: tela sendo destruída

**Exercício 4:** Sobrescreva os 5 métodos do ciclo de vida e use `Log.d("Ciclo", "onX chamado")` em cada um. Observe a ordem no Logcat ao girar a tela do emulador.

---

## Módulo 5 — Layouts e Views (XML)

### 5.1 ConstraintLayout (padrão recomendado)
```xml
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/textTitulo"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Bem-vindo!"
        android:textSize="24sp"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

    <Button
        android:id="@+id/botaoEntrar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Entrar"
        app:layout_constraintTop_toBottomOf="@id/textTitulo"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

### 5.2 Outros layouts comuns
- **LinearLayout**: organiza em linha ou coluna
- **FrameLayout**: sobreposição de elementos
- **ScrollView**: conteúdo rolável

### 5.3 Boas práticas
- Sempre use `strings.xml` para textos (facilita tradução):
```xml
<string name="titulo_boas_vindas">Bem-vindo!</string>
```
- Use `dimens.xml` para espaçamentos reutilizáveis

**Exercício 5:** Monte uma tela de login com `EditText` (usuário/senha) e um `Button`, usando ConstraintLayout.

---

## Módulo 6 — Componentes de UI e Eventos

### 6.1 Referenciando views no Java
```java
TextView texto = findViewById(R.id.textTitulo);
Button botao = findViewById(R.id.botaoEntrar);

botao.setOnClickListener(v -> {
    texto.setText("Você clicou!");
});
```

### 6.2 View Binding (alternativa moderna e mais segura)
No `build.gradle`:
```groovy
android {
    buildFeatures {
        viewBinding true
    }
}
```
No código:
```java
private ActivityMainBinding binding;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    binding = ActivityMainBinding.inflate(getLayoutInflater());
    setContentView(binding.getRoot());

    binding.botaoEntrar.setOnClickListener(v -> {
        binding.textTitulo.setText("Você clicou!");
    });
}
```

### 6.3 Componentes essenciais
| View | Uso |
|---|---|
| `TextView` | Exibir texto |
| `EditText` | Campo de entrada |
| `Button` | Botão clicável |
| `ImageView` | Exibir imagens |
| `CheckBox` / `Switch` | Opções booleanas |
| `Spinner` | Lista suspensa |
| `ProgressBar` | Indicador de carregamento |

**Exercício 6:** Complete a tela de login: ao clicar em "Entrar", valide se os campos não estão vazios e mostre um `Toast` de erro ou sucesso.

---

## Módulo 7 — Intents e Navegação

### 7.1 Intent explícita (abrir outra Activity do seu app)
```java
Intent intent = new Intent(MainActivity.this, PerfilActivity.class);
intent.putExtra("nome_usuario", "Ana");
startActivity(intent);
```

Recebendo o dado na outra Activity:
```java
String nome = getIntent().getStringExtra("nome_usuario");
```

### 7.2 Intent implícita (abrir apps do sistema)
```java
Intent intentLigar = new Intent(Intent.ACTION_DIAL, Uri.parse("tel:123456789"));
startActivity(intentLigar);

Intent intentSite = new Intent(Intent.ACTION_VIEW, Uri.parse("https://exemplo.com"));
startActivity(intentSite);
```

### 7.3 Recebendo resultado de outra Activity
```java
ActivityResultLauncher<Intent> launcher = registerForActivityResult(
    new ActivityResultContracts.StartActivityForResult(),
    result -> {
        if (result.getResultCode() == RESULT_OK) {
            String resposta = result.getData().getStringExtra("resposta");
        }
    }
);

launcher.launch(new Intent(this, OutraActivity.class));
```

**Exercício 7:** Crie uma segunda Activity que recebe o nome digitado na tela de login e exibe "Bem-vindo, [nome]!".

---

## Módulo 8 — RecyclerView e Adapters

Essencial para exibir listas (redes sociais, e-commerce, etc.)

### 8.1 Dependência
```groovy
implementation 'androidx.recyclerview:recyclerview:1.3.2'
```

### 8.2 Layout do item (`item_produto.xml`)
```xml
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="16dp">

    <TextView android:id="@+id/textNome"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <TextView android:id="@+id/textPreco"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
</LinearLayout>
```

### 8.3 Modelo de dados
```java
public class Produto {
    private String nome;
    private double preco;

    public Produto(String nome, double preco) {
        this.nome = nome;
        this.preco = preco;
    }

    public String getNome() { return nome; }
    public double getPreco() { return preco; }
}
```

### 8.4 Adapter
```java
public class ProdutoAdapter extends RecyclerView.Adapter<ProdutoAdapter.ViewHolder> {

    private List<Produto> lista;

    public ProdutoAdapter(List<Produto> lista) {
        this.lista = lista;
    }

    @NonNull
    @Override
    public ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.item_produto, parent, false);
        return new ViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull ViewHolder holder, int position) {
        Produto produto = lista.get(position);
        holder.textNome.setText(produto.getNome());
        holder.textPreco.setText("R$ " + produto.getPreco());
    }

    @Override
    public int getItemCount() {
        return lista.size();
    }

    static class ViewHolder extends RecyclerView.ViewHolder {
        TextView textNome, textPreco;

        ViewHolder(View itemView) {
            super(itemView);
            textNome = itemView.findViewById(R.id.textNome);
            textPreco = itemView.findViewById(R.id.textPreco);
        }
    }
}
```

### 8.5 Usando na Activity
```java
RecyclerView recyclerView = findViewById(R.id.recyclerView);
recyclerView.setLayoutManager(new LinearLayoutManager(this));

List<Produto> produtos = new ArrayList<>();
produtos.add(new Produto("Camiseta", 49.90));
produtos.add(new Produto("Tênis", 199.90));

recyclerView.setAdapter(new ProdutoAdapter(produtos));
```

**Exercício 8:** Adicione um `OnClickListener` em cada item da lista que abre uma tela de detalhes do produto.

---

## Módulo 9 — Fragments

Fragments são "sub-telas" reutilizáveis dentro de uma Activity, muito usados com Bottom Navigation.

### 9.1 Criando um Fragment
```java
public class HomeFragment extends Fragment {

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                              Bundle savedInstanceState) {
        return inflater.inflate(R.layout.fragment_home, container, false);
    }
}
```

### 9.2 Adicionando dinamicamente
```java
getSupportFragmentManager()
    .beginTransaction()
    .replace(R.id.container, new HomeFragment())
    .commit();
```

**Exercício 9:** Crie dois Fragments (`HomeFragment` e `PerfilFragment`) e alterne entre eles com dois botões.

---

## Módulo 10 — Persistência de Dados

### 10.1 SharedPreferences (dados simples: configurações, login)
```java
SharedPreferences prefs = getSharedPreferences("MeuApp", MODE_PRIVATE);

// Salvar
prefs.edit().putString("usuario", "Ana").apply();

// Ler
String usuario = prefs.getString("usuario", "");
```

### 10.2 Room (banco de dados local, recomendado sobre SQLite puro)

Dependências:
```groovy
implementation 'androidx.room:room-runtime:2.6.1'
annotationProcessor 'androidx.room:room-compiler:2.6.1'
```

Entidade:
```java
@Entity
public class Usuario {
    @PrimaryKey(autoGenerate = true)
    public int id;
    public String nome;
    public String email;
}
```

DAO:
```java
@Dao
public interface UsuarioDao {
    @Insert
    void inserir(Usuario usuario);

    @Query("SELECT * FROM Usuario")
    List<Usuario> listarTodos();
}
```

Banco de dados:
```java
@Database(entities = {Usuario.class}, version = 1)
public abstract class AppDatabase extends RoomDatabase {
    public abstract UsuarioDao usuarioDao();
}
```

**Exercício 10:** Crie um cadastro de usuários que salva no Room e exibe a lista em um RecyclerView.

---

## Módulo 11 — Consumindo APIs com Retrofit

### 11.1 Dependências
```groovy
implementation 'com.squareup.retrofit2:retrofit:2.11.0'
implementation 'com.squareup.retrofit2:converter-gson:2.11.0'
```

### 11.2 Permissão de internet (AndroidManifest.xml)
```xml
<uses-permission android:name="android.permission.INTERNET" />
```

### 11.3 Interface da API
```java
public interface ApiService {
    @GET("users")
    Call<List<Usuario>> listarUsuarios();
}
```

### 11.4 Configurando o Retrofit
```java
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("https://api.exemplo.com/")
    .addConverterFactory(GsonConverterFactory.create())
    .build();

ApiService api = retrofit.create(ApiService.class);

api.listarUsuarios().enqueue(new Callback<List<Usuario>>() {
    @Override
    public void onResponse(Call<List<Usuario>> call, Response<List<Usuario>> response) {
        if (response.isSuccessful()) {
            List<Usuario> usuarios = response.body();
        }
    }

    @Override
    public void onFailure(Call<List<Usuario>> call, Throwable t) {
        Log.e("API", "Erro: " + t.getMessage());
    }
});
```

**Exercício 11:** Consuma uma API pública gratuita (ex: `https://jsonplaceholder.typicode.com/users`) e liste os resultados em um RecyclerView.

---

## Módulo 12 — Permissões e Services

### 12.1 Permissões em tempo de execução (Android 6+)
```java
if (ContextCompat.checkSelfPermission(this, Manifest.permission.CAMERA)
        != PackageManager.PERMISSION_GRANTED) {
    ActivityCompat.requestPermissions(this,
            new String[]{Manifest.permission.CAMERA}, 100);
}
```

### 12.2 Service simples (tarefa em background)
```java
public class MeuService extends Service {
    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        // trabalho em background
        return START_STICKY;
    }
}
```

**Exercício 12:** Peça permissão de câmera e, se concedida, abra a câmera do dispositivo usando `Intent(MediaStore.ACTION_IMAGE_CAPTURE)`.

---

## Módulo 13 — Testes Automatizados

### 13.1 Teste unitário (JUnit) — `src/test/java`
```java
public class CalculadoraTest {
    @Test
    public void somaDoisNumeros() {
        Calculadora calc = new Calculadora();
        assertEquals(5, calc.somar(2, 3));
    }
}
```

### 13.2 Teste instrumentado (Espresso) — `src/androidTest/java`
```java
@Test
public void clicarBotaoAtualizaTexto() {
    onView(withId(R.id.botaoEntrar)).perform(click());
    onView(withId(R.id.textTitulo)).check(matches(withText("Você clicou!")));
}
```

Rodando pelo Gradle:
```bash
./gradlew test              # testes unitários
./gradlew connectedAndroidTest   # testes instrumentados (precisa de emulador/device)
```

---

## Módulo 14 — Gerando e Assinando o APK

### 14.1 APK de debug (rápido, para testes)
```bash
./gradlew assembleDebug
```
Arquivo gerado em: `app/build/outputs/apk/debug/app-debug.apk`

### 14.2 Criando uma keystore (assinatura obrigatória para release)
```bash
keytool -genkey -v -keystore minha-chave.jks -keyalg RSA -keysize 2048 -validity 10000 -alias minhaapp
```

### 14.3 Configurando assinatura no `build.gradle`
```groovy
android {
    signingConfigs {
        release {
            storeFile file("minha-chave.jks")
            storePassword "SUA_SENHA"
            keyAlias "minhaapp"
            keyPassword "SUA_SENHA"
        }
    }

    buildTypes {
        release {
            signingConfig signingConfigs.release
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
}
```

> ⚠️ **Nunca** coloque senhas direto no `build.gradle` em projetos reais/públicos. Use um arquivo `gradle.properties` fora do controle de versão ou variáveis de ambiente.

### 14.4 Gerando o APK final assinado
```bash
./gradlew assembleRelease
```
Arquivo gerado em: `app/build/outputs/apk/release/app-release.apk`

Ou pelo Android Studio: **Build → Generate Signed Bundle / APK**

**Exercício 14:** Gere sua própria keystore e produza um APK de release do app que você construiu ao longo do curso. Instale-o manualmente em um celular real (`adb install app-release.apk`).

---

## Módulo 15 — Publicando na Google Play

1. Crie uma conta de desenvolvedor em `play.google.com/console` (taxa única)
2. Prefira gerar um **.aab** (Android App Bundle) em vez de `.apk`: `./gradlew bundleRelease`
3. Preencha ficha da loja: nome, descrição, ícone, capturas de tela, política de privacidade
4. Defina classificação de conteúdo e público-alvo
5. Envie para revisão (teste interno → fechado → produção)

---

## Módulo 16 — Projeto Final Sugerido: App de Lista de Tarefas

Consolide tudo o que aprendeu construindo um **"To-Do List"** completo:

- [ ] Tela de login simples com SharedPreferences
- [ ] CRUD de tarefas usando Room (criar, listar, editar, excluir)
- [ ] Lista de tarefas em RecyclerView
- [ ] Tela de detalhes/edição via Intent
- [ ] Notificação local ao criar tarefa com prazo (usar `NotificationManager`)
- [ ] Gerar o APK assinado final

---

## Recursos complementares

- Documentação oficial: `developer.android.com/docs`
- Referência de componentes Material Design: `material.io/develop/android`
- Prática de exercícios de Java: `developer.android.com/courses`

---

*Bons estudos! O caminho mais eficaz é: leia a teoria de cada módulo, digite o código manualmente (não copie e cole) e sempre complete o exercício antes de avançar.*
