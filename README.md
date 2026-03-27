# Music API Explorer (iTunes & Spotify)

Este projeto é um laboratório de estudos focado em integração de APIs musicais. O aplicativo explora a API pública do iTunes para busca e prévia de áudio, e integra-se com o ecossistema do Spotify (Web API e App Remote) para controle de reprodução e gerenciamento de playlists.

O objetivo principal foi aprofundar conhecimentos em **URLSession**, arquitetura **MVVM-C**, e comparar paradigmas de assincronismo (**Combine vs. Async/Await**).

-----

## Funcionalidades

### Integração iTunes (Pública)

  * **Busca de Músicas:** Pesquisa em tempo real usando a iTunes Search API.
  * **Paginação:** Paginação básica da quantidade de dados buscada pela API durante a pesquisa em tempo real.
  * **Prévia de Áudio:** Reprodução de trechos de 30s utilizando `AVPlayer`.
  * **Interface Reativa:** Feedback visual de carregamento e tratamento de erros.

### Integração Spotify (Autenticada)

  * **Spotify App Remote:** Conexão com o app nativo do Spotify instalado no dispositivo para visualizar o *Player State* (música atual, capa, play/pause).
  * **Controle de Reprodução:** Comandos de Play/Pause via `SpotifyiOS` SDK.
  * **Autenticação OAuth 2.0:** Fluxo de autorização para obter tokens de acesso.
  * **Gerenciamento de Playlists:**
      * Listagem de playlists do usuário.
  * **Listagem de Álbuns:** Busca de álbuns de artistas específicos (ex: Don L).

-----

## Tech Stack & Arquitetura

O projeto foi desenvolvido 100% em Swift, priorizando abordagens modernas, mas mantendo interoperabilidade quando necessário.

  * **Interface:** SwiftUI (quase completamente) com integração de UIKit (`UIViewControllerRepresentable`) para o Spotify App Remote.
  * **Arquitetura:** **MVVM-C** (Model-View-ViewModel + Coordinator).
      * Utilização do padrão **Coordinator** com `NavigationStack` para gerenciamento de fluxo de navegação.
  * **Teste de outros Design Patterns:** Separação por naming spaces
      * Namespacing com Enums: Utilização de Case-less Enums para criar namespaces estritos (ex: API.Spotify), organizando os tipos da API e evitando conflitos de nomenclatura com outros modelos do app.
  * **Networking:**
      * `URLSession` genérico com `Codable` para parsing de JSON.
      * Implementação de *Endpoints* tipados (`Enum` based) para segurança de tipos nas requisições.
  * **Concorrência (Estudo Comparativo):**
      * **Combine:** Utilizado para *binding* de dados e fluxos reativos (ex: `DataTaskPublisher`).
      * **Async/Await:** Implementado para chamadas de API mais modernas e legíveis (ex: criação de playlists).

-----

## 📂 Estrutura do Projeto

```text
Music/
├── Coordinator/       # Gerenciamento de navegação (NavigationStack)
├── Remote/            # Camada de Networking (API Client, Endpoints, Request Builders)
│   ├── iTunes/        # Lógica específica do iTunes
│   └── Spotify/       # Lógica específica do Spotify (Auth, Responses)
├── View/              # Telas SwiftUI e Componentes Visuais
├── ViewModel/         # Lógica de negócios e Estado da View
├── Services/          # Serviços utilitários (ex: AudioPlayerService)
└── Resources/         # Assets e Configurações (Info.plist)
```

-----

## Como Executar

Para rodar o projeto, você precisará de uma conta de desenvolvedor no Spotify e o Xcode instalado.

1.  **Clone o repositório:**
    ```bash
    git clone https://github.com/seu-usuario/iTunes-Visualization.git
    ```
2.  **Dependências:**
    O projeto utiliza o **SpotifyiOS SDK** via Swift Package Manager. O Xcode deve resolver as dependências automaticamente.
3.  **Configuração do Spotify:**
      * Crie um app no [Spotify Developer Dashboard](https://developer.spotify.com/dashboard/).
      * Adicione o *Redirect URI* nas configurações do seu app no dashboard: `MigratePlaylistToSpotify://spotify-login-callback`.
      * Adicione o *Bundle ID* do projeto nas configurações do app no dashboard: `pedrolarry.Music`.
4.  **Chaves de API:**
      * Abra o arquivo `Constants.swift` ou `SpotifyAPI + AuthManager.swift`.
      * Substitua `clientID` e `clientSecret` pelas chaves geradas no dashboard do Spotify.
5.  **Execute:**
      * Selecione o simulador ou dispositivo físico (necessário ter o app do Spotify instalado e logado para testar o *App Remote*).

-----

##  Aprendizados

Este projeto serviu para consolidar os seguintes conceitos:

1. **Evolução do Swift:** A implementação híbrida no `SpotifyAlbumListViewModel` demonstra a transição de *completion handlers* e *Combine* para o moderno *Structured Concurrency* (Async/Await).
2. **Generics em Networking:** Criação de um `APIClient` agnóstico que pode decodificar qualquer modelo `Decodable`, reutilizando código entre as APIs do iTunes e Spotify.
3. **UIKit em SwiftUI:** A necessidade de usar o SDK do Spotify (que é baseado em `delegate` e UIKit) exigiu a criação de *wrappers* (`UIViewControllerRepresentable`) para funcionar harmonicamente em um app SwiftUI puro.

-----

## Pontos a serem melhorados

  * [ ] Melhorar o tratamento de *refresh token* na autenticação do Spotify, permitindo que o app se mantenha conectado por mais tempo.
  * [ ] Tornar a autenticação amigável ao usuário, permitindo com que ele se autentique apenas pelo próprio app e não substituindo variáveis dentro do código através do Spotify Dashboard.
  * [ ] Migrar totalmente o `SpotifyPlayer` (UIKit) para SwiftUI nativo se o SDK permitir.
  * [ ] Adicionar testes unitários para os ViewModels.
