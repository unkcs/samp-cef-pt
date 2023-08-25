# SAMP CEF
Este projeto incorpora o CEF no SA:MP, expandindo as capacidades de expressão com belas interfaces de jogo usando HTML / CSS / JavaScript.

Trata-se de **um FRAMEWORK** (ou SDK), não algo que você baixa e usa. Para poder criar, você deve ter alguns conhecimentos básicos de desenvolvimento web (JS / HTML / CSS).

## O que você pode fazer
- Criar visualizações de navegador a partir de um gamemode ou de plugins do lado do cliente (C ABI).
- Colocar navegadores em objetos (com um tipo de som espacial)
- Enviar e receber eventos definidos personalizados de / para os clientes.

## Pacotes
- `cef` - Wrappers em Rust para a API CEF C.
- `cef-api` - Wrappers em Rust para construir plugins de cliente usando CEF.
- `cef-interface` - exemplo de um plugin em Rust.
- `cef-sys` - Bindings para a API CEF C.
- `client` - Plugin CEF do lado do cliente.
- `d3dx9` - Bindings para o SDK do DirectX.
- `loader` - Pequeno carregador que faz o funcionamento (deve ser nomeado como `cef.asi`).
- `messages` - Mensagens protobuf para comunicação com o servidor na rede.
- `network` - Conexão com o Quinn (semelhante ao Laminar).
- `proto` - Arquivos proto crus.
- `renderer` - Ligação entre o processo de renderização do CEF e a lógica principal.
- `server` - Plugin do lado do servidor.

## Download
As versões mais recentes do branch principal estão disponíveis nas [Actions](https://github.com/ZOTTCE/samp-cef/actions).

Atualmente existem os seguintes sistemas operacionais:
- CentOS 7 (`cef-centos-7.so`)
- Debian 9, 10, 11 (`cef-debian-*.so`)
- Ubuntu 18.04, 20.04 (`cef-ubuntu-*.so`)
- Windows (`cef-windows.dll`)

E também todas as partes do lado do cliente (`cef.asi`, `client.dll`, `renderer.exe`).

## Compilação
### Dependências
- [Compilador Rust (nightly) com a ferramenta `i686-windows-pc-msvc`](https://rust-lang.org)
- CEF pré-compilado com códigos proprietários (se você quiser usar streams). Eu tinha um para você nas releases. (Somente cliente)
- Variável de ambiente `CEF_PATH` que aponte para `libcef.lib` (somente cliente).
    - No powershell, seria algo como `$env:CEF_PATH="C:/algum/caminho"`
    - Depois, faça a compilação

### Notas ...
Se você encontrar um erro de linkagem, você deve alterar os links codificados no código-fonte

- `client/build.rs` - caminho para o DirectX SDK (o padrão)

### Executando o Rust
e agora
> ~~`cargo +nightly build --target i686-windows-pc-msvc --release`~~

```sh
rustup toolchain install nightly-i686
cargo +nightly-i686 build --release
```

O plugin do cliente também pode ser compilado usando o OpenAL para som ([rodio](https://crates.io/crates/rodio) por padrão). Para fazer isso, compile o cliente sem os recursos padrão. Por exemplo:
```
cargo +nightly-i686 build --release --package client --no-default-features
```
Para fazer funcionar, você deve colocar o `openal.dll` como `sound.dll` na pasta `cef`. Eu não me lembro exatamente qual versão é usada... mas tenho isso na página de releases.

Para compilar uma parte específica, você pode adicionar `--package <NOME>`

Por exemplo, se você tentar compilar TODOS os pacotes no Linux, receberá um erro. Portanto, passe `--package server` para compilar apenas o servidor no Linux.

## Versão do CEF

Versões atuais do CEF e Chromium:
`89.0.5+gc1f90d8+chromium-89.0.4389.40` `branch de lançamento 4389`

```
Data:             26 de fevereiro de 2021

Versão do CEF:    89.0.5+gc1f90d8+chromium-89.0.4389.40
URL do CEF:       https://bitbucket.org/chromiumembedded/cef.git
                  @c1f90d8c933dce163b74971707dbd79f00f18219

Versão do Chromium: 89.0.4389.40
URL do Chromium:  https://chromium.googlesource.com/chromium/src.git
                  @2c3400a2b467aa3cf67b4942740db29e60feecb8
```

## Documentação
- [docs/main_ru.md](/docs/main_ru.md)
- [docs/main_en.md](/docs/main_en.md) (é melhor usar o Google Tradutor para o russo...)
- Além disso, confira a wiki no GitHub.

## Exemplos em vídeo
- https://www.youtube.com/watch?v=Jh9IBlOKoVM (goblin por toda a casa)
- https://www.youtube.com/watch?v=jU-O8_t1AfI (interfaces simples)
- https://www.youtube.com/watch?v=qs7n8LoVYs4 (interface personalizada do GTA)
- https://www.youtube.com/watch?v=vcyTjn3RJhs (bate-papo por voz)
- https://www.youtube.com/watch?v=6OnCSHKcOGU (cozinha na TV)

## GRANDE TAREFA: EXEMPLOS
Mas é semelhante às implementações do FiveM ou RAGE Multiplayer.
